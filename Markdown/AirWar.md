**GameStart.ts**

```typescript
/**
 * 游戏开始界面
 */
class GameStart extends ui.GameStartUI {
    // 游戏资源地址数组
    private assetArr = [{url:"res/atlas/gameRole.atlas"},
					   {url:"sound/achievement.mp3", type:Laya.Loader.SOUND}, 
					   {url:"sound/bullet.mp3", type:Laya.Loader.SOUND},
					   {url:"sound/game_over.mp3", type:Laya.Loader.SOUND},
					   {url:"sound/enemy1_die.mp3", type:Laya.Loader.SOUND},
					   {url:"sound/enemy3_out.mp3", type:Laya.Loader.SOUND}]

    //游戏开始界面
    public GameStart() {
        //游戏加载未完成暂时不显示，防止点击错误
        this.btn_start.visible = false;
        //监听界面是否关闭
        this.once(Laya.Event.CLOSE,this,this.onClose);
        //加载剩余游戏资源，音乐，加载完成与加载进度回调方法
        Laya.loader.load(this.assetArr, Laya.Handler.create(this, this.onComplete),Laya.Handler.create(this, this.onProgress))
    }

    //游戏资源加载完成
    private onComplete():void {
        //加载完成
        this.txt_load.text="资源加载完成,开始游戏吧...";
        //游戏开始按钮显示并弹出
        this.btn_start.visible = true;
        //缓动类弹出动画
        Laya.Tween.from(this.btn_start, {y:this.btn_start.y+20},1000,Laya.Ease.elasticOut)
    }

    //资源加载进度
    private onProgress(loadNum:number):void {
        //显示加载进度 
        this.txt_load.text = "资源记载中,当前进度： " + (loadNum * 100).toString()+"%";
    }

    //界面关闭
    private onClose():void {
        //从舞台移除自己
        this.removeSelf();
        //只加载一次，因此直接销毁自己
        this.destroy();
    }
}

```

**GamePlay.ts**

```typescript
//游戏内UI，血量，积分，等级显示，暂停等
class GamePlay extends ui.GamePlayUI {

    constructor() {
        super();
        this.GamePlay()

    }
    //游戏内UI，血量，积分，等级显示，暂停等
    public GamePlay() {
        //监听暂停按钮事件
        this.gamePause.on(Laya.Event.MOUSE_DOWN,this,this.onPause)
    }
    //游戏暂停
    private onPause():void {
        //显示IDE中隐藏的暂停界面
        this.pauseView.visible = true;
        //暂停界面加点击监听(一次)
        this.pauseView.once(Laya.Event.MOUSE_DOWN,this,this.onContinue)
        //时间对象缩放为0就是停止
        Laya.timer.scale = 0;
    }

    //游戏继续
    private onContinue():void {
        //时间对象缩放为1就是正常速度播放
        Laya.timer.scale = 1;
        //隐藏暂停界面
        this.pauseView.visible = false;
    }

    //本局游戏数据UI更新
    public update(hp:number, level:number, score:number):void {
        //角色血量更新
        this.air_hp.text = "HP:"+hp;
        //关卡等级更新
        this.air_level.text = "level:"+level;
        //游戏分数更新
        this.air_score.text = "Score:"+score;
    }
}
```

**GameOver.ts**

```typescript
class GameOver extends ui.GameOverUI
	{


		constructor() {
			super();
			this.GameOver();
		}
		/**
		 * 游戏结束界面
		 */
		public  GameOver()
		{
			//"重新开始"按钮鼠标事件
			this.btn_restart.on(Laya.Event.MOUSE_DOWN,this,this.onRestart);
		}
		/**
		游戏重新开始
		 */		
		private onRestart():void
		{
			//播放IDE中编辑的按钮动画
			this.ani1_restart.play(0,false);
			//监听动画完成事件，注意用once
			this.ani1_restart.once(Laya.Event.COMPLETE,this,this.AniComplete);
		}
		/**
		 按钮动画播放完成
		 */
		public AniComplete():void
		{
			console.log("我进来了");
			//发送重新开始事件，在Main类中监听
			this.event("reStart")

			//缓动动画关闭效果。IDE中页面为Dialog才可用
			this.close();
		}
	}

```



**GameMap.ts**

```typescript
//游戏背景
class GameMap extends ui.GameBgUI {
    public GameMap() {

    }

    //游戏背景移动更新
    public updateMap():void {
        this.y += 1;
        //如果背景图到了下面不可见，立即调整位置到上面循环显示
		//游戏舞台高为1280
        if (this.bg1.y + this.y >= 1280) {
           this.bg1.y -= 1280 * 2;
        }
        if (this.bg2.y + this.y >= 1280) {
            this.bg2.y -= 1280 * 2;
        }
    }
}
```



**main.ts**

```typescript
/**
 * 游戏流程：
 *      1.添加页面全局变量，一共四个页面
 *      2.用3个方法来作为游戏的主要流程：游戏开始gameStart(), 游戏中gameInit(), 游戏结束gameOver(), 负责游戏流程页面的显示和切换
 *      注：在gameInit()方法中，因为暂时没有角色加入，无法用主角死亡来调用gameOver()方法，因此加入一个时间延迟来模拟流程调用 
 */



//游戏主类，游戏入口
class main {
    // 开始界面
    private start:GameStart;
    // 地图页面
    private map:GameMap;
    // 游戏中页面 
    private play:GamePlay;
    // 游戏结束页面 
    private over:GameOver;

    //游戏关卡数
    public static level:number = 1;
    //玩家得分
    public static score:number = 0;
    //角色层容器
    private roleLayer:Laya.Sprite;
    //玩家主角
    private hero:Role;
    //鼠标上一帧X坐标
    private moveX:number;
    //鼠标上一帧y坐标
    private moveY:number;

    //敌机血量表
    private hps = [1, 7, 15];
    //敌机生成数量表
    private nums = [2, 1, 1];
    //敌机速度表
    private speeds = [3, 2, 1];
    //敌机被击半径表
    private radius = [20, 35, 80];
    /****主角死亡后游戏结束时间***/
	private deathTime:number = 0
    //游戏关卡提升属性 
    //敌人刷新加速
    private createTime:number = 0;
    //敌人速度提升
    private speedUp:number = 0;
    //敌人血量提升
    private hpUp:number = 0;
    //敌人数量提升
    private numUp:number = 0;
    //升级等级所需的成绩数量
    private levelUpScore: number = 10;
	

	constructor(){
		this.Main();
	}
	



    public Main() {
        //初始化引擎，建议增加webGl模式
        Laya.init(720, 1280, Laya.WebGL);
        Laya.Stat.show();
        //全屏不等比例缩放模式
        Laya.stage.scaleMode = Laya.Stage.SCALE_EXACTFIT;
        //加载游戏页面资源(如果界面资源太大[超过50k],建议开始页面单独建立文件夹打包)
        Laya.loader.load([
			"res/atlas/gameUI.atlas",
			"res/atlas/gameRole.atlas",
			"res/atlas/comp.atlas",

			],Laya.Handler.create(this, this.gameStart))
    }



    /**
		 资源加载完成后，加载游戏开始界面
		 */
    private  gameStart(): void {
		
       //实例化开始页面 
    	this.start = new GameStart();
	//以弹出方式打开，有缓动效果。IDE中页面为Dialog才可用
		this.start.popup();
		// Laya.stage.addChild(this.start);
       //监听开始游戏开始按钮开始事件，点击后进入游戏中(IDE中设置的变量定义)
       	this.start.btn_start.on(Laya.Event.MOUSE_UP,this,this.gameInit);
    }

    /**
		 游戏中，游戏初始化
		 */
    private gameInit():void {
        //缓动动画关闭效果。IDE中页面为Dialog才可用
        this.start.close();

        //重置关卡数据
        //游戏关卡数
       main.level = 1;
       //玩家得分
       main.score = 0;
       //敌人刷新加速
       this.createTime = 0;
       //敌人速度提升
       this.speedUp = 0;
       //敌人血量提升
       this.hpUp = 0;
       //敌人数量提升
       this.numUp = 0;
       //升级等级所需的成绩数量
       this.levelUpScore = 10;

       //实例化地图背景页面(如果已实例化，不需要重新new)   swordsmaneye 
       this.map||(this.map = new GameMap());
       //加载到舞台
       Laya.stage.addChild(this.map);

       //实例化角色层并加载到舞台(如果已经实例化，不需要重新new)
       this.roleLayer ||  (this.roleLayer = new Laya.Sprite());
       Laya.stage.addChild(this.roleLayer);

       //实例化游戏中UI页面(如果已实例化，不需要重新new)
			this.play|| (this.play = new GamePlay());			
			//加载到舞台
			Laya.stage.addChild(this.play);
			
			//实例化主角(如果已实例化，不需要重新new)
			this.hero || (this.hero = new Role());
			//初始化角色类型、血量，注：速度speed为0，因为主角是通过操控改变位置,阵营为0
			this.hero.init("hero",10,0,30,0);
			//死亡后会隐藏，重新开始后需显示
			this.hero.visible=true;
			//主角位置修改
			this.hero.pos(360,800);
			//角色加载到角色层中
			this.roleLayer.addChild(this.hero);
			
			//鼠标按下监听
			Laya.stage.on(Laya.Event.MOUSE_DOWN,this,this.onMouseDown);
			//鼠标抬起监听
			Laya.stage.on(Laya.Event.MOUSE_UP,this,this.onMouseUp);
			
			//游戏主循环
			Laya.timer.frameLoop(1,this,this.loop);
    }

    /**
		 点击开始触发移动
		 */	
		private  onMouseDown():void
		{
			//记录鼠标按下时的位置，用于计算鼠标移动量
			this.moveX=Laya.stage.mouseX;
			this.moveY=Laya.stage.mouseY;
			//
			Laya.stage.on(Laya.Event.MOUSE_MOVE,this,this.onMouseMove);
		}

        /**
		 主角跟随鼠标移动
		 */	
		private  onMouseMove():void
		{
			//计算角色移动量
			var xx:number=this.moveX-Laya.stage.mouseX;
			var yy:number=this.moveY-Laya.stage.mouseY;
			//更新移动位置
			this.hero.x-=xx;
			this.hero.y-=yy;
			//更新本帧的移动座标
			this.moveX=Laya.stage.mouseX;
			this.moveY=Laya.stage.mouseY;
		}

        /**
		 鼠标抬起、关闭移动监听
		 */		
		private onMouseUp():void
		{
			Laya.stage.off(Laya.Event.MOUSE_MOVE,this,this.onMouseMove) ;
		}
		


		
		/**
		 游戏主循环
		 */
		private loop():void {
		//本局游戏数据更新
        this.play.update(this.hero.hp, main.level, main.score)
        //如果主角死亡
			if(this.hero.hp<=0)
			{
				//玩家飞机死亡后延迟时间，100帧后弹出游戏结束界面
				this.deathTime++
				if (this.deathTime>=100)
				{
					this.deathTime=0;
					//游戏结束
					this.gameOver();
					//本方法内后续逻辑不执行
					return;
				}
			}else//主角未死亡
			{
				//主角射击
				this.hero.shoot();
				//游戏升级计算
				this.levelUp();
			}

			//地图滚动更新
			this.map.updateMap()
				
			//游戏碰撞逻辑
			//遍历所有飞机，更改飞机状态
			for (var i: number = this.roleLayer.numChildren - 1; i > -1; i--) 
			{
				//获取第一个角色
				var role:Role = this.roleLayer.getChildAt(i) as Role;
				//角色自身更新
				role.update();				
				//如果角色死亡，下一循环
				if(role.hp<=0) continue;
				//碰撞检测
				for(var j:number=i-1;j>-1;j--)
				{	//获取第二个角色
					var role1:Role=this.roleLayer.getChildAt(j) as Role;
					//如果role1未死亡且不同阵营
					if(role1.hp>0&&role1.camp!=role.camp)
					{
						//获取碰撞半径
						var hitRadius:number=role.hitRadius+role1.hitRadius;
						//碰撞检测
						if(Math.abs(role.x-role1.x)<hitRadius&&Math.abs(role.y-role1.y)<hitRadius)
						{
							//如果某一个碰撞体是道具，则吃道具，否则掉血
							if(role.propType!=0||role1.propType!=0)
							{
								//无法判断哪个是道具，因此都相互吃试试
								role.eatProp(role1);
								role1.eatProp(role);
							}else
							{
								//角色相互掉血
								role.lostHp(1);
								role1.lostHp(1);
							}
						}
					}
				}
			}
			
			//创建敌机，加入关卡升级数据，提高难度
			//生成小飞机
			if (Laya.timer.currFrame % (80 - this.createTime) ==0)
			{
				this.createEnemy(0, this.hps[0],this.speeds[0] + this.speedUp , this.nums[0] + this.numUp);
			}
			//生成中型飞机
			if (Laya.timer.currFrame % (170 - this.createTime * 2) == 0) 
			{
				this.createEnemy(1, this.hps[1] + this.hpUp * 2,this.speeds[1] + this.speedUp , this.nums[1] + this.numUp);
			}
			//生成boss
			if (Laya.timer.currFrame % (1000 - this.createTime * 3) == 0) 
			{
				this.createEnemy(2, this.hps[2] + this.hpUp * 6,this.speeds[2], this.nums[2]);
			}
}
            /**
		 游戏升级计算
		 */
		private levelUp():void
		{
			if(main.score>this.levelUpScore)
			{
				//关卡等级提升
				main.level++;
				//角色血量增加，最大30
				this.hero.hp=Math.min(this.hero.hp + main.level*1,30);
				//关卡越高，创建敌机间隔越短
				this.createTime = main.level < 30 ? main.level * 2 : 60;
				//关卡越高，敌机飞行速度越高
				this.speedUp = Math.floor(main.level / 6);
				//关卡越高，敌机血量越高
				this.hpUp = Math.floor(main.level / 8);
				//关卡越高，敌机数量越多
				this.numUp = Math.floor(main.level / 10);
				//提高下一级的升级分数
				this.levelUpScore += main.level * 10;
			}
		}
		
		/**
		 *  创建敌人
		 * @param index 	敌人编号
		 * @param hp   		 敌人血量
		 * @param speed		敌人速度
		 * @param num		敌人数量
		 */
		private createEnemy(index:number,hp:number,speed:number,num:number):void 
		{
			for (var i: number = 0; i < num; i++)
			{
				//创建敌人，从对象池创建
				var enemy:Role = Laya.Pool.getItemByClass("role", Role);
				//初始化敌人
				enemy.init("enemy" + (index+1), hp, speed,this.radius[index],1);
				//从对象池中创建的对象死亡前被隐藏了，因此要重新初始化显示，否则新创建角色不会显示出来
				enemy.visible=true;
				//随机位置
				enemy.pos(Math.random() *(720-80)+50, -Math.random() * 100);
				//添加到舞台上
				this.roleLayer.addChild(enemy);
			}
		}
		
		/**
		 游戏结束
		 */
		private  gameOver():void
		{
			//移除所有舞台事件，鼠标操控
			Laya.stage.offAll();
			
			//移除地图背景
			this.map.removeSelf();
			//移除游戏中UI
			this.play.removeSelf();
			
			//清空角色层子对象
			this.roleLayer.removeChildren(0,this.roleLayer.numChildren-1);
			//移除角色层
			this.roleLayer.removeSelf();
			
			//去除游戏主循环
			Laya.timer.clear(this,this.loop);
			
			//实例化游戏结束页面
			this.over || (this.over = new GameOver());
			//游戏积分显示
			this.over.text_score.text=main.score.toString();
			//以弹出方式打开，有缓动效果。IDE中页面为Dialog才可用
			this.over.popup();

			
			//重新开始事件监听,点击后进入游戏中
			this.over.on("reStart",this,this.gameInit);
			//this.over.AniComplete();
		}
	}

new main();
```

**Role.ts**

```typescript
//角色类，飞机，敌人，子弹，道具
class Role extends Laya.Sprite {
    //飞机的类型  "hero":玩家飞机，"enemy":敌人飞机， "bullet":子弹，"ufo"：道具
    public type: String;
    //飞机的血量
    public hp: number = 0;
    //飞机的速度
    private speed: number = 0;

    //飞机的被攻击半径
    public hitRadius: number;
    //飞机的阵营(敌我区别)
    public camp: number;

    //角色的动画资源
    private roleAni: Laya.Animation;
    //当前动画动作
    private action: String;
    //射击间隔
    public shootInterval: number = 300;
    //下次射击时间
    public shootTime: number = 300;
    //是否是子弹
    public isBullet: Boolean = false;

    //道具类型 0:飞机或子弹， 1：子弹箱， 2，血瓶
    public propType: number = 0;
    //子弹级别(吃子弹道具后升级) 
    public bulletLevel: number = 0;
    //同时射击子弹数量
    public shootNum: number = 1;
    //子弹偏移位置
    private bulletPos = [[-15, 15], [-30, 0, 30], [-45, -15, 15, 45]];

    constructor() {
        super();
        //实例化动画
        this.roleAni = new Laya.Animation();
        //加载IDE编辑的动画文件
        this.roleAni.loadAnimation("GameRole.ani");

        //加载动画对象
        this.addChild(this.roleAni);
    }

    /**
     * 角色初始化
     * @param type  角色类型 ---“hero”:玩家飞机，“enemy1-3”：敌人飞机、“bulle:1-2”：子弹、"ufo1-2":道具
     * @param hp      血量
     * @param speed   速度
     * @param hitRadius   碰撞半径
     * @param camp    阵营
     */

    public init(type: String, hp: number, speed: number, hitRadius: number, camp: number): void {
        //角色初始化属性
        this.type = type;
        this.hp = hp;
        this.speed = speed;
        this.hitRadius = hitRadius;
        this.camp = camp;

        //对象基本都从对象池中创建，如果之前为子弹，不重新赋值的话不会播放死亡动画
        this.isBullet = false;
        //道具属性初始化为0
        this.propType = 0;

        // //实例化动画
        // this.Role();

        //监听动画完成事件
        this.roleAni.on(Laya.Event.COMPLETE, this, this.onComplete)
        //播放默认飞行动画
        this.playAction("fly");
    }
    //动画完成后回调方法
    private onComplete(): void {
        //如果角色还未有宽，获得角色宽高
        if (this.roleAni.width == 0) {
            //获得动画矩形边界
            let bounds: Laya.Rectangle = this.roleAni.getBounds();
            //角色 宽高赋值
            this.roleAni.size(bounds.width, bounds.height)
        }

        //如果死亡动画播放完成
        if (this.action == "die") {
            //update()方法中，隐藏后进行回收
            this.visible = false;
            this.lostProp();
        }

        else if (this.action == "hit") {   //如果是受伤动画，下一帧播放飞行动画
            this.playAction("fly");
        }
    }

    /**
     * 角色失血
     */
    public lostHp(lostHp: number): void {
        //减血
        this.hp -= lostHp;
        //根据血量判断
        if (this.hp > 0) {
            //如果未死亡，则播放受击动画
            this.playAction("hit");
        } else {
            //如果死亡，则播放爆炸动画
            if (this.isBullet) {
                //隐藏，下一帧回收
                this.visible = false;
            } else {
                //添加死亡动画
                this.playAction("die");
                //添加死亡音效
                if (this.type == "hero") Laya.SoundManager.playSound("sound/game_over.mp3");
                else Laya.SoundManager.playSound("sound/enemy_die.mp3");
                //如果碰撞血死亡者不是角色和子弹
                if (this.type != "hero" && !this.isBullet) {
                    //增加游戏积分
                    main.score++;
                }
            }
        }
    }
    /*角色死亡掉落物品*/
    private lostProp(): void {
        if (this.type != "enemy3") return;

        //从对象池里面创建一个道具
        let prop: Role = Laya.Pool.getItemByClass("role", Role);
        //生成随机道具类型
        let r: number = Math.random();
        let num: number = (r < 0.7) ? 1 : 2;

        //重新初始化道具属性，阵营为地方(只与主角发生碰撞)
        prop.init("ufo" + num, 1, 2, 30, 1);
        //道具类型
        prop.propType = num;
        //强制显示
        prop.visible = true;
        //生成的位置为死亡者的位值
        prop.pos(this.x, this.y);
        //加载到父容器
        this.parent.addChild(prop);
    }

    /**
     * 角色吃到道具，加血或子弹级别
     */
    public eatProp(prop: Role): void {
        //如果调用者是主角或prop不是道具，则返回
        if (this.type != "hero" || prop.propType == 0) return;
        //添加吃强化道具音效
        Laya.SoundManager.playSound("sound/achievement.mp3");
        //吃子弹箱
        if (prop.propType == 1) {
            //积分增加
            main.score++;
            //子弹级别增加
            this.bulletLevel++;
            //子弹每升2级，子弹数量增加1，最大数量限制在4个
            this.shootNum = Math.min(Math.floor(this.bulletLevel / 2) + 1, 4);
            //子弹级别越高，发射频率越快
            this.shootInterval = 300 - 8 * (this.bulletLevel > 8 ? 8 : this.bulletLevel);
        }
        else if (prop.propType == 2) { //吃血
            //血量增加
            this.hp += 2;
            //积分增加
            main.score += 1;
        }
        //道具死亡
        prop.hp = 0;
        //道具吃完后消失，下一帧回收
        prop.visible = false;
    }

    /**
     * 播放动画 
     * @param action 动画状态   "fly"、"hit"、"die"
     */
    public playAction(action: string): void {
        this.action = action;
        //播放角色动画，name=角色类型——动画状态，如：hero_fly
        this.roleAni.play(0, true, this.type + "_" + action);
    }

    /**
     * 角色更新,边界检查
     */
    public update(): void {
        //如果角色隐藏，角色消失并回收
        if (!this.visible) {
            //主角不死亡回收，只隐藏，以免其他对象以主角回对象创建，发生引用修改
            if (this.type != "hero") this.die();
            return;
        }
        //角色根据速度飞行
        this.y += this.speed;
        //如果移动到显示区域以外，则移除
        if (this.type != "hero" && (this.y > 1280 + 100 || this.y < -150)) {
            this.visible = false;
        }
        //主角边缘检查
        if (this.type == "hero") {
            //需减去角色宽或高的一半，因为在IDE中制作动画时，我们把角色的中心做为了角色对象的原点
            //判断是否左右超出
            if (this.x < this.roleAni.width / 2) {
                this.x = this.roleAni.width / 2;
            }
            else if (this.x > 720 - this.roleAni.width / 2) {
                this.x = 720 - this.roleAni.width / 2;
            }
            //判断是否上下超出
            if (this.y < this.roleAni.height / 2) {
                this.y = this.roleAni.height / 2;
            }
            else if (this.y > 1280 - this.roleAni.height / 2) {
                this.y = 1280 - this.roleAni.height / 2;
            }
        }
    }
	/**
		 角色射击，生成子弹
		 */
    public shoot(): void {
        //获取当前时间
        let time: number = Laya.Browser.now();
        //如果当前时间大于下次射击时间
        if (time > this.shootTime) {
            //获得发射子弹的位置数组
            let pos = this.bulletPos[this.shootNum - 1]
            for (var i: number = 0; i < pos.length; i++) {
                //更新下次子弹射击的时间
                this.shootTime = time + this.shootInterval;
                //从对象池里面创建一个子弹
                var bullet: Role = Laya.Pool.getItemByClass("role", Role);
                //初始化子弹信息
                bullet.init("bullet2", 1, -10, 1, this.camp)
                //角色类型为子弹类型
                bullet.isBullet = true;
                //子弹消失后会不显示，重新初始化
                bullet.visible = true;
                //设置子弹发射初始化位置
                bullet.pos(this.x + pos[i], this.y - 80);
                //添加到角色层
                this.parent.addChild(bullet);

                //添加子弹音效					
                Laya.SoundManager.playSound("sound/bullet.mp3");
            }
        }
    }

    /**角色死亡并回收到对象池**/
    public die(): void {
        //角色动画停止
        this.roleAni.stop();
        //去除所有动画监听
        this.roleAni.offAll();
        //从舞台移除
        this.removeSelf();
        //回收到对象池
        Laya.Pool.recover("role", this);
    }
}
```


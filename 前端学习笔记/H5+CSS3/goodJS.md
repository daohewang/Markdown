-    addLoadEvent函数

  - 把现有的window.onload事件处理函数的值存入变量oldonload。
  - 如果在这个处理函数上还没有绑定任何函数，就像平时那样把新函数添加给它
  - 如果在这个处理函数上已经绑定了一些函数，就把新函数追加到现有指令的末尾

- ```js
  function addLoadEvent(fuc) {
      var oldonload = window.onload;
      if (typeof window.onload != 'function') {
          window.onload = func;
      } else {
          window.onload = function() {
              oldonload();
              func();
          }
      }
  }
  ```

  nodeType属性总共有12种可取值，但是其中仅有3种具有实用价值  

  - **元素节点**的nodeType属性值是 1
  - **属性节点**的nodeType属性值时 2
  - **文本节点**的nodeType属性值时 3 

- 编写**insertAfter**函数

  ```javascript
  function insertAfter(newElement,targetElement) {
      var parent = targetElement.parentNode;
      if (parent.lastChild == targetElement) {
          parent.appendChild(newElement);
      } else {
          parent.insertBefore(newElement,targetElement.nextSibling);
      }
  } 
  ```

- 查找某个特地元素的下一个元素节点  

  ```javascript
  function getNextElement(node) {
      if (node.nodeType == 1) {
          return node;
      }
      if (node.nextSibling) {
          return getNextElement(node.nextSibling);
      }
      return null;
  }
  ```

- 给一个元素追加新的class属性

  ```javascript
  function addClass(element,value) {
      if (!element.className) {
          element.className = value;
      } else {
          newClassName = element.className;
          newClassName += " ";
          newClassName += value;
          element.className = newClassName;
      }
  }
  ```

- 为一个元素追加新的变化后的class属性

  ```JavaScript
  function styleElementSiblings(tag,theclass) {
      if (!document.getElementByTagName) return false;
      var elems = document.getElementByTagName(tag);
      var elem;
      for (var i=0; i<elems.length; i++) {
          elem = getNextElement(elems[i].nextSibling);
          addClass(elem,theclass);
      }
  }
  /*使用方法：
  比如说给h1后的子元素节点添加样式，可以这么做:
  styleElementSiblings("h1","intro");  
  .intro {
      font: 14px "salas";
      ......
  }*/
  ```

- js实现动画

  ```javascript
  function moveElement(elementID,final_x,final_y,interval) {
      if (!document.getElementById) return false;
      if (!document.getElementById(elementID)) return false;
      var elem = document.getElementById(elementID);
      /*清除积累在setTimeOut队列里的事件
       不管moveElement函数正在移动的是哪个元素，该元素都将获得一个名为movement的属性
       。如果该元素在moveElement函数开始执行时已经有了一个movement属性，就应该用clearElement
       函数进行复位。
      */
      if (elem.movement) {
          clearTimeOut(elem.movement);
      }
      //如果这两个值未被设定，将其初始化为0px；
      if (!elem.style.left) {
          elem.style.left = "0px";
      }
      if (!elem.style.top) {
          elem.style.top = "0px";
      }
      var xpos = parseInt(elem.style.left);
      var ypos = parseInt(elem.style.top);
      var dist = 0;
      if (xpos == final_x && ypos == final_y) {
          return true;
      }
      /*Math.ceil(number): 把浮点数number向 "大于"方向舍入为与之最接近的整数
        Math.floor(number):把浮点数number向 "小于"方向舍入为与之最接近的整数
        Math.round(number):把任意浮点数舍入为与之最接近的整数
       */
      if (xpos < final_x) {
          dist = Math.ceil((final_x-xpos)/10);
          xpos = xpos + dist;
      }
      if (xpos > final_x) {
          dist = Math.ceil((final_x-xpos)/10);
          xpos = xpos - dist;
      }
      if (ypos < final_y) {
          dist = Math.ceil((final_y-ypos)/10);
          xpos = ypos + dist;
      }
      if (ypos > final_y) {
          dist = Math.ceil((final_y-ypos)/10);
          xpos = ypos - dist;
      }
      elem.style.left = xpos + "px";
      elem.style.top = ypos + "px";
      var repeat = "moveElement('"+elementID+"',"+final_x+","+final_y+","+interval+")";
      elem.movement = setTimeOut(repeat,interval);
  }
  
  /*用法：
  <p id="message">html部分</p>
  
   function positionMessage() {
              if (!document.getElementById) return false;
              if (!document.getElementById("message")) return false;
              var elem = document.getElementById("message");
              elem.style.position = "absolute";
              elem.style.left = "100px";
              elem.style.top = "100px";
              moveElement("message",200,100,10);
          }
  */
  ```

- elementSupportsAttribute函数：检查特定的属性 

  ```JavaScript
  function elementSupportsAttribute(elementName, attribute) {
      if (!document.createElement) return false;
      var temp = document.creatElement(elementName);
      return (attribute in temp);
  }
  /*使用方法： 只需要传入元素名和要检查的属性名
  if (!elementSupportsAttribute('input', 'placeholder')) {
  	//生成占位符提示信息的脚本
  }
  */
  ```

- 表格中label文本被点击时，关联的表单字段获得焦点  

  ```JavaScript
  function focusLabels() {
    if (!document.getElementsByTagName) return false;
    var labels = document.getElementsByTagName("label");
    for (var i=0; i<labels.length; i++) {
      if (!labels[i].getAttribute("for")) continue;
      labels[i].onclick = function() {
        var id = this.getAttribute("for");
        if (!document.getElementById(id)) return false;
        var element = document.getElementById(id);
        element.focus();
      }
    }
  }
  ```

---
title: jsp-tags
date: 2019-02-17 21:33:16
tags: JSP
index_img: ../post_img/jsp-tags.jpeg
---

## JSP

### 动态 JSP 和静态 JSP 的区别

静态引入：

``` jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%@ include file="/footer.jsp"%>
```

动态引入：

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<jsp:include page="/footer.jsp"/>
```

注意：将包含有其他 `.jsp` 的文件称为主体文件，将被包含的文件称为包含文件。

#### **区别**

 1. <%@ include file=” ”%> 是指令元素。<jsp:include page=” ”/> 是行为元素。两者最终编译时产生的数目不同（后者更多）。
 2. 静态包含在转换成为 java 的文件的时候将包含文件的内容“复制”到主体文件，然后作为一个整体编译。最终编译为一个 java 文件。
 3. 动态包含是各个 `.jsp` 文件分别转化、分别编译，最终编译成多个 java 文件。
 4. 执行时间不同，静态包含发生在 JSP -> Java 文件阶段；动态包含发生在执行 class 文件阶段，动态加入。
 5. 由于静态包含相当于将包含文件内容直接复制到主体文件中，如果出现相同的变量，就会出现覆盖等问题，导致文件出错。而动态包含相当于调用不同的 jsp，变量所在的空间不同，自然不会出现覆盖等现象。
 6. 无论是动态包含还是静态包含，其 request 对象都是相同的。也就是同一个 request 对象。静态包含最终编译成一个 java 文件，有一个 request 对象很好理解。而动态包含最终编译成多个 jsp 文件，为何会使用一个 request 对象呢？其实这些 jsp 组合的过程是一个请求转发的过程，自然也使用同一个 request 对象了。
 7. 使用静态包含，包含页面和被包含页面的 request 对象为同一对象，因为静态包含只是将被包含的页面的内容复制到包含的页面中去；而动态包含包含页面和被包含页面不是同一个页面，被包含的页面的 request 对象可以取到的参数范围要相对大些，不仅可以取到传递到包含页面的参数，同样也能取得在包含页面向下传递的参数。
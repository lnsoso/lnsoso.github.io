---
layout: post
title: Javascript 加载本地 XML 文件
author: Alvin
date: !binary |-
  MjAwNi0xMS0wNyAxNTo0NjoyNyArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0wNyAwNzo0NjoyNyArMDgwMA==
---
之前自己写的两个小函数.
/*
 * @file : localxml.js
 * @date : 2006-05-09
 * @auth : Alvin
 
 */
// 查找对象
function findobj(s)
{
 return document.getElementById(s);
}
function LocalXML() 
{
 if( window.ActiveXObject )
 {
  var oDoc = new ActiveXObject("Microsoft.XMLDOM");
 }
 else if( document.implementation && document.implementation.createDocument )
 {
  var oDoc = document.implementation.createDocument("", "", null);
 }
  
 this.loadXML = function(sFilename, sFunction)
 {
  if( sFunction )
  {
   this.onchange(sFunction);
  }
  this.async(false);
  try
  {
   oDoc.load(sFilename);
  }
  catch (e)
  {
   // no such file
   return;
  }
 }
 
 this.getNode = function(sXpath)
 {
  var iRetval = "";
  var iValue = oDoc.selectSingleNode(sXpath);
 
  if (iValue)
  {
   iRetval = iValue.text;
  }
  return iRetval;
 }
 this.selectNodes = function(sXpath)
 {
  return oDoc.selectNodes(sXpath);
 }
 
 this.readyState = function()
 {
  return oDoc.readyState;
 }
 
 this.loaded = function()
 {
  if( this.readyState() == 4 )
  {
   return true;
  }
  return false;
 }
 
 this.onchange = function(sFunction)
 {
  oDoc.onreadystatechange = eval(sFunction);
 }
 
 this.async = function(bType)
 {
  oDoc.async = bType
 }
}

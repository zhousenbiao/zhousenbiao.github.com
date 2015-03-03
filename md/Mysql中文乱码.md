layout: post.md
title: Mysql中文乱码问题完美解决方案
categories: tech
tags: mysql


MySQL会出现中文乱码的原因不外乎下列几点：
1.server本身设定问题，例如还停留在latin1
2.table的语系设定问题(包含character与collation)
3.客户端程式(例如php)的连线语系设定问题
强烈建议使用utf8!!!!
utf8可以兼容世界上所有字符!!!!
一、避免创建数据库及表出现中文乱码和查看编码方法
1、创建数据库的时候：CREATE DATABASE `test`
CHARACTER SET 'utf8'
COLLATE 'utf8_general_ci';
2、建表的时候 CREATE TABLE `database_user` (
`ID` varchar(40) NOT NULL default '',
`UserID` varchar(40) NOT NULL default '',
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

这3个设置好了，基本就不会出问题了,即建库和建表时都使用相同的编码格式。
但是如果你已经建了库和表可以通过以下方式进行查询。
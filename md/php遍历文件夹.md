layout: post.md
title: php遍历文件夹
categories: tech
tags: php

http://blog.csdn.net/heiyeshuwu/article/details/42262951


利用php函数unlink()，指定文件的路径作为参数即可删除指定路径下的文件。文件路径可以声明一个路径的变量表示，将其赋值为目录名和文件名链接的字符串即可。例如：$file="./photo/".$ph;（"./photo"为目录的路径，$.ph变量表示的是文件名）
<?php
$file="./photo/".$_REQUEST["name1"];
echo  $file;
 $result = @unlink ($file); 
if($result == false){
echo  "删除成功";
}
else{
echo  "删除失败";
}
?>


使用PHP遍历文件夹与子目录

我们可以使用的函数有 Scandir，它的作用是列出指定路径中的文件和目录，就像 Dir 一样，
以及更强力的 Glob() 函数，作用是以数组的形式返回与指定模式相匹配的文件名或目录。 

一. 遍历单层文件夹： 
function get_dir_glob(){ 
$tree = array(); 
foreach(glob('./*') as $single){ 
echo $single."<br/>\r\n"; 
} 
} 
get_dir_glob(); 

二. 递归遍历文件树： 
Glob 函数扫描非常准确，并且会自动排好顺序:

$path = '..'; 
function get_filetree($path){ 
$tree = array(); 
foreach(glob($path.'/*') as $single){ 
if(is_dir($single)){ 
$tree = array_merge($tree,get_filetree($single)); 
} 
else{ 
$tree[] = $single; 
} 
} 
return $tree; 
} 
print_r(get_filetree($path)); 
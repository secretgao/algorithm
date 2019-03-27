##### 求寻找最长不含有重复字符的子串
* ‘bbbbabcdefabcdeabcdabcab’  -> abcdef 6
* 'bbbbwpbbbbb'               ->bwp     3
* 'bbbbbbbbbb'                ->b 1 
* 'abcabcbdc'                 -> abc 3

```
<?php
//寻找最长不含有重复字符的子串。。。 写的不好多提宝贵意见
$str1='abcabcbdc';
$str1='bbbbbbbbbb';
$str1='bbbbwpbbbbb';
$str1='bbbbabcdefabcdeabcdabcab';

function findSubstring($str){

    if (empty($str)){
        return false;
    }

    $arrStr=preg_split('/(?<!^)(?!$)/u',$str);//中文字符识别
    $length=0;//长度
    $start=0;//分段的起始位置
    $arr=[];//更新每个值得键
    $sonStr ='';
    foreach ($arrStr as $k=>$v){
        if(@$arr[$v]!==null&&$arr[$v]>=$start){
            $start=$arr[$v]+1;
        }
        if(($k-$start+1)>$length){
            $length=$k-$start+1;
            $sonStr=substr($str,$start,$length);
        }
        $arr[$v]=$k;
    }
    return array('length'=>$length,'sonstr'=>$sonStr);
}

$res = findSubstring($str1);
echo '<pre>';

var_dump($res);



```

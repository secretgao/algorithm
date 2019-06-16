#### 递归实现敏感词过滤

* 简单思路图
![](https://s2.ax1x.com/2019/06/16/VTQrk9.png)

```
<?php
/**
 * Created by PhpStorm.
 * Date: 2019/6/15
 */

$dbms='mysql';
$dbName='test';
$user='root';
$pwd='';
$host='localhost';
$charset = 'utf8';
$dsn="$dbms:host=$host;dbname=$dbName;charset=$charset";
try{
    $pdo=new PDO($dsn,$user,$pwd);
} catch(Exception $e) {
    echo $e->getMessage();
}

//假设 用户提交过来的数据在50字以内

$input = '今天天气真好呀你穿的什么哈哈哈哈哈哈';
echo '用户输入内容:'.$input.'<br>';

function diGui($input,$pdo,$i=0){

    echo '用户输入的内入第'.$i.'次过滤结果:'.$input.PHP_EOL;
    $count = mb_strlen($input);
    $arr = inputContentTransferArr($input,$count);
    foreach ($arr as $key=>$value){

        if ($value == '*'){
            continue;
        }
        $select_sql = 'select `word`,`length` from word where word like :word';
        $stmts = $pdo->prepare($select_sql);
        $stmts->execute(array(':word'=>$value.'%'));
        $row = $stmts->fetchAll(PDO::FETCH_ASSOC);
        if (!$row){
            continue;
        }

        $replace = charactersMatching($key,$count,$row);
        if (!$replace){
            continue;
        }
        echo '<pre>';
        $result = strtr($input,$replace);
        return digui($result,$pdo,$i+1);
    }
    return $input;
}

$res =  digui($input,$pdo);
echo '过滤结果:';
var_dump($res);



//把用户输入的内容转化成数组
function inputContentTransferArr($str,$count){

    if (empty($str)){
        return false;
    }
    $arr = [];
    for ($i=0;$i<$count;$i++){
        $arr[$i] = mb_substr($str, $i, 1, 'utf-8');
    }
    return $arr;
}


/** 词库和输入进行匹配
 * @param $arr    用户输入的字符串转换成的数组
 * @param $value  当前查找的汉字的数组索引
 * @param $count  当前汉字的长度mb_strlen
 * @param $selectResult  当前单个汉字匹配敏感词的结果
 */
function charactersMatching($key=0,$count,$selectResult){
    //计算差值缩小筛选筛选$selectResult 数组范围
    $c_k = $count - $key;
    $filterNum = $c_k == 0 ? 1 : $c_k;
    $filterSelectResult = filterSelectResult($selectResult,$filterNum);
    return $filterSelectResult;
}

/**
 * 在给定的二维数组中找到 $selectResult[][length] == $filter的数组
 * @param $selectResult
 * @param $filter
 */
function filterSelectResult($selectResult,$filter){

      $data = array_filter($selectResult, function($v) use ($filter) {
          return $v['length'] <= $filter;
      });

      if (!$data){
          return false;
      }
      $result =[];
      foreach ($data as $key=>$val){
          $result[$val['word']]='*';
      }
      return $result;
}
```

##### 输出结果
```$xslt
用户输入内容:今天天气真好呀你穿的什么哈哈哈哈哈哈
用户输入的内入第0次过滤结果:今天天气真好呀你穿的什么哈哈哈哈哈哈
用户输入的内入第1次过滤结果:今*真好呀你穿的什么哈哈哈哈哈哈
用户输入的内入第2次过滤结果:今*真*呀你穿的什么哈哈哈哈哈哈
用户输入的内入第3次过滤结果:今*真*呀*穿的什么哈哈哈哈哈哈
用户输入的内入第4次过滤结果:今*真*呀**的什么哈哈哈哈哈哈
用户输入的内入第5次过滤结果:今*真*呀***什么哈哈哈哈哈哈
用户输入的内入第6次过滤结果:今*真*呀****哈哈哈哈哈哈
用户输入的内入第7次过滤结果:今*真*呀**********
过滤结果:string(21) "今*真*呀**********"
```
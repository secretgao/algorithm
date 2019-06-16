
![](https://s2.ax1x.com/2019/06/16/VTrPII.png)
```$xslt
<?php
//树的节点
class TrieNode
{
    public $data;          //数据
    public $children = []; //子节点
    public $isEndingChar = false;
    public function __construct($data)
    {
        $this->data = $data;
    }
}

//树
class Tire {
    private $root;

    public function __construct() {
        $this->root = new TrieNode('/'); //根节点
    }

    /**
     * 获取整个trie 树结构
     * @return TrieNode
     */
    public function getRoot() {
        return $this->root;
    }
    /**
     * 写入树
     * 添加敏感词
     * @param array $txtWords
     * @author qpf
     */
    public function insert(array $wordsList)
    {
        foreach ($wordsList as $words) {
            $trie = $this->root;
            $strLen = $this->getStrLen($words);
            for ($i = 0; $i < $strLen; $i++) {
                $word = $this->getIndexValue($words,$i);
                if(!isset($trie->children[$word])){
                    $newNode = new TrieNode($word);
                    $trie->children[$word] = $newNode;
                }
                $trie = $trie->children[$word];
            }
            $trie->isEndingChar = true;
        }
    }
    /* 查询子节点 */
    public function searchChildNode($value){
        foreach ($this->children as $k => $v) {
            if($v->value == $value){
                // 存在节点，返回该节点
                return $this->children[$k];
            }
        }
        return false;
    }


    /**
     * 查找对应敏感词
     *
     * @param string $txt
     * @return array
     * @author qpf
     */
    public function search($str)
    {
        $wordsList = array();
        $hasWordList = array();
        $strLength = $this->getStrLen($str);
        for ($i = 0; $i < $strLength; $i++) {

            $wordLength = $this->checkWord($str, $i, $strLength);

            if($wordLength > 0) {
              //  echo $wordLength;
                $words =  $this->getIndexValue($str,$i,$wordLength);
                $node = $this->getChildString($words);
                if ($node){
                    $hasWordList[] = $this->getChildString($node);
                }

              //  $i += $wordLength - 1;
            }
        }
        return $wordsList;
    }

    /**
     * 敏感词检测
     * @param $txt
     * @param $beginIndex
     * @param $length
     * @return int
     */
    private function checkWord($txt, $beginIndex, $length)
    {
        $flag = false;
        $wordLength = 0;
        $trie = $this->root; //获取敏感词树
        for ($i = $beginIndex; $i < $length; $i++) {
            $word = $this->getIndexValue($txt,$i); //检验单个字
            if (!isset($trie->children[$word])) { //如果树中不存在，结束
                break;
            }
            //如果存在
            $wordLength++;
            $trie = $trie->children[$word];
            if ($trie->isEndingChar === true) {
                $flag = true;
                break;
            }
        }
        if($beginIndex > 0) {
            $flag || $wordLength = 0; //如果$flag == false  赋值$wordLenth为0
        }
        return $wordLength;
    }

    /* 获取所有字符串--递归 */
    function getChildString($node, $str_array = array(), $str = '')
    {
        if($node->isEndingChar == true){
            $str_array[] = $str;
        }
        if(empty($node->children)){
            return $str_array;
        }else{
            foreach ($node->children as $k => $v) {
                $str_array = getChildString($v, $str_array, $str . $v->value);
            }
            return $str_array;
        }
    }

    /**
     * 计算汉字长度
     * @param $str
     * @return int
     */
    public function getStrLen($str){
        return mb_strlen($str);
    }

    /**
     * 根据给定的index 索引位置 返回对应的汉字
     * @param $str
     * @param $index
     * @return string
     */
    public function getIndexValue($str,$index,$length = 1){
        return mb_substr($str, $index, $length, 'utf-8');
    }
}

$trie = new Tire();

//敏感词数组
$arr = [
    '今天好'//,'今天不'//,'今','今日阴','今时今日','天气',
   // '明','明日','明日有雨',
   // '冬至',
  //  '夏天','夏季'
];

echo '<pre>';
$trie->insert($arr);
//插入到树中构造树形结构
var_dump($trie->getRoot());

$input = '今天好';
$res = $trie->search($input);
var_dump($res);

//

//
```
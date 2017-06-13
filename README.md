# wechat

- 1.代码源文件为utf8无BOM格式
- 2.查看服务器端口是否为80端口
- 3.可查看url和token是否正确,token名字起的唯一一点

当文件编码为utf8 bom时文件前会自动添加3个字符，把文件去掉bom 再次提交，验证成功！
```php
<?php
/**
  * wechat php test
  */

//define your token
define("TOKEN", "1025yingshi");
$wechatObj = new wechatCallbackapiTest();
$wechatObj->valid();
$wechatObj->responseMsg();
class wechatCallbackapiTest
{
  public function valid()
    {
        $echoStr = $_GET["echostr"];

        //valid signature , option
        if($this->checkSignature()){
          echo $echoStr;
          exit;
        }
    }

    public function responseMsg()
    {
    //get post data, May be due to the different environments
    //$postStr = $GLOBALS["HTTP_RAW_POST_DATA"];
    //php5.6版本替换为此函数
    $postArr = file_get_contents("php://input");

        //extract post data
    if (!empty($postStr)){
                
                $postObj = simplexml_load_string($postStr, 'SimpleXMLElement', LIBXML_NOCDATA);
                $fromUsername = $postObj->FromUserName;
                $toUsername = $postObj->ToUserName;
                $keyword = trim($postObj->Content);
                $time = time();
                $textTpl = "<xml>
              <ToUserName><![CDATA[%s]]></ToUserName>
              <FromUserName><![CDATA[%s]]></FromUserName>
              <CreateTime>%s</CreateTime>
              <MsgType><![CDATA[%s]]></MsgType>
              <Content><![CDATA[%s]]></Content>
              <FuncFlag>0</FuncFlag>
              </xml>";             
        if(!empty( $keyword ))
                {
                  $msgType = "text";
                  switch($keyword)
                  {
                  	case '1024':
                  		$contentStr = "1024";
                  		break;
                  	default:
                  	    $contentStr = "";
                  }
          //        $contentStr = "http://cl.giit.us ";
                  $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                  echo $resultStr;
                }else{
                  echo "Input something...";
                }

        }else {
          echo "";
          exit;
        }
    }
    
  private function checkSignature()
  {
        $signature = $_GET["signature"];
        $timestamp = $_GET["timestamp"];
        $nonce = $_GET["nonce"];  
            
    $token = TOKEN;
    $tmpArr = array($token, $timestamp, $nonce);
    sort($tmpArr);
    $tmpStr = implode( $tmpArr );
    $tmpStr = sha1( $tmpStr );
    
    if( $tmpStr == $signature ){
      return true;
    }else{
      return false;
    }
  }
}

?>
```

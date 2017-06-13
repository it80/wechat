# wechat

- 1.代码源文件为utf8无BOM格式
- 2.查看服务器端口是否为80端口
- 3.可查看url和token是否正确,token名字起的唯一一点

当文件编码为utf8 bom时文件前会自动添加3个字符，把文件去掉bom 再次提交，验证成功！
- 微信公众平台技术文档 https://mp.weixin.qq.com/wiki?id=mp1421140454&highline=subscribe

- 微信公众平台接口调试工具 https://mp.weixin.qq.com/debug/cgi-bin/apiinfo?t=index&type=%E6%B6%88%E6%81%AF%E6%8E%A5%E5%8F%A3%E8%B0%83%E8%AF%95&form=%E4%BA%8B%E4%BB%B6%E6%B6%88%E6%81%AF

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

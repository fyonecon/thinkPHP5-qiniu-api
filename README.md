# thinkPHP5-qiniu-api
基于TP5的七牛云对象存储上传文件api，支持任意文件格式。简单的api方便移植。

直接调用：http://localhost/wxmail/public/?s=/qiniuapi/index 即可查看返回结果http://p9mda7c37.bkt.clouddn.com20180601_11-34-23_5b10bebfa683c.png

    /*
     * 向七牛云上传文件【post Api】，支持【一切有格式】的文件，并返回文件名
     * 接口示例：http://xxxxxx/public/?s=/qiniuapi/index/qiniu_upload_api
     *
     * $file_path：文件服务器中路径，例如：D:\wamp64\www\wxmail\h5/letter/niming.png 写法：ROOT_PATH."h5/letter/niming.png"
     * $qiniu_bucket：七牛bucket
     *
     * */
    public function qiniu_upload_api($file_path, $qiniu_bucket){

        if($_REQUEST){

            $accessKey = '7We6C5IOMdYGyFv8_sD3Uh1MzXZpSMDf9t3vJVQZ';
            $secretKey = 'JANUchd3uGPff-hRumhUGjuFA6NZw8KVC5QiBb-J';
            //$domain = 'http://p9mda7c37.bkt.clouddn.com'; // 临时域名或绑定的域名http://p9mda7c37.bkt.clouddn.com
            //$bucket='test'; // 七牛上面的文件夹

            if(!$file_path || !$qiniu_bucket){
                return array("status"=>0,"content"=>"file_path or qiniu_buket is null");
            }

            $bucket = $qiniu_bucket;

            $auth = new Auth($accessKey,$secretKey);
            $token = $auth->uploadToken($bucket);
            $uploadMgr = new UploadManager();

            $files = $file_path; // 文件服务器中路径
            $pattern = substr(strrchr($files, '.'), 1); // 正则文件格式
            if (!$pattern){
                return array("status"=>0, "content"=>"pattern is null");
            }
            //print_r($files);
            $tmpArr = array($files);
            foreach ($tmpArr as $k => $value) {
                $filePath = $value;
                $key = date("Ymd_H-i-s")."_".uniqid().".".$pattern; // 文件保存的路径及其文件名
                $res = $uploadMgr->putFile($token, $key, $filePath); // 上传

                //$link = $domain."/".$key; // 文件直接访问地址
                //print_r($link);

                if ($res){ //成功上传
                    return array("status"=>1,"content"=>$res[0]['key']); // 返回文件名
                }else{
                    return array("status"=>0,"content"=>"qiniu-upload-img is error");
                }
            }

        }else{
            return array( "status"=>0, "content"=>"REQUEST is error");
        }
    }



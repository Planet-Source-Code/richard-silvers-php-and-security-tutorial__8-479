<div align="center">

## PHP and Security \- Tutorial


</div>

### Description

Hopefully aiding in writing more secure PHP scripts.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Richard Silvers](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/richard-silvers.md)
**Level**          |Intermediate
**User Rating**    |4.6 (298 globes from 65 users)
**Compatibility**  |PHP 3\.0, PHP 4\.0
**Category**       |[Security](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/security__8-14.md)
**World**          |[PHP](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/php.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/richard-silvers-php-and-security-tutorial__8-479/archive/master.zip)





### Source Code

<p>This is my first contribution in the form of a PSC tutorial, so please bear with me. In this tutorial I am going to discuss security as it relates to protecting your PHP scripts from preying eyes, as well as protecting your system and your web pages from would-be assailants. This tutorial walks through some exploits regarding PHP, as well as fixes for them. This article, this site, or I do not condone using any of this knowledge in a devious or malicious manner.</p><br>
<p>The rest of this tutorial will be laid out in the following sections:</p><br><br>
	<p>1. PHP and Security</p>
	<p>2. Site Defacement</p>
	<p>3. Externally working with variables</p>
	<p>4. File Access</p>
	<p>5. Encryption</p>
	<p>6. Cookie Encryption</p>
	<p>7. Protecting Scripts</p>
	<p>8. One-way Password Authentication</p>
<h3>Section 1: PHP and Security</h3><br>
<p>While most of the world focuses on crackers gaining access to websites from bugs in the web server amongst other things, the major flaws that simply running PHP on your system can cause go unnoticed. Most new PHP users, and probably some veteran ones, don't fully understand the holes that can be readily opened by a single ill-written PHP script. You may think opening a local file on your system is secure, but is it? We'll explore this and other exploits later in this tutorial.</p>
<p>PHP is a very forgiving language, and with this (if not because of it), it is very easy to design PHP programs that have bugs or undesirable consequences. Bugs are easier to implement in PHP programs mainly because of the way it handles variables. Not only can different types of variables be loosely assigned to one another, but also PHP doesn&#8217;t really care where a variable has come from, which leads to large security holes.</p>
<h3>Section 2: Site Defacement</h3><br>
<p>Site defacement is a common occurrence on the Internet. Site defacement is where a person, by various means, is able to edit the HTML code of a web page and use it to display their own content. This attack is usually more embarrassing than it is harmful, but this is not always the case. A person with access to the source of a web page can do obvious things like link to URLs containing malicious software or not-so-obvious things such as display false statements on a company&#8217;s website, thus embarrassing the organization and possibly ruining its reputation.</p>
<p>Site defacement is just one of the many threats web server administrators have to deal with every day that they run their site, but it is also one of the most common. You may be surprised to learn just how easy it is to set yourself up for site defacement in PHP.</p>
<p>PHP is commonly used to develop guest books, web bulletin boards, or just about anything else that requires some form of input from users. This is where most people get into trouble. Examine the following piece of hypothetical code:</p>
<font color="#1FD1E0" face="Courier New">
	&lt;?<br>
	 print(&#8220;$MsgAuthor[27]<BR>&#8221;);<br>
	 print(&#8220;$MsgDate[27]<BR>&#8221;);<br>
	 print(&#8220;$MsgSubject[27]<BR>&#8221;);<br>
	 print(&#8220;$MsgBody[27]<BR>&#8221;);<br>
	?&gt;<br><br></font>
<p>This code, taken from a bulletin board, sends the Author name, Date, Subject, and Body of the 27th entry to anyone that clicks on the link to it. This code will function perfectly; in fact, it will do exactly what you want it to. But what you want is probably not what is best. Suppose that when the person who originally created the 27th entry he put some HTML code of his own in $MsgBody. What will happen to the next person who reads that message? Basically, whatever the creator wanted. It could be anything from displaying harmless images to malicious things such as repetitive loading of java script pop-up windows.</p>
<p>There are instances where you would users to be able to enter HTML into their message bodies, but I don&#8217;t. (And since I am writing this tutorial, you have to go along). In case you don&#8217;t want to allow this either, PHP provides you with a function that will take care of this problem for you. Let&#8217;s take a look at it:</p>
<font color="#1FD1E0" face="Courier New">
	&lt;?<br>
	 print(&#8220;$MsgAuthor[27]<BR>&#8221;);<br>
	 print(&#8220;$MsgDate[27]<BR>&#8221;);<br>
	 print(&#8220;$MsgSubject[27]<BR>&#8221;);<br>
	 print(htmlspecialchars($MsgBody[27]) . &#8220;<BR>&#8221;);<br>
	?&gt;<br><br></font>
<p>The htmlspecialchars() function strips all of the HTML parsing symbols (<, >, &) and replaces them with their equivalents as html entities (< > &). This prevents the execution of any HTML you don&#8217;t want.</p>
<p>In addition to the htmlspecialchars() function there is also a htmlentities() function that will strip out all special characters and replace them with their respective HTML entity equivalents.</p>
<p>This does not fix all possibilities for site defacement, but it fixes a major one within PHP that not many people seem to know exist.</p>
<h3>Section 3: Externally Working With Variables</h3><br>
<p>This is a large issue with PHP and can have an even larger number of results depending on what the script does. This flaw relies on the fact that variables in PHP do not need to be defined, and can be created directly from the URL. Examine the following piece of code:</p>
<font color="#1FD1E0" face="Courier New">
	&lt;?<br>
	 If ($Password == &#8220;root&#8221;) {<br>
	 $AdminPass = TRUE;<br>
	 }<br>
<br>
	 If ($AdminPass == TRUE) {<br>
	 print(&#8220;Welcome to the system. Enjoy yourself.&#8221;);<br>
	 } else {<br>
	 print(&#8220;Password refused. Try again.&#8221;);<br>
	 return;<br>
	 }<br>
<br>
	?&gt;<br><br></font>
<p>What&#8217;s wrong with this script? Besides the fact that it is poorly written, nothing is wrong with it, right? PHP will still interpret and execute this script. If we were to hypothetically assume that $AdminPass contains whether the administrator password was accepted or not, it appears fine. Now lets pretend that this script is entitled &#8220;admin.php&#8221; and was called from the URL: http://www.thesite.com/admin.php?AdminPass=1. What happens? You can bypass the test and access the site without even having to enter a password.</p>
<p>Now remind yourself that this is a purely hypothetical situation and that hopefully no one would write such a script. But the fact that variables can be set from the URL string is quite dangerous and can have completely unexpected and unintended results that your program isn&#8217;t prepared to handle.</p>
<p>In the above example, there isn&#8217;t a good way to check whether or not the variable is set with the URL string or not, but by using explicit type checking you can usually predict if it is a variable you had defined or came from an external source. A good method to try is the &#8220;===&#8221; operator which checks if the arguments are equal to each other and are of the same type. The &#8220;===&#8221; operator makes sure that the values are the same and that PHP identifies them as the same type. This prevents expressions like &#8220;TRUE == 1&#8221; from returning a TRUE value.</p>
<p>What if the $AdminPass was set to TRUE in the URL string though? It would match even with specific type checking. The best solution to the above problem is to initialize all of the used variables in our PHP script to a null value.</p>
<font color="#1FD1E0" face="Courier New">
	&lt;?<br>
	 $AdminPass = FALSE;<br>
<br>
	 If ($Password == &#8220;root&#8221;) {<br>
	 $AdminPass = TRUE;<br>
	 }<br>
<br>
	 If ($AdminPass == TRUE) {<br>
	 print(&#8220;Welcome to the system. Enjoy yourself.&#8221;);<br>
	 } else {<br>
	 print(&#8220;Password refused. Try again.&#8221;);<br>
	 return;<br>
	 }<br>
	?&gt;<br><br></font>
<p>In the above example $AdminPass was initialized to FALSE as no operations have yet to be performed on it and we know that changing its value at the beginning of our program will not affect the results of the script. If there was a URL string variable supplied as $AdminPass, it will be cleared and set to FALSE, fixing a large hole.</p>
<h3>Section 4: File Access</h3><br>
<p>There are a number of holes in PHP when it is used to access files. One of the most devious is using the external variable method described above in conjunction with a script that supposedly opens &#8220;safe&#8221; files.</p><br><br>
<p>In browsing the World Wide Web you have undoubtedly come across URLs that are formed like this: www.thesite.com/loadpicture.php?file=butterfly.jpg. It&#8217;s obviously a script that is used to load pictures, and it is calling the file &#8220;butterfly.jpg&#8221; to load. What&#8217;s so wrong with this? Nothing as long as you know exactly what files this script can call. What if some malicious person were to edit that URL and replace it with &#8220;file=/etc/passwd&#8221;? If you are unlucky enough to allow access to /etc/passwd your password file has now been sent out over the Internet. Good work.</p>
<p>The unfortunate solution to this hole is that there isn&#8217;t really a good one. Included with PHP is a configuration known as open_basedir which allows you to specify which directories your PHP scripts can access, but if you intend to work with password files or any sensitive data you must be able to access it to use it, and therefore the file must be within one of the specified directories. Note that open_basedir is still a valid option to protect parts of your system which you have no need to access. For the rest of your system, however, we must use encryption.</p>
<h3>Section 5: Encryption</h3><br>
<p>Encryption. It&#8217;s the buzzword of the Internet recently. If you aren&#8217;t encrypting all of your web traffic, e-mail messages, telnet sessions, and about everything else you use online, you just aren&#8217;t cool anymore. Seriously though, encryption is a highly valuable tool, especially if you are dealing with important things like password files.</p>
<p>Encryption is the process of taking a file, usually referred to as plaintext, and encrypting that file into an unrecognizable form known as ciphertext. Decrypting the file backwards from cipher to plaintext usually requires a key of some sort. Without the key it is very difficult to reconstruct the original plaintext message.</p>
<p>There are two main categories of encryption: one-way and two-way. A two-way encryption scheme will allow the encryption and decryption of text and usually requires some form of key. A one-way encryption scheme will only encrypt data and, having no key to decrypt it, will remain that way. One-way encryption is typically used for password files because it is more secure to simply encrypt attempted passwords and compare the encrypted version against encrypted passwords already inside the password file.</p>
<p>PHP will often gracefully handle this task for you, but you must remember that if you use a two-way password encryption scheme, the encrypted file is only as safe as the access to your decryption routines. A better way to handle encryption, and one that is commonly used to encrypt the /etc/passwd file on Unix, is one-way encryption. PHP allows for this form of encryption in it&#8217;s crypt() function.</p>
<h3>Section 6: Cookie Encryption</h3><br>
<p>The biggest proponent of cookies is commercial websites, though this isn&#8217;t always the case. Whether you are using a cookie to keep track of someone&#8217;s buying habits, or just to more specifically tailor your site to fit their personal needs, your cookies should always be encrypted.</p>
<p>If you do not encrypt your cookies they are viewable as plain text by the user and thus, he or she can then easily modify the cookie&#8217;s data. Not only can this have bad results for your site, as PHP automatically loads cookies into variables (and thus their modifications), but they could LIE to you about their buying habits or their household income, now isn&#8217;t that terrible?</p>
<p>Seriously now, the main flaw is that the modified cookies are automatically loaded into PHP and have basically the same effect as creating variables from the URL string (shown in Section 3). If you don&#8217;t encrypt your cookies your PHP program must be set up to distinguish variables that you set from variables that the user may set by modifying their cookies, it&#8217;s not an easy task.</p>
<p>Cookie encryption is as easy as:</p>
<font color="#1FD1E0" face="Courier New">
	&lt;?<br>
	 setcookie(base64_encode($cookie));<br>
	?&gt;<br><br></font>
<p>That&#8217;s it. Using the built-in base64_encode() function, the cookie will be encrypted before being sent to the user. It&#8217;s such an easy hole to fix; it&#8217;s amazing that not more people do. Using just the base64_encode() function should provide sufficient security, but if someone were to know what method you had used to encrypt it, decrypting it would be as easy as loading the cookie into PHP and running base64_decode(). If you handle highly sensitive data with cookies and want better security, there are other encryption functions you can use that are supplied by PHP.</p>
<h3>Section 7: Protecting Scripts</h3><br>
<p>Unfortunately there isn&#8217;t a lot you can do to protect your script from people who have physical access to your system. The scripts must be readable or else the web server will not be able to load them, meaning that anyone who has security clearance at or above the level of the web server can potentially access your scripts. This is rather dangerous because anyone who has access to your scripts can see how you have encrypted your scripts, see where you store the passwords, modify the scripts content, and do basically whatever they want to them. The good news to this, however, are that the chances of your script being released by the web server are slim, and doubly so if you have installed PHP as a module.</p>
<h3>Section 8: One-way Password Authentication</h3><br>
<p>When a script works with passwords unfortunately it is common to see some of them work like this (Note that the decrypt() function is not a PHP function, but merely a hypothetical representation of some user-created functions):</p><br><br>
<font color="#1FD1E0" face="Courier New">
	&lt;?<br>
	 $userpass = decrypt($userpass);<br>
	 if ($attemptedpass == $userpass) {<br>
	 print(&#8220;Welcome to the system.&#8221;);<br>
	 } else {<br>
	 print(&#8220;Wrong. Try again.&#8221;);<br>
	 }<br>
	?&gt;<br><br></font>
<p>The problem with the script is that you decrypt the password at all. The decrypted version of the users password is stored in memory, even if only temporarily, and is being passed around inside your script. It is not safe. A much safer version of this is to use one-way encryption to compare the passwords.</p><br><br>
<p>Compare the above script with this one:</p>
<font color="#1FD1E0" face="Courier New">
	&lt;?<br>
	 If(crypt($attemptedpass) == $userpass) {<br>
	 print(&#8220;Welcome to the system.&#8221;);<br>
	 } else {<br>
	 print(&#8220;Wrong. Try again.&#8221;);<br>
	 }<br>
	?&gt;<br><br></font>
<p>This script is much safer. The attempted password is encrypted, and if it is the same as the $userpass, the encrypted versions of both will match. This makes sure that no unencrypted passwords are being passed around throughout your PHP script.</p>
<p>---</p>
<p>That&#8217;s the end of the tutorial. If you had the stomach to read this much of my writing, and liked it, vote for it. :)</p>
<p>-Richard J. Silvers</p>


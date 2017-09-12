---
layout: post
title:  How to store a password
date:   2017-09-12 20:41:34 +0300
comments: true
---

Recently I was pen testing some boring web apps and I noticed how people **REALLY** 
don't know how to store passwords in a database. I couldn't judge them because no one
thought them how they should store passwords securely in a database.

For average Joe storing a password in a database is the same as storing email and 
other stuff ... **AS PLAIN TEXT**. That's really bad, I mean **REALLY REALLY REALLY**
horrible. This is the worst thing you can do when you store users password.

![store plain password](/images/store-plain-password.jpg)

So I thought I should write some about it. I don't wanna make my blog a tutorial site
where you can copy and paste code but I thought I should at least give them a highlight.

## Here is why storing password in clear is bad

![have i been pwned myspace](/images/haveibeenpwned-myspace.png)

More pawned sites at [https://haveibeenpwned.com/PwnedWebsites](https://haveibeenpwned.com/PwnedWebsites)

Imagine if someone used the same password in their PayPal account. In the matter of fact, 
You don't need to imagine, as 
[research by Sophos in 2013](https://nakedsecurity.sophos.com/2013/04/23/users-same-password-most-websites/)
found out 55% of net users use the same password for most, if not all, websites. 

![i will just use the same password](/images/i-will-just-use-the-same-password.jpg)

Plus my email was found in three data breaches. If I have been using the same password as the
same as my Facebook, Google or GitHub accounts it's a disaster, right?

> In a side note, check out if u have been pawned by clicking [here](https://haveibeenpwned.com).

Now I think you understood why you should store users password securely. Not doing so
is letting your client and customer down with you when you got hacked.

## How to store a password in a database?

I will first list how you should **NOT** store passwords then I will show you the right way 
to store a password.

## 1. As a plain text

> Storing plain text passwords in the database is a sin. [*](http://www.geeksforgeeks.org/store-password-database/)

A password is not some other field you just pass to the database. It's the most confidential 
non-financial property I could think of. So please don't store our password in plain text for God sake.

***PLEASE NEVER DO THIS AGAIN***.

## 2. As Base64 encoded
You might be surprised but believe me or not, one of the sites I was testing was using base64 
as a password storage.

This is just plain stupid. Base64 encoding is an **ENCODING**, no security at all. 
Even Caesar cipher is better than base64 encoding. Your adversary can just decode it and boom ... 
a password in a clear.

## 3. Encrypt them with a single key
A lot of people do this ([Including Adobe](https://nakedsecurity.sophos.com/2013/11/04/anatomy-of-a-password-disaster-adobes-giant-sized-cryptographic-blunder/)), 
they have a **"SUPER SECRET"** key in the backend to encrypt every password in the database.

Well, this is defiantly better than the previous once. Even if an adversary compromises you 
database he/she will get only the cipher of the password and they don’t know the key.

What's wrong with it.

### First: Password is still recoverable
If the attacker found a vulnerability in the system and learn the key he can just decipher 
them and all the passwords are now in clear.

### Second: It's easy to brute force
The attacker only needs to try one key for every user. Let me rephrase it, The attacker 
only needs to go through the key space once to compromise every password. If they found
a key for deciphering one password they can use it to decipher every password.

### Third: An adversary can have a rainbow table for every key you could possibly have as a password
Plus they don't need to encrypt every possible password. They can encrypt one chosen password with 
every possible key once and sign up for your service with that password and look up the cipher 
in the rainbow table to learn the key.

I can list more but this should be enough to convince you not to use it

## 4. Encrypt it by itself as a key
When I was in [Bahir Dar University](http://bdu.edu.et) defending some project of mine I was suggested by one of 
the examiners to encrypt the password by it self as a key to store as a password. I know 
it's not secure but I didn't judge her because that's how she learned about hashing/one-way function.

The problem is when lecturers thought their students about a hash functions they use this analogy. 
Let's say I have a users password in a registration form and I encrypt it's with it self and 
store the cipher in the database. Now I can throw away the password. If a user login later I can 
encrypt the provided password with it self and if the cipher text match with what ever stored in 
the database I will let them in.

Plus even if an attacker compromises the database or even the whole system he can't decipher the 
cipher to get the password because the key for the decryption is the password it self and we don't
have that either. Sound secure?

In fact, this is again better than the previous one but let's see what's wrong with it.

### First: Similar passwords have similar cipher
Let's say Facebook was using this and 1 million out of the billion users use the same password '123456'. 
Now an attacker can brute force once cipher to learn a million users password

Or simply, if i saw someone else's password cipher and it's the same as mine. Mean we have the same password!

### Second: Rainbow table
An adversary can have a rainbow table for every password just by going in the key space once just 
like the previous one. Every password have the same corresponding key.

We can also generate a rainbow table for the most used passwords which is economic.

### Third: Depending on the cipher you are using it can leak the length of the password.

## 5. Using cryptographic hashing functions
If you don't know what cryptographic hash functions are 

> A cryptographic hash function is a special class of hash function that has certain properties 
which make it suitable for use in cryptography. It is a mathematical algorithm that maps data 
of arbitrary size to a bit string of a fixed size (a hash function) which is designed to also 
be a one-way function, that is, a function which is infeasible to invert. The only way to recreate
 the input data from an ideal cryptographic hash function's output is to attempt a brute-force 
 search of possible inputs to see if they produce a match, or use a rainbow table of matched hashes. 
 Bruce Schneier has called one-way hash functions "the workhorses of modern cryptography".

This is way better than any of the above but still not secure enough to store password. Let's see why.

### First: Google search / Rainbow table
I better demonstrate this one. Google for `5d7845ac6ee7cfffafc5fe5f35cf666d`. Awesome right?

![wonka md5](/images/wonka-md5.jpg)

You think md5 is the only one? Google for `f2b14f68eb995facb3a1c35287b778d5bd785511`
and `fcf730b6d95236ecd3c9fc2d92d7b6b2bb061514961aec041d6c7a7192f592e4`

and perhaps for `348735696e74c45e7fbf9c6839d87f891486d19e5059db7e397d5086e486dc0051a533752805dc9288463673f0a6fcbf2a655548738a85305b2d571bae44a71e` too.

You are screwed if your adversary knows how to Google. lol

I will add more on this but let's address this issue and we can talk about the rest later

## 6. Using a cryptographic hash function with a salt
If you don't know what salt mean

> In cryptography, a salt is random data that is used as an additional input to a one-way function
that "hashes" a password or passphrase. The primary function of salts is to defend against dictionary
attacks or against its hashed equivalent, a pre-computed rainbow table attack.

> A new salt is randomly generated for each password. In a typical setting, the salt and the password
(or its version after Key stretching) are concatenated and processed with a cryptographic hash function,
and the resulting output (but not the original password) is stored with the salt in a database. Hashing
allows for later authentication without keeping and therefore risking the plaintext password in the event
that the authentication data store is compromised.

So let's Google for a hash  `e37ad635765263fc3efa63bf0142801a`

No result found right? That's because I added 16 bytes long random none sense(AKA salt) before/after it.

Is it secure now?

Not really!

Let's see what's wrong with cryptographic hash function(with salt) for password storage

### First: cryptographic hash function is really really fast

One of the specifications of a cryptographic hash function is to be fast but we don't want it to be 
fast to be cracked.

Thanks for down of cryptocurrency there are ASICs and GPU which and can hash millions and billions of 
hash per second.

Cracking a single hash would take a couple of minute or hours in average I suppose.

### Second: Even with you salt in place a gigantic rainbow table is feasible for an adversary with a great computational power.

### Third: Length extension attack
I will let you Google that too

Right now i hope you realized that none of the above are secure.

## Now let's see how you SHOULD store your password.

Believe it or not, this is the hardest part. I can just recommend you how you should store users password
but it might be not secure next year so it's hard if people don't find an updated state of the art 
password storage technique.

I hope I will remove or recommend a better one if this information became irrelevant and misguiding but 
please Google the recent blog post if you are reading this in the distant future.

As today(Sept 12, 2017) the recommended way of storing password is using 

## Adaptive hash function

> Adaptive one-way functions compute a one-way (irreversible) transform. Each function allows configuration 
of ‘work factor’. Underlying mechanisms used to achieve irreversibility and govern work factors 
(such as time, space, and parallelism) vary between functions and remain unimportant to this discussion.

> As it turns out, just hashing a password using md5() or even sha512() isn't good enough. Cryptographic hash
 functions are designed to be fast. This is good for cryptographic needs such as signing. But for password 
 hashing, that's a problem since it allows an attacker to brute force a lot of passwords very quickly. 
 Adding a salt makes it resistant to rainbow tables, but not resistant to brute forcing where that salt 
 is known.

> By using either a stretched algorithm (Such as PBKDF2) or an algorithm designed to be slow (Such as bcrypt),
 a much better defense against brute forcing will be had.

The four [OWASP](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet) recommended adaptive hash functions are
 - **Argon2** is the winner of the [password hashing competition](https://password-hashing.net/) and should be considered as your first choice for new applications;
 - **PBKDF2** when FIPS certification or enterprise support on many platforms is required;
 - **scrypt** where resisting any/all hardware accelerated attacks is necessary but support isn’t.
 - **bcrypt** where scrypt support is not available.

That being said I usually use bcrypt because it's already built into PHP as a default but that doesn't 
mean you can't use the others in PHP but they require you to install an extension.

And in the bright side PHP have started built-in support for Argon2 in [PHP 7.2](https://wiki.php.net/rfc/argon2_password_hash).

Now I highlight how you can corporate bcrypt into you PHP project.

> Make sure you are using PHP >= 5.5

Now let's say the user is registering and we have their password, we can bcrypt it as  

```php?start_inline=1
$hashed = password_hash($password, PASSWORD_BCRYPT);
```

now go head and store the password.
* Make sure the password field is at least 100 characters long
* No need to generate salt by your self PHP got your back
* There is a third parameter which specifies options but please leave it as it is, it has a pretty good default

When the user login we can authenticate them like this

```php?start_inline=1
$stmt = $pdo->prepare('SELECT * FROM users WHERE email=:email LIMIT 1;');
$stmt->execute([':email' => $email]);

if ($stmt->rowCount() > 0 && ($user = $stmt->fetch()) &&
	password_verify($_POST['password'], $user->password)) {
	// login successed
} else {
	// login failed
}
```

That’s how easy it is. But please don’t deal with password your self, use a framework.

That's all for today folks.
Please give some feedback, it will help me in my future writings.

Happy new year and thanks for reading.

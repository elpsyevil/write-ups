# VulnLawyers

## Subdomain discovery

<p align="center">
  <img  src="Pasted image 20210723164534.png">
</p>

<p align="center">
  <img  src="Pasted image 20210723164549.png">
</p>

Flag 1 : `[^FLAG^E78DEBBFDFBEAFF1336B599B0724A530^FLAG^]`

## Content discovery
Found login page :

<p align="center">
  <img  src="Pasted image 20210723164637.png">
</p>
<p align="center">
  <img  src="Pasted image 20210723164726.png">
</p>

Got redirected to this page but if we check the response in Burp we can see the Flag 

<p align="center">
  <img  src="Pasted image 20210723165008.png">
</p>

Flag 2 : `[^FLAG^FB52470E40F47559EBA87252B2D4CF67^FLAG^]`

And we can see that there is a subfolder `/lawyers-only` :

<p align="center">
  <img  src="Pasted image 20210723170229.png">
</p>

Found users subfolder in `data` subdomain :

<p align="center">
  <img  src="Pasted image 20210723172140.png">
</p>

Flag 3 : `[^FLAG^25032EB0D322F7330182507FBAA1A55F^FLAG^]`

Brute forced password using those emails and found :
`jaskaran.lowe@vulnlawyers.co.uk:summer`

<p align="center">
  <img  src="Pasted image 20210723175214.png">
</p>

Flag 4 : `[^FLAG^7F1ED1F306FC4E3399CEE15DF4B0AE3C^FLAG^]`

<p align="center">
  <img  src="Pasted image 20210723180634.png">
</p>

When I click on update, a GET request is done on `lawyers-only-profile-details/4`
<p align="center">
  <img  src="Pasted image 20210723180447.png">
</p>

It's storing the creds of the user :

<p align="center">
  <img  src="Pasted image 20210723180653.png">
</p>

Maybe it's vulnerable to IDOR
Let's change the number

It is :

<p align="center">
  <img  src="Pasted image 20210723180830.png">
</p>

Flag 5 : `[^FLAG^938F5DC109A1E9B4FF3E3E92D29A56B3^FLAG^]`

And we get the creds of `Shayne Cairns`, let's login :

```log
shayne.cairns@vulnlawyers.co.uk
q2V944&#2a1^3p
```

<p align="center">
  <img  src="Pasted image 20210723181017.png">
</p>

When we click on delete we get the last flag :
<p align="center">
  <img  src="Pasted image 20210723181135.png">
</p>

Flag 6 : `[^FLAG^B38BAE0B8B804FCB85C730F10B3B5CB5^FLAG^]`





# VulnBegin

<p align="center">
  <img  src="Pasted image 20210723144641.png">
</p>

## Subdomain discovery
```bash
nslookup -type=any vulnbegin.co.uk 8.8.8.8
```

<p align="center">
  <img  src="Pasted image 20210723144713.png">
</p>

First flag : `^FLAG^BED649C4DB2DF265BD29419C13D82117^FLAG^`

<p align="center">
  <img  src="Pasted image 20210723145635.png">
</p>

Found `server.vulnbegin.co.uk`

<p align="center">
  <img  src="Pasted image 20210723145737.png">
</p>

Second flag : `^FLAG^E858ED9649E57BECE9ACD1A4C60D3446^FLAG^`

<p align="center">
  <img  src="Pasted image 20210723155532.png">
</p>

<p align="center">
  <img  src="Pasted image 20210723155545.png">
</p>

Third flag : `^FLAG^047524FE61AE6B5FD1D184994C7322FC^FLAG^`
## Content Discovery

Found `robots.txt` and `cpadmin` in the `www` subdomain:

<p align="center">
  <img  src="Pasted image 20210723152436.png">
</p>

<p align="center">
  <img  src="Pasted image 20210723152500.png">
</p>

Fourth flag : `^FLAG^2B22E2CB70E218510802B0359488F6A2^FLAG^`

<p align="center">
  <img  src="Pasted image 20210723145850.png">
</p>

Found `robots.txt` file and a secrect sub-directory :
`/s3cr3T_d1r3ct0rY/`
## Login bruteforce

<p align="center">
  <img  src="Pasted image 20210723152633.png">
</p>

<p align="center">
  <img  src="Pasted image 20210723152835.png">
</p>

Password is : `159753`

<p align="center">
  <img  src="Pasted image 20210723152921.png">
</p>

Fifth flag : `^FLAG^93D7491FB4B054FB5C5AC3E0292BE41C^FLAG^`

<p align="center">
  <img  src="Pasted image 20210723152952.png">
</p>

`token=2eff535bd75e77b62c70ba1e4dcb2873`

Found env folder in `cpadmin`:

<p align="center">
  <img  src="Pasted image 20210723154303.png">
</p>

Sixth flag : `[^FLAG^F6A691584431F9F2C29A3A2DE85A2210^FLAG^]`

`X-Token: 492E64385D3779BC5F040E2B19D67742`

Used the token on `server` subdomain and worked :

<p align="center">
  <img  src="Pasted image 20210723154442.png">
</p>

Seventh flag : `^FLAG^0BDC60CC5E283476E7107C814C18DCCF^FLAG^`

Added to token match/replace rules so that Burp sends it everytime with the header

<p align="center">
  <img  src="Pasted image 20210723154641.png">
</p>

Found a user sub directory that leads to a FLAG 

<p align="center">
  <img  src="Pasted image 20210723154918.png">
</p>

Eighth flag : 

`^FLAG^7B3A24F3368E71842ED7053CF1E51BB0^FLAG^`

Let's brute force users :

<p align="center">
  <img  src="Pasted image 20210723155203.png">
</p>

Found admin with had `id=5` 

Ninth flag : `[^FLAG^3D82BE780F46EE86CE060D23E6E80639^FLAG^]`
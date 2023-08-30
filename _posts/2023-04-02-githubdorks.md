---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://www.springboard.com/blog/wp-content/uploads/2021/07/What-is-github-and-how-to-use-it-scaled-1536x589.jpg"> Github Dork for finding Sensitive Information
date: 2023-04-02 00:00:02 +240
categories: [Resources, bugbounty]
tags: [resources,github-dork,google-dorks,dorks,discovery,recon,reconnaissance,information-gathering] # TAG names should always be lowercase

---
&nbsp; &nbsp;


## 🔍 GitHub Dorks for Finding Sensitive Information

GitHub is not just a platform for version control and collaborative software development, but also a goldmine for sensitive information. GitHub Dorks are specific search queries that can help to find this sensitive information, such as API keys, passwords, usernames, and more. Here are some useful GitHub Dorks categorized by their purpose:

### 🔑 GitHub Dorks for Finding Files

`filename:manifest.xml` 
`filename:travis.yml  `
`filename:vim_settings.xml`  
`filename:database  `
`filename:prod.exs NOT prod.secret.exs  `
`filename:prod.secret.exs  `
`filename:.npmrc _auth  `
`filename:.dockercfg auth  `
`filename:WebServers.xml  `
`filename:.bash_history <Domain name>  `
`filename:sftp-config.json  `
`filename:sftp.json path:.vscode  `
`filename:secrets.yml password  `
`filename:.esmtprc password  `
`filename:passwd path:etc  `
`filename:dbeaver-data-sources.xml  `
`path:sites databases password  `
`filename:config.php dbpasswd  `
`filename:prod.secret.exs  `
`filename:configuration.php JConfig password  `
`filename:.sh_history  `
`shodan_api_key language:python  `
`filename:shadow path:etc  `
`JEKYLL_GITHUB_TOKEN  `
`filename:proftpdpasswd  `
`filename:.pgpass  `
`filename:idea14.key`  
`filename:hub oauth_token`  
`HEROKU_API_KEY language:json  `
`HEROKU_API_KEY language:shell  `
`SF_USERNAME salesforce  `
`filename:.bash_profile aws  `
`extension:json api.forecast.io`  
`filename:.env MAIL_HOST=smtp.gmail.com ` 
`filename:wp-config.php  `
`extension:sql mysql dump  `
`filename:credentials aws_access_key_id`  
`filename:id_rsa or filename:id_dsa`

### 🧧 GitHub Dorks for Finding Languages

`language:python username`  
`language:php username  `
`language:sql username  `
`language:html password  `
`language:perl password`  
`language:shell username`  
`language:java api  `
`HOMEBREW_GITHUB_API_TOKEN language:shell`

### 🔐GiHub Dorks for Finding API Keys, Tokens and Passwords

`api_key  `
`“api keys”  `
`authorization_bearer:  `
`oauth  `
`auth  `
`authentication  `
`client_secret  `
`api_token:  `
`“api token”  `
`client_id  `
`password  `
`user_password`  
`user_pass  `
`passcode  `
`client_secret`  
`secret  `
`password hash`  
`OTP  `
`user auth`

### 🧑‍🦲GitHub Dorks for Finding Usernames

`user:name (user:admin)  `
`org:name (org:google type:users)  `
`in:login (<username> in:login)`  
`in:name (<username> in:name)`  
`fullname:firstname lastname (fullname:<name> <surname>)  `
`in:email (data in:email)`

### 🗓️GitHub Dorks for Finding Information using Dates

`created:<2012–04–05  `
`created:>=2011–06–12  `
`created:2016–02–07 location:iceland  `
`created:2011–04–06..2013–01–14 <user> in:username`

### GitHub Dorks for Finding Information using Extension

`extension:pem private`  
`extension:ppk private  `
`extension:sql mysql dump  `
`extension:sql mysql dump password`  
`extension:json api.forecast.io`  
`extension:json mongolab.com  `
`extension:yaml mongolab.com  `
`[WFClient] Password= extension:ica  `
`extension:avastlic “support.avast.com”  `
`extension:json googleusercontent client_secret`


=======================================

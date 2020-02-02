# easyrecon

A collection of my tools to do easy passive &amp; active recon.

## 1. Nmap1 for basic - full nmap scan

```
#!/bin/bash
echo -e "-----------------------"
echo -e "NMAP SCANNING - FAST"
echo -e "-----------------------"
nmap -F $1 -Pn -T5
echo -e "-----------------------"
echo -e "NMAP SCANNING - ENUM"
echo -e "-----------------------"
nmap --script=http-enum -p 80,433 -Pn -T4 $1
echo -e "-----------------------"
echo -e "NMAP SCANNING - SV - SC"
echo -e "-----------------------"
nmap -sV -sC -Pn -T5 $1
```

## 2. Masscan1 for full port scan

```
#!/bin/bash
masscan $1 -p0-65535 --max-rate 1000
```

## 3. Google dorks script ( to be updated )

```
site: site.com
```
> Will update others / scripts

## 4. Wayback / commonurl 

```
wayback-logo(){
	echo -e """

WW      WW                 BBBBB                  kk     
WW      WW   aa aa yy   yy BB   B    aa aa   cccc kk  kk 
WW   W  WW  aa aaa yy   yy BBBBBB   aa aaa cc     kkkkk  
 WW WWW WW aa  aaa  yyyyyy BB   BB aa  aaa cc     kk kk  
  WW   WW   aaa aa      yy BBBBBB   aaa aa  ccccc kk  kk 
                    yyyyy                                
	"""
}

WAYBACK(){
	wget -qO- "http://web.archive.org/cdx/search/cdx?url=*.${IP}/*&output=json&fl=original&collapse=urlkey&filter=statuscode:200" |sort |uniq |cut -d "'" -f2|cut -d "\"" -f2 > Output/$IP/wayback-output/$IP-wayback-$(date +"%Y-%m-%d").txt
}
VIRUSTATOL(){
	curl -s --request GET --url "https://www.virustotal.com/vtapi/v2/domain/report?apikey=api_key&domain=${IP}"| jq --raw-output -r '.undetected_urls[]? | .[]'|grep 'http' >> Output/$IP/wayback-output/$IP-wayback-$(date +"%Y-%m-%d").txt
}
COMMONCRAWL(){
	curl -sX GET "http://index.commoncrawl.org/CC-MAIN-2017-09-index?url=*.${IP}/*&output=json"|jq -r .url |sort -u >> Output/$IP/wayback-output/$IP-wayback-$(date +"%Y-%m-%d").txt
}

if [[ $1 == '--wayback' ]] || [[ $1 == '-w' ]]; then
	
	wayback-logo
	QUOTES
	IP=$2
	mkdir -p Output/$IP/wayback-output
	WAYBACK
	VIRUSTATOL
	COMMONCRAWL
	count=$(wc -l Output/$IP/wayback-output/${IP}-wayback-$(date +"%Y-%m-%d").txt|cut -d " " -f1)
	echo "${count}"
	echo -e "\n${YELLO}[NOTE] ${RED}[${count}]${NC} Url's are Loaded from $IP-wayback-$(date +"%Y-%m-%d").txt${NC}"
	cat Output/$IP/wayback-output/$IP-wayback-$(date +"%Y-%m-%d").txt
	exit 1

fi
```

## 5. href-urls

* convert links from text file to hyper links.

```
#!/bin/bash
echo "<html>" >> $1-urls.html
echo "<head>" >> $1-urls.html
echo "<title>Generated hyper Link URLS</title>" >> $1-urls.html
echo "</head>" >> $1-urls.html
echo "<body>" >> $1-urls.html

cat $1 | while read urls; do
        echo "<a href=${urls}>${urls}</a></br></br>" >> $1-urls.html
done

echo "</body>" >> $1-urls.html
echo "</html>" >> $1-urls.html
```

## 6. Parameter scanning - arjun

```
#!/bin/bash
python3 ~/tools/Arjun/Arjun/arjun.py -u $1 -t 15 --get -f ~/wordlists/burp-parameter-names.txt
```

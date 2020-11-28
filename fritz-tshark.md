# FRITZ!Box Packet Capture

## fritz.sh
```bash
fritz_user=
fritz_pwd=
fritz_challenge=$(wget -qO- http://fritz.box/login_sid.lua | grep -o "<Challenge>[a-z0-9]\{8\}" | cut -d'>' -f2)
fritz_hash=$(echo -n "$fritz_challenge-$fritz_pwd" | iconv -f UTF-8 -t UTF-16LE | md5sum | cut -d' ' -f1)
fritz_sid=$(wget -qO- "http://fritz.box/login_sid.lua?user=$fritz_user&response=$fritz_challenge-$fritz_hash" | grep -o "<SID>[a-z0-9]\{16\}" |  cut -d'>' -f2)
wget -qO- "http://fritz.box/cgi-bin/capture_notimeout?ifaceorminor=2-0&snaplen=&capture=Start&sid=$fritz_sid"
```

## tshark
* `./fritz.sh | tshark -i- -l -Y 'dns && dns.flags.response==1' -e dns.qry.type -e dns.qry.name -e dns.a -e dns.aaaa -e dns.cname -T fields -E separator=';' > dns.log`
* `./fritz.sh | tshark -i- -l -Y 'tcp and tcp.flags.syn == 1 and tcp.flags.ack == 0' -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport  -T fields -E separator=';'`
* `./fritz.sh | tshark -i- -qz hosts`

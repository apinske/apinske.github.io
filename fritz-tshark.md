# FRITZ!Box Packet Capture

## fritz.sh
```bash
#!/bin/sh
fritz_user=
fritz_pwd=
fritz_challenge=$(wget -qO- http://fritz.box/login_sid.lua | grep -o "<Challenge>[a-z0-9]\{8\}" | cut -d'>' -f2)
fritz_hash=$(echo -n "$fritz_challenge-$fritz_pwd" | iconv -f UTF-8 -t UTF-16LE | md5sum | cut -d' ' -f1)
fritz_sid=$(wget -qO- "http://fritz.box/login_sid.lua?user=$fritz_user&response=$fritz_challenge-$fritz_hash" | grep -o "<SID>[a-z0-9]\{16\}" |  cut -d'>' -f2)
curl -s "http://fritz.box/cgi-bin/capture_notimeout?ifaceorminor=2-0&snaplen=&capture=Start&sid=$fritz_sid"
```

## tshark
* DNS `./fritz.sh | tshark -i- -l -Y 'dns && dns.flags.response==1' -e dns.qry.type -e dns.qry.name -e dns.a -e dns.aaaa -e dns.cname -T fields -E separator=';' > dns.log`
* TCP `./fritz.sh | tshark -i- -l -Y 'tcp and tcp.flags.syn == 1 and tcp.flags.ack == 0' -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport  -T fields -E separator=';'`
* `./fritz.sh | tshark -i- -qz hosts`
* YouTube
    * `./fritz.sh | tshark -i- -l -t ad -e _ws.col.Time -e ip.src -e udp.srcport -e tcp.srcport -e ip.dst -e udp.dstport -e tcp.dstport -e tcp.stream -e udp.stream -Y 'ip.src==192.168.100.22' -T fields -E separator=';' -Q | awk -F';' -v OFS=';' '{ $1=substr($1,12,5); print $0 >> $1}'`
    * `watch 'cat ??:?? | sort | uniq -c'`

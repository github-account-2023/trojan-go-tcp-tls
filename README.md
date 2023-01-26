# trojan-go-tcp-tls

```
latest_version="$(wget --no-check-certificate -qO- https://api.github.com/repos/p4gefau1t/trojan-go/tags | grep 'name' | cut -d\" -f4 | head -1)"
trojango_link="https://github.com/p4gefau1t/trojan-go/releases/download/${latest_version}/trojan-go-linux-amd64.zip"
mkdir -p "/usr/bin/trojan-go"
mkdir -p "/etc/trojan-go"
cd `mktemp -d`
wget -nv "${trojango_link}" -O trojan-go.zip
unzip -q trojan-go.zip && rm -rf trojan-go.zip
mv trojan-go /usr/bin/ && chmod +x /usr/bin/trojan-go
mv geoip.dat /etc/trojan-go/geoip.dat
mv geosite.dat /etc/trojan-go/geosite.dat
mv example/trojan-go.service /etc/systemd/system/trojan-go.service
sed -i 's/nobody/root/g' /etc/systemd/system/trojan-go.service
if [ ! -f "/etc/trojan-go/config.json" ]; then
  mv example/server.json /etc/trojan-go/config.json
fi
systemctl daemon-reload
systemctl reset-failed
echo "trojan-go is installed."

# https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert
apt install socat
curl https://get.acme.sh | sh

# make sure tcp port 80 is opened or use zerossl
~/.acme.sh/acme.sh --register-account -m whatever@gmail.com

# replace yourdomain.com and you can add --server letsencrypt if you don't like zerossl
~/.acme.sh/acme.sh --issue --standalone -d yourdomain.com
~/.acme.sh/acme.sh --installcert --key-file /root/private.key --fullchain-file /root/cert.crt -d yourdomain.com

# edit trojan-go config.json
vim /etc/trojan-go/config.json
systemctl restart trojan-go
systemctl enable trojan-go
```

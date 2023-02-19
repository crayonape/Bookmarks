## Naive Proxy



based on OCI && Cloudflare && CentOS 8



#### Command Line

dnf install python3-librepo -y

dnf install epel-release

dnf upgrade

yum install snapd

systemctl enable --now snapd.socket

ln -s /var/lib/snapd/snap /snap

reboot

snap install core

dnf remove certbot

snap install --classic certbot

ln -s /snap/bin/certbot /usr/bin/certbot



wget https://go.dev/dl/go1.19.linux-amd64.tar.gz

tar -zxvf go1.19.linux-amd64.tar.gz -C /usr/local/

echo export PATH=$PATH:/usr/local/go/bin  >> /etc/profile

source /etc/profile

go version

go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest

~/go/bin/xcaddy build --with github.com/caddyserver/forwardproxy@caddy2=github.com/klzgrad/forwardproxy@naive --with github.com/caddy-dns/cloudflare@latest

cp caddy /usr/bin/

caddy version

setcap cap_net_bind_service=+ep /usr/bin/caddy

bash -c 'echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf'

bash -c 'echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf'

sysctl -p

mkdir /etc/caddy/

cat /dev/urandom | tr -dc a-zA-Z0-9 | head -c32; echo;

vim /etc/caddy/Caddyfile

```bash
# for test
:80 {
        respond "Hello, world!"
}
# for proxy
:443, example.com {
        tls mail-address {
                dns cloudflare your-cloudflare-api-token
        }

        route {
                forward_proxy {
                        basic_auth username password
                        hide_ip
                        hide_via
                        probe_resistance
                }
                reverse_proxy https://www.google.com {
                        header_up Host {upstream_hostport}
                }
        }
}
```

caddy fmt --overwrite /etc/caddy/Caddyfile

caddy run --config /etc/caddy/Caddyfile

systemctl start firewalld

firewall-cmd --zone=public --add-port=443/tcp --permanent 

firewall-cmd --reload



#### Cloudflare

generate Api Token 

edit DNS only in DNS tab

select Full in TLS tab



#### OCI

allow ingress of 443 in Security List
---
layout: post
title: "Acesso à rede sem fios (Eduroam)"
thumbnail: 'thumbnails/tutorials/eduroam.svg'
date: 2013-04-08
updated: 2024-09-13
description: "Como configurar a rede eduroam na sua distribuição GNU/Linux."
tags: []
tutorial : true
comments: false
author: "Leandro Ricardo"
---

Neste tutorial pode encontrar informações de como configurar a rede eduroam na sua distribuição de GNU/Linux.


## Certificados

Para poder conectar-se à rede **eduroam**, vai ser preciso indicar um certificado de autenticação, cujo destino e ficheiro difere entre distribuições GNU/LINUX.
Assim sendo, pode confirmar na seguinte tabela qual é o caminho e ficheiro para as diferentes distribuições:

Para poder conectar-se à rede **eduroam** é necessário indicar um certificado de autentificação, cuja a sua localização difere para as diferentes distribuições GNU/LINUX. Assim sendo, pode confirmar na seguinte tabela qual é o caminho do ficheiro para as diferentes distribuições.

|            Distribuição             |        Ficheiro a selecionar         |
|:-----------------------------------:|:------------------------------------:|
| **Debian/Ubuntu/Gentoo/Arch Linux** | `/etc/ssl/certs/ca-certificates.crt` |
| **Fedora/RHEL**                     | `/etc/pki/tls/certs/ca-bundle.crt`   |
| **openSUSE/SLE**                    | `/etc/ssl/ca-bundle.pem`             |


________________________________

>Caso continue sem conexão, selecione o certificado **_DigiCert_Assured_ID_Root_CA.pem_**

## Gnome3

No canto superior direito deve selecionar o ícon de rede
![gnomeInternetIcon](img/gnome3-1.png)

Após selecionar a rede **eduroam**, deverá preencher os dados como na print, colocando o seu email e password da UA.

![gnomeInternetMenu](img/gnome3-2.png)

Depois de confirmar basta aguardar a validação de dados.

![gnomeInternetLoad](img/gnome3-3.png)

________________________________

<!--## KDE-->

<!--No canto inferior direito selecionar o ícon de rede. -->
<!--![kdeInternetIcon](img/kde-1.png)-->

<!--Após selecionar a rede **eduroam**, deverá preencher os dados como nas secções anteriores. (ex: [Unity](#unity))-->

<!--![kdeInternetMenu](img/kde-2.png)-->

<!--Despois de confirmar os dados inseridos, estes serão validados e terá a conexão com a rede **eduroam**.-->

<!--![kdeInternetLoad](img/kde-3.png)-->

<!--________________________________-->

## Linha de Comandos

{{<callout type="warning">}}
(**Atenção: Apenas para utilizadores com experiência...**)
{{</callout>}}

Caso prefira conectar-se à rede **eduroam** utilizando a consola existem duas opções:

### Método WPA-Supplicant

1 - Criar o ficheiro eduroam.conf e guardar o mesmo na home (~):

```bash
ctrl_interface=/var/run/wpa_supplicant

network={
        ssid="eduroam"
        key_mgmt=WPA-EAP
        eap=PEAP
        ca_cert="SUBSTITUIR"		# localização do ficheiro de certificados
        identity="UTILIZADOR@ua.pt"	# Substituir pelas vossas
        password="**********"		# credenciais de acesso
}
```
> Caso queira iniciar com a eduroam por padrão adicione a parte do network ao ficheiro /etc/wpa_supplicant/wpa_supplicant.conf (Os passos 3 e 4 passam a ser desnecessários)

2 - Alterar o texto ***SUBSTITUIR*** para o certificado de acordo com a sua distribuição

{{<callout type="info">}}
**Consultar a secção dos [certificados](#certificados)**
{{</callout>}}

```bash
ca_cert="SUBSTITUIR"
```

3 - De seguida, execute o seguinte comando

```bash
sudo pkill wpa_supplicant && sudo wpa_supplicant -i wlan0 -c /home/$USERNAME/eduroam.conf
```

4 - Por fim, será necessário fazer um pedido ao servidor de DHCP, também como súper-utilizador(___root___). Para tal, é preciso abrir outra consola e escrever:

```bash
sudo dhclient wlan0
```

Foi usado o cliente de DHCP `dhclient` (típico de Debian/Ubuntu/Fedora), mas poderia ser usado qualquer outro à escolha, por exemplo, o `dhcpcd` (típico de ArchLinux/Gentoo).

> Caso tenha problemas de "_disconnects_", ou problemas em obter um endereço IP, na distribuição Arch Linux, adicione a seguinte linha na secção _main_ do ficheiro _NetoworkManager.conf_ que se encontra em */etc/NetoworkManager/NetoworkManager.conf*
```bash
DHCP = dhclient
```

> Caso não queira abrir outra consola, pode-se executar o _wpa_supplicant_ em segundo plano usando:
~~~bash
sudo wpa_supplicant -i wlan0 -c/home/$USERNAME/eduroam.conf -B
~~~

Pronto, tem acesso à rede eduroam!


### Método IWD
1 - Criar o ficheiro eduroam.8021x e guardar o mesmo na pasta /var/lib/iwd com o seguinte conteúdo:

```bash
[Security]
EAP-Method=PEAP
EAP-Identity=UTILIZADOR@ua.pt                   # Email
EAP-PEAP-CACert=embed:eduroam_ca_cert
EAP-PEAP-ServerDomainMask=celeborn.ua.pt;galadriel.ua.pt
EAP-PEAP-Phase2-Method=MSCHAPV2
EAP-PEAP-Phase2-Identity=UTILIZADOR@ua.pt       # Email
EAP-PEAP-Phase2-Password=**********             # Password

[@pem@eduroam_ca_cert]
-----BEGIN CERTIFICATE-----
MIIFpDCCA4ygAwIBAgIQOcqTHO9D88aOk8f0ZIk4fjANBgkqhkiG9w0BAQsFADBs
MQswCQYDVQQGEwJHUjE3MDUGA1UECgwuSGVsbGVuaWMgQWNhZGVtaWMgYW5kIFJl
c2VhcmNoIEluc3RpdHV0aW9ucyBDQTEkMCIGA1UEAwwbSEFSSUNBIFRMUyBSU0Eg
Um9vdCBDQSAyMDIxMB4XDTIxMDIxOTEwNTUzOFoXDTQ1MDIxMzEwNTUzN1owbDEL
MAkGA1UEBhMCR1IxNzA1BgNVBAoMLkhlbGxlbmljIEFjYWRlbWljIGFuZCBSZXNl
YXJjaCBJbnN0aXR1dGlvbnMgQ0ExJDAiBgNVBAMMG0hBUklDQSBUTFMgUlNBIFJv
b3QgQ0EgMjAyMTCCAiIwDQYJKoZIhvcNAQEBBQADggIPADCCAgoCggIBAIvC569l
mwVnlskNJLnQDmT8zuIkGCyEf3dRywQRNrhe7Wlxp57kJQmXZ8FHws+RFjZiPTgE
4VGC/6zStGndLuwRo0Xua2s7TL+MjaQenRG56Tj5eg4MmOIjHdFOY9TnuEFE+2uv
a9of08WRiFukiZLRgeaMOVig1mlDqa2YUlhu2wr7a89o+uOkXjpFc5gH6l8Cct4M
pbOfrqkdtx2z/IpZ525yZa31MJQjB/OCFks1mJxTuy/K5FrZx40d/JiZ+yykgmvw
Kh+OC19xXFyuQnspiYHLA6OZyoieC0AJQTPb5lh6/a6ZcMBaD9YThnEvdmn8kN3b
LW7R8pv1GmuebxWMevBLKKAiOIAkbDakO/IwkfN4E8/BPzWr8R0RI7VDIp4BkrcY
AuUR0YLbFQDMYTfBKnya4dC6s1BG7oKsnTH4+yPiAwBIcKMJJnkVU2DzOFytOOqB
AGMUuTNe3QvboEUHGjMJ+E20pwKmafTCWQWIZYVWrkvL4N48fS0ayOn7H6NhStYq
E613TBoYm5EPWNgGVMWX+Ko/IIqmhaZ39qb8HOLubpQzKoNQhArlT4b4UEV4AIHr
W2jjJo3Me1xR9BQsQL4aYB16cmEdH2MtiKrOokWQCPxrvrNQKlr9qEgYRtaQQJKQ
CoReaDH46+0N0x3GfZkYVVYnZS6NRcUk7M7jAgMBAAGjQjBAMA8GA1UdEwEB/wQF
MAMBAf8wHQYDVR0OBBYEFApII6ZgpJIKM+qTW8VX6iVNvRLuMA4GA1UdDwEB/wQE
AwIBhjANBgkqhkiG9w0BAQsFAAOCAgEAPpBIqm5iFSVmewzVjIuJndftTgfvnNAU
X15QvWiWkKQUEapobQk1OUAJ2vQJLDSle1mESSmXdMgHHkdt8s4cUCbjnj1AUz/3
f5Z2EMVGpdAgS1D0NTsY9FVqQRtHBmg8uwkIYtlfVUKqrFOFrJVWNlar5AWMxaja
H6NpvVMPxP/cyuN+8kyIhkdGGvMA9YCRotxDQpSbIPDRzbLrLFPCU3hKTwSUQZqP
JzLB5UkZv/HywouoCjkxKLR9YjYsTewfM7Z+d21+UPCfDtcRj88YxeMn/ibvBZ3P
zzfF0HvaO7AWhAw6k9a+F9sPPg4ZeAnHqQJyIkv3N3a6dcSFA1pj1bF1BcK5vZSt
jBWZp5N99sXzqnTPBIWUmAD04vnKJGW/4GKvyMX6ssmeVkjaef2WdhW+o45WxLM0
/L5H9MG0qPzVMIho7suuyWPEdr6sOBjhXlzPrjoiUevRi7PzKzMHVIf6tLITe7pT
BGIBnfHAT+7hOtSLIBD6Alfm78ELt5BGnBkpjNxvoEppaZS3JGWg/6w/zgH7IS79
aPib8qXPMThcFarmlwDB31qlpzmq6YR/PFGoOtmUW4y/Twhx5duoXNTSpv4Ao8YW
xw/ogM4cKGR0GQjTQuPOAF1/sdwTsOEFy9EgqoZ0njnnkf3/W9b3raYvAwtt41dU
63ZTGI0RmLo=
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIGBTCCA+2gAwIBAgIQFNV782kiKCGaVWf6kWUbIjANBgkqhkiG9w0BAQsFADBs
MQswCQYDVQQGEwJHUjE3MDUGA1UECgwuSGVsbGVuaWMgQWNhZGVtaWMgYW5kIFJl
c2VhcmNoIEluc3RpdHV0aW9ucyBDQTEkMCIGA1UEAwwbSEFSSUNBIFRMUyBSU0Eg
Um9vdCBDQSAyMDIxMB4XDTI1MDEwMzExMTUwMFoXDTM5MTIzMTExMTQ1OVowYDEL
MAkGA1UEBhMCR1IxNzA1BgNVBAoMLkhlbGxlbmljIEFjYWRlbWljIGFuZCBSZXNl
YXJjaCBJbnN0aXR1dGlvbnMgQ0ExGDAWBgNVBAMMD0dFQU5UIFRMUyBSU0EgMTCC
AaIwDQYJKoZIhvcNAQEBBQADggGPADCCAYoCggGBAKEEaZSzEzznAPk8IEa17GSG
yJzPTj4cwRY7/vcq2BPT5+IRGxQtaCdgLXIEl2cdPdIkj2eyakFmgMjAtyeju8V8
dRayQCD/bWjJ7thDlowgLljQaXirxnYbT8bzRHAhCZqBakYgi5KWw9dANLyDHGpX
UdY259ab0lWEaFE5Uu6IzQSMJOAy4l/Twym8GUiy0qMDEBFSlm31C9BXpdHKKAlh
vIjMiKoDeTWl5vZaLB2MMRGY1yW2ftPgIP0/MkX1uFITlvHmmMTngxplH1nybEIJ
FiwHg1KiLk1TprcZgeO2gxE5Lz3wTFWrsUlAzrh5xWmscWkjNi/4BpeuiT5+NExF
czboLnXOfjuci/7bsnPi1/aZN/iKNbJRnngFoLaKVMmqCS7Xo34f+BITatryQZFE
u2oDKExQGlxDBCfYMLgLucX/onpLzUSgeQITNLx6i5tGGbUYH+9Dy3GI66L/5tPj
qzlOsydki8ZYGE5SBJeWCZ2IrhUe0WzZ2b6Zhk6JAQIDAQABo4IBLTCCASkwEgYD
VR0TAQH/BAgwBgEB/wIBADAfBgNVHSMEGDAWgBQKSCOmYKSSCjPqk1vFV+olTb0S
7jBNBggrBgEFBQcBAQRBMD8wPQYIKwYBBQUHMAKGMWh0dHA6Ly9jcnQuaGFyaWNh
LmdyL0hBUklDQS1UTFMtUm9vdC0yMDIxLVJTQS5jZXIwEQYDVR0gBAowCDAGBgRV
HSAAMB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATBCBgNVHR8EOzA5MDeg
NaAzhjFodHRwOi8vY3JsLmhhcmljYS5nci9IQVJJQ0EtVExTLVJvb3QtMjAyMS1S
U0EuY3JsMB0GA1UdDgQWBBSGAXI/jKlw4jEGUxbOAV9becg8OzAOBgNVHQ8BAf8E
BAMCAYYwDQYJKoZIhvcNAQELBQADggIBABkssjQzYrOo4GMsKegaChP16yNe6Sck
cWBymM455R2rMeuQ3zlxUNOEt+KUfgueOA2urp4j6TlPbs/XxpwuN3I1f09Luk5b
+ZgRXM7obE6ZLTerVQWKoTShyl34R2XlK8pEy7+67Ht4lcJzt+K6K5gEuoPSGQDP
ef+fUfmXrFcgBMcMbtfDb9dubFKNZZxo5nAXiqhFMOIyByag3H+tOTuH8zuId9pH
RDsUpAIHJ9/W2WBfLcKav7IKRlNBRD/sPBy903J9WHPKwl8kQSDA+aa7XCYk7bJt
Eyf+7GM9F5cZ7+YyknXqnv/rtQEkTKZdQo5Us18VFe9qqj94tXbLdk7PejJYNB4O
Zlli44Ld7rtqfFlUych7gIxFOmiyxMQQYrYmUi+74lEZvfoNhuref0CupuKpz6O3
dLv6kO9T10uNdDBoBQTkge3UzHafTIe3R2o3ujXKUGPwyc9m7/FETyKLUCwSU/5O
AVOeBCU8QtkKKjM8AmbpKpe3pHWcyq3R7B3LmIALkMPTydyDfxen65IDqREbVq8N
xjhkJThUz40JqOlN6uqKqeDISj/IoucYwsqW24AlO7ZzNmohQmMi8ep23H4hBSh0
GBTe2XvkuzaNf92syK8l2HzO+13GLCjzYLTPvXTO9UpK8DGyfGZOuamuwbAnbNpE
3RfjV9IaUQGJ
-----END CERTIFICATE-----
```

> Caso pretendas a conexão automática, acrescenta as seguintes duas linhas:
```bash
[Settings]
AutoConnect=true
```

{{<callout type="info">}}
O conteúdo abaixo de `[@pem@eduroam_ca_cert]` são os certificados root e intermediário em Base-64. À data de publicação deste artigo, os ficheiros `root_ca.cer` e `intermedio.cer` disponíveis em https://www.ua.pt/en/stic/page/24010 estão já neste formato. Caso o contéudo esteja no formato DER, este pode ser convertido em Base-64 através do seguinte comando:
```bash
openssl x509 -inform der -in certificado-em-DER.cer -outform pem -out certificado-em-base64.pem
```
{{</callout>}}

2 - Para conectar basta correr o seguinte comando:
```bash
sudo iwctl station wlan0 connect eduroam 8021x
```

Pronto, tem acesso à rede eduroam!

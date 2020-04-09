---
title: Włącz TLS z kontenerem wózka bocznego
description: Tworzenie punktu końcowego SSL lub TLS dla grupy kontenerów uruchomionych w wystąpieniach kontenera platformy Azure przez uruchomienie Nginx w kontenerze wózka bocznego
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: b9ea9367219db694b89d6bf4a1e52efb373c71c4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984610"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Włączanie punktu końcowego TLS w kontenerze wózka bocznego

W tym artykule pokazano, jak utworzyć [grupę kontenerów](container-instances-container-groups.md) z kontenerem aplikacji i kontenerem wózka bocznego z uruchomionym dostawcą protokołu TLS/SSL. Konfiguruj grupę kontenerów z oddzielnym punktem końcowym TLS, można włączyć połączenia TLS dla aplikacji bez zmiany kodu aplikacji.

Skonfiguruj przykładową grupę kontenerów składającą się z dwóch kontenerów:
* Kontener aplikacji, który uruchamia prostą aplikację sieci web przy użyciu publicznego obrazu [Microsoft aci-helloworld.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* Kontener wózka bocznego z publicznym obrazem [Nginx](https://hub.docker.com/_/nginx) skonfigurowany do używania protokołu TLS. 

W tym przykładzie grupa kontenerów udostępnia tylko port 443 dla Nginx z jego publicznym adresem IP. Nginx kieruje żądania HTTPS do towarzyszącej aplikacji sieci web, która nasłuchuje wewnętrznie na porcie 80. Można dostosować przykład dla aplikacji kontenera, które nasłuchują na innych portach. 

Zobacz [Następne kroki](#next-steps) dla innych metod włączania protokołu TLS w grupie kontenerów.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby ukończyć ten artykuł, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, zaleca się wersję 2.0.55 lub nowszą. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Aby skonfigurować Nginx jako dostawcę protokołu TLS, potrzebny jest certyfikat TLS/SSL. W tym artykule pokazano, jak utworzyć i skonfigurować certyfikat TLS/SSL z podpisem własnym. W przypadku scenariuszy produkcyjnych należy uzyskać certyfikat od urzędu certyfikacji.

Aby utworzyć certyfikat TLS/SSL z podpisem własnym, użyj narzędzia [OpenSSL](https://www.openssl.org/) dostępnego w usłudze Azure Cloud Shell i wielu dystrybucjach systemu Linux lub użyj porównywalnego narzędzia klienckiego w systemie operacyjnym.

Najpierw utwórz żądanie certyfikatu (plik csr) w lokalnym katalogu roboczym:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Postępuj zgodnie z instrukcjami, aby dodać informacje identyfikacyjne. W przypadku nazwy pospolitej wprowadź nazwę hosta skojarzoną z certyfikatem. Po wyświetleniu monitu o podanie hasła naciśnij klawisz Enter bez wpisywania, aby pominąć dodanie hasła.

Uruchom następujące polecenie, aby utworzyć certyfikat z podpisem własnym (plik crt) z żądania certyfikatu. Przykład:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

W katalogu powinny być teraz widoczne trzy pliki: żądanie certyfikatu (`ssl.csr`), klucz prywatny (`ssl.key`) i certyfikat z podpisem własnym (`ssl.crt`). Używasz `ssl.key` `ssl.crt` i w późniejszych krokach.

## <a name="configure-nginx-to-use-tls"></a>Konfigurowanie nginx do używania protokołu TLS

### <a name="create-nginx-configuration-file"></a>Tworzenie pliku konfiguracyjnego Nginx

W tej sekcji należy utworzyć plik konfiguracyjny dla Nginx do używania protokołu TLS. Zacznij od skopiowania następującego tekstu `nginx.conf`do nowego pliku o nazwie . W usłudze Azure Cloud Shell można użyć programu Visual Studio Code do utworzenia pliku w katalogu roboczym:

```console
code nginx.conf
```

W `location`, należy `proxy_pass` ustawić z odpowiednim portem dla aplikacji. W tym przykładzie ustawiliśmy port `aci-helloworld` 80 dla kontenera.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Wpisy tajne i plik konfiguracyjny kodowane przez Base64

Kodowanie bazy 64 pliku konfiguracyjnego Nginx, certyfikatu TLS/SSL i klucza TLS. W następnej sekcji należy wprowadzić zakodowaną zawartość w pliku YAML używanym do wdrażania grupy kontenerów.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Wdrażanie grupy kontenerów

Teraz wdrożyć grupę kontenerów, określając konfiguracje kontenera w [pliku YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Tworzenie pliku YAML

Skopiuj następujący YAML `deploy-aci.yaml`do nowego pliku o nazwie . W usłudze Azure Cloud Shell można użyć programu Visual Studio Code do utworzenia pliku w katalogu roboczym:

```console
code deploy-aci.yaml
```

Wprowadź zawartość plików zakodowanych w base64, `secret`jeżeli jest to wskazane w obszarze . Na przykład `cat` każdy z plików zakodowanych base64, aby wyświetlić jego zawartość. Podczas wdrażania te pliki są dodawane do [tajnego woluminu](container-instances-volume-secret.md) w grupie kontenerów. W tym przykładzie tajny wolumin jest zamontowany do kontenera Nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Wdrażanie grupy kontenerów

Utwórz grupę zasobów za pomocą polecenia [utwórz grupę az:](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Wdrażanie grupy kontenerów za pomocą polecenia [tworzenia kontenera az,](/cli/azure/container#az-container-create) przekazując plik YAML jako argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Wyświetlanie stanu wdrożenia

Aby wyświetlić stan wdrożenia, użyj następującego polecenia [az container show:](/cli/azure/container#az-container-show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

W przypadku pomyślnego wdrożenia dane wyjściowe są podobne do następujących czynności:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Weryfikowanie połączenia TLS

Użyj przeglądarki, aby przejść do publicznego adresu IP grupy kontenerów. Adres IP pokazany w `52.157.22.76`tym przykładzie **https://52.157.22.76**to adres URL , więc adres URL jest . Aby wyświetlić uruchomiono aplikację, należy użyć protokołu HTTPS ze względu na konfigurację serwera Nginx. Próby połączenia za pośrednictwem protokołu HTTP nie powiodły się.

![Zrzut ekranu przedstawiający aplikację uruchomioną w wystąpieniu kontenera platformy Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Ponieważ w tym przykładzie użyto certyfikatu z podpisem własnym, a nie certyfikatu z urzędu certyfikacji, przeglądarka wyświetla ostrzeżenie o zabezpieczeniach podczas łączenia się z witryną za pośrednictwem protokołu HTTPS. Aby przejść do strony, może być konieczne zaakceptowanie ostrzeżenia lub dostosowanie ustawień przeglądarki lub certyfikatu. Takie zachowanie jest oczekiwane.

>

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak skonfigurować kontener Nginx, aby włączyć połączenia TLS z aplikacją sieci web działającą w grupie kontenerów. W tym przykładzie można dostosować aplikacje, które nasłuchują na portach innych niż port 80. Można również zaktualizować plik konfiguracyjny Nginx, aby automatycznie przekierowywać połączenia serwera na porcie 80 (HTTP) w celu użycia protokołu HTTPS.

W tym artykule używa się Nginx w wózku bocznym, można użyć innego dostawcy TLS, takiego jak [Caddy](https://caddyserver.com/).

Jeśli wdrożysz grupę kontenerów w [sieci wirtualnej platformy Azure,](container-instances-vnet.md)można rozważyć inne opcje, aby włączyć punkt końcowy TLS dla wystąpienia kontenera wewnętrznej bazy danych, w tym:

* [Serwery proxy funkcji platformy Azure](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Brama aplikacji platformy Azure](../application-gateway/overview.md) — zobacz przykładowy [szablon wdrożenia](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).

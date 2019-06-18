---
title: Włącz protokół SSL w usłudze Azure Container Instances
description: Tworzenie punktu końcowego protokołu SSL lub TLS dla grupy kontenerów, działające w usłudze Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 12de4ef31084d8ac8586c79ffe3d0a8e891727bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411396"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Włączanie punktu końcowego protokołu SSL w grupie kontenerów

W tym artykule przedstawiono sposób tworzenia [grupy kontenerów](container-instances-container-groups.md) z kontenerem aplikacji i kontenerem przyczepki uruchomionym dostawcą protokołu SSL. Konfigurując grupy kontenerów, za pomocą oddzielnego punktu końcowego protokołu SSL, można włączyć połączenia SSL w aplikacji bez konieczności zmieniania kodu aplikacji.

Możesz skonfigurować grupę kontenerów składające się z dwóch kontenerów:
* Kontener aplikacji, który uruchamia prostą aplikację internetową przy użyciu publicznych firmy Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) obrazu. 
* Kontenerem przyczepki systemem publicznie [Nginx](https://hub.docker.com/_/nginx) obrazu, skonfigurowany do używania protokołu SSL. 

W tym przykładzie Grupa kontenerów przedstawia tylko port 443 dla kontenera Nginx za pomocą publicznego adresu IP. Serwer Nginx kieruje żądania HTTPS do aplikacji sieci web pomocnika, która wewnętrznie nasłuchuje na porcie 80. Można dostosować przykład aplikacji kontenera, które nasłuchują na inne porty.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure służy do ukończenia tego artykułu. Jeśli chcesz używać go lokalnie, wersja 2.0.55 lub nowszy, jest zalecane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Aby skonfigurować serwer Nginx jako dostawca protokołu SSL, wymagany jest certyfikat SSL. W tym artykule przedstawiono sposób tworzenia i konfigurowania certyfikatu SSL z podpisem własnym. Na potrzeby scenariuszy produkcyjnych należy uzyskać certyfikat z urzędu certyfikacji.

Aby utworzyć certyfikat SSL z podpisem własnym, użyj [OpenSSL](https://www.openssl.org/) narzędzia dostępne w usłudze Azure Cloud Shell i wielu dystrybucji systemu Linux, lub użyj narzędzia porównywalne klienta w systemie operacyjnym.

Najpierw utwórz żądanie certyfikatu (plik CSR) w lokalny katalog roboczy:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Postępuj zgodnie z monitami, aby dodać informacje identyfikacyjne. Nazwa pospolita wprowadź nazwę hosta skojarzony z certyfikatem. Po wyświetleniu monitu o podanie hasła, naciśnij klawisz Enter, aby bez wpisywania, aby pominąć, dodając hasło.

Uruchom następujące polecenie, aby utworzyć certyfikat z podpisem własnym (plik .crt) z żądania certyfikatu. Na przykład:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Zostaną wyświetlone trzy pliki w katalogu: żądanie certyfikatu (`ssl.csr`), klucza prywatnego (`ssl.key`), a certyfikat z podpisem własnym (`ssl.crt`). Możesz użyć `ssl.key` i `ssl.crt` w kolejnych krokach.

## <a name="configure-nginx-to-use-ssl"></a>Konfigurowanie serwera Nginx, aby używały protokołu SSL

### <a name="create-nginx-configuration-file"></a>Utwórz plik konfiguracji Nginx

W tej sekcji utworzysz plik konfiguracji dla kontenera Nginx do używania protokołu SSL. Zacznij od skopiowania poniższy tekst do nowego pliku o nazwie`nginx.conf`. W usłudze Azure Cloud Shell można użyć programu Visual Studio Code, aby utworzyć plik w katalogu roboczym:

```console
code nginx.conf
```

W `location`, należy ustawić `proxy_pass` za pomocą poprawnego portu dla aplikacji. W tym przykładzie ustawimy port 80 dla `aci-helloworld` kontenera.

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
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
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

### <a name="base64-encode-secrets-and-configuration-file"></a>Kodowanie Base64 wpisów tajnych i pliku konfiguracji

Base64 — kodowanie plik konfiguracji Nginx, certyfikat SSL i klucza protokołu SSL. W następnej sekcji możesz wprowadzić zakodowanej treści w pliku YAML, używane do wdrażania grupy kontenerów.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Wdrażanie grupy kontenerów

Teraz wdrożyć grupę kontenerów, określając kontenera konfiguracji w [pliku YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Utwórz plik YAML

Skopiuj poniższego kodu YAML do nowego pliku o nazwie `deploy-aci.yaml`. W usłudze Azure Cloud Shell można użyć programu Visual Studio Code, aby utworzyć plik w katalogu roboczym:

```console
code deploy-aci.yaml
```

Wprowadź zawartość algorytmem base64 plików w przypadku, gdy wskazane w `secret`. Na przykład `cat` każdego z plików algorytmem Base64, aby wyświetlić jego zawartość. Podczas wdrażania, te pliki zostaną dodane do [wolumin tajny](container-instances-volume-secret.md) w grupie kontenerów. W tym przykładzie wolumin tajny jest zainstalowany do kontenera Nginx.

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

Utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create) polecenia:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż grupę kontenerów z [utworzyć kontener az](/cli/azure/container#az-container-create) polecenia, przekazanie pliku YAML jako argumentu.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Wyświetl stan wdrożenia

Aby wyświetlić stan wdrożenia, należy użyć następującego [az container show](/cli/azure/container#az-container-show) polecenia:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Do pomyślnego wdrożenia dane wyjściowe będą podobne do następujących:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Weryfikowanie połączenia SSL

Aby wyświetlić uruchomioną aplikację, przejdź do adresu IP w przeglądarce. Na przykład w poniższym przykładzie adres IP jest `52.157.22.76`. Należy użyć `https://<IP-ADDRESS>` Aby wyświetlić działającą aplikację, ze względu na konfigurację serwera Nginx. Próbuje nawiązać połączenie z `http://<IP-ADDRESS>` się nie powieść.

![Zrzut ekranu przedstawiający aplikację uruchomioną w wystąpieniu kontenera platformy Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Ponieważ w tym przykładzie użyto certyfikatu z podpisem własnym, a nie jeden z urzędu certyfikacji, w przeglądarce pojawi się ostrzeżenie o zabezpieczeniach podczas nawiązywania połączenia z lokacją przy użyciu protokołu HTTPS. To zachowanie jest oczekiwane.
>

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano, jak skonfigurować kontenera Nginx, aby włączyć połączenia SSL z aplikacji internetowej uruchomionej w grupie kontenerów. Można dostosować ten przykład w przypadku aplikacji, które nasłuchują na porty inne niż port 80. Możesz także zaktualizować plik konfiguracji Nginx, automatyczne przekierowywanie połączeń na porcie 80 (HTTP), aby używać protokołu HTTPS z serwerem.

W tym artykule używa serwera Nginx w przyczepka, ale można używać innego dostawcy SSL takich jak [Caddy](https://caddyserver.com/).

Innym podejściem do włączania protokołu SSL w grupie kontenerów jest wdrożenie grupy w [sieci wirtualnej platformy Azure](container-instances-vnet.md) z [bramy aplikacji platformy Azure](../application-gateway/overview.md). Bramy można skonfigurować jako punktu końcowego protokołu SSL. Zobacz przykład [Szablon wdrożenia](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) można dostosować do kończenie żądań protokołu SSL na bramie.

---
title: Obsługa protokołu SSH w usłudze App Service w systemie Linux — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze Azure App Service w systemie Linux przy użyciu protokołu SSH.
keywords: Aplikacja sieci web, linux, oss w usłudze Azure app service
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.custom: seodec18
ms.openlocfilehash: 9a05769b4cfd4bcaca0df9e1af1816d99f78bc62
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984478"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) jest najczęściej używany do wykonywania poleceń administracyjnych zdalnie z poziomu terminalu wiersza polecenia. Usługi App Service w systemie Linux obsługuje protokół SSH w kontenerze aplikacji z każdym wbudowane obrazy platformy Docker, używany do stosu środowiska uruchomieniowego, nowych aplikacji sieci web. 

![Stosy środowiska uruchomieniowego](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Dla niestandardowych obrazów platformy Docker, konfigurując serwer SSH w obrazie niestandardowym.

Możesz również nawiązać kontenera bezpośrednio z lokalnej maszynie do programowania za pomocą protokołu SSH i SFTP.

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

Aby połączenia klienta SSH za pomocą usługi kontenera, aplikacja powinna być uruchomiona.

Wklej następujący adres URL do przeglądarki i Zastąp \<nazwa_aplikacji > nazwą swojej aplikacji:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Jeśli nie są już uwierzytelniony, są wymagane do uwierzytelniania przy użyciu subskrypcji platformy Azure, aby połączyć. Po uwierzytelnieniu zobaczysz shell w przeglądarce, gdzie możesz uruchamiać polecenia wewnątrz kontenera.

![Połączenie SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Obsługa protokołu SSH za pomocą niestandardowych obrazów platformy Docker

Aby niestandardowy obraz platformy Docker na potrzeby obsługi komunikacji SSH między kontenerem a klienta w witrynie Azure portal wykonaj następujące kroki dla obrazu platformy Docker.

Te kroki są wyświetlane w repozytorium usługi Azure App Service jako [przykład](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Obejmują `openssh-server` instalacji w [ `RUN` instrukcji](https://docs.docker.com/engine/reference/builder/#run) w pliku Dockerfile obrazu i ustaw hasło do głównego konta do `"Docker!"`.

    > [!NOTE]
    > Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH może zostać oceniony jedynie za pomocą aparatu Kudu / SCM lokacji, który jest uwierzytelniany przy użyciu poświadczeń publikowania.

    ```Dockerfile
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

2. Dodaj [ `COPY` instrukcji](https://docs.docker.com/engine/reference/builder/#copy) do pliku Dockerfile, aby skopiować [sshd_config](https://man.openbsd.org/sshd_config) plik */etc/ssh/* katalogu. Plik konfiguracji powinien być oparty na pliku sshd_config w repozytorium GitHub Azure aplikacji usługi [tutaj](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > *Sshd_config* plik musi zawierać następujące lub połączenie nie powiedzie się: 
    > * `Ciphers` musi zawierać co najmniej jedną z następujących: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` musi zawierać co najmniej jedną z następujących: `hmac-sha1,hmac-sha1-96`.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

3. Obejmują port 2222 w [ `EXPOSE` instrukcji](https://docs.docker.com/engine/reference/builder/#expose) dla pliku Dockerfile. Mimo iż hasło konta root jest znane, nie można uzyskać dostępu do portu 2222 z Internetu. Jest to tylko port wewnętrzny dostępny tylko przez kontenerów w sieci mostkowanej prywatnej sieci wirtualnej.

    ```Dockerfile
    EXPOSE 2222 80
    ```

4. Upewnij się uruchomić usługę SSH za pomocą skryptu powłoki (Zobacz przykład w [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Plik Dockerfile używa [ `ENTRYPOINT` instrukcji](https://docs.docker.com/engine/reference/builder/#entrypoint) do uruchomienia skryptu.

    ```Dockerfile
    COPY init_container.sh /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Otwórz sesję SSH z powłoki zdalnej

> [!NOTE]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej.
>

Za pomocą protokołu TCP tunelowania, możesz utworzyć połączenie sieciowe między komputerem deweloperskim i Web App for Containers za pośrednictwem uwierzytelnionego połączenia protokołu WebSocket. Umożliwia ona Otwórz sesję SSH z kontenera uruchamianie w usłudze App Service z wybranego klienta.

Aby rozpocząć pracę, musisz zainstalować [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Aby zobaczyć, jak to działa bez konieczności instalowania interfejsu wiersza polecenia platformy Azure, otwórz [usługi Azure Cloud Shell](../../cloud-shell/overview.md). 

Dodaj najnowsze rozszerzenie usługi App Service, uruchamiając [Dodaj rozszerzenie az](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add --name webapp
```

Jeśli po uruchomieniu już `az extension add` , uruchom [aktualizacji rozszerzenia az](/cli/azure/extension?view=azure-cli-latest#az-extension-update) zamiast tego:

```azurecli-interactive
az extension update --name webapp
```

Otwórz połączenie zdalne z usługą aplikacji przy użyciu [Utwórz połączenie zdalne az webapp](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) polecenia. Określ  _\<subskrypcji\_identyfikator >_,  _\<grupy\_name >_ i \_< aplikacji\_nazwa > _ dla aplikacji.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> &
```

> [!TIP]
> `&` na końcu polecenia jest tylko do zapewnienia wygody, jeśli używasz usługi Cloud Shell. Działa proces w tle, dzięki czemu możesz uruchomić poniższe polecenie w ta sama powłoka.

Dane wyjściowe polecenia zapewnia informacje, które należy otworzyć sesję SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Otwórz sesję SSH z kontenera za pomocą klienta, co pozwala przy użyciu portu lokalnego. W poniższym przykładzie użyto domyślnie [ssh](https://ss64.com/bash/ssh.html) polecenia:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Po wyświetleniu monitu wpisz `yes` Aby nadal łączyć się. Następnie zostanie wyświetlony monit o hasło. Użyj `Docker!`, wcześniej, który został wyświetlony użytkownikowi.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Po użytkownik jest uwierzytelniony, powinien zostać wyświetlony ekran powitalny sesji.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Masz teraz połączenie Twojego łącznika. 

Spróbuj uruchomić [górnej](https://ss64.com/bash/top.html) polecenia. Można wyświetlić procesu aplikacji na liście procesu. W przykładzie danych wyjściowych poniżej, jest jednym z `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Kolejne kroki

Możesz zadać pytania i uwagi na [forum usługi Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Aby uzyskać więcej informacji na temat Web App for Containers zobacz:

* [Wprowadzenie do zdalnego debugowania aplikacji Node.js w usłudze Azure App Service z programu VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [How to use a custom Docker image for Web App for Containers](quickstart-docker-go.md) (Używanie niestandardowego obrazu platformy Docker dla usługi Web App for Containers)
* [Using .NET Core in Azure App Service on Linux](quickstart-dotnetcore.md) (Korzystanie z platformy .NET Core w usłudze Azure App Service w systemie Linux)
* [Using Ruby in Azure App Service on Linux](quickstart-ruby.md) (Używanie języka Ruby w usłudze Azure App Service w systemie Linux)
* [Azure App Service Web App for Containers FAQ](app-service-linux-faq.md) (Usługa Web App for Containers w usłudze Azure App Service — często zadawane pytania)

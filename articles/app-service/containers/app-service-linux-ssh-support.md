---
title: Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux | Dokumentacja firmy Microsoft
description: Więcej informacji o korzystaniu z protokołu SSH z usługi Azure App Service w systemie Linux.
keywords: Usługa aplikacji Azure, aplikacji sieci web, linux, oss
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
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) jest najczęściej używany do wykonywania poleceń administracyjnych zdalnie z wiersza polecenia terminalu. Usługi aplikacji w systemie Linux zapewnia obsługę protokołu SSH w kontenerze aplikacji z każdym z wbudowanych Docker obrazami używanymi na potrzeby stosu środowiska uruchomieniowego nowych aplikacji sieci web. 

![Stosy środowiska wykonawczego](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Dla niestandardowe obrazy usługi Docker, konfigurując serwer SSH w niestandardowego obrazu.

Można również nawiązać kontenera bezpośrednio z komputera lokalnego Programowanie przy użyciu protokołu SSH i SFTP.

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH przeglądarki

Aby utworzyć połączenie klienta SSH z Twojej kontenera, aplikacja powinna być uruchomiona.

Wklej następujący adres URL do przeglądarki i Zastąp \<nazwa_aplikacji > nazwą Twojej aplikacji:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Jeśli użytkownik nie jest już uwierzytelniony, są wymagane do uwierzytelniania z subskrypcją platformy Azure, aby połączyć. Po uwierzytelnieniu widzisz powłoki w przeglądarce, gdzie możesz uruchamiać polecenia wewnątrz z kontenera.

![Połączenie SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Obsługa protokołu SSH za pomocą niestandardowe obrazy usługi Docker

Aby Docker obrazu niestandardowego do obsługi komunikacji SSH między kontenerem i klienta w portalu Azure wykonaj następujące kroki dla obrazu Docker.

Te kroki są wyświetlane w repozytorium Azure App Service jako [przykład](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Obejmują `openssh-server` instalacji w [ `RUN` instrukcji](https://docs.docker.com/engine/reference/builder/#run) w plik Dockerfile obrazu i ustaw hasło dla głównego konta `"Docker!"`.

    > [!NOTE]
    > Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. SSH jest możliwy tylko za pośrednictwem Kudu / SCM lokacji, który jest uwierzytelniany przy użyciu poświadczeń publikowania.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Dodaj [ `COPY` instrukcji](https://docs.docker.com/engine/reference/builder/#copy) Aby skopiować plik Dockerfile [sshd_config](http://man.openbsd.org/sshd_config) pliku */itp/ssh/* katalogu. Plik konfiguracji powinny być oparte na pliku sshd_config w repozytorium GitHub usługi App [tutaj](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > *Sshd_config* plik musi zawierać następujące lub połączenie nie powiedzie się: 
    > * `Ciphers` musi zawierać co najmniej jedną z następujących: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` musi zawierać co najmniej jedną z następujących: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Obejmują portu 2222 [ `EXPOSE` instrukcji](https://docs.docker.com/engine/reference/builder/#expose) dla plik Dockerfile. Mimo iż hasło konta root jest znane, nie można uzyskać dostępu do portu 2222 z Internetu. Jest wewnętrzny tylko port dostępny tylko przez kontenery w ramach sieci Mostek wirtualnej sieci prywatnej.

    ```docker
    EXPOSE 2222 80
    ```

1. Upewnij się, że można uruchomić usługi SSH za pomocą skryptu powłoki (Zobacz przykład w [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Plik Dockerfile używa [ `ENTRYPOINT` instrukcji](https://docs.docker.com/engine/reference/builder/#entrypoint) do uruchomienia skryptu.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Otwórz sesję SSH z powłoki zdalnej

> [!NOTE]
> Ta funkcja jest obecnie w przeglądzie.
>

Za pomocą protokołu TCP tunelowania, możesz utworzyć połączenie sieciowe między komputerze deweloperskim i aplikacji sieci Web dla kontenerów za pośrednictwem uwierzytelnionego połączenia obiektu WebSocket. Pozwala otworzyć sesję SSH z Twojej kontenera działające w usłudze aplikacji z dowolnego klienta.

Aby rozpocząć pracę, musisz zainstalować [interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Aby zobaczyć, jak działa bez konieczności instalowania interfejsu wiersza polecenia Azure, otwórz [powłoki chmury Azure](../../cloud-shell/overview.md). 

Dodaj najnowsze rozszerzenia usługi aplikacji przez uruchomienie [dodać rozszerzenie az](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add -–name webapp
```

Jeśli już uruchomione `az extension add` przed, uruchom [aktualizacji rozszerzenia az](/cli/azure/extension?view=azure-cli-latest#az-extension-update) zamiast tego:

```azurecli-interactive
az extension update -–name webapp
```

Otwórz połączenia zdalnego przy użyciu aplikacji [Tworzenie połączenia zdalnego aplikacji sieci Web az](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) polecenia. Określ  _\<grupy\_nazwa >_ i \_< aplikacji\_name > _ aplikacji i Zastąp \<port > z numerem portu lokalnego.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> `&` na końcu polecenia jest tylko do zapewnienia wygody korzystania z powłoki chmury. Uruchamia go proces w tle, co umożliwia uruchamianie następne polecenie w powłoce tego samego.

Dane wyjściowe polecenia zapewnia informacje, które należy otworzyć sesję SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Otwórz sesję SSH z Twojej kontener z klienta wybranych przez użytkownika, przy użyciu portu lokalnego. W poniższym przykładzie użyto domyślnie [ssh](https://ss64.com/bash/ssh.html) polecenia:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Po wyświetleniu monitu wpisz `yes` aby kontynuować łączenie. Następnie zostanie wyświetlony monit o hasło. Użyj `Docker!`, która została przedstawiona do Ciebie.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Gdy użytkownik jest uwierzytelniony, powinny zostać wyświetlone na ekranie powitalnym sesji.

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

Masz teraz połączenie z łącznikiem. 

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

Pytania i uwagi można umieścić na [Azure forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Aby uzyskać więcej informacji dotyczących aplikacji sieci Web dla kontenerów zobacz:

* [Wprowadzenie do zdalnego debugowania aplikacji Node.js w usłudze Azure App Service w kodzie VS](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [How to use a custom Docker image for Web App for Containers](quickstart-docker-go.md) (Używanie niestandardowego obrazu platformy Docker dla usługi Web App for Containers)
* [Using .NET Core in Azure App Service on Linux](quickstart-dotnetcore.md) (Korzystanie z platformy .NET Core w usłudze Azure App Service w systemie Linux)
* [Using Ruby in Azure App Service on Linux](quickstart-ruby.md) (Używanie języka Ruby w usłudze Azure App Service w systemie Linux)
* [Azure App Service Web App for Containers FAQ](app-service-linux-faq.md) (Usługa Web App for Containers w usłudze Azure App Service — często zadawane pytania)
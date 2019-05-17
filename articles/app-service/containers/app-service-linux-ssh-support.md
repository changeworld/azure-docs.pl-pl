---
title: Obsługa protokołu SSH w usłudze App Service w systemie Linux — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze Azure App Service w systemie Linux przy użyciu protokołu SSH.
keywords: Aplikacja sieci web, linux, oss w usłudze Azure app service
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 18d10afc9132c81c2dcfbb1aa17ded81a21336ca
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780046"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) jest najczęściej używany do wykonywania poleceń administracyjnych zdalnie z poziomu terminalu wiersza polecenia. Usługi App Service w systemie Linux obsługuje protokół SSH w kontenerze aplikacji z każdym wbudowane obrazy platformy Docker, używany do stosu środowiska uruchomieniowego, nowych aplikacji sieci web. 

![Stosy środowiska uruchomieniowego](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Dla niestandardowych obrazów platformy Docker, konfigurując serwer SSH w obrazie niestandardowym.

Możesz również nawiązać kontenera bezpośrednio z lokalnej maszynie do programowania za pomocą protokołu SSH i SFTP.

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Obsługa protokołu SSH za pomocą niestandardowych obrazów platformy Docker

Zobacz [Konfigurowanie protokołu SSH w kontenerze niestandardowe](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Otwórz sesję SSH z powłoki zdalnej

> [!NOTE]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej.
>

Za pomocą protokołu TCP tunelowania, możesz utworzyć połączenie sieciowe między komputerem deweloperskim i Web App for Containers za pośrednictwem uwierzytelnionego połączenia protokołu WebSocket. Umożliwia ona Otwórz sesję SSH z kontenera uruchamianie w usłudze App Service z wybranego klienta.

Aby rozpocząć pracę, musisz zainstalować [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Aby zobaczyć, jak to działa bez konieczności instalowania interfejsu wiersza polecenia platformy Azure, otwórz [usługi Azure Cloud Shell](../../cloud-shell/overview.md). 

Otwórz połączenie zdalne z usługą aplikacji przy użyciu [Utwórz połączenie zdalne az webapp](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) polecenia. Określ  _\<identyfikator subskrypcji >_,  _\<nazwa_grupy >_ i \__ < nazwa aplikacji > dla aplikacji.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
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

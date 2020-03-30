---
title: Dostęp SSH dla kontenerów Linuksa
description: Sesję SSH można otworzyć w kontenerze systemu Linux w usłudze Azure App Service. Kontenery systemu Linux niestandardowe są obsługiwane z pewnymi modyfikacjami obrazu niestandardowego.
keywords: azure app service, aplikacja internetowa, Linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: dab13f222b441c7415a8d09d0d91ab3af5aaf836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280185"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH support for Azure App Service on Linux (Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux)

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) jest często używany do wykonywania poleceń administracyjnych zdalnie z terminala wiersza polecenia. Usługa app service w systemie Linux zapewnia obsługę SSH w kontenerze aplikacji. 

![Usługa aplikacji systemu Linux SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh.png)

Można również połączyć się z kontenerem bezpośrednio z lokalnej maszyny deweloperskiej przy użyciu SSH i SFTP.

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Korzystanie z obsługi SSH z niestandardowymi obrazami platformy Docker

Zobacz [Konfigurowanie SSH w kontenerze niestandardowym](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Otwórz sesję SSH ze zdalnej powłoki

> [!NOTE]
> Ta funkcja jest obecnie w wersji zapoznawczej.
>

Za pomocą tunelowania TCP można utworzyć połączenie sieciowe między komputerem deweloperskim a aplikacją Web App dla kontenerów za pośrednictwem uwierzytelnionego połączenia WebSocket. Umożliwia otwarcie sesji SSH z kontenerem uruchomionym w usłudze App Service od wybranego klienta.

Aby rozpocząć, musisz zainstalować [platformę Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Aby zobaczyć, jak to działa bez instalowania interfejsu wiersza polecenia platformy [Azure,](../../cloud-shell/overview.md)otwórz usługę Azure Cloud Shell . 

Otwórz połączenie zdalne z aplikacją za pomocą polecenia [tworzenia połączenia zdalnego az webapp.](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) \_ \<Określ _ \<>identyfikatora subskrypcji, _ _ \<>nazw grupy_ i>_ nazw aplikacji dla aplikacji.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&`na końcu polecenia jest tylko dla wygody, jeśli używasz Cloud Shell. Uruchamia proces w tle, dzięki czemu można uruchomić następne polecenie w tej samej powłoce.

Dane wyjściowe polecenia zawiera informacje potrzebne do otwarcia sesji SSH.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Otwórz sesję SSH z kontenerem z wybranym klientem przy użyciu portu lokalnego. W poniższym przykładzie użyto domyślnego polecenia [ssh:](https://ss64.com/bash/ssh.html)

```bash
ssh root@127.0.0.1 -p <port>
```

Po wyświetleniu monitu wpisz, `yes` aby kontynuować łączenie. Następnie zostanie wyświetlony monit o podanie hasła. Użyj `Docker!`, który został pokazany wcześniej.

```output
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Po uwierzytelnieniu powinien zostać wyświetlony ekran powitalny sesji.

```output
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Teraz masz połączenie ze złączem.  

Spróbuj uruchomić polecenie [top.](https://ss64.com/bash/top.html) Proces aplikacji powinien być widoczny na liście procesów. W poniższym przykładzie danych wyjściowych `PID 263`jest to ten z .

```output
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

## <a name="next-steps"></a>Następne kroki

Pytania i wątpliwości można publikować na [forum platformy Azure](https://docs.microsoft.com/answers/topics/azure-webapps.html).

Aby uzyskać więcej informacji na temat aplikacji sieci Web dla kontenerów, zobacz:

* [Wprowadzenie zdalnego debugowania aplikacji Node.js w usłudze Azure App Service z programu VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [How to use a custom Docker image for Web App for Containers](quickstart-docker-go.md) (Używanie niestandardowego obrazu platformy Docker dla usługi Web App for Containers)
* [Using .NET Core in Azure App Service on Linux](quickstart-dotnetcore.md) (Korzystanie z platformy .NET Core w usłudze Azure App Service w systemie Linux)
* [Using Ruby in Azure App Service on Linux](quickstart-ruby.md) (Używanie języka Ruby w usłudze Azure App Service w systemie Linux)
* [Azure App Service Web App for Containers FAQ](app-service-linux-faq.md) (Usługa Web App for Containers w usłudze Azure App Service — często zadawane pytania)

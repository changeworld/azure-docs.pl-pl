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
ms.openlocfilehash: cf27e852f5ec9b7e12b0c678e9940596bc57b385
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Obsługa protokołu SSH dla usługi Azure App Service w systemie Linux

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kryptograficznych protokół sieci bezpieczny sposób za pomocą usług sieciowych. Najczęściej służy do logowania do systemu zdalnego bezpiecznie z wiersza polecenia i wykonywania poleceń administracyjnych zdalnie.

Usługi aplikacji w systemie Linux zapewnia obsługę protokołu SSH w kontenerze aplikacji z każdym z wbudowanych Docker obrazami używanymi na potrzeby stosu środowiska uruchomieniowego nowych aplikacji sieci web.

![Stosy środowiska wykonawczego](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Za pomocą protokołu SSH i niestandardowe obrazy usługi Docker przy tym serwer SSH jako część obrazu i jego konfigurowania, zgodnie z opisem w tym artykule.

> [!NOTE] 
> Można również nawiązać kontenera aplikacji bezpośrednio z komputera lokalnego Programowanie przy użyciu protokołu SSH, SFTP lub Visual Studio Code (dla aplikacji Node.js debugowania na żywo). Aby uzyskać więcej informacji, zobacz [zdalnego debugowania i SSH w usłudze App Service w systemie Linux](https://aka.ms/linux-debug).
>

## <a name="making-a-client-connection"></a>Połączenia klienta

Aby utworzyć połączenie klienta SSH, lokacji głównej musi być uruchomiona.

Wklej punkt końcowy zarządzania kontroli źródła (SCM) dla aplikacji sieci web w przeglądarce, za pomocą następującej postaci:

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

Jeśli użytkownik nie jest już uwierzytelniony, są wymagane do uwierzytelniania z subskrypcją platformy Azure, aby połączyć.

![Połączenie SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>Obsługa protokołu SSH z niestandardowymi obrazami Docker

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

## <a name="next-steps"></a>Kolejne kroki

Pytania i uwagi można umieścić na [Azure forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Aby uzyskać więcej informacji dotyczących aplikacji sieci Web dla kontenerów zobacz:

* [How to use a custom Docker image for Web App for Containers](quickstart-docker-go.md) (Używanie niestandardowego obrazu platformy Docker dla usługi Web App for Containers)
* [Using .NET Core in Azure App Service on Linux](quickstart-dotnetcore.md) (Korzystanie z platformy .NET Core w usłudze Azure App Service w systemie Linux)
* [Using Ruby in Azure App Service on Linux](quickstart-ruby.md) (Używanie języka Ruby w usłudze Azure App Service w systemie Linux)
* [Azure App Service Web App for Containers FAQ](app-service-linux-faq.md) (Usługa Web App for Containers w usłudze Azure App Service — często zadawane pytania)
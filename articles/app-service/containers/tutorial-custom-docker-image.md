---
title: Używanie niestandardowego obrazu platformy Docker dla usługi Web App for Containers — Azure App Service | Microsoft Docs
description: Jak używać niestandardowego obrazu platformy Docker dla usługi Web App for Containers.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 98e690ab73b9a51126f4eae9ac5eff410e211957
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435846"
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Używanie niestandardowego obrazu platformy Docker dla usługi Web App for Containers

Usługa [Web App for Containers](app-service-linux-intro.md) zapewnia wbudowane obrazy platformy Docker w systemie Linux z obsługą określonych wersji, takich jak środowisko PHP 7.0 i Node.js 4.5. Przy użyciu technologii kontenerów platformy Docker usługa Web App for Containers hostuje zarówno wbudowane, jak i niestandardowe obrazy w modelu platforma jako usługa. W tym samouczku dowiesz się, jak utworzyć niestandardowy obraz platformy Docker i wdrożyć go w usłudze Web App for Containers. Ten wzorzec jest przydatny, gdy wbudowane obrazy nie uwzględniają wybranego przez Ciebie języka lub gdy aplikacja wymaga określonej konfiguracji, której wbudowane obrazy nie obejmują.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie obrazu niestandardowego platformy Docker na platformie Azure
> * Konfigurowanie zmiennych środowiskowych w celu uruchomienia kontenera
> * Aktualizowanie obrazu platformy Docker i jego ponowne wdrażanie
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH
> * Wdrażanie prywatnego obrazu platformy Docker na platformie Azure

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Usługa Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* [Konto usługi Docker Hub](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminalu uruchom następujące polecenie, aby sklonować repozytorium przykładowej aplikacji na maszynę lokalną, a następnie przejść do katalogu zawierającego przykładowy kod.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Tworzenie obrazu na podstawie pliku platformy Docker

W repozytorium Git zapoznaj się z zawartością pliku _Dockerfile_. W tym pliku opisano środowisko Python wymagane do uruchomienia aplikacji. Ponadto obraz skonfiguruje serwer [SSH](https://www.ssh.com/ssh/protocol/) na potrzeby bezpiecznej komunikacji między kontenerem a hostem.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Aby utworzyć obraz platformy Docker, uruchom polecenie `docker build` i podaj nazwę _mydockerimage_ oraz tag _v1.0.0_. Zastąp ciąg _\<docker-id>_ identyfikatorem konta usługi Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Polecenie spowoduje wygenerowanie danych wyjściowych podobnych do następujących:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Sprawdź, czy kompilacja działa, uruchamiając kontener platformy Docker. Uruchom polecenie [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) i przekaż do niego nazwę oraz tag obrazu. Pamiętaj o określeniu portu przy użyciu argumentu `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Upewnij się, że aplikacja internetowa i kontener działają prawidłowo, przechodząc do adresu `http://localhost:2222`.

![Lokalne testowanie aplikacji internetowych](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> Możesz również nawiązać połączenie z kontenerem aplikacji bezpośrednio z lokalnego komputera deweloperskiego przy użyciu protokołu SSH, SFTP lub programu Visual Studio Code (na potrzeby debugowania na żywo aplikacji Node.js). Aby uzyskać więcej informacji, zobacz [Zdalne debugowanie i protokół SSH w usłudze App Service w systemie Linux](https://aka.ms/linux-debug).
>

## <a name="push-the-docker-image-to-docker-hub"></a>Wypychanie obrazu platformy Docker do usługi Docker Hub

Rejestr to aplikacja hostująca obrazy i udostępniająca usługi obrazów oraz kontenerów. Aby udostępnić obraz, należy wypchnąć go do rejestru. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Wykonujesz wypchnięcie do prywatnego rejestru platformy Docker? Zobacz opcjonalne instrukcje dotyczące [używania obrazu platformy Docker z dowolnego rejestru prywatnego](#use-a-docker-image-from-any-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Usługa Docker Hub jest rejestrem obrazów platformy Docker, który umożliwia hostowanie własnych repozytoriów — publicznych lub prywatnych. Aby wypchnąć niestandardowy obraz platformy Docker do publicznej usługi Docker Hub, użyj polecenia [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) i podaj pełną nazwę obrazu oraz jego tag. Pełna nazwa obrazu i jego tag wyglądają jak w następującym przykładzie:

```
<docker-id>/image-name:tag
```

Przed wypchnięciem obrazu należy najpierw zalogować się w usłudze Docker Hub przy użyciu polecenia [`docker login`](https://docs.docker.com/engine/reference/commandline/login/). Zastąp ciąg _\<docker-id>_ nazwą swojego konta i wpisz swoje hasło w wierszu polecenia konsoli.

```bash
docker login --username <docker-id>
```

Pomyślne logowanie zostanie potwierdzone stosownym komunikatem. Po zalogowaniu możesz wypchnąć obraz do usługi Docker Hub przy użyciu polecenia [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Sprawdź, czy wypchnięcie się powiodło, badając dane wyjściowe polecenia.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Aby utworzyć aplikację, która używa obrazu, które zostały wypchnięte, uruchamiasz polecenia interfejsu wiersza polecenia platformy Azure, które tworzyć grupy, a następnie planu usług, a na końcu samą aplikację internetową. 

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi App Service dla systemu Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W usłudze Cloud Shell utwórz [aplikację internetową](app-service-linux-intro.md) w planie usługi App Service `myAppServicePlan` za pomocą polecenia [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Pamiętaj o zastąpieniu ciągu _<appname>_ unikatową nazwą aplikacji i ciągu _\<docker-ID>_ identyfikatorem platformy Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Większość obrazów platformy Docker ma zmienne środowiskowe, które trzeba skonfigurować. Jeśli używasz istniejącego obrazu platformy Docker utworzonego przez inną osobę, ten obraz może korzystać z portu innego niż 80. Poinformuj platformę Azure o porcie, którego używa obraz, za pomocą ustawienia aplikacji `WEBSITES_PORT`. Na stronie usługi GitHub dotyczącej [przykładowego kodu w języku Python w tym samouczku](https://github.com/Azure-Samples/docker-django-webapp-linux) przedstawiono, co jest potrzebne, aby ustawić opcję `WEBSITES_PORT` na wartość _8000_.

Aby określić ustawienia aplikacji, użyj polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Wykonujesz wdrożenie z rejestru prywatnego platformy Docker? Zobacz opcjonalne instrukcje dotyczące [używania obrazu platformy Docker z dowolnego rejestru prywatnego](#use-a-docker-image-from-any-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Testowanie aplikacji internetowej

Upewnij się, że aplikacja internetowa działa, przechodząc do niej (`http://<app_name>.azurewebsites.net`). 

![Testowanie konfiguracji portu aplikacji internetowej](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Zmienianie aplikacji internetowej i ponowne wdrażanie

W lokalnym repozytorium Git otwórz plik app/templates/app/index.html. Znajdź pierwszy element HTML i zmień go na:

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Po zmodyfikowaniu pliku języka Python i zapisaniu go należy ponownie utworzyć i wypchnąć obraz platformy Docker. Następnie uruchom ponownie aplikację internetową, aby zmiany zaczęły obowiązywać. Użyj tych samych poleceń, których już wcześniej używano w tym samouczku. Zapoznaj się z sekcjami [Tworzenie obrazu na podstawie pliku platformy Docker](#build-the-image-from-the-docker-file) i [Wypychanie obrazu platformy Docker do usługi Docker Hub](#push-the-docker-image-to-docker-hub). Przetestuj aplikację internetową, postępując zgodnie z instrukcjami w sekcji [Testowanie aplikacji internetowej](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Nawiązywanie połączenia z usługą Web App for Containers przy użyciu protokołu SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby niestandardowy obraz platformy Docker obsługiwał protokół SSH, należy wbudować go w plik Dockerfile. Protokół SSH należy włączyć w pliku platformy Docker. Instrukcje dotyczące protokołu SSH zostały już dodane do przykładowego pliku Dockerfile, więc możesz postępować zgodnie z nimi, korzystając z własnego obrazu niestandardowego:

* Następnie instrukcja [RUN](https://docs.docker.com/engine/reference/builder/#run) wywołująca polecenie `apt-get` ustawia hasło konta root na `"Docker!"`.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępny tylko za pośrednictwem witryny Kudu/SCM. Witryna Kudu/SCM jest uwierzytelniana przy użyciu poświadczeń publikowania.

* Instrukcja [COPY](https://docs.docker.com/engine/reference/builder/#copy), która powoduje, że aparat platformy Docker kopiuje plik [sshd_config](https://man.openbsd.org/sshd_config) do katalogu */etc/ssh/*. Plik konfiguracji powinien być oparty na [tym pliku sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Plik *sshd_config* musi zawierać następujące elementy: 
    > * Element `Ciphers` musi zawierać co najmniej jeden element z tej listy: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * Element `MACs` musi zawierać co najmniej jeden element z tej listy: `hmac-sha1,hmac-sha1-96`.

* Instrukcja [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose), która uwidacznia port 2222 w kontenerze. Mimo iż hasło konta root jest znane, nie można uzyskać dostępu do portu 2222 z Internetu. Jest to port wewnętrzny dostępny tylko dla kontenerów w sieci mostkowanej prywatnej sieci wirtualnej. Następnie polecenia kopiują szczegóły konfiguracji protokołu SSH i uruchamiają usługę `ssh`.

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* Pamiętaj, aby [uruchomić usługę SSH](https://github.com/Azure-App-Service/node/blob/master/8.9/startup/init_container.sh#L18), używając skryptu w katalogu /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Otwieranie połączenia SSH z kontenerem

Usługa Web App for Containers nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępny tylko za pośrednictwem witryny Kudu dostępnej pod adresem `https://<app_name>.scm.azurewebsites.net`.

Aby nawiązać połączenie, przejdź pod adres `https://<app_name>.scm.azurewebsites.net/webssh/host` i zaloguj się przy użyciu konta platformy Azure.

Nastąpi wtedy przekierowanie do strony z wyświetloną interaktywną konsolą. 

Możesz sprawdzić, czy w kontenerze są uruchomione określone aplikacje. Aby sprawdzić kontener i zweryfikować uruchomione procesy, uruchom polecenie `top` w wierszu polecenia.

```bash
top
```

Polecenie `top` uwidacznia wszystkie uruchomione procesy w kontenerze.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Gratulacje! Skonfigurowano niestandardowy obraz platformy Docker dla aplikacji internetowej usługi Web App for Containers.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Używanie obrazu prywatnego z usługi Docker Hub (opcjonalnie)

W sekcji [Tworzenie aplikacji internetowej](#create-a-web-app) określono obraz w usłudze Docker Hub w poleceniu `az webapp create`. Jest to wystarczające dla obrazu publicznego. Aby używać obrazu prywatnego, należy skonfigurować identyfikator i hasło konta platformy Docker w aplikacji internetowej platformy Azure.

W usłudze Cloud Shell uruchom polecenie `az webapp create`, a następnie polecenie [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Zastąp ciąg *\<app_name>*, a także ciągi _\<docker-id>_ i _\<password>_, podając identyfikator i hasło platformy Docker.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

Dane wyjściowe polecenia są podobne jak w przypadku następującego ciągu JSON i oznaczają, że zmiana konfiguracji powiodła się:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Używanie obrazu platformy Docker z dowolnego rejestru prywatnego (opcjonalnie)

W tej sekcji dowiesz się, jak używać obrazu platformy Docker z rejestru prywatnego w usłudze Web App for Containers na przykładzie usługi Azure Container Registry. Procedura używania innych rejestrów prywatnych jest podobna. 

Usługa Azure Container Registry jest usługą zarządzaną platformy Docker na platformie Azure służącą do hostowania obrazów prywatnych. Wdrożenia mogą być dowolnego typu, w tam także [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) i Web App for Containers. 

### <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

W usłudze Cloud Shell utwórz rejestr Azure Container Registry przy użyciu polecenia [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create). Przekaż nazwę, grupę zasobów i wartość `Basic` dla jednostki SKU. Dostępne jednostki SKU to `Classic` (Klasyczna), `Basic` (Podstawowa), `Standard` (Standardowa) i `Premium` (Premium).

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Utworzenie kontenera spowoduje zwrócenie następujących danych wyjściowych:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Logowanie w usłudze Azure Container Registry

W celu wypchnięcia obrazu do rejestru, należy podać poświadczenia, aby rejestr zaakceptował wypchnięcie. Te poświadczenia możesz pobrać, wydając polecenie [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) w usłudze Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

To polecenie ujawni dwa hasła, których można użyć z nazwą użytkownika.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Z poziomu okna lokalnego terminalu zaloguj się w usłudze Azure Container Registry przy użyciu polecenia `docker login`. Do zalogowania potrzebna jest nazwa serwera. Użyj formatu `{azure-container-registry-name>.azurecr.io`. Wpisz hasło w wierszu polecenia konsoli.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Upewnij się, że logowanie się powiodło. 

### <a name="push-an-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

> [!NOTE]
> Jeśli używasz własnego obrazu, otaguj go w następujący sposób:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Wypchnij obraz przy użyciu polecenia `docker push`. Otaguj obraz nazwą rejestru, a następnie nazwą i tagiem obrazu.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Upewnij się, że wypchnięcie pomyślnie dodało kontener do rejestru, wyświetlając listę repozytoriów usługi ACR. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Wyświetlenie listy obrazów w rejestrze pozwala potwierdzić, że obraz `mydockerimage` znajduje się w rejestrze.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Konfigurowanie aplikacji internetowej do używania obrazu z rejestru Azure Container Registry (lub dowolnego rejestru prywatnego)

Usługę Web App for Containers możesz skonfigurować tak, aby uruchamiała kontener przechowywany w rejestrze Azure Container Registry. Rejestru Azure Container Registry używa się tak jak dowolnego rejestru prywatnego, jeśli więc chcesz używać własnego rejestru, procedura wykonania tego zadania wygląda podobnie.

W usłudze Cloud Shell wyświetl nazwę użytkownika i hasło dla rejestru Azure Container Registry przy użyciu polecenia [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show). Skopiuj nazwę użytkownika i jedno z haseł, aby móc za ich pomocą skonfigurować aplikację internetową w następnym kroku.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

W usłudze Cloud Shell przypisz obraz niestandardowy platformy Docker do aplikacji internetowej przy użyciu polecenia [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Zastąp ciągi *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ i _\<password>_. W przypadku rejestru Azure Container Registry element *\<docker-registry-server-url>* ma format `https://<azure-container-registry-name>.azurecr.io`. Jeśli oprócz rejestru usługi Docker Hub używasz też jakiegokolwiek innego rejestru, nazwa obrazu musi zaczynać się od w pełni kwalifikowanej nazwy domeny (FQDN) Twojego rejestru. W przypadku rejestru Azure Container Registry będzie to wyglądało następująco: `<azure-container-registry>.azurecr.io/mydockerimage`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> W elemencie *\<docker-registry-server-url>* wymagany jest ciąg `https://`.
>
> [!NOTE]
> Korzystając z rejestru niż dockerhub, `docker-custom-image-name` musi zawierać w pełni kwalifikowana nazwa domeny (FQDN) Twojego rejestru.  
> W przypadku rejestru Azure Container Registry będzie to wyglądało następująco: `<azure-container-registry>.azurecr.io/mydockerimage`.

Dane wyjściowe polecenia są podobne jak w przypadku następującego ciągu JSON i oznaczają, że zmiana konfiguracji powiodła się:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej platformy Docker przy użyciu języka Python i bazy danych PostgreSQL na platformie Azure](tutorial-python-postgresql-app.md)
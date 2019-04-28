---
title: Tworzenie niestandardowego obrazu i dla sieci Web App for Containers — usłudze Azure App Service | Dokumentacja firmy Microsoft
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
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8463ffcb9d9983ff435c01f75dd48f68bde31767
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62117589"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Samouczek: Tworzenie niestandardowego obrazu i uruchamianie w usłudze App Service z rejestru prywatnego

[Usługa App Service](app-service-linux-intro.md) zapewnia wbudowane obrazy platformy Docker w systemie Linux z obsługą określonych wersji, takich jak środowisko PHP 7.0 i Node.js 4.5. Usługa App Service używa technologii kontenerów platformy Docker do hostowania obrazów niestandardowych i wbudowanych obrazów jako platforma jako usługa. W tym samouczku dowiesz się, jak utworzyć obraz niestandardowy i uruchom go w usłudze App Service. Ten wzorzec jest przydatny, gdy wbudowane obrazy nie uwzględniają wybranego przez Ciebie języka lub gdy aplikacja wymaga określonej konfiguracji, której wbudowane obrazy nie obejmują.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie niestandardowych obrazów do prywatnego rejestru kontenerów
> * Uruchamianie niestandardowego obrazu w usłudze App Service
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne wdrażanie obrazu
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Usługa Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

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

Zbuduj obraz Docker za pomocą `docker build` polecenia.

```bash
docker build --tag mydockerimage .
```

Sprawdź, czy kompilacja działa, uruchamiając kontener platformy Docker. Uruchom polecenie [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) i przekaż do niego nazwę oraz tag obrazu. Pamiętaj o określeniu portu przy użyciu argumentu `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Upewnij się, że aplikacja internetowa i kontener działają prawidłowo, przechodząc do adresu `http://localhost:8000`.

![Lokalne testowanie aplikacji internetowych](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Aby utworzyć aplikację, która używa obrazu, który został utworzony, uruchamiasz polecenia interfejsu wiersza polecenia platformy Azure, Utwórz grupę zasobów, wypycha obraz, a następnie tworzy aplikacji sieci web planu usługi App Service, aby go uruchomić.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

W usłudze Cloud Shell utwórz rejestr Azure Container Registry przy użyciu polecenia [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create).

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Zaloguj się do usługi Azure Container Registry

Aby wypchnąć obraz do rejestru, należy do uwierzytelniania przy użyciu prywatnego rejestru. W usłudze Cloud Shell za pomocą [ `az acr show` ](/cli/azure/acr?view=azure-cli-latest#az-acr-show) polecenie, aby pobrać poświadczeń z rejestru zostanie utworzony.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Dane wyjściowe ujawni dwa hasła wraz z nazwą użytkownika.

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

Z poziomu okna lokalnego terminalu, zaloguj się do usługi Azure Container Registry przy użyciu `docker login` polecenia, jak pokazano w poniższym przykładzie. Zastąp  *\<azure kontenera registry-name >* i  *\<registry-username >* za pomocą wartości rejestru. Po wyświetleniu monitu wpisz jedno z haseł z poprzedniego kroku.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Upewnij się, że logowanie zakończy się pomyślnie.

### <a name="push-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

Tag obrazu systemu lokalnego dla usługi Azure Container Registry. Na przykład:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Wypchnij obraz przy użyciu polecenia `docker push`. Otaguj obraz nazwą rejestru, a następnie nazwą i tagiem obrazu.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

W usłudze Cloud Shell Sprawdź, że wypchnięcie zakończyła się powodzeniem.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Powinna pojawić się następujące dane wyjściowe.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Tworzenie aplikacji internetowej

W usłudze Cloud Shell utwórz [aplikację internetową](app-service-linux-intro.md) w planie usługi App Service `myAppServicePlan` za pomocą polecenia [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Zastąp  _\<Nazwa aplikacji >_ przy użyciu unikatowej nazwy aplikacji, i  _\<azure kontenera registry-name >_ przy użyciu nazwy swojego rejestru.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
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
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Skonfiguruj poświadczenia rejestru w aplikacji sieci web

Dla usługi App Service do ściągania obrazu prywatnego potrzebuje ona danych informacji dotyczących obrazu i rejestru. W usłudze Cloud Shell udostępnione im [ `az webapp config container set` ](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) polecenia. Zastąp  *\<Nazwa aplikacji >*,  *\<azure kontenera registry-name >*,  _\<registry-username >_ i  _\<hasło >_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Korzystając z rejestru z innych niż usługi Docker Hub `--docker-registry-server-url` muszą być sformatowane jako `https://` następuje w pełni kwalifikowana nazwa domeny rejestru.
>

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Większość obrazów platformy Docker, użyj niestandardowe zmienne środowiskowe, takie jak portu innego niż 80. Nakazać usłudze App Service o porcie, którego używa obraz, używając `WEBSITES_PORT` ustawienia aplikacji. Na stronie usługi GitHub dotyczącej [przykładowego kodu w języku Python w tym samouczku](https://github.com/Azure-Samples/docker-django-webapp-linux) przedstawiono, co jest potrzebne, aby ustawić opcję `WEBSITES_PORT` na wartość _8000_.

Aby określić ustawienia aplikacji, użyj polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testowanie aplikacji internetowej

Upewnij się, że aplikacja internetowa działa, przechodząc do niej (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Dostęp do aplikacji, po raz pierwszy jego zająć trochę czasu, ponieważ należy ściągnąć obraz całej usługi App Service. Jeśli upłynie limit czasu przeglądarki, wystarczy odświeżyć stronę.

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

Po zmodyfikowaniu pliku języka Python i zapisaniu go należy ponownie utworzyć i wypchnąć obraz platformy Docker. Następnie uruchom ponownie aplikację internetową, aby zmiany zaczęły obowiązywać. Użyj tych samych poleceń, których już wcześniej używano w tym samouczku. Możesz zapoznać się z [utworzyć obraz na podstawie pliku platformy Docker](#build-the-image-from-the-docker-file) i [wypychania obraz do usługi Azure Container Registry](#push-image-to-azure-container-registry). Przetestuj aplikację internetową, postępując zgodnie z instrukcjami w sekcji [Testowanie aplikacji internetowej](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Włącz połączenia SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. W celu udostępnienia połączenia SSH do kontenera, obraz niestandardowy musi być skonfigurowany dla niego. Przyjrzyjmy się w przykładowym repozytorium, który już ma niezbędną konfigurację.

* W [pliku Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile), poniższy kod instaluje serwer SSH, a także ustawia poświadczenia logowania.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępny tylko za pośrednictwem witryny Kudu/SCM. Witryna Kudu/SCM jest uwierzytelniana przy użyciu konta platformy Azure.

* [Pliku Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopie [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config file in the repository) do */etc/ssh/* katalogu.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Pliku Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) uwidacznia port 2222 w kontenerze. Jest to port wewnętrzny dostępny tylko dla kontenerów w sieci mostkowanej prywatnej sieci wirtualnej. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Skrypt wejścia](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) uruchamia serwer SSH.

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

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

Gratulacje! Skonfigurowano niestandardowy kontenera systemu Linux w usłudze App Service.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Kolejne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Wdrażanie niestandardowych obrazów do prywatnego rejestru kontenerów
> * Uruchamianie niestandardowego obrazu w usłudze App Service
> * Konfigurowanie zmiennych środowiskowych
> * Aktualizowanie i ponowne wdrażanie obrazu
> * Uzyskiwanie dostępu do dzienników diagnostycznych
> * Nawiązywanie połączenia z kontenerem przy użyciu protokołu SSH

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: Mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie niestandardowego kontenera](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Samouczek: Aplikację WordPress z obsługą wielu kontenerów](tutorial-multi-container-app.md)

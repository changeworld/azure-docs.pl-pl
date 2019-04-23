---
title: Konfigurowanie klienta - kontenerów w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania aplikacji Node.js do pracy w usłudze Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001885"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurowanie niestandardowego kontenera systemu Linux dla usługi Azure App Service

W tym artykule pokazano, jak skonfigurować niestandardowe kontenera systemu Linux do uruchamiania w usłudze Azure App Service.

Ten przewodnik zawiera podstawowe pojęcia i instrukcje dotyczące konteneryzacji aplikacji systemu Linux w usłudze App Service. Jeśli nie znasz usługi Azure App Service, postępuj zgodnie z [Szybki Start niestandardowego kontenera](quickstart-docker-go.md) i [samouczek](tutorial-custom-docker-image.md) pierwszy. Istnieje również [aplikacja wielu kontenerów — Szybki Start](quickstart-multi-container.md) i [samouczek](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Konfigurowanie numeru portu

Serwer sieci web w obrazie niestandardowym może używać portu innego niż 80. Poinformuj platformę Azure o porcie, którego używa niestandardowych, przy użyciu `WEBSITES_PORT` ustawienia aplikacji. Na stronie usługi GitHub dotyczącej [przykładowego kodu w języku Python w tym samouczku](https://github.com/Azure-Samples/docker-django-webapp-linux) przedstawiono, co jest potrzebne, aby ustawić opcję `WEBSITES_PORT` na wartość _8000_. Można ją ustawić, uruchamiając [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w usłudze Cloud Shell. Na przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Kontenera niestandardowego może używać zmiennych środowiskowych, które należy podać zewnętrznie. Można przekazać je, uruchamiając [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w usłudze Cloud Shell. Na przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Ta metoda działa zarówno dla aplikacji kontenera z jednym lub wieloma kontenerami aplikacji, gdy zmienne środowiskowe są określone w *docker-compose.yml* pliku.

## <a name="use-persistent-shared-storage"></a>Korzystać z udostępnionego magazynu trwałego

Możesz użyć */home* katalogu w systemie plików aplikacji utrwalanie plików między ponowne uruchomienie i udostępniać je między wystąpieniami. `/home` w aplikacji jest dostarczana, aby umożliwić aplikacji kontenera w celu dostępu do magazynu trwałego.

Gdy z magazynu trwałego jest wyłączona, a następnie zapisuje `/home` katalogu nie są zachowywane przez ponowne uruchomienie aplikacji lub w wielu wystąpieniach. Jedynym wyjątkiem jest `/home/LogFiles` katalogu, który jest używany do przechowywania dzienników platformy Docker i kontenerów. Po włączeniu magazynu trwałego, wszystkie operacje zapisu do `/home` katalogu są zachowywane i są dostępne dla wszystkich wystąpień aplikacji skalowanych w poziomie.

Domyślnie jest z magazynu trwałego *wyłączone*. Aby włączyć lub wyłączyć, należy ustawić `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienia aplikacji, uruchamiając [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w usłudze Cloud Shell. Na przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Możesz również [Skonfiguruj Magazyn trwały](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Włączyć protokół SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby kontener niestandardowy obsługiwał protokół SSH należy dodać go do pliku Dockerfile, sam.

> [!TIP]
> Wszystkie wbudowane kontenerów systemu Linux zostały dodane instrukcje dotyczące protokołu SSH w repozytoriach obrazu. Można przejść przez następujące instrukcje z [repozytorium Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) aby zobaczyć, jak to ma włączone.

- Użyj [Uruchom](https://docs.docker.com/engine/reference/builder/#run) instrukcji, aby zainstalować serwer SSH i ustawić hasło dla konta głównego, aby `"Docker!"`. Na przykład w przypadku obrazu na podstawie [Alpine Linux](https://hub.docker.com/_/alpine), potrzebujesz następujących poleceń:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępna wyłącznie za pośrednictwem `https://<app-name>.scm.azurewebsites.net` i uwierzytelnić się za pomocą poświadczeń publikowania.

- Dodaj [tym pliku sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) do repozytorium obrazów i użyj [KOPIOWANIA](https://docs.docker.com/engine/reference/builder/#copy) instrukcjami, aby skopiować plik */etc/ssh/* katalogu. Aby uzyskać więcej informacji na temat *sshd_config* plików, zobacz [dokumentacji OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Plik *sshd_config* musi zawierać następujące elementy:
    > - Element `Ciphers` musi zawierać co najmniej jeden element z tej listy: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - Element `MACs` musi zawierać co najmniej jeden element z tej listy: `hmac-sha1,hmac-sha1-96`.

- Użyj [UJAWNIĆ](https://docs.docker.com/engine/reference/builder/#expose) instrukcjami, aby otworzyć port 2222 w kontenerze. Mimo że hasło główne jest znany, port 2222 jest niedostępny z Internetu. Jest on dostępny tylko przez kontenerów w sieci mostkowanej prywatnej sieci wirtualnej.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- W skrypcie uruchamiania kontenera uruchom serwer SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Aby uzyskać przykład, zobacz jak domyślne [kontenera Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) uruchamia serwer SSH.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurowanie aplikacji obsługującej wiele kontenerów

- [Korzystanie z magazynu trwałego w narzędzia Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ograniczenia wersji zapoznawczej](#preview-limitations)
- [Opcje tworzenia platformy docker](#docker-compose-options)
- [Opcje konfiguracji rozwiązania Kubernetes](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Korzystanie z magazynu trwałego w narzędzia Docker Compose

Wielokontenerowych aplikacji, takich jak WordPress, należy do poprawnego działania z magazynu trwałego. Aby ją włączyć, konfigurację narzędzia Docker Compose musi wskazywać lokalizację magazynu *poza* kontenera. Lokalizacje przechowywania wewnątrz kontenera nie utrwalić zmiany po ponownym uruchomieniu aplikacji.

Włącz z magazynu trwałego, ustawiając `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplikacji ustawienie, przy użyciu [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w usłudze Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

W swojej *docker-compose.yml* plików, mapowanie `volumes` opcję `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` to zmienna środowiskowa w usłudze App Service mapowana na magazyn trwały aplikacji. Na przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Użyj magazynu niestandardowego w narzędzia Docker Compose

Usługa Azure Storage (Azure Files lub Azure Blob) mogą być instalowane przy użyciu aplikacji obsługującej wiele kontenerów przy użyciu identyfikatora niestandardowe. Aby wyświetlić nazwę niestandardowego id, uruchom [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

W swojej *docker-compose.yml* plików, mapowanie `volumes` opcję `custom-id`. Na przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Wiele kontenerów jest obecnie w wersji zapoznawczej. Poniższe funkcje platformy usługi App Service nie są obsługiwane:

- Uwierzytelnianie/autoryzacja
- Tożsamości zarządzane

### <a name="docker-compose-options"></a>Opcje tworzenia platformy docker

Poniższej przedstawiono obsługiwane i nieobsługiwane opcje narzędzia Docker Compose konfiguracji:

#### <a name="supported-options"></a>Obsługiwane opcje

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nieobsługiwane opcje

- build (niedozwolona)
- depends_on (ignorowana)
- networks (ignorowana)
- secrets (ignorowana)
- porty inne niż 80 i 8080 (zignorowany)

> [!NOTE]
> Inne opcje nie są jawnie wywoływane są ignorowane w publicznej wersji zapoznawczej.

### <a name="kubernetes-configuration-options"></a>Opcje konfiguracji rozwiązania Kubernetes

Następujące opcje konfiguracji są obsługiwane w przypadku rozwiązania Kubernetes:

- args
- command
- containers
- image
- name
- ports
- spec

> [!NOTE]
> Inne opcje nie są jawnie przywołane nie są obsługiwane w publicznej wersji zapoznawczej.
>

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie z repozytorium prywatnego kontenerów](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Samouczek: Aplikację WordPress z obsługą wielu kontenerów](tutorial-multi-container-app.md)

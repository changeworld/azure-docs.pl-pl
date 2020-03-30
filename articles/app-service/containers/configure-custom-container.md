---
title: Konfigurowanie niestandardowego kontenera systemu Linux
description: Dowiedz się, jak skonfigurować niestandardowy kontener systemu Linux w usłudze Azure App Service. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280146"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurowanie niestandardowego kontenera systemu Linux dla usługi Azure App Service

W tym artykule pokazano, jak skonfigurować niestandardowy kontener systemu Linux do uruchomienia w usłudze Azure App Service.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dotyczące konteneryzacji aplikacji systemu Linux w usłudze App Service. Jeśli nigdy nie używasz usługi Azure App Service, wykonaj najpierw [niestandardowy szybki start kontenera](quickstart-docker-go.md) i [samouczek.](tutorial-custom-docker-image.md) Istnieje również [wiele kontenerów aplikacji Szybki start](quickstart-multi-container.md) i [samouczek](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Konfigurowanie numeru portu

Serwer www w obrazie niestandardowym może używać portu innego niż 80. Powiesz platformie Azure o porcie używanym `WEBSITES_PORT` przez kontener niestandardowy przy użyciu ustawienia aplikacji. Na stronie usługi GitHub dotyczącej [przykładowego kodu w języku Python w tym samouczku](https://github.com/Azure-Samples/docker-django-webapp-linux) przedstawiono, co jest potrzebne, aby ustawić opcję `WEBSITES_PORT` na wartość _8000_. Można go ustawić, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) uruchamiając polecenie w usłudze Cloud Shell. Przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Kontener niestandardowy może używać zmiennych środowiskowych, które muszą być dostarczane zewnętrznie. Można je przekazać, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) uruchamiając polecenie w usłudze Cloud Shell. Przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Ta metoda działa zarówno dla aplikacji z jednym kontenerem, jak i aplikacji z wieloma kontenerami, gdzie zmienne środowiskowe są określone w pliku *docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Korzystanie z trwałego udostępnionego magazynu

Za pomocą *katalogu /home* w systemie plików aplikacji można utrwalić pliki podczas ponownego uruchamiania i udostępniać je w różnych wystąpieniach. W `/home` aplikacji jest dostępna, aby umożliwić aplikacji kontenera dostęp do magazynu trwałego.

Gdy magazyn trwały jest wyłączony, `/home` a następnie zapisy w katalogu nie są zachowywane przez ponowne uruchomienie aplikacji lub w wielu wystąpieniach. Jedynym wyjątkiem jest `/home/LogFiles` katalog, który jest używany do przechowywania dzienników platformy Docker i kontenera. Gdy magazyn trwały jest włączony, wszystkie `/home` zapisy w katalogu są utrwalone i mogą być dostępne dla wszystkich wystąpień aplikacji skalowane w poziomie.

Domyślnie magazyn trwały jest *włączony,* a ustawienie nie jest widoczne w ustawieniach aplikacji. Aby go wyłączyć, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustaw ustawienie [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) aplikacji, uruchamiając polecenie w aplikacji Cloud Shell. Przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Można również [skonfigurować własną pamięć trwałą](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Włącz SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby kontener niestandardowy do obsługi SSH, należy dodać go do dockerfile się.

> [!TIP]
> Wszystkie wbudowane kontenery systemu Linux dodały instrukcje SSH w swoich repozytoriach obrazów. Możesz przejść przez następujące instrukcje z [repozytorium Node.js 10.14,](https://github.com/Azure-App-Service/node/blob/master/10.14) aby zobaczyć, jak jest tam włączona.

- Użyj instrukcji [RUN,](https://docs.docker.com/engine/reference/builder/#run) aby zainstalować serwer SSH i ustawić `"Docker!"`hasło dla konta głównego na . Na przykład dla obrazu opartego na [Alpine Linux](https://hub.docker.com/_/alpine)potrzebne są następujące polecenia:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. SSH jest dostępny `https://<app-name>.scm.azurewebsites.net` tylko za pośrednictwem i uwierzytelniony z poświadczeniami publikowania.

- Dodaj [ten plik sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) do repozytorium obrazów i użyj instrukcji [KOPIUJ,](https://docs.docker.com/engine/reference/builder/#copy) aby skopiować plik do katalogu */etc/ssh/.* Aby uzyskać więcej informacji na temat *sshd_config* plików, zobacz [Dokumentacja OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Plik *sshd_config* musi zawierać następujące elementy:
    > - Element `Ciphers` musi zawierać co najmniej jeden element z tej listy: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - Element `MACs` musi zawierać co najmniej jeden element z tej listy: `hmac-sha1,hmac-sha1-96`.

- Użyj instrukcji [EXPOSE,](https://docs.docker.com/engine/reference/builder/#expose) aby otworzyć port 2222 w kontenerze. Chociaż hasło główne jest znane, port 2222 jest niedostępny z Internetu. Jest dostępny tylko przez kontenery w sieci mostowej prywatnej sieci wirtualnej.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- W skrypcie rozruchowym kontenera uruchom serwer SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Na przykład zobacz, jak [domyślny kontener Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) uruchamia serwer SSH.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurowanie aplikacji z wieloma kontenerami

- [Używanie magazynu trwałego w kompozycji dokowane](#use-persistent-storage-in-docker-compose)
- [Ograniczenia wersji zapoznawczej](#preview-limitations)
- [Opcje redagowania docker](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Używanie magazynu trwałego w kompozycji dokowane

Aplikacje wielokontenowe, takie jak WordPress, potrzebują trwałego przechowywania, aby działać poprawnie. Aby ją włączyć, konfiguracja dokceny compose musi wskazywać lokalizację magazynu *poza* kontenerem. Lokalizacje magazynu wewnątrz kontenera nie utrwalają zmian poza ponownym uruchomieniem aplikacji.

Włącz magazyn trwały, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawiając ustawienie aplikacji, używając polecenia [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w aplikacji Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

W pliku *docker-compose.yml* `volumes` mapuj `${WEBAPP_STORAGE_HOME}`opcję na . 

`WEBAPP_STORAGE_HOME` to zmienna środowiskowa w usłudze App Service mapowana na magazyn trwały aplikacji. Przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Multi-container jest obecnie w wersji zapoznawczej. Następujące funkcje platformy usługi App Service nie są obsługiwane:

- Uwierzytelnianie/autoryzacja
- Tożsamości zarządzane

### <a name="docker-compose-options"></a>Opcje redagowania docker

Na poniższych listach przedstawiono obsługiwane i nieobsługiwalne opcje konfiguracji docker compose:

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
- porty inne niż 80 i 8080 (ignorowane)

> [!NOTE]
> Wszystkie inne opcje, które nie zostały jawnie wywołane, są ignorowane w publicznej wersji zapoznawczej.

## <a name="configure-vnet-integration"></a>Konfigurowanie integracji sieci wirtualnej

Przy użyciu kontenera niestandardowego z integracji sieci wirtualnej może wymagać dodatkowej konfiguracji kontenera. Zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie z prywatnego repozytorium kontenerów](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Samouczek: Wieloobiektowa aplikacja WordPress](tutorial-multi-container-app.md)

---
title: Wdrażanie aplikacji języka Python w funkcji Azure Web App for Containers
description: Opis wdrażania obrazu platformy Docker z aplikacją języka Python w funkcji Web App for Containers.
keywords: azure app service, web app, python, docker, container
services: app-service
author: cephalin
manager: jeconnoc
ms.service: app-service
ms.devlang: python
ms.topic: quickstart
ms.date: 07/13/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6d328d8a3556f565e7eac8ee079bd191b7dcadef
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433446"
---
# <a name="deploy-a-python-web-app-in-web-app-for-containers"></a>Wdrażanie aplikacji internetowej języka Python w funkcji Web App for Containers

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku Szybki start pokazano, jak utworzyć aplikację internetową i wdrożyć w niej prostą aplikację platformy Flask przy użyciu niestandardowego obrazu usługi Docker Hub. Do tworzenia aplikacji internetowej używany jest [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
* <a href="https://www.docker.com/community-edition" target="_blank">Zainstaluj platformę Docker Community Edition</a>
* <a href="https://hub.docker.com/" target="_blank">Załóż konto usługi Docker Hub</a>

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenia, aby sklonować aplikację przykładową na maszynę lokalną, a następnie przejść do katalogu zawierającego przykład kodu.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

To repozytorium zawiera prostą aplikację platformy Flask, znajdującą się w folderze _/app_, oraz plik _Dockerfile_ określający trzy parametry:

- Użyj obrazu podstawowego [tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).
- Kontener powinien nasłuchiwać na porcie 8000.
- Skopiu katalog `/app` do katalogu `/app` w kontenerze.

Konfiguracja jest zgodna z [instrukcjami dotyczącymi obrazu podstawowego](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację w kontenerze platformy Docker.

```bash
docker build --rm -t flask-quickstart .
docker run --rm -it -p 8000:8000 flask-quickstart
```

Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:8000`.

Na stronie zostanie wyświetlony komunikat **Witaj, świecie** z przykładowej aplikacji.

![Przykładowa aplikacja działająca w środowisku lokalnym](media/quickstart-python/localhost-hello-world-in-browser.png)

W oknie terminalu naciśnij klawisze **Ctrl+C**, aby zatrzymać kontener.

## <a name="deploy-image-to-docker-hub"></a>Wdrażanie obrazu w usłudze Docker Hub

Zaloguj się na swoim koncie usługi Docker Hub. Po wyświetleniu monitu wprowadź swoje poświadczenia usługi Docker Hub.

```bash
docker login
```

Dodaj tagi do obrazu i wypchnij go do nowego repozytorium _publicznego_ na koncie usługi Docker Hub, o nazwie `flask-quickstart`. Zastąp ciąg *\<dockerhub-id>* swoim identyfikatorem usługi Docker Hub.

```bash
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

> [!NOTE]
> Polecenie `docker push` tworzy repozytorium publiczne, jeśli wskazane repozytorium nie istnieje. W tym przewodniku Szybki start założono użycie publicznego repozytorium w usłudze Docker Hub. Jeśli wolisz wypchnąć obraz do repozytorium prywatnego, później będzie konieczne skonfigurowanie poświadczeń usługi Docker Hub w usłudze Azure App Service. Zobacz [Tworzenie aplikacji internetowej](#create-a-web-app).

Po zakończeniu wypychania obrazu możesz używać go w swojej aplikacji internetowej platformy Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Utwórz [aplikację internetową](../app-service-web-overview.md) w `myAppServicePlan`planie usługi App Service za pomocą polecenia [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Zastąp ciąg *\<app name* globalnie unikatową nazwą aplikacji, a ciąg *\<dockerhub_id* swoim identyfikatorem usługi Docker Hub.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name <dockerhub_id>/flask-quickstart
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
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Jeśli wcześniej obraz został przekazany do repozytorium prywatnego, musisz również skonfigurować poświadczenia usługi Docker Hub w usłudze App Service. Aby uzyskać więcej informacji, zobacz [Używanie obrazu prywatnego z usługi Docker Hub](tutorial-custom-docker-image.md#use-a-private-image-from-docker-hub-optional).

### <a name="specify-container-port"></a>Określanie portu kontenera

Zgodnie z ustawieniem w pliku _Dockerfile_ kontener nasłuchuje na porcie 8000. Aby usługa App Service kierowała żądania do właściwego portu, należy ustawić parametr *WEBSITES_PORT* w ustawieniach aplikacji.

W usłudze Cloud Shell uruchom polecenie [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).


```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings WEBSITES_PORT=8000
```

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

```bash
http://<app_name>.azurewebsites.net/
```

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-world-in-browser.png)

> [!NOTE]
> Uruchomienie aplikacji internetowej przy pierwszym żądaniu może trochę potrwać, ponieważ wymaga pobrania i uruchomienia obrazu z usługi Docker Hub. Jeśli za pierwszym razem po upływie długiego czasu zobaczysz błąd, po prostu odśwież stronę.

**Gratulacje!** Wdrożono niestandardowy obraz platformy Docker z aplikacją języka Python w funkcji Web App for Containers.

## <a name="update-locally-and-redeploy"></a>Lokalne aktualizowanie i ponowne wdrażanie

Za pomocą lokalnego edytora tekstów otwórz plik `app/main.py` w aplikacji w języku Python i wprowadź niewielką zmianę w tekście obok instrukcji `return`:

```python
return 'Hello, Azure!'
```

Ponownie skompiluj obraz i ponownie wypchnij go do usługi Docker Hub.

```bash
docker build --rm -t flask-quickstart .
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

Używając usługi Cloud Shell, uruchom ponownie aplikację. Ponowne uruchomienie aplikacji gwarantuje, że wszystkie ustawienia zostaną zastosowane, a z rejestru zostanie ściągnięty najnowszy kontener.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Poczekaj około 15 sekund, aż usługa App Service ściągnie zaktualizowany obraz. Przejdź z powrotem do okna przeglądarki otwartego w kroku **przechodzenia do aplikacji**, a następnie odśwież stronę.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację internetową.

W lewym menu kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji internetowej platformy Azure](./media/quickstart-python/app-service-list.png)

Domyślnie portal zawiera stronę **Omówienie** aplikacji internetowej. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/quickstart-python/app-service-detail.png)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Python z PostgreSQL](tutorial-docker-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Używanie niestandardowych obrazów](tutorial-custom-docker-image.md)
---
title: 'Szybki start: tworzenie aplikacji Python w systemie Linux — usługa Azure App Service'
description: Wdróż swoją pierwszą aplikację Hello World w języku Python w usłudze Azure App Service w systemie Linux w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: msangapu-msft
ms.topic: quickstart
ms.date: 1/14/2020
ms.author: msangapu
ms.custom: seo-python-october2019
experimental: false
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 68dc36ce96737fe8395280c3a833e359084d2fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78246854"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Szybki start: tworzenie aplikacji języka Python w usłudze Azure App Service w systemie Linux

W tym przewodniku Szybki start można wdrożyć aplikację sieci Web języka Python do [usługi App Service w systemie Linux](app-service-linux-intro.md), wysoce skalowalna, samoładująca się usługa hostingu azure. Lokalny interfejs [wiersza polecenia platformy Azure (CLI)](/cli/azure/install-azure-cli) jest używany na komputerze Mac, Linux lub Windows. Skonfigurowana aplikacja sieci web używa bezpłatnej warstwy usługi App Service, więc w trakcie tego artykułu nie ponosisz żadnych kosztów.

Jeśli wolisz wdrażać aplikacje za pośrednictwem ide, zobacz [Wdrażanie aplikacji języka Python w usłudze App Service z programu Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 jest również obsługiwany)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Interfejs wiersza polecenia platformy Azure</a>

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenie, aby sklonować przykładową aplikację na komputerze lokalnym. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Następnie przejdź do tego folderu:

```terminal
cd python-docs-hello-world
```

Repozytorium zawiera plik *application.py,* który informuje app service, że kod zawiera aplikację Flask. Aby uzyskać więcej informacji, zobacz [Proces uruchamiania kontenera i dostosowania](how-to-configure-python.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Narzędzie wiersza polecenia platformy Azure zapewnia wiele wygodnych poleceń używanych z terminala lokalnego do inicjowania obsługi administracyjnej zasobów platformy Azure i zarządzania nimi z wiersza polecenia. Polecenia służy do wykonywania tych samych zadań, które można za pośrednictwem witryny Azure portal w przeglądarce. Można również użyć poleceń interfejsu wiersza polecenia w skryptach do automatyzacji procesów zarządzania.

Aby uruchomić polecenia platformy Azure w platformie Azure `az login` CLI, należy najpierw zalogować się przy użyciu polecenia. To polecenie otwiera przeglądarkę w celu zebrania poświadczeń.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Wdrażanie próbki

Polecenie [`az webapp up`](/cli/azure/webapp#az-webapp-up) tworzy aplikację sieci web w usłudze App Service i wdraża kod.

W folderze *python-docs-hello-world* zawierającym przykładowy `az webapp up` kod uruchom następujące polecenie. Zamień `<app-name>` na globalnie unikatową nazwę aplikacji *(prawidłowe znaki to `a-z`, `0-9`i `-` *). Również `<location-name>` zastąpić regionu Azure, takich jak **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**, i tak dalej. (Można pobrać listę dozwolonych regionów dla konta platformy Azure, uruchamiając [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) polecenie).


```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

To polecenie może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji w przeglądarce `http://<app-name>.azurewebsites.net`internetowej pod adresem URL .

Przykładowy kod języka Python jest uruchomiony kontener systemu Linux w usłudze App Service przy użyciu wbudowanego obrazu.

![Uruchamianie przykładowej aplikacji języka Python na platformie Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulacje!** Aplikacja Języka Python została wdrożona w usłudze App Service w systemie Linux.

## <a name="run-the-sample-app-locally"></a>Lokalne uruchamianie przykładowej aplikacji

W oknie terminala użyj poniższych poleceń (odpowiednio dla systemu operacyjnego), aby zainstalować wymagane zależności i uruchomić wbudowany serwer dewelopera. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Otwórz przeglądarkę internetową i przejdź do `http://localhost:5000/`przykładowej aplikacji pod adresem . Aplikacja wyświetla komunikat **Hello World!**.

![Lokalne uruchamianie przykładowej aplikacji języka Python](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

W oknie terminala naciśnij klawisz **Ctrl**+**C,** aby wyjść z serwera sieci Web.

## <a name="redeploy-updates"></a>Ponowne wdrożenie aktualizacji

W ulubionym edytorze kodu otwórz *application.py* i `return` zmień instrukcję w ostatnim wierszu, aby dopasować go do następującego kodu. Instrukcja `print` znajduje się w tym miejscu, aby wygenerować dane wyjściowe rejestrowania, z którymi pracujesz w następnej sekcji. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Zapisz zmiany i zamknij edytor. 

Ponownie rozmieścić aplikację przy `az webapp up` użyciu następującego polecenia, przy użyciu tego samego polecenia, które zostały użyte do wdrożenia aplikacji po raz pierwszy, zastępując `<app-name>` i `<location-name>` z tych samych nazw, które były wcześniej używane. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Po zakończeniu wdrażania przełącz się z `http://<app-name>.azurewebsites.net` powrotem do otwartego okna przeglądarki i odśwież stronę, która powinna wyświetlić zmodyfikowany komunikat:

![Uruchamianie zaktualizowanej przykładowej aplikacji języka Python na platformie Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code udostępnia zaawansowane rozszerzenia dla języka Python i usługi Azure App Service, które upraszczają proces wdrażania aplikacji internetowych języka Python w usłudze App Service. Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji języka Python w usłudze App Service z programu Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Można uzyskać dostęp do dzienników konsoli generowanych z wewnątrz aplikacji i kontenera, w którym działa. Dzienniki obejmują wszelkie `print` dane wyjściowe generowane przy użyciu instrukcji.

Najpierw włącz rejestrowanie kontenerów, uruchamiając następujące polecenie `<app-name>` w terminalu, `<resource-group-name>` zastępując nazwą aplikacji i nazwą grupy zasobów `az webapp up` wyświetlaną w danych wyjściowych użytego polecenia (na przykład "appsvc_rg_Linux_centralus"):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Po włączeniu rejestrowania kontenerów uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Odśwież aplikację w przeglądarce, aby wygenerować dzienniki konsoli, które powinny zawierać wiersze podobne do następującego tekstu. Jeśli nie widzisz danych wyjściowych od razu, spróbuj ponownie w ciągu 30 sekund.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Można również sprawdzić pliki dziennika z `https://<app-name>.scm.azurewebsites.net/api/logs/docker`przeglądarki w .

Aby zatrzymać przesyłanie strumieniowe `Ctrl` + `C`dziennika w dowolnym momencie, wpisz .

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją. Wyszukaj i wybierz **pozycję Usługi aplikacji**.

![Przechodzenie do usług aplikacji w witrynie Azure portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Wybierz nazwę aplikacji platformy Azure.

![Przechodzenie do aplikacji języka Python w usługach app services w witrynie Azure portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Zarządzanie aplikacją języka Python na stronie Przegląd w witrynie Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Menu Usługi aplikacji zawiera różne strony do konfigurowania aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Grupa zasobów ma nazwę taką jak "appsvc_rg_Linux_CentralUS" w zależności od lokalizacji. Jeśli używasz jednostki SKU usługi app service innych niż bezpłatna warstwa F1, te zasoby poniosą bieżące koszty.

Jeśli nie spodziewasz się, że te zasoby będą potrzebne w przyszłości, `<resource-group-name>` usuń grupę zasobów, uruchamiając `az webapp up` następujące polecenie, zastępując grupę zasobów pokazaną w danych wyjściowych polecenia, taką jak "appsvc_rg_Linux_centralus". Polecenie może potrwać minutę.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Python (Django) aplikacja internetowa z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Języka Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Samouczek: Uruchamianie aplikacji Python w kontenerze niestandardowym](tutorial-custom-docker-image.md)

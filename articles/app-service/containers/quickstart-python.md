---
title: Tworzenie aplikacji w języku Python w systemie Linux — Azure App Service | Microsoft Docs
description: Wdróż swoją pierwszą aplikację Hello World w języku Python w Azure App Service w systemie Linux w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
experimental: true
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: ed7e0df77abb39c07bea8946381247539afb3134
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177027"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Tworzenie aplikacji w języku Python w Azure App Service w systemie Linux

W tym przewodniku szybki start wdrożono prostą aplikację w języku Python do [App Service w systemie Linux](app-service-linux-intro.md), która zapewnia wysoce skalowalną, samoobsługową usługę hostingu w Internecie. Korzystając z interfejsu wiersza polecenia platformy Azure ( [CLI](/cli/azure/install-azure-cli)) za pośrednictwem interakcyjnego, opartego na przeglądarce Azure Cloud Shell, można wykonać kroki opisane w sekcji Korzystanie z komputera Mac, Linux lub Windows.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start:

* <a href="https://www.python.org/downloads/" target="_blank">Zainstaluj Język Python 3,7</a>
* <a href="https://git-scm.com/" target="_blank">Instalowanie usługi git</a>
* Subskrypcja platformy Azure. Jeśli jeszcze tego nie zrobiono, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="download-the-sample-locally"></a>Pobierz przykład lokalnie

W oknie terminalu uruchom następujące polecenia, aby sklonować przykładową aplikację na maszynę lokalną, a następnie przejdź do katalogu przy użyciu przykładowego kodu.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Repozytorium zawiera *Application.py*, które informuje App Service, że repozytorium zawiera aplikację z kolbą. Aby uzyskać więcej informacji, zobacz temat [proces uruchamiania kontenera i dostosowania](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Uruchamianie aplikacji lokalnie

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna wyglądać podczas wdrażania na platformie Azure. Otwórz okno terminalu i Użyj poniższych poleceń, aby zainstalować wymagane zależności i uruchomić wbudowany serwer programistyczny. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

```

Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji w `http://localhost:5000/`.

Zobaczysz **Hello World!** komunikat z przykładowej aplikacji wyświetlanej na stronie.

![Przykładowa aplikacja uruchamiana lokalnie](media/quickstart-python/hello-world-in-browser.png)

W oknie terminalu naciśnij **kombinację klawiszy CTRL + C** , aby wyjść z serwera sieci Web.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Pobierz przykład

W Cloud Shell Utwórz katalog szybkiego startu, a następnie przejdź do niego.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Następnie uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji do katalogu szybkiego startu.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

W trakcie działania są wyświetlane informacje podobne do następującego przykładu:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>tworzenie aplikacji internetowej

Przejdź do katalogu, który zawiera przykładowy kod, i uruchom polecenie `az webapp up`.

W poniższym przykładzie Zastąp `<app-name>` globalnie unikatową nazwą aplikacji (*prawidłowe znaki to `a-z`, `0-9` i `-`* ).

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Wykonanie tego polecenia może potrwać kilka minut. W trakcie działania są wyświetlane informacje podobne do następującego przykładu:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
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
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Przejdź do aplikacji

Przejdź do wdrożonej aplikacji przy użyciu przeglądarki sieci Web.

```bash
http://<app-name>.azurewebsites.net
```

Przykładowy kod w języku Python jest uruchamiany w App Service w systemie Linux przy użyciu wbudowanego obrazu.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-world-in-browser.png)

**Nabycia!** Twoja pierwsza aplikacja w języku Python została wdrożona w celu App Service w systemie Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Aktualizuj lokalnie i ponownie Wdróż kod

W Cloud Shell wprowadź `code application.py`, aby otworzyć Edytor Cloud Shell.

![Application.py kodu](media/quickstart-python/code-applicationpy.png)

 Wprowadź niewielką zmianę w tekście w wywołaniu `return`:

```python
return "Hello Azure!"
```

Zapisz zmiany i Zamknij Edytor. Za pomocą polecenia `^S` Zapisz i `^Q`, aby wyjść.

Wdróż ponownie aplikację przy użyciu [`az webapp up`](/cli/azure/webapp#az-webapp-up) polecenia. Zastąp nazwę aplikacji `<app-name>` i określ lokalizację dla `<location-name>` (przy użyciu jednej z wartości podanych w [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) polecenie).

```bash
az webapp up -n <app-name> -l <location-name>
```

Po zakończeniu wdrażania przejdź z powrotem do okna przeglądarki otwartego w kroku **Przejdź do aplikacji** , a następnie Odśwież stronę.

![Zaktualizowana Przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Przejdź do <a href="https://portal.azure.com" target="_blank">Azure Portal</a> , aby zarządzać utworzoną aplikacją.

Z menu po lewej stronie wybierz pozycję **App Services**, a następnie wybierz nazwę aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-python/app-service-list.png)

Zobaczysz stronę omówienia swojej aplikacji. W tym miejscu można wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Strona App Service w Azure Portal](media/quickstart-python/app-service-detail.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja sieci Web języka Python (Django) z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie aplikacji języka Python w kontenerze niestandardowym](tutorial-custom-docker-image.md)

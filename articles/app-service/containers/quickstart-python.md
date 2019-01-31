---
title: Tworzenie aplikacji w języku Python w systemie Linux — Azure App Service | Microsoft Docs
description: Wdróż swoją pierwszą aplikację Hello World w języku Python w usłudze Azure App Service w systemie Linux w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: be78c91a4fb5c1e79e7b58620f65c9f17bfb4bae
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226489"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Tworzenie aplikacji języka Python w usłudze App Service dla systemu Linux (wersja zapoznawcza)

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku Szybki Start przedstawiono sposób wdrażania aplikacji w języku Python na podstawie wbudowanego obrazu Python (wersja zapoznawcza) w usłudze App Service w systemie Linux przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Kroki podane w tym artykule możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://www.python.org/downloads/" target="_blank">Instalacja języka Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenia, aby sklonować aplikację przykładową na maszynę lokalną, a następnie przejść do katalogu zawierającego przykładowy kod.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Repozytorium zawiera plik *application.py*, który informuje usługę App Service o tym, które repozytorium zawiera aplikację Flask. Aby uzyskać więcej informacji, zobacz [Proces uruchamiania kontenera i dostosowania](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. Otwórz okno terminala i użyj poleceń poniżej, aby zainstalować wymagane zależności i uruchomić wbudowany serwer wdrożeniowy. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:5000/`.

Na stronie zostanie wyświetlony komunikat **Hello World!** z przykładowej aplikacji.

![Przykładowa aplikacja działająca w środowisku lokalnym](media/quickstart-python/hello-world-in-browser.png)

W oknie terminalu naciśnij kombinację klawiszy **Ctrl + C**, aby zamknąć serwer sieci Web.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

Przejdź do witryny, aby zobaczyć nowo utworzoną aplikację z wbudowanym obrazem. Zastąp ciąg _&lt;app name>_ nazwą swojej aplikacji.

```bash
http://<app_name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja:

![Pusta strona aplikacji](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 42, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (39/39), done.
Writing objects: 100% (42/42), 9.43 KiB | 0 bytes/s, done.
Total 42 (delta 15), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'c40efbb40e'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
.
.
.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://user2234@cephalin-python.scm.azurewebsites.net/cephalin-python.git
 * [new branch]      master -> master
 ```

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```bash
http://<app_name>.azurewebsites.net
```

Przykładowy kod w języku Python jest uruchamiany w usłudze App Service dla systemu Linux z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-world-in-browser.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Python w usłudze App Service w systemie Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Lokalne aktualizowanie i ponowne wdrażanie kodu

W repozytorium lokalnym otwórz plik `application.py` i wprowadź niewielką zmianę w tekście w ostatniej linijce:

```python
return "Hello Azure!"
```

Zatwierdź zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po zakończeniu wdrożenia przejdź z powrotem do okna przeglądarki otwartego w kroku **przechodzenia do aplikacji**, a następnie odśwież stronę.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-python/app-service-list.png)

Zostanie wyświetlona strona Przegląd aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Strona usługi App Service w witrynie Azure Portal](media/quickstart-python/app-service-detail.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Wbudowany obraz języka Python w usłudze App Service w systemie Linux jest obecnie w wersji zapoznawczej. Można dostosować polecenie używane do uruchamiania aplikacji. Można też tworzyć aplikacje produkcyjne w języku Python przy użyciu kontenera niestandardowego.

> [!div class="nextstepaction"]
> [Python z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie niestandardowego polecenia uruchamiania](how-to-configure-python.md#customize-startup-command)

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów](how-to-configure-python.md#troubleshooting)

> [!div class="nextstepaction"]
> [Używanie niestandardowych obrazów](tutorial-custom-docker-image.md)

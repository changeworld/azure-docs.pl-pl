---
title: Tworzenie aplikacji internetowej Node.js na platformie Azure | Microsoft Docs
description: Wdróż swoją pierwszą aplikację Hello World w środowisku Node.js w usłudze Azure App Service Web Apps w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/13/2017
ms.author: cephalin;cfowler
ms.custom: mvc, devcenter
ms.openlocfilehash: 31c1ba30eaff66016202888f8488b0e9e2bdc679
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439055"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Tworzenie aplikacji internetowej Node.js na platformie Azure

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć aplikację w usłudze App Service w systemie _Linux_, zobacz [Tworzenie aplikacji internetowej środowiska Node.js w usłudze Azure App Service w systemie Linux](./containers/quickstart-nodejs.md).
>

Usługa [Azure Web Apps](app-service-web-overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostowaną w Internecie.  Ten samouczek Szybki start przedstawia sposób wdrażania aplikacji Node.js w usłudze Azure Web Apps. Aplikację internetową możesz utworzyć przy użyciu [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), a usługa Git umożliwia wdrażanie przykładowego kodu w języku Node.js w aplikacji internetowej.

![Przykładowa aplikacja działająca na platformie Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Poniższe kroki możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux. Po zainstalowaniu wymagań wstępnych wykonanie czynności trwa około pięciu minut.   

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-Node-Developers/Create-a-Nodejs-app-in-Azure-Quickstart/player]   

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://nodejs.org/" target="_blank">Zainstaluj środowisko Node.js i menedżer NPM</a>

## <a name="download-the-sample"></a>Pobierz przykład

Pobierz przykładowy projekt Node.js z [https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) i wyodrębnij archiwum ZIP.

W oknie terminalu przejdź do katalogu głównego przykładowego projektu Node.js (zawierającego plik _index.js_).

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie, otwierając okno terminala i korzystając ze skryptu `npm start` w celu uruchomienia wbudowanego serwera HTTP środowiska Node.js.

```bash
npm start
```

Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:1337`.

Na stronie zostanie wyświetlony komunikat **Hello World** z przykładowej aplikacji.

![Przykładowa aplikacja działająca w środowisku lokalnym](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

W oknie terminalu naciśnij kombinację klawiszy **Ctrl + C**, aby zamknąć serwer sieci Web.

[!INCLUDE [Create ZIP file](../../includes/app-service-web-create-zip.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)] 

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W usłudze Cloud Shell utwórz aplikację internetową w planie usługi App Service `myAppServicePlan` za pomocą polecenia [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). 

W poniższym przykładzie zastąp ciąg `<app_name>` globalnie unikatową nazwą aplikacji (prawidłowe znaki to `a-z`, `0-9` i `-`). Środowisko uruchomieniowe ma ustawioną wartość `NODE|6.9`. Aby wyświetlić wszystkie obsługiwane środowiska uruchomieniowe, uruchom polecenie [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9"
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9"
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
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Przejdź do nowo utworzonej aplikacji internetowej. Zastąp ciąg _&lt;nazwa aplikacji>_ unikatową nazwą aplikacji.

```bash
http://<app name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja internetowa:

![Pusta strona aplikacji internetowej](media/app-service-web-get-started-php/app-service-web-service-created.png)

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```
http://<app_name>.azurewebsites.net
```

Przykładowy kod w języku Node.js jest uruchamiany w aplikacji internetowej usługi Azure App Service.

![Przykładowa aplikacja działająca na platformie Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Node.js w usłudze App Service.

## <a name="update-and-redeploy-the-code"></a>Aktualizowanie i ponowne wdrażanie kodu

Za pomocą edytora tekstów otwórz plik `index.js` w aplikacji Node.js i wprowadź niewielką zmianę w tekście w wywołaniu `response.end`:

```nodejs
response.end("Hello Azure!");
```

W lokalnym oknie terminalu przejdź do katalogu głównego aplikacji i utwórz nowy plik ZIP dla zaktualizowanego projektu.

```
# Bash
zip -r myUpdatedAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myUpdatedAppFiles.zip
``` 

Wdróż ten nowy plik ZIP w usłudze App Service, korzystając z tych samych kroków w sekcji [Przekazywanie pliku ZIP](#upload-the-zip-file).

Przejdź z powrotem do okna przeglądarki otwartego w kroku **przechodzenia do aplikacji**, a następnie odśwież stronę.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Zarządzanie nową aplikacją internetową platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją internetową.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji internetowej platformy Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Strona usługi App Service w witrynie Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Środowisko Node.js z bazą danych MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

---
title: Technologie wdrażania w Azure Functions | Microsoft Docs
description: Poznaj różne sposoby wdrażania kodu do Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 7f931a72eab534bc2856e9e545b684d2b8ae7a60
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444036"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie wdrażania w Azure Functions

Do wdrożenia kodu projektu Azure Functions na platformie Azure można użyć kilku różnych technologii. Ten artykuł zawiera wyczerpującą listę tych technologii, opis technologii, które są dostępne dla których rodzajów funkcji, wyjaśniono, co się dzieje w przypadku korzystania z każdej metody, i zawiera zalecenia dotyczące najlepszej metody do użycia w różnych scenariuszach . Różne narzędzia obsługujące wdrażanie w Azure Functions są dostrojone do właściwej technologii w oparciu o ich kontekst.

## <a name="deployment-technology-availability"></a>Dostępność technologii wdrażania

Azure Functions obsługuje Międzyplatformowe programowanie lokalne i hosting w systemach Windows i Linux. Obecnie dostępne są trzy plany hostingu:

+ [Wyrażon](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedykowane (App Service)](functions-scale.md#app-service-plan)

Każdy plan ma inne zachowania. Nie wszystkie technologie wdrażania są dostępne dla każdej wersji Azure Functions. Na poniższym wykresie przedstawiono, które technologie wdrażania są obsługiwane dla każdej kombinacji systemu operacyjnego i planu hostingu:

| Technologia wdrażania | Użycie systemu Windows | Windows Premium (wersja zapoznawcza) | Dedykowane systemu Windows  | Użycie systemu Linux (wersja zapoznawcza) | System Linux — dedykowany |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| Zewnętrzny adres URL pakietu<sup>1</sup> |✔|✔|✔|✔|✔|
| Wdróż plik zip |✔|✔|✔| |✔|
| Kontener platformy Docker | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| Kontrola źródła |✔|✔|✔| |✔|
| Lokalne git<sup>1</sup> |✔|✔|✔| |✔|
| Synchronizacja w chmurze<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Edytowanie portalu |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> technologia wdrażania, która wymaga [synchronizacji ręcznej wyzwalacza](#trigger-syncing).  
<sup>2</sup> edytowanie w portalu jest włączone tylko dla wyzwalaczy http i Timer dla funkcji w systemie Linux przy użyciu planów Premium i dedykowanych.

## <a name="key-concepts"></a>Kluczowe pojęcia

Niektóre kluczowe koncepcje mają na celu zrozumienie, w jaki sposób wdrożenia działają w Azure Functions.

### <a name="trigger-syncing"></a>Wyzwalanie synchronizacji

Po zmianie któregokolwiek z wyzwalaczy infrastruktura funkcji musi pamiętać o zmianach. Synchronizacja odbywa się automatycznie w przypadku wielu technologii wdrażania. Jednak w niektórych przypadkach należy ręcznie zsynchronizować wyzwalacze. Podczas wdrażania aktualizacji przez odwołanie się do zewnętrznego adresu URL pakietu, lokalnego narzędzia Git, synchronizacji w chmurze lub FTP należy ręcznie zsynchronizować wyzwalacze. Wyzwalacze można synchronizować na jeden z trzech sposobów:

* Uruchom ponownie aplikację funkcji w Azure Portal
* Wyślij żądanie HTTP POST, aby `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` użyć [klucza głównego](functions-bindings-http-webhook.md#authorization-keys).
* Wyślij żądanie HTTP POST do `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Zamień symbole zastępcze na identyfikator subskrypcji, nazwę grupy zasobów i nazwę aplikacji funkcji.

## <a name="deployment-technology-details"></a>Szczegóły technologii wdrażania 

W Azure Functions są dostępne następujące metody wdrażania.

### <a name="external-package-url"></a>Zewnętrzny adres URL pakietu

Możesz użyć zewnętrznego adresu URL pakietu, aby odwołać się do pliku pakietu zdalnego (zip), który zawiera aplikację funkcji. Plik zostanie pobrany z podanego adresu URL, a aplikacja zostanie uruchomiona w trybie [uruchamiania z poziomu pakietu](run-functions-from-deployment-package.md) .

>__Jak z niej korzystać:__ Dodaj `WEBSITE_RUN_FROM_PACKAGE` do ustawień aplikacji. Wartość tego ustawienia powinna być adresem URL (lokalizacją określonego pliku pakietu, który chcesz uruchomić). Możesz dodać ustawienia [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) lub za [pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Jeśli używasz usługi Azure Blob Storage, użyj prywatnego kontenera z sygnaturą [dostępu](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) współdzielonego, aby zapewnić funkcje dostępu do pakietu. Za każdym razem, gdy aplikacja zostanie ponownie uruchomiona, pobiera kopię zawartości. Twoje odwołanie musi być ważne przez okres istnienia aplikacji.

>__Kiedy używać go:__ Zewnętrzny adres URL pakietu to jedyna obsługiwana metoda wdrażania dla Azure Functions uruchamiana w systemie Linux w planie zużycia (wersja zapoznawcza). Gdy aktualizujesz plik pakietu, do którego odwołuje się aplikacja funkcji, musisz [ręcznie zsynchronizować wyzwalacze](#trigger-syncing) , aby poinformować platformę Azure, że Twoja aplikacja uległa zmianie.

### <a name="zip-deploy"></a>Wdróż plik zip

Użyj narzędzia zip Deploy, aby wypchnąć plik zip, który zawiera aplikację funkcji na platformie Azure. Opcjonalnie możesz ustawić aplikację tak, aby uruchamiała się w trybie [uruchamiania z poziomu pakietu](run-functions-from-deployment-package.md) .

>__Jak z niej korzystać:__ Wdróż przy użyciu ulubionego narzędzia klienckiego: [Vs Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)lub [interfejs wiersza polecenia platformy Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Aby ręcznie wdrożyć plik zip w aplikacji funkcji, postępuj zgodnie z instrukcjami w temacie [Deploy from a. zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Podczas wdrażania przy użyciu narzędzia zip Deploy można ustawić, aby aplikacja była uruchamiana w trybie [uruchamiania z poziomu pakietu](run-functions-from-deployment-package.md) . Aby ustawić przebieg z trybu pakietu, ustaw `WEBSITE_RUN_FROM_PACKAGE` wartość ustawienia aplikacji na. `1` Zalecamy wdrożenie pliku zip. Zapewnia ona krótszy czas ładowania aplikacji i jest to wartość domyślna dla VS Code, Visual Studio i interfejsu wiersza polecenia platformy Azure.

>__Kiedy używać go:__ Usługa zip Deploy to zalecana technologia wdrażania dla funkcji działających w systemach Windows i Linux w planie Premium lub dedykowanym.

### <a name="docker-container"></a>Kontener platformy Docker

Można wdrożyć obraz kontenera systemu Linux zawierający aplikację funkcji.

>__Jak z niej korzystać:__ Utwórz aplikację funkcji systemu Linux w planie Premium lub dedykowanym i określ, z którego obrazu kontenera chcesz korzystać. Można to zrobić na dwa sposoby:
>
>* Utwórz aplikację funkcji systemu Linux na planie Azure App Service w Azure Portal. W obszarze **Publikowanie**wybierz opcję **obraz platformy Docker**, a następnie skonfiguruj kontener. Wprowadź lokalizację, w której jest hostowany obraz.
>* Tworzenie aplikacji funkcji systemu Linux w planie App Service przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie funkcji w systemie Linux przy użyciu obrazu niestandardowego](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Aby wdrożyć w istniejącej aplikacji przy użyciu niestandardowego kontenera, w [Azure Functions Core Tools](functions-run-local.md)Użyj [`func deploy`](functions-run-local.md#publish) polecenia.

>__Kiedy używać go:__ Użyj opcji kontenera Docker, gdy potrzebna jest większa kontrola nad środowiskiem systemu Linux, w której działa aplikacja funkcji. Ten mechanizm wdrażania jest dostępny tylko w przypadku funkcji działających w systemie Linux w planie App Service.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy pakiety i wdraża aplikacje systemu Windows na dowolnym serwerze usług IIS, w tym aplikacje funkcji działające w systemie Windows na platformie Azure.

>__Jak z niej korzystać:__ Użyj [narzędzi Visual Studio Tools for Azure Functions](functions-create-your-first-function-visual-studio.md). Wyczyść pole wyboru **Uruchom z pliku pakietu (zalecane)** .
>
>Możesz również pobrać [Web Deploy 3,6](https://www.iis.net/downloads/microsoft/web-deploy) i bezpośrednio wywoływać `MSDeploy.exe` .

>__Kiedy używać go:__ Web Deploy jest obsługiwana i nie ma żadnych problemów, ale preferowany mechanizm to polecenie [zip Deploy z włączonym pakietem uruchamiania z pakietu](#zip-deploy). Aby dowiedzieć się więcej, zobacz [Przewodnik programowania w programie Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Kontrola źródła

Użyj kontroli źródła, aby połączyć aplikację funkcji z repozytorium git. Aktualizacja kodu w tym repozytorium wyzwala wdrożenie. Aby uzyskać więcej informacji, zobacz witrynę [typu wiki kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak z niej korzystać:__ Użyj centrum wdrażania w obszarze funkcje portalu, aby skonfigurować publikowanie z kontroli źródła. Aby uzyskać więcej informacji, zobacz [wdrażanie ciągłe dla Azure Functions](functions-continuous-deployment.md).

>__Kiedy używać go:__ Użycie kontroli źródła jest najlepszym rozwiązaniem dla zespołów, które współpracują ze swoimi aplikacjami funkcji. Kontrola źródła jest dobrą opcją wdrażania, która umożliwia bardziej zaawansowane potoki wdrażania.

### <a name="local-git"></a>Lokalna usługa Git

Możesz użyć lokalnego narzędzia Git do wypychania kodu z komputera lokalnego do Azure Functions przy użyciu narzędzia Git.

>__Jak z niej korzystać:__ Postępuj zgodnie z instrukcjami w [lokalnym wdrożeniu git, aby Azure App Service](../app-service/deploy-local-git.md).

>__Kiedy używać go:__ Ogólnie rzecz biorąc zalecamy użycie innej metody wdrażania. W przypadku publikowania z poziomu lokalnego narzędzia Git należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizacja z chmurą

Zsynchronizuj zawartość z usługi Dropbox i OneDrive do Azure Functions za pomocą synchronizacji z chmurą.

>__Jak z niej korzystać:__ Postępuj zgodnie z instrukcjami w temacie [synchronizowanie zawartości z folderu w chmurze](../app-service/deploy-content-sync.md).

>__Kiedy używać go:__ Ogólnie rzecz biorąc, zalecamy stosowanie innych metod wdrażania. W przypadku publikowania przy użyciu funkcji synchronizacji z chmurą należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="ftp"></a>Protokół FTP

Za pomocą protokołu FTP można bezpośrednio przetransferować pliki do Azure Functions.

>__Jak z niej korzystać:__ Postępuj zgodnie z instrukcjami podanymi w temacie [wdrażanie zawartości przy użyciu protokołu FTP/s](../app-service/deploy-ftp.md).

>__Kiedy używać go:__ Ogólnie rzecz biorąc, zalecamy stosowanie innych metod wdrażania. W przypadku publikowania przy użyciu protokołu FTP należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="portal-editing"></a>Edytowanie portalu

W edytorze opartym na portalu można bezpośrednio edytować pliki znajdujące się w aplikacji funkcji (zasadniczo wdrażane za każdym razem, gdy zapisujesz zmiany).

>__Jak z niej korzystać:__ Aby móc edytować funkcje w Azure Portal, musisz [utworzyć funkcje w portalu](functions-create-first-azure-function.md). Aby zachować pojedyncze Źródło prawdy, przy użyciu dowolnej innej metody wdrażania funkcja jest tylko do odczytu i zapobiega edytowaniu portalu. Aby powrócić do stanu, w którym można edytować pliki w Azure Portal, można ręcznie włączyć tryb edycji z powrotem do `Read/Write` i usunąć wszystkie ustawienia aplikacji powiązane z wdrożeniem (na przykład `WEBSITE_RUN_FROM_PACKAGE`). 

>__Kiedy używać go:__ Portal jest dobrym sposobem na rozpoczęcie pracy z Azure Functions. Aby uzyskać więcej intensywnych prac programistycznych, zalecamy użycie jednego z następujących narzędzi klienta:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (wiersz polecenia)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

W poniższej tabeli przedstawiono systemy operacyjne i języki obsługujące edycję portalu:

| | Użycie systemu Windows | Windows Premium (wersja zapoznawcza) | Dedykowane systemu Windows | Użycie systemu Linux (wersja zapoznawcza) | Linux Premium (wersja zapoznawcza)| System Linux — dedykowany |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| C#Napisy |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (wersja zapoznawcza) | | | | | | |
| PowerShell (wersja zapoznawcza) |✔|✔|✔| | | |
| TypeScript (Node. js) | | | | | | |

<sup>*</sup>Edytowanie portalu jest włączone tylko dla wyzwalaczy HTTP i Timer dla funkcji w systemie Linux przy użyciu Premium i planów dedykowanych.

## <a name="deployment-slots"></a>Miejsca wdrożenia

Po wdrożeniu aplikacji funkcji na platformie Azure można wdrożyć ją w osobnym miejscu wdrożenia zamiast bezpośrednio wdrażać ją w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat miejsc wdrożenia, zobacz [Azure App Service miejsc](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Poziomy obsługi miejsc wdrożenia

Istnieją dwa poziomy wsparcia dla miejsc wdrożenia:

* **Ogólna dostępność (ga)** : W pełni obsługiwane i zatwierdzone do użycia w środowisku produkcyjnym.
* **Wersja**zapoznawcza: Nie jest jeszcze obsługiwane, ale oczekuje się, że w przyszłości zostanie osiągnięty stan GA.

| System operacyjny/plan hostingu | Poziom pomocy technicznej |
| --------------- | ------ |
| Użycie systemu Windows | Wersja zapoznawcza |
| Windows Premium (wersja zapoznawcza) | Wersja zapoznawcza |
| Dedykowane systemu Windows | Ogólna dostępność |
| Użycie systemu Linux | Nieobsługiwane |
| Linux Premium (wersja zapoznawcza) | Wersja zapoznawcza |
| System Linux — dedykowany | Ogólna dostępność |

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj te artykuły, aby dowiedzieć się więcej o wdrażaniu aplikacji funkcji: 

+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)
+ [Ciągłe dostarczanie za pomocą usługi Azure DevOps](functions-how-to-azure-devops.md)
+ [Wdrożenia ZIP dla Azure Functions](deployment-zip-push.md)
+ [Uruchamianie Azure Functions z pliku pakietu](run-functions-from-deployment-package.md)
+ [Automatyzowanie wdrażania zasobów dla aplikacji funkcji w Azure Functions](functions-infrastructure-as-code.md)

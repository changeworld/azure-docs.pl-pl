---
title: Technologie wdrażania w usłudze Azure Functions
description: Dowiedz się, jak wdrożyć kod w usłudze Azure Functions.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277130"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie wdrażania w usłudze Azure Functions

Można użyć kilku różnych technologii, aby wdrożyć kod projektu usługi Azure Functions na platformie Azure. Ten artykuł zawiera wyczerpującą listę tych technologii, opisuje, które technologie są dostępne dla których smaków funkcji, wyjaśnia, co się dzieje, gdy używasz każdej metody, i zawiera zalecenia dotyczące najlepszej metody do użycia w różnych scenariuszach . Różne narzędzia, które obsługują wdrażanie w usłudze Azure Functions są dostrojone do odpowiedniej technologii na podstawie ich kontekstu. Ogólnie rzecz biorąc wdrożenie zip jest zalecana technologia wdrażania dla usługi Azure Functions.

## <a name="deployment-technology-availability"></a>Dostępność technologii wdrażania

Usługa Azure Functions obsługuje wieloplatformowe lokalne programowanie i hosting w systemach Windows i Linux. Obecnie dostępne są trzy plany hostingowe:

+ [Zużycie](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedykowane (usługa aplikacji)](functions-scale.md#app-service-plan)

Każdy plan ma różne zachowania. Nie wszystkie technologie wdrażania są dostępne dla każdego smaku usługi Azure Functions. Na poniższym wykresie pokazano, które technologie wdrażania są obsługiwane dla każdej kombinacji systemu operacyjnego i planu hostingu:

| Technologia wdrażania | Zużycie systemu Windows | Windows Premium | Dedykowany system Windows  | Zużycie Linuksa | Linux Premium | Linux dedykowany |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Adres URL pakietu zewnętrznego<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Wdrażanie na zamek błyskawiczny |✔|✔|✔|✔|✔|✔|
| Kontener platformy Docker | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Kontrola źródła |✔|✔|✔| |✔|✔|
| Lokalny Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Synchronizacja w<sup>chmurze 1</sup> |✔|✔|✔| |✔|✔|
| Ftp<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edycja portalu |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Technologia wdrażania, która wymaga [ręcznej synchronizacji wyzwalacza](#trigger-syncing).  
<sup>2</sup> Edycja portalu jest włączona tylko dla wyzwalaczy HTTP i Timer dla funkcji w systemie Linux przy użyciu planów premium i dedykowanych.

## <a name="key-concepts"></a>Kluczowe pojęcia

Niektóre kluczowe pojęcia mają kluczowe znaczenie dla zrozumienia, jak działają wdrożenia w usłudze Azure Functions.

### <a name="trigger-syncing"></a>Synchronizacja wyzwalacza

Po zmianie dowolnego z wyzwalaczy, functions infrastruktury musi być świadomy zmian. Synchronizacja odbywa się automatycznie dla wielu technologii wdrażania. Jednak w niektórych przypadkach należy ręcznie zsynchronizować wyzwalacze. Podczas wdrażania aktualizacji przez odwoływanie się do zewnętrznego adresu URL pakietu, lokalnego Gita, synchronizacji w chmurze lub FTP, musisz ręcznie zsynchronizować wyzwalacze. Wyzwalacze można synchronizować na jeden z trzech sposobów:

* Ponowne uruchamianie aplikacji funkcji w witrynie Azure portal
* Wyślij żądanie HTTP `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` POST do użycia [klucza głównego](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Wyślij żądanie HTTP `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`POST do pliku . Zastąp symbole zastępcze identyfikatorem subskrypcji, nazwą grupy zasobów i nazwą aplikacji funkcyjnej.

### <a name="remote-build"></a>Kompilacja zdalna

Usługa Azure Functions może automatycznie wykonywać kompilacje na kod, który otrzymuje po wdrożeniach zip. Te kompilacje zachowują się nieco inaczej w zależności od tego, czy aplikacja jest uruchomiona w systemie Windows czy Linux. Kompilacje zdalne nie są wykonywane, gdy aplikacja została wcześniej ustawiona na uruchamianie w trybie [Uruchom z pakietu.](run-functions-from-deployment-package.md) Aby dowiedzieć się, jak używać kompilacji zdalnej, przejdź do [wdrożenia w zip](#zip-deploy).

> [!NOTE]
> Jeśli masz problemy z kompilacją zdalną, może to być spowodowane tym, że aplikacja została utworzona przed udostępnieniem tej funkcji (1 sierpnia 2019 r.). Spróbuj utworzyć nową aplikację funkcji `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` lub uruchomić, aby zaktualizować aplikację funkcji. To polecenie może potrwać dwie próby pomyślne.

#### <a name="remote-build-on-windows"></a>Zdalna kompilacja w systemie Windows

Wszystkie aplikacje funkcyjne działające w systemie Windows mają małą aplikację do zarządzania, witrynę SCM (lub [Kudu).](https://github.com/projectkudu/kudu) Ta witryna obsługuje większość logiki wdrażania i kompilacji dla usługi Azure Functions.

Po wdrożeniu aplikacji w systemie Windows są `dotnet restore` uruchamiane polecenia `npm install` specyficzne dla języka, takie jak (C#) lub (JavaScript).

#### <a name="remote-build-on-linux"></a>Zdalna kompilacja na Linuksie

Aby włączyć zdalną kompilację w systemie Linux, należy ustawić następujące [ustawienia aplikacji:](functions-how-to-use-azure-function-app-settings.md#settings)

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Domyślnie zarówno [narzędzia podstawowe usług Azure Functions,](functions-run-local.md) jak i rozszerzenie usług Azure Functions dla kodu programu Visual [Studio](functions-create-first-function-vs-code.md#publish-the-project-to-azure) wykonują kompilacje zdalne podczas wdrażania w systemie Linux. Z tego powodu oba narzędzia automatycznie tworzą te ustawienia na platformie Azure. 

Gdy aplikacje są tworzone zdalnie w systemie Linux, [działają z pakietu wdrożeniowego](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Plan Zużycie

Aplikacje funkcji systemu Linux działające w planie zużycia nie mają witryny SCM/Kudu, co ogranicza opcje wdrażania. Jednak aplikacje funkcji w systemie Linux działające w planie zużycia obsługują kompilacje zdalne.

##### <a name="dedicated-and-premium-plans"></a>Dedykowane i premium plany

Aplikacje funkcyjne działające na linuksie w [planie Dedykowana (Usługa aplikacji)](functions-scale.md#app-service-plan) i [plan Premium](functions-scale.md#premium-plan) mają również ograniczoną witrynę SCM/Kudu.

## <a name="deployment-technology-details"></a>Szczegóły dotyczące technologii wdrażania

Następujące metody wdrażania są dostępne w usłudze Azure Functions.

### <a name="external-package-url"></a>Adres URL pakietu zewnętrznego

Za pomocą zewnętrznego adresu URL pakietu można odwołać się do pliku pakietu zdalnego (zip), który zawiera aplikację funkcji. Plik jest pobierany z podanego adresu URL, a aplikacja działa w trybie [Uruchom z pakietu.](run-functions-from-deployment-package.md)

>__Jak go używać:__ Dodaj `WEBSITE_RUN_FROM_PACKAGE` do ustawień aplikacji. Wartość tego ustawienia powinna być adresem URL (lokalizacją określonego pliku pakietu, który chcesz uruchomić). Ustawienia można dodawać [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) lub za pomocą interfejsu [wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Jeśli używasz magazynu obiektów Blob platformy Azure, użyj prywatnego kontenera z [podpisem dostępu współdzielonego (SAS),](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) aby nadać funkcji dostęp do pakietu. Za każdym razem, gdy aplikacja zostanie ponownie uruchomiona, pobiera kopię zawartości. Odwołanie musi być prawidłowe przez cały okres istnienia aplikacji.

>__Kiedy go używać:__ Adres URL pakietu zewnętrznego jest jedyną obsługiwaną metodą wdrażania usługi Azure Functions działającą w systemie Linux w planie zużycia, jeśli użytkownik nie chce, aby [wystąpiła kompilacja zdalna.](#remote-build) Po zaktualizowaniu pliku pakietu, do którego odwołuje się aplikacja funkcji, należy [ręcznie zsynchronizować wyzwalacze,](#trigger-syncing) aby poinformować platformę Azure, że aplikacja została zmieniona.

### <a name="zip-deploy"></a>Wdrażanie na zamek błyskawiczny

Użyj zip deploy do wypychania pliku zip, który zawiera aplikację funkcji na platformę Azure. Opcjonalnie można ustawić aplikację, aby rozpocząć [uruchamianie z pakietu](run-functions-from-deployment-package.md)lub określić, że nastąpi [kompilacja zdalna.](#remote-build)

>__Jak go używać:__ Wdrażanie przy użyciu ulubionego narzędzia klienckiego: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio](functions-develop-vs.md#publish-to-azure)lub z wiersza polecenia przy użyciu narzędzi podstawowe usług [Azure Functions](functions-run-local.md#project-file-deployment). Domyślnie te narzędzia używają wdrożenia zip i [uruchamiają z pakietu](run-functions-from-deployment-package.md). Podstawowe narzędzia i rozszerzenie kodu programu Visual Studio umożliwiają [zdalną kompilację](#remote-build) podczas wdrażania w systemie Linux. Aby ręcznie wdrożyć plik zip w aplikacji funkcyjnej, postępuj zgodnie z instrukcjami zawartymi w [polu Wdrażanie z pliku zip lub adresu URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Podczas wdrażania przy użyciu zip deploy, można ustawić aplikację do [uruchamiania z pakietu](run-functions-from-deployment-package.md). Aby uruchomić z pakietu, ustaw wartość ustawienia `WEBSITE_RUN_FROM_PACKAGE` aplikacji na `1`. Zalecamy wdrożenie zip. Daje krótszy czas ładowania dla aplikacji i jest domyślny dla programu VS Code, Visual Studio i interfejsu wiersza polecenia platformy Azure. 

>__Kiedy go używać:__ Zip deploy to zalecana technologia wdrażania usługi Azure Functions.

### <a name="docker-container"></a>Kontener platformy Docker

Można wdrożyć obraz kontenera systemu Linux, który zawiera aplikację funkcji.

>__Jak go używać:__ Utwórz aplikację funkcji systemu Linux w planie Premium lub Dedykowane i określ, z którego obrazu kontenera ma być uruchamiany. Możesz to zrobić na dwa sposoby:
>
>* Utwórz aplikację funkcji systemu Linux w planie usługi Azure App Service w witrynie Azure portal. W obszarze **Publikuj**wybierz pozycję **Obraz platformy Docker**, a następnie skonfiguruj kontener. Wprowadź lokalizację, w której znajduje się obraz.
>* Utwórz aplikację funkcji systemu Linux w planie usługi App Service przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Aby wdrożyć w istniejącej aplikacji przy użyciu kontenera niestandardowego, w [narzędziach podstawowe usług Azure Functions](functions-run-local.md), użyj [`func deploy`](functions-run-local.md#publish) polecenia.

>__Kiedy go używać:__ Użyj opcji kontenera platformy Docker, gdy potrzebujesz większej kontroli nad środowiskiem systemu Linux, w którym działa aplikacja funkcji. Ten mechanizm wdrażania jest dostępny tylko dla funkcji uruchomionych w systemie Linux.

### <a name="web-deploy-msdeploy"></a>Wdrażanie w sieci Web (MSDeploy)

Wdrażanie pakietów w sieci Web i wdraża aplikacje systemu Windows na dowolnym serwerze usług IIS, w tym w aplikacjach funkcji uruchomionych w systemie Windows na platformie Azure.

>__Jak go używać:__ Użyj [narzędzi programu Visual Studio dla usług Azure Functions](functions-create-your-first-function-visual-studio.md). Wyczyść pole wyboru **Uruchom z pliku pakietu (zalecane).**
>
>Można również pobrać [web deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) i wywołać `MSDeploy.exe` bezpośrednio.

>__Kiedy go używać:__ Wdrażanie w sieci Web jest obsługiwane i nie ma problemów, ale preferowanym mechanizmem jest [zip deploy z włączoną funkcją Uruchom z pakietu](#zip-deploy). Aby dowiedzieć się więcej, zobacz [przewodnik po programach Visual Studio rozwoju](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Kontrola źródła

Użyj formantu źródła, aby połączyć aplikację funkcji z repozytorium Git. Aktualizacja kodu w tym repozytorium wyzwala wdrożenie. Aby uzyskać więcej informacji, zobacz [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak go używać:__ Użyj Centrum wdrażania w obszarze Funkcje portalu, aby skonfigurować publikowanie z formantu źródłowego. Aby uzyskać więcej informacji, zobacz [Ciągłe wdrażanie usług Azure Functions](functions-continuous-deployment.md).

>__Kiedy go używać:__ Korzystanie z kontroli źródła jest najlepszym rozwiązaniem dla zespołów, które współpracują nad ich aplikacji funkcji. Kontrola źródła jest dobrą opcją wdrażania, która umożliwia bardziej zaawansowane potoki wdrażania.

### <a name="local-git"></a>Lokalna usługa Git

Za pomocą lokalnego gita można wypychać kod z komputera lokalnego do usługi Azure Functions przy użyciu git.

>__Jak go używać:__ Postępuj zgodnie z instrukcjami w [lokalnym wdrażaniu git do usługi Azure App Service](../app-service/deploy-local-git.md).

>__Kiedy go używać:__ Ogólnie rzecz biorąc zaleca się użycie innej metody wdrażania. Podczas publikowania z lokalnego Git, należy [ręcznie synchronizować wyzwalacze](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizacja w chmurze

Synchronizacja w chmurze umożliwia synchronizację zawartości z Dropbox i usługi OneDrive z usługami Azure.

>__Jak go używać:__ Postępuj zgodnie z instrukcjami w [synchronizacji zawartości z folderu w chmurze](../app-service/deploy-content-sync.md).

>__Kiedy go używać:__ Ogólnie rzecz biorąc zaleca się inne metody wdrażania. Podczas publikowania przy użyciu synchronizacji w chmurze należy [ręcznie synchronizować wyzwalacze](#trigger-syncing).

### <a name="ftp"></a>FTP

Za pomocą protokołu FTP można bezpośrednio przesyłać pliki do usługi Azure Functions.

>__Jak go używać:__ Postępuj zgodnie z instrukcjami w [deploy zawartości przy użyciu FTP/s](../app-service/deploy-ftp.md).

>__Kiedy go używać:__ Ogólnie rzecz biorąc zaleca się inne metody wdrażania. Podczas publikowania za pomocą protokołu FTP należy [ręcznie synchronizować wyzwalacze](#trigger-syncing).

### <a name="portal-editing"></a>Edycja portalu

W edytorze opartym na portalu można bezpośrednio edytować pliki, które znajdują się w aplikacji funkcji (zasadniczo wdrażania za każdym razem, gdy można zapisać zmiany).

>__Jak go używać:__ Aby móc edytować funkcje w witrynie Azure portal, musisz [utworzyć funkcje w portalu](functions-create-first-azure-function.md). Aby zachować jedno źródło prawdy, przy użyciu innej metody wdrażania sprawia, że funkcja jest tylko do odczytu i zapobiega ciągłej edycji portalu. Aby powrócić do stanu, w którym można edytować pliki w witrynie Azure `Read/Write` Portal, można ręcznie włączyć tryb `WEBSITE_RUN_FROM_PACKAGE`edycji z powrotem do i usunąć wszystkie ustawienia aplikacji związane z wdrażaniem (np. ). 

>__Kiedy go używać:__ Portal to dobry sposób na rozpoczęcie pracy z usługą Azure Functions. Aby uzyskać bardziej intensywne prace rozwojowe, zaleca się użycie jednego z następujących narzędzi klienta:
>
>* [Kod programu Visual Studio](functions-create-first-function-vs-code.md)
>* [Podstawowe narzędzia azure functions (wiersz polecenia)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

W poniższej tabeli przedstawiono systemy operacyjne i języki obsługujące edycję portalu:

| | Zużycie systemu Windows | Windows Premium | Dedykowany system Windows | Zużycie Linuksa | Linux Premium | Linux dedykowany |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| Skrypt języka C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (wersja zapoznawcza) | | | | | | |
| PowerShell (wersja zapoznawcza) |✔|✔|✔| | | |
| Kod TypeScript (node.js) | | | | | | |

<sup>*</sup>Edycja portalu jest włączona tylko dla wyzwalaczy HTTP i Timer dla funkcji w systemie Linux przy użyciu planów premium i dedykowanych.

## <a name="deployment-slots"></a>Miejsca wdrożenia

Podczas wdrażania aplikacji funkcji na platformie Azure, można wdrożyć do oddzielnego gniazda wdrażania zamiast bezpośrednio do produkcji. Aby uzyskać więcej informacji na temat gniazd wdrażania, zobacz dokumentację [slotów wdrażania usług Azure Functions, aby](../app-service/deploy-staging-slots.md) uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Następne kroki

Przeczytaj te artykuły, aby dowiedzieć się więcej o wdrażaniu aplikacji funkcji: 

+ [Ciągłe wdrażanie dla usług Azure Functions](functions-continuous-deployment.md)
+ [Ciągłe dostarczanie przy użyciu usługi Azure DevOps](functions-how-to-azure-devops.md)
+ [Wdrożenia zip dla usług Azure Functions](deployment-zip-push.md)
+ [Uruchamianie usługi Azure Functions z pliku pakietu](run-functions-from-deployment-package.md)
+ [Automatyzacja wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions](functions-infrastructure-as-code.md)

---
title: Technologie wdrażania w usłudze Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się, różne sposoby, możesz wdrożyć kod do usługi Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594393"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie wdrażania w usłudze Azure Functions

Aby wdrożyć kod projektu funkcji platformy Azure do platformy Azure, można użyć kilku różnych technologii. Ten artykuł zawiera wyczerpujący wykaz tych technologii, w tym artykule opisano, technologii, które są dostępne dla których odmian funkcje, wyjaśnia, co się stanie, gdy używasz każdej metody i zawiera zalecenia dotyczące najlepszą metodę do użycia w różnych scenariuszach . Różne narzędzia, które obsługują wdrażanie do usługi Azure Functions jest dostosowana do odpowiedniej technologii, na podstawie ich kontekstu.

## <a name="deployment-technology-availability"></a>Wdrażanie technologii dostępności

> [!IMPORTANT]
> Usługa Azure Functions obsługuje lokalne programowanie dla wielu platform i hosting w systemie Windows i Linux. Obecnie są dostępne trzy plany hostowania: [Użycie](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), i [(usługa Azure App Service) w wersji dedykowanej](functions-scale.md#app-service-plan). Każdy plan zawiera różne zachowania. Nie wszystkie technologie wdrażania są dostępne dla każdej wersji usługi Azure Functions.

| Technologia wdrażania | Użycie Windows | Windows — wersja Premium (wersja zapoznawcza) | Windows w wersji dedykowanej  | Użycie systemu Linux (wersja zapoznawcza) | Linux w wersji dedykowanej |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| Adres URL pakietu zewnętrznego<sup>1</sup> |✔|✔|✔|✔|✔|
| Wdrażanie pliku zip |✔|✔|✔| |✔|
| Kontener platformy docker | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| Kontrola źródła |✔|✔|✔| |✔|
| Lokalnego narzędzia Git<sup>1</sup> |✔|✔|✔| |✔|
| Synchronizacja w chmurze<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Edytowanie w portalu |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> technologii wdrażania, która wymaga [wyzwalacza ręcznego synchronizowania](#trigger-syncing).  
<sup>2</sup> edycji portal jest włączona tylko w przypadku wyzwalaczy HTTP oraz czasomierzem dla funkcji w systemie Linux za pomocą dedykowanego planu.

## <a name="key-concepts"></a>Kluczowe pojęcia

Niektóre kluczowe pojęcia są krytyczne dla zrozumienia, jak działają wdrożeń w usłudze Azure Functions.

### <a name="trigger-syncing"></a>Trwa synchronizowanie wyzwalacza

Po zmianie dowolnego usługi wyzwalaczy infrastruktury funkcji należy pamiętać o zmianach. Synchronizacja odbywa się automatycznie w przypadku wielu technologii wdrożenia. Jednak w niektórych przypadkach należy ręcznie zsynchronizować usługi wyzwalaczy. Wdrażając aktualizacje, odwołując się do pakietu zewnętrznego adresu URL, lokalnego narzędzia Git, synchronizacji w chmurze lub FTP, możesz ręcznie zsynchronizować usługi wyzwalaczy. Można synchronizować wyzwalaczy w jeden z trzech sposobów:

* Uruchom ponownie swoją aplikację funkcji w witrynie Azure portal
* Wyślij żądanie HTTP POST do `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` przy użyciu [klucz główny](functions-bindings-http-webhook.md#authorization-keys).
* Wyślij żądanie HTTP POST do `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Zastąp symbole zastępcze Identyfikatora subskrypcji, nazwę grupy zasobów i nazwę aplikacji funkcji.

## <a name="deployment-technology-details"></a>Szczegóły technologii wdrożenia 

Poniższych metod wdrażania są dostępne w usłudze Azure Functions.

### <a name="external-package-url"></a>Adres URL zewnętrznego pakietu

Adres URL pakietu zewnętrznego służy odwołać się do pliku zdalnego pakietu (zip), który zawiera aplikację funkcji. Plik jest pobierany z podanego adresu URL, a aplikacja jest uruchamiana [uruchomienia z pakietu](run-functions-from-deployment-package.md) trybu.

>__Jak z niego korzystać:__ Dodaj `WEBSITE_RUN_FROM_PACKAGE` w ustawieniach Twojej aplikacji. Wartość tego ustawienia należy adres URL (lokalizacja pliku określonego pakietu, który chcesz uruchomić). Możesz dodać ustawienia albo [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) lub [przy użyciu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Jeśli używasz usługi Azure Blob storage, należy użyć kontenera prywatnych przy użyciu [sygnatury dostępu współdzielonego (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) do udostępnienia funkcji do pakietu. Dowolny ponownym uruchomieniu aplikacji, narzędzie pobiera informacje o kopia zawartości. Okres istnienia aplikacji, której można się odwołać musi być prawidłowy.

>__Kiedy stosować:__ Adres URL pakietów zewnętrznych jest tylko obsługiwanej metody wdrażania dla usługi Azure Functions działającej w systemie Linux w ramach planu zużycie (wersja zapoznawcza). Podczas aktualizacji pliku pakietu, który odwołuje się do aplikacji funkcji, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing) Azure stwierdzić, że aplikacja została zmieniona.

### <a name="zip-deploy"></a>Wdrażanie pliku zip

Użyj pliku zip wdrożyć Wypchnij plik zip, który zawiera aplikację funkcji na platformie Azure. Opcjonalnie można ustawić aplikację do uruchamiania w [uruchomienia z pakietu](run-functions-from-deployment-package.md) trybu.

>__Jak z niego korzystać:__ Wdrażanie przy użyciu narzędzia do ulubionego klienta: [Program VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [programu Visual Studio](functions-develop-vs.md#publish-to-azure), lub [wiersza polecenia platformy Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Aby ręcznie wdrożyć plik zip do aplikacji funkcji, postępuj zgodnie z instrukcjami [Wdróż z pliku zip lub adres URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>W przypadku wdrażania przy użyciu pliku zip wdrażania, możesz ustawić aplikacja ma działać [uruchomienia z pakietu](run-functions-from-deployment-package.md) trybu. Aby ustawić tryb uruchomienia z pakietu, należy ustawić `WEBSITE_RUN_FROM_PACKAGE` wartość ustawienia aplikacji do `1`. Firma Microsoft zaleca wdrażanie pliku zip. Daje krótszy czas ładowania aplikacji, a jest ustawieniem domyślnym dla programu VS Code, program Visual Studio i wiersza polecenia platformy Azure.

>__Kiedy stosować:__ Wdrażanie pliku zip jest technologia zalecane wdrożenie dla usługi Azure Functions w systemie Windows i usługi Azure Functions działającej w systemie Linux w przypadku dedykowanego planu.

### <a name="docker-container"></a>Kontener platformy docker

Można wdrożyć obraz kontenera systemu Linux, który zawiera aplikację funkcji.

>__Jak z niego korzystać:__ Tworzenie aplikacji funkcji systemu Linux w przypadku dedykowanego planu i określić, który obraz kontenera, aby uruchamiała się z. Można to zrobić na dwa sposoby:
>
>* Tworzenie aplikacji funkcji systemu Linux na plan usługi Azure App Service w witrynie Azure portal. Dla **Publikuj**, wybierz opcję **obrazu platformy Docker**, a następnie skonfiguruj kontenera. Wprowadź lokalizację, w którym jest hostowany obraz.
>* Tworzenie aplikacji funkcji systemu Linux na plan usługi App Service przy użyciu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej, zobacz temat [Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Aby wdrożyć do istniejącej aplikacji przy użyciu niestandardowego kontenera w [podstawowych narzędzi usługi Azure Functions](functions-run-local.md), użyj [ `func deploy` ](functions-run-local.md#publish) polecenia.

>__Kiedy stosować:__ Użyj opcji kontenera platformy Docker, gdy potrzebujesz większej kontroli nad środowiskiem systemu Linux której działa aplikacja funkcji. Ten mechanizm wdrażania jest dostępna tylko w przypadku funkcji działającej w systemie Linux w ramach planu usługi App Service.

### <a name="web-deploy-msdeploy"></a>Narzędzie Web Deploy (MSDeploy)

Narzędzie Web Deploy pakietów i wdrożenie aplikacji Windows na dowolnym serwerze usług IIS, w tym Twoje aplikacje funkcji z systemem Windows na platformie Azure.

>__Jak z niego korzystać:__ Użyj [narzędzi programu Visual Studio dla usługi Azure Functions](functions-create-your-first-function-visual-studio.md). Wyczyść **uruchamiania z pliku pakietu (zalecane)** pole wyboru.
>
>Możesz również pobrać [3.6 wdrażania sieci Web](https://www.iis.net/downloads/microsoft/web-deploy) i wywołać `MSDeploy.exe` bezpośrednio.

>__Kiedy stosować:__ Narzędzie Web Deploy jest obsługiwane i nie ma żadnych problemów, ale jest to preferowany sposób [zip wdrażania z uruchamiania z pakietem włączone](#zip-deploy). Aby dowiedzieć się więcej, zobacz [Podręcznik programowania Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Kontrola źródła

Za pomocą kontroli źródła, aby połączyć aplikację funkcji z repozytorium Git. Aktualizacja do kodu, w tym repozytorium wyzwala wdrożenia. Aby uzyskać więcej informacji, zobacz [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak z niego korzystać:__ Aby skonfigurować publikowanie z kontroli źródła, należy użyć Centrum wdrażania w portalu usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md).

>__Kiedy stosować:__ Przy użyciu kontroli źródła jest najlepszym rozwiązaniem dla zespołów, które współpracować nad ich aplikacji funkcji. Kontrola źródła jest opcji dobre wdrażania, która umożliwia bardziej złożone potoki wdrożenia.

### <a name="local-git"></a>Lokalna usługa Git

Można użyć lokalnego narzędzia Git do wypychania kodu z komputera lokalnego do usługi Azure Functions przy użyciu narzędzia Git.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami w [wdrażanie lokalnej usługi Git w usłudze Azure App Service](../app-service/deploy-local-git.md).

>__Kiedy stosować:__ Ogólnie rzecz biorąc zaleca się użycie metody innego wdrożenia. Podczas publikowania z lokalnego repozytorium Git, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizacji chmury

Użycie chmury synchronizacji — Synchronizuj zawartość z usługi Dropbox i OneDrive do usługi Azure Functions.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami w [Synchronizuj zawartość z folderu w chmurze](../app-service/deploy-content-sync.md).

>__Kiedy stosować:__ Ogólnie rzecz biorąc zaleca się innych metod wdrażania. Podczas publikowania za pomocą synchronizacji w chmurze, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing).

### <a name="ftp"></a>FTP

FTP umożliwia bezpośredni transfer plików w usłudze Azure Functions.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami w [wdrażania zawartości przy użyciu protokołu FTP/s](../app-service/deploy-ftp.md).

>__Kiedy stosować:__ Ogólnie rzecz biorąc zaleca się innych metod wdrażania. Podczas publikowania za pomocą protokołu FTP, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing).

### <a name="portal-editing"></a>Edytowanie w portalu

W edytorze oparte na portalu, można bezpośrednio edytować pliki, które znajdują się w aplikacji funkcji (zasadniczo wdrożenia za każdym razem, aby zapisać zmiany).

>__Jak z niego korzystać:__ Aby móc edytować funkcji w witrynie Azure portal, musisz mieć [utworzonej funkcji w portalu](functions-create-first-azure-function.md). Aby zachować pojedyncze wiarygodne źródło, za pomocą innej metody wdrażania sprawia, że funkcja tylko do odczytu i uniemożliwia dalsze edycję portalu. Aby powrócić do stanu, w którym można edytować pliki w witrynie Azure portal, można ręcznie włączyć tryb edycji do `Read/Write` i usuwania wszelkich ustawień związanych z wdrażaniem aplikacji (takich jak `WEBSITE_RUN_FROM_PACKAGE`). 

>__Kiedy stosować:__ Portal jest dobrym sposobem, aby rozpocząć pracę z usługą Azure Functions. Im bardziej intensywny prace deweloperskie zaleca się, że używasz klienta narzędzi:
>
>* [Rozpoczęcie korzystania z programu VS Code](functions-create-first-function-vs-code.md)
>* [Rozpoczęcie korzystania z podstawowych narzędzi usługi Azure Functions](functions-run-local.md)
>* [Rozpoczęcie korzystania z programu Visual Studio](functions-create-your-first-function-visual-studio.md)

W poniższej tabeli przedstawiono systemy operacyjne i języki tej obsługi portalu edycji:

| | Użycie Windows | Windows — wersja Premium (wersja zapoznawcza) | Windows w wersji dedykowanej | Użycie systemu Linux (wersja zapoznawcza) | Linux w wersji dedykowanej |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C# Script |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (wersja zapoznawcza) | | | | | |
| PowerShell (wersja zapoznawcza) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> Edytowanie portalu jest włączona tylko dla protokołu HTTP i czasomierza wyzwala dla funkcji w systemie Linux za pomocą dedykowanego planu.

## <a name="deployment-slots"></a>Miejsca wdrożenia

Gdy wdrażasz aplikację funkcji na platformie Azure, można wdrożyć w miejscu wdrażane pojedynczo, a nie bezpośrednio do wdrożenia produkcyjnego. Aby uzyskać więcej informacji na temat miejsc wdrożenia, zobacz [miejsc w usłudze Azure App Service](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Pomoc techniczną na poziomach miejsca wdrożenia

Istnieją dwa poziomy wsparcia dla miejsc wdrożenia:

* **Ogólna dostępność (GA)** : W pełni obsługiwane i zatwierdzone do użytku produkcyjnego.
* **Podgląd**: Nie są jeszcze obsługiwane, ale oczekuje się, że osiągną stan wersji ogólnie dostępnej w przyszłości.

| Plan hostingu/OS | Poziom wsparcia |
| --------------- | ------ |
| Użycie Windows | Wersja zapoznawcza |
| Windows — wersja Premium (wersja zapoznawcza) | Wersja zapoznawcza |
| Windows w wersji dedykowanej | Ogólna dostępność |
| Użycie systemu Linux | Nieobsługiwane |
| Linux w wersji dedykowanej | Ogólna dostępność |

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły, aby dowiedzieć się więcej na temat wdrażania aplikacji funkcji: 

+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)
+ [Ciągłe dostarczanie za pomocą DevOps platformy Azure](functions-how-to-azure-devops.md)
+ [ZIP wdrożenia dla usługi Azure Functions](deployment-zip-push.md)
+ [Uruchamianie usługi Azure Functions z pliku pakietu](run-functions-from-deployment-package.md)
+ [Automatyzowanie wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions](functions-infrastructure-as-code.md)

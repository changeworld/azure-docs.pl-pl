---
title: Technologie wdrażania w usłudze Azure Functions | Dokumentacja firmy Microsoft
description: Dowiedz się więcej i szczegółowymi informacjami dotyczącymi różnych sposobów, możesz wdrożyć kod do usługi Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 118daf02ab59646f2926071763aa4d7e97846e04
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508220"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie wdrażania w usłudze Azure Functions

Istnieje kilka różnych technologii, których można użyć, aby wdrożyć kod projektu usługi Azure Functions na platformie Azure. W tym artykule zawiera kompletną listę tych technologii, informuje o technologie, które są dostępne dla których odmian funkcje, wyjaśnia, co rzeczywiście dzieje się każdej z metod jest używany, gdy zawiera zalecenia dotyczące najlepszych metodę do różne scenariusze. Różne narzędzia, które obsługują wdrażanie do usługi Azure Functions jest dostosowana do odpowiedniej technologii, na podstawie ich kontekstu.

## <a name="deployment-technology-availability"></a>Wdrażanie technologii dostępności

> [!IMPORTANT]
> Usługi Azure Functions obsługuje cross lokalne programowanie na platformie i hostowania w dwoma systemami operacyjnymi: Windows i Linux. Istnieją trzy plany hostowania obecnie dostępne, każdy z różnych zachowań - [zużycie](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), i [(usługa App Service) w wersji dedykowanej](functions-scale.md#app-service-plan). Nie wszystkie technologie wdrażania są dostępne dla każdej wersji usługi Azure Functions.

| Technologia wdrażania | Użycie Windows | Windows — wersja premium (wersja zapoznawcza) | Windows w wersji dedykowanej  | Użycie systemu Linux (wersja zapoznawcza) | Linux w wersji dedykowanej |
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
<sup>2</sup> edycji portal jest włączona tylko wyzwalaczy HTTP oraz czasomierzem dla funkcji w systemie Linux przy użyciu dedykowanych planów.

## <a name="key-concepts"></a>Kluczowe pojęcia

Przed kontynuowaniem, jest ważne dowiedzieć się, niektóre kluczowe pojęcia, które będą mają kluczowe znaczenie dla zrozumienia, jak działają wdrożeń w usłudze Azure Functions.

### <a name="trigger-syncing"></a>Trwa synchronizowanie wyzwalacza

Po zmianie dowolnego usługi wyzwalaczy infrastruktury funkcji należy pamiętać o tych zmianach. Wykonanie synchronizacji odbywa się automatycznie w przypadku wielu technologii wdrożenia. Jednak w niektórych przypadkach należy ręcznie zsynchronizować usługi wyzwalaczy. Podczas wdrażania aktualizacji za pomocą pakietu zewnętrznego adresu URL, lokalnego narzędzia Git, synchronizacji w chmurze lub FTP, należy się ręcznie zsynchronizować usługi wyzwalaczy. Można synchronizować wyzwalaczy w jeden z trzech sposobów:

* Uruchom ponownie swoją aplikację funkcji w witrynie Azure portal
* Wyślij żądanie HTTP POST do `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` przy użyciu [klucz główny](functions-bindings-http-webhook.md#authorization-keys).
* Wyślij żądanie HTTP POST do `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Zastąp symbole zastępcze Identyfikatora subskrypcji, nazwę grupy zasobów i nazwę aplikacji funkcji.

## <a name="deployment-technology-details"></a>Szczegóły technologii wdrożenia  

Te poniższych metod wdrażania są obsługiwane przez usługi Azure Functions.

### <a name="external-package-url"></a>Adres URL zewnętrznego pakietu

Można odwoływać się do pliku zdalnego pakietu (zip), który zawiera aplikację funkcji. Plik jest pobierany z podanego adresu URL, a aplikacja jest uruchamiana [uruchomienia z pakietu](run-functions-from-deployment-package.md) trybu.

>__Jak z niego korzystać:__ Dodaj `WEBSITE_RUN_FROM_PACKAGE` w ustawieniach Twojej aplikacji. Wartość tego ustawienia należy adres URL — lokalizacja pliku określonego pakietu, który chcesz uruchomić. Możesz dodać ustawienia albo [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) lub [przy użyciu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Jeśli przy użyciu usługi Azure blob storage, należy użyć kontenera prywatnych przy użyciu [sygnatury dostępu współdzielonego (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) do udostępnienia funkcji do pakietu. Dowolnym ponownym uruchomieniem aplikacji go powoduje pobranie kopii zawartości, co oznacza, że użytkownikowi musi być ważny przez okres istnienia aplikacji.

>__Kiedy stosować:__ To jest metoda wdrażania tylko obsługiwana w przypadku usługi Azure Functions działającej w systemie Linux w ramach planu zużycie (wersja zapoznawcza). Podczas aktualizacji pliku pakietu, odwołuje się do aplikacji funkcji, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing) Azure stwierdzić, że aplikacja została zmieniona.

### <a name="zip-deploy"></a>Wdrażanie pliku zip

Umożliwia wypychanie plik zip zawierający aplikację funkcji na platformie Azure. Opcjonalnie można również określić uruchamiania w aplikacji [uruchomienia z pakietu](run-functions-from-deployment-package.md) trybu.

>__Jak z niego korzystać:__ Wdrażanie przy użyciu Twojego ulubionego narzędzia klienta - [programu VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [programu Visual Studio](functions-develop-vs.md#publish-to-azure), lub [wiersza polecenia platformy Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Aby ręcznie wdrożyć plik zip do aplikacji funkcji, postępuj zgodnie z instrukcjami przedstawionymi w temacie [wdrażania z pliku zip lub adres url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Ponadto w przypadku wdrażania za pomocą pliku zip wdrażania, użytkownicy mogą określić do uruchamiania aplikacji z w [uruchomienia z pakietu](run-functions-from-deployment-package.md) tryb ustawiając `WEBSITE_RUN_FROM_PACKAGE` wartość ustawienia aplikacji jako `1`. Ta opcja jest zalecane i daje krótszy czas ładowania aplikacji. Odbywa się domyślnie dla powyższych narzędzi klienckich.

>__Kiedy stosować:__ Jest to technologia zalecane wdrożenie usługi Azure Functions w systemie Windows i usługi Azure Functions działającej w systemie Linux w przypadku dedykowanego planu.

### <a name="docker-container"></a>Kontener platformy docker

Wdrażanie obrazu kontenera systemu Linux, który zawiera aplikację funkcji.

>__Jak z niego korzystać:__ Tworzenie aplikacji funkcji systemu Linux w przypadku dedykowanego planu i określić, który obraz kontenera, aby uruchamiała się z. Można to zrobić na dwa sposoby:
>
>* Tworzenie aplikacji funkcji systemu Linux na plan usługi App Service w witrynie Azure portal. Wybierz **obrazu platformy Docker** dla **Publikuj**oraz konfigurowanie kontenera, podając lokalizację, w którym jest hostowany obraz.
>* Tworzenie aplikacji funkcji systemu Linux na plan usługi App Service przy użyciu wiersza polecenia platformy Azure. Dowiedz się, jak przeglądając [Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Aby wdrożyć istniejącą aplikację przy użyciu niestandardowego kontenera, należy użyć [ `func deploy` ](functions-run-local.md#publish) polecenia [podstawowych narzędzi usługi Azure Functions](functions-run-local.md).

>__Kiedy stosować:__ Użyj tej opcji, gdy potrzebujesz większej kontroli nad środowiskiem Linux której działa aplikacja funkcji. Ten mechanizm wdrażania jest dostępna tylko dla funkcji działającej w systemie Linux w ramach planu usługi App Service.

### <a name="web-deploy-msdeploy"></a>Narzędzie Web deploy (MSDeploy)

Pakiety i wdrażania aplikacji Windows na dowolnym serwerze usług IIS, w tym Twoje aplikacje funkcji z systemem Windows na platformie Azure.

>__Jak z niego korzystać:__ Użyj [narzędzi programu Visual Studio dla usługi Azure Functions](functions-create-your-first-function-visual-studio.md)i usuń zaznaczenie pola wyboru `Run from package file (recommended)` pole.
>
> Możesz również pobrać [3.6 wdrażania sieci Web](https://www.iis.net/downloads/microsoft/web-deploy) i wywołać `MSDeploy.exe` bezpośrednio.

>__Kiedy stosować:__ Ta technologia wdrożenie jest obsługiwane i nie ma żadnych problemów, ale jest to preferowany sposób, teraz [Zip wdrażania z uruchamiania z pakietem włączone](#zip-deploy). Aby dowiedzieć się więcej, odwiedź stronę [Podręcznik programowania Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Kontrola źródła

Pozwala połączyć swoją aplikację funkcji z repozytorium git, taki sposób, że wszelkie aktualizacje kodu w tym repozytorium wyzwala wdrożenia. Aby uzyskać więcej informacji, zapoznaj się [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak z niego korzystać:__ Aby skonfigurować publikowanie z kontroli źródła, należy użyć Centrum wdrażania w portalu usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md).

>__Kiedy stosować:__ Przy użyciu kontroli źródła jest najlepszym rozwiązaniem dla zespołów współpracy ich aplikacji funkcji i jest to doskonałe rozwiązanie, który umożliwia bardziej złożone potoki wdrożenia.

### <a name="local-git"></a>Lokalnego narzędzia git

Umożliwia wypychanie kodu z komputera lokalnego do usługi Azure Functions przy użyciu narzędzia Git.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami w artykule [lokalne wdrożenie narzędzia Git w usłudze Azure App Service](../app-service/deploy-local-git.md).

>__Kiedy stosować:__ Ogólnie rzecz biorąc zaleca się innych metod wdrażania. Podczas publikowania z lokalnego repozytorium git, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizacji chmury

Umożliwia synchronizowanie zawartości z usługi Dropbox i OneDrive do usługi Azure Functions.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami w [Synchronizuj zawartość z folderu w chmurze](../app-service/deploy-content-sync.md).

>__Kiedy stosować:__ Ogólnie rzecz biorąc zaleca się innych metod wdrażania. Podczas publikowania z opcją synchronizacji w chmurze, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing).

### <a name="ftp"></a>Protokół FTP

Umożliwia bezpośrednie transferu plików do usługi Azure Functions.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami w [wdrażania zawartości przy użyciu protokołu FTP/s](../app-service/deploy-ftp.md).

>__Kiedy stosować:__ Ogólnie rzecz biorąc zaleca się innych metod wdrażania. Podczas publikowania, przy użyciu protokołu FTP, należy najpierw [ręcznie zsynchronizować wyzwalaczy](#trigger-syncing).

### <a name="portal-editing"></a>Edytowanie w portalu

Za pomocą edytora oparte na portalu umożliwia bezpośrednio edytować pliki na aplikację funkcji (zasadniczo wdrażanie w dowolnym momencie możesz kliknąć pozycję **Zapisz**).

>__Jak z niego korzystać:__ Aby móc edytować funkcji w witrynie Azure portal, musisz mieć [utworzonej funkcji w portalu](functions-create-first-azure-function.md). Za pomocą innej metody wdrażania sprawia, że funkcja tylko do odczytu i uniemożliwia dalsze edycję portalu, aby zachować jednego źródła faktów. Aby powrócić do stanu, w którym można edytować pliki przy użyciu witryny Azure portal, można ręcznie włączyć tryb edycji do `Read/Write` i usuwania wszelkich ustawień związanych z wdrażaniem aplikacji (takich jak `WEBSITE_RUN_FROM_PACKAGE`). 

>__Kiedy stosować:__ Portal jest to doskonały sposób, aby rozpocząć pracę z usługą Azure Functions, ale dla dowolnego im bardziej intensywny tworzonym przy użyciu klienta zaleca się narzędzi:
>
>* [Rozpoczęcie korzystania z programu VS Code](functions-create-first-function-vs-code.md)
>* [Rozpoczęcie korzystania z podstawowych narzędzi usługi Azure Functions](functions-run-local.md)
>* [Rozpoczęcie korzystania z programu Visual Studio](functions-create-your-first-function-visual-studio.md)

W poniższej tabeli przedstawiono systemy operacyjne i języki, dla których jest obsługiwana edycja portalu:

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

<sup>*</sup> Edytowanie w portalu jest włączona tylko wyzwalaczy HTTP oraz czasomierzem dla funkcji w systemie Linux przy użyciu dedykowanych planów.

## <a name="deployment-slots"></a>Miejsca wdrożenia

Podczas wdrażania aplikacji funkcji na platformie Azure, można wdrożyć w miejscu wdrażane pojedynczo zamiast bezpośrednio do środowiska produkcyjnego. Aby uzyskać więcej informacji na temat miejsc wdrożenia, zobacz [dokumentacji usługi Azure App Service miejsc](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Pomoc techniczną na poziomach miejsca wdrożenia

Istnieją dwa poziomy pomocy technicznej:

* _Ogólnie dostępna (GA)_ — w pełni obsługiwana i zatwierdzone do użycia w środowisku produkcyjnym.
* _Podgląd_ — nie są jeszcze obsługiwane, ale oczekuje się, że osiągną stan wersji ogólnie dostępnej w przyszłości.

| Plan hostingu/OS | Poziom wsparcia |
| --------------- | ------ |
| Użycie Windows | Wersja zapoznawcza |
| Windows — wersja Premium (wersja zapoznawcza) | Wersja zapoznawcza |
| Windows w wersji dedykowanej | Ogólnie dostępna |
| Użycie systemu Linux | Nieobsługiwane |
| Linux w wersji dedykowanej | Ogólnie dostępna |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat wdrażania aplikacji funkcji w następujących artykułach: 

+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)
+ [Ciągłe dostarczanie za pomocą DevOps platformy Azure](functions-how-to-azure-devops.md)
+ [ZIP wdrożenia dla usługi Azure Functions](deployment-zip-push.md)
+ [Uruchamianie usługi Azure Functions z pliku pakietu](run-functions-from-deployment-package.md)
+ [Automatyzowanie wdrażania zasobów dla aplikacji funkcji w usłudze Azure Functions](functions-infrastructure-as-code.md)

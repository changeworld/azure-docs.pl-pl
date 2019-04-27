---
title: Praca z modułami środowiska Node.js
description: Dowiedz się, jak pracować z modułów Node.js w przypadku korzystania z usługi Azure App Service lub usług w chmurze.
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 08f3a2dcf9d36eb76b2f657232a426b078066273
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634925"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)
Ten dokument zawiera wskazówki na temat używania modułów Node.js w aplikacjach hostowanych na platformie Azure. Zapewnia wskazówki na zapewnienie, że aplikacja używa określonej wersji modułu, a także korzystanie z modułów natywnych przy użyciu platformy Azure.

Jeśli masz już zaznajomieni z używaniem modułów Node.js **package.json** i **npm shrinkwrap.json** plików, co zostało omówione w tym artykule krótkie podsumowanie zawiera następujące informacje:

* Usługa Azure App Service obsługuje **package.json** i **npm shrinkwrap.json** plików i może zainstalować moduły na podstawie wpisów w tych plikach.

* Usługi Azure Cloud Services oczekuje, że wszystkie moduły, należy zainstalować na środowisko deweloperskie i **węzła\_modułów** katalogu do uwzględnienia jako część pakietu wdrożeniowego. Istnieje możliwość włączyć obsługę instalowania modułów za pomocą **package.json** lub **npm shrinkwrap.json** plików na usługi w chmurze; jednak ta konfiguracja wymaga dostosowania domyślnych skrypty używane przez projektów usług w chmurze. Na przykład sposobu konfigurowania tego środowiska zobacz [zadanie uruchamiania platformy Azure do uruchamiania instalacji npm, aby uniknąć wdrażania moduły node.js](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Maszyny wirtualne platformy Azure nie zostały omówione w tym artykule, ponieważ środowisko wdrażania na maszynie wirtualnej jest zależny od systemu operacyjnego, obsługiwany przez maszynę wirtualną.
> 
> 

## <a name="nodejs-modules"></a>Moduły node.js
Moduły są obciążana pakietów języka JavaScript, które dostarczają określonych funkcji aplikacji. Moduły zwykle są instalowane za pomocą **npm** wiersza polecenia narzędzia, jednak niektórych modułów (np. moduł http) są dostarczane jako część pakietu środowiska Node.js core.

Po zainstalowaniu moduły są przechowywane w **węzła\_modułów** katalogu w folderze głównym struktury katalogów aplikacji. Każdy moduł w ramach **węzła\_modułów** directory zachowuje swój własny katalog, który zawiera wszystkie moduły, które zależy od jego i ten problem powtarza się dla każdego modułu, aż w łańcuchu zależności. To środowisko umożliwia każdy moduł zainstalowana tak, aby mieć własne wymagania dotyczące wersji dla modułów ona zależy, jednak może spowodować w strukturze bardzo dużych katalogów.

Wdrażanie **węzła\_modułów** katalogu jako część aplikacji, zwiększa rozmiar wdrożenia, w porównaniu z przy użyciu **package.json** lub  **npm shrinkwrap.json** pliku; jednak gwarantuje, że wersje moduły używane w środowisku produkcyjnym są takie same jak modułami użytymi w rozwoju.

### <a name="native-modules"></a>Moduły macierzyste
Mimo że większość modułów pliki JavaScript po prostu zwykłego tekstu, niektóre moduły są obrazów binarnych specyficzne dla platformy. Te moduły są kompilowane w czasie instalacji, zwykle za pomocą języka Python i gyp węzła. Ponieważ zależą od usług Azure Cloud Services **węzła\_modułów** folderu wdrażane jako część aplikacji, wszelkie natywnego modułu dołączone jako część zainstalowanych modułów powinny działać w usłudze w chmurze, tak długo, jak został zainstalowany i skompilowany w systemie Windows, programowanie.

Usługa Azure App Service nie obsługuje wszystkich modułów macierzystych i może zakończyć się niepowodzeniem podczas kompilowania modułów z określonych wymagań wstępnych. Niektórych popularnych modułów, takich jak bazy danych MongoDB ma opcjonalny zależności natywnych i działać poprawnie bez ich, dwa obejścia problemu można było zaobserwować przy użyciu prawie wszystkie moduły macierzyste dostępne już dzisiaj:

* Uruchom **npm zainstalować** na komputerze Windows, który ma zainstalowane warunki wstępne wszystkich natywnego modułu. Następnie można wdrożyć utworzony **węzła\_modułów** folder jako część aplikacji w usłudze Azure App Service.

  * Przed skompilowaniem, sprawdź, czy lokalnej instalacji środowiska Node.js ma pasujące architekturę i wersja jest możliwie jak najbardziej zbliżone do jednego używane na platformie Azure (bieżące wartości może zostać sprawdzone w środowisku uruchomieniowym przy użyciu właściwości **process.arch** i **process.version**).

* Usługa Azure App Service można skonfigurować w celu wykonania niestandardowego powłoki bash lub skryptów powłoki podczas wdrażania, co daje możliwość wykonywania niestandardowych poleceń i dokładnie skonfigurować sposób **npm zainstalować** jest uruchamiany. Film przedstawiający sposób konfigurowania tego środowiska, zobacz [skrypty wdrażania witryny sieci Web niestandardowego za pomocą aparatu Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Przy użyciu pliku package.json

**Package.json** plik jest sposób, aby określić zależności najwyższego poziomu, aplikacja wymaga, aby umożliwić zainstalowanie platformy hostingowej, zależności, zamiast konieczności, które mają zostać uwzględnione **węzła\_ Moduły** folder jako część wdrożenia. Po wdrożeniu aplikacji **npm zainstalować** polecenie służy do analizowania **package.json** pliku i zainstalowanie wszystkich składników zależnych wymienionych.

Podczas tworzenia aplikacji, można użyć **— Zapisz**, **— zapisywanie dev**, lub **— opcjonalnie Zapisz** parametrów podczas instalowania modułów, aby dodać odpowiedni wpis dla modułu usługi **package.json** pliku automatycznie. Aby uzyskać więcej informacji, zobacz [instalacji npm](https://docs.npmjs.com/cli/install).

Jeden potencjalny problem z **package.json** plik jest, że tylko określa wersję zależności najwyższego poziomu. Każdy moduł zainstalowane może lub nie można określić wersji modułów, od których zależy, a więc jest możliwe, że użytkownik może pozostać przy użyciu łańcucha zależności innego niż używane w rozwoju.

> [!NOTE]
> W przypadku wdrażania w usłudze Azure App Service, jeśli Twoja <b>package.json</b> plik odwołuje się moduł macierzysty, można napotkać błąd podobny do poniższego przykładu, w przypadku publikowania aplikacji przy użyciu narzędzia Git:
> 
> npm błąd! module-name@0.6.0 Zainstaluj: "node gyp Konfigurowanie kompilacji"
> 
> npm błąd! "cmd"/ c""węzeł gyp Konfigurowanie kompilacji"" nie powiodło się z 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Przy użyciu pliku npm shrinkwrap.json
**Npm shrinkwrap.json** plik jest próba adresów modułu przechowywanie wersji ograniczenia **package.json** pliku. Gdy **package.json** plik zawiera tylko wersje dla moduły najwyższego poziomu **npm shrinkwrap.json** plik zawiera wymagania dotyczące wersji dla modułu pełny łańcuch zależności.

Gdy aplikacja jest gotowa do produkcji, można zablokować wymagania dotyczące wersji i tworzyć **npm shrinkwrap.json** plików przy użyciu **npm shrinkwrap** polecenia. To polecenie będzie używać wersji zainstalowanej w **węzła\_modułów** folderu i zarejestrować tych wersji **npm shrinkwrap.json** pliku. Po wdrożeniu aplikacji do środowiska hostingu **npm zainstalować** polecenie służy do analizowania **npm shrinkwrap.json** pliku i zainstalowanie wszystkich składników zależnych wymienionych. Aby uzyskać więcej informacji, zobacz [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> W przypadku wdrażania w usłudze Azure App Service, jeśli Twoja <b>npm shrinkwrap.json</b> plik odwołuje się moduł macierzysty, można napotkać błąd podobny do poniższego przykładu, w przypadku publikowania aplikacji przy użyciu narzędzia Git:
> 
> npm błąd! module-name@0.6.0 Zainstaluj: "node gyp Konfigurowanie kompilacji"
> 
> npm błąd! "cmd"/ c""węzeł gyp Konfigurowanie kompilacji"" nie powiodło się z 1
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy już rozumiesz sposób używania modułów Node.js z platformą Azure, Dowiedz się jak [określanie wersji środowiska Node.js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [tworzenie i wdrażanie aplikacji sieci web Node.js](app-service/app-service-web-get-started-nodejs.md), i [sposób używania wiersza polecenia platformy Azure Interfejs dla systemów Mac i Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](/nodejs/azure/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo

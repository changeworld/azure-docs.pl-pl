---
title: Praca z modułami Node.js
description: Dowiedz się, jak pracować z modułami Node.js podczas korzystania z usługi Azure App Service lub usług w chmurze.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70309272"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)
Ten dokument zawiera wskazówki dotyczące korzystania z modułów Node.js z aplikacjami hostowanymi na platformie Azure. Zawiera wskazówki dotyczące zapewnienia, że aplikacja używa określonej wersji modułu, a także przy użyciu modułów natywnych z platformą Azure.

Jeśli znasz już przy użyciu node.js modułów, **package.json** i **npm-shrinkwrap.json** plików, następujące informacje zawiera krótkie podsumowanie tego, co zostało omówione w tym artykule:

* Usługa Azure App Service rozumie pliki **package.json** i **npm-shrinkwrap.json** i może instalować moduły na podstawie wpisów w tych plikach.

* Usługi w chmurze platformy Azure oczekuje, że wszystkie moduły mają być zainstalowane w środowisku deweloperskim, a katalog **modułów węzłów\_** zostanie dołączony jako część pakietu wdrażania. Możliwe jest włączenie obsługi instalowania modułów przy użyciu plików **package.json** lub **npm-shrinkwrap.json** w usługach w chmurze; Jednak ta konfiguracja wymaga dostosowania skryptów domyślnych używanych przez projekty usługi w chmurze. Na przykład jak skonfigurować to środowisko, zobacz [Zadanie uruchamiania platformy Azure do uruchomienia instalacji npm, aby uniknąć wdrażania modułów węzłów](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Maszyny wirtualne platformy Azure nie są omówione w tym artykule, ponieważ środowisko wdrażania na maszynie wirtualnej zależy od systemu operacyjnego hostowanego przez maszynę wirtualną.
> 
> 

## <a name="nodejs-modules"></a>Moduły node.js
Moduły są ładowalne pakiety JavaScript, które zapewniają określone funkcje dla aplikacji. Moduły są zwykle instalowane za pomocą narzędzia wiersza polecenia **npm,** jednak niektóre moduły (takie jak moduł http) są dostarczane jako część podstawowego pakietu Node.js.

Po zainstalowaniu modułów są one przechowywane w katalogu **modułów węzłów\_** w katalogu głównym struktury katalogów aplikacji. Każdy moduł w katalogu **modułów węzłów\_** zachowuje swój własny katalog, który zawiera wszystkie moduły, od których zależy, a to zachowanie powtarza się dla każdego modułu aż do łańcucha zależności. To środowisko pozwala każdemu zainstalowanemu modułowi mieć własne wymagania dotyczące wersji dla modułów, od których zależy, jednak może to spowodować dość dużą strukturę katalogów.

Wdrażanie katalogu **modułów węzłów\_** w ramach aplikacji zwiększa rozmiar wdrożenia w porównaniu z użyciem pliku **package.json** lub **npm-shrinkwrap.json;** jednak gwarantuje, że wersje modułów używanych w produkcji są takie same jak moduły używane w rozwoju.

### <a name="native-modules"></a>Moduły natywne
Podczas gdy większość modułów to po prostu pliki JavaScript w postaci zwykłego tekstu, niektóre moduły są obrazami binarnymi specyficznymi dla platformy. Moduły te są kompilowane w czasie instalacji, zwykle przy użyciu Języka Python i node-gyp. Ponieważ usługi w chmurze platformy Azure polegają na folderze **modułów węzłów\_** wdrażanym jako część aplikacji, każdy moduł macierzysty dołączony do zainstalowanych modułów powinien działać w usłudze w chmurze, o ile został zainstalowany i skompilowany w systemie dewelopera systemu Windows.

Usługa Azure App Service nie obsługuje wszystkich modułów natywnych i może zakończyć się niepowodzeniem podczas kompilowania modułów z określonymi wymaganiami wstępnymi. Podczas gdy niektóre popularne moduły, takie jak MongoDB, mają opcjonalne zależności natywnych i działają dobrze bez nich, dwa obejścia okazały się skuteczne w prawie wszystkich dostępnych obecnie modułach natywnych:

* Uruchom **instalację npm** na komputerze z systemem Windows, na który są zainstalowane wszystkie wymagania wstępne modułu macierzystego. Następnie wdrożyć folder **utworzonych modułów\_węzłów** jako część aplikacji do usługi Azure App Service.

  * Przed kompilacją sprawdź, czy lokalna instalacja node.js ma pasującą architekturę, a wersja jest jak najbardziej zbliżona do tej używanej na platformie Azure (bieżące wartości można sprawdzić w czasie wykonywania z właściwości **process.arch** i **process.version**).

* Usługa Azure App Service można skonfigurować do wykonywania skryptów niestandardowe bash lub powłoki podczas wdrażania, co daje możliwość wykonywania poleceń niestandardowych i precyzyjnie skonfigurować sposób **instalacji npm** jest uruchamiany. Aby uzyskać film przedstawiający sposób konfigurowania tego środowiska, zobacz [Niestandardowe skrypty wdrażania witryny sieci Web z programem Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Korzystanie z pliku package.json

Plik **package.json** jest sposobem określenia zależności najwyższego poziomu, których wymaga aplikacja, aby platforma hostingowa mogła zainstalować zależności, zamiast wymagać uwzględnienia folderu **modułów węzłów\_** w ramach wdrożenia. Po wdrożeniu aplikacji polecenie **npm install** jest używane do analizowanie pliku **package.json** i instalowanie wszystkich wymienionych zależności.

Podczas tworzenia programu można użyć parametrów **--save**, **--save-dev**lub **--save-optional** podczas instalowania modułów, aby automatycznie dodać wpis dla modułu do pliku **package.json.** Aby uzyskać więcej informacji, zobacz [npm-install](https://docs.npmjs.com/cli/install).

Jednym z potencjalnych problemów z plikiem **package.json** jest to, że określa tylko wersję dla zależności najwyższego poziomu. Każdy zainstalowany moduł może lub nie może określić wersję modułów, od których zależy, a więc jest możliwe, że może skończyć się z innym łańcuchem zależności niż używany w rozwoju.

> [!NOTE]
> Podczas wdrażania w usłudze Azure App Service, jeśli plik <b>package.json</b> odwołuje się do modułu macierzystego może pojawić się błąd podobny do następującego przykładu podczas publikowania aplikacji przy użyciu git:
> 
> npm ERR! module-name@0.6.0install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' nie powiodło się z 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Korzystanie z pliku npm-shrinkwrap.json
Plik **npm-shrinkwrap.json** jest próbą rozwiązania ograniczeń przechowywania wersji modułu pliku **package.json.** Plik **package.json** zawiera tylko wersje dla modułów najwyższego poziomu, plik **npm-shrinkwrap.json** zawiera wymagania dotyczące wersji dla pełnego łańcucha zależności modułu.

Gdy aplikacja jest gotowa do produkcji, można zablokować wymagania dotyczące wersji i utworzyć plik **npm-shrinkwrap.json** za pomocą polecenia **npm shrinkwrap.** To polecenie użyje wersji aktualnie zainstalowanych w folderze **modułów węzłów\_** i zapisze te wersje do pliku **npm-shrinkwrap.json.** Po wdrożeniu aplikacji w środowisku hostingu polecenie **npm install** służy do analizowania pliku **npm-shrinkwrap.json** i instalowania wszystkich wymienionych zależności. Aby uzyskać więcej informacji, zobacz [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Podczas wdrażania w usłudze Azure App Service, jeśli plik <b>npm-shrinkwrap.json</b> odwołuje się do modułu macierzystego, podczas publikowania aplikacji przy użyciu gita może zostać wyświetlony błąd podobny do następującego przykładu:
> 
> npm ERR! module-name@0.6.0install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' nie powiodło się z 1
> 
> 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy zrozumiesz, jak korzystać z modułów Node.js z platformą Azure, dowiedz się, jak [określić wersję Node.js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [utworzyć i wdrożyć aplikację internetową Node.js](app-service/app-service-web-get-started-nodejs.md)oraz [Jak używać interfejsu wiersza polecenia platformy Azure dla komputerów Mac i Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo

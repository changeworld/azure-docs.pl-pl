---
title: Praca z modułami Node.js
description: Dowiedz się, jak pracować z modułów Node.js w przypadku korzystania z usługi aplikacji Azure lub usługi w chmurze.
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
ms.openlocfilehash: 76679ea0ff2c1e88d1923488717a245351437165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864056"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)
Ten dokument zawiera wskazówki dotyczące używania modułów Node.js z aplikacjami hostowanej na platformie Azure. Zwraca uwagę na upewnienie się, że aplikacja korzysta z odpowiedniej wersji modułu, a także opisuje korzystanie z modułów natywnych w systemie Azure.

Jeśli znasz już przy użyciu modułów Node.js **package.json** i **npm shrinkwrap.json** pliki, szybkie podsumowanie co opisano w tym artykule zawiera następujące informacje:

* Rozumie, usługa aplikacji Azure **package.json** i **npm shrinkwrap.json** pliki i można zainstalować na podstawie pozycji w tych plikach modułów.

* Usługi w chmurze Azure oczekiwać wszystkie moduły instalowanego na środowisko deweloperskie i **węzła\_modułów** katalogu być dołączane jako część pakietu wdrożeniowego. Można włączyć obsługę instalowanie modułów za pomocą **package.json** lub **npm shrinkwrap.json** plików na usługi w chmurze; jednak ta konfiguracja wymaga dostosowania skryptów domyślne używane przez projekty usługi w chmurze. Na przykład sposobu konfigurowania tego środowiska zobacz [Azure uruchamiania zadania instalacji npm, aby uniknąć Wdrażanie modułów węzła](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Maszyny wirtualne platformy Azure nie omówiono w tym artykule jako środowisko wdrażania na maszynie wirtualnej jest zależny od systemu operacyjnego, obsługiwany przez maszynę wirtualną.
> 
> 

## <a name="nodejs-modules"></a>Moduły środowiska node.js
Moduły są obciążana pakiety języka JavaScript, które zapewniają określonych funkcji aplikacji. Moduły zwykle są instalowane za pomocą **npm** wiersza polecenia narzędzia, jednak niektóre moduły (takich jak moduł http) są dostarczane jako część pakietu środowiska Node.js core.

Po zainstalowaniu moduły są przechowywane w **węzła\_modułów** katalogu w katalogu głównym aplikacji struktury katalogów. Każdy moduł w **węzła\_modułów** directory zachowuje własną **węzła\_modułów** katalog, który zawiera wszystkie moduły zależy, które to zachowanie jest powtarzany dla każdego modułu w dół do końca łańcuch zależności. To środowisko umożliwia poszczególnych modułów, aby mieć własną wersję wymagania dotyczące modułów zależy, jednak może to spowodować bardzo dużych strukturą katalogów.

Wdrażanie **węzła\_modułów** katalogu jako części aplikacji spowoduje zwiększenie rozmiaru wdrożenia, w porównaniu z zastosowaniem **package.json** lub **npm shrinkwrap.json** pliku; jednak gwarantuje, że wersje moduły używane w środowisku produkcyjnym są takie same, jak moduły używane do rozwoju.

### <a name="native-modules"></a>Moduły macierzyste
Większość modułów są pliki JavaScript po prostu zwykłego tekstu, niektóre moduły są obrazy binarne specyficzne dla platformy. Te moduły są kompilowane w czasie instalacji, zwykle za pomocą języka Python i gyp węzła. Ponieważ zależą od usługi w chmurze Azure **węzła\_modułów** folderu wdrażane jako część aplikacji, w każdym natywnego modułu wchodzącego w skład zainstalowanych modułów powinny działać w usłudze w chmurze tak długo, jak została zainstalowana, a na rozwoju systemu Windows.

Usługa aplikacji Azure nie obsługuje wszystkie moduły natywne i może zakończyć się niepowodzeniem w przypadku kompilowania kodu modułów z określonych wymagań wstępnych. Niektóre popularnych modułów, takich jak bazy danych MongoDB opcjonalne zależnościami macierzystego i działają prawidłowo bez obawy, dwa obejścia potwierdza, że pomyślnie z prawie wszystkie moduły macierzyste dostępne dzisiaj:

* Uruchom **instalacji narzędzia npm** na komputerze z systemem Windows, który ma zainstalowane wymagania wstępne wszystkich modułu macierzystego w. Następnie Wdróż utworzony **węzła\_modułów** folder jako część aplikacji w usłudze Azure App Service.

  * Przed skompilowaniem, sprawdź, czy lokalnej instalacji programu Node.js ma architekturę zgodną i wersja jest możliwie jak najbardziej zbliżone do jednego używane na platformie Azure (bieżące wartości można sprawdzić na środowisko uruchomieniowe przy użyciu właściwości **process.arch** i **process.version**).

* Usługa aplikacji Azure może być skonfigurowane do wykonywania bash niestandardowych lub skryptów powłoki podczas wdrażania, umożliwiając możliwość wykonania polecenia niestandardowych i dokładnie skonfigurować sposób **instalacji narzędzia npm** jest uruchamiana. Aby obejrzeć wideo przedstawiający sposób konfigurowania tego środowiska, zobacz [niestandardowych skryptów wdrażania witryny sieci Web z Kudu].

### <a name="using-a-packagejson-file"></a>Przy użyciu pliku package.json

**Package.json** plik jest możliwość określenia zależności najwyższego poziomu, aplikacja wymaga, aby Platforma macierzysta można zainstalować zależności, zamiast konieczności obejmują **węzła\_ Moduły** folder jako część wdrożenia. Po wdrożeniu aplikacji **instalacji narzędzia npm** polecenie służy do analizowania **package.json** plików i zainstalować wszystkie zależności na liście.

Podczas tworzenia, można użyć **— Zapisz**, **— Zapisz deweloperów**, lub **— opcjonalne Zapisz** parametrów podczas instalowania modułów, aby dodać wpis dla modułu do sieci **package.json** pliku automatycznie. Aby uzyskać więcej informacji, zobacz [npm install](https://docs.npmjs.com/cli/install).

Jednym z potencjalnych problemów z **package.json** plik jest, że tylko określa wersję zależności najwyższego poziomu. Każdy zainstalowany moduł może lub nie może określić wersji modułów, od których zależy, a więc jest to możliwe, że może to spowodować z łańcucha zależności innego niż używane w rozwoju.

> [!NOTE]
> W przypadku wdrażania w usłudze Azure App Service, jeśli Twoje <b>package.json</b> odwołuje się do pliku modułu macierzystego podczas publikowania aplikacji przy użyciu narzędzia Git napotkać błąd podobny do poniższego przykładu:
> 
> Błąd npm! module-name@0.6.0Zainstaluj: "gyp węzła Konfigurowanie kompilacji"
> 
> Błąd npm! "cmd"/ c""gyp węzła Konfigurowanie kompilacji"" nie powiodło się z 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Przy użyciu pliku npm shrinkwrap.json
**Npm shrinkwrap.json** plik jest próba adresów ograniczenia wersji modułu **package.json** pliku. Gdy **package.json** pliku obejmuje tylko wersje dla modułów najwyższego poziomu, **npm shrinkwrap.json** plik zawiera wymagania dotyczące wersji dla modułu pełny łańcuch zależności.

Gdy aplikacja jest gotowa do produkcji, można zablokować wymagania dotyczące wersji i utworzyć **npm shrinkwrap.json** pliku przy użyciu **npm shrinkwrap** polecenia. To polecenie będzie używać wersji aktualnie zainstalowane w **węzła\_modułów** folderu i zarejestruj tych wersji **npm shrinkwrap.json** pliku. Po wdrożeniu aplikacji do środowiska macierzystego **instalacji narzędzia npm** polecenie służy do analizowania **npm shrinkwrap.json** plików i zainstalować wszystkie zależności na liście. Aby uzyskać więcej informacji, zobacz [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> W przypadku wdrażania w usłudze Azure App Service, jeśli Twoje <b>npm shrinkwrap.json</b> odwołuje się do pliku modułu macierzystego podczas publikowania aplikacji przy użyciu narzędzia Git napotkać błąd podobny do poniższego przykładu:
> 
> Błąd npm! module-name@0.6.0Zainstaluj: "gyp węzła Konfigurowanie kompilacji"
> 
> Błąd npm! "cmd"/ c""gyp węzła Konfigurowanie kompilacji"" nie powiodło się z 1
> 
> 

## <a name="next-steps"></a>Następne kroki
Teraz można zrozumieć, jak można użyć modułów programu Node.js dla platformy Azure, Dowiedz się, jak [określanie wersji środowiska Node.js], [tworzenia i wdrażania aplikacji sieci web Node.js](app-service/app-service-web-get-started-nodejs.md), i [sposób użycia interfejsu wiersza polecenia platformy Azure dla systemów Mac i Linux].

Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](/nodejs/azure/).

[określanie wersji środowiska Node.js]: nodejs-specify-node-version-azure-apps.md
[sposób użycia interfejsu wiersza polecenia platformy Azure dla systemów Mac i Linux]:cli-install-nodejs.md
[niestandardowych skryptów wdrażania witryny sieci Web z Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo

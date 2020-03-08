---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671504"
---
* Systemy wielodostępne, które obsługują pełny zakres planów cenowych, z wyjątkiem izolowanych
* App Service Environment (ASE), który wdraża w sieci wirtualnej i obsługuje aplikacje izolowanego planu cenowego

Ten dokument przechodzi przez funkcję integracji sieci wirtualnej, która jest używana w aplikacjach wielodostępnych. Jeśli Twoja aplikacja znajduje się w [App Service Environment][ASEintro], jest już w sieci wirtualnej i nie wymaga użycia funkcji integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej. Aby uzyskać szczegółowe informacje o wszystkich funkcjach sieciowych, Przeczytaj [App Service funkcje sieciowe][Networkingfeatures]

Integracja z siecią wirtualną umożliwia aplikacji dostęp do zasobów w sieci wirtualnej, ale nie przyznaje prywatnego dostępu do aplikacji w ramach komunikacji przychodzącej. Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak z poziomu sieci wirtualnej platformy Azure. Integracja z siecią wirtualną dotyczy tylko wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej. Funkcja integracji sieci wirtualnej działa inaczej, gdy jest używana z sieci wirtualnych w tym samym regionie i z sieci wirtualnych w innych regionach. Funkcja integracji sieci wirtualnej ma dwie odmiany.

* Integracja regionalnej sieci wirtualnej — w przypadku nawiązywania połączenia z usługą Menedżer zasobów sieci wirtualnych w tym samym regionie należy mieć dedykowaną podsieć w ramach integracji z usługą. 
* Integracja sieci wirtualnej wymagana przez bramę — w przypadku nawiązywania połączenia z usługą sieci wirtualnych w innych regionach lub w klasycznej sieci wirtualnej w tym samym regionie potrzebna jest brama Virtual Network obsługiwana w docelowej sieci wirtualnej.

Funkcje integracji sieci wirtualnej:

* Wymagaj planu cenowego w warstwie Standardowa, Premium, PremiumV2 lub elastycznym 
* Obsługa protokołów TCP i UDP
* Pracuj z aplikacjami App Service i aplikacjami funkcji

Istnieje kilka rzeczy, które nie są obsługiwane przez integrację sieci wirtualnej, w tym:

* Instalowanie dysku
* Integracja z usługą AD 
* NetBios

Integracja sieci wirtualnej wymagana przez bramę zapewnia tylko dostęp do zasobów w docelowej sieci wirtualnej lub w sieciach połączonych z docelową siecią wirtualną za pomocą komunikacji równorzędnej lub sieci VPN. Integracja sieci wirtualnej wymagana przez bramę nie umożliwia dostępu do zasobów dostępnych w połączeniach ExpressRoute lub współpracuje z punktami końcowymi usługi. 

Niezależnie od używanej wersji integracja z siecią wirtualną zapewnia aplikacji dostęp do zasobów w sieci wirtualnej, ale nie przyznaje prywatnego dostępu do aplikacji z sieci wirtualnej. Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak z poziomu sieci wirtualnej platformy Azure. Integracja z siecią wirtualną dotyczy tylko wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
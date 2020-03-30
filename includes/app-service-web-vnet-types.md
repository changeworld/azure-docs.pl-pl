---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671504"
---
* Systemy wielodostępne, które obsługują pełny zakres planów cenowych z wyjątkiem
* Środowisko usługi aplikacji (ASE), które wdraża się w sieci wirtualnej i obsługuje aplikacje izolowanego planu cenowego

Ten dokument przechodzi przez funkcję integracji sieci wirtualnej, która jest do użytku w aplikacjach z wieloma dzierżawcami. Jeśli aplikacja znajduje się w [środowisku usługi app service,][ASEintro]a następnie jest już w sieci wirtualnej i nie wymaga użycia funkcji integracji sieci wirtualnej, aby dotrzeć do zasobów w tej samej sieci wirtualnej. Aby uzyskać szczegółowe informacje na temat wszystkich funkcji sieciowych, zobacz [Funkcje sieci usługi App Service][Networkingfeatures]

Integracja sieci wirtualnej daje aplikacji dostęp do zasobów w sieci wirtualnej, ale nie udziela przychodzącego dostępu prywatnego do aplikacji z sieci wirtualnej. Dostęp do witryny prywatnej odnosi się do tworzenia aplikacji tylko z sieci prywatnej, na przykład z sieci wirtualnej platformy Azure. Integracja sieci wirtualnej jest tylko do wykonywania wywołań wychodzących z aplikacji do sieci wirtualnej. Funkcja integracji sieci wirtualnej zachowuje się inaczej, gdy jest używana z sieciami wirtualnymi w tym samym regionie i z sieciami wirtualnymi w innych regionach. Funkcja integracji sieci wirtualnej ma dwie odmiany.

* Integracja regionalnej sieci wirtualnej — podczas łączenia się z sieciami wirtualnymi Menedżera zasobów w tym samym regionie musi mieć dedykowaną podsieć w sieci wirtualnej, z którą integrujesz się. 
* Integracja sieci wirtualnej wymagane bramy — podczas łączenia się z sieciami wirtualnymi w innych regionach lub do klasycznej sieci wirtualnej w tym samym regionie, potrzebujesz bramy sieci wirtualnej aprowizowana w docelowej sieci wirtualnej.

Funkcje integracji sieci wirtualnej:

* wymagają planu cenowego Standard, Premium, PremiumV2 lub Elastic Premium 
* obsługa TCP i UDP
* praca z aplikacjami usługi App Service i aplikacjami funkcji

Istnieje kilka rzeczy, które integracja sieci wirtualnej nie obsługuje, w tym:

* montaż napędu
* Integracja z usługą AD 
* NetBios

Integracja sieci wirtualnej wymaganej bramy zapewnia dostęp tylko do zasobów w docelowej sieci wirtualnej lub w sieciach połączonych z docelową siecią wirtualną z komunikacją równorzędną lub sieciami VPN. Integracja sieci wirtualnej wymaganej bramy nie umożliwia dostępu do zasobów dostępnych w połączeniach usługi ExpressRoute lub współpracuje z punktami końcowymi usługi. 

Niezależnie od używanej wersji integracja sieci wirtualnej daje aplikacji dostęp do zasobów w sieci wirtualnej, ale nie udziela przychodzącego dostępu prywatnego do aplikacji z sieci wirtualnej. Dostęp do witryny prywatnej odnosi się do tworzenia aplikacji tylko z sieci prywatnej, na przykład z sieci wirtualnej platformy Azure. Integracja sieci wirtualnej jest tylko do wykonywania wywołań wychodzących z aplikacji do sieci wirtualnej. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
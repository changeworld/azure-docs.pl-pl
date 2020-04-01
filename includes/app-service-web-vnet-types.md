---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419542"
---
* Systemy wielodostępne, które obsługują pełny zakres planów cenowych z wyjątkiem izolowane.
* Środowisko usługi aplikacji, które wdraża w sieci wirtualnej i obsługuje aplikacje izolowane planu cenowego.

Funkcja integracji sieci wirtualnej jest używana w aplikacjach wielodostępne. Jeśli aplikacja znajduje się w [środowisku usługi app service,][ASEintro]to jest już w sieci wirtualnej i nie wymaga użycia funkcji integracji sieci wirtualnej, aby dotrzeć do zasobów w tej samej sieci wirtualnej. Aby uzyskać więcej informacji na temat wszystkich funkcji sieciowych, zobacz [Funkcje sieci usługi App Service][Networkingfeatures].

Integracja sieci wirtualnej daje aplikacji dostęp do zasobów w sieci wirtualnej, ale nie udziela przychodzącego dostępu prywatnego do aplikacji z sieci wirtualnej. Dostęp do witryny prywatnej odnosi się do tworzenia aplikacji dostępne tylko z sieci prywatnej, takich jak w sieci wirtualnej platformy Azure. Integracja sieci wirtualnej jest używana tylko do wykonywania wywołań wychodzących z aplikacji do sieci wirtualnej. Funkcja integracji sieci wirtualnej zachowuje się inaczej, gdy jest używana z sieciami wirtualnymi w tym samym regionie i z sieciami wirtualnymi w innych regionach. Funkcja integracji sieci wirtualnej ma dwie odmiany:

* **Regionalna integracja sieci wirtualnej:** Podczas łączenia się z sieciami wirtualnymi usługi Azure Resource Manager w tym samym regionie musi mieć dedykowaną podsieć w sieci wirtualnej, z którą integrujesz.
* **Integracja sieci wirtualnej wymaganej przez bramę:** Podczas łączenia się z sieciami wirtualnymi w innych regionach lub z klasyczną siecią wirtualną w tym samym regionie potrzebna jest brama sieci wirtualnej platformy Azure aprowizowana w docelowej sieci wirtualnej.

Funkcje integracji sieci wirtualnej:

* Wymagaj planu cenowego Standard, Premium, PremiumV2 lub Elastic Premium.
* Obsługa protokołu TCP i UDP.
* Praca z aplikacjami usługi Azure App Service i aplikacjami funkcyjnymi.

Istnieje kilka rzeczy, które integracja sieci wirtualnej nie obsługuje, takich jak:

* Montaż napędu.
* Integracja z usługą Active Directory.
* Netbios.

Integracja sieci wirtualnej wymaganej przez bramę zapewnia dostęp do zasobów tylko w docelowej sieci wirtualnej lub w sieciach połączonych z docelową siecią wirtualną z komunikacją równorzędną lub sieciami VPN. Integracja sieci wirtualnej wymaganej przez bramę nie umożliwia dostępu do zasobów dostępnych w połączeniach usługi Azure ExpressRoute ani nie współpracuje z punktami końcowymi usługi.

Niezależnie od używanej wersji integracja sieci wirtualnej daje aplikacji dostęp do zasobów w sieci wirtualnej, ale nie udziela przychodzącego dostępu prywatnego do aplikacji z sieci wirtualnej. Dostęp do witryny prywatnej odnosi się do udostępnienia aplikacji tylko z sieci prywatnej, na przykład z poziomu sieci wirtualnej platformy Azure. Integracja sieci wirtualnej służy tylko do wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features

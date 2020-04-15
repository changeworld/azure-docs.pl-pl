---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312831"
---
* Systemy wielodostępne, które obsługują pełny zakres planów cenowych z wyjątkiem izolowane.
* Środowisko usługi aplikacji, które wdraża w sieci wirtualnej i obsługuje aplikacje izolowanego planu cenowego.

Funkcja integracji sieci wirtualnej jest używana w aplikacjach wielodostępne. Jeśli aplikacja znajduje się w [środowisku usługi app service,][ASEintro]a następnie jest już w sieci wirtualnej i nie wymaga użycia funkcji integracji sieci wirtualnej, aby dotrzeć do zasobów w tej samej sieci wirtualnej. Aby uzyskać więcej informacji na temat wszystkich funkcji sieciowych, zobacz [Funkcje sieci usługi App Service][Networkingfeatures].

Integracja sieci wirtualnej daje aplikacji dostęp do zasobów w sieci wirtualnej, ale nie udziela przychodzącego dostępu prywatnego do aplikacji z sieci wirtualnej. Dostęp do witryny prywatnej odnosi się do tworzenia aplikacji dostępne tylko z sieci prywatnej, takich jak w sieci wirtualnej platformy Azure. Integracja sieci wirtualnej jest używana tylko do wykonywania wywołań wychodzących z aplikacji do sieci wirtualnej. Funkcja integracji sieci wirtualnej zachowuje się inaczej, gdy jest używana z siecią wirtualną w tym samym regionie i z siecią wirtualną w innych regionach. Funkcja integracji sieci wirtualnej ma dwie odmiany:

* **Regionalna integracja sieci wirtualnej:** Podczas łączenia się z sieciami wirtualnymi usługi Azure Resource Manager w tym samym regionie musi mieć dedykowaną podsieć w sieci wirtualnej, z którą integrujesz.
* **Integracja sieci wirtualnej wymaganej przez bramę:** Podczas łączenia się z siecią wirtualną w innych regionach lub z klasyczną siecią wirtualną w tym samym regionie potrzebna jest brama sieci wirtualnej platformy Azure aprowizowana w docelowej sieci wirtualnej.

Funkcje integracji sieci wirtualnej:

* Wymagaj planu cenowego Standard, Premium, PremiumV2 lub Elastic Premium.
* Obsługa protokołu TCP i UDP.
* Praca z aplikacjami usługi Azure App Service i aplikacjami funkcyjnymi.

Istnieje kilka rzeczy, które integracja sieci wirtualnej nie obsługuje, takich jak:

* Montaż napędu.
* Integracja z usługą Active Directory.
* Netbios.

Integracja sieci wirtualnej wymaganej przez bramę zapewnia dostęp do zasobów tylko w docelowej sieci wirtualnej lub w sieciach połączonych z docelową siecią wirtualną z komunikacją równorzędną lub sieciami VPN. Integracja sieci wirtualnej wymaganej przez bramę nie umożliwia dostępu do zasobów dostępnych w połączeniach usługi Azure ExpressRoute ani nie współpracuje z punktami końcowymi usługi.

Niezależnie od używanej wersji integracja sieci wirtualnej daje aplikacji dostęp do zasobów w sieci wirtualnej, ale nie udziela przychodzącego dostępu prywatnego do aplikacji z sieci wirtualnej. Dostęp do witryny prywatnej odnosi się do tworzenia aplikacji dostępne tylko z sieci prywatnej, takich jak w sieci wirtualnej platformy Azure. Integracja sieci wirtualnej jest tylko do wykonywania wywołań wychodzących z aplikacji do sieci wirtualnej.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features

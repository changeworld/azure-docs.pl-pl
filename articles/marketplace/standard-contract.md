---
title: Standardowej umowy | Azure
description: Standardowej umowy w witrynie Azure Marketplace i AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620377"
---
# <a name="standard-contract"></a>Kontrakt standardowy

Aby uprościć proces zakupów dla klientów i ograniczyć złożoność prawne dla dostawców oprogramowania, firma Microsoft oferuje szablon standardowej umowy w celu ułatwienia transakcji w portalu marketplace. Zamiast tworzenia niestandardowych warunków i postanowień, wydawcom portalu Azure Marketplace można oferować swoje oprogramowanie w ramach standardowej umowy, klienci muszą tylko Zweryfikuj i akceptują jeden raz. Standardowa kontrakt można znaleźć tutaj: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178). 

Warunki i postanowienia oferty są zdefiniowane na karcie Marketplace podczas tworzenia oferty w portalu Cloud Partner. Opcja standardowej umowy jest włączona, zmieniając ustawienie Yes (tak).

![Włączenie opcji standardowej umowy](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Jeśli zdecydujesz się używać standardowej umowy, oddzielne warunki i postanowienia są nadal wymagane dla [Cloud Solution Provider](./cloud-solution-providers.md) kanału.

## <a name="standard-contract-amendments"></a>Zmiany do standardowej umowy

Standardowa zmiany umowy umożliwia wydawcom zaznacz kontrakt standardowego dla uproszczenia i niestandardowe warunki ich produktu lub business.  Klienci muszą tylko Przejrzyj zmiany umowy, jeśli masz już przejrzane i zaakceptowane standardowej umowy Microsoft.

Dostępne są dwa rodzaje zmian dla wydawców w portalu Azure Marketplace:

* Uniwersalne zmiany: Zmiany te są powszechnie stosowane do standardowej umowy, dla wszystkich klientów. Uniwersalne zmiany są wyświetlane na każdy klient korzystający z produktu w procesie zakupu.

![Uniwersalne zmiany](media/marketplace-publishers-guide/universal-amendaments.png)

* Niestandardowe zmiany: Portal Azure Marketplace zawiera również świadczenia niestandardowych zmian, przeznaczone dla dzierżaw. Są to specjalne zmiany do standardowej umowy, które są przeznaczone tylko dla niektórych klientów. Wydawcy można wybrać dzierżawy, do której mają być docelowe. Klienci z tej dzierżawy będzie kupić produkt w aplecie standardowej umowy i zmiany docelowego.

![Niestandardowe zmiany](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Klienci docelowi niestandardowe zmiany otrzymają również universal poprawce do standardowych terminów podczas zakupu.

>[!Note]
>Następujące typy oferty pomocy technicznej Standard zmiany umowy: Aplikacje platformy Azure (szablony rozwiązań i zarządzanych aplikacji), maszyny wirtualne, kontenery, aplikacje kontenera.

### <a name="customer-experience"></a>Komfort

W procesie zakupu w witrynie Azure portal klienci będą mogli zobaczyć warunki skojarzony z produktem standardowej umowy firmy Microsoft oraz zmiany.

![Środowisko portalu klienta na platformie Azure.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>interfejs API

Klienci mogą korzystać z `Get-AzureRmMarketplaceTerms` pobierania warunki oferty i zaakceptuj je. W danych wyjściowych polecenia cmdlet zostaną zwrócone do standardowej umowy i zmianami skojarzone.

---

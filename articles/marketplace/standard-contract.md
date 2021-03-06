---
title: Umowa standardowa | Azure
description: Umowa standardowa dla portalu Azure Marketplace i usługi AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: dsindona
ms.openlocfilehash: 00a83a1b3005043f317ed49cafa735540cd21793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284963"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Umowa standardowa dla komercyjnego rynku firmy Microsoft

Aby uprościć proces zaopatrzenia dla klientów i zmniejszyć złożoność prawną dostawców oprogramowania, firma Microsoft oferuje umowę standardową dla komercyjnego rynku firmy Microsoft w celu ułatwienia transakcji na rynku. Zamiast tworzyć niestandardowe warunki, wydawcy komercyjnego rynku mogą zdecydować się na oferowanie swojego oprogramowania w ramach standardowej umowy, którą klienci muszą zweryfikować i zaakceptować tylko raz. Umowę standardową można znaleźć [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)tutaj: .

Warunki oferty są definiowane podczas tworzenia oferty w Centrum partnerów lub w portalu Cloud Partner Portal. Zamiast udostępniać własne niestandardowe warunki, można wybrać opcję korzystania z kontraktu standardowego dla komercyjnego portalu Microsoft marketplace.

>[!Note]
>Po opublikowaniu oferty przy użyciu umowy standardowej dla komercyjnego rynku firmy Microsoft nie można korzystać z własnych niestandardowych warunków. Jest to scenariusz "lub". Oferujesz swoje rozwiązanie w ramach umowy standardowej *lub* własnych warunków. Jeśli chcesz zmodyfikować warunki Umowy Standardowej, możesz to zrobić poprzez standardowe zmiany umowy.

## <a name="standard-contract-amendments"></a>Zmiany w umowie standardowej

Standardowe zmiany umowy umożliwiają wydawcom wybór umowy standardowej dla uproszczenia i dostosowanych warunków dla ich produktu lub firmy. Klienci muszą zapoznać się ze zmianami umowy tylko wtedy, gdy już przejrzeli i zaakceptowali umowę standardową firmy Microsoft.

Wydawcy rynku komercyjnego mają do dyspozycji dwa rodzaje zmian:

* Uniwersalne zmiany: Zmiany te są powszechnie stosowane do umowy standardowej dla wszystkich klientów. Uniwersalne zmiany są wyświetlane każdemu klientowi oferty w przepływie zakupu. Klienci muszą zaakceptować warunki Umowy Standardowej i zmiany, zanim będą mogli skorzystać z Twojej oferty.

* Zmiany niestandardowe: Te zmiany są specjalnymi zmianami umowy standardowej, które są przeznaczone dla określonych klientów tylko za pośrednictwem identyfikatorów dzierżawy platformy Azure. Wydawcy mogą wybrać dzierżawę, na którą mają być kierowane. Tylko klienci z najemcy zostaną przedstawieni z niestandardowymi warunkami zmian w przepływie zakupu oferty.  Klienci muszą zaakceptować warunki Umowy Standardowej i zmiany, zanim będą mogli skorzystać z Twojej oferty.

>[!Note]
>Te dwa rodzaje poprawek kumulują się jeden na drugim. Klienci objęci niestandardowymi zmianami otrzymają również uniwersalną zmianę umowy standardowej podczas zakupu.

Umowę standardową dla komercyjnego portalu Microsoft można wykorzystać dla następujących typów ofert: Aplikacje platformy Azure (szablony rozwiązań i aplikacje zarządzane), maszyny wirtualne, kontenery, aplikacje kontenerów, moduły usługi IoT edge i usługi SaaS .

## <a name="customer-experience"></a>obsługa klienta

Podczas odnajdowania w portalu Azure marketplace lub AppSource klienci będą mogli zobaczyć warunki skojarzone z ofertą jako umowę standardową dla komercyjnego portalu Microsoft i wszelkie uniwersalne zmiany.

![Środowisko odnajdywania klientów w witrynie Azure portal.](media/marketplace-publishers-guide/azure-discovery-process.png)

Podczas procesu zakupu w witrynie Azure portal klienci będą mogli zobaczyć warunki skojarzone z ofertą jako umowę standardową dla komercyjnego rynku firmy Microsoft oraz wszelkie uniwersalne i/lub specyficzne dla dzierżawy zmiany.

![Środowisko zakupu klienta w witrynie Azure portal.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>interfejs API

Klienci mogą używać Get-AzureRmMarketplaceTerms, aby pobrać warunki oferty i zaakceptować ją. Umowa standardowa i związane z nim zmiany zostaną zwrócone w danych wyjściowych polecenia cmdlet.

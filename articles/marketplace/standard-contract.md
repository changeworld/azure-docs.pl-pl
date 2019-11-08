---
title: Kontrakt standardowy | Azure
description: Kontrakt standardowy w witrynie Azure Marketplace i AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819493"
---
# <a name="standard-contract"></a>Kontrakt standardowy

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy szablon kontraktu, aby ułatwić obsługę transakcji w portalu Marketplace. Zamiast przedstawiać niestandardowe warunki i postanowienia, wydawcy portalu Azure Marketplace mogą zaoferować swoje oprogramowanie w ramach standardowej umowy, której klienci muszą jedynie Zweryfikuj i akceptować. Kontrakt standardowy można znaleźć tutaj: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

Warunki i postanowienia oferty są zdefiniowane na karcie Marketplace podczas tworzenia oferty w portal Cloud Partner. Opcja kontraktu standardowego jest włączona, zmieniając ustawienie na tak.

![Włączanie opcji kontraktu standardowego](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Jeśli zdecydujesz się na korzystanie z kontraktu standardowego, w przypadku kanału [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md) nadal są wymagane oddzielne warunki i postanowienia.

## <a name="standard-contract-amendments"></a>Standardowe zmiany kontraktu

Standardowe zmiany umów umożliwiają wydawcom wybranie standardowej kontraktu dla uproszczenia oraz dostosowane warunki dla ich produktu lub firmy.  Klienci muszą jedynie przejrzeć zmiany w umowie, jeśli zostali już zrecenzowani i zaakceptowali umowę standardową firmy Microsoft.

Istnieją dwa rodzaje zmian dostępnych dla wydawców portalu Azure Marketplace:

* Uniwersalne zmiany: te zmiany są powszechnie stosowane do standardowej umowy dla wszystkich klientów. Uniwersalne zmiany są wyświetlane dla każdego klienta produktu w przepływie zakupu.

![Uniwersalne zmiany](media/marketplace-publishers-guide/universal-amendaments.png)

* Zmiany niestandardowe: Witryna Azure Marketplace udostępnia również niestandardowe zmiany przeznaczone dla dzierżawców. Są to specjalne zmiany w standardowej umowie, które są przeznaczone tylko dla niektórych klientów. Wydawcy mogą wybrać dzierżawcę, do którego chce się kierować. Klienci z tej dzierżawy kupują produkt zgodnie z umową standardowa i wprowadzanymi zmianami.

![Zmiany niestandardowe](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Klienci z niestandardowymi zmianami otrzymają także uniwersalną zmianę standardowych warunków podczas zakupu.

>[!Note]
>Następujące typy ofert obsługują standardowe zmiany kontraktu: aplikacje platformy Azure (szablony rozwiązań i zarządzane aplikacje), Virtual Machines, kontenery i aplikacje kontenerów.

### <a name="customer-experience"></a>Obsługa klienta

W ramach procesu zakupu w Azure Portal klienci będą mogli zobaczyć warunki związane z produktem jako kontrakt standardowy firmy Microsoft oraz zmiany.

![Azure Portal środowiska klienta.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>Interfejs API

Klienci mogą używać `Get-AzureRmMarketplaceTerms`, aby pobrać warunki oferty i zaakceptować ją. W danych wyjściowych polecenia cmdlet zostanie zwrócony kontrakt standardowy i skojarzone zmiany.

---

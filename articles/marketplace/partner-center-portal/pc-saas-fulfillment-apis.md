---
title: Interfejsy API realizacji SaaS | Azure Marketplace
description: Przedstawia wersje interfejsów API realizacji, które umożliwiają integrację ofert SaaS z usługą Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275734"
---
# <a name="saas-fulfillment-apis"></a>Interfejsy API realizacji SaaS

Interfejsy API realizacji usługi SaaS umożliwiają niezależnym dostawcom oprogramowania (ISV) zintegrowanie aplikacji SaaS z witryną Azure Marketplace. Te interfejsy API umożliwiają aplikacjom isv uczestnictwo we wszystkich kanałach z włączoną usługą commerce: bezpośrednim, prowadzonym przez partnera (sprzedawca) i kierowanym w terenie.  Są one wymagane do wyświetlania ofert SaaS transakcji w portalu Azure Marketplace.

> [!WARNING]
> Bieżąca wersja tego interfejsu API jest w wersji 2, która powinna być używana dla wszystkich nowych ofert SaaS.  Wersja 1 interfejsu API jest przestarzała i jest utrzymywana w celu obsługi istniejących ofert.


## <a name="business-model-support"></a>Obsługa modeli biznesowych

Ten interfejs API obsługuje następujące możliwości modelu biznesowego; Można:

* Określ wiele planów oferty. Plany te mają różne funkcje i mogą być wyceniane inaczej.
* Zapewnij ofertę na stronie lub na podstawie modelu rozliczeniowego użytkownika.
* Zapewnij miesięczne i roczne opcje rozliczeń (opłacone z góry).
* Zapewnienie klientowi prywatnych cen na podstawie wynegocjowanej umowy handlowej.


## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, zarejestruj aplikację SaaS w [witrynie Azure portal,](https://ms.portal.azure.com) jak wyjaśniono w [rejestrze aplikacji usługi Azure AD](./pc-saas-registration.md).  Następnie użyj najnowszej wersji tego interfejsu do opracowania: [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md).

---
title: Interfejsy API realizacji SaaS | Portal Azure Marketplace
description: Wprowadza wersje interfejsów API realizacji, które umożliwiają integrację ofert SaaS z portalem Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: ebfc278d09c244970df5807df1505295fe7016c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819119"
---
# <a name="saas-fulfillment-apis"></a>Interfejsy API realizacji SaaS

Interfejsy API realizacji SaaS zapewniają niezależnym dostawcom oprogramowania (ISV) integrację swoich aplikacji SaaS z portalem Azure Marketplace. Te interfejsy API umożliwiają aplikacjom niezależnego dostawcy oprogramowania uczestnictwo we wszystkich kanałach z obsługą handlu: bezpośrednich, partnerskich (odsprzedawców) i na terenie.  Są one wymagane do tworzenia listy ofert SaaS transakcyjnych w portalu Azure Marketplace.

> [!WARNING]
> Bieżąca wersja tego interfejsu API jest w wersji 2, która powinna być używana dla wszystkich nowych ofert SaaS.  Wersja 1 interfejsu API jest przestarzała i jest utrzymywana do obsługi istniejących ofert.


## <a name="business-model-support"></a>Obsługa modelu biznesowego

Ten interfejs API obsługuje następujące możliwości modelu biznesowego: Można:

* Określ wiele planów dla oferty. Te plany mają różne funkcje i mogą być wyceniane w różny sposób.
* Oferowanie oferty dla poszczególnych witryn lub poszczególnych modeli rozliczeń na użytkownika.
* Zapewnianie miesięcznych i rocznych (płatnych z góry) opcji rozliczeń.
* Podawanie klientom prywatnych cen w oparciu o negocjowaną umowę biznesową.


## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, zarejestruj swoją aplikację SaaS w [Azure Portal](https://ms.portal.azure.com) zgodnie z opisem w temacie [Rejestrowanie aplikacji usługi Azure AD](./pc-saas-registration.md).  Następnie użyj najnowszej wersji tego interfejsu na potrzeby programowania: [SaaS realizacji interfejsu API w wersji 2](./pc-saas-fulfillment-api-v2.md).

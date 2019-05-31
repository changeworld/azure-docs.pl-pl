---
title: Interfejsami API usług SaaS realizacji | Portal Azure Marketplace
description: Wprowadza wersje realizacji, oferowanych przez interfejsy API, które umożliwiają integrowanie usługi SaaS przy użyciu portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 7896ed77d9dbb3358ddb1c809ca342828280f66a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258927"
---
# <a name="saas-fulfillment-apis"></a>Interfejsy API realizacji SaaS

Interfejsy API z realizacji SaaS umożliwiają niezależnym dostawcom oprogramowania (ISV), aby zintegrować swoje aplikacje SaaS przy użyciu portalu Azure Marketplace. Te interfejsy API umożliwiają aplikacjom niezależnego dostawcy oprogramowania do wzięcia udziału w wszystkie kanały commerce włączone: bezpośrednie, prowadzonej przez partnera (odsprzedawcy) i doprowadziło do pola.  Są one wymagane do wyświetlania listy transactable ofert SaaS w portalu Azure Marketplace.

> [!WARNING]
> Bieżąca wersja tego interfejsu API jest w wersji 2, który powinien być używany dla wszystkich nowych ofert SaaS.  W wersji 1 interfejsu API jest przestarzały i jest już uaktualniana do obsługi istniejących ofert.


## <a name="business-model-support"></a>Wsparcia modelu biznesowego

Ten interfejs API obsługuje następujące funkcje modeli biznesowych; Można:

* Określ wiele planów do oferty. Te plany mają różne funkcje i mogą być różne ceny.
* Udostępnić ofertę w każdej lokacji lub na użytkownika modelu rozliczeń.
* Podaj miesięczne i roczne (płatność z góry) opcjami rozliczania.
* Podaj prywatnej cen oparta na umowie biznesowej wynegocjowanym klientowi.


## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze tego nie zrobiono, Zarejestruj swoją aplikację SaaS w [witryny Azure portal](https://ms.portal.azure.com) zgodnie z objaśnieniem w [zarejestrować aplikację usługi Azure AD](./pc-saas-registration.md).  Następnie na użytek najbardziej aktualną wersję tego interfejsu programowania: [Wersja interfejsu API realizacji SaaS 2](./pc-saas-fulfillment-api-v2.md).

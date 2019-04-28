---
title: Interfejsy API z realizacji SaaS — witryna Azure Marketplace | Dokumentacja firmy Microsoft
description: Wprowadza wersje realizacji, oferowanych przez interfejsy API, które umożliwiają integrowanie usługi SaaS przy użyciu portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101357"
---
# <a name="saas-fulfillment-apis"></a>Interfejsy API realizacji SaaS

Interfejsy API z realizacji SaaS umożliwiają niezależnym dostawcom oprogramowania (ISV), aby zintegrować swoje aplikacje SaaS przy użyciu portalu Azure Marketplace. Te interfejsy API umożliwiają aplikacjom niezależnego dostawcy oprogramowania do wzięcia udziału w wszystkie kanały commerce włączone: bezpośrednie, prowadzonej przez partnera (odsprzedawcy) i doprowadziło do pola.  Są one wymagane do wyświetlania listy transactable ofert SaaS w portalu Azure Marketplace.

> [!WARNING]
> Bieżąca wersja tego interfejsu API jest w wersji 2, który powinien być używany dla wszystkich nowych SaaS oferuje.  W wersji 1 interfejsu API jest przestarzały i jest już uaktualniana do obsługi istniejących ofert.


## <a name="business-model-support"></a>Wsparcia modelu biznesowego

Ten interfejs API obsługuje następujące funkcje modeli biznesowych; Można:

* Określ wiele planów do oferty. Te plany mają różne funkcje i mogą być różne ceny.
* Udostępnić ofertę w każdej lokacji lub na użytkownika modelu rozliczeń.
* Podaj miesięczne i roczne (płatność z góry) opcjami rozliczania.
* Podaj prywatnej cen oparta na umowie biznesowej wynegocjowanym klientowi.


## <a name="next-steps"></a>Kolejne kroki

Jeśli jeszcze tego nie zrobiono, Zarejestruj swoją aplikację SaaS w [witryny Azure portal](https://ms.portal.azure.com) zgodnie z objaśnieniem w [zarejestrować aplikację usługi Azure AD](./cpp-saas-registration.md).  Następnie na użytek najbardziej aktualną wersję tego interfejsu programowania: [Wersja interfejsu API realizacji SaaS 2](./cpp-saas-fulfillment-api-v2.md).

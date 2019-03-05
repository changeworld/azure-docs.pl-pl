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
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 9c3fbe7cd7ebd41f59be360f40d66b8d38dbce5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318936"
---
# <a name="saas-fulfillment-apis"></a>Interfejsy API realizacji SaaS

Interfejs API realizacji SaaS umożliwia niezależnym dostawcom oprogramowania (ISV), aby zintegrować swoje aplikacje SaaS przy użyciu portalu Azure Marketplace. Ten interfejs API umożliwia aplikacji niezależnych dostawców oprogramowania do wzięcia udziału w wszystkie kanały commerce włączone: bezpośrednie, prowadzonej przez partnera (odsprzedawcy) i doprowadziło do pola.  Ten interfejs API jest wymagana dla list transactable SaaS oferty w portalu Azure Marketplace.

Bieżąca wersja tego interfejsu API jest w wersji 2, który powinien być używany dla wszystkich nowych SaaS oferuje.  W wersji 1 interfejsu API jest przestarzały i jest już uaktualniana do obsługi istniejących ofert.


## <a name="business-model-support"></a>Wsparcia modelu biznesowego

Ten interfejs API obsługuje następujące funkcje modeli biznesowych; Można:

* Określ wiele planów do oferty. Te plany mają różne funkcje i mogą być różne ceny.
* Udostępnić ofertę w każdej lokacji lub na użytkownika modelu rozliczeń.
* Podaj miesięczne i roczne (płatność z góry) opcjami rozliczania.
* Podaj prywatnej cen oparta na umowie biznesowej wynegocjowanym klientowi.


## <a name="next-steps"></a>Kolejne kroki

Użyj najnowszej wersji tego interfejsu do tworzenia aplikacji: [Wersja interfejsu API realizacji SaaS 2](./cpp-saas-fulfillment-api-v2.md).

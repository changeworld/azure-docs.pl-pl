---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66271570"
---
| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Ustawienia automatycznego skalowania |100 na region na subskrypcję. | Taka sama jak domyślna. |
| Alerty metryki (wersja klasyczna) |100 aktywnych reguł alertów na subskrypcję. | Z działem pomocy technicznej. |
| Alerty dotyczące metryk |1000 aktywnych reguł alertów na subskrypcję (w chmurach publicznych) i 100 aktywnych reguł alertów na subskrypcję w chińskiej wersji platformy Azure i Azure dla instytucji rządowych. | Z działem pomocy technicznej. |
| Alerty dotyczące dziennika aktywności | 100 aktywnych reguł alertów na subskrypcję. | Taka sama jak domyślna. |
| Alerty dotyczące dzienników | 512 | Z działem pomocy technicznej. |
| Grupy akcji |2000 grup akcji na subskrypcję. | Z działem pomocy technicznej. |

**Limity dotyczące grupy akcji**

| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Wypychanie aplikacji platformy Azure | 10 działania aplikacji platformy azure dla każdej grupy akcji. | Z działem pomocy technicznej. |
| Email | 1000 akcji poczty e-mail do grupy akcji. Zobacz też [ograniczania informacje o szybkości](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Z działem pomocy technicznej. |
| ITSM | Akcje ITSM 10 grupy akcji. | Z działem pomocy technicznej. | 
| Aplikacja logiki | 10 akcje aplikacji logic app do grupy akcji. | Z działem pomocy technicznej. |
| Element Runbook | 10 działania elementu runbook w grupy akcji. | Z działem pomocy technicznej. |
| SMS | 10 akcje programu SMS w grupy akcji. Zobacz też [ograniczania informacje o szybkości](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Z działem pomocy technicznej. |
| Połączenia głosowe | 10 akcje głosu w grupy akcji. Zobacz też [ograniczania informacje o szybkości](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Z działem pomocy technicznej. |
| Webhook | 10 Akcje elementu webhook w grupy akcji.  Maksymalna liczba wywołań elementu webhook jest 1500 na minutę na subskrypcję. Inne limity są dostępne pod adresem [informacje specyficzne dla działania](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Z działem pomocy technicznej. |

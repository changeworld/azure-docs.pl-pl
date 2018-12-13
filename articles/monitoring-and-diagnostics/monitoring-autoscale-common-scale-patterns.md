---
title: Omówienie typowe wzorce skalowania automatycznego
description: Dowiedz się, niektóre typowe wzorce automatycznego skalowania zasobu na platformie Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 7fa2cbc831c6bcb734b4a4596a3f75f81e8ee085
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317036"
---
# <a name="overview-of-common-autoscale-patterns"></a>Omówienie typowe wzorce skalowania automatycznego
W tym artykule opisano niektóre typowe wzorce skalowania zasobu na platformie Azure.

Skalowanie automatyczne platformy Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usług w chmurze](https://azure.microsoft.com/services/cloud-services/), [App Service — Web Apps](https://azure.microsoft.com/services/app-service/web/), i [usługi API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Umożliwia rozpoczęcie pracy

W tym artykule założono, że czytelnik zna automatycznego skalowania. Możesz [Rozpocznij tutaj skalować zasób][1]. Poniżej przedstawiono niektóre typowe wzorce skalowania.

## <a name="scale-based-on-cpu"></a>Skalowanie procesora CPU

Masz aplikację sieci web (/ zestawu skalowania maszyn wirtualnych/rola usługi w chmurze) a

- Chcesz skalowania poza/skalowania do wewnątrz na podstawie procesora CPU.
- Ponadto należy upewnić się, że minimalna liczba wystąpień.
- Ponadto chcesz mieć pewność, ustawienie limitu maksymalnej liczby wystąpień, które można skalować na potrzeby.

![Skalowanie procesora CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skalować w różny sposób w weekendy vs dni tygodnia

Masz aplikację sieci web (/ zestawu skalowania maszyn wirtualnych/rola usługi w chmurze) a

- Ma domyślnie 3 wystąpień (w dni robocze)
- Ruch nie powinien w weekendy, i dlatego ma być skalowana w dół, wystąpienia: 1 w weekendy.

![Skalować w różny sposób w weekendy vs dni tygodnia][3]

## <a name="scale-differently-during-holidays"></a>Skalować w różny sposób podczas dni wolnych od pracy

Masz aplikację sieci web (/ zestawu skalowania maszyn wirtualnych/rola usługi w chmurze) a

- Chcesz skalowanie w górę/w dół na podstawie użycia procesora CPU, domyślnie
- Jednak w okresie świątecznym (lub określone dni, które są istotne dla Twojej firmy) mają być zastąpi wartości domyślne i mają więcej pojemności do dyspozycji.

![Dni wolne inaczej w skali][4]

## <a name="scale-based-on-custom-metric"></a>Skali, w oparciu o metryki niestandardowe

Masz frontonu sieci web i warstwę interfejsu API, który komunikuje się z wewnętrznej bazy danych.

- Aby skalować warstwę interfejsu API na podstawie niestandardowych zdarzeń frontonu (przykład: Chcesz skalować proces wyewidencjonowania na podstawie liczby elementów w koszyku)

![Skali, w oparciu o metryki niestandardowe][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png

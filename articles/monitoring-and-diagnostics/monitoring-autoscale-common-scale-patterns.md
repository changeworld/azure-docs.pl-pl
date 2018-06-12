---
title: Omówienie typowe wzorce skalowania automatycznego
description: Dowiedz się, niektóre typowe wzorce do automatycznego skalowania zasobu na platformie Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 84727ec3694f64d40ad002a248a255df9074d7f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263265"
---
# <a name="overview-of-common-autoscale-patterns"></a>Omówienie typowe wzorce skalowania automatycznego
W tym artykule opisano niektóre typowe wzorce skalowania zasobu na platformie Azure.

Azure automatyczne skalowanie monitora dotyczy tylko zestawów skali maszyny wirtualnej (VMSS), usługi w chmurze, planów usługi aplikacji i środowiska usługi app service. 

# <a name="lets-get-started"></a>Umożliwia wprowadzenie

W tym artykule przyjęto założenie, że czytelnik zna automatyczne skalowanie. Możesz [tutaj wprowadzenie do skalowania zasobu][1]. Poniżej przedstawiono niektóre typowe wzorce skali.

## <a name="scale-based-on-cpu"></a>Oparte na Procesorze skali

Aplikacja sieci web (/ VMSS/chmury usługi roli) i 

- Aby skalowania w poziomie/skali w na podstawie procesora CPU.
- Ponadto chcesz upewnij się, że minimalna liczba wystąpień. 
- Ponadto chcesz zapewnić ustawienie limitu maksymalnej liczby wystąpień, które można skalować.

![Oparte na Procesorze skali][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skalowanie inaczej w weekendy vs dni tygodnia

Aplikacja sieci web (/ VMSS/chmury usługi roli) i

- Ma domyślnie wystąpień 3 (w dni robocze)
- Nieoczekiwanie ruchu w weekendy, i dlatego ma być skalowana do 1 wystąpienie w weekendy.

![Skalowanie inaczej w weekendy vs dni tygodnia][3]

## <a name="scale-differently-during-holidays"></a>Skalowanie inaczej w dni wolne

Aplikacja sieci web (/ VMSS/chmury usługi roli) i 

- Chcesz skalować w górę/dół oparte na użycie procesora CPU domyślnie
- Jednak podczas świąt (lub określone dni, które są ważne dla biznesu) chcesz zastąpić wartości domyślne i mieć większej pojemności do Twojej dyspozycji.

![Święta inaczej w skali][4]

## <a name="scale-based-on-custom-metric"></a>Skalowanie w oparciu metryki niestandardowe

Masz frontonu sieci web i warstwy interfejsu API, który komunikuje się z wewnętrznej bazy danych. 

- Aby skalować warstwę interfejsu API na podstawie niestandardowych zdarzeń frontonu (przykład: chcesz skalować procesu wyewidencjonowania na podstawie liczby elementów w koszyku)

![Skalowanie w oparciu metryki niestandardowe][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png
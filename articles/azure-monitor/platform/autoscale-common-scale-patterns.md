---
title: Przegląd typowych wzorców autoskalowania
description: Poznaj niektóre typowe wzorce, aby automatycznie skalować zasób na platformie Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396090"
---
# <a name="overview-of-common-autoscale-patterns"></a>Przegląd typowych wzorców autoskalowania
W tym artykule opisano niektóre typowe wzorce skalowania zasobu na platformie Azure.

Skalowanie automatyczne usługi Azure Monitor dotyczy tylko [zestawów skalowania maszyn wirtualnych,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [usług w chmurze,](https://azure.microsoft.com/services/cloud-services/) [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/)i usług [zarządzania interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Zacznijmy od pracy

W tym artykule przyjęto założenie, że znasz skalowanie automatyczne. Możesz [rozpocząć tutaj skalowanie zasobu][1]. Poniżej przedstawiono niektóre z typowych wzorców skali.

## <a name="scale-based-on-cpu"></a>Skala oparta na procesorze

Masz aplikację internetową (/VMSS/rolę usługi w chmurze) i

- Chcesz skalować w poziomie/skalować w oparciu o procesor CPU.
- Ponadto chcesz upewnić się, że istnieje minimalna liczba wystąpień.
- Ponadto chcesz upewnić się, że ustawiono maksymalny limit liczby wystąpień, do których można skalować.

![Skala oparta na procesorze][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skaluj się inaczej w dni powszednie i w weekendy

Masz aplikację internetową (/VMSS/rolę usługi w chmurze) i

- Chcesz 3 wystąpienia domyślnie (w dni powszednie)
- Nie spodziewasz się ruchu w weekendy, a zatem chcesz zmniejszyć się do 1 wystąpienia w weekendy.

![Skaluj się inaczej w dni powszednie i w weekendy][3]

## <a name="scale-differently-during-holidays"></a>Skaluj się inaczej podczas wakacji

Masz aplikację internetową (/VMSS/rolę usługi w chmurze) i

- Domyślnie chcesz skalować w górę/w dół na podstawie użycia procesora CPU
- Jednak w okresie świątecznym (lub określonych dni, które są ważne dla Twojej firmy) chcesz zastąpić ustawienia domyślne i mieć więcej możliwości do Państwa dyspozycji.

![Skaluj się inaczej w święta][4]

## <a name="scale-based-on-custom-metric"></a>Skaluj na podstawie metryki niestandardowej

Masz frontołów sieci web i warstwy interfejsu API, który komunikuje się z wewnętrznej bazy danych.

- Chcesz skalować warstwę interfejsu API na podstawie zdarzeń niestandardowych w frontonie (przykład: Chcesz skalować proces realizacji transakcji na podstawie liczby produktów w koszyku)

![Skaluj na podstawie metryki niestandardowej][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png


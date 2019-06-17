---
title: Inteligentne wykrywanie — niewielkie wykorzystanie zasobów w chmurze wykryte przez usługę Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitoruj aplikacje za pomocą usługi Azure Application Insights dla niewielkie wykorzystanie zasobów w chmurze.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7cf72068b9cabceb0c5b535986ac4dfb62151b94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61297517"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>Niski poziom wykorzystania Procesora zasobów w chmurze (wersja zapoznawcza)

Application Insights analizuje użycia procesora CPU dla wszystkich wystąpień roli w aplikacji i automatycznie wykryje wystąpienia przy niskim obciążeniu procesorów. Wykrywanie pozwala na zmniejszenie zasobów platformy Azure i obniżyć koszty, zmniejszając liczbę wystąpień roli, który korzysta z poszczególnych ról lub poprzez zmniejszenie liczby ról.

Ta funkcja wymaga nie specjalne ustawienia innego niż [Konfigurowanie liczników wydajności](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) dla aplikacji. Może to być aktywny, gdy aplikacja generuje wystarczającej ilości telemetrii licznik wydajności procesora CPU (% czasu procesora).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Gdy otrzyma ten typ powiadomienia wykrywania inteligentnego
Powiadomienie o typowych występuje, gdy wiele wystąpień roli sieć Web/proces roboczy następującej liczby etapów stwierdzono niskiego poziomu wykorzystania Procesora.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Zbyt wiele zasobów jest zdecydowanie zużywać mojej aplikacji?
Nie, powiadomienia nie oznacza, że aplikacja zdecydowanie zużywa zbyt wiele zasobów. Mimo że wzorców takiego niskiego poziomu wykorzystania Procesora zazwyczaj wskazują, czy można zmniejszyć zużycie zasobów, to zachowanie może być typową dla określonej roli użytkownika, lub może mieć uzasadnienie biznesowe fizycznych i można je zignorować. Na przykład może być, że wiele wystąpień są potrzebne do innych zasobów, takich jak pamięci i sieci, a nie procesora CPU.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi w procesie diagnostyki:
1. **Klasyfikacja.** Powiadomienie zawiera role w swojej aplikacji, które wykazują niskiego poziomu wykorzystania Procesora. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres.** Jak wiele ról uwidocznione niskiego poziomu wykorzystania Procesora i liczbę wystąpień w każdej roli wykorzystanie Procesora niski? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie.** Wykrywanie zawiera wartość procentową wykorzystania, procesora, przedstawiający wykorzystanie procesora CPU każdego wystąpienia wraz z upływem czasu. Umożliwia także elementy pokrewne i raporty — łączenie dodatkowe informacje, takie jak percentyle wykorzystanie procesora CPU, aby pomóc w dalszym diagnozowaniu problemu.

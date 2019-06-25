---
title: Wydajności i obciążenia, testowanie za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Konfigurowanie testów wydajności i obciążenia za pomocą usługi Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304956"
---
# <a name="performance-testing"></a>Testowanie wydajności

> [!NOTE]
> Usługi testowania obciążenia opartej na chmurze jest przestarzała. Więcej informacji na temat amortyzacja, dostępność usług i alternatywne usług można znaleźć [tutaj](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Usługa Application Insights umożliwia generowanie testów obciążeniowych dla witryn sieci Web. Podobnie jak [testy dostępności](monitor-web-app-availability.md), mogą wysyłać albo podstawowe żądania lub [żądania wieloetapowe](availability-multistep.md) z platformy Azure należy przetestować agentów na całym świecie. Testy wydajności umożliwiają symulować maksymalnie 20 000 równoczesnych użytkowników przez maksymalnie 60 minut.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Aby utworzyć test wydajności, najpierw musisz utworzyć zasób usługi Application Insights. Jeśli już utworzono zasób przejść do następnej sekcji.

W witrynie Azure portal, wybierz **Utwórz zasób** > **narzędzi deweloperskich** > **usługi Application Insights** i tworzenia usługi Application Insights zasób.

## <a name="configure-performance-testing"></a>Skonfigurować testowania wydajnościowego

Jeśli po raz pierwszy tworzenia wybierz test wydajności **Ustaw organizacji** i wybrać organizację DevOps platformy Azure jako źródło dla testów wydajności.

W obszarze **Konfiguruj**, przejdź do **testowania Wydajnościowego** i kliknij przycisk **New** Aby utworzyć test.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/performance-testing/new-performance-test.png)

Aby utworzyć test wydajności podstawowe, wybierz typ testu **ręcznego testu** i wypełnij odpowiednie ustawienia dla testu.

|Ustawienie| Wartość maksymalna
|----------|------------|
| Obciążenie użytkownikami | 20,000 |
| Czas trwania (minuty)  | 60 |  

Po utworzeniu testu kliknij **Uruchom test**.

Po zakończeniu testu, będzie możliwe zobaczenie wyników, które będą przypominać poniższe wyniki:

![Wyniki tekstu](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurowanie testów sieci web programu Visual Studio

Projekty testowe zaawansowane możliwości testowania są zbudowane na podstawie wydajności programu Visual Studio i obciążenia w usłudze Application Insights.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wieloetapowy test witryny](availability-multistep.md)
* [Testów ping adresu URL](monitor-web-app-availability.md)
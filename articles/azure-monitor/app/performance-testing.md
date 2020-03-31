---
title: Testowanie wydajności i obciążenia za pomocą usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Konfigurowanie testów wydajności i ładowania za pomocą usługi Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669866"
---
# <a name="performance-testing"></a>Testowanie wydajności

> [!NOTE]
> Usługa testowania obciążenia oparta na chmurze została przestarzała. Więcej informacji na temat wycofania, dostępności usług i alternatywnych usług można znaleźć [tutaj](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Usługa Application Insights umożliwia generowanie testów obciążenia dla witryn sieci Web. Podobnie jak [testy dostępności](monitor-web-app-availability.md), można wysyłać żądania podstawowe lub [wieloetapowe żądania](availability-multistep.md) od agentów testowych platformy Azure na całym świecie. Testy wydajności pozwalają symulować do 20 000 jednoczesnych użytkowników przez maksymalnie 60 minut.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Aby utworzyć test wydajności, należy najpierw utworzyć zasób usługi Application Insights. Jeśli zasób został już utworzony, przejdź do następnej sekcji.

W witrynie Azure Portal wybierz pozycję Utwórz usługę**Aplikacje narzędzi** >  **deweloperskich zasobów** > **i** utwórz zasób usługi Application Insights.

## <a name="configure-performance-testing"></a>Konfigurowanie testowania wydajności

Jeśli jest to pierwszy raz tworzenia testu wydajności wybierz **Ustaw organizację** i wybierz organizację Azure DevOps być źródłem testów wydajności.

W obszarze **Konfigurowanie**przejdź do **testowania wydajności** i kliknij pozycję **Nowy,** aby utworzyć test.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/performance-testing/new-performance-test.png)

Aby utworzyć podstawowy test wydajności, wybierz typ testu **testu ręcznego** i wypełnij odpowiednie ustawienia testu.

|Ustawienie| Wartość maksymalna
|----------|------------|
| Obciążenie użytkownika | 20 000 |
| Czas trwania (minuty)  | 60 |  

Po utworzeniu testu kliknij przycisk **Uruchom test**.

Po zakończeniu testu zobaczysz wyniki, które wyglądają podobnie do poniższych wyników:

![Wyniki tekstu](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurowanie testu sieci Web programu Visual Studio

Zaawansowane funkcje testowania wydajności usługi Application Insights są oparte na projektach testów wydajności i obciążenia programu Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Następne kroki

* [Wieloetapowe testy sieci Web](availability-multistep.md)
* [Testy ping url](monitor-web-app-availability.md)

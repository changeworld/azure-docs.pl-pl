---
title: Ciągłe monitorowanie potoku wydania metodyki DevOps dzięki podejściu DevOps platformy Azure i usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Przedstawiono instrukcje pozwalające szybko skonfigurować monitorowania ciągłego za pomocą usługi Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 41999defb01e024773b6364f169a1ce3b1377237
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60902386"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Dodawanie ciągłego monitorowania, tak aby potok wydania

Usługom DevOps platformy Azure integruje się z usługą Azure Application Insights umożliwia ciągłe monitorowanie potoku wydania metodyki DevOps w całym cyklu życia tworzenia oprogramowania. 

Usługom DevOps platformy Azure obsługuje teraz ciągłego monitorowania, według którego potoki wydań może obejmować dane monitorowania z usługi Application Insights i innych zasobów platformy Azure. Po wykryciu alert usługi Application Insights, wdrożenie może pozostać warunkowe lub wycofana, wróć do momentu usunięcia alertu. Jeśli wszystkie wykryte, wdrożeń przejść automatycznie z testu aż do środowiska produkcyjnego, bez konieczności ręcznej interwencji. 

## <a name="configure-continuous-monitoring"></a>Skonfigurowanie funkcji ciągłego monitorowania

1. Wybierz istniejący projekt usługi Azure DevOps.

2. Umieść kursor nad **kompilowania i wydawania** > Wybierz **wersji** > kliknij **znak plus** > **Tworzenie definicji wydania** > Wyszukaj **monitorowania** > **wdrażanie usługi Azure App Service za pomocą ciągłego monitorowania.**

   ![Nowy potok wersji usługi Azure DevOps](media/continuous-monitoring/001.png)

3. Kliknij przycisk **zastosowania.**

4. Obok czerwonego wykrzyknika zaznacz tekst w kolorze niebieskim do **przeglądać zadania w środowisku.**

   ![Wyświetl zadania środowiska](media/continuous-monitoring/002.png)

   Zostanie wyświetlone okno konfiguracji, skorzystaj z poniższej tabeli, wypełnij pola wejściowe.

    | Parametr        | Wartość |
   | ------------- |:-----|
   | **Nazwa środowiska**      | Nazwa opisująca środowiska potok wydania |
   | **Subskrypcja platformy Azure** | Lista rozwijana wypełnia żadnych subskrypcji platformy Azure, połączone z organizacji usługom DevOps platformy Azure|
   | **Nazwa usługi App Service** | Ręczne wprowadzanie nowej wartości mogą być wymagane dla tego pola, w zależności od innych opcji |
   | **Grupa zasobów**    | Lista rozwijana zostaną wyświetlone wszystkie dostępne grupy zasobów |
   | **Nazwa zasobu usługi Application Insights** | Lista rozwijana wypełnia wszystkie zasoby usługi Application Insights, które odnoszą się do wcześniej wybranej grupy zasobów.

5. Wybierz **alerty Konfiguruj usługę Application Insights**

6. Domyślne reguły alertu można wybrać **Zapisz** > Wprowadź opisowy komentarz > kliknij **OK**

## <a name="modify-alert-rules"></a>Modyfikowanie reguły alertów

1. Aby zmodyfikować wstępnie zdefiniowane ustawienia alertu, kliknij w tym polu **wielokropek...**  po prawej stronie **reguły alertów.**

   (Istnieją out-of-box cztery reguły alertów: Dostępność, żądań zakończonych niepowodzeniem, czas odpowiedzi serwera, wyjątki serwera.)

2. Kliknij symbol listy rozwijanej obok **dostępności.**

3. Modyfikowanie dostępności **próg** zgodnie z wymaganiami poziomu usługi.

   ![Modyfikuj Alert](media/continuous-monitoring/003.png)

4. Wybierz **OK** > **Zapisz** > Wprowadź opisowy komentarz > kliknij **OK.**

## <a name="add-deployment-conditions"></a>Dodawanie warunków wdrożenia

1. Kliknij przycisk **potoku** > Wybierz **Pre** lub **po wdrożeniu warunków** symbolu, w zależności od etapu, na którym wymaga brama ciągłego monitorowania.

   ![Warunki wstępne wdrożenia](media/continuous-monitoring/004.png)

2. Ustaw **bramy** do **włączone** > **zatwierdzania bramy**> kliknij **Dodaj.**

3. Wybierz **usługi Azure Monitor** (Ta opcja zapewnia możliwość dostępu alerty zarówno z usługi Azure Monitor i Application Insights)

    ![Azure Monitor](media/continuous-monitoring/005.png)

4. Wprowadź **limitu czasu bramy** wartość.

5. Wprowadź **interwału próbkowania.**

## <a name="deployment-gate-status-logs"></a>Dzienniki stanu bramy wdrożenia

Po dodaniu bramy do wdrożenia, alert w usłudze Application Insights, co powoduje przekroczenie uprzednio zdefiniowany próg, chroni wdrożenia z wersji niechciane podwyższania poziomu. Gdy alert nie zostanie rozwiązany, automatycznie przejść wdrożenia.

Aby zaobserwować to zachowanie, zaznacz **wersji** > wersji kliknij prawym przyciskiem myszy nazwę **Otwórz** > **dzienniki.**

![Dzienniki](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat potoków Azure Wypróbuj te [przewodników Szybki Start.](https://docs.microsoft.com/azure/devops/pipelines)

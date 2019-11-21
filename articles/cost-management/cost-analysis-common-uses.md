---
title: Typowe zastosowania analizy kosztów w Azure Cost Management
description: W tym artykule wyjaśniono, jak uzyskać wyniki typowych zadań analizy kosztów w Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/16/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 01087f3e40e278490abb87d0ab3b7b6ab5052b6b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219492"
---
# <a name="common-cost-analysis-uses"></a>Często używane analizy kosztów

Azure Cost Management Użytkownicy często chcą udzielić odpowiedzi na pytania, które są zadawane przez wiele innych. Ten artykuł przeprowadzi Cię przez proces pobierania wyników typowych zadań analizy kosztów w Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Wyświetlanie podziału kosztów według usługi platformy Azure

Wyświetlanie kosztów według usługi platformy Azure może pomóc w lepszym zrozumieniu części infrastruktury, która jest kosztowna. Na przykład koszty obliczeniowe maszyny wirtualnej mogą być małe. W związku z tym można naliczać znaczne koszty sieci z powodu ilości informacji emitowanych z maszyn wirtualnych. Zrozumienie podstawowych sterowników kosztów usług platformy Azure jest niezbędne, aby można było dostosować użycie usługi zgodnie z potrzebami.

1. W Azure Portal przejdź do analizy kosztów dla zakresu. Na przykład: **Cost Management + Rozliczanie** > **Cost Management** > **kosztów analizy**.
1. Wybierz opcję **Koszt według usługi** , a następnie Grupuj według **warstwy usług**.
1. Zmień widok na **tabelę**.

![Podział kosztów według usługi platformy Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Wyświetlanie podziału kosztów według zasobów platformy Azure

Usługi są tworzone za pomocą zasobów platformy Azure. Przeglądanie kosztów na podstawie zasobów może ułatwić szybkie zidentyfikowanie głównych współpracowników kosztów. Jeśli usługa ma zasoby, które są zbyt kosztowne, rozważ wprowadzenie zmian w celu zmniejszenia kosztów.

1. W Azure Portal przejdź do analizy kosztów dla zakresu. Na przykład: **Cost Management + Rozliczanie** > **Cost Management** > **kosztów analizy**.
1. Wybierz pozycję **Koszt według zasobu**.
1. Zmień widok na **tabelę**.

![Wyświetlanie podziału kosztów według zasobów platformy Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Wyświetl podział kosztów według wybranych wymiarów

Wymiary umożliwiają organizowanie kosztów w oparciu o różne wartości metadanych pokazane w opłatach. Możesz na przykład grupować swoje koszty według lokalizacji.

1. W Azure Portal przejdź do analizy kosztów dla zakresu. Na przykład: **Cost Management + Rozliczanie** > **Cost Management** > **kosztów analizy**.
1. Wybierz filtr **Grupuj według** .  
    ![wybierz pozycję Grupuj według](./media/cost-analysis-common-uses/group-by.png)
1. Opcjonalnie Zapisz widok do późniejszego użycia.
1. Kliknij wykres kołowy poniżej grafu, aby wyświetlić bardziej szczegółowe dane.  
    ![Wyświetl podział kosztów według wybranych wymiarów](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Wyświetl koszty dziennie lub według miesiąca

Codzienne i miesięczne koszty mogą pomóc lepiej zrozumieć, czy istnieje czas tygodnia lub roku, w którym koszty są wyższe. Jeśli masz większy ruch klienta w okresie świątecznym, czy chcesz zwiększyć swój koszt platformy Azure? Czy w piątek jest droższe niż poniedziałek?

1. W Azure Portal przejdź do analizy kosztów dla zakresu. Na przykład: **Cost Management + Rozliczanie** > **Cost Management** > **kosztów analizy**.
1. Ustaw **stopień szczegółowości** **miesięcznie** lub **codziennie**.

![Wyświetl koszty dziennie](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Wyświetlanie kosztów dla określonego tagu

Wielu użytkowników platformy Azure stosuje znaczniki do swoich zasobów, takich jak centrum kosztów lub środowisko programistyczne (produkcyjne i testowe), aby lepiej klasyfikować opłaty. Tagi są wyświetlane jako wymiar w analizie kosztów. Możesz użyć wymiaru, aby uzyskać wgląd w niestandardowe kategoryzacje etykiet.

1. W Azure Portal przejdź do analizy kosztów dla zakresu. Na przykład: **Cost Management + Rozliczanie** > **Cost Management** > **kosztów analizy**.
1. Wybierz pozycję **Grupuj według** dla tagu.

![Wyświetlanie kosztów dla określonego tagu](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Pobierz szczegóły użycia

Plik raportu szczegóły użycia w formacie CSV zawiera podział wszystkich opłat naliczanych w ramach faktury. Możesz użyć raportu, aby porównać go z i lepiej zrozumieć swoją fakturę. Każda opłata rozliczana na fakturze odnosi się do opłat za podwyższenie poziomu w raporcie użycia.

1. W Azure Portal przejdź do karty **użycie i opłaty** dla konta rozliczeń lub subskrypcji. Na przykład: **Cost Management + Rozliczanie** > **rozliczanie** > **użycie i opłaty**.
1. Wybierz element wiersza do pobrania, a następnie kliknij symbol pobierania.  
    ![Pobierz użycie i opłaty](./media/cost-analysis-common-uses/download1.png)
1.  Wybierz plik użycia do pobrania.  
    ![Wybierz plik użycia do pobrania](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Wyświetl miesięczny podział kosztów umowy EA

Rejestracja w ramach umowy EA naliczy koszty dla całej organizacji. Zrozumienie sposobu, w jaki koszty są naliczane i są fakturowane w czasie, ułatwiają zaangażowanie właściwych uczestników projektu w celu zapewnienia, że koszty są zarządzane.

1. W Azure Portal przejdź do omówienia **Cost Management i Rozliczeń** > **Przegląd**.
1. Kliknij pozycję **podział** na bieżący miesiąc i Wyświetl swoje zobowiązanie pieniężne.  
    przegląd kosztów ![EA — podsumowanie podziału](./media/cost-analysis-common-uses/breakdown1.png)
1.  Kliknij kartę **użycie i opłaty** , a następnie Wyświetl podział poprzedniego miesiąca w wybranym przedziale czasu.  
    ![karta użycie i opłaty](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Wyświetlanie miesięcznego kosztu rejestracji według terminu

Skorzystaj z graficznego widoku kosztów miesięcznych rejestracji, aby zrozumieć trendy kosztów i zafakturowane kwoty w danym okresie.

1. W Azure Portal przejdź do analizy kosztów dla zakresu. Na przykład: **Cost Management + Rozliczanie** > **Cost Management** > **kosztów analizy**.
1. Wybierz swoją rejestrację i ustaw termin rejestracji.
1. Ustaw poziom szczegółowości na wartość miesięczny, a następnie ustaw wartość widok na **kolumnowy (skumulowany)** .

Można grupować i filtrować dane w celu uzyskania bardziej szczegółowej analizy.

![Miesięczny koszt rejestracji według terminu](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Wyświetlanie skumulowanych kosztów rejestracji w ramach umowy EA

Wyświetl łączne opłaty netto z upływem czasu, aby poznać ogólne wydatki dla organizacji w danym okresie.

1. W Azure Portal przejdź do analizy kosztów dla zakresu. Na przykład: **Cost Management + Rozliczanie** > **Cost Management** > **kosztów analizy**.
1. Wybierz swoją rejestrację, a następnie Wyświetl bieżące skumulowane koszty.

![Zakumulowane koszty związane z rejestracją](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Następne kroki
- Jeśli nie została już zakończona pierwszym przewodniku Szybki Start usługi Cost Management, zapoznaj się z adresem [zacząć analizować koszty](quick-acm-cost-analysis.md).
- Przeczytaj [dokumentację Cost Management](index.yml).

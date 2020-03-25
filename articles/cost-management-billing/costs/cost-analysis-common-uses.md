---
title: Najczęstsze zastosowania analizy kosztów w usłudze Azure Cost Management
description: W tym artykule wyjaśniono, jak uzyskać wyniki dla typowych zadań analizy kosztów w usłudze Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 02/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 901f271ac401cb985e59c434b9e6d7f8db03889f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79203104"
---
# <a name="common-cost-analysis-uses"></a>Najczęstsze zastosowania analizy kosztów

Użytkownicy usługi Azure Cost Management często poszukują odpowiedzi na pytania zadawane przez innych. Ten artykuł zawiera instrukcje, jak uzyskać wyniki dla typowych zadań analizy kosztów w usłudze Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Wyświetlanie podziału kosztów według usługi platformy Azure

Wyświetlanie kosztów według usługi platformy Azure może pomóc w lepszym zrozumieniu, które części infrastruktury generują największe koszty. Na przykład koszty obliczeniowe maszyn wirtualnych mogą być małe. Jednak ze względu na ilość informacji emitowanych przez maszyny wirtualne mogą zostać naliczone znaczne koszty sieci. Zrozumienie podstawowych czynników wpływających na koszty usług platformy Azure jest niezbędne, aby móc dostosowywać użycie usługi w zależności od potrzeb.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz pozycję **Koszt według usługi**, a następnie grupuj według **warstwy usług**.
1. Zmień widok na **Tabela**.

![Podział kosztów według usługi platformy Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Wyświetlanie podziału kosztów według zasobu platformy Azure

Usługi są tworzone za pomocą zasobów platformy Azure. Przeglądanie kosztów w oparciu o zasoby może ułatwić szybką identyfikację głównych składników kosztów. Jeśli usługa ma zasoby, które są zbyt kosztowne, rozważ wprowadzenie zmian w celu ograniczenia kosztów.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz pozycję **Koszt według zasobu**.
1. Zmień widok na **Tabela**.

![Wyświetlanie podziału kosztów według zasobu platformy Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Wyświetlanie podziału kosztów według wybranych wymiarów

Wymiary umożliwiają organizowanie kosztów w oparciu o różne wartości metadanych pokazane w opłatach. Można na przykład grupować swoje koszty według lokalizacji.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz filtr **Grupuj według**.  
    ![Wybierz pozycję Grupuj według elementu](./media/cost-analysis-common-uses/group-by.png)
1. Opcjonalnie zapisz widok do późniejszego użycia.
1. Kliknij wykres kołowy pod wykresem, aby wyświetlić bardziej szczegółowe dane.  
    ![Wyświetlanie podziału kosztów według wybranych wymiarów](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Wyświetlanie kosztów w rozbiciu dziennym i miesięcznym

Analiza dziennych i miesięcznych kosztów może pomóc lepiej zrozumieć, czy istnieją części tygodnia lub roku, kiedy koszty są wyższe. Jeśli notujesz większy ruch klientów w okresie świątecznym, czy przekłada się to na wzrost kosztów platformy Azure? Czy piątek jest bardziej kosztowny od poniedziałku?

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Ustaw **Poziom szczegółowości** na **Miesięczny** lub **Dzienny**.

![Wyświetlanie kosztów w rozbiciu dziennym](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Wyświetlanie kosztów dla określonego tagu

Wielu użytkowników platformy Azure stosuje tagi do swoich zasobów, takich jak centrum kosztów lub środowisko programistyczne (produkcyjne i testowe), aby lepiej klasyfikować opłaty. Tagi są wyświetlane jako wymiar w analizie kosztów. Można użyć wymiaru, aby uzyskać wgląd w niestandardowe kategoryzacje tagów.

Obsługa tagów dotyczy użycia zgłaszanego *po tym*, gdy tag został zastosowany do zasobu. Tagi nie są stosowane wstecznie dla celów zestawień kosztów.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz pozycję **Grupuj według** dla tagu.

![Wyświetlanie kosztów dla określonego tagu](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Pobieranie szczegółowego zestawienia użycia

Plik raportu szczegółów użycia w formacie CSV zawiera podział wszystkich opłat naliczanych w ramach faktury. Możesz użyć raportu, aby porównać go z fakturą i lepiej ją zrozumieć. Każda opłata rozliczona na fakturze ma swój odpowiednik w rozbitych opłatach w raporcie użycia.

1. W witrynie Azure Portal przejdź do karty **Użycie i opłaty** dla konta rozliczeniowego lub subskrypcji. Przykład: **Zarządzanie kosztami i rozliczenia** > **Rozliczenia** > **Użycie i opłaty**.
1. Wybierz element wiersza do pobrania, a następnie kliknij symbol pobierania.  
    ![Pobieranie użycia i opłat](./media/cost-analysis-common-uses/download1.png)
1.  Wybierz plik użycia, który chcesz pobrać.  
    ![Wybieranie pliku użycia do pobrania](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Wyświetlanie miesięcznego podziału kosztów umowy EA

Rejestracja umowy EA powoduje naliczanie kosztów dla całej organizacji. Zrozumienie sposobu, w jaki koszty są naliczane i fakturowane w czasie, ułatwia zaangażowanie właściwych uczestników projektu w celu zapewnienia, że koszty będą zarządzane w sposób odpowiedzialny.

Koszty są wyświetlane tylko dla aktywnej rejestracji. W przypadku przeniesienia rejestracji (nieaktywnej) do nowej (aktywnej) koszty dla poprzedniej rejestracji nie są wyświetlane w usłudze Cost Management.


1. W witrynie Azure Portal przejdź do obszaru **Zarządzanie kosztami i rozliczenia** > **Omówienie**.
1. Kliknij pozycję **Podział** dla bieżącego miesiąca i wyświetl podział zobowiązania pieniężnego.  
    ![Przegląd kosztów umowy EA — podsumowanie z podziałem](./media/cost-analysis-common-uses/breakdown1.png)
1.  Kliknij kartę **Użycie i opłaty** i wyświetl podział dla poprzedniego miesiąca w wybranym przedziale czasu.  
    ![Karta Użycie i opłaty](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Wyświetlanie miesięcznego kosztu rejestracji według okresu

Skorzystaj z graficznego widoku miesięcznych kosztów rejestracji, aby zrozumieć trendy kosztowe i zafakturowane kwoty w danym okresie.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz rejestrację i ustaw okres rejestracji.
1. Ustaw stopień szczegółowości na miesięczny, a następnie ustaw widok na **Kolumna     (skumulowany)** .

Można grupować i filtrować dane pod kątem ich bardziej szczegółowej analizy.

![Miesięczny koszt rejestracji według okresu](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Wyświetlanie skumulowanych kosztów rejestracji w ramach umowy EA

Wyświetl łączne opłaty netto na przestrzeni czasu, aby poznać ogólne wydatki dla organizacji w danym okresie.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz rejestrację, a następnie wyświetl bieżące skumulowane koszty.

![Skumulowane koszty rejestracji](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Następne kroki
- Jeśli pierwszy przewodnik Szybki start dla usługi Cost Management nie został jeszcze przez Ciebie ukończony, przeczytaj go w temacie [Rozpoczęcie analizowania kosztów](quick-acm-cost-analysis.md).
- Przeczytaj [dokumentację dotyczącą usługi Cost Management](../index.yml).

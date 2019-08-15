---
title: Tryb debugowania przepływu danych mapowania Azure Data Factory
description: Rozpocznij interaktywną sesję debugowania podczas kompilowania przepływów danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 945d123c0901722a527e7cc8181c91f09e4e95ec
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014523"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapowanie trybu debugowania przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="overview"></a>Omówienie

Tryb debugowania przepływu danych mapowania Azure Data Factory można włączyć przy użyciu przycisku "Debugowanie przepływu danych" w górnej części powierzchni projektowej. Podczas projektowania przepływów danych włączenie trybu debugowania pozwala interaktywnie obejrzeć transformację kształtu danych podczas kompilowania i debugowania przepływów danych. Sesja debugowania może być używana zarówno w sesjach projektu przepływu danych, jak i podczas wykonywania debugowania przez potok przepływów danych.

![Przycisk Debuguj](media/data-flow/debugbutton.png "Przycisk Debuguj")

Gdy tryb debugowania jest włączony, będziesz interaktywnie kompilować przepływ danych przy użyciu aktywnego klastra Spark. Sesja zostanie zamknięta po wyłączeniu debugowania w Azure Data Factory. Należy zwrócić uwagę na opłaty godzinowe naliczane przez Azure Databricks w czasie, gdy sesja debugowania jest włączona.

W większości przypadków dobrym sposobem jest skompilowanie przepływów danych w trybie debugowania, aby można było zweryfikować logikę biznesową i wyświetlić przekształcenia danych przed opublikowaniem pracy w Azure Data Factory. Użyj przycisku "Debuguj" w panelu potoku, aby przetestować przepływ danych w potoku.

> [!NOTE]
> Gdy lampa trybu debugowania jest zielona na pasku narzędzi Data Factory, opłata zostanie naliczona za szybkość debugowania przepływu danych wynoszącą 8 rdzeni/HR obliczeń ogólnych, a 60 minut czasu wygaśnięcia 

## <a name="cluster-status"></a>Stan klastra

Wskaźnik stanu klastra w górnej części powierzchni projektowej zmieni kolor na zielony, gdy klaster jest gotowy do debugowania. Jeśli klaster jest już grzany, zielony wskaźnik zostanie wyświetlony niemal natychmiast. Jeśli klaster nie był jeszcze uruchomiony po wprowadzeniu trybu debugowania, należy poczekać 5-7 minut, aż klaster wyjdzie. Wskaźnik zostanie zastosowany do momentu jego gotowości.

Po zakończeniu debugowania należy wyłączyć przełącznik debugowania, aby klaster Azure Databricks mógł zakończyć pracę i nie będzie już rozliczany dla działania debugowania.

## <a name="debug-settings"></a>Ustawienia debugowania

Ustawienia debugowania można edytować, klikając pozycję "ustawienia debugowania" na pasku narzędzi kanwy przepływu danych. W tym miejscu możesz wybrać limit wierszy lub źródło pliku do użycia dla każdego z przekształceń źródłowych. Limity wierszy w tym ustawieniu są tylko dla bieżącej sesji debugowania. Możesz również wybrać połączoną usługę, która ma być używana dla źródła magazynu danych SQL. 

![Ustawienia debugowania](media/data-flow/debug-settings.png "Ustawienia debugowania")

Jeśli masz parametry w przepływie danych lub w dowolnym z nich, do których istnieją odwołania, możesz określić wartości, które mają być używane podczas debugowania, wybierając kartę **Parametry** .

![Parametry ustawień debugowania](media/data-flow/debug-settings2.png "Parametry ustawień debugowania")

## <a name="data-preview"></a>Podgląd danych

Po zakończeniu debugowania na karcie Podgląd danych zostanie wystawiony dolny panel. Bez trybu debugowania w przepływie danych będzie zawierać tylko bieżące metadane z i z każdego przekształcenia na karcie Inspekcja. W podglądzie danych zostanie zbadana tylko liczba wierszy, które zostały ustawione jako limit w ustawieniach debugowania. Kliknij przycisk **Odśwież** , aby pobrać Podgląd danych.

![Podgląd danych](media/data-flow/datapreview.png "Podgląd danych")

Podczas uruchamiania w trybie debugowania w przepływie danych dane nie będą zapisywane do transformacji ujścia. Sesja debugowania jest przeznaczona do użycia jako zespół testów dla transformacji. Ujścia nie są wymagane podczas debugowania i są ignorowane w przepływie danych. Jeśli chcesz przetestować zapisywanie danych w ujściach, wykonaj przepływ danych z potoku Azure Data Factory i użyj wykonywania debugowania z potoku.

### <a name="quick-actions"></a>Szybkie akcje

Gdy zobaczysz Podgląd danych, możesz wygenerować szybką transformację, aby rzutowanie, usunąć lub zmodyfikować kolumnę. Kliknij nagłówek kolumny, a następnie wybierz jedną z opcji na pasku narzędzi Podgląd danych.

![Szybkie akcje](media/data-flow/quick-actions1.png "Szybkie akcje")

Po wybraniu modyfikacji Podgląd danych zostanie natychmiast odświeżony. Kliknij przycisk **Potwierdź** w prawym górnym rogu, aby wygenerować nowe przekształcenie.

![Szybkie akcje](media/data-flow/quick-actions2.png "Szybkie akcje")

**Rzutowanie** i **Modify** wygenerują transformację kolumn pochodnych, a **usunięcie** spowoduje wygenerowanie transformacji SELECT.

![Szybkie akcje](media/data-flow/quick-actions3.png "Szybkie akcje")

> [!NOTE]
> W przypadku edycji przepływu danych należy ponownie pobrać Podgląd danych przed dodaniem szybkiej transformacji.

### <a name="data-profiling"></a>Profilowanie danych

Po wybraniu kolumn na karcie Podgląd danych i kliknięciu pozycji **statystyki** na pasku narzędzi Podgląd danych zostanie wyświetlona tabela z prawej strony siatki danych z szczegółowymi statystykami dotyczącymi każdego pola. Azure Data Factory wykona Określanie na podstawie próbkowania danych, którego wykres ma być wyświetlany. Pola o dużej kardynalności będą domyślnie mieć wartości NULL/NOT NULL, podczas gdy kategorii i dane liczbowe o niskiej kardynalności będą wyświetlać wykresy słupkowe pokazujące częstotliwość wartości danych. Zobaczysz również maksymalną/len długość pól ciągów, minimalną/maksymalną wartość w polach liczbowych, standardowe dev, percentyly, liczniki i średnie.

![Statystyki kolumn](media/data-flow/stats.png "Statystyki kolumn")

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu kompilowania i debugowania przepływu danych [Wykonaj go z potoku.](control-flow-execute-data-flow-activity.md)
* Podczas testowania potoku przy użyciu przepływu danych Użyj [opcji wykonywania uruchamiania debugowania](iterative-development-debugging.md) potoku.

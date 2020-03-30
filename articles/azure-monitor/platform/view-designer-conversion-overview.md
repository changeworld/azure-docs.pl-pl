---
title: Przewodnik po przejściu widoku usługi Azure Monitor do skoroszytów
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658697"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Przewodnik po przejściu widoku usługi Azure Monitor do skoroszytów
[Projektant widoku](view-designer.md) jest funkcją usługi Azure Monitor, która umożliwia tworzenie widoków niestandardowych ułatwiające wizualizację danych w obszarze roboczym usługi Log Analytics za pomocą wykresów, list i osi czasu. Są one stopniowo wycofywane i zastępowane skoroszytami, które zapewniają dodatkowe funkcje. Ten artykuł zawiera omówienie procesu konwertowania istniejących widoków na skoroszyty.

## <a name="workbooks-overview"></a>Omówienie skoroszytów
[Skoroszyty](../insights/vminsights-workbooks.md) łączą tekst, [zapytania dziennika,](../log-query/query-language.md)metryki i parametry w zaawansowane raporty interaktywne. Członkowie zespołu z tym samym dostępem do zasobów platformy Azure mogą również edytować skoroszyty.

Skoroszyty są przydatne w scenariuszach, takich jak:

-   Eksplorowanie użycia maszyny wirtualnej, gdy nie znasz metryki zainteresowania z wyprzedzeniem: wykorzystanie procesora CPU, miejsce na dysku, pamięci, zależności sieciowe, itp. W przeciwieństwie do innych narzędzi do analizy użycia skoroszyty umożliwiają łączenie wielu rodzajów wizualizacji i analiz, dzięki czemu doskonale nadają się do tego rodzaju eksploracji swobodnej.
-   Wyjaśniając zespołowi, jak działa niedawno aprowizowana maszyna wirtualna, pokazując metryki dla liczników kluczy i innych zdarzeń dziennika.
-   Udostępnianie wyników eksperymentu zmiany rozmiaru maszyny wirtualnej innym członkom zespołu. Możesz wyjaśnić cele eksperymentu za pomocą tekstu, a następnie wyświetlić wszystkie metryki użycia i zapytania analityczne używane do oceny eksperymentu, a także wyraźne wywołania, czy każda metryka była powyżej lub poniżej celu.
-   Raportowanie wpływu awarii na użycie maszyny Wirtualnej, łączenie danych, wyjaśnienie tekstu i omówienie kolejnych kroków, aby zapobiec awariom w przyszłości.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Dlaczego warto konwertować pulpity nawigacyjne projektanta widoków na skoroszyty?

Projektant widoku oferuje możliwość generowania różnych widoków i wizualizacji opartych na zapytaniach. Wiele dostosowań najwyższego poziomu pozostaje jednak ograniczonych, na przykład formatowanie układów siatek i kafelków czy wybieranie alternatywnej grafiki mającej reprezentować dane. Projektant widoku jest ograniczony do łącznie dziewięciu różnych kafelków do reprezentowania danych.

Skoroszyty to platforma, która uwalnia pełny potencjał danych. skoroszyty nie tylko zachowują wszystkie możliwości, ale także obsługują dodatkowe funkcje za pomocą tekstu, metryk, parametrów i wielu innych. Na przykład skoroszyty umożliwiają użytkownikom konsolidowanie gęstych siatek i dodawanie pasków wyszukiwania w celu łatwego filtrowania i analizowania danych. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Zalety używania skoroszytów w projektancie widoku

* Obsługuje zarówno dzienniki, jak i metryki.
* Umożliwia zarówno widoki osobiste dla widoków indywidualnej kontroli dostępu, jak i udostępnionych skoroszytów.
* Niestandardowe opcje układu z zakładkami, rozmiarami i kontrolkami skalowania.
* Obsługa wykonywania zapytań w wielu obszarach roboczych usługi Log Analytics, aplikacjach usługi Application Insights i subskrypcjach.
* Włącza parametry niestandardowe, które dynamicznie aktualizują skojarzone wykresy i wizualizacje.
* Obsługa galerii szablonów z publicznego programu GitHub.

Ten przewodnik oferuje proste kroki bezpośredniego odtworzenia kilku najczęściej używanych widoków projektanta widoków, ale skoroszyty umożliwiają użytkownikom swobodę tworzenia i projektowania własnych wizualizacji i metryk niestandardowych. Poniższy zrzut ekranu pochodzi z [szablonu użycia obszaru roboczego](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) i przedstawia przykład tego, jakie skoroszyty są w stanie utworzyć:


![Przykład aplikacji skoroszytów](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Jak rozpocząć korzystanie ze skoroszytów
Otwieranie skoroszytów ze skoroszytów jest włączone w obszarach roboczych usługi Log Analytics jako element na bocznym pasku nawigacyjnym, bezpośrednio pod lokalizacją projektanta widoku.

![Nawigacja do skoroszytów](media/view-designer-conversion-overview/workbooks-nav.png)

Po wybraniu zostanie wyświetlona galeria zawierająca listę wszystkich zapisanych skoroszytów i szablonów dla obszaru roboczego.

![Galeria skoroszytów](media/view-designer-conversion-overview/workbooks-gallery.png)

Aby rozpocząć nowy skoroszyt, możesz wybrać **pusty** szablon w obszarze **Szybki start**lub ikonę **Nowy** na górnym pasku nawigacyjnym. Aby wyświetlić szablony lub powrócić do zapisanych skoroszytów, wybierz element z galerii lub wyszukaj nazwę na pasku wyszukiwania.

Aby zapisać skoroszyt, należy zapisać raport z określonym tytułem, subskrypcją, grupą zasobów i lokalizacją.
Skoroszyt automatycznie dopełni te same ustawienia co obszar roboczy LA, z tą samą subskrypcją, grupą zasobów, jednak użytkownicy mogą zmienić te ustawienia raportu. Skoroszyty są domyślnie zapisywane w *folderze Moje raporty,* dostępne tylko dla poszczególnych użytkowników. Można je również zapisać bezpośrednio w raportach udostępnionych lub udostępnić później.

![Zapisywanie skoroszytów](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Następne kroki

- [Opcje konwersji](view-designer-conversion-options.md)

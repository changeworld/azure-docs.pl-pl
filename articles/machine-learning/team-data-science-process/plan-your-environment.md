---
title: Identyfikowanie scenariuszy i planowanie procesu analizy - Proces nauki o danych zespołowych | Uczenie maszynowe platformy Azure
description: Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analitycznych, biorąc pod uwagę serię kluczowych pytań.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710312"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analizy

Jakie zasoby są wymagane do utworzenia środowiska, które może wykonywać zaawansowane przetwarzanie analizy na zestawie danych? W tym artykule zaproszę serię pytań, które mogą pomóc w zidentyfikowaniu zadań i zasobów odpowiednich dla danego scenariusza.

Aby dowiedzieć się więcej o kolejności kroków wysokiego poziomu w zakresie analizy predykcyjnej, zobacz [Co to jest proces nauki o danych zespołu (TDSP).](overview.md) Każdy krok wymaga określonych zasobów dla zadań związanych z danym scenariuszem.

Odpowiedz na kluczowe pytania w następujących obszarach, aby zidentyfikować scenariusz:

* logistyka danych
* charakterystyka danych
* jakość zestawu danych
* preferowane narzędzia i języki

## <a name="logistic-questions-data-locations-and-movement"></a>Pytania logistyczne: lokalizacje danych i ruch

Pytania logistyczne obejmują następujące elementy:

* lokalizacja źródła danych
* miejsce docelowe na platformie Azure
* wymogów dotyczących przenoszenia danych, w tym harmonogramu, kwoty i zasobów

Może być konieczne przeniesienie danych kilka razy podczas procesu analizy. Typowym scenariuszem jest przeniesienie danych lokalnych do jakiejś formy magazynu na platformie Azure, a następnie do usługi Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Jakie jest Twoje źródło danych?

Czy Twoje dane są lokalne lub w chmurze? Możliwe lokalizacje obejmują:

* publicznie dostępny adres HTTP
* lokalna lub sieciowa lokalizacja pliku
* Baza danych programu SQL Server
* kontener usługi Azure Storage

### <a name="what-is-the-azure-destination"></a>Co to jest miejsce docelowe platformy Azure?

Gdzie potrzebne są Twoje dane do przetwarzania lub modelowania? 

* Azure Blob Storage
* Bazy danych sql azure
* Program SQL Server na maszynie wirtualnej platformy Azure
* Tabele HDInsight (Hadoop na platformie Azure) lub Ule
* Azure Machine Learning
* Wirtualne dyski twarde platformy Azure do montażu

### <a name="how-are-you-going-to-move-the-data"></a>Jak zamierzasz przenieść dane?

Procedury i zasoby do pozyskiwania lub ładowania danych do różnych środowisk magazynowania i przetwarzania, zobacz:

* [Ładowanie danych w środowiskach magazynowania do celów analizy](ingest-data.md)
* [Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio (klasyczne) z różnych źródeł danych](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Czy dane muszą być przenoszone zgodnie z regularnym harmonogramem lub modyfikowane podczas migracji?

Należy rozważyć użycie usługi Azure Data Factory (ADF), gdy dane muszą być stale migrowane. Podajnik ADF może być pomocny w przypadku:

* scenariusz hybrydowy obejmujący zarówno zasoby lokalne, jak i w chmurze
* scenariusz, w którym dane są transagowane, modyfikowane lub zmieniane przez logikę biznesową w trakcie migracji

Aby uzyskać więcej informacji, zobacz [Przenoszenie danych z lokalnego serwera SQL do platformy SQL Azure za pomocą usługi Azure Data Factory.](move-sql-azure-adf.md)

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Jaka część danych ma zostać przeniesiona na platformę Azure?

Duże zestawy danych mogą przekraczać pojemność magazynu niektórych środowisk. Na przykład zobacz omówienie limitów rozmiaru dla machine learning studio (klasyczny) w następnej sekcji. W takich przypadkach można użyć próbki danych podczas analizy. Aby uzyskać szczegółowe informacje na temat pobierania próbek w dół zestawu danych w różnych środowiskach platformy Azure, zobacz [przykładowe dane w procesie nauki o danych zespołu.](sample-data.md)

## <a name="data-characteristics-questions-type-format-and-size"></a>Pytania dotyczące charakterystyki danych: typ, format i rozmiar

Te pytania są kluczem do planowania środowiska pamięci masowej i przetwarzania. Pomogą one wybrać odpowiedni scenariusz dla typu danych i zrozumieć wszelkie ograniczenia.

### <a name="what-are-the-data-types"></a>Jakie są typy danych?

* Numeryczne
* Podzielone na kategorie
* Ciągi
* plików binarnych

### <a name="how-is-your-data-formatted"></a>Jak są sformatowane dane?

* Pliki płaskie oddzielone przecinkami (CSV) lub z kartą (TSV)
* Skompresowane lub nieskompresowane
* Obiekty BLOB platformy Azure
* Stoły Hadoop Hive
* Tabele programu SQL Server

### <a name="how-large-is-your-data"></a>Jak duże są Twoje dane?

* Małe: mniej niż 2 GB
* Średni: większy niż 2 GB i mniej niż 10 GB
* Duży: ponad 10 GB

Weźmy na przykład środowisko usługi Azure Machine Learning Studio (klasyczne):

* Aby uzyskać listę formatów i typów danych obsługiwanych przez usługę Azure Machine Learning Studio, zobacz [Obsługiwane formaty danych i typy danych.](../studio/import-data.md#supported-data-formats-and-data-types)
* Aby uzyskać informacje na temat ograniczeń innych usług platformy Azure używanych w procesie analizy, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Pytania dotyczące jakości danych: eksploracja i przetwarzanie wstępne

### <a name="what-do-you-know-about-your-data"></a>Co wiesz o swoich danych?

Poznaj podstawowe cechy danych:

* Jakie wzorce lub trendy wykazuje
* Jakie ma odstające
* Ile wartości brakuje

Ten krok jest ważne, aby pomóc:

* Określ, ile przetwarzania wstępnego jest potrzebne
* Sformułować hipotezy, które sugerują najbardziej odpowiednie cechy lub rodzaj analizy
* Formułowanie planów dodatkowego gromadzenia danych

Przydatne techniki kontroli danych obejmują opisowe obliczenia statystyk i wykresy wizualizacji. Aby uzyskać szczegółowe informacje na temat eksplorowania zestawu danych w różnych środowiskach platformy Azure, zobacz [Eksplorowanie danych w procesie nauki o danych zespołu.](explore-data.md)

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Czy dane wymagają wstępnego przetwarzania lub czyszczenia?

Przed efektywnym użyciem zestawu danych do uczenia maszynowego może być konieczne wstępne przetwarzanie i czyszczenie danych. Surowe dane są często hałaśliwe i zawodne. Może brakować wartości. Używanie takich danych do modelowania może spowodować wprowadzające w błąd wyniki. Aby uzyskać opis, zobacz [Zadania, aby przygotować dane do ulepszonego uczenia maszynowego](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Pytania dotyczące narzędzi i języków

Istnieje wiele opcji dla języków, środowisk programistycznych i narzędzi. Pamiętaj o swoich potrzebach i preferencjach.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Jakich języków wolisz używać do analizy?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Jakich narzędzi należy używać do analizy danych?

* [Microsoft Azure Powershell](/powershell/azure/overview) — język skryptu używany do administrowania zasobami platformy Azure w języku skryptu
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Analiza rewolucji](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Notesy programu Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identyfikowanie scenariusza zaawansowanej analizy

Po udzieleniu odpowiedzi na pytania w poprzedniej sekcji, można przystąpić do określenia, który scenariusz najlepiej pasuje do sprawy. Przykładowe scenariusze przedstawiono w [scenariuszach zaawansowanej analizy w usłudze Azure Machine Learning.](plan-sample-scenarios.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Co to jest zespołowe przetwarzanie danych dla celów naukowych?](overview.md)

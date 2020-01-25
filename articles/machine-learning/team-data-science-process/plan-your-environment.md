---
title: Identyfikowanie scenariuszy i planowanie procesu analizy — proces nauki danych zespołu | Azure Machine Learning
description: Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analizy, biorąc pod uwagę szereg pytań kluczowych.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710312"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analizy

Jakie zasoby są wymagane do utworzenia środowiska, które może wykonywać zaawansowane przetwarzanie analiz w zestawie danych? W tym artykule przedstawiono szereg pytań, które mogą pomóc w identyfikacji zadań i zasobów związanych z Twoim scenariuszem.

Aby dowiedzieć się więcej na temat kolejności kroków wysokiego poziomu dla analizy predykcyjnej, zobacz [co to jest proces nauki o danych zespołowych (przetwarzania TDSP)](overview.md). Każdy krok wymaga określonych zasobów do zadań związanych z konkretnym scenariuszem.

Odpowiedzi na kluczowe pytania w następujących obszarach, aby zidentyfikować swój scenariusz:

* Logistyka danych
* charakterystyki danych
* jakość zestawu danych
* preferowane narzędzia i Języki

## <a name="logistic-questions-data-locations-and-movement"></a>Pytania logistycznej: lokalizacje danych i przepływu

Pytania logistyczne obejmują następujące elementy:

* Lokalizacja źródła danych
* Lokalizacja docelowa na platformie Azure
* wymagania dotyczące przeniesienia danych, w tym harmonogram, ilość i zasoby, których dotyczy

Może być konieczne kilkakrotne przeniesienie danych podczas procesu analizy. Jest to typowy scenariusz przenoszenia danych lokalnych do pewnego rodzaju magazynu na platformie Azure, a następnie do usługi Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Co to jest źródło danych?

Czy dane są lokalne lub w chmurze? Możliwe są następujące lokalizacje:

* publicznie dostępny adres HTTP
* Lokalizacja pliku lokalnego lub sieciowego
* Baza danych SQL Server
* kontener usługi Azure Storage

### <a name="what-is-the-azure-destination"></a>Co to jest miejsce docelowe platformy Azure?

Gdzie dane wymagają przetworzenia lub modelowania? 

* Azure Blob Storage
* Baz danych SQL Azure
* Program SQL Server na maszynie wirtualnej platformy Azure
* HDInsight (Hadoop na platformie Azure) lub tabele programu Hive
* Azure Machine Learning
* Instalowalne wirtualne dyski twarde platformy Azure

### <a name="how-are-you-going-to-move-the-data"></a>Jak chcesz przenieść dane?

Aby zapoznać się z procedurami i zasobami w celu pozyskiwania lub ładowania danych do różnych środowisk magazynowania i przetwarzania, zobacz:

* [Ładowanie danych w środowiskach magazynowania do analizy](ingest-data.md)
* [Importowanie danych szkoleniowych do Azure Machine Learning Studio (klasyczne) z różnych źródeł danych](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Czy dane muszą być przenoszone zgodnie z regularnym harmonogramem czy modyfikowane podczas migracji?

Należy rozważyć użycie Azure Data Factory (ADF), gdy dane wymagają ciągłego migrowania. ADF można ułatwić:

* scenariusz hybrydowy, który obejmuje zasoby lokalne i w chmurze
* scenariusz, w którym dane są transakcyjne, modyfikowane lub zmieniane przez logikę biznesową w trakcie migrowania

Aby uzyskać więcej informacji, zobacz [przenoszenie danych z lokalnego programu SQL Server do platformy SQL Azure za pomocą Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Jaka część danych ma zostać przeniesiona na platformę Azure?

Duże zestawy danych mogą przekroczyć pojemność magazynu w niektórych środowiskach. Aby zapoznać się z przykładem, zobacz Omówienie limitów rozmiaru dla Machine Learning Studio (klasyczny) w następnej sekcji. W takich przypadkach można użyć próbki danych podczas analizy. Aby uzyskać szczegółowe informacje dotyczące obniżenie częstotliwości próbkowania zestawu danych w różnych środowiskach Azure, zobacz [przykładowych danych w procesie nauki o danych zespołu](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Pytania dotyczące danych cechy: typ, formatu i rozmiar

Te pytania są kluczowe do planowania środowiska magazynu i przetwarzania. Pomogą Ci w wyborze odpowiedniego scenariusza dla danego typu danych i zrozumieniu ograniczeń.

### <a name="what-are-the-data-types"></a>Jakie są typy danych?

* Wartości liczbowych
* Podzielone na kategorie
* Ciągi
* Binary

### <a name="how-is-your-data-formatted"></a>Jak są sformatowane dane?

* Rozdzielana przecinkami (CSV) lub rozdzielane tabulatorami (TSV) plików prostych
* Skompresowane lub bez kompresji
* Obiekty BLOB platformy Azure
* Tabele programu Hive w usłudze Hadoop
* Tabel programu SQL Server

### <a name="how-large-is-your-data"></a>Jak duże są dane?

* Mała liczba godzin: mniej niż 2 GB
* Średni: Większa niż 2 GB i mniejsza niż 10 GB
* Duże: Większy niż 10 GB

Wypełnij środowisko Azure Machine Learning Studio (klasyczne) na przykład:

* Aby uzyskać listę formatów danych i typy obsługiwanych przez usługi Azure Machine Learning Studio, zobacz [formatów danych i typy danych obsługiwane](../studio/import-data.md#supported-data-formats-and-data-types) sekcji.
* Aby uzyskać informacji na temat ograniczeń innych usług platformy Azure używane w procesie analytics, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Pytania dotyczące jakości danych: eksplorowanie i wstępne przetwarzanie

### <a name="what-do-you-know-about-your-data"></a>Co wiesz o danych?

Zapoznaj się z podstawowymi charakterystykami danych:

* Jakie wzorce lub trendy występują
* Jakie odstają się one
* Liczba brakujących wartości

Ten krok ma na celu ułatwienie:

* Określ, ile jest wymagane wstępne przetwarzanie
* Formułowanie form, które sugerują najbardziej odpowiednie funkcje lub typ analizy
* Formułowanie planów dotyczących dodatkowej zbierania danych

Przydatne techniki inspekcji danych obejmują Obliczanie statystyk i wykresy wizualizacji. Aby uzyskać szczegółowe informacje dotyczące eksplorować zestaw danych w różnych środowiskach Azure, zobacz [Eksplorowanie danych w procesie nauki o danych zespołu](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Czy dane wymagają przetwarzania wstępnego czy czyszczenia?

Może być konieczne wstępne przetworzenie i oczyszczenie danych, aby można było efektywnie korzystać z zestawu danych w celu uczenia maszynowego. Dane pierwotne często są zakłóceniami i zawodowo. Może brakować wartości. Przy użyciu tych danych do modelowania może spowodować wyświetlenie nieprawdziwych wyników. Aby uzyskać opis, zobacz [zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Pytania dotyczące narzędzi i języków

Istnieje wiele opcji języków, środowisk deweloperskich i narzędzi. Zapoznaj się z potrzebami i preferencjami.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Jakich języków wolisz używać na potrzeby analizy?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Jakie narzędzia należy stosować do analizy danych?

* [Microsoft Azure PowerShell](/powershell/azure/overview) — język skryptu używany do administrowania zasobami platformy Azure w języku skryptów
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Zakup firmy revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [Programu RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Notesy programu Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identyfikowanie własnego scenariusza zaawansowanej analizy

Po udzieleniu odpowiedzi na pytania z poprzedniej sekcji możesz określić, który scenariusz najlepiej pasuje do Twojego przypadku. Przykładowe scenariusze są opisane w [scenariusze zaawansowanej analizy w usłudze Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Co to jest zespołowe przetwarzanie danych (przetwarzania TDSP)?](overview.md)

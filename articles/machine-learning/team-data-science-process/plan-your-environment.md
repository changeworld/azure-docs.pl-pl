---
title: Identyfikowanie scenariuszy i planowanie proces analytics — proces analizy danych Team | Usługi Azure Machine Learning
description: Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analizy, biorąc pod uwagę szereg pytań kluczowych.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d8eed4f2425cdbfec7d3addad11ddaba57e5370e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704485"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analizy

Jakie zasoby są wymagane do tworzenia środowiska, które może wykonywać zaawansowane analizy przetwarzanie w zestawie danych? W tym artykule sugeruje szereg pytań do zadania, które mogą pomóc w identyfikacji zadania i odpowiednie zasoby danego scenariusza.

Aby dowiedzieć się więcej na temat kolejności ogólne kroki do analizy predykcyjnej, zobacz [co to jest Team Data Science naukowych](overview.md). Każdy krok wymaga określonych zasobów dla zadania, które są odpowiednie dla danego scenariusza.

Odpowiadanie na kluczowe pytania w następujących obszarów identyfikowanie własnego scenariusza:

* logistyką danych
* właściwości danych
* jakość zestawu danych
* preferowanych narzędzi i języków

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Pytania logistycznej: lokalizacje danych i przepływu

Pytania logistycznej obejmują następujące elementy:

* Lokalizacja źródła danych
* docelowej na platformie Azure
* wymagania dotyczące przenoszenia danych, w tym harmonogramu, kwota i zasobów

Może być konieczne przeniesienie danych kilka razy podczas analizy. Jest to typowy scenariusz przenoszenia danych lokalnych do pewnego rodzaju magazynu na platformie Azure, a następnie do usługi Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Co to jest źródło danych?

Czy Twoje dane lokalne lub w chmurze? Możliwe lokalizacje obejmują:

* publicznie dostępny adres HTTP
* lokalną lub sieciową lokalizację pliku
* bazy danych programu SQL Server
* kontener usługi Azure storage

### <a name="what-is-the-azure-destination"></a>Co to jest docelowych platformy Azure?

Gdzie dane potrzebne do przetwarzania lub modelowania? 

* Azure Blob Storage
* Baz danych SQL Azure
* Program SQL Server na maszynie wirtualnej platformy Azure
* HDInsight (Hadoop na platformie Azure) lub tabele programu Hive
* Azure Machine Learning
* Instalacja platformy Azure wirtualne dyski twarde

### <a name="how-are-you-going-to-move-the-data"></a>Jak chcesz przenosić dane?

Procedury i zasobów w celu pozyskiwania lub ładowania danych do różnych środowisk przetwarzania i innego magazynu zobacz:

* [Ładowanie danych w środowiskach magazynowania do analizy](ingest-data.md)
* [Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio z różnych źródeł danych](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Czy dane mają zostać przeniesiona zgodnie z ustalonym harmonogramem lub modyfikowany podczas migracji?

Należy wziąć pod uwagę przy użyciu usługi Azure Data Factory (ADF), gdy dane muszą stale można migrować. ADF może być przydatne w przypadku:

* scenariusza hybrydowego, który obejmuje zarówno w lokalnych i zasobów w chmurze
* Scenariusz, w którym odbywających się dane, modyfikacji lub zmienione przez logikę biznesową w trakcie migracji

Aby uzyskać więcej informacji, zobacz [przenoszenie danych z lokalnego programu SQL server do usługi SQL Azure z usługą Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Jak dużo danych ma zostać przesunięta na platformie Azure?

Bardzo dużych zestawów danych może być dłuższa niż pojemność magazynu w niektórych środowiskach. Aby uzyskać przykład Zobacz Omówienie limitów rozmiaru do usługi Machine Learning Studio w następnej sekcji. W takich przypadkach można użyć przykładowych danych podczas analizy. Aby uzyskać szczegółowe informacje dotyczące obniżenie częstotliwości próbkowania zestawu danych w różnych środowiskach Azure, zobacz [przykładowych danych w procesie nauki o danych zespołu](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Pytania dotyczące danych cechy: typ, formatu i rozmiar

Te pytania są kluczem do planowania sieci magazynowania i przetwarzania środowiska. Pomagają one wybierz odpowiedni scenariusz dla danego typu danych i zrozumienie ograniczeń.

### <a name="what-are-the-data-types"></a>Jakie są typy danych?

* Wartości liczbowych
* Podzielone na kategorie
* Ciągi
* Binarny

### <a name="how-is-your-data-formatted"></a>Sposób formatowania danych?

* Rozdzielana przecinkami (CSV) lub rozdzielane tabulatorami (TSV) plików prostych
* Skompresowane lub bez kompresji
* Obiekty BLOB platformy Azure
* Tabele programu Hive w usłudze Hadoop
* Tabel programu SQL Server

### <a name="how-large-is-your-data"></a>Jak duże są Twoje dane?

* Small: Mniej niż 2 GB
* Średnie: Większe niż 2 GB i mniejsza niż 10 GB
* Duże: Większy niż 10 GB

Na przykład wykonać środowisko Azure Machine Learning Studio:

* Aby uzyskać listę formatów danych i typy obsługiwanych przez usługi Azure Machine Learning Studio, zobacz [formatów danych i typy danych obsługiwane](../studio/import-data.md#supported-data-formats-and-data-types) sekcji.
* Aby uzyskać informacji na temat ograniczeń innych usług platformy Azure używane w procesie analytics, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Pytania dotyczące jakości danych: eksplorowanie i wstępne przetwarzanie

### <a name="what-do-you-know-about-your-data"></a>Co należy wiedzieć o danych?

Poznaj podstawowe właściwości dotyczące danych:

* Co wzorców lub trendów go wykazuje
* Jakie elementy odstające w nim
* Brak wartości liczby

Ten krok jest ważne, aby pomóc Ci:

* Określić, ile przetwarzania wstępnego jest wymagana.
* Formułowanie hipotezy, które sugerują najbardziej odpowiednie funkcje lub typ analizy
* Formułowanie planów zbierania dodatkowych danych

Techniki przydatne do inspekcji danych obejmują Obliczanie statystyk opisową i wizualizacji, które. Aby uzyskać szczegółowe informacje dotyczące eksplorować zestaw danych w różnych środowiskach Azure, zobacz [Eksplorowanie danych w procesie nauki o danych zespołu](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Dane wymaga wstępne przetwarzanie i czyszczenie?

Może być konieczne wstępne przetwarzanie i czyszczenie danych, zanim użyjesz zestawu danych skutecznie dla usługi machine learning. Dane pierwotne są często hałas i nieprzewidywalne. Jego prawdopodobnie brakuje wartości. Przy użyciu tych danych do modelowania może spowodować wyświetlenie nieprawdziwych wyników. Aby uzyskać opis, zobacz [zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Pytania dotyczące narzędzi i języków

Istnieje wiele opcji dla języków, środowisk deweloperskich i narzędzi. Należy pamiętać o swoich potrzeb i preferencji.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Jakich języków wolą używać do analizy?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Jakich narzędzi należy użyć do analizy danych?

* [Microsoft Azure Powershell](/powershell/azure/overview) — język skryptu, używane do administrowania zasobami platformy Azure w języku skryptów
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Zakup firmy revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [Programu RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Program Jupyter notebooks](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identyfikowanie własnego scenariusza zaawansowanej analizy

Po należy odpowiedzieć na pytania w poprzedniej sekcji, możesz przystąpić do określenia scenariusze, które najlepiej odpowiada wymaganiom tej sprawy. Przykładowe scenariusze są opisane w [scenariusze zaawansowanej analizy w usłudze Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Co to jest proces do nauki o danych zespołu (TDSP)?](overview.md)

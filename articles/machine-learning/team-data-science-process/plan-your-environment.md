---
title: Identyfikowanie scenariuszy i planowanie proces analizy — zespołu danych dla celów naukowych
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
ms.openlocfilehash: 0cf9ffddffe7f97008e69c9c4fb7945faeb779da
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469604"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analizy
Jakie zasoby należy zaplanować do uwzględnienia podczas konfigurowania środowiska do zaawansowanej analizy, przetwarzanie w zestawie danych? W tym artykule sugeruje szereg pytań do zadania ułatwiające identyfikowanie własnego scenariusza zadania i odpowiednie zasoby. Kolejność etapów wysokiego poziomu do analizy predykcyjnej jest opisany w [co to jest Team Data Science naukowych?](overview.md). Każdy z tych kroków wymaga określonych zasobów dla zadania, które są odpowiednie dla danego scenariusza. Kluczowe pytania do identyfikowanie własnego scenariusza dotyczą danych logistycznych, właściwości, jakości zestawy danych, narzędzia i języki, aby wykonać analizy.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Pytania logistycznej: lokalizacje danych i przepływu
Logistycznej pytania dotyczą lokalizacji **źródła danych**, **docelowej** platformy Azure, a wymagania dotyczące przenoszenia danych, w tym harmonogramu, ilości i zasobów. Dane, może być konieczne można przenieść kilka razy podczas analizy. Jest to typowy scenariusz przenoszenia danych lokalnych do pewnego rodzaju magazynu na platformie Azure, a następnie do usługi Machine Learning Studio.

1. **Co to jest źródło danych?** Jest to, lokalnej lub w chmurze? Na przykład:
   
   * Dane są publicznie dostępne pod adresem HTTP.
   * Dane znajdują się w lokalizacji pliku sieć lokalna.
   * Dane znajdują się w bazie danych programu SQL Server.
   * Dane są przechowywane w kontenerze usługi Azure storage
2. **Co to jest docelowych platformy Azure?** Gdy jest konieczne w przypadku przetwarzania lub modelowania? Na przykład:
   
   * Azure Blob Storage
   * Baz danych SQL Azure
   * Program SQL Server na maszynie wirtualnej platformy Azure
   * HDInsight (Hadoop na platformie Azure) lub tabele programu Hive
   * Azure Machine Learning
   * Instalacja platformy Azure wirtualnych dysków twardych.
3. **Jak chcesz przenosić dane?** Procedury składowane i dostępnych zasobów umożliwiających pozyskiwania lub ładowania danych do różnych innego magazynu i środowisk przetwarzania są opisane w następujących artykułach:
   
   * [Ładowanie danych w środowiskach magazynowania do analizy](ingest-data.md)
   * [Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio z różnych źródeł danych](../studio/import-data.md).
4. **Czy dane mają zostać przeniesiona zgodnie z ustalonym harmonogramem lub modyfikowany podczas migracji?** Należy rozważyć użycie usługi Azure Data Factory (ADF), gdy dane muszą zostać stale zmigrowane, szczególnie jeśli scenariusza hybrydowego, który uzyskuje dostęp do swoich lokalnych i zasobów w chmurze jest zaangażowana lub gdy danych jest wykonany lub musi można modyfikować ani logiki biznesowej dodane do ją w trakcie migracji. Aby uzyskać więcej informacji, zobacz [przenoszenie danych z lokalnego programu SQL server do usługi SQL Azure z usługą Azure Data Factory](move-sql-azure-adf.md)
5. **Jak dużo danych ma zostać przesunięta na platformie Azure?** Zestawy danych, które są bardzo duże może przekraczać pojemność magazynu w niektórych środowiskach. Aby uzyskać przykład Zobacz Omówienie limitów rozmiaru do usługi Machine Learning Studio w następnej sekcji. W takich przypadkach próbkę danych może być używany podczas analizy. Aby uzyskać szczegółowe informacje dotyczące obniżenie częstotliwości próbkowania zestawu danych w różnych środowiskach Azure, zobacz [przykładowych danych w procesie nauki o danych zespołu](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Pytania dotyczące danych cechy: typ, formatu i rozmiar
Te pytania są kluczem do planowania sieci magazynowania i przetwarzania środowiska, które są odpowiednie dla różnych typów danych i z których każdy ma pewne ograniczenia.

1. **Jakie są typy danych?** Na przykład:
   
   * Wartości liczbowych
   * Podzielone na kategorie
   * Ciągi
   * Binarny
2. **Sposób formatowania danych?** Na przykład:
   
   * Rozdzielana przecinkami (CSV) lub rozdzielane tabulatorami (TSV) plików prostych
   * Skompresowane lub bez kompresji
   * Obiekty BLOB platformy Azure
   * Tabele programu Hive w usłudze Hadoop
   * Tabel programu SQL Server
3. **Jak duże są Twoje dane?**
   
   * Small: Mniej niż 2 GB
   * Średnie: Większe niż 2 GB i mniejsza niż 10 GB
   * Duże: Większy niż 10 GB

Na przykład wykonać środowisko Azure Machine Learning Studio:

* Aby uzyskać listę formatów danych i typy obsługiwanych przez usługi Azure Machine Learning Studio, zobacz [formatów danych i typy danych obsługiwane](../studio/import-data.md#data-formats-and-data-types-supported) sekcji.


Aby uzyskać informacji na temat ograniczeń innych usług platformy Azure używane w procesie analytics, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Pytania dotyczące jakości danych: eksplorowanie i wstępne przetwarzanie
1. **Co należy wiedzieć o danych?** Eksplorowanie danych, aby uzyskać opis jej podstawowymi charakterystykami. Jakie wzorców lub trendów wskazuje, jakie elementy odstające, ma ona lub ile wartości Brak. Ten krok jest ważne w przypadku określenia w zakresie wstępnego przetwarzania, w razie opracowywania hipotezy, które można zasugerować najbardziej odpowiednie funkcje lub wpisz analizy i opracowywania planów zbierania dodatkowych danych. Obliczanie statystyk opisową i kreślenia wizualizacji są przydatne techniki inspekcja danych. Aby uzyskać szczegółowe informacje dotyczące eksplorować zestaw danych w różnych środowiskach Azure, zobacz [Eksplorowanie danych w procesie nauki o danych zespołu](explore-data.md).
2. **Dane wymaga przetwarzania wstępnego lub czyszczenie?**
   Wstępne przetwarzanie i czyszczenie danych są ważne zadania, które zazwyczaj należy przeprowadzić przed zestawu danych można efektywnie używać uczenia maszynowego. Nieprzetworzone dane są często hałas i zawodnych i może brakować wartości. Przy użyciu tych danych do modelowania może spowodować wyświetlenie nieprawdziwych wyników. Aby uzyskać opis, zobacz [zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Pytania dotyczące narzędzi i języków
Istnieje wiele opcji w tym miejscu, w zależności od jakich języków i środowisk deweloperskich lub narzędzia potrzebne, lub są najbardziej conformable za pośrednictwem.

1. **Jakich języków wolą używać do analizy?**  
   
   * R
   * Python
   * SQL
2. **Jakich narzędzi należy użyć do analizy danych?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) — język skryptu, używane do administrowania zasobami platformy Azure w języku skryptów.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Zakup firmy revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
   * [Programu RStudio](http://www.rstudio.com)
   * [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Program Jupyter notebooks](http://jupyter.org/)
   * [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identyfikowanie własnego scenariusza zaawansowanej analizy
Gdy należy odpowiedzieć na pytania w poprzedniej sekcji, możesz przystąpić do określić scenariusze, które najlepiej odpowiada tej sprawy. Przykładowe scenariusze są opisane w [scenariusze zaawansowanej analizy w usłudze Azure Machine Learning](plan-sample-scenarios.md).


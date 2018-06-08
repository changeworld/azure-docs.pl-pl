---
title: Określenie scenariuszy i Planowanie procesu analytics - Azure | Dokumentacja firmy Microsoft
description: Planowanie zaawansowana analityka przy uwzględnieniu szereg pytań klucza.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 16cc7c5841708b8b27cff4fcc7c93cdbb2fe0fa4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838337"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Identyfikowanie scenariuszy i planowanie zaawansowanego przetwarzania danych analizy
Jakie zasoby powinny zamierzasz włączyć podczas konfigurowania środowiska w celu zaawansowana analityka przetwarzania na zestaw danych? W tym artykule sugeruje szereg pytań pomagające w identyfikacji zadania i odpowiednie zasoby danego scenariusza. Kolejność etapów wysokiego poziomu analizy predykcyjnej jest opisane w temacie [co to jest proces nauki danych zespołu (TDSP)?](overview.md). Każda z tych czynności wymaga określonych zasobów dla zadań dotyczących Twojego scenariusza. Ważne pytania, aby zidentyfikować scenariusz dotyczy danych logistyki cech jakości zestawów danych, narzędzia i języki, aby wykonać analizy.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Pytania logistyczna: lokalizacje danych i przepływu
Logistyczna pytania dotyczą lokalizację **źródła danych**, **docelowej** platformy Azure i wymagania dotyczące przenoszenia danych, w tym harmonogram, wielkość i zasobach. Dane mogą należy przenieść kilka razy podczas procesu analytics. Typowy scenariusz obejmuje przenoszenia danych lokalnych do jakiegoś magazynu na platformie Azure, a następnie do usługi Machine Learning Studio.

1. **Co to jest źródło danych?** Jest lokalnym lub w chmurze? Na przykład:
   
   * Dane są publicznie dostępne pod adresem HTTP.
   * Dane znajdują się w lokalizacji pliku sieć lokalna.
   * Dane są w bazie danych programu SQL Server.
   * Dane są przechowywane w kontenerze magazynu Azure
2. **Co to jest Azure docelowego?** Gdy jest konieczne do przetwarzania lub modelowania? Na przykład:
   
   * Azure Blob Storage
   * Baz danych SQL Azure
   * Program SQL Server na maszynie wirtualnej platformy Azure
   * HDInsight (Hadoop na platformie Azure) lub tabele programu Hive
   * Azure Machine Learning
   * Instalację Azure wirtualnych dysków twardych.
3. **Jak zamierzasz przenieść dane?** Procedury i dostępnych zasobów do pozyskiwania lub załadować danych do różnych innego magazynu i przetwarzania środowisk zostały opisane w następujących artykułach:
   
   * [Ładowanie danych do środowiska magazynu dla analityka](ingest-data.md)
   * [Importowanie danych szkoleniowych w usłudze Azure Machine Learning Studio z różnych źródeł danych](../studio/import-data.md).
4. **Czy dane musi być przenoszone zgodnie z ustalonym harmonogramem lub zmodyfikować w trakcie migracji?** Należy wziąć pod uwagę przy użyciu fabryki danych Azure (ADF), gdy dane trzeba stale migracji, zwłaszcza w wypadku scenariusza hybrydowego, który uzyskuje dostęp do swoich lokalnych i uczestniczy zasobów w chmurze, lub gdy dane jest nietransakcyjnego lub musi być zmodyfikowany lub zostały dodane do logiki biznesowej go w trakcie migrowane. Aby uzyskać więcej informacji, zobacz [przenoszenia danych z lokalnego programu SQL server SQL Azure z fabryką danych Azure](move-sql-azure-adf.md)
5. **Ile danych ma zostać przeniesione do platformy Azure?** Są bardzo dużych zestawów danych może przekroczyć pojemność magazynu w niektórych środowiskach. Na przykład Zobacz Omówienie limity rozmiaru dla usługi Machine Learning Studio w następnej sekcji. W takich przypadkach próbkę danych może być używany podczas analizy. Szczegóły dotyczące zestawu danych w różnych środowiskach Azure próbek w dół, aby zapoznać [przykładowe dane w procesie nauki danych zespołu](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Pytania dotyczące danych właściwości: typ, format i rozmiar
Pytania te stanowią podstawę planowania magazynu i przetwarzania środowiska, które są odpowiednie do różnych typów danych i każdy z nich ma pewne ograniczenia.

1. **Jakie są typy danych?** Na przykład:
   
   * Wartości liczbowych
   * Podzielone na kategorie
   * Ciągi
   * Binarny
2. **Sposób formatowania danych?** Na przykład:
   
   * Rozdzielana przecinkami (CSV) lub tabulatorami (TSV) plików prostych
   * Skompresowane lub nieskompresowane
   * Obiekty BLOB platformy Azure
   * Tabele programu Hive usługi Hadoop
   * Tabel programu SQL Server
3. **Jak duże jest danych?**
   
   * Mała liczba godzin: mniej niż 2 GB
   * Średnia liczba godzin: Większa niż 2 GB i mniejsza niż 10 GB
   * Duże: Ponad 10 GB

Na przykład wykonać środowiska Azure Machine Learning Studio:

* Aby uzyskać listę formatów danych i typów obsługiwanych w usłudze Azure Machine Learning Studio, zobacz [danych formaty i typy danych obsługiwane](../studio/import-data.md#data-formats-and-data-types-supported) sekcji.
* Informacje w danych ograniczenia dotyczące usługi Azure Machine Learning Studio, zobacz **jak duży może być zestaw danych dla moich modułów?** sekcji [importowanie i eksportowanie danych na potrzeby uczenia maszynowego](../studio/faq.md#machine-learning-studio-questions)

Aby informacji na temat ograniczeń innymi usługami Azure używanego podczas analizy, zobacz [subskrypcji platformy Azure i ograniczenia usługi, przydziały i ograniczenia](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Pytania dotyczące jakości danych: eksploracji i przetwarzania wstępnego
1. **Co należy wiedzieć o danych?** Eksplorowanie danych, aby uzyskać opis jego podstawowymi charakterystykami. Jakie wzorców i trendów wskazuje, ile wartości lub wartości, jakie odstających ma Brak. Ten krok jest ważne w przypadku określania przetwarzanie wstępne potrzebnych formułowania hipotez, które może sugerować najbardziej odpowiednie funkcje lub wpisz analizy i opracowywania planów zbierania dodatkowych danych. Obliczanie statystyki opisowe i kreślenia wizualizacje są przydatne techniki danych inspekcji. Uzyskać szczegółowe informacje o sposobach eksplorowania zestawu danych w różnych środowiskach Azure, zobacz [Eksplorowanie danych w procesie nauki danych zespołu](explore-data.md).
2. **Czy przetwarzanie wstępne lub czyszczenie danych wymaga?**
   Przetwarzanie wstępne i czyszczenia danych są ważne zadania, które zwykle należy przeprowadzić przed zestawu danych można skutecznie uczenia maszynowego. Dane pierwotne jest często zakłócenia i zawodnych i może brakować wartości. Przy użyciu tych danych do modelowania może wygenerować błędne wyniki. Aby uzyskać opis, zobacz [uczenia maszynowego zadania, aby przygotować dane dla rozszerzonego](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Narzędzia i języki pytania
Istnieje wiele opcji w zależności od jakich języków i środowisk deweloperskich lub narzędzia konieczne lub są najbardziej conformable przy użyciu.

1. **Jakich języków wolisz korzystać do analizy?**  
   
   * R
   * Python
   * SQL
2. **Jakie narzędzia należy używać do analizy danych?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) -język skryptów służących do administrowania zasobami Azure w języku skryptu.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Obrót analityka](http://www.revolutionanalytics.com/revolution-r-open)
   * [Programu RStudio](http://www.rstudio.com)
   * [Python Tools for Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Notesy Jupyter](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Zidentyfikuj scenariusz zaawansowana analityka
Po należy odpowiedzieć na pytania w poprzedniej sekcji, można przystąpić do określenia scenariusza najlepiej pasuje do sprawę. Przykładowe scenariusze są opisane w temacie [scenariusze zaawansowana analityka w usłudze Azure Machine Learning](plan-sample-scenarios.md).


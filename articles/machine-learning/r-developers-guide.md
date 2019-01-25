---
title: Przewodnik dewelopera języka R na platformie Azure — programowania w języku R | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie różnych sposobów analityków, którzy mogą wykorzystanie posiadanych umiejętności przy użyciu języka R w języku programowania w usłudze Azure. Platforma Azure oferuje wiele usług, których deweloperzy języka R mogą być wykorzystywane rozszerzyć swoje obciążenia do nauki o danych do chmury.
services: machine-learning
documentationcenter: ''
author: AnalyticJeremy
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: R
ms.topic: article
ms.date: 09/12/2018
ms.author: jepeach
ms.openlocfilehash: 102191b885d2a4a9234b7783b0a51b09903d3abd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807460"
---
# <a name="r-developers-guide-to-azure"></a>Przewodnik dewelopera języka R na platformie Azure
<img src="media/r-developers-guide/logo_r.svg" alt="R logo" align="right" width="200" />

Wielu analityków zajmujących się coraz większej ilości danych szuka sposobów wykorzystać możliwości chmury obliczeniowej na ich analizy.  Ten artykuł zawiera omówienie różnych sposobów, które analityków, którzy mogą korzystać z posiadanych już umiejętności za pomocą [język programowania R](https://www.r-project.org) na platformie Azure.

Microsoft jest w pełni wdrożyła język programowania R jako najwyższej klasy narzędzia dla analityków danych.  Firmy, udostępniając wiele różnych opcji dla deweloperów języka R do uruchomienia kodu na platformie Azure, jest zapewnienie analityków danych rozszerzyć swoje obciążenia do nauki o danych na chmurę, gdy co dzień do czynienia projektów w dużej skali.

Przeanalizujmy różne opcje i najbardziej atrakcyjnych scenariuszy, dla każdej z nich.

## <a name="azure-services-with-r-language-support"></a>Usługi platformy Azure z obsługą języka R
W tym artykule opisano następujących usług platformy Azure, które obsługują język R:

|Usługa                                                          |Opis                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[Maszyna wirtualna do nauki o danych](#data-science-virtual-machine)    |dostosowane maszyny Wirtualnej do użycia jako stacja robocza do nauki o danych lub niestandardowe obliczeniowego elementu docelowego|
|[Usługi uczenie Maszynowe na HDInsight](#ml-services-on-hdinsight)            |klaster z systemem do uruchamiania R analiz na dużych zestawach danych na wielu węzłach   |
|[Azure Databricks](#azure-databricks)                            |środowisko współpracy platformy Spark, które obsługuje języków R i innych języków               |
|[Azure Machine Learning Studio](#azure-machine-learning-studio)  |uruchamianie niestandardowych skryptów języka R w eksperymentów uczenia maszynowego platformy Azure                      |
|[Azure Batch](#azure-batch)                                      |oferuje różne opcje ekonomicznie uruchamiania kod R na wielu węzłach w klastrze|
|[Azure Notebooks](#azure-notebooks)                              |bezpłatnie oparte na chmurze wersję notesów programu Jupyter                  |
|[Azure SQL Database](#azure-sql-database)                        |uruchamianie skryptów języka R wewnątrz aparatu bazy danych programu SQL Server                            |

## <a name="data-science-virtual-machine"></a>Maszyna wirtualna do analizy danych
[Maszyny wirtualnej do nauki o danych](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) jest dostosowany obraz maszyny Wirtualnej na platformie Azure w chmurze firmy Microsoft przeznaczony do nauki o danych. Ma wiele narzędzi do analizy danych popularne, w tym:
* [Microsoft R Open](https://mran.microsoft.com/open/)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)
* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server)

Maszyny DSVM może zostać aprowizowane z Windows lub Linux jako system operacyjny.  Można użyć maszyny DSVM na dwa sposoby: jako interaktywna stacja robocza lub platforma obliczeniowa w niestandardowych klastra.

### <a name="as-a-workstation"></a>Jako stacja robocza
Jeśli chcesz rozpocząć pracę przy użyciu języka R w chmurze, szybko i łatwo, jest to najlepsze rozwiązanie.  Środowisko będzie znany osobom pracował przy użyciu języka R na lokalnej stacji roboczej.  Zamiast korzystać z zasobów lokalnych, środowisko języka R jest uruchamiane na maszynie Wirtualnej w chmurze.  Jeśli dane są już przechowywane na platformie Azure, ma to jednocześnie ma dodatkową zaletę co Twoje skrypty języka R uruchomić "uznane za bliższe do danych." Zamiast przesyłania danych przez Internet, dane są dostępne za pośrednictwem sieci wewnętrznej platformy Azure, która zapewnia znacznie krótszy czas dostępu.

Maszyny DSVM może być szczególnie przydatne dla małych zespołów deweloperów R.  Zamiast inwestować w zaawansowanych stacje robocze dla każdego dewelopera i konieczności członków zespołu, na które wersje różnych pakietów oprogramowania używają synchronizowanie, Każdy deweloper może zwiększać wystąpienia maszyny wirtualnej DSVM zawsze wtedy, gdy potrzebne.

### <a name="as-a-compute-platform"></a>Jako platforma obliczeniowa
Oprócz używany jako stacja robocza maszyny DSVM służy również jako platforma obliczeniowa elastycznie skalowalne dla projektów języka R.  Za pomocą <code>[AzureDSVM](https://github.com/Azure/AzureDSVM)</code> pakietu języka R można programistycznie sterować tworzenie i usuwanie wystąpień maszyny wirtualnej DSVM.  Można tworzą wystąpienia w klastrze i wdrożyć rozproszonych analiz wykonywanych w chmurze.  Całego procesu mogą być kontrolowane przez kod języka R działający na lokalnej stacji roboczej.

Aby dowiedzieć się więcej na temat maszyny DSVM, zapoznaj się z ["Wprowadzenie do platformy Azure maszyna wirtualna do nauki o danych dla systemów Linux i Windows."](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)

## <a name="ml-services-on-hdinsight"></a>Usługi ML w usłudze HDInsight
[Usługi uczenia Maszynowego firmy Microsoft](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) zapewniają analityków danych, statystykom i programistom języka R dostęp na żądanie do skalowalnych, rozproszonych metod analizy w HDInsight.  To rozwiązanie zapewnia najnowszych możliwości analizy na podstawie języka R w zestawach danych z praktycznie dowolnego rozmiaru, załadowane do magazynu obiektów Blob platformy Azure lub usługi Data Lake.

To rozwiązanie przeznaczonych dla przedsiębiorstw, które pozwala na skalowanie kodu języka R w klastrze.  Dzięki wykorzystaniu funkcji firmy Microsoft
<code>[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</code> pakiet, Twoje skrypty języka R na HDInsight można uruchomić funkcji do przetwarzania danych równolegle na wielu węzłach w klastrze.  Dzięki temu R Przetwarzaj dane na znacznie większych niż jest to możliwe przy użyciu języka R z jednowątkowe uruchomione na stacji roboczej.

Możliwość skalowania sprawia, że usługi uczenie Maszynowe na HDInsight doskonałe rozwiązanie dla deweloperów języka R przy użyciu ogromnych zestawów danych.  Zapewnia to elastyczna i skalowalna platforma do uruchamiania skryptów języka R w chmurze.

Aby uzyskać przewodnik na temat tworzenia klastra usługi uczenie Maszynowe, zobacz ["Wprowadzenie do usługi ML w usłudze Azure HDInsight"](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started) artykułu.

## <a name="azure-databricks"></a>Azure Databricks
[Azure Databricks](https://azure.microsoft.com/services/databricks/) to platforma analizy oparta na usłudze Apache Spark i zoptymalizowana pod kątem platformy usług w chmurze Microsoft Azure.  Usługa Databricks, zaprojektowana wspólnie z twórcami usługi Apache Spark, jest zintegrowana z platformą Azure w celu zapewnienia konfigurowania jednym kliknięciem, usprawnionych przepływów pracy oraz interakcyjnego obszaru roboczego, który umożliwia współpracę między analitykami danych, inżynierami danych i analitykami biznesowymi.

Współpraca w usłudze Databricks jest włączona, system notesu platformy.  Użytkownikom można utworzyć, udostępniać i edytować notesów z innymi użytkownikami systemów.  Notesy te umożliwiają użytkownikom napisać kod, który jest wykonywany przed klastry Spark zarządzani w środowisku usługi Databricks.  Te notesów w pełni obsługuje R i zapewnić użytkownikom dostęp do aparatu Spark za pośrednictwem zarówno `SparkR` i `sparklyr` pakietów.

Ponieważ usługi Databricks bazuje na platformie Spark oraz skoncentrowanie się na współpracy, platformy jest często używana przez zespoły analityków danych, które współpracują ze sobą na złożone analizy dużych zestawów danych.  Ponieważ notesów w usłudze Databricks obsługi innych języków, oprócz języka R, jest szczególnie przydatna dla zespołów, gdzie analitycy korzystają z różnych języków do podstawowej pracy.

Artykuł ["Co to jest usługa Azure Databricks?"](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)
zapewnia więcej szczegółów na temat platform i ułatwiają rozpoczęcie.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[Usługa Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) jest narzędziem współpracy, obsługiwane metodą przeciągania i upuszczania, można użyć do tworzenia, testowania i wdrażania rozwiązań do analizy predykcyjnej w chmurze.  Dzięki temu pojawiających się analityków danych, do tworzenia i wdrażania modeli uczenia maszynowego bez konieczności pisania większej ilości kodu.

ML Studio obsługuje języków R i Python.  Za pomocą języka R i usługi ML Studio na dwa sposoby.

### <a name="custom-r-scripts-in-your-experiments"></a>Skrypty niestandardowe języka R w eksperymentów
Po pierwsze można rozszerzyć, manipulowanie danymi i usługi machine learning możliwości usługi ML Studio, pisząc niestandardowych skryptów języka R.
Chociaż usługi ML Studio zawiera szereg modułów na potrzeby przygotowywania i analizowania danych, nie może być zgodna możliwości dojrzała języków języka R.  W związku z tym usługi zaprojektowano tak, aby możliwe było wprowadzenie własnych niestandardowych skryptów języka R w przypadkach, gdzie nie spełnia podany modułów Twoich potrzeb.

Aby wykorzystać tę funkcję, przeciągnij i upuść moduł "Wykonanie skryptu języka R" w eksperymencie.  Następnie należy użyć edytora kodu w okienku "Properties", aby napisać nowy skrypt języka R lub wkleić istniejącego skryptu.  W ramach skryptów możesz odwoływać się zewnętrznych pakietów języka R.  Aby użyć skryptu, można manipulować danymi lub do nauczenia złożonych modeli uczenia Maszynowego, które nie są częścią standardowej biblioteki modeli usługi ML Studio.

Aby uzyskać dokładne wprowadzenie przy użyciu języka R w ramach eksperymentów w usłudze ML Studio, zapoznaj się ["Samouczek szybkiego startu dotyczący języka programowania R dla usługi Azure Machine Learning".](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart)

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a>Tworzenie, zarządzanie i wdrażanie eksperymentów ze środowiska lokalnego języka R
Inny sposób, że za pomocą języka R ML Studio jest użycie
<code>[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)</code> pakiet do monitorowania i kontrolowania procesu eksperymentowania przy użyciu środowisko programistyczne języka R.  Ten pakiet, który jest obsługiwany przez firmę Microsoft, pozwala na przekazywanie i pobieranie zestawów danych do i z usługi Azure ML na potrzeby analizowania eksperymentów funkcji języka R jako usługi sieci web Azure ML, publikowanie, a także uruchamianie danych języka R za pośrednictwem istniejących usług sieci web i pobrać dane wyjściowe.

Ten pakiet sprawia, że znacznie łatwiej wykorzystać usługę Azure ML jako platforma skalowalnego wdrażania kodu R.  Zamiast klikając i przeciągając w interfejsie użytkownika, możesz zautomatyzować proces całego wdrożenia, korzystając z narzędzi, które już znasz.

## <a name="azure-batch"></a>Azure Batch
W przypadku zadania R na dużą skalę, można użyć [usługi Azure Batch](https://azure.microsoft.com/services/batch/).  Ta usługa zapewnia zarządzanie obliczeniami i Planowanie zadania skali chmury, dzięki czemu obciążenia języka R mogą skalować dziesiątek, setek lub tysięcy maszyn wirtualnych.  Ponieważ jest uogólniony platforma obliczeniowa, istnieje kilka opcji do uruchamiania zadań języka R w usłudze Azure Batch.

Jednym rozwiązaniem jest użycie przez firmę Microsoft <code>[doAzureParallel](https://github.com/Azure/doAzureParallel)</code> pakietu.  Ten pakiet języka R jest równoległe zaplecze dla `foreach` pakietu.  Umożliwia ona każda iteracja `foreach` pętli do równoległego uruchamiania w węźle w klastrze usługi Azure Batch.  Wprowadzenie do pakietu, należy przeczytać ["doAzureParallel: Skorzystaj z zalet obliczeń elastyczne platformy Azure bezpośrednio z poziomu sesji języka R"](https://azure.microsoft.com/blog/doazureparallel/) wpis w blogu.

Inną opcją do uruchamiania skryptu języka R w usłudze Azure Batch jest pakietu kodu za pomocą "Rscript.exe.{0}nie" jako aplikacji usługi Batch w witrynie Azure portal.  Aby uzyskać szczegółowy przewodnik, zapoznaj się ["Obciążenia języka R w usłudze Azure Batch".](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/)

Trzecia opcja jest użycie [rozproszonych danych inżynierów zestawu narzędzi platformy Azure](https://github.com/Azure/aztk) (AZTK), co pozwala do aprowizowania klastrów platformy Spark na żądanie, przy użyciu kontenerów platformy Docker w usłudze Azure Batch.  Zapewnia to ekonomiczny sposób uruchamiania zadań platformy Spark na platformie Azure.  Za pomocą [SparklyR z AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK), Twoje skrypty języka R można skalować w poziomie w chmurze łatwe i ekonomicznie.

## <a name="azure-notebooks"></a>Azure Notebooks

[Notesy platformy Azure](https://notebooks.azure.com) jest metodą niskie koszty, prostego, dla deweloperów języka R, którzy wolą Praca z notesami do Przenieś swój kod na platformę Azure.  Jest to bezpłatna usługa dla wszystkim użytkownikom tworzenie i uruchamianie kodu w ich przy użyciu przeglądarki [Jupyter](https://jupyter.org/), co jest projekt typu open source, łącząc prose języka znaczników markdown, kodu wykonywalnego i grafiki na jednym kanwę.

Warstwy bezpłatna usługi notesów usługi Azure jest rentowną opcją projektów na małą skalę, ponieważ ogranicza każdego notesu procesu do 4GB pamięci i 1GB zestawów danych. Jeśli potrzebujesz mocy obliczeniowej i poza te ograniczenia, jednak można uruchamiać notesów w wystąpieniu maszyny wirtualnej do nauki o danych. Aby uzyskać więcej informacji, zobacz [zarządzanie i konfigurować projekty notesy platformy Azure — warstwa wystąpień obliczeniowych](/azure/notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="azure-sql-database"></a>Azure SQL Database
[Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/) to usługa bazy danych w inteligentną i w pełni zarządzana relacyjna chmury firmy Microsoft.  Umożliwia ona używać pełnych możliwości programu SQL Server bez żadnych trudności związanych ze skomplikowanymi konfigurowania infrastruktury.  Obejmuje to [usługi Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017), który jest nowsze dodatki do usługi SQL.

Ta funkcja oferuje osadzony, predykcyjne analizy i danych do analizy aparat, który może wykonać kod R w bazie danych programu SQL Server jako procedur składowanych, skrypty T-SQL, które zawierają instrukcje języka R lub jako kod języka R, zawierający języka T-SQL.  Zamiast wyodrębniania danych z bazy danych, a następnie załadowanie go do środowiska języka R, ładowania kodu języka R bezpośrednio do bazy danych i pozwolić mu działać w prawo obok danych.

Usługi Machine Learning została część na lokalnym serwerze SQL od 2016, ale jest stosunkowo nowe dla usługi Azure SQL Database.  Jest on obecnie w [ograniczonej wersji zapoznawczej](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap) , ale będzie rozwijać.


### <a name="next-steps"></a>Kolejne kroki
* [Uruchamianie kodu języka R na platformie Azure przy użyciu mrsdeploy](http://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [W usłudze Machine Learning Server w chmurze](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [Dodatkowe zasoby dotyczące serwer Machine Learning i Microsoft R](https://docs.microsoft.com/machine-learning-server/resources-more)
* [R na platformie Azure](https://github.com/yueguoguo/r-on-azure) — Przegląd pakietów, narzędzi i analizy przypadków: miejsca do używania języka R na platformie Azure

---

<sub>Jest to logo R &copy; 2016 R Foundation i jest używana zgodnie z postanowieniami [licencji Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/).</sub>

---
title: Wprowadzenie do usługi ML w usłudze Azure HDInsight
description: Dowiedz się, jak używać usług ML w usłudze HDInsight do tworzenia aplikacji do analizy danych Big Data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: 5108424c4e39c1c47710c0e25e4e22c4474e68ad
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941695"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co to jest usługa ML w usłudze Azure HDInsight

Microsoft Machine Learning Server jest dostępna jako opcja wdrażania podczas tworzenia klastrów usługi HDInsight na platformie Azure. Typ klastra, który zapewnia tę opcję, nosi nazwę **usługi ml**. Ta funkcja zapewnia analitykom danych, statystyków i programistom języka R dostęp na żądanie do skalowalnych, dystrybuowanych metod analizy w usłudze HDInsight.

Usługi ML w usłudze HDInsight zapewniają najnowsze możliwości analizy opartej na języku R na zestawach danych praktycznie dowolnego rozmiaru, ładowanych do usługi Azure Blob lub magazynu Data Lake. Ze względu na to, że klaster usługi ML jest oparty na języku R typu "open source", tworzone aplikacje oparte na języku R mogą korzystać z dowolnych pakietów języka R typu "open source". Dostępne są również procedury w ramach skalowania, czyli pakiet do analizy danych Big Data firmy Microsoft.

Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. W węźle brzegowym można uruchamiać równoległe funkcje rozproszone skalowania na wielu rdzeniach serwera węzła brzegowego. Można je również uruchamiać w węzłach klastra przy użyciu mapy usługi Hadoop skalowania w celu zmniejszenia lub Apache Spark kontekstów obliczeniowych.

Modele lub przewidywania powstałe w wyniku analizy mogą być pobierane do użytku lokalnego. Mogą również działać w innym miejscu na platformie Azure, w szczególności za pomocą [usługi sieci web](../../machine-learning/studio/publish-a-machine-learning-web-service.md) [Azure Machine Learning Studio](https://studio.azureml.net) .

## <a name="get-started-with-ml-services-on-hdinsight"></a>Wprowadzenie do usługi l Services w usłudze HDInsight

Aby utworzyć klaster usług ML w usłudze Azure HDInsight, podczas tworzenia klastra usługi HDInsight przy użyciu Azure Portal wybierz typ klastra **usług ml** . Typ klastra usługi ML zawiera ML Server w węzłach danych klastra i w węźle brzegowym, który służy jako strefa docelowa dla analiz opartych na usługach ML. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal,](../hdinsight-hadoop-create-linux-clusters-portal.md) aby zapoznać się z przewodnikiem dotyczącym tworzenia klastra.

## <a name="why-choose-ml-services-in-hdinsight"></a>Dlaczego warto wybrać usługi ML w usłudze HDInsight?

Usługi ML w usłudze HDInsight zapewniają następujące korzyści:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Innowacje AI firmy Microsoft i Open-Source

  Usługa l Services obejmuje wysoce skalowalny, rozproszony zestaw algorytmów, takich jak [kolekcję funkcji revoscaler](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [biblioteki revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)i [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , które mogą pracować z rozmiarem danych większym od rozmiaru pamięci fizycznej i uruchamiać na wielu różnych platformach w sposób dystrybuowany. Dowiedz się więcej na temat kolekcji niestandardowych [pakietów języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) firmy Microsoft i [pakietów Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) zawartych w produkcie.
  
  Usługi ML mostkuje te innowacje i wkłady firmy Microsoft pochodzące z społeczności typu "open source" (zestawu narzędzi języka R, Python i AI) na jednej platformie klasy korporacyjnej. Każdy pakiet uczenia maszynowego "open source" w języku R lub Python może współpracować ze wszystkimi zastrzeżonymi innowacyjnymi firmy Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Proste, bezpieczne i wysoce skalowane operacjonalizacji i administracja

  Przedsiębiorstwa opierają się na tradycyjnych odmianach i środowiskach inwestycji dużo czasu i wysiłku na operacjonalizacji. Wynika to z niezryczałtowanych kosztów i opóźnień, w tym czasu tłumaczenia dla modeli, iteracji, aby zachować ich ważność i bieżące, zatwierdzać prawa i zarządzać uprawnieniami za pomocą operacjonalizacji.

  Usługi Virtual Services oferują [operacjonalizacji](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)klasy korporacyjnej w tym przypadku, gdy model uczenia maszynowego zostanie ukończony, zajmie zaledwie kilka kliknięć, aby generować interfejsy API usług sieci Web. Te [usługi sieci Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) są hostowane w sieci na serwerze w chmurze i mogą być zintegrowane z aplikacjami biznesowymi. Możliwość wdrażania w elastycznej siatce umożliwia bezproblemowe skalowanie z potrzebami firmy zarówno w przypadku partii, jak i w czasie rzeczywistym. Aby uzyskać instrukcje, zobacz [OPERACJONALIZOWAĆ ml Services w usłudze HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Najważniejsze funkcje usługi ML Services w usłudze HDInsight

Poniższe funkcje są zawarte w usłudze ML w usłudze HDInsight.

| Kategoria funkcji | Opis |
|------------------|-------------|
| Język R — włączony | [Pakiety języka r](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) dla rozwiązań pisanych w języku r, z dystrybucją typu open source dla języka r i infrastruktury czasu wykonywania na potrzeby wykonywania skryptów. |
| Python — włączono | [Moduły języka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) dla rozwiązań utworzonych w języku Python, z dystrybucją typu open source dla języka Python i infrastruktury czasu wykonywania na potrzeby wykonywania skryptów.
| [Modele wstępnie nauczone](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Na potrzeby analizy wizualizacji i tekstu tonacji można przystąpić do oceny danych, które zapewniasz. |
| [Wdrażanie i korzystanie z](r-server-operationalize.md) | Operacjonalizować serwer i wdrażaj rozwiązania jako usługę sieci Web. |
| [Zdalne wykonywanie](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Uruchom sesje zdalne w klastrze usługi ML w sieci z poziomu stacji roboczej klienta. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opcje przechowywania danych dla usług ML w usłudze HDInsight

Magazyn domyślny dla systemu plików HDFS klastrów usługi HDInsight można skojarzyć z kontem usługi Azure Storage lub Azure Data Lake Storage. To skojarzenie gwarantuje, że dane przesyłane do magazynu klastra podczas analizy są trwałe, a dane są dostępne nawet po usunięciu klastra. Istnieją różne narzędzia do obsługi transferu danych do wybranej opcji magazynu, w tym funkcji przekazywania opartej na portalu dla konta magazynu i narzędzia [AzCopy](../../storage/common/storage-use-azcopy.md) .

Istnieje możliwość włączenia dostępu do dodatkowych obiektów blob i usług Data Lake Store podczas procesu aprowizacji klastra niezależnie od opcji magazynu podstawowego w użyciu.  Aby dowiedzieć się więcej o używaniu wielu kont magazynu, zobacz temat [Opcje usługi Azure Storage dla usług ml w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) .

Możesz również użyć [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) jako opcji magazynu do użycia w węźle brzegowym. Azure Files umożliwia zainstalowanie udziału plików utworzonego w usłudze Azure Storage w systemie plików Linux. Aby uzyskać więcej informacji na temat tych opcji przechowywania danych dla usług ML w klastrze usługi HDInsight, zobacz [Opcje usługi Azure Storage dla usług ml w usłudze HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Węzeł brzegowy usługi Access Services

Można nawiązać połączenie z usługą Microsoft ML Server w węźle brzegowym przy użyciu przeglądarki. Jest instalowana domyślnie podczas tworzenia klastra.  Możesz również połączyć się z węzłem brzegowym klastra z poziomu wiersza polecenia, używając protokołu SSH/wykreśleń w celu uzyskania dostępu do konsoli języka R.

## <a name="develop-and-run-r-scripts"></a>Opracowywanie i uruchamianie skryptów języka R

Tworzone i uruchamiane skrypty języka R mogą korzystać z dowolnych z pakietów języka R typu "open source", oprócz równoległych i rozproszonych procedur dostępnych w bibliotece skalowania. Ogólnie rzecz biorąc, skrypt, który jest uruchamiany z usługami ML w węźle brzegowym, działa w obrębie interpretera języka R w tym węźle. Wyjątkami są te kroki, które muszą wywołać funkcję skalowania z kontekstem obliczeniowym ustawionym na wartość ograniczenia mapy Hadoop (RxHadoopMR) lub Spark (obliczeniowego rxspark). W takim przypadku funkcja jest uruchamiana w sposób rozproszony przez węzły danych (zadania) klastra, które są skojarzone z odwołaniami do danych. Aby uzyskać więcej informacji na temat różnych opcji kontekstu obliczeniowego, zobacz [Opcje kontekstu obliczeniowego dla usług w usłudze ml w usłudze HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacjonalizowanie modelu

Po zakończeniu modelowania danych można operacjonalizować model, aby utworzyć prognozy dla nowych danych na platformie Azure lub lokalnie. Ten proces jest znany jako ocenianie. Ocenianie może odbywać się w usłudze HDInsight, Azure Machine Learning lub lokalnie.

### <a name="score-in-hdinsight"></a>Wyniki w usłudze HDInsight

Aby wypróbować w usłudze HDInsight, napisz funkcję języka R, która wywołuje model, aby utworzyć prognozy dla nowego pliku danych, który został załadowany do konta magazynu. Następnie Zapisz przewidywania z powrotem na koncie magazynu. Tę procedurę można uruchomić na żądanie na węźle brzegowym klastra lub przy użyciu zaplanowanego zadania.

### <a name="score-in-azure-machine-learning-aml"></a>Wynik w Azure Machine Learning (AML)

Aby wypróbować Azure Machine Learning, Użyj pakietu języka R "Open Azure Machine Learning source", który jest znany jako [Azure](https://cran.r-project.org/src/contrib/Archive/AzureML/) , aby opublikować model jako usługę sieci Web platformy Azure. Dla wygody ten pakiet jest wstępnie zainstalowany w węźle brzegowym. Następnie użyj funkcji w Azure Machine Learning, aby utworzyć interfejs użytkownika dla usługi sieci Web, a następnie Wywołaj usługę sieci Web zgodnie z wymaganiami dla oceny.

Jeśli wybierzesz tę opcję, musisz przekonwertować wszystkie obiekty modelu skalowania na równoważne obiekty modelu "open source", które będą używane z usługą sieci Web. W przypadku tej konwersji Użyj funkcji przekształcania skalowania, `as.randomForest()` na przykład dla modeli opartych na modelu.

### <a name="score-on-premises"></a>Ocena w środowisku lokalnym

Aby oceniać lokalne po utworzeniu modelu, można serializować model w języku R, pobrać go, deserializować, a następnie użyć go do oceny nowych danych. Nowe dane można wystawić przy użyciu opisanego wcześniej podejścia do [oceny usługi HDInsight](#score-in-hdinsight) lub [usług sieci Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Obsługa klastra

### <a name="install-and-maintain-r-packages"></a>Instalowanie i konserwowanie pakietów języka R

Większość używanych pakietów języka R jest wymagana w węźle brzegowym, ponieważ większość kroków skryptów języka R jest w tej chwili uruchamiana. Aby zainstalować dodatkowe pakiety języka r w węźle brzegowym, można użyć `install.packages()` metody w języku R.

Jeśli używasz tylko procedur z biblioteki skalowania w klastrze, zazwyczaj nie musisz instalować dodatkowych pakietów R w węzłach danych. Mogą jednak być potrzebne dodatkowe pakiety do obsługi **rxExec** lub **RxDataStep** wykonywania w węzłach danych.

W takich przypadkach dodatkowe pakiety mogą być instalowane z akcją skryptu po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługami ml w klastrze usługi HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Zmień ustawienia pamięci Apache Hadoop MapReduce

Klaster można zmodyfikować, aby zmienić ilość pamięci dostępnej dla usług ML, gdy jest uruchomione zadanie MapReduce. Aby zmodyfikować klaster, użyj interfejsu użytkownika Apache Ambari, który jest dostępny za pomocą bloku Azure Portal w klastrze. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do interfejsu użytkownika Ambari dla klastra, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

Istnieje również możliwość zmiany ilości pamięci dostępnej dla usług ML przy użyciu przełączników Hadoop w wywołaniu **RxHadoopMR** w następujący sposób:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skalowanie klastra

Istniejący klaster usług ML w usłudze HDInsight można skalować w górę lub w dół w portalu. Skalowanie w górę pozwala uzyskać dodatkową pojemność, która może być potrzebna w przypadku większych zadań przetwarzania lub można skalować klaster w stan bezczynności. Aby uzyskać instrukcje dotyczące skalowania klastra, zobacz [Zarządzanie klastrami usługi HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Obsługa systemu

Konserwacja w celu zastosowania poprawek systemu operacyjnego i innych aktualizacji jest wykonywana na podstawowych maszynach wirtualnych z systemem Linux w klastrze usługi HDInsight w ciągu kilku godzin. Zwykle konserwacja odbywa się o godzinie 3:30 (na podstawie czasu lokalnego dla maszyny wirtualnej) co poniedziałek i czwartek. Aktualizacje są wykonywane w taki sposób, że nie wpływają na więcej niż jeden kwartał klastra jednocześnie.

Ponieważ węzły główne są nadmiarowe i nie mają wpływu na wszystkie węzły danych, wszystkie zadania, które są uruchomione w tym czasie, mogą spowalniać działanie. Jednak powinny one nadal działać. Wszelkie niestandardowe oprogramowanie lub dane lokalne są zachowywane w ramach tych zdarzeń konserwacyjnych, chyba że wystąpi błąd krytyczny, który wymaga ponownego skompilowania klastra.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opcje środowiska IDE dla usług ML w usłudze HDInsight

Węzeł brzegowy systemu Linux klastra usługi HDInsight jest strefą docelową analizy na podstawie języka R. Najnowsze wersje usługi HDInsight zapewniają domyślną instalację serwera RStudio na węźle brzegowym jako środowisko IDE oparte na przeglądarce. Korzystanie z serwera RStudio jako środowiska IDE do tworzenia i wykonywania skryptów języka R może być znacznie bardziej wydajne niż w przypadku korzystania z konsoli języka R.

Ponadto można zainstalować środowisko IDE pulpitu i używać go do uzyskiwania dostępu do klastra za pomocą zdalnego kontekstu obliczeniowego MapReduce lub Spark. Dostępne opcje to: [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) firmy Microsoft (RTVS), RStudio i Walware. [](http://www.walware.de/goto/statet)

Ponadto możesz uzyskać dostęp do konsoli języka R w węźle brzegowym, wpisując **R** w wierszu polecenia systemu Linux po nawiązaniu połączenia za pośrednictwem protokołu SSH lub wyciągania. W przypadku korzystania z interfejsu konsoli można uruchomić Edytor tekstu dla tworzenia skryptów języka R w innym oknie, a następnie wyciąć i wkleić sekcje skryptu do konsoli języka R zgodnie z potrzebami.

## <a name="pricing"></a>Cennik

Ceny skojarzone z klastrem usługi HDInsight usług w usłudze ML mają strukturę podobną do cen dla innych typów klastrów HDInsight. Są one oparte na wymiarach podstawowych maszyn wirtualnych w obrębie nazwy, danych i węzłów brzegowych, z dodaniem wzroście-godzinowego. Aby uzyskać więcej informacji, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z usług ML w klastrach usługi HDInsight, zobacz następujące tematy:

* [Wykonywanie skryptu języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu serwera RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Opcje magazynu dla klastra usług ML w usłudze HDInsight](r-server-storage.md)

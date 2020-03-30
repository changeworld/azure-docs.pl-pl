---
title: Wprowadzenie do usług ML w usłudze Azure HDInsight
description: Dowiedz się, jak używać usług ML w programie HDInsight do tworzenia aplikacji do analizy dużych zbiorów danych.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77122375"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co to są usługi ML w usłudze Azure HDInsight

Program Microsoft Machine Learning Server jest dostępny jako opcja wdrażania podczas tworzenia klastrów usługi HDInsight na platformie Azure. Typ klastra, który udostępnia tę opcję, nosi nazwę **USŁUGI ML**. Ta funkcja zapewnia analitykom danych, statystykom i programistom R dostęp na żądanie do skalowalnych, rozproszonych metod analizy w programie HDInsight.

Usługi ML w usłudze HDInsight zapewniają najnowsze możliwości analizy opartej na r na zestawach danych o praktycznie dowolnym rozmiarze, ładowanych do magazynu obiektów Blob platformy Azure lub usługi Data Lake. Ponieważ klaster usług ML jest zbudowany na r typu open source, aplikacje oparte na języku R, które tworzysz, mogą korzystać z dowolnego z ponad 8000 pakietów języka R typu open source. Procedury w ScaleR, pakiet analizy dużych zbiorów danych firmy Microsoft są również dostępne.

Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. W węźle brzegowym można uruchamiać równoległe funkcje rozproszone ScaleR na rdzeniach serwera węzłów brzegowych. Można również uruchomić je w węzłach klastra przy użyciu programu ScaleR's Hadoop Map Reduce lub Apache Spark obliczeń kontekstów.

Modele lub prognozy, które wynikają z analizy można pobrać do użytku lokalnego. Można je również zudralizować w innym miejscu na platformie Azure, w szczególności za pośrednictwem [usługi Azure Machine Learning Studio (klasycznej).](https://studio.azureml.net) [web service](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)

## <a name="get-started-with-ml-services-on-hdinsight"></a>Wprowadzenie do usług ML w u klienta HDInsight

Aby utworzyć klaster usług ML w usłudze Azure HDInsight, wybierz typ klastra **usług ML** podczas tworzenia klastra USŁUGI HDInsight przy użyciu portalu Azure. Typ klastra usług ML zawiera serwer ML w węzłach danych klastra i w węźle brzegowym, który służy jako strefa docelowa dla analizy opartej na usługach ML. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure,](../hdinsight-hadoop-create-linux-clusters-portal.md) aby uzyskać instrukcje dotyczące tworzenia klastra.

## <a name="why-choose-ml-services-in-hdinsight"></a>Dlaczego warto wybrać usługi ML w HDInsight?

Usługi ML w hdinsight zapewnia następujące korzyści:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Innowacje w zakresie si firmy Microsoft i open source

  Usługi ML obejmują wysoce skalowalny, rozproszony zestaw algorytmów, takich jak [RevoscaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)i [microsoftML,](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) które mogą pracować na rozmiarach danych większych niż rozmiar pamięci fizycznej i działać na różnych platformach w sposób rozproszony. Dowiedz się więcej o kolekcji niestandardowych [pakietów języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) firmy Microsoft i [pakietów Pythona](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) dołączonych do produktu.
  
  Usługi ML łączy te innowacje i wkłady firmy Microsoft pochodzące ze społeczności open source (R, Python i AI) na jednej platformie klasy korporacyjnej. Każdy pakiet uczenia maszynowego typu open source typu R lub Python może współpracować ze swoimi zastrzeżonymi innowacjami firmy Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Prosta, bezpieczna i zakrojona na szeroką skalę operacjonalizacja i administracja

  Przedsiębiorstwa opierające się na tradycyjnych paradygmatach i środowiskach inwestują dużo czasu i wysiłku w kierunku operationalization. Powoduje to zawyżone koszty i opóźnienia, w tym czas tłumaczenia dla modeli, iteracje, aby zachować ich prawidłowość i aktualność, zatwierdzenie regulacyjne i zarządzanie uprawnieniami poprzez operacjonalizację.

  Usługi ML oferuje operationalization klasy [przedsiębiorstwa,](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)w tym, że po ukończeniu modelu uczenia maszynowego, trwa zaledwie kilka kliknięć do generowania interfejsów API usług sieci web. Te [usługi sieci web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) są hostowane w siatce serwera w chmurze i mogą być zintegrowane z aplikacjami biznesowymi. Możliwość wdrażania w elastycznej siatce umożliwia bezproblemowe skalowanie zgodnie z potrzebami firmy, zarówno w przypadku oceniania wsadowego, jak i w czasie rzeczywistym. Aby uzyskać instrukcje, zobacz [Operationalize Usługi ML w programie HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Typ klastra usług ML w programie HDInsight jest obsługiwany tylko w programie HDInsight 3.6. HDInsight 3.6 ma przejść na emeryturę 31 grudnia 2020 roku.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Najważniejsze cechy usług ML w programie HDInsight

Następujące funkcje są zawarte w usługi ML na HDInsight.

| Kategoria funkcji | Opis |
|------------------|-------------|
| Funkcja R | [Pakiety języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) dla rozwiązań napisanych w języku R, z dystrybucją języka R typu open source i infrastrukturą wykonawczą do wykonywania skryptów. |
| Python z włączoną obsługą | [Moduły Języka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) dla rozwiązań napisanych w języku Python, z dystrybucją open source języka Python i infrastrukturą wykonawczą do wykonywania skryptów.
| [Wstępnie wyszkolone modele](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Do analizy wizualnej i analizy tonacji tekstu, gotowy do uzyskania danych, które podasz. |
| [Wdrażanie i korzystanie z](r-server-operationalize.md) | Operacjonalizacja serwera i wdrażanie rozwiązań jako usługi sieci web. |
| [Zdalne wykonanie](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Rozpocznij sesje zdalne w klastrze usług ML w sieci ze stacji roboczej klienta. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opcje przechowywania danych dla usług ML w programie HDInsight

Domyślny magazyn dla systemu plików HDFS klastrów HDInsight może być skojarzony z kontem usługi Azure Storage lub usługą Azure Data Lake Storage. To skojarzenie zapewnia, że niezależnie od danych jest przekazytywał do magazynu klastra podczas analizy jest trwałe i dane są dostępne nawet po usunięciu klastra. Istnieją różne narzędzia do obsługi transferu danych do wybranej opcji przechowywania, w tym funkcja przesyłania opartego na portalu konta magazynu i narzędzie [AzCopy.](../../storage/common/storage-use-azcopy.md)

Istnieje możliwość włączenia dostępu do dodatkowych magazynów obiektów Blob i Data lake podczas procesu inicjowania obsługi administracyjnej klastra, niezależnie od używanej opcji magazynu podstawowego.  Zobacz opcje usługi usługi ML usługi [usługi platformy Azure w programie HDInsight,](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) aby dowiedzieć się więcej na temat korzystania z wielu kont magazynu.

Można również użyć [usługi Azure Files](../../storage/files/storage-how-to-use-files-linux.md) jako opcji magazynu do użycia w węźle krawędzi. Usługa Azure Files umożliwia zainstalowanie udziału plików utworzonego w usłudze Azure Storage w systemie plików systemu Linux. Aby uzyskać więcej informacji na temat tych opcji przechowywania danych dla usług ML Services w klastrze HDInsight, zobacz [Opcje usługi Azure Storage dla usług ML w usłudze HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Węzeł brzegowy usług ML programu Access

Za pomocą przeglądarki można połączyć się z serwerem Microsoft ML Server w węźle brzegowym. Jest on instalowany domyślnie podczas tworzenia klastra.  Można również połączyć się z węzłem krawędzi klastra z wiersza polecenia za pomocą SSH/PuTTY, aby uzyskać dostęp do konsoli Języka R.

## <a name="develop-and-run-r-scripts"></a>Tworzenie i uruchamianie skryptów języka R

Skrypty języka R, które tworzysz i uruchamiasz, mogą używać dowolnego z ponad 8000 pakietów języka R typu open source oprócz równoległych i rozproszonych procedur dostępnych w bibliotece ScaleR. Ogólnie rzecz biorąc skrypt, który jest uruchamiany z usługami ML w węźle brzegowym działa w interpreterze języka R w tym węźle. Wyjątkami są te kroki, które muszą wywołać funkcję ScaleR z kontekstem obliczeniowym, który jest ustawiony na Hadoop Map Reduce (RxHadoopMR) lub Spark (RxSpark). W takim przypadku funkcja działa w sposób rozproszony między tymi węzłami danych (zadania) klastra, które są skojarzone z danymi, do których się odwołuje. Aby uzyskać więcej informacji na temat różnych opcji kontekstu obliczeniowego, zobacz [Opcje kontekstu obliczeń dla usług ML w programie HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacjonalizowanie modelu

Po zakończeniu modelowania danych można operacjalizować model, aby prognozować nowe dane z platformy Azure lub lokalnie. Ten proces jest znany jako punktacji. Ocenianie można wykonać w usłudze HDInsight, usłudze Azure Machine Learning lub lokalnie.

### <a name="score-in-hdinsight"></a>Wynik w HDInsight

Aby uzyskać wynik w HDInsight, napisz funkcję R, która wywołuje model, aby prognozować nowy plik danych, który został załadowany do konta magazynu. Następnie zapisz prognozy z powrotem na koncie magazynu. Tę procedurę można uruchomić na żądanie w węźle brzegowym klastra lub przy użyciu zaplanowanego zadania.

### <a name="score-in-azure-machine-learning-aml"></a>Wynik w usłudze Azure Machine Learning (AML)

Aby uzyskać wynik przy użyciu usługi Azure Machine Learning, użyj pakietu języka R usługi Azure Azure open source znanego jako [AzureML,](https://cran.r-project.org/src/contrib/Archive/AzureML/) aby opublikować model jako usługę sieci Web platformy Azure. Dla wygody ten pakiet jest wstępnie zainstalowany w węźle brzegowym. Następnie użyj obiektów w usłudze Azure Machine Learning, aby utworzyć interfejs użytkownika dla usługi sieci web, a następnie wywołać usługę sieci web w razie potrzeby do oceniania.

Jeśli wybierzesz tę opcję, należy przekonwertować wszystkie obiekty modelu ScaleR na równoważne obiekty modelu open source do użytku z usługą sieci web. Użyj funkcji przymusu ScaleR, takich jak `as.randomForest()` dla modeli opartych na zespole, dla tej konwersji.

### <a name="score-on-premises"></a>Ocena lokalnie

Aby uzyskać wynik lokalnie po utworzeniu modelu, można serializować model w R, pobrać go, de-serialize go, a następnie użyć go do oceniania nowych danych. Nowe dane można uzyskać przy użyciu podejścia opisanego wcześniej w [Score in HDInsight](#score-in-hdinsight) lub za pomocą [usług internetowych.](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)

## <a name="maintain-the-cluster"></a>Obsługa klastra

### <a name="install-and-maintain-r-packages"></a>Instalowanie i obsługa pakietów R

Większość pakietów języka R, których używasz, jest wymagana w węźle brzegowej, ponieważ większość kroków skryptów języka R jest tam uruchamiana. Aby zainstalować dodatkowe pakiety R w węźle krawędzi, można użyć `install.packages()` metody w R.

Jeśli używasz tylko procedur z biblioteki ScaleR w klastrze, zwykle nie trzeba instalować dodatkowych pakietów języka R w węzłach danych. Jednak może być konieczne dodatkowe pakiety do obsługi wykonywania **rxExec** lub **RxDataStep** w węzłach danych.

W takich przypadkach dodatkowe pakiety mogą być instalowane z akcji skryptu po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługami ML w klastrze HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Zmiana ustawień pamięci Apache Hadoop MapReduce

Klaster można zmodyfikować, aby zmienić ilość pamięci, która jest dostępna dla usług ML, gdy jest uruchomiony zadanie MapReduce. Aby zmodyfikować klaster, należy użyć interfejsu użytkownika Apache Ambari, który jest dostępny za pośrednictwem bloku portalu Azure dla klastra. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do interfejsu użytkownika ambari dla klastra, zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

Istnieje również możliwość zmiany ilości pamięci dostępnej dla usług ML za pomocą przełączników Hadoop w wywołaniu **RxHadoopMR** w następujący sposób:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skalowanie klastra

Istniejący klaster usług ML w programie HDInsight można skalować w górę lub w dół za pośrednictwem portalu. Skalowanie w górę, można uzyskać dodatkową pojemność, która może być potrzebna do większych zadań przetwarzania lub można skalować z powrotem klastra, gdy jest bezczynny. Aby uzyskać instrukcje dotyczące skalowania klastra, zobacz [Zarządzanie klastrami HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Obsługa systemu

Konserwacja w celu zastosowania poprawek systemu operacyjnego i innych aktualizacji jest wykonywana na podstawowych maszynach wirtualnych z systemem Linux w klastrze HDInsight poza godzinami pracy. Zazwyczaj konserwacja odbywa się o godzinie 3:30 (na podstawie czasu lokalnego maszyny Wirtualnej) w każdy poniedziałek i czwartek. Aktualizacje są wykonywane w taki sposób, że nie mają wpływu na więcej niż jedną czwartą klastra naraz.

Ponieważ węzły głównego są nadmiarowe i nie wszystkie węzły danych są zagrożone, wszystkie zadania, które są uruchomione w tym czasie może spowolnić. Jednak powinny one nadal działać do końca. Wszelkie niestandardowe oprogramowanie lub dane lokalne, które masz jest zachowywany przez te zdarzenia konserwacji, chyba że wystąpi katastrofalny błąd, który wymaga odbudowy klastra.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opcje IDE dla usług ML w programie HDInsight

Węzeł krawędzi systemu Linux klastra HDInsight jest strefą lądowania dla analizy opartej na r. Najnowsze wersje usługi HDInsight zapewniają domyślną instalację serwera RStudio server w węźle brzegowym jako ide oparty na przeglądarce. Korzystanie z serwera RStudio jako IDE do tworzenia i wykonywania skryptów języka R może być znacznie bardziej produktywne niż tylko przy użyciu konsoli Języka R.

Ponadto można zainstalować ide pulpitu i używać go do uzyskiwania dostępu do klastra za pomocą zdalnego mapreduce lub kontekstu obliczeniowego Platformy Spark. Opcje obejmują narzędzia R firmy Microsoft [dla programu Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio i [statet oparty](http://www.walware.de/goto/statet)na przyćmieć firmy Walware.

Ponadto można uzyskać dostęp do konsoli R w węźle krawędzi, wpisując **R** w wierszu polecenia Linuksa po nawiązaniu połączenia przez SSH lub PuTTY. Podczas korzystania z interfejsu konsoli, jest wygodne, aby uruchomić edytor tekstu dla tworzenia skryptów języka R w innym oknie i wycinać i wklejać sekcje skryptu do konsoli Języka R w razie potrzeby.

## <a name="pricing"></a>Cennik

Ceny skojarzone z klastrem USŁUGI ML HDInsight są skonstruowane podobnie do cen innych typów klastra HDInsight. Są one oparte na wielkości podstawowych maszyn wirtualnych w nazwa, dane i węzły krawędzi, z dodatkiem podniesienia godzin core. Aby uzyskać więcej informacji, zobacz [HDInsight cennik](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z usług ML w klastrach HDInsight, zobacz następujące tematy:

* [Wykonywanie skryptu języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu serwera RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Opcje pamięci masowej klastra usług ML w programie HDInsight](r-server-storage.md)

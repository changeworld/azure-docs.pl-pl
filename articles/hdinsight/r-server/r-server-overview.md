---
title: Wprowadzenie do języka R Server w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać R Server w usłudze HDInsight do tworzenia aplikacji do analizy danych big data.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19c286db9a8a2aa537badc83d98a1b74b73e9873
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Wprowadzenie do języka R Server i open source możliwości R w usłudze HDInsight

Serwer R firmy Microsoft (znanej także jako Microsoft Machine Learning serwera) jest dostępna jako opcję wdrażania, podczas tworzenia klastrów usługi HDInsight w systemie Azure. Typ klastra, który zawiera tę opcję, jest nazywany **R Server**. Ta funkcja zapewnia analityków danych, chi i programistom R dostęp na żądanie do skalowalnych, rozproszonych metody analizy w usłudze HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Serwer R w usłudze HDInsight zawiera najnowszych funkcji analizy na podstawie R po zestawów danych w praktycznie dowolnej wielkości, załadowane do magazynu obiektów Blob platformy Azure lub usługi Data Lake. Ponieważ klaster serwera R jest zbudowany na R open source, tworzone aplikacje oparte na R mogą korzystać z dowolnej 8000 + open source R pakietów. Dostępne są także procedury w ScaleR, pakiet analizy danych big data firmy Microsoft dołączony R Server.

Węzeł krawędzi klastra umożliwia wygodne Połącz się z klastrem i uruchamiania skryptów R. Z węzłem krawędzi istnieje możliwość uruchomionych zrównoleglone funkcje rozproszonej ScaleR na rdzeni serwera węzła krawędzi. Można również uruchomić je w węzłach klastra przy użyciu jego ScaleR Hadoop mapy zmniejszyć lub Spark obliczeniowe kontekstach.

Do użytku lokalnego można pobrać modele lub prognoz, które wynikają z analizy. One może również być operationalized w innym miejscu na platformie Azure, w szczególności za pośrednictwem [Azure Machine Learning Studio](http://studio.azureml.net) [usługi sieci web](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-server-on-hdinsight"></a>Wprowadzenie do oprogramowania R Server w usłudze HDInsight

Aby utworzyć klaster R Server w usłudze Azure HDInsight, wybierz **R Server** typ klastra podczas tworzenia klastra usługi HDInsight przy użyciu portalu Azure. Typ klastra R Server zawiera R Server w węzłach danych klastra i węzła krawędzi, która służy jako strefy docelowej na serwerze R analizy. Zobacz [wprowadzenie R Server w usłudze HDInsight](r-server-get-started.md) przewodnik dotyczący sposobu tworzenia klastra.

## <a name="why-choose-r-server-in-hdinsight"></a>Dlaczego R Server w usłudze HDInsight?

Serwer R w usłudze HDInsight zapewnia możliwość użycia R Server na platformie Azure. Serwer R obejmuje:

+ **Najlepsze innowacji AI firmy Microsoft i typu open source**

  Firma Microsoft dokłada starań udostępnić AI dla każdej osoby i organizacji. R Server zawiera bogaty zestaw wysoce skalowalnych, rozproszonych zestawem algorytmów, takich jak [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), i [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) można pracować większe rozmiary danych niż rozmiar pamięci fizycznej, a następnie uruchom na różnych platformach w sposób rozproszony. Dowiedz się więcej na temat kolekcji firmy Microsoft przez niestandardowe [pakiety języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) i [pakietów języka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) dołączone do produktu.
  
  Serwer R pomost te innowacje Microsoft a danych pochodzących ze społecznością typu open source (narzędzi R, Python i AI) wszystkich na platformie pojedynczego korporacyjnej. Dowolny pakiet R lub Python uczenia maszynowego typu open source można pracować side-by-side z dowolnego zastrzeżonych innowacji firmy Microsoft. 

+ **Operationalization proste, bezpieczną i wysokiej skali i administracji**

  Przedsiębiorstwa polegania na operationalization tradycyjnych wzorcami oraz środowisk przechodzili inwestycją dużo czasu i pracy w kierunku tego obszaru. Słabe punkty często powoduje w kosztach nadmuchany i obejmują opóźnienia: podczas tłumaczenia dla modeli, iteracji, aby zapewnić ich prawidłowe i aktualne, zatwierdzenia przepisami, zarządzanie uprawnieniami do operationalization.

  Serwer R oferuje najlepsze w klasie [operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) — od czasu ukończenia modelu uczenia maszynowego, zajmuje kilka kliknięć, aby wygenerować usług sieci web API. Te [usług sieci web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) znajdują się w siatce serwera lokalnego lub w chmurze i może zostać zintegrowany z aplikacji — biznesowych. Możliwość wdrażania do skalowania bezproblemowo z potrzebami firmy zarówno dla partii i oceniania w czasie rzeczywistym umożliwia elastyczne siatki. Aby uzyskać instrukcje, zobacz [Operacjonalizacji R Server w usłudze HDInsight](r-server-operationalize.md).

+ **Głębokie ekosystemu testowania dostarczania Powodzenie klienta z optymalną całkowity koszt posiadania**

  Dostępne w podróży, co inteligentnego swoich aplikacji lub po prostu pożądane dowiedzieć się nowych świecie AI i uczenia maszynowego, należy rozpocząć odpowiednich zasobów, aby ułatwić im osób. Oprócz tej dokumentacji firma Microsoft udostępnia kilka materiałów szkoleniowych i zaangażowany kilku partnerów szkolenia ułatwiających lepsze poznanie i szybko stać się produktywności.


## <a name="key-features-of-r-server-on-hdinsight"></a>Najważniejsze funkcje R Server w usłudze HDInsight

Poniższe funkcje dostępne w R Server w usłudze HDInsight.

| Funkcja kategorii | Opis |
|------------------|-------------|
| Włączone R | [Pakiety języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) rozwiązań zapisany w R, za pomocą typu open source dystrybucję R i środowiska wykonawczego infrastruktury do wykonania skryptu. |
| Z obsługą języka Python | [Modułów środowiska Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) rozwiązań napisany w języku Python, za pomocą typu open source dystrybucję Python i środowiska wykonawczego infrastruktury do wykonania skryptu.  
| [Wstępnie przeszkolone modeli](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Visual analizy i analiza wskaźniki nastrojów klientów tekstu gotowy do wyniku danych musisz podać. |
| [Wdrażanie i korzystanie](r-server-operationalize.md) | Operacjonalizuj serwera i wdrażanie rozwiązań jako usługę sieci web. |
| [Zdalne wykonywanie kodu](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | Sesje zdalne na R Server w sieci z poziomu stacji roboczej użytkownika. |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>Opcje magazynu danych dla serwera R w usłudze HDInsight

Domyślny magazyn systemu plików HDFS klastrów usługi HDInsight można skojarzyć z konta magazynu Azure lub usługi Azure Data Lake Store. To skojarzenie gwarantuje, że niezależnie od danych została przekazana do klastra magazynu podczas analizy wprowadzono trwałe i dane są dostępne nawet po usunięciu klastra. Istnieją różne narzędzia do obsługi transferu danych do opcji magazynowania wybierz, w tym funkcji przekazywania oparte na portalu konta magazynu i [AzCopy](../../storage/common/storage-use-azcopy.md) narzędzia.

Istnieje możliwość dodania dostęp do dodatkowych obiektów Blob i usługi Data lake przechowuje w procesie niezależnie od opcji magazynu podstawowy używany inicjowania obsługi klastra. Zobacz [wprowadzenie R Server w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) informacji o dodawaniu dostęp do dodatkowych kont. Zobacz dodatkowych [opcje usługi Azure Storage platformy R Server w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) artykuł, aby dowiedzieć się więcej o korzystaniu z wielu kont magazynu.

Można również użyć [plików Azure](../../storage/files/storage-how-to-use-files-linux.md) jako opcji magazynu do użycia w węźle krawędzi. Usługa pliki Azure umożliwia zainstalowanie udziału plików, który został utworzony w usłudze Azure Storage w systemie Linux. Aby uzyskać więcej informacji o tych opcjach przechowywania danych platformy R Server w klastrze usługi HDInsight, zobacz [opcje usługi Azure Storage platformy R Server w usłudze HDInsight](r-server-storage.md).

## <a name="access-machine-learning-server-on-the-cluster"></a>Dostęp do maszyny Learning serwera w klastrze

W węźle krawędzi za pomocą przeglądarki sieci może nawiązać Microsoft Machine Learning serwera. Jest instalowana domyślnie podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [uzyskać stared z serwerem R w usłudze HDInsight](r-server-get-started.md). Można również nawiązać serwerem ML z wiersza polecenia przy użyciu protokołu SSH/PuTTY dostęp do konsoli R. 

## <a name="develop-and-run-r-scripts"></a>Tworzenie i uruchamianie skryptów R

Tworzenie i uruchamianie skryptów R, można użyć dowolnego z 8000 + open source R pakiety oprócz procedury zrównoleglone i rozproszone dostępne w bibliotece ScaleR. Ogólnie rzecz biorąc skrypt, który jest uruchamiany z serwerem R w węźle krawędzi jest uruchamiany w ramach interpreter języka R w tym węźle. Wyjątki są te kroki, które należy wywołać funkcję ScaleR z kontekstem obliczeń, który jest ustawiony do Hadoop mapy Zmniejsz (RxHadoopMR) lub Spark (RxSpark). W takim przypadku funkcja działa w sposób rozproszonych w tych węzłów danych (zadania) klastra, które są skojarzone z danych, do których odwołuje się. Aby uzyskać więcej informacji na temat opcji kontekstu obliczeń różnych zobacz [obliczeniowe kontekstu opcje serwera R w usłudze HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacjonalizowanie modelu

Po zakończeniu Twojej modelowania danych, aby operacjonalizować modelu w celu tworzenia prognoz dotyczących nowych danych z platformy Azure i lokalnymi. Ten proces jest nazywany oceniania. Ocenianie może odbywać się w HDInsight, uczenie maszynowe Azure lub lokalnie.

### <a name="score-in-hdinsight"></a>Wynik w usłudze HDInsight
Wyniki w usłudze HDInsight, zapisać R funkcję, która wywołuje modelu w celu tworzenia prognoz dla nowego pliku danych, które zostały załadowane do konta magazynu. Następnie zapisz prognozy na konto magazynu. Tej procedury na żądanie można uruchamiać na krawędzi węzła klastra lub przy użyciu zaplanowanego zadania.

### <a name="score-in-azure-machine-learning-aml"></a>Wynik na platformie Azure maszyny uczenia (AML)
Wyniki za pomocą usługi Azure Machine Learning, za pomocą pakietu Azure Machine Learning R open source, znany jako [uczenie maszynowe Azure](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) Aby opublikować model jako usługę sieci web platformy Azure. Dla wygody ten pakiet jest wstępnie zainstalowane w węźle krawędzi. Następnie umożliwiają tworzenie interfejsu użytkownika dla usługi sieci web urządzenia w usłudze Azure Machine Learning, a następnie wywołać usługę sieci web, odpowiednio do oceniania.

Jeśli wybierzesz tę opcję, przekonwertuj wszystkie obiekty modelu ScaleR obiekty równoważne modelu open source do użycia z usługą sieci web. Używać funkcji koercja ScaleR, takich jak `as.randomForest()` dla modeli opartych na zespół, dla tej konwersji.

### <a name="score-on-premises"></a>Wynik lokalnymi
Wyniki lokalnego po utworzeniu modelu, można serializować modelu w R, pobierz go, zdeserializować go i następnie użyć jej do oceniania nowych danych. Można oceniać nowe dane przy użyciu podejścia opisanego wcześniej w [oceniania w usłudze HDInsight](#scoring-in-hdinsight) lub za pomocą [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Obsługa klastra

### <a name="install-and-maintain-r-packages"></a>Zainstalowanie i konserwowanie pakiety języka R

Większość pakietów języka R, używane są wymagane w węzła krawędzi od większości kroki uruchamianie skryptów R. Aby zainstalować dodatkowe pakiety języka R w węźle krawędzi, można użyć standardowego `install.packages()` metody w języku R.

Jeśli właśnie używasz procedury z biblioteki ScaleR w klastrze, zazwyczaj zbędna, nie można zainstalować dodatkowe pakiety języka R w węzłach danych. Jednak może być konieczne dodatkowe pakiety do korzystania z **rxExec** lub **RxDataStep** wykonywania w węzłach danych.

W takich przypadkach można zainstalować dodatkowe pakiety przy użyciu akcji skryptu po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [Zarządzanie serwerem R w klastrze usługi HDInsight](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Zmień ustawienia pamięci MapReduce z Hadoop

Klaster można zmodyfikować w taki sposób, aby zmienić ilość pamięci, który jest dostępny dla serwera R, gdy jest uruchomione zadania MapReduce. Aby zmodyfikować klastra, należy użyć interfejsu użytkownika narzędzia Ambari Apache, która jest dostępna za pośrednictwem portalu Azure bloku dla klastra. Aby uzyskać instrukcje dotyczące dostępu interfejsu użytkownika narzędzia Ambari dla klastra, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

Istnieje również możliwość zmienić ilość pamięci, która jest dostępna dla R Server przy użyciu platformy Hadoop przełączników w wywołaniu **RxHadoopMR** w następujący sposób:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skalowanie klastra

Istniejącego klastra R Server w usłudze HDInsight można skalować w górę lub w dół za pośrednictwem portalu. Skalując w górę, można uzyskać dodatkowej pojemności, może być potrzebny większy zadań przetwarzania lub można skalować wstecz klastra w stanie bezczynności. Aby uzyskać instrukcje dotyczące sposobu skalowania klastra, zobacz [Zarządzanie klastrami usługi HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Obsługa systemu

Na podstawowych maszynach wirtualnych systemu Linux w klastrze HDInsight jest wykonywana konserwacja do stosowania poprawek systemu operacyjnego i inne aktualizacje poza godzinami. Zazwyczaj każdy poniedziałek i czwartek konserwacji odbywa się na 3:30 AM (na podstawie czasu lokalnego dla maszyny Wirtualnej). Aktualizacji są wykonywane w taki sposób, że ich nie mieć wpływ na więcej niż kwartału klastra w czasie.  

Ponieważ węzłów głównych są zbędne, a nie wszystkie węzły danych ma wpływ, wszystkie zadania, które są uruchomione w tym czasie może spowolnić. Jednak należy nadal działają do zakończenia. Niestandardowe oprogramowania ani danych lokalnych, czy masz jest zachowywana przez te zdarzenia konserwacji o ile nie wystąpił błąd krytyczny wymagającego odbudowie klastra.

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>Opcje środowiska IDE R Server w klastrze usługi HDInsight

Węzeł krawędzi Linux klaster usługi HDInsight jest strefy docelowej do analizy na podstawie R. Nowe wersje HDInsight zawierają domyślnej instalacji serwera programu RStudio węzła krawędzi jako środowiska IDE bazujące na przeglądarce. Korzystania z serwera programu RStudio jako IDE dla rozwoju i wykonywanie skryptów R może być znacznie większą wydajność niż tylko przy użyciu konsoli R.

Ponadto można zainstalować pulpitu IDE i umożliwia dostęp do klastra przy użyciu zdalnego kontekstu obliczeń MapReduce lub Spark. Dostępne są następujące opcje firmy Microsoft [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), programu RStudio i Walware na podstawie Eclipse [StatET](http://www.walware.de/goto/statet).

Ponadto są dostępne Konsola R na węzeł krawędzi, wpisując **R** w wierszu polecenia systemu Linux, po nawiązaniu połączenia za pośrednictwem protokołu SSH lub PuTY. Przy użyciu konsoli interfejsu, jest odpowiedni moment na uruchamianie edytora tekstu dla rozwoju skryptu języka R w innym oknie i wycinanie i wklejanie sekcje skryptu do konsoli R, zgodnie z potrzebami.

## <a name="pricing"></a>Cennik

Ceny, które są skojarzone z klastrem usługi HDInsight, z serwerem R mają strukturę podobną do ceny dla standardowych klastrów usługi HDInsight. Są one oparte na zmiany rozmiaru źródłowej maszyn wirtualnych na nazwę, danych i węzły krawędzi, z uwzględnieniem wzrost core godzinnym. Aby uzyskać więcej informacji na temat cen usługi HDInsight, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o sposobie używania R Server w klastrach HDInsight, zobacz następujące tematy:

* [Wprowadzenie do korzystania z klastra serwera R w usłudze HDInsight](r-server-get-started.md)
* [Compute context options for R Server on HDInsight (Opcje kontekstu obliczeniowego dla oprogramowania R Server w usłudze HDInsight)](r-server-compute-contexts.md)
* [Azure Storage options for R Server on HDInsight (Opcje usługi Azure Storage dla oprogramowania R Server w usłudze HDInsight)](r-server-storage.md)

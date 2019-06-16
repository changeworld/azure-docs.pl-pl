---
title: Wprowadzenie do usługi ML w usłudze Azure HDInsight
description: Dowiedz się, jak używać usługi ML na HDInsight do tworzenia aplikacji do analizy danych big data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: d24686a094c524c5ce913eee4b711daf1c60100d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67130615"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Co to są usługi ML w usłudze Azure HDInsight

Serwer Microsoft Machine Learning jest dostępna jako opcja wdrażania podczas tworzenia klastrów HDInsight w systemie Azure. Typ klastra, który zawiera ta opcja jest wywoływana **usługi ML**. Ta funkcja zapewnia, że analityków danych, statystykom i programistom języka R dostęp na żądanie do skalowalnych, rozproszonych metod analizy w HDInsight.

Usługi uczenie Maszynowe na HDInsight udostępnia najnowsze możliwości analizy na podstawie języka R w zestawach danych z praktycznie dowolnego rozmiaru, załadowane do magazynu obiektów Blob platformy Azure lub usługi Data Lake. Ponieważ klaster usługi ML jest oparta na języka R typu open source, tworzone aplikacje na podstawie języka R mogą korzystać z dowolnej pakietów języka R typu open-source 8000 +. Procedury w ScaleR pakiet analizy danych big data firmy Microsoft są także dostępne.

Węzeł krawędzi klastra zapewnia wygodne miejsce do łączenia z klastrem i do uruchamiania skryptów języka R. Z węzłem krawędzi istnieje możliwość uruchamiania równoległego rozproszonych funkcji programu ScaleR między rdzeniami serwera węzła krawędzi. Można również uruchomić je w węzłach klastra za pomocą usługi Hadoop Mapreduce lub konteksty obliczeniowe aparatu Apache Spark przez program ScaleR.

Modele lub prognoz, wynikających z analizy można pobrać do użycia w środowisku lokalnym. One może również być przygotowany do działania innym miejscu na platformie Azure, w szczególności za pośrednictwem [Azure Machine Learning Studio](https://studio.azureml.net) [usługi sieci web](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Rozpoczynanie pracy z usługami uczenia Maszynowego na HDInsight

Aby utworzyć klaster usługi ML w usłudze Azure HDInsight, wybierz **usługi ML** typ klastra podczas tworzenia klastra usługi HDInsight przy użyciu witryny Azure portal. Typ klastra usługi ML obejmuje ML Server, na węzłami klastra i węzła krawędzi, która służy jako strefa docelowa dla analiza na podstawie usługi ML. Zobacz [rozpoczęcie korzystania z usługi uczenie Maszynowe na HDInsight](r-server-get-started.md) przewodnik dotyczący sposobu tworzenia klastra.

## <a name="why-choose-ml-services-in-hdinsight"></a>Dlaczego warto wybrać usługi ML w HDInsight?

Usługi ML w HDInsight zapewnia następujące korzyści:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Innowacje sztucznej Inteligencji firmy Microsoft i typu open-source

  Usługi ML obejmuje zestaw wysoce skalowalne i rozproszone algorytmów, takie jak [kolekcję funkcji RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [biblioteki](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), i [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , może działać względem danych większych niż rozmiar pamięci fizycznej, a następnie uruchom na różnych platformach, w sposób rozproszony. Dowiedz się więcej na temat kolekcji firmy Microsoft przez niestandardowe [pakiety języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) i [pakiety języka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) dołączonej do produktu.
  
  Usługi ML pomost te innowacji firmy Microsoft i wkład pochodzących od społeczności typu open source (R, Python i sztucznej Inteligencji zestaw narzędzi) wszystkie na podstawie platformy pojedynczego przeznaczonych dla przedsiębiorstw. Dowolnego pakietu języka R lub Python uczenia maszynowego typu open-source może działać równolegle z dowolnym własności innowacji firmy Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operacjonalizacja proste, bezpieczne i o dużej skali i Administracja

  Opierając się na tradycyjnych paradygmatów i środowiskach przedsiębiorstw zainwestować dużo czasu i wysiłku kierunku operacjonalizacji. To powoduje nadmuchany koszty i opóźnienia w tym podczas translacji dla modeli, iteracji, aby były prawidłowe zatwierdzenia bieżącej, przepisami i zarządzanie uprawnieniami za pomocą funkcji operacjonalizacji.

  Usługi ML oferują korporacyjnej [operacjonalizacji](https://docs.microsoft.com/machine-learning-server/what-is-operationalization), w tym po zakończeniu modelu usługi machine learning zajmuje zaledwie kilku kliknięć, aby wygenerować interfejsów API usług sieci web. Te [usług sieci web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) znajdują się w siatce serwera w chmurze i może zostać zintegrowany z aplikacjami line-of-business. Możliwość wdrażania na elastycznej siatki pozwala na skalowanie bezproblemowo z potrzeb biznesowych, zarówno dla usługi batch i ocenianie w czasie rzeczywistym. Aby uzyskać instrukcje, zobacz [Operacjonalizacji usługi ML w HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Kluczowe funkcje usługi uczenie Maszynowe na HDInsight

Poniższe funkcje dostępne w usługi uczenie Maszynowe na HDInsight.

| Kategoria funkcji | Opis |
|------------------|-------------|
| Z obsługą języka R | [Pakiety języka R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) rozwiązań napisanej w języku R, rozkład typu open source R i infrastrukturę czasu wykonywania do wykonania skryptu. |
| Z obsługą języka Python | [Moduły języka Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) rozwiązań napisanej w języku Python, rozkład typu open source języka Python i środowiska wykonawczego infrastruktury do wykonania skryptu.
| [Wstępnie szkolone modele](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Analizy wizualnej i analiza opinii w tekście chcesz oceniać dane należy podać. |
| [Wdrażanie i korzystanie](r-server-operationalize.md) | Operacjonalizowanie serwera i wdrażanie rozwiązań jako usługi sieci web. |
| [Zdalne wykonywanie kodu](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Sesje zdalne w klastrze usługi ML w sieci z poziomu stacji roboczej użytkownika. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opcje magazynu danych usługi ML w HDInsight

Domyślny magazyn dla systemu plików HDFS klastrów HDInsight może być skojarzony z kontem usługi Azure Storage lub usługi Azure Data Lake Storage. To skojarzenie gwarantuje, że dowolne dane są przekazywane do klastra magazynu podczas analizy jest trwałe i dane są dostępne, nawet w przypadku, po usunięciu klastra. Istnieją różne narzędzia do obsługi transferu danych do opcji magazynu, który wybierzesz, w tym funkcji przekazywania oparte na portalu konta usługi storage i [AzCopy](../../storage/common/storage-use-azcopy.md) narzędzia.

Istnieje możliwość włączenia dostępu do obiektu Blob dodatkowe i usługi Data lake przechowuje podczas procesu niezależnie od opcji magazynu podstawowego, używany do aprowizacji klastra. Zobacz [rozpoczęcie korzystania z usługi uczenie Maszynowe na HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) informacji na temat dodawania dostęp do dodatkowych kont. Zobacz [opcji usługi Azure Storage dla usługi ML w HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) artykuł, aby dowiedzieć się więcej o korzystaniu z wieloma kontami magazynu.

Można również użyć [usługi Azure Files](../../storage/files/storage-how-to-use-files-linux.md) jako opcji magazynu do użycia w węźle brzegowym. Usługa Azure Files umożliwia instalowanie udziału plików, który został utworzony w usłudze Azure Storage w systemie plików systemu Linux. Aby uzyskać więcej informacji o tych opcjach magazynu danych usługi ML w klastrze HDInsight, zobacz [opcji usługi Azure Storage dla usługi ML w HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Węzeł krawędzi usługi ML dostępu

Możesz nawiązać Microsoft ML Server, w węźle krawędzi, za pomocą przeglądarki. Instalowany domyślnie podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [uzyskać stared z usługami uczenia Maszynowego na HDInsight](r-server-get-started.md). Można również nawiązać węzłem brzegowym klastra z poziomu wiersza polecenia przy użyciu protokołu SSH/PuTTY uzyskać dostęp do konsoli R.

## <a name="develop-and-run-r-scripts"></a>Twórz i uruchamiaj skrypty języka R

Skrypty języka R, tworzenie i uruchamianie użyć dowolnej 8000 + typu open-source pakiety R oprócz procedury równoległego i rozproszonych, które muszą być dostępne w bibliotece programu ScaleR. Ogólnie rzecz biorąc skrypt, który jest uruchamiany za pomocą usługi ML w węźle brzegowym jest uruchamiany w ramach interpreter języka R w tym węźle. Wyjątki są te kroki, które należy wywołać funkcję programu ScaleR o kontekście obliczeniowym, który jest ustawiony na platformie Hadoop Mapreduce (RxHadoopMR) lub platformą Spark (RxSpark). W takim przypadku funkcja działa w sposób rozproszone między te węzły danych (zadanie) klastra, które są skojarzone z danych, do których odwołuje się. Aby uzyskać więcej informacji na temat opcji obliczeniowej kontekstu zobacz [COMPUTE context options obliczeniowego usługi ML w HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacjonalizowanie modelu

Po zakończeniu modelowania danych możesz zopernacjonalizować model aby tworzenia prognoz dotyczących nowych danych z platformy Azure lub lokalnie. Ten proces jest nazywany oceniania. Ocenianie może odbywać się w HDInsight, Azure Machine Learning lub w środowisku lokalnym.

### <a name="score-in-hdinsight"></a>Wynik w HDInsight

Aby wynik w HDInsight, Napisz funkcję R, który wywołuje model do przewidywania przyszłych zdarzeń dla nowego pliku danych, który został załadowany do swojego konta magazynu. Następnie zapisania prognozy na koncie magazynu. W węźle brzegowym klastra lub przy użyciu zaplanowanego zadania, można uruchomić tej procedury na żądanie.

### <a name="score-in-azure-machine-learning-aml"></a>Ocena w usłudze Azure Machine Learning (AML)

Aby wynik, za pomocą usługi Azure Machine Learning, należy użyć pakietu Azure Machine Learning R typu open source, znane jako [usługi Azure ml](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) Publikowanie modelu jako usługi sieci web platformy Azure. Dla wygody ten pakiet jest wstępnie zainstalowany w węźle brzegowym. Następnie użyj urządzenia w usłudze Azure Machine Learning, aby utworzyć interfejs użytkownika usługi sieci web, a następnie wywołaj usługę sieci web, zgodnie z potrzebami do oceniania.

Jeśli wybierzesz tę opcję, należy przekonwertować wszystkie obiekty modelu programu ScaleR na obiekty równoważne modelu open source do użycia z usługą sieci web. Użyj programu ScaleR wymuszenia funkcje, takie jak `as.randomForest()` dla modeli na podstawie zespołu, do konwersji.

### <a name="score-on-premises"></a>Wynik w środowisku lokalnym

Zdobycie lokalnego po utworzeniu modelu, można serializuje model w języku R, ją pobrać, deserializować go i użyć go do oceniania nowe dane. Można oceniać nowe dane przy użyciu podejścia opisanego wcześniej w [wynik w HDInsight](#score-in-hdinsight) lub za pomocą [usług sieci web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Obsługa klastra

### <a name="install-and-maintain-r-packages"></a>Instalowania i konserwowania pakiety języka R

Większość pakietów języka R, używane są wymagane w węźle brzegowym od większość czynności Twoje skrypty języka R Uruchom istnieje. Aby zainstalować dodatkowe pakiety R na węźle krawędzi, można użyć `install.packages()` metoda w języku R.

Jeśli po prostu używasz procedury z biblioteki programu ScaleR w klastrze, nie zazwyczaj trzeba zainstalować dodatkowe pakiety R na węzłach danych. Jednak może być konieczne dodatkowe pakiety, aby obsługiwały korzystanie z **rxExec** lub **RxDataStep** wykonanie w węzłach danych.

W takich przypadkach można zainstalować dodatkowe pakiety przy użyciu akcji skryptu po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [zarządzania usługi ML w klastrze HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Zmienianie ustawień pamięci Apache Hadoop MapReduce

Klaster można zmodyfikować w taki sposób, aby zmienić ilość pamięci, która jest dostępna dla usługi ML, gdy jest uruchomiona, zadanie MapReduce. Aby zmodyfikować klastra, należy użyć interfejsu użytkownika Ambari Apache, który jest dostępny za pośrednictwem blok witrynie Azure portal dla klastra. Aby uzyskać instrukcje dotyczące sposobu dostępu do interfejsu użytkownika Ambari dla klastra, zobacz [HDInsight Zarządzanie klastrami za pomocą interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md).

Istnieje również możliwość zmienić ilość pamięci, która jest dostępna dla usługi uczenia Maszynowego przy użyciu usługi Hadoop przełączników w wywołaniu **RxHadoopMR** w następujący sposób:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skalowanie klastra

Istniejący klaster usługi ML w HDInsight można skalować w górę lub w dół za pośrednictwem portalu. Skalując w górę, aby uzyskać dodatkową pojemność, które mogą wymagać większych zadań przetwarzania lub możesz skalować ponownie klaster w stanie bezczynności. Aby uzyskać instrukcje na temat skalowania klastra, zobacz [klastrów HDInsight zarządzanie](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Obsługa systemu

Konserwacja stosowanie poprawek systemu operacyjnego i inne aktualizacje odbywa się na podstawowych maszynach wirtualnych systemu Linux w klastrze usługi HDInsight poza godzinami szczytu. Zazwyczaj każdy poniedziałek i czwartek konserwacja odbywa się o 3:30 (na podstawie czasu lokalnego dla maszyny Wirtualnej). Aktualizacje są wykonywane w taki sposób, że nie ma wpływ na więcej niż jeden kwartał klastra w danym momencie.

Ponieważ węzły główne są nadmiarowe, a nie wszystkie węzły danych dotyczy problem, wszystkie zadania, które są uruchomione w tym czasie może spowolnić. Jednak powinna nadal działają one ukończone. Niestandardowe oprogramowanie lub dane lokalne, czy masz są zachowywane w tych zdarzeń konserwacji, chyba że wystąpi poważnej awarii, która wymaga odbudowania klastra.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opcje środowiska IDE dla usługi ML w HDInsight

Linux węzłem brzegowym klastra usługi HDInsight jest strefa docelowa dla analizy na podstawie języka R. Najnowsze wersje HDInsight zapewniają domyślnej instalacji programu RStudio Server w węźle brzegowym jako środowisko IDE oparte na przeglądarce. Korzystanie z programu RStudio Server jako środowisko IDE przeznaczone do tworzenia i wykonywanie skryptów języka R można znacznie zwiększyć wydajność, niż tylko przy użyciu konsoli R.

Ponadto można zainstalować komputerowego środowiska IDE i umożliwia dostęp do klastra przy użyciu zdalny kontekst obliczeniowy MapReduce lub platformą Spark. Opcje obejmują firmy Microsoft [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) RStudio i Walware użytkownika opartych na systemie Eclipse [StatET](http://www.walware.de/goto/statet).

Ponadto, możesz uzyskać dostęp konsoli R w węźle brzegowym, wpisując **R** w wierszu polecenia systemu Linux po nawiązaniu połączenia za pośrednictwem protokołu SSH lub programu PuTTY. Za pomocą interfejsu konsoli jest wygodne uruchomić edytora tekstów w celu tworzenia skryptów języka R w innym oknie i wycinać i wklejać części skryptu do konsoli R, zgodnie z potrzebami.

## <a name="pricing"></a>Cennik

Ceny, które są skojarzone z klastrem usługi HDInsight usługi ML mają strukturę podobną do cen dla innych typów klastra HDInsight. Są one oparte na temat rozmiarów maszyn wirtualnych z nazwy, danych i węzłów brzegowych, dodając wzroście rdzeń za godz. Aby uzyskać więcej informacji, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat korzystania z usług uczenia Maszynowego w klastrach HDInsight, zobacz następujące tematy:

* [Rozpoczynanie pracy z klastrem usługi ML w HDInsight](r-server-get-started.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Opcje magazynu dla klastra usługi ML na HDInsight](r-server-storage.md)

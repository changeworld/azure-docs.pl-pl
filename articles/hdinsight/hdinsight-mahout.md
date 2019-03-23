---
title: Generowanie rekomendacji za pomocą biblioteki Mahout HDInsight za pomocą programu PowerShell — platformy Azure
description: Dowiedz się, jak na potrzeby uczenia biblioteki maszynowego Apache Mahout Generowanie rekomendacji filmów za pomocą HDInsight (Hadoop) z skryptu programu PowerShell uruchomionego na komputerze klienckim.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: df8e15bbde5ad60d2e5cb90ba37892c135070f41
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359300"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-apache-hadoop-in-hdinsight-powershell"></a>Generowanie rekomendacji filmów za pomocą Apache Mahout Apache Hadoop w HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Dowiedz się, jak używać [Apache Mahout](https://mahout.apache.org) Biblioteka uczenia maszynowego przy użyciu usługi Azure HDInsight do Generowanie rekomendacji filmów. W przykładzie, w tym dokumencie użyto programu Azure PowerShell do uruchamiania zadań Mahout.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Klaster HDInsight opartych na systemie Linux. Aby uzyskać informacje o tworzeniu, zobacz [rozpoczęcie korzystania z opartą na systemie Linux platformą Hadoop w HDInsight][getstarted].

    > [!IMPORTANT]  
    > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Generowanie rekomendacji za pomocą programu Azure PowerShell

> [!WARNING]  
> Zadania w tej sekcji działa przy użyciu programu Azure PowerShell. Wiele klas dostarczonych za pomocą biblioteki Mahout obecnie nie współpracujesz z programem Azure PowerShell. Aby uzyskać listę klas, które nie będą działać przy użyciu programu Azure PowerShell, zobacz [Rozwiązywanie problemów](#troubleshooting) sekcji.
>
> Przykład przy użyciu protokołu SSH, aby nawiązać połączenie HDInsight i uruchamiania przykładów Mahout bezpośrednio w klastrze, zobacz [Generowanie rekomendacji filmów za pomocą Apache Mahout i HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

Funkcji, które są dostarczane przez Mahout on aparat rekomendacji. Ten aparat akceptuje dane w formacie `userID`, `itemId`, i `prefValue` (preferencji użytkowników dla elementu). Mahout używa danych, aby określić użytkowników z preferencjami podobnych elementów, które mogą być używane wydaje zalecenia.

Poniższy przykład to uproszczona omówienie sposobu działania procesu zalecenia:

* **wystąpienie wspólnej**: Jan, Alice i Bob wszystkie zbędne *gwiezdnych*, *ponownie ataki Empire*, i *Return Jedi*. Mahout Określa, że użytkownicy, którzy także, takich jak jeden z tych filmów, takich jak pozostałe dwa.

* **wystąpienie wspólnej**: Bob i Alicja również zbędne *zagrożenie fantom*, *ataku klony*, i *zemsty Sith*. Mahout Określa, że użytkownicy, którzy również zbędne poprzednie trzy filmy, takich jak te filmy.

* **Zalecenie podobieństwa**: Ponieważ Jan zbędne pierwszych trzech filmy, Mahout analizuje filmy tej osoby z preferencjami podobnych zbędne, ale Jan nie ma obserwowane (zbędne/klasyfikowane). W tym przypadku zaleca się Mahout *zagrożenie fantom*, *ataku klony*, i *zemsty Sith*.

### <a name="understanding-the-data"></a>Opis danych

[Badania GroupLens] [ movielens] dostarcza ocenę danych dotyczących filmów w formacie, który jest zgodny z biblioteki Mahout. Dane te są dostępne na domyślny magazyn dla klastra w `/HdiSamples/HdiSamples/MahoutMovieData`.

Istnieją dwa pliki `moviedb.txt` (informacje na temat filmów) i `user-ratings.txt`. `user-ratings.txt` Plik jest używany podczas analizy. `moviedb.txt` Plik jest używany do tekstu przyjazny dla użytkownika podczas wyświetlania wyników analizy.

Dane zawarte w ratings.txt użytkownika ma strukturę `userID`, `movieID`, `userRating`, i `timestamp`, która informuje, jak wysoko ocenianych filmu przez każdego użytkownika. Oto przykład danych:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Uruchamianie zadania

Użyj następującego skryptu programu Windows PowerShell do uruchamiania zadań, która używa aparatu rekomendacji Mahout danymi filmu:

> [!NOTE]  
> Ten plik wyświetli monit o podanie informacji, który służy do połączenia z klastrem HDInsight i uruchamianie zadań. Może upłynąć kilka minut na ukończenie, a następnie pobrać plik output.txt zadań.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]  
> Zadania biblioteki mahout nie usuwaj dane tymczasowe, który jest tworzony podczas przetwarzania zadania. `--tempDir` Parametr jest określony w ramach zadania przykład, aby odizolować pliki tymczasowe w określonym katalogu.

Zadania biblioteki Mahout nie zwraca dane wyjściowe do STDOUT. Zamiast tego, przechowuje go w katalogu określonym produktem wyjściowym jako **część r-00000**. Skrypt pobierze ten plik, aby **output.txt** w bieżącym katalogu na stacji roboczej.

Następujący tekst jest przykładem zawartości tego pliku:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Pierwsza kolumna jest `userID`. Wartości zawarte w "[" i "]" są `movieId`:`recommendationScore`.

Pobranie skryptu `moviedb.txt` i `user-ratings.txt` pliki, które są potrzebne do formatowania danych wyjściowych, aby były bardziej czytelne.

### <a name="view-the-output"></a>Wyświetlanie danych wyjściowych

Mimo że wygenerowane dane wyjściowe mogą być OK do użycia w aplikacji, nie jest przyjazna dla użytkownika. `moviedb.txt` z serwera może służyć do rozwiązywania `movieId` nazwę filmu. Poniższy skrypt programu PowerShell umożliwia wyświetlanie zaleceń o nazwach film:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Aby wyświetlić zalecenia w formacie przyjazny dla użytkownika, użyj następującego polecenia: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

Dane wyjściowe będą podobne do następującego tekstu:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cannot-overwrite-files"></a>Nie można zastąpić pliki

Mahout zadania nie usuwaj pliki tymczasowe, które zostały utworzone podczas przetwarzania. Ponadto zadania nie zastępuj istniejącego pliku wyjściowego.

Aby uniknąć błędów podczas uruchamiania zadania Mahout, Usuń pliki tymczasowe i wyjściowe między działa. Aby usunąć plików utworzonych przez wcześniejsze skrypty w tym dokumencie, użyj następującego skryptu programu PowerShell:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Klasy, które nie będą działać przy użyciu programu Azure PowerShell

Mahout zadania, które używają następujących klas, zwracać różne komunikaty o błędach w przypadku używania z programu Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Uruchamianie zadań, które używają tych klas, nawiąż połączenie z klastrem HDInsight przy użyciu protokołu SSH i uruchamiania zadań w wierszu polecenia. Przykład do uruchamiania zadań Mahout przy użyciu protokołu SSH, zobacz [Generowanie rekomendacji filmów za pomocą Apache Mahout i HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak używać Apache Mahout poznać inne sposoby pracy z danymi w HDInsight:

* [Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig z HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: https://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: https://grouplens.org/datasets/movielens/
[100k]: https://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: https://en.wikipedia.org/wiki/Machine_learning
[forest]: https://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

---
title: Generowanie zaleceń przy użyciu Mahout HDInsight z programu PowerShell - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki uczenia maszynowego Apache Mahout celu generowania zaleceń filmu z usługą HDInsight (Hadoop) ze skryptu środowiska PowerShell uruchomionego na komputerze klienckim.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 49a092ee23b79c483aa7bbd8b3d5150e909b6884
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Generowanie zaleceń filmu przy użyciu Apache Mahout Hadoop w usłudze HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Dowiedz się, jak używać [Apache Mahout](http://mahout.apache.org) maszyny biblioteki learning z usługą Azure HDInsight w celu generowania zaleceń filmu. W przykładzie w niniejszym dokumencie użyto programu Azure PowerShell do uruchamiania zadań Mahout.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi HDInsight opartej na systemie Linux. Informacje o tworzeniu jedną, zobacz [Rozpoczynanie pracy z opartą na systemie Linux platformą Hadoop w usłudze HDInsight][getstarted].

    > [!IMPORTANT]
    > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Generowanie zaleceń przy użyciu programu Azure PowerShell

> [!WARNING]
> Zadania w tej sekcji działa przy użyciu programu Azure PowerShell. Wiele klas wyposażone Mahout obecnie nie współpracujesz z programem Azure PowerShell. Lista klas, które nie są obsługiwane przy użyciu programu Azure PowerShell, zobacz [Rozwiązywanie problemów](#troubleshooting) sekcji.
>
> Na przykład przy użyciu protokołu SSH do nawiązania połączenia usługi HDInsight i uruchamiania przykładów Mahout bezpośrednio w klastrze, zobacz [Generowanie zaleceń filmu przy użyciu Mahout i HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

Jedną z funkcji, które są udostępniane przez Mahout jest aparatem zalecenia. Ten aparat akceptuje dane w formacie `userID`, `itemId`, i `prefValue` (preferencji Użytkownicy elementu). Mahout używa danych w celu określenia użytkowników z preferencje podobnych elementów, które mogą być używane zaleceń.

Poniższy przykład jest uproszczone przewodnika działania procesu zalecenia:

* **wystąpienie wspólnej**: Jan, Alicja i Robert wszystkie zbędne *słów*, *ponownie ataki Empire*, i *powrotu Jedi*. Mahout Określa, że użytkownicy, którzy także, takich jak jeden z tych filmów, takich jak pozostałe dwa.

* **wystąpienie wspólnej**: Robert i Alicja również zbędne *zagrożenie fantom*, *ataku klonów*, i *zemsty Sith*. Mahout Określa, że użytkownicy, którzy także zbędne poprzednie trzy filmów, takich jak te filmów.

* **Zalecenie podobieństwa**: ponieważ Jan zbędne pierwsze trzy filmów, Mahout analizuje filmy tej osoby z podobne preferencje zbędne, ale Jan nie ma obserwowane (zbędne/klasyfikowane). W takim przypadku zaleca Mahout *zagrożenie fantom*, *ataku klonów*, i *zemsty Sith*.

### <a name="understanding-the-data"></a>Opis danych

[Badania GroupLens] [ movielens] zapewnia filmów w formacie, który jest zgodny z Mahout klasyfikacji danych. Dane te są dostępne na domyślny magazyn dla klastra w `/HdiSamples/HdiSamples/MahoutMovieData`.

Istnieją dwa pliki `moviedb.txt` (informacje na temat filmów) i `user-ratings.txt`. `user-ratings.txt` Plik jest używany podczas analizy. `moviedb.txt` Pliku służy do zapewnienia tekst przyjazną dla użytkownika, wyświetlając wyniki analizy.

Dane zawarte w ratings.txt użytkownika ma struktury `userID`, `movieID`, `userRating`, i `timestamp`, które informuje, jak bardzo każdego użytkownika oceną filmu. Oto przykładowe dane:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Uruchom zadanie

Użyj następującego skryptu programu Windows PowerShell do uruchomienia zadania, które używa aparatu zalecenie Mahout z danymi film:

> [!NOTE]
> Ten plik monituje o informacje, które służą do łączenia się z klastrem usługi HDInsight i uruchamiania zadań. Może upłynąć kilka minut na zakończenie i Pobierz plik output.txt zadań.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Mahout zadania nie należy usuwać dane tymczasowe, który jest tworzony podczas przetwarzania zadania. `--tempDir` Parametr jest określony w zadaniu przykład aby wyodrębnić pliki tymczasowe w określonym katalogu.

Zadanie Mahout nie zwraca dane wyjściowe stdout. Zamiast tego, przechowuje go w katalogu określonym produktem wyjściowym jako **części r-00000**. Ten plik, aby pliki do pobrania skryptu **output.txt** w bieżącym katalogu na stacji roboczej.

Przykładem zawartości tego pliku jest następujący tekst:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Pierwsza kolumna `userID`. Wartości zawartych w "[" i "]" są `movieId`:`recommendationScore`.

Skrypt również pobiera `moviedb.txt` i `user-ratings.txt` pliki, które są potrzebne do formatowania danych wyjściowych, aby było bardziej czytelne.

### <a name="view-the-output"></a>Wyświetl dane wyjściowe

Chociaż wygenerowanych danych wyjściowych może być OK do użycia w aplikacji, nie jest przyjazną dla użytkownika. `moviedb.txt` z serwera może być używany do rozpoznania `movieId` na nazwę filmu. Umożliwia wyświetlanie zaleceń o nazwach filmu następujący skrypt programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Aby wyświetlić zalecenia w formacie przyjazną dla użytkownika, użyj następującego polecenia: 

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

Wykonaj zadania mahout nie oczyszczania plików tymczasowych, które zostały utworzone podczas przetwarzania. Ponadto zadania nie zastępuj istniejącego pliku wyjściowego.

Aby uniknąć błędów podczas uruchamiania zadania Mahout, Usuń pliki tymczasowe i dane wyjściowe między działa. Aby usunąć pliki tworzone przez skrypty wcześniej w tym dokumencie, użyj następującego skryptu programu PowerShell:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Klasy, które nie są obsługiwane przy użyciu programu Azure PowerShell

Zadania mahout, używane przez następujące klasy zwracać różne komunikaty o błędach w przypadku używania z programu Windows PowerShell:

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

Aby uruchomić zadania korzystające z tych klas, połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH, a następnie uruchom zadania z wiersza polecenia. Na przykład do uruchomienia zadań Mahout przy użyciu protokołu SSH, zobacz [Generowanie zaleceń filmu przy użyciu Mahout i HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu używania Mahout, odnajdywanie inne sposoby pracy z danymi w usłudze HDInsight:

* [Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

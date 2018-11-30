---
title: Analizowanie danych serwisu Twitter przy użyciu technologii Apache Hadoop w HDInsight — Azure
description: Dowiedz się, jak wykorzystać technologię Hive do analizowania danych z usługi Twitter na platformie Apache Hadoop w HDInsight w celu określenia częstotliwości użycia konkretnego wyrazu.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 85bd9e93fbd4c5ab5c0d2388d19334bc2cd3cb9e
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497525"
---
# <a name="analyze-twitter-data-using-apache-hive-in-hdinsight"></a>Analizowanie danych serwisu Twitter przy użyciu technologii Hive w HDInsight
Społecznościowych witryn sieci Web są jednym z głównych sił kierowania do przyjęcia danych big data. Publicznych interfejsów API dostarczonych przez witryn, takich jak Twitter, są przydatne źródło danych do analizowania i zrozumienia popularnych trendów.
W ramach tego samouczka, możesz uzyskać tweety za pomocą usługi Twitter z interfejsu API przesyłania strumieniowego, a następnie użyj [Apache Hive](https://hive.apache.org/) w usłudze Azure HDInsight w celu uzyskania listy użytkowników usługi Twitter, którzy wysłali najbardziej tweety, które zawierały określony wyraz.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra HDInsight z systemem Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Kroki dotyczące określonego klastra opartego na systemie Linux, zawiera temat [Twitter analizowanie danych przy użyciu technologii Hive w HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Stacja robocza** przy użyciu programu Azure PowerShell zainstalowany i skonfigurowany.

    Wykonywanie skryptów programu Windows PowerShell, należy uruchomić programu Azure PowerShell jako administrator i ustawione zasady wykonywania *RemoteSigned*. Zobacz [skrypty uruchamiania środowiska Windows PowerShell][powershell-script].

    Przed uruchomieniem skryptów programu Windows PowerShell, upewnij się, że masz połączenie z subskrypcją platformy Azure przy użyciu następującego polecenia cmdlet:

    ```powershell
    Connect-AzureRmAccount
    ```

    Jeśli masz wiele subskrypcji platformy Azure, użyj następującego polecenia cmdlet, aby ustawić bieżącą subskrypcję:

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > Obsługa programu Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i została usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie środowiska Azure PowerShell](/powershell/azureps-cmdlets-docs), aby zainstalować najnowszą wersję środowiska Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

* **Klaster usługi Azure HDInsight**. Aby uzyskać instrukcje dotyczące inicjowania obsługi klastra, zobacz [rozpoczęcie korzystania z HDInsight] [ hdinsight-get-started] lub [Provision HDInsight clusters][hdinsight-provision]. W dalszej części tego samouczka konieczne będzie nazwa klastra.

W poniższej tabeli wymieniono pliki używane w ramach tego samouczka:

| Pliki | Opis |
| --- | --- |
| /Tutorials/Twitter/Data/tweets.txt |Źródło danych pod kątem zadania Hive. |
| /Tutorials/Twitter/Output |Folder danych wyjściowych zadania Hive. Domyślna nazwa pliku danych wyjściowych zadania Hive jest **000000_0**. |
| Tutorials/Twitter/Twitter.hql |Plik skryptu HiveQL. |
| /Tutorials/Twitter/JobStatus |Stan zadania usługi Hadoop. |

## <a name="get-twitter-feed"></a>Pobierz kanale w serwisie Twitter
W tym samouczku użyjesz [przesyłania strumieniowego interfejsów API w usłudze Twitter][twitter-streaming-api]. Twitter określonych, użyjesz interfejsu API przesyłania strumieniowego to [stany/filtr][twitter-statuses-filter].

> [!NOTE]
> Plik zawierający 10 000 tweetów i plik skryptu programu Hive (co omówiono w następnej sekcji) zostały przekazane publicznego kontenera obiektów Blob. Jeśli chcesz użyć do przekazywania plików, można pominąć tę sekcję.

Tweety, dane są przechowywane w formacie JavaScript Object Notation (JSON), który zawiera złożone struktury zagnieżdżonej. Zamiast pisania wielu wierszy kodu za pomocą konwencjonalnych języka programowania, można przekształcić tej struktury zagnieżdżonej do tabeli programu Hive, dzięki czemu mogą być przeszukiwane przez język SQL (Structured Query) — takie jak języka o nazwie HiveQL.

Zapewnienie autoryzowanego dostępu do jego interfejsu API usługi Twitter korzysta z uwierzytelniania OAuth. OAuth to protokół uwierzytelniania, który umożliwia użytkownikom w celu zatwierdzenia aplikacji do działania w ich imieniu bez udostępniania swojego hasła. Więcej informacji znajduje się w temacie [oauth.net](http://oauth.net/) lub doskonałą [dla początkujących OAuth](http://hueniverse.com/oauth/) z Hueniverse.

Pierwszym krokiem do używają protokołu OAuth ma utworzyć nową aplikację w witrynie Twitter dla deweloperów.

**Aby utworzyć aplikację usługi Twitter**

1. Zaloguj się do witryny [https://apps.twitter.com/](https://apps.twitter.com/). Kliknij przycisk **Zarejestruj się teraz** łącze, jeśli nie masz konta w serwisie Twitter.
2. Kliknij przycisk **Utwórz nową aplikację**.
3. Wprowadź **nazwa**, **opis**, **witryny sieci Web**. Można uzupełnić adres URL **witryny sieci Web** pola. W poniższej tabeli przedstawiono niektóre przykładowe wartości do użycia:

   | Pole | Wartość |
   | --- | --- |
   |  Name (Nazwa) |MyHDInsightApp |
   |  Opis |MyHDInsightApp |
   |  Witryna internetowa |http://www.myhdinsightapp.com |
4. Sprawdź **tak, zgadzam**, a następnie kliknij przycisk **tworzenie aplikacji usługi Twitter**.
5. Kliknij przycisk **uprawnienia** kartę. Domyślne uprawnienia **tylko do odczytu**. Jest to wystarczające do celów tego samouczka.
6. Kliknij przycisk **klucze i tokeny dostępu** kartę.
7. Kliknij przycisk **Utwórz Mój token dostępu**.
8. Kliknij przycisk **OAuth testu** w prawym górnym rogu strony.
9. Zapisz **konsumenta**, **klucz tajny klienta**, **token dostępu**, i **klucz tajny tokenu dostępu**. Wartości będą potrzebne w dalszej części tego samouczka.

Wywołanie usługi sieci web, w tym samouczku Użyj programu Windows PowerShell. To popularne narzędzie można wykonywać wywołań usług sieci web [ *Curl*][curl]. Można pobrać z programu curl [tutaj][curl-download].

> [!NOTE]
> Korzystając z polecenia curl w Windows, użyj podwójnych cudzysłowów zamiast pojedynczych cudzysłowów dla wartości opcji.

**Aby uzyskać tweetów**

1. Otwórz program Windows PowerShell zintegrowane skryptów środowiska (ISE). (Na ekran początkowy systemu Windows 8, wpisz **PowerShell_ISE** a następnie kliknij przycisk **środowiska Windows PowerShell ISE**. Zobacz [uruchomić program Windows PowerShell w systemie Windows 8 i Windows](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell?view=powershell-6)
2. Skopiuj poniższy skrypt w okienku skryptu:

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Connect-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Ustaw pierwszy pięciu do ośmiu zmienne w skrypcie:

    Zmienna|Opis
    ---|---
    $clusterName|Jest to nazwa klastra HDInsight, w którym chcesz uruchomić aplikację.
    $oauth_consumer_key|To jest aplikacja usługi Twitter **konsumenta** zapisanej wcześniej podczas tworzenia aplikacji usługi Twitter.
    $oauth_consumer_secret|To jest aplikacja usługi Twitter **klucz tajny klienta** zapisanej wcześniej.
    $oauth_token|To jest aplikacja usługi Twitter **token dostępu** zapisanej wcześniej.
    $oauth_token_secret|To jest aplikacja usługi Twitter **klucz tajny tokenu dostępu** zapisanej wcześniej.
    $destBlobName|Jest to nazwa obiektu blob danych wyjściowych. Wartość domyślna to **tutorials/twitter/data/tweets.txt**. Jeśli zmienisz wartość domyślną, należy odpowiednio zaktualizować skryptów programu Windows PowerShell.
    $trackString|Usługa sieci web zwróci tweetów związanych z tych słów kluczowych. Wartość domyślna to **HDInsight systemu Azure, chmura,**. Jeśli zmienisz wartość domyślną, będzie odpowiednio zaktualizować skryptów programu Windows PowerShell.
    $lineMax|Wartość określa, ile tweetów będzie odczytywał element skryptu. Trwa około trzeciej minuty odczytać 100 tweetów. Można ustawić zbyt wiele, ale potrwa dłużej do pobrania.

1. Naciśnij klawisz **F5**, aby uruchomić skrypt. Jeśli napotkasz problemy, jako obejście tego problemu, zaznacz wszystkie wiersze, a następnie naciśnij klawisz **F8**.
2. Zostanie wyświetlona "Zakończono"! na koniec dane wyjściowe. Komunikaty o błędach będą wyświetlane w kolorze czerwonym.

Jako procedurę sprawdzania poprawności, można sprawdzić plik wyjściowy **/tutorials/twitter/data/tweets.txt**, w usłudze Azure Blob storage za pomocą Eksploratora usługi storage platformy Azure lub programu Azure PowerShell. Aby uzyskać przykładowy skrypt programu Windows PowerShell do wyświetlania listy plików, zobacz [Użyj usługi Blob storage za pomocą HDInsight][hdinsight-storage-powershell].

## <a name="create-hiveql-script"></a>Utwórz skrypt HiveQL
Przy użyciu programu Azure PowerShell, możesz uruchomić wiele [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) instrukcji jeden w czasie, lub pakietu w pliku skryptu za pomocą instrukcji HiveQL. W tym samouczku utworzysz skrypt HiveQL. W pliku skryptu muszą być przesłane do usługi Azure Blob storage. W następnej sekcji będzie uruchamiany plik skryptu przy użyciu programu Azure PowerShell.

> [!NOTE]
> Plik skryptu programu Hive i plik zawierający 10 000 tweetów zostały przekazane publicznego kontenera obiektów Blob. Jeśli chcesz użyć do przekazywania plików, można pominąć tę sekcję.

Skrypt HiveQL będzie wykonywać następujące czynności:

1. **Porzuć tabelę tweets_raw** w przypadku, gdy tabela już istnieje.
2. **Tworzenie tabeli Hive tweets_raw**. Tabela tymczasowa ze strukturą Hive przechowuje dane dla dalszych wyodrębniania, przekształcania i ładowania (ETL) przetwarzania. Aby uzyskać informacje na partycje, zobacz [samouczek Hive][apache-hive-tutorial].
3. **Ładowanie danych** z folderu źródłowego /tutorials/twitter/data. Zestaw danych dużych tweety w formacie JSON zagnieżdżone ma zostały przetworzone na tymczasowe struktura tabeli programu Hive.
4. **Porzuć tabelę tweetów** w przypadku, gdy tabela już istnieje.
5. **Utwórz tabelę tweetów**. Aby wykonać zapytanie względem zestawu danych tweetów, korzystając z programu Hive, musisz uruchomić inny proces ETL. Ten proces ETL definiuje bardziej szczegółowe schematu tabeli dla danych, które mają być przechowywane w tabeli "twitter_raw".
6. **Wstaw tabelę Zastąp**. To złożone skryptu Hive będzie uruchamiał zbiór zadań MapReduce przez klaster usługi Hadoop. W zależności od zestawu danych i rozmiar klastra może to potrwać około 10 minut.
7. **Wstaw zastąpić katalogu**. Uruchomienie zapytania, a następnie wyjściowy zestaw danych do pliku. To zapytanie zwróci listę użytkowników usługi Twitter, którzy wysłali większość tweetów, zawierające słowo "Azure".

**Aby utworzyć skrypt programu Hive i przekaż go na platformie Azure**

1. Otwórz program Windows PowerShell ISE.
2. Skopiuj poniższy skrypt w okienku skryptu:

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

    DROP TABLE tweets;
    CREATE TABLE tweets
    (
        id BIGINT,
        created_at STRING,
        created_at_date STRING,
        created_at_year STRING,
        created_at_month STRING,
        created_at_day STRING,
        created_at_time STRING,
        in_reply_to_user_id_str STRING,
        text STRING,
        contributors STRING,
        retweeted STRING,
        truncated STRING,
        coordinates STRING,
        source STRING,
        retweet_count INT,
        url STRING,
        hashtags array<STRING>,
        user_mentions array<STRING>,
        first_hashtag STRING,
        first_user_mention STRING,
        screen_name STRING,
        name STRING,
        followers_count INT,
        listed_count INT,
        friends_count INT,
        lang STRING,
        user_location STRING,
        time_zone STRING,
        profile_image_url STRING,
        json_response STRING
    );

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
            when "Jan" then "01"
            when "Feb" then "02"
            when "Mar" then "03"
            when "Apr" then "04"
            when "May" then "05"
            when "Jun" then "06"
            when "Jul" then "07"
            when "Aug" then "08"
            when "Sep" then "09"
            when "Oct" then "10"
            when "Nov" then "11"
            when "Dec" then "12" end,
        substr (get_json_object(json_response, '$.created_at'),9,2),
        substr (get_json_object(json_response, '$.created_at'),12,8),
        get_json_object(json_response, '$.in_reply_to_user_id_str'),
        get_json_object(json_response, '$.text'),
        get_json_object(json_response, '$.contributors'),
        get_json_object(json_response, '$.retweeted'),
        get_json_object(json_response, '$.truncated'),
        get_json_object(json_response, '$.coordinates'),
        get_json_object(json_response, '$.source'),
        cast (get_json_object(json_response, '$.retweet_count') as INT),
        get_json_object(json_response, '$.entities.display_url'),
        array(
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
        array(
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
        trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
        trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
        get_json_object(json_response, '$.user.screen_name'),
        get_json_object(json_response, '$.user.name'),
        cast (get_json_object(json_response, '$.user.followers_count') as INT),
        cast (get_json_object(json_response, '$.user.listed_count') as INT),
        cast (get_json_object(json_response, '$.user.friends_count') as INT),
        get_json_object(json_response, '$.user.lang'),
        get_json_object(json_response, '$.user.location'),
        get_json_object(json_response, '$.user.time_zone'),
        get_json_object(json_response, '$.user.profile_image_url'),
        json_response
    WHERE (length(json_response) > 500);

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Ustaw najpierw dwie zmienne w skrypcie:

   | Zmienna | Opis |
   | --- | --- |
   |  $clusterName |Wprowadź nazwę klastra HDInsight, w którym chcesz uruchomić aplikację. |
   |  $subscriptionID |Wprowadź identyfikator subskrypcji platformy Azure. |
   |  $sourceDataPath |Lokalizacja magazynu obiektów Blob platformy Azure, gdzie odczyta dane z zapytania programu Hive. Nie trzeba zmieniać tej zmiennej. |
   |  $outputPath |Lokalizacja magazynu obiektów Blob platformy Azure, gdzie zwróci wyniki zapytania programu Hive. Nie trzeba zmieniać tej zmiennej. |
   |  $hqlScriptFile |Lokalizacja i nazwa pliku w pliku skryptu HiveQL. Nie trzeba zmieniać tej zmiennej. |
4. Naciśnij klawisz **F5**, aby uruchomić skrypt. Jeśli napotkasz problemy, jako obejście tego problemu, zaznacz wszystkie wiersze, a następnie naciśnij klawisz **F8**.
5. Zostanie wyświetlona "Zakończono"! na koniec dane wyjściowe. Komunikaty o błędach będą wyświetlane w kolorze czerwonym.

Jako procedurę sprawdzania poprawności, można sprawdzić plik wyjściowy **/tutorials/twitter/twitter.hql**, w usłudze Azure Blob storage za pomocą Eksploratora usługi storage platformy Azure lub programu Azure PowerShell. Aby uzyskać przykładowy skrypt programu Windows PowerShell do wyświetlania listy plików, zobacz [Użyj usługi Blob storage za pomocą HDInsight][hdinsight-storage-powershell].

## <a name="process-twitter-data-by-using-hive"></a>Przetwarzanie danych z usługi Twitter przy użyciu technologii Hive
Zakończono wszystkie prace przygotowania. Teraz możesz wywoływać skryptu programu Hive i sprawdź wyniki.

### <a name="submit-a-hive-job"></a>Przesyłanie zadania Hive
Użyj następującego skryptu programu Windows PowerShell do uruchamiania skryptu programu Hive. Należy ustawić pierwszy zmienną.

> [!NOTE]
> Aby użyć tweety i [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skrypt przekazany w ostatnich dwóch sekcjach, ustaw $hqlScriptFile "/ tutorials/twitter/twitter.hql". Aby korzystać z tych, które zostały przekazane do publicznego obiektu blob dla Ciebie, ustaw $hqlScriptFile "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace �\s*$� -replace �.*\s�
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>Sprawdź wyniki
Użyj następującego skryptu programu Windows PowerShell, aby sprawdzić dane wyjściowe zadania Hive. Należy ustawić pierwsze dwie zmienne.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> W tabeli programu Hive używa \001 jako ogranicznik pola. Ogranicznik nie jest widoczny w danych wyjściowych.

Po wyniki analizy zostały umieszczone w usłudze Azure Blob storage, możesz wyeksportować dane do usługi Azure SQL database/programu SQL server, dane są eksportowane do programu Excel za pomocą dodatku Power Query lub połączyć aplikację z danych za pomocą sterownika ODBC usługi Hive. Aby uzyskać więcej informacji, zobacz [Użyj Apache Sqoop z HDInsight][hdinsight-use-sqoop], [analizowanie danych dotyczących opóźnień lotów przy użyciu HDInsight][hdinsight-analyze-flight-delay-data], [ Łączenie programu Excel z HDInsight za pomocą dodatku Power Query][hdinsight-power-query], i [łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft][hdinsight-hive-odbc].

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku Zaobserwowaliśmy się, jak przekształcić zestaw danych JSON bez struktury w strukturze tabeli Apache Hive zapytania, eksplorować i analizować dane z usługi Twitter za pomocą HDInsight na platformie Azure. Aby dowiedzieć się więcej, zobacz:

* [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu HDInsight][hdinsight-analyze-flight-delay-data]
* [Łączenie programu Excel z HDInsight za pomocą dodatku Power Query][hdinsight-power-query]
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft][hdinsight-hive-odbc]
* [Za pomocą HDInsight przy użyciu narzędzia Apache Sqoop][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://developer.twitter.com/en/docs/api-reference-index
[twitter-statuses-filter]: https://developer.twitter.com/en/docs/tweets/filter-realtime/api-reference/post-statuses-filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-power-query]:hadoop/apache-hadoop-connect-excel-power-query.md
[hdinsight-hive-odbc]:hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md

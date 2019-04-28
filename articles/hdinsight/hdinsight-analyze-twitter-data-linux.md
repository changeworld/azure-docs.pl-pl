---
title: Analizowanie danych serwisu Twitter przy użyciu Apache Hive — Azure HDInsight
description: Dowiedz się, jak używać Apache Hive i Apache Hadoop w HDInsight do Przekształcanie nieprzetworzonych danych serwisu TWitter w tabeli programu Hive można wyszukiwać.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: e10717b0fe14d2ed6ee720a2414e753bdd2474f1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095935"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analizowanie danych serwisu Twitter przy użyciu Apache Hive i Apache Hadoop w HDInsight

Dowiedz się, jak używać [Apache Hive](https://hive.apache.org/) do przetwarzania danych w usłudze Twitter. Wynik jest listę użytkowników usługi Twitter, którzy wysłali większość tweety, które zawierają określony wyraz.

> [!IMPORTANT]  
> Kroki opisane w niniejszym dokumencie zostały przetestowane w HDInsight 3.6.
>
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="get-the-data"></a>Pobieranie danych

Twitter pozwala pobrać danych dla każdego tweetu jako dokument JavaScript Object Notation (JSON) za pośrednictwem interfejsu API REST. [OAuth](https://oauth.net) jest wymagany do uwierzytelniania do interfejsu API.

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

1. Z poziomu przeglądarki sieci web, zaloguj się do [ https://apps.twitter.com/ ](https://apps.twitter.com/). Kliknij przycisk **Utwórz konto już teraz** łącze, jeśli nie masz konta w serwisie Twitter.

2. Kliknij przycisk **Utwórz nową aplikację**.

3. Wprowadź **nazwa**, **opis**, **witryny sieci Web**. Można uzupełnić adres URL **witryny sieci Web** pola. W poniższej tabeli przedstawiono niektóre przykładowe wartości do użycia:

   | Pole | Wartość |
   |:--- |:--- |
   | Name (Nazwa) |MyHDInsightApp |
   | Opis |MyHDInsightApp |
   | Witryna internetowa |https:\//www.myhdinsightapp.com |

4. Sprawdź **tak, zgadzam**, a następnie kliknij przycisk **tworzenie aplikacji usługi Twitter**.

5. Kliknij przycisk **uprawnienia** kartę. Domyślne uprawnienia **tylko do odczytu**.

6. Kliknij przycisk **klucze i tokeny dostępu** kartę.

7. Kliknij przycisk **Utwórz Mój token dostępu**.

8. Kliknij przycisk **OAuth testu** w prawym górnym rogu strony.

9. Zapisz **konsumenta**, **klucz tajny klienta**, **token dostępu**, i **klucz tajny tokenu dostępu**.

### <a name="download-tweets"></a>Pobierz tweety

Poniższy kod Python pobiera 10 000 tweety z serwisu Twitter, a następnie zapisz je w pliku o nazwie **tweets.txt**.

> [!NOTE]  
> Następujące kroki są wykonywane w klastrze HDInsight, ponieważ Python jest już zainstalowana.

1. Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Użyj następujących poleceń, aby zainstalować [Tweepy](https://www.tweepy.org/), [pasek postępu](https://pypi.python.org/pypi/progressbar/2.2)i inne wymagane pakiety:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Użyj następującego polecenia, aby utworzyć plik o nazwie **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Skorzystaj z poniższego tekstu jako zawartość **gettweets.py** pliku:

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]  
    > Przy użyciu informacji z aplikacji usługi twitter, należy zastąpić tekst symbolu zastępczego dla następujących elementów:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]  
    > Dostosowanie filtru tematów w ostatnim wierszu, aby śledzić popularne słowa kluczowe. Przy użyciu popularnych słów kluczowych w momencie uruchomienia skryptu umożliwia szybsze przechwytywania danych.

6. Użyj **Ctrl + X**, następnie **Y** można zapisać pliku.

7. Uruchom plik i Pobierz tweety, użyj następującego polecenia:

    ```bash
    python gettweets.py
    ```

    Wyświetlany jest wskaźnik postępu. Jak tweety są pobierane, liczniki do 100%.

   > [!NOTE]  
   > Jeśli trwa zbyt długo, aż pasek postępu awansować, należy zmienić filtru w celu śledzenia trendów tematów. W przypadku wielu tweety na temat w filtrze można szybko uzyskać 10000 tweety potrzebne.

### <a name="upload-the-data"></a>Przekazywanie danych

Aby przekazać dane do magazynu HDInsight, użyj następujących poleceń:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Te polecenia przechowywanie danych w lokalizacji dostępnej dla wszystkich węzłów w klastrze.

## <a name="run-the-hiveql-job"></a>Uruchom zadanie HiveQL

1. Użyj następującego polecenia, aby utworzyć plik zawierający [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) instrukcji:

   ```bash
   nano twitter.hql
   ```

    Skorzystaj z poniższego tekstu jako zawartość pliku:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
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
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
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
   ```

2. Naciśnij klawisz **Ctrl + X**, naciśnij klawisz **Y** można zapisać pliku.
3. Użyj następującego polecenia do uruchomienia HiveQL zawarte w pliku:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    To polecenie uruchamia **twitter.hql** pliku. Po wykonaniu zapytania zostanie wyświetlony `jdbc:hive2//localhost:10001/>` wiersza.

4. W wierszu polecenia z usługi beeline Użyj następującego zapytania, aby sprawdzić, czy dane zostały zaimportowane:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Ta kwerenda zwraca maksymalnie 10 tweety, które zawierają słowo **Azure** w treści wiadomości.

    > [!NOTE]  
    > Jeśli zmienisz filtru `gettweets.py` skryptu i Zastąp **Azure** przy użyciu jednego z filtrów jest używany.

## <a name="next-steps"></a>Kolejne kroki

Wiesz jak przekształcić zestaw danych JSON bez struktury w strukturze [Apache Hive](https://hive.apache.org/) tabeli. Aby dowiedzieć się więcej na temat programu Hive na HDInsight, zobacz następujące dokumenty:

* [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

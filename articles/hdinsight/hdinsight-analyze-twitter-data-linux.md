---
title: Analizowanie danych Twittera za pomocą usługi Apache Hive — Azure HDInsight
description: Dowiedz się, jak za pomocą Apache Hive i Apache Hadoop w programie HDInsight przekształcić surowe dane TWitter w tabelę hive z wyszukuj.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435611"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analizuj dane z Twittera za pomocą Apache Hive i Apache Hadoop na HDInsight

Dowiedz się, jak przetwarzać dane z Twittera za pomocą [usługi Apache Hive.](https://hive.apache.org/) Rezultatem jest lista użytkowników Twittera, którzy wysłali najwięcej tweetów, które zawierają określone słowo.

> [!IMPORTANT]  
> Kroki w tym dokumencie zostały przetestowane na HDInsight 3.6.

## <a name="get-the-data"></a>Pobieranie danych

Twitter umożliwia pobieranie danych dla każdego tweeta jako dokumentu notacji obiektu JavaScript (JSON) za pośrednictwem interfejsu API REST. [OAuth](https://oauth.net) jest wymagane do uwierzytelniania w interfejsie API.

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

1. W przeglądarce internetowej zaloguj [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/)się do . Wybierz link **Zarejestruj się teraz,** jeśli nie masz konta na Twitterze.

2. Wybierz **pozycję Utwórz nową aplikację**.

3. Wprowadź **nazwę**, **opis**, **witrynę sieci Web**. Możesz uzupełnić adres URL pola **Witryna.** W poniższej tabeli przedstawiono niektóre przykładowe wartości do użycia:

   | Pole | Wartość |
   |--- |--- |
   | Nazwa |MyHDInsightApp (Polski) |
   | Opis |MyHDInsightApp (Polski) |
   | witryna sieci web |`https://www.myhdinsightapp.com` |

4. Wybierz **tak, zgadzam się**, a następnie wybierz pozycję **Utwórz aplikację Twitter**.

5. Wybierz kartę **Uprawnienia.** Domyślne uprawnienie to **Tylko do odczytu**.

6. Wybierz kartę **Klucze i Tokeny dostępu.**

7. Wybierz **pozycję Utwórz token dostępu**.

8. Wybierz **opcję Testuj OAuth** w prawym górnym rogu strony.

9. Zapisz **klucz konsumenta,** **klucz konsumenta, klucz tajny** **konsumenta, token dostępu**i klucz tajny **tokenu dostępu**.

### <a name="download-tweets"></a>Pobierz tweety

Poniższy kod Pythona pobiera 10 000 tweetów z Twittera i zapisuje je w pliku o nazwie **tweets.txt**.

> [!NOTE]  
> Następujące kroki są wykonywane w klastrze HDInsight, ponieważ Python jest już zainstalowany.

1. Użyj [polecenia ssh,](./hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj następujących poleceń, aby zainstalować [Tweepy,](https://www.tweepy.org/) [Pasek postępu](https://pypi.python.org/pypi/progressbar/2.2)i inne wymagane pakiety:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **gettweets.py:**

   ```bash
   nano gettweets.py
   ```

1. Edytuj poniższy kod, `Your consumer key` `Your access token`zastępując `Your consumer secret` `Your access token secret` , , i z odpowiednimi informacjami z aplikacji twitter. Następnie wklej edytowany kod jako zawartość pliku **gettweets.py.**

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
   max_tweets=100

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

    > [!TIP]  
    > Dostosuj filtr tematy w ostatnim wierszu, aby śledzić popularne słowa kluczowe. Używanie słów kluczowych popularnych w momencie uruchamiania skryptu pozwala na szybsze przechwytywanie danych.

1. Użyj **klawiszy Ctrl + X**, a następnie **Y,** aby zapisać plik.

1. Użyj następującego polecenia, aby uruchomić plik i pobrać tweety:

    ```bash
    python gettweets.py
    ```

    Pojawi się wskaźnik postępu. Liczy się do 100% jak tweety są pobierane.

   > [!NOTE]  
   > Jeśli przejście paska postępu zajmuje dużo czasu, należy zmienić filtr, aby śledzić popularne tematy. Gdy istnieje wiele tweets na ten temat w filtrze, można szybko uzyskać 100 tweets potrzebne.

### <a name="upload-the-data"></a>Przesyłanie danych

Aby przekazać dane do magazynu HDInsight, użyj następujących poleceń:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Te polecenia przechowują dane w lokalizacji, do którą mają dostęp wszystkie węzły w klastrze.

## <a name="run-the-hiveql-job"></a>Uruchamianie zadania HiveQL

1. Aby utworzyć plik zawierający [instrukcje HiveQL,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) użyj następującego polecenia:

   ```bash
   nano twitter.hql
   ```

    Użyj następującego tekstu jako zawartości pliku:

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

1. Naciśnij **klawisze Ctrl + X**, a następnie naciśnij klawisz **Y,** aby zapisać plik.

1. Użyj następującego polecenia, aby uruchomić hiveQL zawarte w pliku:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    To polecenie uruchamia plik **twitter.hql.** Po zakończeniu kwerendy zostanie `jdbc:hive2//localhost:10001/>` wyświetlony monit.

1. Z monitu pszczelego użyj następującej kwerendy, aby sprawdzić, czy dane zostały zaimportowane:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Ta kwerenda zwraca maksymalnie 10 tweetów, które zawierają słowo **Azure** w tekście wiadomości.

    > [!NOTE]  
    > Jeśli zmieniłeś filtr `gettweets.py` w skrypcie, zastąp **platformę Azure** jednym z używanych filtrów.

## <a name="next-steps"></a>Następne kroki

Przedstawiono sposób przekształcania nieustrukturyzowanego zestawu danych JSON w uporządkowaną tabelę [gałęzi Apache.](https://hive.apache.org/) Aby dowiedzieć się więcej o gałęzi w programie HDInsight, zobacz następujące dokumenty:

* [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analizowanie danych opóźnienia lotu przy użyciu funkcji HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)

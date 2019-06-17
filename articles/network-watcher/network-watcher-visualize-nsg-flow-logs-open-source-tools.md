---
title: Zarządzanie i analizować dzienniki przepływu sieciowych grup zabezpieczeń przy użyciu usługi Network Watcher i Elastic Stack | Dokumentacja firmy Microsoft
description: Zarządzanie i analizować dzienniki sieciowych grup zabezpieczeń przepływu na platformie Azure przy użyciu usługi Network Watcher i Elastic Stack.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 7361eff0f76271564fd5a0e9b8a18221ec4138e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860120"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Wizualizowanie dzienników przepływów Azure Network Watcher NSG przy użyciu narzędzi typu open source

Dzienniki przepływu sieciowej grupy zabezpieczeń zawierają informacje, który może służyć zrozumieć przychodzący i wychodzący ruch IP na grupy zabezpieczeń sieci. Te dzienniki przepływu Pokaż przepływy wychodzące i przychodzące na podstawie poszczególnych reguł, przepływ ma zastosowanie do karty Sieciowej, 5 elementowe spójne kolekcje informacji o przepływie (źródłowy i docelowy adres IP, Port źródłowy i docelowy, Protocol), oraz czy ruch był dozwolony.

Te dzienniki przepływu może być trudne do ręcznego analizowania i uzyskiwania wglądu w. Istnieje jednak kilka narzędzi typu open source, które mogą pomóc wizualizację tych danych. W tym artykule zapewni rozwiązania w celu wizualizacji tych dzienników przy użyciu programu Elastic Stack, które umożliwią szybkie indeksu wizualizowanie i przepływ dzienniki na pulpicie nawigacyjnym rozwiązania Kibana.

> [!Warning]  
> Poniższe kroki pracy z dzienników przepływu w wersji 1. Aby uzyskać więcej informacji, zobacz [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md). Poniższe instrukcje nie będzie działać w wersji 2 pliki dziennika, bez żadnych modyfikacji.

## <a name="scenario"></a>Scenariusz

W tym artykule skonfigurujemy to rozwiązanie, które umożliwia wizualizowanie dzienników przepływu sieciowej grupy zabezpieczeń za pomocą programu Elastic Stack.  Dodatek Logstash w danych wejściowych osiągnie dzienników przepływów bezpośrednio z magazynu obiektów blob, skonfigurowane do przechowywania dzienników przepływów. Następnie przy użyciu programu Elastic Stack, dzienników przepływu będzie być indeksowane i użyty do utworzenia pulpitu nawigacyjnego Kibana, aby wizualizować informacje.

![scenariusz][scenario]

## <a name="steps"></a>Kroki

### <a name="enable-network-security-group-flow-logging"></a>Rejestrowanie przepływu Włącz sieciowej grupy zabezpieczeń
W tym scenariuszu konieczne jest posiadanie sieci zabezpieczeń grupy przepływu Rejestrowanie włączone na co najmniej jedną grupę zabezpieczeń sieci w ramach Twojego konta. Aby uzyskać instrukcje na temat włączania dzienników przepływu zabezpieczeń sieci, zapoznaj się z następującym artykułem [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Konfigurowanie programu Elastic Stack
Łącząc dzienniki przepływu sieciowych grup zabezpieczeń przy użyciu programu Elastic Stack, możemy utworzyć pulpitu nawigacyjnego Kibana co pozwala na wyszukiwanie, wykres, analizowanie i pobieraj szczegółowe informacje z naszych dzienników.

#### <a name="install-elasticsearch"></a>Instalowanie usługi Elasticsearch

1. Elastic Stack w wersji 5.0 lub nowszym wymaga Java 8. Uruchom polecenie `java -version` Aby sprawdzić swoją wersję. Jeśli nie masz zainstalowane w języku java, zajrzyj do dokumentacji na [JDK Azure suppored](https://aka.ms/azure-jdks).
2. Pobierz poprawny pakiet binarne w systemie:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Inne metody instalacji znajduje się w temacie [instalacji usługi Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Sprawdź, czy usługa Elasticsearch działa przy użyciu polecenia:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Powinny pojawić się odpowiedź podobna do poniższego:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Dalsze instrukcje dotyczące instalowania elastycznej wyszukiwania, można znaleźć [instrukcje dotyczące instalacji](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalowanie programu Logstash

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Następnie należy skonfigurować Logstash w celu uzyskania dostępu i analizowanie dzienników przepływów. Tworzenie pliku logstash.conf przy użyciu:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Dodaj następującą zawartość do pliku:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Aby uzyskać dalsze instrukcje na temat instalowania programu Logstash, zapoznaj się [oficjalną dokumentacją](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalowanie wtyczki wejściowych Logstash dla usługi Azure blob storage

Ten dodatek Logstash pozwoli uzyskać bezpośredni dostęp do dzienników przepływu ze swojego konta magazynu wyznaczonego. Aby zainstalować ten dodatek plug-in z katalogu instalacyjnego programu Logstash domyślna (w tym /usr/share/logstash/bin wielkości liter) uruchom polecenie:

```bash
logstash-plugin install logstash-input-azureblob
```

Aby uruchomić program Logstash, uruchom polecenie:

```bash
sudo /etc/init.d/logstash start
```

Aby uzyskać więcej informacji dotyczących tej wtyczki, zobacz [dokumentacji](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Zainstaluj program Kibana

1. Uruchom następujące polecenia, aby zainstalować program Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Aby uruchomić program Kibana, użyj polecenia:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Aby wyświetlić Kibana interfejsu sieci web, przejdź do `http://localhost:5601`
4. W tym scenariuszu wzorzec indeksu używane dla dzienników przepływu jest "ruch dzienniki sieciowych grup zabezpieczeń usługi flow". Mogą ulec zmianie ze wzorcem indeksu w sekcji "Wyjście" w pliku logstash.conf.
5. Jeśli użytkownik chce zdalnie wyświetlić pulpitu nawigacyjnego Kibana, Utwórz regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego umożliwia dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Kibana

Przykładowy pulpit nawigacyjny, aby wyświetlić trendy i szczegóły alertów jest wyświetlany na poniższej ilustracji:

![rysunek 1][1]

Pobierz [pliku pulpitu nawigacyjnego](https://aka.ms/networkwatchernsgflowlogdashboard), [plik wizualizacji](https://aka.ms/networkwatchernsgflowlogvisualizations)i [zapisany plik wyszukiwania](https://aka.ms/networkwatchernsgflowlogsearch).

W obszarze **zarządzania** kartę programu Kibana, przejdź do **zapisywane obiekty** i zaimportować wszystkie trzy pliki. Następnie z **pulpit nawigacyjny** karcie, można otworzyć i załadować przykładowy pulpit nawigacyjny.

Można również utworzyć własne wizualizacje i pulpity nawigacyjne, dostosowanych do własnych istotne metryki. Przeczytaj więcej na temat tworzenia wizualizacji Kibana z firmy Kibana [oficjalną dokumentacją](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń

Przykładowy pulpit nawigacyjny zawiera kilka wizualizacji dzienników przepływu:

1. Przepływy według kierunku/decyzji w czasie — wykresy serii czasu pokazujące liczbę przepływów w przedziale czasu. Jednostka czasu i zakres oba te wizualizacje można edytować. Przepływy według decyzji przedstawiono część blokują lub zezwalają na decyzje, podczas gdy przepływy według kierunku część ruchu przychodzącego i wychodzącego. Z tymi elementami wizualnymi można zbadać trendy ruchu, wraz z upływem czasu i wyszukiwać wszelkie skoki lub nietypowe wzorce.

   ![Rysunek 2][2]

2. Przepływy według portów docelowego/źródło — wykresy kołowe przedstawiający podział przepływy do odpowiednich portów. Z tego widoku możesz zobaczyć swoje najczęściej używane porty. Po kliknięciu na określonym porcie w obrębie wykresu kołowego pozostałej części pulpitu nawigacyjnego zostanie odfiltrowania przepływów tego portu.

   ![figure3][3]

3. Liczba przepływów i Najwcześniejsza godzina dziennika — metryki pokazujące, liczba przepływów rejestrowane i daty najwcześniejszej dziennika przechwycone.

   ![figure4][4]

4. Przepływy według sieciowych grup zabezpieczeń i regułę — wykres słupkowy przedstawiający, dystrybucja przepływów w ramach poszczególnych sieciowych grupach zabezpieczeń, a także dystrybucji reguły w poszczególnych sieciowych grupach zabezpieczeń. W tym miejscu zobaczysz, które sieciowej grupy zabezpieczeń i reguł generowane najczęściej ruchu.

   ![figure5][5]

5. Top 10 źródłowe/docelowe adresy IP — wykresów słupkowych przedstawiający 10 najważniejszych źródłowe i docelowe adresy IP. Można dostosować te wykresy, aby wyświetlić więcej lub mniej najważniejsze adresy IP. W tym miejscu widać, najczęściej występujące adresy IP, a także decyzji ruch (Zezwalaj lub Odmów) zostaną wprowadzone do poszczególnych adresów IP.

   ![figure6][6]

6. Przepływ krotek — w tej tabeli dowiesz się z informacji zawartych w każda krotka przepływu, a także jego odpowiedniego klasyfikacje i reguły.

   ![figure7][7]

W górnej części pulpitu nawigacyjnego, korzystając z paska zapytania, można odfiltrować pulpitu nawigacyjnego na podstawie dowolnej parametru przepływów, na przykład identyfikator subskrypcji, grupy zasobów, reguły lub inne zmienne zainteresowania. Więcej informacji na temat zapytania i filtry w Kibana, zapoznaj się [oficjalnych dokumentów](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Podsumowanie

Łącząc dzienniki przepływu sieciowych grup zabezpieczeń z elastycznego stosu, mają uzyskujemy wydajne i można go dostosowywać sposób wizualizować naszych ruchu sieciowego. Te pulpity nawigacyjne pozwalają szybko uzyskać i udostępniania szczegółowych informacji dotyczących ruchu sieciowego, jak również filtru w dół i zbadać na wszelkich potencjalnych nieprawidłowości. Przy użyciu rozwiązania Kibana, można dostosować te pulpity nawigacyjne i tworzyć wizualizacje określonych potrzeb żadnych zabezpieczeń, inspekcji i zgodności.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń z usługą Power BI, odwiedzając [wizualizacji przepływów sieciowych grup zabezpieczeń dzienników za pomocą usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png

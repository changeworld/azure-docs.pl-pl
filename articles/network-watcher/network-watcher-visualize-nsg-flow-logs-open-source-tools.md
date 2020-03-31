---
title: Wizualizuj dzienniki przepływu NSG - elastyczny stos
titleSuffix: Azure Network Watcher
description: Zarządzanie dziennikami przepływu grupy zabezpieczeń sieciowych i analizowanie ich na platformie Azure przy użyciu funkcji Kontrola sieci i stos elastyczny.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: e567994038fb4f71ef86dc577760ecf4699a0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840642"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Wizualizowanie dzienników przepływu sieciowych grup zabezpieczeń usługi Azure Network Watcher przy użyciu narzędzi open source

Dzienniki przepływu sieciowej grupy zabezpieczeń zawierają informacje, które mogą być używane, rozumieją ruch IP przychodzących i wychodzących w sieciowych grupach zabezpieczeń. Te dzienniki przepływu pokazują przepływy wychodzące i przychodzące na podstawie reguły, karta sieciowa, do którą ma zastosowanie przepływ, 5 informacji o krocie przepływu (adres IP źródła/miejsca docelowego, port źródłowy/docelowy, protokół) oraz jeśli ruch był dozwolony lub odrzucany.

Te dzienniki przepływu może być trudne do ręcznej analizy i uzyskać szczegółowe informacje z. Istnieje jednak kilka narzędzi open source, które mogą pomóc w wizualizacji tych danych. Ten artykuł zapewni rozwiązanie do wizualizacji tych dzienników przy użyciu stosu elastycznego, który pozwoli ci szybko indeksować i wizualizować dzienniki przepływu na pulpicie nawigacyjnym Kibana.

> [!Warning]  
> Poniższe kroki działają z dziennikami przepływu w wersji 1. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieci .](network-watcher-nsg-flow-logging-overview.md) Poniższe instrukcje nie będą działać z wersją 2 plików dziennika, bez modyfikacji.

## <a name="scenario"></a>Scenariusz

W tym artykule firma Microsoft skonfigurujemy rozwiązanie, które pozwoli ci wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń przy użyciu stosu elastycznego.  Wtyczka wejściowa Logstash uzyska dzienniki przepływu bezpośrednio z obiektu blob magazynu skonfigurowanego do przechowywania dzienników przepływu. Następnie za pomocą stosu elastycznego dzienniki przepływu zostaną zindeksowane i wykorzystane do utworzenia pulpitu nawigacyjnego Kibana do wizualizacji informacji.

![scenariusz][scenario]

## <a name="steps"></a>Kroki

### <a name="enable-network-security-group-flow-logging"></a>Włączanie rejestrowania przepływu sieciowej grupy zabezpieczeń
W tym scenariuszu musi być włączone rejestrowanie przepływu sieciowej grupy zabezpieczeń na co najmniej jednej sieciowej grupie zabezpieczeń na koncie. Instrukcje dotyczące włączania dzienników przepływu zabezpieczeń sieciowych można znaleźć w artykule [Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieciowych](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Konfigurowanie stosu elastycznego
Łącząc dzienniki przepływu sieciowej sieciowej z elastycznym stosem, możemy utworzyć pulpit nawigacyjny Kibana, który pozwala nam wyszukiwać, wykresować, analizować i czerpać wnioski z naszych dzienników.

#### <a name="install-elasticsearch"></a>Zainstaluj Elasticsearch

1. Stos elastyczny z wersji 5.0 i nowszych wymaga java 8. Uruchom polecenie, `java -version` aby sprawdzić wersję. Jeśli nie masz zainstalowanej oprogramowania Java, zapoznaj się z dokumentacją dotyczącą [zestawów JDK z suppored platformy Azure.](https://aka.ms/azure-jdks)
2. Pobierz odpowiedni pakiet binarny dla swojego systemu:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Inne metody instalacji można znaleźć na [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Sprawdź, czy Elasticsearch działa za pomocą polecenia:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Powinna zostać wyświetlna odpowiedź podobna do tej:

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

Dalsze instrukcje dotyczące instalacji wyszukiwania elastycznego można znaleźć w [instrukcji instalacji](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalowanie programu Logstash

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Następnie musimy skonfigurować Logstash, aby uzyskać dostęp i przeanalizować dzienniki przepływu. Utwórz plik logstash.conf przy użyciu:

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

Dalsze instrukcje dotyczące instalacji logstash można znaleźć w [oficjalnej dokumentacji.](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalowanie wtyczki wejściowej Logstash dla magazynu obiektów blob platformy Azure

Ta wtyczka Logstash pozwoli Ci bezpośrednio uzyskać dostęp do dzienników przepływu z ich wyznaczonego konta pamięci masowej. Aby zainstalować tę wtyczkę, z domyślnego katalogu instalacyjnego Logstash (w tym przypadku /usr/share/logstash/bin) uruchom polecenie:

```bash
logstash-plugin install logstash-input-azureblob
```

Aby uruchomić logstash uruchom polecenie:

```bash
sudo /etc/init.d/logstash start
```

Aby uzyskać więcej informacji na temat tej wtyczki, zapoznaj się z [dokumentacją](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalowanie Kibana

1. Uruchom następujące polecenia, aby zainstalować Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Aby uruchomić Kibana użyj poleceń:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Aby wyświetlić interfejs internetowy Kibana, przejdź do`http://localhost:5601`
4. W tym scenariuszu wzorzec indeksu używany dla dzienników przepływu jest "nsg-flow-logs". Możesz zmienić wzorzec indeksu w sekcji "dane wyjściowe" pliku logstash.conf.
5. Jeśli chcesz zdalnie wyświetlić pulpit nawigacyjny Kibana, utwórz przychodzącą regułę nsg umożliwiającą dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Kibana

Przykładowy pulpit nawigacyjny do wyświetlania trendów i szczegółów w alertach jest wyświetlany na poniższym obrazie:

![rysunek 1][1]

Pobierz [plik pulpitu nawigacyjnego,](https://aka.ms/networkwatchernsgflowlogdashboard) [plik wizualizacji](https://aka.ms/networkwatchernsgflowlogvisualizations)i [zapisany plik wyszukiwania](https://aka.ms/networkwatchernsgflowlogsearch).

Na karcie **Zarządzanie** w kibanie przejdź do pozycji Zapisane obiekty i zaimportuj wszystkie trzy pliki. **Saved Objects** Następnie na karcie **Pulpit nawigacyjny** można otworzyć i załadować przykładowy pulpit nawigacyjny.

Możesz również tworzyć własne wizualizacje i pulpity nawigacyjne dostosowane do danych, które cię interesują. Przeczytaj więcej o tworzeniu wizualizacji Kibana z [oficjalnej dokumentacji](https://www.elastic.co/guide/en/kibana/current/visualize.html)Kibana .

### <a name="visualize-nsg-flow-logs"></a>Wizualizuj dzienniki przepływu nsg

Przykładowy pulpit nawigacyjny zawiera kilka wizualizacji dzienników przepływu:

1. Przepływy według decyzji/kierunku w czasie — wykresy szeregów czasowych pokazujące liczbę przepływów w okresie. Można edytować jednostkę czasu i zakresu obu tych wizualizacji. Przepływy według decyzji pokazuje proporcję zezwalania lub odrzucać decyzje podjęte, podczas gdy przepływy według kierunku pokazuje proporcję ruchu przychodzącego i wychodzącego. Dzięki tym wizualizacjom możesz sprawdzić trendy ruchu w czasie i wyszukać wszelkie kolce lub nietypowe wzorce.

   ![rysunek2][2]

2. Przepływy według portu docelowego/źródłowego — wykresy kołowe przedstawiające podział przepływów do odpowiednich portów. W tym widoku można zobaczyć najczęściej używane porty. Jeśli klikniesz określony port w obrębie wykresu kołowego, reszta pulpitu nawigacyjnego zostanie odfiltrowane do przepływów tego portu.

   ![rysunek3][3]

3. Liczba przepływów i najwcześniejszy czas dziennika — metryki pokazujące liczbę zarejestrowanych przepływów i datę przechwycenia najwcześniejszego dziennika.

   ![rysunek4][4]

4. Przepływy przez nsg i reguły - wykres słupkowy pokazujący rozkład przepływów w obrębie każdej sieciowej grupy zabezpieczeń, a także rozkład reguł w ramach każdej sieciowej grupy zabezpieczeń. W tym miejscu można zobaczyć, które nsg i reguły generowane najwięcej ruchu.

   ![rysunek5][5]

5. 10 najlepszych adresów IP źródłowych/docelowych — wykresy słupkowe przedstawiające 10 najlepszych adresów IP źródłowych i docelowych. Możesz dostosować te wykresy, aby wyświetlać mniej lub bardziej najpopularniejsze ustawienia IP. W tym miejscu można zobaczyć najczęściej występujących adresów IP, jak również decyzji ruchu (zezwalaj lub zaprzeczaj) podejmowane w stosunku do każdego adresu IP.

   ![rysunek6][6]

6. Krotek przepływu — w tej tabeli przedstawiono informacje zawarte w każdej krotki przepływu, a także odpowiadające jej NGS i reguły.

   ![rysunek7][7]

Korzystając z paska zapytań w górnej części pulpitu nawigacyjnego, można filtrować pulpit nawigacyjny na podstawie dowolnego parametru przepływów, takiego jak identyfikator subskrypcji, grupy zasobów, reguła lub inna zmienna zainteresowania. Więcej informacji na temat zapytań i filtrów Kibana można znaleźć w [oficjalnej dokumentacji](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Podsumowanie

Łącząc dzienniki przepływu sieciowej grupy zabezpieczeń z elastycznym stosem, wymyśliliśmy zaawansowany i konfigurowalny sposób wizualizacji naszego ruchu sieciowego. Te pulpity nawigacyjne umożliwiają szybkie uzyskiwanie i udostępnianie szczegółowych informacji na temat ruchu sieciowego, a także filtrowanie i badanie wszelkich potencjalnych anomalii. Korzystając z kibana, możesz dostosować te pulpity nawigacyjne i tworzyć specjalne wizualizacje, aby spełnić wszelkie potrzeby w zakresie bezpieczeństwa, inspekcji i zgodności.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dzienniki przepływu sieciowych sieci płciowych za pomocą usługi Power BI, odwiedzając [wizualizuj dzienniki przepływów sieciowych sieciowych za pomocą usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png

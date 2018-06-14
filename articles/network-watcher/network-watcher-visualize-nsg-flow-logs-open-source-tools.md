---
title: Wizualizuj dzienników przepływu NSG obserwatora sieci Azure przy użyciu narzędzi typu open source | Dokumentacja firmy Microsoft
description: Ta strona informacje dotyczące używania narzędzi typu open source do wizualizacji dzienniki przepływu NSG.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f7d51352aa8411e36f4224804c90c2554d4ef9e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394174"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Wizualizuj dzienników przepływu NSG obserwatora sieci Azure przy użyciu narzędzi typu open source

Dzienniki przepływu sieciowej grupy zabezpieczeń zapewniają informacje, które mogą być używane zrozumieć przychodzące i wychodzące ruchu IP na grupy zabezpieczeń sieci. Te dzienniki przepływu Pokaż przepływów wychodzącego i przychodzącego na podstawie reguły na, przepływ ma zastosowanie do karty Sieciowej, 5 informacji spójnej kolekcji o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego protokołu), oraz czy ruch został dozwolony lub niedozwolony.

Te dzienniki przepływu może być trudne ręcznie przeanalizować i uzyskaj informacje z. Istnieje jednak kilka narzędzi typu open source, które ułatwiają wizualizowanie tych danych. W tym artykule umożliwi rozwiązanie w celu wizualizacji tych dzienników przy użyciu elastycznej stosu, co umożliwi szybkie indeksu i wizualizacji z przepływu dzienniki na pulpicie nawigacyjnym Kibana.

## <a name="scenario"></a>Scenariusz

W tym artykule skonfigurujemy rozwiązania, które umożliwia wizualizowanie sieciowej grupy zabezpieczeń przepływu dzienników przy użyciu elastycznej stosu.  Dodatek wejściowych Logstash uzyska dzienniki przepływu bezpośrednio z obiektu blob magazynu skonfigurowane do przechowywania dzienników przepływu. Następnie przy użyciu elastycznej stosu, przepływ dzienniki będą indeksowane i pozwala utworzyć pulpit nawigacyjny Kibana do wizualizacji danych.

![scenariusz][scenario]

## <a name="steps"></a>Kroki

### <a name="enable-network-security-group-flow-logging"></a>Rejestrowanie przepływu włączyć grupy zabezpieczeń sieci
W tym scenariuszu muszą mieć sieci grupy przepływu rejestrowanie zabezpieczeń włączone na co najmniej jedną grupę zabezpieczeń sieci w ramach Twojego konta. Instrukcje dotyczące włączania dzienniki przepływu zabezpieczeń sieciowych, można znaleźć w artykule [wprowadzenie do przepływu rejestrowania dla grup zabezpieczeń sieci](network-watcher-nsg-flow-logging-overview.md).


### <a name="set-up-the-elastic-stack"></a>Konfigurowanie elastycznej stosu
Łącząc dzienniki przepływu NSG do stosu elastycznych, możemy utworzyć pulpit nawigacyjny Kibana co pozwala wyszukiwania, wykresy, analizowanie i pochodzi z naszych dzienników szczegółowych informacji.

#### <a name="install-elasticsearch"></a>Zainstaluj Elasticsearch

1. Elastyczne stosu w wersji 5.0 i nowszych wymaga Java 8. Uruchom polecenie `java -version` do sprawdź wersję. Jeśli nie masz java instalacji, zapoznaj się z dokumentacją na [witryny sieci Web programu Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Pobierz poprawny pakiet binarnych systemu:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Inne metody instalacji można znaleźć w folderze [Elasticsearch instalacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Sprawdź, czy Elasticsearch działa przy użyciu polecenia:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Powinna zostać wyświetlona odpowiedź podobną do poniższego:

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

Dodatkowe instrukcje dotyczące instalowania elastycznej wyszukiwania, można znaleźć na stronie [instalacji](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Zainstaluj Logstash

1. Aby zainstalować Logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Obok należy skonfigurować Logstash dostępu i analizować dzienniki przepływu. Tworzenie pliku logstash.conf przy użyciu:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Dodaj następującą zawartość do pliku:

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

Aby uzyskać dalsze instrukcje na temat instalowania Logstash odwoływać się do [oficjalnej dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalowanie wtyczki wejściowych Logstash magazynu obiektów blob platformy Azure

Ten dodatek plug-in Logstash pozwoli uzyskać bezpośredniego dostępu do dzienników przepływu ze swojego konta magazynu wyznaczonego. Aby zainstalować ten dodatek plug-in, z poziomu katalogu instalacyjnego Logstash domyślnej (w tym /usr/share/logstash/bin przypadków), uruchom polecenie:

```bash
logstash-plugin install logstash-input-azureblob
```

Aby uruchomić Logstash, uruchom polecenie:

```bash
sudo /etc/init.d/logstash start
```

Aby uzyskać więcej informacji na temat tej wtyczki, zajrzyj do dokumentacji [tutaj](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)

### <a name="install-kibana"></a>Zainstaluj Kibana

1. Uruchom następujące polecenia, aby zainstalować Kibana:

  ```bash
  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
  ```

1. Aby uruchomić Kibana należy użyć polecenia:

  ```bash
  cd kibana-5.2.0-linux-x86_64/
  ./bin/kibana
  ```

1. Aby wyświetlić Kibana interfejsu sieci web, przejdź do `http://localhost:5601`
1. W tym scenariuszu wzorzec indeksu, używany dla dzienników przepływu jest "nsg przepływu dzienniki". Wzór indeksu w sekcji "wyjściowej" w pliku logstash.conf mogą ulec zmianie.

1. Jeśli chcesz wyświetlić pulpit nawigacyjny Kibana zdalnie, Utwórz regułę ruchu przychodzącego grupy NSG zezwalania na dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Utwórz pulpit nawigacyjny Kibana

W tym artykule firma Microsoft umieściła przykładowy pulpit nawigacyjny do wyświetlania trendów i informacji w alerty.

![rysunek 1][1]

1. Pobierz plik pulpitu nawigacyjnego [tutaj](https://aka.ms/networkwatchernsgflowlogdashboard), plik wizualizacji [tutaj](https://aka.ms/networkwatchernsgflowlogvisualizations), a plikiem zapisanego wyszukiwania [tutaj](https://aka.ms/networkwatchernsgflowlogsearch).

1. W obszarze **zarządzania** kartę z Kibana, przejdź do **zapisane obiektów** i zaimportować wszystkie trzy pliki. Następnie z **pulpitu nawigacyjnego** kartę można otwierać i załadować przykładowy pulpit nawigacyjny.

Można również utworzyć własne wizualizacje i pulpity nawigacyjne dostosowane do metryki użytkownika. Przeczytaj więcej na temat tworzenia wizualizacje Kibana z jego Kibana [oficjalnej dokumentacji](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Wizualizuj dzienniki przepływu NSG

Przykładowy pulpit nawigacyjny udostępnia kilka wizualizacje dzienników przepływu:

1. Przechodzi przez kierunku/decyzji w miarę upływu czasu — czas serii wykresów wyświetlana jest liczba przepływów w przedziale czasu. Jednostka czasu i zakres obu tych wizualizacje można edytować. Przepływy decyzją zawiera część akceptować lub odrzucać decyzje, podczas gdy przepływów przez kierunek część ruchu przychodzącego i wychodzącego. Z tych elementów wizualnych można zbadać ruchu trendów w czasie i poszukaj nagłego ani nietypowe wzorce.

  ![figure2][2]

1. Przechodzi przez Port docelowy/źródłowy — wykresy kołowe przedstawiający podział przepływów do odpowiednich portów. Z tym widokiem widać z najczęściej używane porty. Jeśli klikniesz przycisk na określonym porcie w obrębie wykresu kołowego, pozostałej części pulpitu nawigacyjnego zostanie odfiltrowana do przepływów tego portu.

  ![figure3][3]

1. Liczba przepływów i najwcześniejszą godzinę dziennika — pokazuje liczbę przepływów rejestrowane i Data najwcześniejszą dziennika przechwycone metryki.

  ![figure4][4]

1. Przepływy NSG i reguła — wykres słupkowy pokazywania podział przepływów w poszczególnych grupach NSG, a także dystrybucji reguły w poszczególnych grupach NSG. W tym miejscu widać, które NSG i reguły generowane najczęściej ruchu.

  ![figure5][5]

1. Pierwszych 10 lokalizacja źródłowa/docelowa adresy IP — wykresy słupkowe przedstawiający 10 pierwszych źródłowe i docelowe adresy IP. Można dostosować te wykresy, aby wyświetlić więcej lub mniej top adresów IP. W tym miejscu zobaczysz, najczęściej występujących adresów IP, a także decyzja ruchu (zezwalania lub odmowy) wysyłanych do każdego adresu IP.

  ![figure6][6]

1. Przepływ krotek — ta tabela zawiera informacje zawarte w każdej krotki przepływu, a także odpowiednie NGS i reguły.

  ![figure7][7]

Używanie paska zapytania w górnej części pulpitu nawigacyjnego, można filtrować dół pulpit nawigacyjny, zależnie od żadnego parametru przepływów, takich jak identyfikator subskrypcji, grupy zasobów, reguły lub innej zmiennej zainteresowań. Więcej informacji na temat kwerend i filtrów w Kibana, można znaleźć w temacie [oficjalnej dokumentacji](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Podsumowanie

Łącząc dzienniki przepływu sieciową grupę zabezpieczeń z elastycznej stosu, mają uzyskujemy zaawansowanego, można dostosować sposób wizualizacji naszych ruchu sieciowego. Te pulpity nawigacyjne pozwala szybko uzyskać i udostępniać informacji na temat ruchu sieciowego, jak również filtru w dół i badanie na wszelkich potencjalnych nieprawidłowości. Przy użyciu Kibana, można dostosować te pulpity nawigacyjne i tworzenie wizualizacji określonych potrzeb żadnych zabezpieczeń, inspekcji i zgodności.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wizualizacji NSG dzienników przepływu z usługi Power BI, odwiedzając [wizualizacji NSG przepływa dzienników przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png

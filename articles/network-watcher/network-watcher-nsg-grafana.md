---
title: Zarządzanie dzienniki sieciowych grup zabezpieczeń przepływu przy użyciu usługi Network Watcher i Grafana | Dokumentacja firmy Microsoft
description: Zarządzanie i analizować dzienniki sieciowych grup zabezpieczeń przepływu na platformie Azure przy użyciu usługi Network Watcher i Grafana.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: mareat
ms.openlocfilehash: 73173c144f979d4a10b90a16aec783fe51a3f90e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116255"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Zarządzanie i analizować dzienniki przepływu sieciowych grup zabezpieczeń przy użyciu usługi Network Watcher i Grafana

[Grupy zabezpieczeń (NSG) dzienników przepływu sieci](network-watcher-nsg-flow-logging-overview.md) zawierają informacje, który może służyć do zrozumienia przychodzący i wychodzący ruch IP w interfejsach sieciowych. Te dzienniki przepływu Pokaż przepływy wychodzące i przychodzące na poszczególnych reguł sieciowej grupy zabezpieczeń, karty Sieciowej przepływ ma zastosowanie do 5-elementowych informacji o przepływie (źródłowy/docelowy adres IP, Port źródłowy i docelowy, Protocol), a jeśli zezwolenie lub odrzucenie ruchu sieciowego.

> [!Warning]  
> Poniższe kroki pracy z dzienników przepływu w wersji 1. Aby uzyskać więcej informacji, zobacz [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md). Poniższe instrukcje nie będzie działać w wersji 2 pliki dziennika, bez żadnych modyfikacji.

Masz liczby sieciowych grup zabezpieczeń w sieci za pomocą funkcji rejestrowania przepływu włączone. Tę kwotę rejestrowania danych sprawia, że kłopotliwe przeanalizować oraz uzyskiwanie szczegółowych informacji z dzienników. Ten artykuł zawiera rozwiązanie, aby centralnie zarządzać tych dzienników przepływu sieciowych grup zabezpieczeń przy użyciu platformy Grafana, typu open source Tworzenie wykresów narzędzia, usługi ElasticSearch, wyszukiwania rozproszonego i aparat analityczny i Logstash, który jest potok przetwarzania danych po stronie serwera "open source".  

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu sieciowej grupy zabezpieczeń są włączane przy użyciu usługi Network Watcher i są przechowywane w usłudze Azure blob storage. Dodatek Logstash służy do łączenia i przetwarzania dzienników przepływu z usługi blob storage i wysyłać je do programu ElasticSearch.  Gdy dzienników przepływu są przechowywane w programie ElasticSearch, mogą być analizowane i wizualizowane w dostosowanych pulpitów nawigacyjnych w Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Kroki instalacji

### <a name="enable-network-security-group-flow-logging"></a>Rejestrowanie przepływu Włącz sieciowej grupy zabezpieczeń

W tym scenariuszu konieczne jest posiadanie sieci zabezpieczeń grupy przepływu Rejestrowanie włączone na co najmniej jedną grupę zabezpieczeń sieci w ramach Twojego konta. Aby uzyskać instrukcje na temat włączania dzienników przepływu zabezpieczeń sieci, zapoznaj się z następującym artykułem [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Zagadnienia dotyczące instalacji

W tym przykładzie Grafana, ElasticSearch i Logstash są skonfigurowane na serwerze Ubuntu 16.04 LTS wdrożonych na platformie Azure. Tej minimalnej instalacji jest używane do uruchamiania wszystkie trzy składniki — wszystkie z nich na tej samej maszyny Wirtualnej. Ta konfiguracja powinna służyć wyłącznie do testowania i innych krytycznych obciążeń. Program Logstash, Elasticsearch i Grafana mogą być wszystkie architekturę niezależne skalowanie na wiele wystąpień. Aby uzyskać więcej informacji zobacz dokumentację dla każdego z tych składników.

### <a name="install-logstash"></a>Instalowanie programu Logstash

Używasz Logstash spłaszczanie dzienników przepływu formatu JSON do poziomu krotki przepływu.

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurowanie usługi Logstash w celu analizowania dzienników przepływu i wyślij je do programu ElasticSearch. Tworzenie pliku Logstash.conf przy użyciu:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Dodaj następującą zawartość do pliku. Zmień klucz konta magazynu nazwy i dostęp do odzwierciedlenia szczegółów konta magazynu:

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

Plik konfiguracji Logstash, pod warunkiem składa się z trzech części: dane wejściowe, filtrować i danych wyjściowych.
Wejściowy sekcji wskazuje źródło danych wejściowych dzienników, które będą przetwarzane Logstash — w tym przypadku będziemy używać "azureblob" danych wejściowych (zainstalowaną wtyczką w następnych krokach), który umożliwi nam dostęp do plików JSON dzienników przepływu sieciowej grupy zabezpieczeń przechowywanych w magazynie obiektów blob. 

Sekcja filtru następnie spłaszcza każdego pliku dziennika przepływu, aby każda krotka poszczególnych przepływ i jego właściwości staje się oddzielne zdarzenie Logstash.

Na koniec sekcji danych wyjściowych przekazuje każde zdarzenie Logstash serwerowi programu ElasticSearch. Możesz zmodyfikować plik konfiguracji Logstash w zależności od określonych potrzeb.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalowanie wtyczki wejściowych Logstash dla usługi Azure Blob storage

Ten dodatek Logstash umożliwia bezpośrednio ze swojego konta magazynu obiektów blob w wyznaczonym dostęp do dzienników przepływów. Aby zainstalować tej wtyczki, z katalogu instalacyjnego programu Logstash domyślna (w tym /usr/share/logstash/bin wielkości liter) uruchom polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tej wtyczki w zobacz [wejściowych wtyczkę Logstash dla usługi Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalowanie usługi ElasticSearch

Aby zainstalować usługi ElasticSearch, można użyć poniższego skryptu. Aby uzyskać informacje o instalowaniu programu ElasticSearch, zobacz [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Zainstaluj narzędzia Grafana

Aby zainstalować i uruchomić narzędzia Grafana, uruchom następujące polecenia:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Dodatkowe informacje dotyczące instalacji, zobacz [instalowania w systemie Debian / Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Dodaj serwer programu ElasticSearch jako źródło danych

Następnie należy dodać indeks ElasticSearch, zawierające dzienniki przepływu jako źródła danych. Źródła danych można dodawać przez zaznaczenie **Dodaj źródło danych** i wypełniając formularz istotne informacje. Przykład tej konfiguracji znajduje się na następującym zrzucie ekranu:

![Dodawanie źródła danych](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Teraz, że pomyślnie skonfigurowano Grafana odczytywanie indeks ElasticSearch, zawierający dzienników przepływu sieciowych grup zabezpieczeń, można utworzyć i spersonalizować pulpitów nawigacyjnych. Aby utworzyć nowy pulpit nawigacyjny, wybierz **Utwórz swój pierwszy pulpit nawigacyjny**. Następujące Przykładowa konfiguracja wykres przedstawia przepływów posegmentowana według reguły sieciowej grupy zabezpieczeń:

![Wykres do pulpitu nawigacyjnego](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Poniższy zrzut ekranu przedstawia wykres i wykres przedstawiający najważniejsze przepływów i częstotliwości. Przepływy są także wyświetlane przez regułę sieciowej grupy zabezpieczeń i przepływy według decyzji. Grafana jest dużym stopniu dostosowywane, dlatego zaleca się, że pulpity nawigacyjne w zależności od określonych potrzeb monitorowania są tworzone. Poniższy przykład przedstawia typowy pulpitu nawigacyjnego:

![Wykres do pulpitu nawigacyjnego](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Podsumowanie

Dzięki integracji usługi Network Watcher z rozwiązań ElasticSearch i Grafana, masz są teraz wygodne i scentralizowany sposób wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń, a także innych danych i zarządzanie nimi. Grafana ma wiele innych zaawansowanych funkcji graficznych, które może również służyć do dalszego zarządzania dzienników przepływu i lepiej zrozumieć ruchu sieciowego. Teraz, gdy masz wystąpienie Grafana, skonfiguruj połączenie platformy Azure, możesz ją także zapoznaj się z innych funkcji, która zapewnia w dalszym ciągu.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o korzystaniu z [usługi Network Watcher](network-watcher-monitoring-overview.md).


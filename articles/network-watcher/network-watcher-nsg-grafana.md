---
title: Zarządzanie sieci grupy przepływu dzienniki zabezpieczeń przy użyciu obserwatora sieciowego i Grafana | Dokumentacja firmy Microsoft
description: Zarządzanie i analizować sieci zabezpieczeń grupy przepływu dzienniki na platformie Azure przy użyciu obserwatora sieciowego i Grafana.
services: network-watcher
documentationcenter: na
author: kumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: 44cf074223c88b8fa539144c0d948e68ae6cbd13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864091"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Zarządzanie i analizowanie dzienników przepływu grupy zabezpieczeń sieci przy użyciu obserwatora sieciowego i Grafana

[Sieciowe grupy zabezpieczeń (NSG) przepływu dzienniki](network-watcher-nsg-flow-logging-overview.md) zawierają informacje, który może służyć do zrozumienia przychodzące i wychodzące ruchu IP w interfejsach sieciowych. Te dzienniki przepływu Pokaż przepływów wychodzącego i przychodzącego na na podstawie reguł NSG, karty interfejsu Sieciowego przepływ ma zastosowanie do 5-elementowej informacji o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego Protocol), i jeśli ruch został dozwolony lub niedozwolony.

Przy włączonym rejestrowaniu przepływu, mogą mieć wiele grup NSG w sieci. Ilość danych rejestrowania, ułatwia skomplikowane, aby przeanalizować i uzyskaj informacje z dzienników. Ten artykuł zawiera rozwiązanie centralnie zarządzać tych dzienników przepływu NSG przy użyciu Grafana, typu open source Tworzenie wykresów narzędzia, ElasticSearch wyszukiwania rozproszonego oraz aparat analizy i Logstash, który jest typu open source potoku przetwarzania danych po stronie serwera.  

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu NSG są włączone, za pomocą Monitora sieci i są przechowywane w magazynie obiektów blob platformy Azure. Dodatek Logstash służy do połączenia i przetwarzania przepływu dzienniki z magazynu obiektów blob i wysyłać je do ElasticSearch.  Gdy przepływ dzienniki są przechowywane w ElasticSearch, mogą być przeanalizowane i wizualizowane w dostosowanych pulpitów nawigacyjnych w Grafana.

![Grafana obserwatora sieciowego NSG](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Kroki instalacji

### <a name="enable-network-security-group-flow-logging"></a>Rejestrowanie przepływu włączyć grupy zabezpieczeń sieci

W tym scenariuszu muszą mieć sieci grupy przepływu rejestrowanie zabezpieczeń włączone na co najmniej jedną grupę zabezpieczeń sieci w ramach Twojego konta. Instrukcje dotyczące włączania dzienniki przepływu zabezpieczeń sieciowych, można znaleźć w artykule [wprowadzenie do przepływu rejestrowania dla grup zabezpieczeń sieci](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Uwagi dotyczące instalacji

W tym przykładzie Grafana, ElasticSearch i Logstash są skonfigurowane na serwerze LTS 16.04 Ubuntu wdrożona na platformie Azure. Minimalny Instalatora służy do uruchamiania wszystkich trzech komponentów — wszystkie z nich na tej samej maszyny Wirtualnej. Tego ustawienia należy używać tylko dla obciążeń testowych i niekrytyczne. Logstash, Elasticsearch i Grafana mogą być wszystkie zaprojektowana niezależne skalowanie w wielu wystąpieniach. Aby uzyskać więcej informacji zobacz dokumentację dla każdego z tych składników.

### <a name="install-logstash"></a>Zainstaluj Logstash

Logstash umożliwia spłaszczanie dzienniki przepływu formatu JSON do poziomu przepływu spójnej kolekcji.

1. Aby zainstalować Logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Skonfiguruj Logstash do analizowania dzienników przepływu i wysyłać je do ElasticSearch. Tworzenie pliku Logstash.conf przy użyciu:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Dodaj następującą zawartość do pliku. Zmień klucz konta magazynu nazwy i dostęp do uwzględnienia informacji o koncie magazynu:

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

W pliku config Logstash podane składa się z trzech części: dane wejściowe, filtrów i danych wyjściowych. Sekcji wejściowej Określa źródło danych wejściowych dzienników, które przetwarzają Logstash — w takim przypadku zamierzamy Użyj "azureblob" wejściowych wtyczki (zainstalowany w następnych krokach), która pozwoli nam dostępu do plików JSON dziennika przepływu NSG przechowywanych w magazynie obiektów blob. 

Sekcji filtra spłaszcza następnie każdego pliku dziennika przepływu tak, aby każda krotka poszczególnych przepływu i jego właściwości staje się oddzielne zdarzenie Logstash.

Na koniec sekcji Wyjście przekazuje każdego zdarzenia Logstash serwerowi ElasticSearch. Możesz zmodyfikować plik konfiguracji Logstash w zależności od określonych potrzeb.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalowanie wtyczki wejściowych Logstash magazynu obiektów Blob platformy Azure

Ten dodatek plug-in Logstash umożliwia bezpośredni dostęp do dzienników przepływu ze swojego konta magazynu wyznaczonego obiektu blob. Aby zainstalować ten plug, z katalogu instalacyjnego Logstash domyślnej (w tym wielkość /usr/share/logstash/bin) uruchom polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tej wtyczki w temacie [Logstash wejściowych wtyczki dla obiektów blob magazynu Azure](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Zainstaluj ElasticSearch

Następujący skrypt służy do instalowania ElasticSearch. Aby uzyskać informacje o instalowaniu ElasticSearch, zobacz [stosu elastycznych](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

### <a name="install-grafana"></a>Zainstaluj Grafana

Aby zainstalować i uruchomić Grafana, uruchom następujące polecenia:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Dodatkowe informacje dotyczące instalacji, zobacz [instalowanie na Debian / Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Dodaj serwer ElasticSearch jako źródła danych

Następnie należy dodać indeksu ElasticSearch zawierającego przepływ dzienniki jako źródła danych. Źródło danych można dodawać przez zaznaczenie **Dodaj źródło danych** i wypełnienie formularza istotne informacje. Przykładowe tej konfiguracji można znaleźć na poniższym zrzucie ekranu:

![Dodawanie źródła danych](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Utwórz pulpit nawigacyjny

Teraz, że pomyślnie skonfigurowano Grafana do odczytu z indeksu ElasticSearch zawierający NSG przepływu dzienniki, można utworzyć i spersonalizować pulpitów nawigacyjnych. Aby utworzyć nowy pulpit nawigacyjny, wybierz **Tworzenie pulpitu nawigacyjnego pierwszy**. Następujące Przykładowa konfiguracja wykres przedstawia segmentowanych przez reguły NSG przepływów:

![Wykres pulpitu nawigacyjnego](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Poniższy zrzut ekranu przedstawia wykres i wykres przedstawiający top przepływów i częstotliwości. Przepływy są także wyświetlane przez reguły NSG i przepływów przez decyzję. Grafana jest dużym stopniu dostosowywane, dlatego zaleca się, że tworzenie pulpitów nawigacyjnych, w zależności od określonych potrzeb monitorowania. W poniższym przykładzie przedstawiono typowe pulpitu nawigacyjnego:

![Wykres pulpitu nawigacyjnego](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Podsumowanie

Integrowanie obserwatora sieciowego z ElasticSearch i Grafana, teraz mogą uzyskiwać wygodne i scentralizowane zarządzanie i wizualizacji dzienniki przepływu NSG, a także innych danych. Grafana ma wiele innych zaawansowanych funkcji graficznych, które mogą służyć do dalszego zarządzania dziennikami przepływu i Poznaj lepiej ruchu sieciowego. Teraz, gdy masz wystąpienie Grafana, skonfigurować i podłączone do platformy Azure, możesz także kontynuować do eksplorowania innych funkcji, która zapewnia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o korzystaniu z [obserwatora sieciowego](network-watcher-monitoring-overview.md).


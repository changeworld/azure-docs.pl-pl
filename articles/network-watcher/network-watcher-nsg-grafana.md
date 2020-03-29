---
title: Zarządzanie dziennikami przepływu nsg za pomocą grawany
titleSuffix: Azure Network Watcher
description: Zarządzanie dziennikami przepływu grupy zabezpieczeń sieciowych i analizowanie ich na platformie Azure przy użyciu funkcji Kontrola sieci i grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: c48d5a02cdb8ef63904642c6c2c76cb5d61e1f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840914"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Zarządzanie dziennikami przepływu sieciowej grupy zabezpieczeń i analizowanie ich przy użyciu funkcji Kontrola sieci i grana

[Dzienniki przepływu sieciowej grupy zabezpieczeń (NSG)](network-watcher-nsg-flow-logging-overview.md) zawierają informacje, które mogą być używane do zrozumienia ruchu IP ruchu przychodzącego i wychodzącego na interfejsach sieciowych. Te dzienniki przepływu pokazują przepływy wychodzące i przychodzące na podstawie reguły sieciowej sieciowej sieciowej, karty sieciowej, do tej ostatniej karty informacji o przepływie (adres IP źródła/miejsca docelowego, protokół) oraz o tym, czy ruch był dozwolony lub odrzucany.

> [!Warning]  
> Poniższe kroki działają z dziennikami przepływu w wersji 1. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieci .](network-watcher-nsg-flow-logging-overview.md) Poniższe instrukcje nie będą działać z wersją 2 plików dziennika, bez modyfikacji.

W sieci może być dostępnych wiele sieciowych grup zabezpieczeń z włączonym rejestrowaniem przepływu. Ta ilość danych rejestrowania sprawia, że kłopotliwe jest analizowanie i uzyskiwanie szczegółowych informacji z dzienników. Ten artykuł zawiera rozwiązanie do centralnego zarządzania tymi dziennikami przepływu sieciowej grupy zabezpieczeń przy użyciu Grafana, narzędzia do wykresów open source, ElasticSearch, rozproszonego aparatu wyszukiwania i analizy oraz Logstash, który jest potokiem przetwarzania danych po stronie serwera open source.  

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu sieciowej sieciowej są włączone przy użyciu funkcji Kontrola sieci i są przechowywane w magazynie obiektów blob platformy Azure. Wtyczka Logstash służy do łączenia i przetwarzania dzienników przepływu z magazynu obiektów blob i wysyłania ich do ElasticSearch.  Po przechowywane dzienniki przepływu w ElasticSearch, mogą być analizowane i wizualizowane w niestandardowych pulpitów nawigacyjnych w Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Kroki instalacji

### <a name="enable-network-security-group-flow-logging"></a>Włączanie rejestrowania przepływu sieciowej grupy zabezpieczeń

W tym scenariuszu musi być włączone rejestrowanie przepływu sieciowej grupy zabezpieczeń na co najmniej jednej sieciowej grupie zabezpieczeń na koncie. Instrukcje dotyczące włączania dzienników przepływu zabezpieczeń sieciowych można znaleźć w artykule [Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieciowych](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Zagadnienia dotyczące instalacji

W tym przykładzie Grafana, ElasticSearch i Logstash są skonfigurowane na serwerze Ubuntu 16.04 LTS wdrożonym na platformie Azure. Ta minimalna konfiguracja jest używana do uruchamiania wszystkich trzech składników — wszystkie są uruchomione na tej samej maszynie wirtualnej. Ta konfiguracja powinna być używana tylko do testowania i obciążeń niekrytycznych. Logstash, Elasticsearch i Grafana można zaprojektować, aby śmy byli skazywany niezależnie w wielu wystąpieniach. Aby uzyskać więcej informacji, zobacz dokumentację dla każdego z tych składników.

### <a name="install-logstash"></a>Instalowanie programu Logstash

Logstash służy do spłaszczenia dzienników przepływu sformatowany JSON do poziomu krotki przepływu.

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Skonfiguruj program Logstash do analizowania dzienników przepływu i wysyłania ich do programu ElasticSearch. Utwórz plik Logstash.conf przy użyciu:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Dodaj do pliku następującą zawartość. Zmień nazwę konta magazynu i klucz dostępu, aby odzwierciedlić szczegóły konta magazynu:

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

Dostarczony plik konfiguracyjny Logstash składa się z trzech części: wejściowego, filtru i wyjścia.
Sekcja wejściowa wyznacza źródło wejściowe dzienników, które logstash będzie przetwarzać - w tym przypadku będziemy używać wtyczki wejściowej "azureblob" (zainstalowanej w następnych krokach), która pozwoli nam uzyskać dostęp do plików JSON dziennika przepływu NSG przechowywanych w magazynie obiektów blob. 

Sekcja filtru następnie spłaszcza każdy plik dziennika przepływu, tak aby każda krotka przepływu i skojarzone z nim właściwości stają się osobnym zdarzeniem Logstash.

Na koniec sekcja danych wyjściowych przekazuje każde zdarzenie Logstash do serwera ElasticSearch. Możesz zmodyfikować plik konfiguracyjny Logstash zgodnie z konkretnymi potrzebami.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalowanie wtyczki wejściowej Logstash dla magazynu obiektów Blob platformy Azure

Ta wtyczka Logstash umożliwia bezpośredni dostęp do dzienników przepływu z ich wyznaczonego konta magazynu obiektów blob. Aby zainstalować tę wtyczkę, z domyślnego katalogu instalacyjnego Logstash (w tym przypadku /usr/share/logstash/bin) uruchom polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tej wtyczki, zobacz [Wtyczka wprowadzania logstash dla obiektów blob usługi Azure Storage](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalowanie programu ElasticSearch

Do zainstalowania programu ElasticSearch można użyć następującego skryptu. Aby uzyskać informacje dotyczące instalowania programu ElasticSearch, zobacz [Elastyczny stos](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

### <a name="install-grafana"></a>Instalowanie grawany

Aby zainstalować i uruchomić grana, uruchom następujące polecenia:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Aby uzyskać dodatkowe informacje na temat instalacji, zobacz [Instalowanie na Debianie / Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Dodawanie serwera ElasticSearch jako źródła danych

Następnie należy dodać indeks ElasticSearch zawierający dzienniki przepływu jako źródło danych. Źródło danych można dodać, wybierając pozycję **Dodaj źródło danych** i wypełniając formularz odpowiednimi informacjami. Przykład tej konfiguracji można znaleźć na poniższym zrzucie ekranu:

![Dodawanie źródła danych](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Teraz, po pomyślnym skonfigurowaniu grafany do odczytu z indeksu ElasticSearch zawierającego dzienniki przepływu nsg, można tworzyć i personalizować pulpity nawigacyjne. Aby utworzyć nowy pulpit nawigacyjny, wybierz pozycję **Utwórz pierwszy pulpit nawigacyjny**. Poniższa przykładowa konfiguracja wykresu przedstawia przepływy podzielone na segmenty według reguły sieciowej grupy zabezpieczeń:

![Wykres pulpitu nawigacyjnego](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Poniższy zrzut ekranu przedstawia wykres i wykres przedstawiający górne przepływy i ich częstotliwość. Przepływy są również wyświetlane przez regułę nsg i przepływy według decyzji. Grafana jest wysoce konfigurowalna, dlatego zaleca się tworzenie pulpitów nawigacyjnych dostosowanych do konkretnych potrzeb związanych z monitorowaniem. W poniższym przykładzie przedstawiono typowy pulpit nawigacyjny:

![Wykres pulpitu nawigacyjnego](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Podsumowanie

Dzięki integracji funkcji Kontrola sieci z programem ElasticSearch i Grafą masz teraz wygodny i scentralizowany sposób zarządzania dziennikami przepływu sieciowej sieciowej sieciowej sieciowej i wizualizacji ich oraz innych danych. Grafana ma wiele innych zaawansowanych funkcji wykresu, które mogą być również używane do dalszego zarządzania dziennikami przepływu i lepszego zrozumienia ruchu sieciowego. Teraz, gdy masz skonfigurowane wystąpienie Grafana i połączenie z platformą Azure, możesz nadal eksplorować inne funkcje, które oferuje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o korzystaniu z [funkcji Obserwatora sieci](network-watcher-monitoring-overview.md).


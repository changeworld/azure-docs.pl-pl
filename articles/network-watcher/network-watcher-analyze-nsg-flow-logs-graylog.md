---
title: Analizowanie dzienników przepływów grupy zabezpieczeń sieci platformy Azure — z narzędzia Graylog | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać i analizowanie dzienników przepływów grupy zabezpieczeń sieci na platformie Azure przy użyciu usługi Network Watcher i z narzędzia Graylog
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
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: a5fadcfce154740a79a8764f44f08b21ad18f4d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625285"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Zarządzanie i analizowanie dzienników przepływów grupy zabezpieczeń sieci na platformie Azure przy użyciu usługi Network Watcher i z narzędzia Graylog

[Dzienników przepływu grupy zabezpieczeń sieci](network-watcher-nsg-flow-logging-overview.md) informacje, które można użyć, aby zrozumieć przychodzący i wychodzący ruch IP dla interfejsów sieciowych platformy Azure. Dzienniki przepływów Pokaż przepływy wychodzące i przychodzące na na podstawie reguł grupy zabezpieczeń sieci, interfejs sieciowy przepływ ma zastosowanie do 5-elementowe spójne kolekcje informacji (źródłowy/docelowy adres IP, Port źródłowy i docelowy, Protocol) na temat przepływu, a jeśli zezwolenie lub odrzucenie ruchu .

Może mieć wiele grup zabezpieczeń sieci w sieci, z włączono rejestrowanie usługi flow. Kilka grup zabezpieczeń sieci z włączone rejestrowanie przepływu można tworzyć i kłopotliwe przeanalizować oraz uzyskiwanie szczegółowych informacji z dzienników. Ten artykuł zawiera rozwiązanie, aby centralnie zarządzać te dzienniki sieciowych grup zabezpieczeń usługi flow przy użyciu z narzędzia Graylog, Zarządzanie dziennikami "open source" oraz narzędzia do analizy i Logstash, potok przetwarzania danych po stronie serwera "open source".

> [!Warning]
> Poniższe kroki pracy z dzienników przepływu w wersji 1. Aby uzyskać więcej informacji, zobacz [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md). Poniższe instrukcje nie będzie działać w wersji 2 pliki dziennika, bez żadnych modyfikacji.

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu sieciowych grup zabezpieczeń są włączane przy użyciu usługi Network Watcher. Przepływ dzienników przepływu w usłudze Azure blob storage. Dodatek Logstash służy do łączenia i przetwarzanie dzienników przepływu z usługi blob storage i wysyłać je do z narzędzia Graylog. Gdy dzienników przepływu są przechowywane w z narzędzia Graylog, mogą być analizowane i wizualizowane w dostosowanych pulpitów nawigacyjnych.

![Przepływ pracy z narzędzia Graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Etapy instalacji

### <a name="enable-network-security-group-flow-logging"></a>Włącz rejestrowanie przepływu grup zabezpieczeń sieci

W tym scenariuszu konieczne jest posiadanie sieci zabezpieczeń grupy przepływu Rejestrowanie włączone na co najmniej jedną sieciową grupę zabezpieczeń na Twoim koncie. Instrukcje dotyczące włączania sieci dzienników przepływu grupy zabezpieczeń, zapoznaj się z następującym artykułem [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Konfigurowanie z narzędzia Graylog

W tym przykładzie z narzędzia Graylog i Logstash są skonfigurowane w systemie Ubuntu Server 14.04, wdrożonych na platformie Azure.

- Zapoznaj się [dokumentacji](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) z z narzędzia Graylog, aby uzyskać instrukcje krok po kroku na temat instalacji na Ubuntu.
- Upewnij się, że również Skonfiguruj interfejs sieci web z narzędzia Graylog zgodnie z poniższymi [dokumentacji](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

W tym przykładzie użyto minimalnej konfiguracji z narzędzia Graylog (tj.) pojedyncze wystąpienie z narzędzia Graylog), ale z narzędzia Graylog może być architektura zaprojektowana pod kątem skalować w zasobach w zależności od używanego systemu i produkcji potrzeb. Aby uzyskać więcej informacji na temat architektury uwag ani architektury szczegółowe wskazówki, zobacz temat z narzędzia Graylog firmy [dokumentacji](https://docs.graylog.org/en/2.2/pages/architecture.html) i [Przewodnik po architekturze](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Z narzędzia Graylog można zainstalować na wiele sposobów, w zależności od platformy i preferencje. Pełną listę metody instalacji to możliwe, można znaleźć w oficjalnej z narzędzia Graylog firmy [dokumentacji](https://docs.graylog.org/en/2.2/pages/installation.html). Aplikacja serwera z narzędzia Graylog działa w dystrybucjach systemu Linux i ma następujące wymagania wstępne:

-  Java SE 8 lub nowszym — [Azul Azure JDK dokumentacji](https://aka.ms/azure-jdks)
-  Elastyczne wyszukiwanie 2.x (2.1.0 lub nowszej)- [dokumentacji instalacji usługi Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  Bazy danych MongoDB 2.4 lub nowszym — [dokumentacji instalacji bazy danych MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalowanie programu Logstash

Program Logstash jest używany do spłaszczenia dzienników przepływu formatu JSON do poziomu krotki przepływu. Spłaszczanie dzienników przepływu ułatwia dzienniki do organizowania i wyszukiwanie z narzędzia Graylog.

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Konfigurowanie usługi Logstash w celu analizowania dzienników przepływu i wysyłać je do z narzędzia Graylog. Utwórz plik Logstash.conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Dodaj następującą zawartość do pliku. Zmień wyróżnione wartości, aby odzwierciedlić szczegółów konta magazynu:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Plik konfiguracji Logstash, pod warunkiem składa się z trzech części: dane wejściowe, filtrować i danych wyjściowych. Wejściowy sekcji wyznacza źródła danych wejściowych dzienników, które będą przetwarzać Logstash — w tym przypadku ma używać Blog dotyczący platformy Azure danych wejściowych (zainstalowaną wtyczką w następnych krokach) umożliwiający dostęp do grupy zabezpieczeń sieciowego pliki JSON przechowywane w magazynie obiektów blob dziennika.

Sekcja filtru następnie spłaszcza każdego pliku dziennika przepływu, aby każda krotka poszczególnych przepływ i jego właściwości staje się oddzielne zdarzenie Logstash.

Na koniec sekcji danych wyjściowych przekazuje każde zdarzenie Logstash do serwera z narzędzia Graylog. Do własnych konkretne potrzeby, zmodyfikować plik konfiguracji Logstash, zgodnie z potrzebami.

   > [!NOTE]
   > Poprzedni plik config przyjęto założenie, że serwer z narzędzia Graylog został skonfigurowany na adres IP hosta lokalnego sprzężenie zwrotne 127.0.0.1. W przeciwnym razie należy zmienić parametru hostów, w sekcji danych wyjściowych do prawidłowego adresu IP.

Aby uzyskać dalsze instrukcje na temat instalowania programu Logstash, zobacz Logstash [dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalowanie wtyczki dla usługi Azure blob storage dane wejściowe Logstash

Dodatek Logstash pozwala uzyskać bezpośredni dostęp do dzienników przepływu ze swojego konta magazynu wyznaczonego obiektu blob. Aby zainstalować dodatek typu plug-in, z domyślnego katalogu instalacji programu Logstash (w tym /usr/share/logstash/bin wielkości liter), uruchom następujące polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tej wtyczki w zobacz [dokumentacji](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Skonfiguruj połączenie z programu Logstash, aby z narzędzia Graylog

Skoro masz do nawiązania połączenia i dzienników przepływów przy użyciu programu Logstash i skonfigurować serwer z narzędzia Graylog, należy skonfigurować z narzędzia Graylog do akceptowania przychodzących plików dziennika.

1. Przejdź do serwera z narzędzia Graylog interfejsu sieci web przy użyciu adresu URL skonfigurowany dla niego. Dostęp do interfejsu programu przez kierowanie przeglądarkę, aby `http://<graylog-server-ip>:9000/`

2. Aby przejść do strony konfiguracji, wybierz pozycję **systemu** menu rozwijane w górnym menu nawigacyjnym pasek po prawej stronie, a następnie kliknij przycisk **dane wejściowe**.
   Ewentualnie przejdź do `http://<graylog-server-ip>:9000/system/inputs`

   ![Wprowadzenie](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Aby uruchomić nowe dane wejściowe, wybierz *GELF UDP* w **zaznacz dane wejściowe** listy rozwijanej, a następnie wypełnij formularz. GELF oznacza z narzędzia Graylog rozszerzony Format dziennika. GELF format jest opracowany przez z narzędzia Graylog. Aby dowiedzieć się więcej na temat jego zalety, zobacz artykuł z narzędzia Graylog [dokumentacji](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Upewnij się powiązać dane wejściowe na adres IP został skonfigurowany na serwerze z narzędzia Graylog. Adres IP powinien być zgodny **hosta** pola danych wyjściowych UDP plik konfiguracji Logstash. Domyślny port powinien być *12201*. Upewnij się, port odpowiada **portu** pola w protokołu UDP danych wyjściowych wymienionych na plik konfiguracji Logstash.

   ![Dane wejściowe](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Po uruchomieniu dane wejściowe, możesz powinna zostać wyświetlona w obszarze **lokalnych danych wejściowych** sekcji, jak pokazano na poniższej ilustracji:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Aby dowiedzieć się więcej na temat z narzędzia Graylog komunikatów wejściowych, zapoznaj się [dokumentacji](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Po tych konfiguracji zostały wprowadzone, można uruchomić program Logstash, aby rozpocząć odczytywanie dzienników przepływu za pomocą następującego polecenia: `sudo systemctl start logstash.service`.

### <a name="search-through-graylog-messages"></a>Przeszukiwanie wiadomości z narzędzia Graylog

Po co pewien czas dla serwera z narzędzia Graylog zbierać komunikaty, jesteś w stanie przeszukiwanie wiadomości. Aby sprawdzić komunikaty przesyłane na serwer z narzędzia Graylog z **dane wejściowe** strony konfiguracji, kliknij przycisk "**Pokaż Odebrane komunikaty**" przycisk GELF UDP wejściowy został utworzony. Nastąpi przekierowanie do ekranu, który wygląda podobnie do poniższej ilustracji: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknięcie linku niebieski "% {Message}" rozwija każdy komunikat, aby wyświetlić parametry każda krotka przepływu, jak pokazano na poniższej ilustracji:

![Komunikaty](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Domyślnie wszystkie pola wiadomości są uwzględnione w wyszukiwania, jeśli nie zaznaczysz pola szczegółowy komunikat o błędzie, aby wyszukać. Jeśli chcesz wyszukać określone komunikaty (tj.) — kolekcje przepływów z określonego źródłowy adres IP), można użyć języka zapytań wyszukiwania z narzędzia Graylog jako [udokumentowane](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analizuj dzienniki sieciowych grup zabezpieczeń usługi flow przy użyciu narzędzia Graylog

Skoro już z narzędzia Graylog ją skonfigurować z systemem, można użyć niektóre swoje funkcje aby lepiej zrozumieć dane dzienników przepływów. Jest jeden taki sposób, przy użyciu pulpitów nawigacyjnych w celu utworzenia określonych widoków danych.

### <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

1. Na górnym pasku nawigacyjnym, wybierz **pulpity nawigacyjne** ścieżkę fizyczną lub przejdź do `http://<graylog-server-ip>:9000/dashboards/`

2. W tym miejscu, kliknij zielony **Utwórz pulpit nawigacyjny** przycisk i wypełnij formularz krótka, tytuł i opis pulpitu nawigacyjnego. Trafienia **Zapisz** przycisk, aby utworzyć nowy pulpit nawigacyjny. Zostanie wyświetlony pulpit nawigacyjny podobnie do poniższej ilustracji:

    ![Pulpity nawigacyjne](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Dodawanie widgetów

Możesz kliknąć tytuł pulpitu nawigacyjnego, aby zobaczyć, jak to, ale w tej chwili jego jest pusta, ponieważ firma Microsoft nie dodano żadnych elementów widget. Element widget łatwy i przydatne typu, aby dodać do pulpitu nawigacyjnego są **szybkie wartości** wykresów, które zawierają listę wartości pola i ich dystrybucji.

1. Przejdź z powrotem do wyniki wyszukiwania danych wejściowych UDP, który otrzymuje dzienników przepływów, wybierając **wyszukiwania** z górnego paska nawigacyjnego.

2. W obszarze **wynik wyszukiwania** panelu po lewej stronie ekranu, Znajdź **pola** kartę, która wyświetla listę różnych pól Każdy przychodzący komunikat krotki przepływu.

3. Wybierz wszystkie żądane parametr w której ma zostać wizualizacji (w tym przykładzie jest wybrane źródło IP). Aby wyświetlić listę możliwych elementów widget, kliknij strzałkę listy rozwijanej niebieskim po lewej stronie pola, a następnie wybierz **szybkie wartości** można wygenerować elementu widget. Powinien zostać wyświetlony podobny do poniższej ilustracji:

   ![Źródłowy adres IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Z tego miejsca możesz wybrać **dodać do pulpitu nawigacyjnego** znajdujący się w prawym górnym rogu elementu widget i wybierz odpowiedni pulpit nawigacyjny, aby dodać.

5. Przejdź z powrotem do pulpitu nawigacyjnego, aby zobaczyć widżet, który właśnie został dodany.

   Szereg innych widżetów, takich jak histogramów i liczby można dodać do pulpitu nawigacyjnego, aby śledzić ważne metryki, takie jak przykładowy pulpit nawigacyjny, pokazane na poniższej ilustracji:

   ![Pulpit nawigacyjny Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Dodatkowo wyjaśnienie na pulpitach nawigacyjnych i innych rodzajów elementów widget, można znaleźć w z narzędzia Graylog firmy [dokumentacji](https://docs.graylog.org/en/2.2/pages/dashboards.html).

Dzięki integracji usługi Network Watcher z z narzędzia Graylog, masz są teraz wygodne i scentralizowany sposób wizualizowanie dzienniki przepływu sieciowych grup zabezpieczeń i zarządzanie nimi. Z narzędzia Graylog numerem inne zaawansowane funkcje, takie jak strumienie i alerty, które może również służyć do dalszego zarządzania dzienników przepływu i lepiej zrozumieć ruchu sieciowego. Teraz, gdy masz z narzędzia Graylog Konfigurowanie połączony z platformą Azure, możesz ją także zapoznaj się z innych funkcji, która zapewnia w dalszym ciągu.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wizualizować swoje dzienniki sieciowych grup zabezpieczeń usługi flow z usługą Power BI, odwiedzając [Visualize sieciowej grupy zabezpieczeń przepływy dzienników za pomocą usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

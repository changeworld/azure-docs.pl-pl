---
title: Analizowanie dzienników przepływu grupy zabezpieczeń sieci platformy Azure - Graylog | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać i analizować dzienniki przepływu grupy zabezpieczeń sieci na platformie Azure przy użyciu obserwatora sieciowego i Graylog.
services: network-watcher
documentationcenter: na
author: mareat
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
ms.openlocfilehash: 8d82ffa84c3d75ec3acd102a2de2bdce3718a995
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26639288"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Zarządzanie danymi i analizowanie dzienników przepływu grupy zabezpieczeń sieci na platformie Azure przy użyciu obserwatora sieciowego i Graylog

[Dzienniki przepływu grupy zabezpieczeń sieci](network-watcher-nsg-flow-logging-overview.md) Podaj informacje umożliwiające zrozumienie przychodzące i wychodzące ruchu IP dla interfejsów sieci platformy Azure. Dzienniki przepływu Pokaż przepływów wychodzącego i przychodzącego na na podstawie reguły grupy zabezpieczeń sieci, interfejsu sieciowego przepływ stosuje się do, 5-elementowej informacji (protokół źródłowego i docelowego adresu IP, portu źródłowego i docelowego) o przepływie, i jeśli ruch został dozwolony lub niedozwolony .

Przy włączonym rejestrowaniu przepływu, może mieć wielu grup zabezpieczeń sieci w sieci. Kilka grup zabezpieczeń sieci przy włączonym rejestrowaniu przepływu można tworzyć skomplikowane, aby przeanalizować i uzyskaj informacje z dzienników. Ten artykuł zawiera rozwiązanie centralnie zarządzać tych sieci grupy przepływu dzienniki zabezpieczeń przy użyciu Graylog, zarządzanie dziennikiem typu open source, a narzędzie do analizy i Logstash, typu open source potoku przetwarzania danych po stronie serwera.

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu grupy zabezpieczeń sieci są włączone, za pomocą Monitora sieci. Przepływu dzienniki przepływu w magazynie obiektów blob Azure. Dodatek Logstash służy do połączenia i przetwarzania przepływu dzienniki z magazynu obiektów blob i wysyłać je do Graylog. Gdy przepływ dzienniki są przechowywane w Graylog, mogą być przeanalizowane i wizualizowane w dostosowanych pulpitów nawigacyjnych.

! [Przepływu pracy Graylog]] (. / media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Etapy instalacji

### <a name="enable-network-security-group-flow-logging"></a>Włącz rejestrowanie przepływu grupy zabezpieczeń sieci

W tym scenariuszu musi mieć włączone na co najmniej jeden sieciowej grupy zabezpieczeń na Twoim koncie sieci zabezpieczeń grupy przepływu rejestrowanie. Instrukcje dotyczące włączania sieci dzienniki przepływu grupy zabezpieczeń, zapoznaj się z następującym artykułem [wprowadzenie do przepływu rejestrowania dla grup zabezpieczeń sieci](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Konfigurowanie Graylog

W tym przykładzie zarówno Graylog, jak i Logstash są skonfigurowane na serwerze Ubuntu 14.04, wdrożona na platformie Azure.

- Zapoznaj się [dokumentacji](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) z Graylog, aby uzyskać instrukcje krok po kroku dotyczące instalowania na Ubuntu.
- Upewnij się, że także skonfigurować interfejs sieci web Graylog wykonując [dokumentacji](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

W tym przykładzie użyto minimalnej konfiguracji Graylog (tj. pojedyncze wystąpienie Graylog), ale Graylog może być zaprojektowana skalowania między zasobami w zależności od używanego systemu i produkcji potrzeb. Aby uzyskać więcej informacji na architektury uwag ani głębokość architektury przewodnik, zobacz Graylog w [dokumentacji](http://docs.graylog.org/en/2.2/pages/architecture.html) i [przewodnik dotyczący architektury](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog można zainstalować na wiele sposobów, w zależności od platformy, a preferencje. Pełną listę metod instalacji można odwoływać się do urzędnika Graylog w [dokumentacji](http://docs.graylog.org/en/2.2/pages/installation.html). Graylog aplikacja serwera działa na dystrybucje systemu Linux i ma następujące wymagania wstępne:

-   Oracle Java SE 8 lub nowszym — [dokumentacja instalacji programu Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-   Elastyczne wyszukiwania 2.x (2.1.0 lub nowszym)- [Elasticsearch instalacji dokumentacji](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-   Bazy danych MongoDB 2.4 lub nowszym — [dokumentacją usługi MongoDB instalacji](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Zainstaluj Logstash

Logstash służy do spłaszczenia dzienniki przepływu formatu JSON do poziomu przepływu spójnej kolekcji. Spłaszczanie dzienniki przepływu ułatwia dzienniki wyszukiwania w Graylog i zorganizować.

1.  Aby zainstalować Logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2.  Skonfiguruj Logstash do analizowania dzienników przepływu i wysyłać je do Graylog. Utwórz plik Logstash.conf:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3.  Dodaj następującą zawartość do pliku. Zmień wyróżnione wartości do uwzględnienia informacji o koncie magazynu:

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
W pliku config Logstash podane składa się z trzech części: dane wejściowe, filtrów i danych wyjściowych. Źródło danych wejściowych dzienników, które przetwarzają Logstash wyznacza sekcji wejściowej — w takim przypadku zamierzamy Użyj Azure blog wejściowych wtyczki (zainstalowany w następnych krokach) umożliwiający dostęp do przepływ grupy zabezpieczeń sieci dzienniki JSON przechowywane w magazynie obiektów blob.

Sekcji filtra spłaszcza następnie każdego pliku dziennika przepływu tak, aby każda krotka poszczególnych przepływu i jego właściwości staje się oddzielne zdarzenie Logstash.

Na koniec sekcji Wyjście przekazuje każdego zdarzenia Logstash serwerowi Graylog. Do konkretnej musi, zmodyfikuj plik konfiguracji Logstash, zgodnie z wymaganiami.

   > [!NOTE]
   > Poprzedni plik konfiguracji przyjęto założenie, że serwer Graylog został skonfigurowany na adres IP hosta lokalnego sprzężenia zwrotnego 127.0.0.1. Jeśli nie, należy zmienić parametr hosta w sekcji Wyjście poprawny adres IP.

Aby uzyskać dalsze instrukcje na temat instalowania Logstash, zobacz Logstash [dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalowanie wtyczki dla magazynu obiektów blob Azure wprowadzania Logstash

Dodatek Logstash umożliwia bezpośredni dostęp do dzienników przepływu ze swojego konta magazynu wyznaczonego obiektu blob. Aby zainstalować wtyczkę, z domyślnego katalogu instalacji Logstash (w tym /usr/share/logstash/bin przypadków), uruchom następujące polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tej wtyczki w temacie [dokumentacji](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Skonfiguruj połączenie z Logstash do Graylog

Teraz, możemy ustanowić połączenie przy użyciu Logstash dzienniki przepływu i konfigurowanie serwera Graylog, należy skonfigurować Graylog do akceptowania przychodzących plików dziennika.

1.  Przejdź do serwera Graylog interfejsu sieci web przy użyciu adresu URL, skonfigurowane pod jego kątem. Dostęp do interfejsu, kierując przy użyciu przeglądarki`http://<graylog-server-ip>:9000/`

2.  Aby przejść do strony konfiguracji, wybierz **systemu** menu rozwijane w górnym menu nawigacyjnym pasek po prawej stronie, a następnie kliknij przycisk **dane wejściowe**.
    Alternatywnie przejdź do`http://<graylog-server-ip>:9000/system/inputs`

    ![Wprowadzenie](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3.  Aby uruchomić nowe dane wejściowe, wybierz *GELF UDP* w **wybierz dane wejściowe** listy rozwijanej, a następnie wypełnij formularz. GELF oznacza Graylog rozszerzony Format dziennika. GELF format opracowanego przez Graylog. Aby dowiedzieć się więcej na temat jego zalety, zobacz Graylog [dokumentacji](http://docs.graylog.org/en/2.2/pages/gelf.html).

    Pamiętaj powiązać dane wejściowe skonfigurowany serwer Graylog na adres IP. Adres IP powinien być zgodny **hosta** pola danych wyjściowych UDP Logstash pliku konfiguracji. Domyślny port powinien być *12201*. Upewnij się, port odpowiada **portu** pola w protokołu UDP output wymienionych w pliku konfiguracyjnym Logstash.

    ![Dane wejściowe](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

    Po uruchomieniu dane wejściowe powinny pojawić się on znajdować się pod **lokalne dane wejściowe** sekcji, jak pokazano na poniższej ilustracji:

    ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

    Aby dowiedzieć się więcej na temat Graylog komunikatów wejściowych, zajrzyj do [dokumentacji](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4.  Po tych konfiguracji zostały wprowadzone, możesz uruchomić Logstash ma rozpocząć się odczyt w dziennikach przepływu przy użyciu następującego polecenia:

    `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>Przeszukaj Graylog wiadomości

Po co pewien czas serwera Graylog zbierać komunikaty, będą mogli wyszukiwać wiadomości. Aby sprawdzić wiadomości wysyłane do serwera Graylog, z **dane wejściowe** strony konfiguracji, kliknij przycisk "**Pokaż odebranych komunikatów**" przycisk GELF UDP wejściowych utworzonego. Nastąpi przekierowanie do ekranu, która wygląda podobnie do poniższej ilustracji: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknięcie łącza niebieski "% {Message}" rozszerza każdy komunikat, aby wyświetlić parametry poszczególne krotki przepływu, jak pokazano na poniższej ilustracji:

![Komunikaty](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Domyślnie wszystkie pola wiadomości są umieszczane w wyszukiwania, jeśli nie zaznaczysz pola wiadomości do wyszukania. Jeśli chcesz wyszukiwanie określonych wiadomości (tj. — krotek przepływ z określonego źródłowy adres IP), można użyć języka zapytań wyszukiwania Graylog jako [udokumentowane](http://docs.graylog.org/en/2.2/pages/queries.html)


## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analizowanie sieci zabezpieczeń grupy przepływu dzienników przy użyciu Graylog

Teraz, kiedy Graylog go skonfigurowane systemem, możemy użyć niektóre jej funkcje aby lepiej zrozumieć przepływu danych dziennika. Jedną z metod jest za pomocą pulpitów nawigacyjnych można utworzyć konkretnych widoków danych.

### <a name="create-a-dashboard"></a>Utwórz pulpit nawigacyjny

1.  W górnym pasku nawigacyjnym, wybierz **pulpity nawigacyjne** lub przejdź do`http://<graylog-server-ip>:9000/dashboards/`

2.  Z tego miejsca, kliknij zielony **Utwórz pulpit nawigacyjny** przycisk i wypełnianie Krótka forma z tytuł i opis pulpitu nawigacyjnego. Trafienia **zapisać** przycisk, aby utworzyć nowy pulpit nawigacyjny. Zostanie wyświetlony pulpit nawigacyjny podobnie do poniższej ilustracji:

    ![Pulpity nawigacyjne](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Dodaj elementy widget

Możesz kliknąć tytuł jest widoczny, ale w tej chwili jego puste, ponieważ nie dodano żadnych elementów widget pulpitu nawigacyjnego. Widżet typu łatwe i przydatne do dodania do pulpitu nawigacyjnego są **szybkie wartości** wykresy, które zawierają listę wartości pola i ich dystrybucji.

1.  Przejdź z powrotem do wyniki wyszukiwania danych wejściowych UDP, która odbiera dzienniki przepływu wybierając **wyszukiwania** z górnym pasku nawigacyjnym.

2.  W obszarze **wynik wyszukiwania** panelu po lewej stronie ekranu, Znajdź **pola** kartę, która zawiera różne pola każdej wiadomości przychodzącej krotki przepływu.

3.  Wybierz wszystkie żądany parametr, w którym do wizualizacji (w tym przykładzie Wybraliśmy źródłowy adres IP). Aby wyświetlić listę możliwych elementy widget, kliknij niebieską strzałką listy rozwijanej w lewo pola, a następnie wybierz **szybkie wartości** do generowania elementu widget. Powinny pojawić się podobnie do poniższej ilustracji:

    ![Źródłowy adres IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4.  Z tego miejsca, można wybrać **dodawać do pulpitu nawigacyjnego** w prawym górnym rogu elementu widget i wybrać odpowiedni pulpitu nawigacyjnego do dodania.

5.  Przejdź z powrotem do pulpitu nawigacyjnego, aby zobaczyć widget, który właśnie został dodany.

    Różne inne elementy widget, takie jak histogramów i liczby można dodawać do pulpitu nawigacyjnego do śledzenia ważnych metryk, takich jak przedstawiono na rysunku poniżej przykładowy pulpit nawigacyjny:

    ![Pulpit nawigacyjny Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Dodatkowo wyjaśnienie na pulpitach nawigacyjnych i innych typów elementów widget można znaleźć w Graylog w [dokumentacji](http://docs.graylog.org/en/2.2/pages/dashboards.html).

Integrowanie obserwatora sieciowego z Graylog, teraz zostały sposób wygodne i scentralizowane zarządzanie i wizualizacji dzienniki przepływu grupy zabezpieczeń sieci. Graylog ma liczbę inne zaawansowane funkcje, takie jak strumienie i alertów, które mogą służyć do dalszego zarządzania dziennikami przepływu i Poznaj lepiej ruchu sieciowego. Teraz, gdy masz Graylog skonfigurować i podłączone do platformy Azure, możesz także kontynuować do eksplorowania innych funkcji, która zapewnia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizacji dzienniki przepływu grupy zabezpieczeń sieci przy użyciu usługi Power BI, odwiedzając [wizualizacja sieciowej grupy zabezpieczeń przepływa dzienników przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

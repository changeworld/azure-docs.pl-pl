---
title: Analizowanie dzienników przepływu grupy zabezpieczeń sieci platformy Azure — Graylog | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać dziennikami przepływu grupy zabezpieczeń sieci i analizować je na platformie Azure przy użyciu funkcji Kontrola sieci i Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 1e597a81967a8fb6be2959d53e65ad01135e5e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842907"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Zarządzanie dziennikami przepływu grupy zabezpieczeń sieciowych i analizowanie ich na platformie Azure przy użyciu funkcji Kontrola sieci i graylog

[Dzienniki przepływu grupy zabezpieczeń sieciowych](network-watcher-nsg-flow-logging-overview.md) zawierają informacje, których można użyć do zrozumienia ruchu ip ruchu przychodzącego i wychodzącego dla interfejsów sieciowych platformy Azure. Dzienniki przepływu pokazują przepływy wychodzące i przychodzące na podstawie reguły grupy zabezpieczeń sieci, interfejs sieciowy, do którym ma zastosowanie przepływ, 5-krotka informacji (adres IP źródła/miejsca docelowego, port źródłowy/docelowy, protokół) o przepływie i czy ruch był dozwolony lub odrzucany.

W sieci może być dostępnych wiele grup zabezpieczeń sieciowych z włączonym rejestrowaniem przepływu. Kilka sieciowych grup zabezpieczeń z włączonym rejestrowaniem przepływu może sprawić, że analizowanie i uzyskiwanie szczegółowych informacji z dzienników jest uciążliwe. Ten artykuł zawiera rozwiązanie do centralnego zarządzania tymi dziennikami przepływu grupy zabezpieczeń sieci przy użyciu Graylog, narzędzia do zarządzania i analizy dziennika open source oraz Logstash, potoku przetwarzania danych po stronie serwera open source.

> [!Warning]
> Poniższe kroki działają z dziennikami przepływu w wersji 1. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieci .](network-watcher-nsg-flow-logging-overview.md) Poniższe instrukcje nie będą działać z wersją 2 plików dziennika, bez modyfikacji.

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu sieciowej grupy zabezpieczeń są włączone przy użyciu funkcji Kontrola sieci. Przepływ dzienników przepływu do magazynu obiektów blob platformy Azure. Wtyczka Logstash służy do łączenia i przetwarzania dzienników przepływu z magazynu obiektów blob i wysyłania ich do Graylog. Gdy dzienniki przepływu są przechowywane w Graylog, mogą być analizowane i wizualizowane w niestandardowych pulpitach nawigacyjnych.

![Przepływ pracy graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Etapy instalacji

### <a name="enable-network-security-group-flow-logging"></a>Włączanie rejestrowania przepływu sieciowej grupy zabezpieczeń

W tym scenariuszu musi być włączone rejestrowanie przepływu sieciowej grupy zabezpieczeń na co najmniej jednej sieciowej grupie zabezpieczeń na koncie. Instrukcje dotyczące włączania dzienników przepływu sieciowej grupy zabezpieczeń można znaleźć w artykule [Wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Konfigurowanie graylog

W tym przykładzie graylog i Logstash są skonfigurowane na serwerze Ubuntu 14.04, wdrożony na platformie Azure.

- Zapoznaj się z [dokumentacją](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) graylog, aby krok po kroku instrukcje dotyczące instalacji na Ubuntu.
- Upewnij się również, aby skonfigurować interfejs internetowy Graylog, postępuje zgodnie z [dokumentacją](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

W tym przykładzie użyto minimalnej konfiguracji Graylog (tj. jedno wystąpienie Graylog), ale Graylog można zaprojektować do skalowania zasobów w zależności od potrzeb systemu i produkcji. Aby uzyskać więcej informacji na temat zagadnień architektonicznych lub głębokiego przewodnika architektonicznego, zobacz [dokumentację](https://docs.graylog.org/en/2.2/pages/architecture.html) grayloga i [przewodnik architektoniczny](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog może być zainstalowany na wiele sposobów, w zależności od platformy i preferencji. Pełna lista możliwych metod instalacji znajduje się w oficjalnej [dokumentacji](https://docs.graylog.org/en/2.2/pages/installation.html)Grayloga. Aplikacja serwera Graylog działa w dystrybucjach systemu Linux i ma następujące wymagania wstępne:

-  Java SE 8 lub nowsza — [dokumentacja JDK platformy Azul Azure](https://aka.ms/azure-jdks)
-  Elastic Search 2.x (2.1.0 lub nowsze) - [Dokumentacja instalacji Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 lub nowsze – [dokumentacja instalacyjna MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalowanie programu Logstash

Logstash służy do spłaszczenia dzienników przepływu sformatowany JSON do poziomu krotki przepływu. Spłaszczenie dzienników przepływu ułatwia organizowanie i wyszukiwanie dzienników w Graylog.

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Skonfiguruj program Logstash do analizowania dzienników przepływu i wysyłania ich do grayloga. Utwórz plik Logstash.conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Dodaj do pliku następującą zawartość. Zmień wyróżnione wartości, aby odzwierciedlić szczegóły konta magazynu:

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
   Dostarczony plik konfiguracyjny Logstash składa się z trzech części: wejściowego, filtru i wyjścia. Sekcja wprowadzania określa źródło wejściowe dzienników, które logstash będzie przetwarzać — w tym przypadku zamierzasz użyć wtyczki wprowadzania blogu platformy Azure (zainstalowanej w następnych krokach), która umożliwia nam dostęp do plików JSON dziennika przepływu grupy zabezpieczeń sieciowych przechowywanych w magazynie obiektów blob.

Sekcja filtru następnie spłaszcza każdy plik dziennika przepływu, tak aby każda krotka przepływu i skojarzone z nim właściwości stają się osobnym zdarzeniem Logstash.

Na koniec sekcja danych wyjściowych przekazuje każde zdarzenie Logstash do serwera Graylog. Aby dostosować się do konkretnych potrzeb, zmodyfikuj plik konfiguracyjny Logstash w razie potrzeby.

   > [!NOTE]
   > Poprzedni plik konfiguracyjny zakłada, że serwer Graylog został skonfigurowany na adres IP sprzężenia zwrotnego lokalnego hosta 127.0.0.1. Jeśli nie, należy zmienić parametr hosta w sekcji danych wyjściowych na poprawny adres IP.

Dalsze instrukcje dotyczące instalacji programu Logstash można znaleźć w [dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)programu Logstash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalowanie wtyczki wejściowej Logstash dla magazynu obiektów blob platformy Azure

Wtyczka Logstash umożliwia bezpośredni dostęp do dzienników przepływu z ich wyznaczonego konta magazynu obiektów blob. Aby zainstalować wtyczkę, z domyślnego katalogu instalacyjnego Logstash (w tym przypadku /usr/share/logstash/bin) uruchom następujące polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tej wtyczki, zobacz [dokumentację](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Konfigurowanie połączenia z logstash do graylog

Teraz, gdy nawiązałeś połączenie z dziennikami przepływu za pomocą logstash i skonfigurowałeś serwer Graylog, musisz skonfigurować Graylog tak, aby akceptował przychodzące pliki dziennika.

1. Przejdź do interfejsu internetowego serwera Graylog przy użyciu adresu URL skonfigurowany dla niego. Dostęp do interfejsu można uzyskać, kierując przeglądarkę do`http://<graylog-server-ip>:9000/`

2. Aby przejść do strony konfiguracji, wybierz menu rozwijane **System** na górnym pasku nawigacyjnym po prawej stronie, a następnie kliknij pozycję **Wejścia**.
   Alternatywnie, przejdź do`http://<graylog-server-ip>:9000/system/inputs`

   ![Wprowadzenie](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Aby uruchomić nowe wejście, wybierz *GELF UDP* z listy rozwijanej **Wybierz wejście,** a następnie wypełnij formularz. GELF to skrót od Graylog Extended Log Format . Format GELF jest rozwijany przez Graylog. Aby dowiedzieć się więcej o jego zaletach, zobacz [dokumentację](https://docs.graylog.org/en/2.2/pages/gelf.html)Graylog .

   Upewnij się, że dane wejściowe są powiązane z adresem IP skonfigurowanym serwerem Graylog. Adres IP powinien być zgodny z **polem hosta** danych wyjściowych UDP pliku konfiguracyjnego Logstash. Domyślny port powinien być *12201*. Upewnij się, że port jest zgodny z polem **portu** w danych wyjściowych UDP wyznaczonych w pliku konfiguracyjnym Logstash.

   ![Dane wejściowe](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Po uruchomieniu danych wejściowych powinno ono pojawić się w sekcji **Lokalne wejścia,** jak pokazano na poniższym rysunku:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Aby dowiedzieć się więcej o wejściach komunikatów Graylog, zapoznaj się z [dokumentacją](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Po dokonaniu tych konfiguracji można uruchomić program Logstash, aby rozpocząć czytanie `sudo systemctl start logstash.service`w dziennikach przepływu za pomocą następującego polecenia: .

### <a name="search-through-graylog-messages"></a>Wyszukiwanie wiadomości Graylog

Po połdzą trochę czasu na serwer Graylog do zbierania wiadomości, można przeszukiwać wiadomości. Aby sprawdzić wiadomości wysyłane do serwera Graylog, na stronie konfiguracji Wejścia kliknij przycisk **"Pokaż odebrane wiadomości"** utworzonego wejścia GELF UDP. **Inputs** Zostaniesz przekierowany do ekranu, który wygląda podobnie do następującego obrazu: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknięcie niebieskiego łącza "%{Message}" powoduje rozwinięcie każdej wiadomości, aby wyświetlić parametry każdej krotki przepływu, jak pokazano na poniższym rysunku:

![Komunikaty](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Domyślnie wszystkie pola wiadomości są uwzględniane w wyszukiwaniu, jeśli nie wybierzesz określonego pola wiadomości do wyszukania. Jeśli chcesz wyszukać konkretne wiadomości (tj. – krotki przepływu z określonego źródłowego adresu IP) można użyć języka zapytania wyszukiwania Graylog jako [udokumentowanego](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analizowanie dzienników przepływu grupy zabezpieczeń sieci przy użyciu graylog

Teraz, gdy Graylog skonfigurować uruchomiony, można użyć niektórych jego funkcji, aby lepiej zrozumieć dane dziennika przepływu. Jednym z takich sposobów jest użycie pulpitów nawigacyjnych do tworzenia określonych widoków danych.

### <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

1. Na górnym pasku nawigacyjnym wybierz pozycję **Pulpity nawigacyjne** lub przejdź do`http://<graylog-server-ip>:9000/dashboards/`

2. Następnie kliknij zielony przycisk **Utwórz pulpit nawigacyjny** i wypełnij krótki formularz tytułem i opisem pulpitu nawigacyjnego. Naciśnij przycisk **Zapisz,** aby utworzyć nowy pulpit nawigacyjny. Następuje pulpit nawigacyjny podobny do następującego obrazu:

    ![Pulpity nawigacyjne](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Dodawanie widżetów

Możesz kliknąć tytuł pulpitu nawigacyjnego, aby go zobaczyć, ale teraz jest pusty, ponieważ nie dodaliśmy żadnych widżetów. Łatwym i użytecznym widżetem typu do dodania do pulpitu nawigacyjnego są wykresy **szybkich wartości,** na których wyświetlana jest lista wartości wybranego pola i ich rozkład.

1. Przejdź z powrotem do wyników wyszukiwania danych wejściowych UDP, które odbiera dzienniki przepływu, wybierając **wyszukiwanie** z górnego paska nawigacyjnego.

2. W panelu **Wyniki wyszukiwania** po lewej stronie ekranu znajdź kartę **Pola,** na której znajdują się różne pola każdej wiadomości z krotki przepływu przychodzącego.

3. Wybierz dowolny żądany parametr, w którym ma zostać wizualizowanych (w tym przykładzie jest zaznaczone źródło IP). Aby wyświetlić listę możliwych widżetów, kliknij niebieską strzałkę rozwijaną po lewej stronie pola, a następnie wybierz **szybkie wartości,** aby wygenerować widżet. Powinieneś zobaczyć coś podobnego do następującego obrazu:

   ![Źródłowy adres IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. W prawym górnym rogu widżetu możesz wybrać przycisk **Dodaj do pulpitu nawigacyjnego** i wybrać odpowiedni pulpit nawigacyjny do dodania.

5. Przejdź z powrotem do pulpitu nawigacyjnego, aby zobaczyć właśnie dodany widżet.

   Możesz dodać wiele innych widżetów, takich jak histogramy i liczby do pulpitu nawigacyjnego, aby śledzić ważne dane, takie jak przykładowy pulpit nawigacyjny pokazany na poniższym obrazie:

   ![Pulpit nawigacyjny dzienników przepływu](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Więcej informacji na temat pulpitów nawigacyjnych i innych typów widżetów można znaleźć w [dokumentacji](https://docs.graylog.org/en/2.2/pages/dashboards.html)Grayloga.

Dzięki integracji obserwatora sieci z graylogem masz teraz wygodny i scentralizowany sposób zarządzania dziennikami przepływu sieciowej grupy zabezpieczeń i wizualizowania ich. Graylog ma wiele innych zaawansowanych funkcji, takich jak strumienie i alerty, które mogą być również używane do dalszego zarządzania dziennikami przepływu i lepszego zrozumienia ruchu sieciowego. Teraz, gdy masz Graylog skonfigurować i połączone z platformą Azure, możesz nadal eksplorować inne funkcje, które oferuje.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą usługi Power BI, odwiedzając [dzienniki przepływów grup zabezpieczeń wizualizuj sieciowe za pomocą usługi Power BI.](network-watcher-visualize-nsg-flow-logs-power-bi.md)

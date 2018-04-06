---
title: Wykonaj włamań sieci z obserwatora sieciowego Azure i narzędzi typu open source | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób używania obserwatora sieciowego Azure i otworzyć narzędzia źródła do wykonywania wykrywania nieautoryzowanego dostępu sieciowego
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: aff1b5f9e8860d3b8dc09b37684bb8a4ac2bf134
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Wykonaj włamań sieci z obserwatora sieciowego i narzędzi typu open source

Pakiet przechwyconych obrazów są kluczowym elementem za zaimplementowanie systemów wykrywania nieautoryzowanego dostępu sieciowego (ID) i przeprowadzanie monitorowania zabezpieczeń sieci (NSM). Istnieje kilka narzędzi identyfikatorów typu open source, które przetwarzają przechwytywania pakietów i poszukaj podpisami sieci atakami złośliwych działań. Za pomocą pakietu znajdują się pod warunkiem przez obserwatora sieciowego można analizować wtargnięcia szkodliwe lub luk w zabezpieczeniach sieci.

Jednym takich narzędzi typu open source jest Suricata, aparat Identyfikatory, który używa zestawów reguł do monitorowania ruchu sieciowego i wyzwala alerty, gdy występują podejrzane zdarzenia. Suricata oferuje wielowątkowych aparatu, co oznacza, że można wykonać analizy ruchu sieciowego z zwiększenie szybkości i wydajności. Aby uzyskać więcej informacji na temat Suricata i jego możliwości witrynie ich w https://suricata-ids.org/.

## <a name="scenario"></a>Scenariusz

W tym artykule opisano sposób konfigurowania środowiska w celu wykonania wykrywania nieautoryzowanego dostępu sieciowego przy użyciu obserwatora sieciowego, Suricata i elastyczny stosu. Obserwatora sieciowego umożliwia przechwytywanie pakietów używane w celu wykonania wykrywania nieautoryzowanego dostępu w sieci. Suricata przetwarza przechwytywania pakietów i wyzwalacza alerty oparte na pakietów, które spełniają jego dany zestaw reguł zagrożeń. Te alerty są przechowywane w pliku dziennika na komputerze lokalnym. Przy użyciu elastycznej stosu, dzienniki generowane przez Suricata może być indeksowana i pozwala utworzyć pulpit nawigacyjny Kibana, zapewniając wizualną reprezentację dzienniki i sposób, aby szybko uzyskać wgląd do potencjalnych luk w zabezpieczeniach sieci.  

![Scenariusz aplikacji sieci web proste][1]

Zarówno narzędzi typu open source można skonfigurować na maszynie Wirtualnej platformy Azure, co umożliwia wykonywanie tej analizy w ramach własnego środowiska sieci platformy Azure.

## <a name="steps"></a>Kroki

### <a name="install-suricata"></a>Zainstaluj Suricata

Dla wszystkich innych metod instalacji odwiedź http://suricata.readthedocs.io/en/latest/install.html

1. Z poziomu wiersza polecenia terminala z maszyną Wirtualną, uruchom następujące polecenia:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Aby zweryfikować instalację, uruchom polecenie `suricata -h` aby zobaczyć pełną listę poleceń.

### <a name="download-the-emerging-threats-ruleset"></a>Pobierz zestaw reguł nowych zagrożeń

Na tym etapie nie ma żadnych reguł dla Suricata do uruchomienia. Można utworzyć własne reguły, jeśli są określone zagrożenia do sieci, które chcesz wykryć, lub możesz również Użyj rozwinięte zestawy reguł z wielu dostawców, takie jak zagrożenia pojawiające się lub reguły VRT z Snort. Ogólnie dostępne ruleset zagrożenia pojawiające się używana tutaj:

Pobierz zestaw reguł i skopiuj je do tego katalogu:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Przechwytywanie pakietów procesu z Suricata

Przetwarzania pakietu znajdują się za pomocą Suricata, uruchom następujące polecenie:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Aby sprawdzić alerty wynikowy, odczytać pliku fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Konfigurowanie elastycznej stosu

Podczas dzienniki, które tworzy Suricata zawierają cenne informacje o tym, co dzieje się w naszej sieci, te pliki dziennika nie są najłatwiejsze do przeczytane i zrozumiane. Łącząc Suricata stosu elastycznych, możemy utworzyć pulpit nawigacyjny Kibana co pozwala wyszukiwania, wykresy, analizowanie i pochodzi z naszych dzienników szczegółowych informacji.

#### <a name="install-elasticsearch"></a>Zainstaluj Elasticsearch

1. Elastyczne stosu w wersji 5.0 i nowszych wymaga Java 8. Uruchom polecenie `java -version` do sprawdź wersję. Jeśli nie masz java instalacji, zapoznaj się z dokumentacją na [witryny sieci Web programu Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Pobierz poprawny pakiet binarnych systemu:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Inne metody instalacji można znaleźć w folderze [Elasticsearch instalacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Sprawdź, czy Elasticsearch działa przy użyciu polecenia:

    ```
    curl http://127.0.0.1:9200
    ```

    Powinna zostać wyświetlona odpowiedź podobną do poniższego:

    ```
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

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Należy skonfigurować Logstash można odczytać z danych wyjściowych pliku eve.json dalej. Tworzenie pliku logstash.conf przy użyciu:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Dodaj następującą zawartość do pliku (Upewnij się, że ścieżka do pliku eve.json jest poprawna):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Upewnij się udzielić odpowiednich uprawnień do pliku eve.json tak, aby Logstash może obsługiwać pliku.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Aby uruchomić Logstash, uruchom polecenie:

    ```
    sudo /etc/init.d/logstash start
    ```

Aby uzyskać dalsze instrukcje na temat instalowania Logstash odwoływać się do [oficjalnej dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Zainstaluj Kibana

1. Uruchom następujące polecenia, aby zainstalować Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Aby uruchomić Kibana należy użyć polecenia:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Aby wyświetlić Kibana interfejsu sieci web, przejdź do `http://localhost:5601`
1. W tym scenariuszu jest używane dla dzienników Suricata wzorzec indeksu "logstash-*"

1. Jeśli chcesz wyświetlić pulpit nawigacyjny Kibana zdalnie, Utwórz regułę ruchu przychodzącego grupy NSG zezwalania na dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Utwórz pulpit nawigacyjny Kibana

W tym artykule firma Microsoft umieściła przykładowy pulpit nawigacyjny do wyświetlania trendów i informacji w alerty.

1. Pobierz plik pulpitu nawigacyjnego [tutaj](https://aka.ms/networkwatchersuricatadashboard), plik wizualizacji [tutaj](https://aka.ms/networkwatchersuricatavisualization), a plikiem zapisanego wyszukiwania [tutaj](https://aka.ms/networkwatchersuricatasavedsearch).

1. W obszarze **zarządzania** kartę z Kibana, przejdź do **zapisane obiektów** i zaimportować wszystkie trzy pliki. Następnie z **pulpitu nawigacyjnego** kartę można otwierać i załadować przykładowy pulpit nawigacyjny.

Można również utworzyć własne wizualizacje i pulpity nawigacyjne dostosowane do metryki użytkownika. Przeczytaj więcej na temat tworzenia wizualizacje Kibana z jego Kibana [oficjalnej dokumentacji](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![pulpit nawigacyjny kibana][2]

### <a name="visualize-ids-alert-logs"></a>Wizualizuj dzienniki alertu Identyfikatory

Przykładowy pulpit nawigacyjny udostępnia kilka wizualizacje Suricata dzienniki alertu:

1. Alerty według GeoIP — mapa przedstawiająca dystrybucji alertów według kraju pochodzenia na podstawie lokalizacji geograficznej (według adresu IP)

    ![Geo ip][3]

1. Pierwszych 10 alertów — podsumowanie 10 najczęściej alertów wyzwalanych i ich opisy. Kliknięcie przycisku alert indywidualny filtruje dół pulpitu nawigacyjnego do informacji dotyczących tego określony alert.

    ![Obraz 4][4]

1. Liczba alertów — łączna liczba alertów wyzwalanych przez zestaw reguł

    ![image 5][5]

1. Wyzwolone na najwyższym 20 lokalizacja źródłowa/docelowa adresy IP/porty — wykresy kołowe przedstawiający górnego 20 adresów IP i portów tego alerty. Można filtrować są inicjowane w dół na określone adresy IP/porty, aby wyświetlić liczbę i typy alertów.

    ![Obraz 6][6]

1. Podsumowanie alertu — tabelę podsumowania szczegółowe informacje dotyczące każdego alert indywidualny. Można dostosować tej tabeli, aby wyświetlić inne parametry istotnych dla każdego alertu.

    ![Obraz 7][7]

Aby uzyskać więcej dokumentacji na temat tworzenia niestandardowych wizualizacje i pulpity nawigacyjne, zobacz [Kibana w oficjalnej dokumentacji](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Podsumowanie

Dzięki połączeniu pakietu przechwytuje dostarczane przez obserwatora sieciowego oraz narzędzia identyfikatorów typu open source, takie jak Suricata, można wykonać wykrywania nieautoryzowanego dostępu sieciowego dla szerokiego zakresu zagrożeń. Te pulpity nawigacyjne umożliwiają szybkie wychwycenia trendów i anomalii w sieci, jak dobrze dig do danych, aby dowiedzieć się, że główne przyczyny alertów, takie jak agentów złośliwy użytkownik lub narażone portów. Z tym wyodrębnione dane można podejmowania świadomych decyzji na temat reagowania na ochronę sieci przed atakami wszelkie szkodliwe nieautoryzowanego dostępu i tworzyć reguły uniemożliwia przyszłych dostęp do sieci.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak można wyzwolić przechwytywania pakietów, na podstawie alertów, odwiedzając [celu sieci aktywnego monitorowania za pomocą usługi Azure Functions Użyj przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, jak wizualizacji NSG dzienników przepływu z usługi Power BI, odwiedzając [wizualizacji NSG przepływa dzienników przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png

---
title: Wykrywanie włamań w sieci za pomocą usługi Azure Network Watcher i narzędzi typu open source | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia usługi Azure Network Watcher i narzędzia open source do wykonania wykrywanie włamań w sieci
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: b5dc885611c6654c1dc1d236e41ed75ef2717cc1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65605705"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Wykrywanie włamań w sieci przy użyciu usługi Network Watcher i narzędzi typu open source

Przechwytywanie pakietów są kluczowym elementem wykonania systemy wykrywania włamań sieci (ID) oraz przeprowadzania monitorowania zabezpieczeń sieci (NSM). Dostępnych jest kilka narzędzi Identyfikatory "open source", które przetwarzają przechwytywania pakietów i poszukaj sygnatur ewentualny włamań i złośliwych działań. Za pomocą pakietu przechwytuje dostarczone przez usługę Network Watcher można analizować sieci pod kątem wszelkie szkodliwe włamań i luk w zabezpieczeniach.

Jednego takiego narzędzia typu open source jest Suricata, aparat identyfikatorów, który używa zestawów reguł do monitorowania ruchu sieciowego i wyzwalania alertów, zawsze wtedy, gdy występują podejrzane zdarzenia. Suricata oferuje aparatu wielowątkowych, co oznacza, że można wykonywać analiza ruchu w sieci z większą szybkość i wydajność. Aby uzyskać więcej informacji na temat Suricata i jego możliwości, odwiedź witrynę tej organizacji w https://suricata-ids.org/.

## <a name="scenario"></a>Scenariusz

W tym artykule wyjaśniono, jak skonfigurować środowisko do wykonywania wykrywanie włamań w sieci przy użyciu usługi Network Watcher, Suricata i Elastic Stack. Usługa Network Watcher umożliwia przechwytywania pakietów, używany do wykonywania wykrywanie włamań w sieci. Suricata przetwarza przechwytywania pakietów i wyzwalania alertów na podstawie pakietów, które odpowiadają jego danego zestawu reguł zagrożeń. Te alerty są przechowywane w pliku dziennika na komputerze lokalnym. Za pomocą programu Elastic Stack, dzienniki generowane przez Suricata może być indeksowana i użyty do utworzenia pulpitu nawigacyjnego Kibana, zapewniając wizualnej reprezentacji dzienniki i oznacza, że szybkie wyciąganie wniosków na potencjalnych luk w zabezpieczeniach sieci.  

![Scenariusz aplikacji prostą][1]

Oba narzędzia typu open source można skonfigurować na Maszynie wirtualnej platformy Azure, co umożliwia testowanie tej analizy w ramach własnego środowiska sieciowego platformy Azure.

## <a name="steps"></a>Kroki

### <a name="install-suricata"></a>Zainstaluj Suricata

Aby uzyskać wszystkie inne metody instalacji odwiedź stronę https://suricata.readthedocs.io/en/latest/install.html

1. W terminalu wiersza polecenia maszyny wirtualnej, uruchom następujące polecenia:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Aby zweryfikować instalację, uruchom polecenie `suricata -h` aby zobaczyć pełną listę poleceń.

### <a name="download-the-emerging-threats-ruleset"></a>Pobierz zestaw reguł pojawiających się zagrożeń

Na tym etapie nie mamy żadnych reguł dla Suricata do uruchomienia. Można utworzyć własne reguły, jeśli istnieją określone zagrożeń dla sieci, które powinny być dostępne do wykrywania, lub można również opracowane zestawów reguł z wielu dostawców, takich jak pojawiających się zagrożeń lub reguły VRT z Snort. Ogólnie dostępne ruleset pojawiających się zagrożeń używamy tutaj:

Pobierz zestaw reguł i skopiować je do katalogu:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Przechwytywanie pakietów procesu przy użyciu Suricata

Przetwarzania pakietu przechwytuje przy użyciu Suricata, uruchom następujące polecenie:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Aby sprawdzić, wynikowe alerty, odczytać pliku fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Konfigurowanie programu Elastic Stack

Gdy dzienników, które generuje Suricata zawierają cenne informacje o tym, co dzieje się na naszej sieci, te pliki dziennika są najłatwiejsze do odczytania i zrozumienia. Łącząc Suricata za pomocą programu Elastic Stack, możemy utworzyć pulpitu nawigacyjnego Kibana co pozwala na wyszukiwanie, wykres, analizowanie i pobieraj szczegółowe informacje z naszych dzienników.

#### <a name="install-elasticsearch"></a>Instalowanie usługi Elasticsearch

1. Elastic Stack w wersji 5.0 lub nowszym wymaga Java 8. Uruchom polecenie `java -version` Aby sprawdzić swoją wersję. Jeśli nie masz zainstalowane w języku java, zajrzyj do dokumentacji na [JDK Azure suppored](https://aka.ms/azure-jdks).

1. Pobierz poprawny pakiet binarne w systemie:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Inne metody instalacji znajduje się w temacie [instalacji usługi Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Sprawdź, czy usługa Elasticsearch działa przy użyciu polecenia:

    ```
    curl http://127.0.0.1:9200
    ```

    Powinny pojawić się odpowiedź podobna do poniższego:

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

### <a name="install-logstash"></a>Instalowanie programu Logstash

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Następnie należy skonfigurować Logstash w celu odczytu z pliku eve.json danych wyjściowych. Tworzenie pliku logstash.conf przy użyciu:

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

1. Pamiętaj przekazać odpowiednie uprawnienia do pliku eve.json Logstash umożliwia pobieranie pliku.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Aby uruchomić program Logstash, uruchom polecenie:

    ```
    sudo /etc/init.d/logstash start
    ```

Aby uzyskać dalsze instrukcje na temat instalowania programu Logstash, zapoznaj się [oficjalnych dokumentów](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Zainstaluj program Kibana

1. Uruchom następujące polecenia, aby zainstalować program Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Aby uruchomić program Kibana, użyj polecenia:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Aby wyświetlić Kibana interfejsu sieci web, przejdź do `http://localhost:5601`
1. W tym scenariuszu jest używane dla dzienników Suricata wzorzec indeksu "logstash-*"

1. Jeśli użytkownik chce zdalnie wyświetlić pulpitu nawigacyjnego Kibana, Utwórz regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego umożliwia dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Kibana

W tym artykule udostępniliśmy przykładowy pulpit nawigacyjny do wyświetlania trendów i szczegółowe informacje w alerty.

1. Pobierz plik pulpitu nawigacyjnego [tutaj](https://aka.ms/networkwatchersuricatadashboard), plik wizualizacji [tutaj](https://aka.ms/networkwatchersuricatavisualization)i pliku zapisanego kryterium wyszukiwania [tutaj](https://aka.ms/networkwatchersuricatasavedsearch).

1. W obszarze **zarządzania** kartę programu Kibana, przejdź do **zapisywane obiekty** i zaimportować wszystkie trzy pliki. Następnie z **pulpit nawigacyjny** karcie, można otworzyć i załadować przykładowy pulpit nawigacyjny.

Można również utworzyć własne wizualizacje i pulpity nawigacyjne, dostosowanych do własnych istotne metryki. Przeczytaj więcej na temat tworzenia wizualizacji Kibana z firmy Kibana [oficjalną dokumentacją](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![pulpitu nawigacyjnego kibana][2]

### <a name="visualize-ids-alert-logs"></a>Wizualizowanie dzienników alertu Identyfikatory

Przykładowy pulpit nawigacyjny zawiera kilka wizualizacji Suricata alertu dzienników:

1. Alerty według GeoIP — mapa przedstawiająca dystrybucji alertów według ich kraju/regionu origin na podstawie lokalizacji geograficznej (według adresu IP)

    ![geograficzna ip][3]

1. Top 10 alertów — podsumowanie 10 najczęstsze alerty wyzwalane i ich opisy. Kliknij alert indywidualny filtrów w dół do pulpitu nawigacyjnego, aby informacje dotyczące tego określonego alertu.

    ![Obraz 4][4]

1. Liczba alertów — łączna liczba alertów wyzwalanych przez zestaw reguł

    ![image 5][5]

1. Najważniejsze 20 lokalizacja źródłowa/docelowa adresy IP/porty — wykresy kołowe przedstawiający górnego 20 adresów IP i portów tego alerty wyzwolone na. Można filtrować w dół na określone adresy IP/porty, aby wyświetlić liczbę i typy alertów są wyzwalane.

    ![Obraz 6][6]

1. Podsumowanie alertu — tabelę zawierającą podsumowanie szczegółowe informacje o poszczególnych poszczególnych alertów. Można dostosować tej tabeli, aby wyświetlić inne parametry znaczenie w odniesieniu do każdego alertu.

    ![Obraz 7][7]

Aby uzyskać więcej dokumentacji na temat tworzenia niestandardowych wizualizacji i pulpitów nawigacyjnych, zobacz [oficjalnych dokumentów firmy Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Podsumowanie

Łącząc pakiet przechwytuje dostarczone przez usługi Network Watcher i narzędzi identyfikatorów typu open source, takich jak Suricata, można wykonać wykrywanie włamań w sieci dla szerokiej gamy zagrożenia. Te pulpity nawigacyjne pozwalają szybko wychwycić trendów i anomalii w ramach sieci, jak dobrze Postaraj się do danych, aby dowiedzieć się, że główne przyczyny alertów, np. agentów złośliwy użytkownik lub narażone porty. Za pomocą tych wyodrębnionych danych można podejmuj świadome decyzje na temat reagowania na chronić sieć przed atakami wszelkie szkodliwe nieautoryzowanego dostępu i utworzyć reguły, aby uniemożliwić przyszłe włamań do sieci.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wyzwalać Przechwytywanie pakietów na podstawie alertów, odwiedzając [używanie przechwytywania pakietów do proaktywnego monitorowania sieci w środowisku Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, jak wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń z usługą Power BI, odwiedzając [wizualizacji przepływów sieciowych grup zabezpieczeń dzienników za pomocą usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png

---
title: Wykrywanie włamań do sieci za pomocą narzędzi open source
titleSuffix: Azure Network Watcher
description: W tym artykule opisano, jak używać usługi Azure Network Watcher i narzędzi open source do wykrywania włamań do sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1bd823d94552d1e920b367b6576b0e3bb74aefb2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474931"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Wykrywanie włamań do sieci za pomocą obserwatora sieciowego i narzędzi open source

Przechwytywanie pakietów jest kluczowym składnikiem implementacji systemów wykrywania włamań do sieci (IDS) i wykonywania monitorowania zabezpieczeń sieciowych (NSM). Istnieje kilka narzędzi open source IDS, które przetwarzają przechwytywanie pakietów i szukają podpisów możliwych włamań do sieci i złośliwej aktywności. Korzystając z przechwytywania pakietów dostarczonych przez Obserwatora sieci, można analizować sieć pod kątem wszelkich szkodliwych włamań lub luk w zabezpieczeniach.

Jednym z takich narzędzi open source jest Suricata, aparat IDS, który używa zestawów reguł do monitorowania ruchu sieciowego i wyzwala alerty, gdy wystąpią podejrzane zdarzenia. Suricata oferuje silnik wielowątkowy, co oznacza, że może przeprowadzać analizę ruchu sieciowego ze zwiększoną prędkością i wydajnością. Aby uzyskać więcej informacji na temat Suricata https://suricata-ids.org/i jej możliwości, odwiedź ich stronę internetową w .

## <a name="scenario"></a>Scenariusz

W tym artykule wyjaśniono, jak skonfigurować środowisko do wykrywania włamań do sieci przy użyciu funkcji Kontrola sieci, Suricata i stos elastyczny. Network Watcher zapewnia przechwytywanie pakietów używane do wykrywania włamań do sieci. Suricata przetwarza przechwytywanie pakietów i wyzwala alerty na podstawie pakietów, które pasują do danego harmonogramu zagrożeń. Te alerty są przechowywane w pliku dziennika na komputerze lokalnym. Za pomocą elastycznego stosu dzienniki generowane przez Suricata mogą być indeksowane i używane do tworzenia pulpitu nawigacyjnego Kibana, zapewniając wizualną reprezentację dzienników i sposób szybkiego uzyskiwania wglądu w potencjalne luki w sieci.  

![prosty scenariusz aplikacji sieci Web][1]

Oba narzędzia open source można skonfigurować na maszynie Wirtualnej platformy Azure, co pozwala na wykonanie tej analizy w ramach własnego środowiska sieci platformy Azure.

## <a name="steps"></a>Kroki

### <a name="install-suricata"></a>Zainstaluj Suricata

W przypadku wszystkich innych metod instalacji należyhttps://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. W terminalu wiersza polecenia maszyny Wirtualnej uruchom następujące polecenia:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Aby zweryfikować instalację, `suricata -h` uruchom polecenie, aby wyświetlić pełną listę poleceń.

### <a name="download-the-emerging-threats-ruleset"></a>Pobierz nowy system reguł zagrożeń

Na tym etapie nie mamy żadnych zasad, aby Suricata działała. Możesz utworzyć własne reguły, jeśli istnieją określone zagrożenia dla sieci, które chcesz wykryć, lub możesz również użyć opracowanych zestawów reguł od wielu dostawców, takich jak Nowe zagrożenia lub reguły VRT z Snort. Używamy swobodnie dostępnego systemu reguł Emerging Threats tutaj:

Pobierz zestaw reguł i skopiuj je do katalogu:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Przechwytywanie pakietów procesowych za pomocą suricata

Aby przetworzyć przechwytywanie pakietów przy użyciu suricata, uruchom następujące polecenie:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Aby sprawdzić wynikowe alerty, przeczytaj plik fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Konfigurowanie stosu elastycznego

Dzienniki, które produkuje Suricata zawierają cenne informacje o tym, co dzieje się w naszej sieci, te pliki dziennika nie są najłatwiejsze do odczytania i zrozumienia. Łącząc Suricata z elastycznym stosem, możemy utworzyć pulpit nawigacyjny Kibana, który pozwala nam wyszukiwać, wykresować, analizować i czerpać wnioski z naszych dzienników.

#### <a name="install-elasticsearch"></a>Zainstaluj Elasticsearch

1. Stos elastyczny z wersji 5.0 i nowszych wymaga java 8. Uruchom polecenie, `java -version` aby sprawdzić wersję. Jeśli nie masz zainstalowanej oprogramowania Java, zapoznaj się z dokumentacją dotyczącą [zestawów JDK z suppored platformy Azure.](https://aka.ms/azure-jdks)

1. Pobierz odpowiedni pakiet binarny dla swojego systemu:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Inne metody instalacji można znaleźć na [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Sprawdź, czy Elasticsearch działa za pomocą polecenia:

    ```
    curl http://127.0.0.1:9200
    ```

    Powinna zostać wyświetlna odpowiedź podobna do tej:

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

Dalsze instrukcje dotyczące instalacji wyszukiwania elastycznego można znaleźć na stronie [Instalacja](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalowanie programu Logstash

1. Aby zainstalować program Logstash, uruchom następujące polecenia:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Następnie musimy skonfigurować Logstash do odczytu z danych wyjściowych pliku eve.json. Utwórz plik logstash.conf przy użyciu:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Dodaj do pliku następującą zawartość (upewnij się, że ścieżka do pliku eve.json jest poprawna):

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

1. Upewnij się, że nadaj odpowiednie uprawnienia do pliku eve.json, aby logstash mógł go pochłonąć.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Aby uruchomić logstash uruchom polecenie:

    ```
    sudo /etc/init.d/logstash start
    ```

Dalsze instrukcje dotyczące instalacji firmy Logstash można znaleźć w [oficjalnej dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Instalowanie Kibana

1. Uruchom następujące polecenia, aby zainstalować Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Aby uruchomić Kibana użyj poleceń:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Aby wyświetlić interfejs internetowy Kibana, przejdź do`http://localhost:5601`
1. W tym scenariuszu wzorzec indeksu używany dla dzienników Suricata jest "logstash-*"

1. Jeśli chcesz zdalnie wyświetlić pulpit nawigacyjny Kibana, utwórz przychodzącą regułę nsg umożliwiającą dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Kibana

W tym artykule udostępniliśmy przykładowy pulpit nawigacyjny, aby wyświetlić trendy i szczegóły w alertach.

1. Pobierz plik pulpitu nawigacyjnego [tutaj,](https://aka.ms/networkwatchersuricatadashboard)plik wizualizacji [tutaj](https://aka.ms/networkwatchersuricatavisualization)i zapisany plik wyszukiwania [tutaj](https://aka.ms/networkwatchersuricatasavedsearch).

1. Na karcie **Zarządzanie** w kibanie przejdź do pozycji Zapisane obiekty i zaimportuj wszystkie trzy pliki. **Saved Objects** Następnie na karcie **Pulpit nawigacyjny** można otworzyć i załadować przykładowy pulpit nawigacyjny.

Możesz również tworzyć własne wizualizacje i pulpity nawigacyjne dostosowane do danych, które cię interesują. Przeczytaj więcej o tworzeniu wizualizacji Kibana z [oficjalnej dokumentacji](https://www.elastic.co/guide/en/kibana/current/visualize.html)Kibana .

![deska rozdzielcza kibana][2]

### <a name="visualize-ids-alert-logs"></a>Wizualizuj dzienniki alertów IDS

Przykładowy pulpit nawigacyjny zawiera kilka wizualizacji dzienników alertów Suricata:

1. Alerty geoip – mapa pokazująca rozkład alertów według ich kraju/regionu pochodzenia na podstawie położenia geograficznego (określonej przez IP)

    ![geo ip][3]

1. Top 10 Alerty - podsumowanie 10 najczęściej wyzwalanych alertów i ich opis. Kliknięcie pojedynczego alertu filtruje pulpit nawigacyjny do informacji dotyczących tego określonego alertu.

    ![zdjęcie 4][4]

1. Liczba alertów – łączna liczba alertów wyzwalanych przez zestawu reguł

    ![fot.][5]

1. Top 20 Źródłowych/docelowych adresów IP/portów — wykresy kołowe przedstawiające 20 pierwszych adresów IP i portów, na których zostały wyzwolone alerty. Można odfiltrować w dół na określonych ip/portów, aby zobaczyć, ile i jakiego rodzaju alerty są wyzwalane.

    ![fot.][6]

1. Podsumowanie alertów — tabela podsumowująca szczegółowe informacje o poszczególnych alertach. Tę tabelę można dostosować, aby wyświetlić inne parametry zainteresowania dla każdego alertu.

    ![zdjęcie 7][7]

Więcej dokumentacji dotyczącej tworzenia niestandardowych wizualizacji i pulpitów nawigacyjnych można znaleźć w [oficjalnej dokumentacji firmy Kibana.](https://www.elastic.co/guide/en/kibana/current/introduction.html)

## <a name="conclusion"></a>Podsumowanie

Łącząc przechwytywanie pakietów dostarczane przez Funkcję Obserwatora sieci i narzędzia IDS typu open source, takie jak Suricata, można przeprowadzać wykrywanie włamań do sieci w celu wykrycia szerokiego zakresu zagrożeń. Te pulpity nawigacyjne umożliwiają szybkie wykrywanie trendów i anomalii w sieci, a także przeglądanie danych w celu odnajdywanie głównych przyczyn alertów, takich jak złośliwi agenci użytkowników lub porty podatne na ataki. Dzięki tym wyodrębnionych danych można podejmować świadome decyzje dotyczące reagowania i ochrony sieci przed wszelkimi szkodliwymi próbami włamania oraz tworzyć reguły zapobiegające przyszłym włamaniom do sieci.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wyzwalać przechwytywanie pakietów na podstawie alertów, odwiedzając witrynę [Użyj przechwytywania pakietów, aby proaktywne monitorowanie sieci za pomocą usługi Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, jak wizualizować dzienniki przepływu sieciowych sieci płciowych za pomocą usługi Power BI, odwiedzając [wizualizuj dzienniki przepływów sieciowych sieciowych za pomocą usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png

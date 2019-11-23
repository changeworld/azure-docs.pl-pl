---
title: Konfigurowanie Azure Monitor do integracji kontenerów Prometheus | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor dla agenta kontenerów w celu wypróbowania metryk z Prometheus z klastrem usługi Azure Kubernetes Service.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 51bdf0cfedb30fbd95f9a44e8f4a0efe4e857104
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514344"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Konfigurowanie wycinków metryk Prometheus za pomocą Azure Monitor dla kontenerów

[Prometheus](https://prometheus.io/) to popularne rozwiązanie do monitorowania metryk typu open source stanowiące składnik fundacji [Cloud Native Compute Foundation](https://www.cncf.io/). Azure Monitor dla kontenerów to bezproblemowe środowisko dołączania do zbierania metryk Prometheus. Zwykle do korzystania z Prometheus należy skonfigurować serwer Prometheus i zarządzać nim za pomocą magazynu. Przez integrację z Azure Monitor serwer Prometheus nie jest wymagany. Wystarczy uwidocznić punkt końcowy metryk Prometheus przez eksporterów lub punkty (aplikację), a agent kontenerowy dla Azure Monitor kontenerów może wyrównać te metryki. 

![Architektura monitorowania kontenerów dla Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Minimalna wersja agenta obsługiwana w przypadku wycinków Prometheus ma wartość ciprod07092019 lub nowszą, a wersja agenta obsługiwana w przypadku zapisywania błędów konfiguracji i agentów w tabeli `KubeMonAgentEvents` to ciprod10112019. Aby uzyskać dodatkowe informacje na temat wersji agenta i elementów uwzględnionych w każdej wersji, zobacz [Informacje o wersji agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Aby sprawdzić wersję agenta, z karty **węzeł** wybierz węzeł, a następnie w okienku właściwości wartość komentarza właściwości **tag obrazu agenta** .

### <a name="prometheus-scraping-settings"></a>Ustawienia odpadków Prometheus

Aktywne odróżnienie metryk z Prometheus jest wykonywane z jednej z dwóch perspektyw:

* Adres URL protokołu HTTP w całym klastrze i odnajdywanie obiektów docelowych z listy punktów końcowych usługi. Na przykład usługi k8s Services, takie jak polecenia-DNS i polecenia-State-Metrics oraz adnotacje specyficzne dla aplikacji. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus data_collection_settings. cluster]* .
* Adres URL protokołu HTTP w całym węźle i odnajdywanie obiektów docelowych z listy punktów końcowych usługi. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Endpoint | Zakres | Przykład |
|----------|-------|---------|
| Pod adnotacją | Cały klaster | adnotacj <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Usługa Kubernetes | Cały klaster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| adres URL/punkt końcowy | Dla każdego węzła i/lub całego klastra | `http://myurl:9101/metrics` |

W przypadku określenia adresu URL Azure Monitor dla kontenerów odnosi się tylko do punktu końcowego. Gdy jest określona usługa Kubernetes, nazwa usługi jest rozpoznawana z serwerem DNS klastra w celu uzyskania adresu IP, a następnie rozwiązanej usługi.

|Zakres | Klucz | Typ danych | Wartość | Opis |
|------|-----|-----------|-------|-------------|
| Cały klaster | | | | Określ jedną z następujących trzech metod, aby wypróbować punkty końcowe dla metryk. |
| | `urls` | Ciąg | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| | `kubernetes_services` | Ciąg | Tablica rozdzielona przecinkami | Tablica usług Kubernetes Services do odporności metryk z metryk polecenia-State-Metrics. Na przykład`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Wartość logiczna | true lub false | Po ustawieniu na `true` w ustawieniach całego klastra, Azure Monitor dla agenta kontenerów będą odporne na Kubernetesy w całym klastrze dla następujących adnotacji Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Wartość logiczna | true lub false | Włącza odpadków pod. `monitor_kubernetes_pods` musi być ustawiona na `true`. |
| | `prometheus.io/scheme` | Ciąg | http lub https | Wartość domyślna to złomowanie za pośrednictwem protokołu HTTP. W razie potrzeby ustaw wartość `https`. | 
| | `prometheus.io/path` | Ciąg | Tablica rozdzielona przecinkami | Ścieżka zasobu HTTP, z której mają zostać pobrane metryki. Jeśli ścieżka metryk nie jest `/metrics`, zdefiniuj ją z tą adnotacją. |
| | `prometheus.io/port` | Ciąg | 9102 | Określ port, z którego mają zostać wycinki. Jeśli port nie jest ustawiony, wartość domyślna to 9102. |
| | `monitor_kubernetes_pods_namespaces` | Ciąg | Tablica rozdzielona przecinkami | Lista dozwolonych przestrzeni nazw do wypadków metryk z Kubernetesowych.<br> Na przykład: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Cały węzeł | `urls` | Ciąg | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| Cały węzeł lub cały klaster | `interval` | Ciąg | 60 s | Interwał kolekcji jest wartością domyślną 1 minuty (60 sekund). Kolekcję można modyfikować w przypadku wartości *[prometheus_data_collection_settings. Node]* i/lub *[prometheus_data_collection_settings. cluster]* do jednostek czasu, takich jak s, m, h. |
| Cały węzeł lub cały klaster | `fieldpass`<br> `fielddrop`| Ciąg | Tablica rozdzielona przecinkami | Można określić, które metryki mają być zbierane lub nie z punktu końcowego przez ustawienie listy Zezwalaj (`fieldpass`) i nie zezwalaj (`fielddrop`). Należy najpierw ustawić listę dozwolonych. |

ConfigMaps jest globalną listą, a do agenta może być zastosowany tylko jeden ConfigMap. Nie można ConfigMaps kolekcji.

## <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie ConfigMaps

Wykonaj następujące kroki, aby skonfigurować i wdrożyć plik konfiguracyjny ConfigMap w klastrze.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) plik Template ConfigMap YAML i Zapisz go jako Container-AZM-MS-agentconfig. YAML.

2. Edytuj plik YAML ConfigMap przy użyciu dostosowań do metryk Prometheusy.

    - Aby zebrać usługi Kubernetes Services w całym klastrze, Skonfiguruj plik ConfigMap przy użyciu poniższego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Aby skonfigurować oddzielanie metryk Prometheus z określonego adresu URL w klastrze, należy skonfigurować plik ConfigMap przy użyciu poniższego przykładu.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Aby skonfigurować oddzielanie metryk Prometheus z elementu daemonset agenta dla każdego węzła w klastrze, skonfiguruj następujące elementy w ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery. 

    - Aby skonfigurować braki metryk Prometheus przez określenie adnotacji pod, wykonaj następujące czynności:

       1. W ConfigMap określ następujące elementy:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Określ następującą konfigurację dla adnotacji pod pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Jeśli chcesz ograniczyć monitorowanie do określonych obszarów nazw dla kart, które mają adnotacje, na przykład Uwzględnij tylko zasobniki dedykowane dla obciążeń produkcyjnych, ustaw `monitor_kubernetes_pod` na `true` w ConfigMap i Dodaj filtr przestrzeni nazw `monitor_kubernetes_pods_namespaces` określenie przestrzenie nazw, z których można odcinać. Na przykład: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Utwórz ConfigMap, uruchamiając następujące polecenie polecenia kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created`.

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanych ConfigMap

Jeśli wdrożono już ConfigMap w klastrze i chcesz ją zaktualizować przy użyciu nowszej konfiguracji, możesz edytować wcześniej używany plik ConfigMap, a następnie zastosować go przy użyciu tego samego polecenia jak wcześniej, `kubectl apply -f <configmap_yaml_file.yaml`.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verify-configuration"></a>Zweryfikuj konfigurację 

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana, użyj następującego polecenia, aby przejrzeć dzienniki z agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Jeśli wystąpiły błędy konfiguracji z omsagentch, w danych wyjściowych zostaną wyświetlone błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji są również dostępne do przeglądu. Następujące opcje są dostępne w celu przeprowadzenia dodatkowego rozwiązywania problemów z zmianami konfiguracji i odpadkami od Prometheus metryk:

- Z dzienników agenta pod użyciem tego samego polecenia `kubectl logs`. 

- Z dzienników na żywo. Dzienniki na żywo pokazują błędy podobne do następujących:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Z tabeli **KubeMonAgentEvents** w obszarze roboczym log Analytics. Dane są wysyłane co godzinę z ważnością *ostrzegawczą* dotyczącą błędów *odpadków i ważności błędów w* przypadku błędów konfiguracji. Jeśli nie ma żadnych błędów, wpis w tabeli będzie zawierał dane z *informacjami*o ważności, które nie zgłaszają błędów. Właściwość **Tags** zawiera więcej informacji na temat identyfikatora kontenera i, na których wystąpił błąd, a także pierwszego wystąpienia, ostatniego wystąpienia i liczby w ciągu ostatniej godziny.

Błędy uniemożliwiają programowi omsagent analizowanie pliku, powodując ponowne uruchomienie i użycie konfiguracji domyślnej. Po naprawieniu błędów w ConfigMap Zapisz plik YAML i Zastosuj zaktualizowany ConfigMaps, uruchamiając polecenie: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="query-prometheus-metrics-data"></a>Zapytanie danych metryk Prometheus

Aby wyświetlić metryki prometheuse oddzielone przez Azure Monitor i wszelkie błędy dotyczące konfiguracji/odpadków, które są raportowane przez agenta, przejrzyj [zapytanie Prometheus dane metryk](container-insights-log-search.md#query-prometheus-metrics-data) i konfigurację [zapytania lub błędy związane z brakiem](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Wyświetlanie metryk Prometheus w Grafana

Azure Monitor dla kontenerów obsługuje wyświetlanie metryk przechowywanych w obszarze roboczym Log Analytics na pulpitach nawigacyjnych Grafana. Podano szablon, który można pobrać z [repozytorium pulpitu nawigacyjnego](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, aby rozpocząć pracę i zapoznać się z pomocą techniczną, aby dowiedzieć się, jak wykonywać zapytania dotyczące dodatkowych danych z monitorowanych klastrów w celu wizualizowania niestandardowych pulpitów nawigacyjnych Grafana. 

## <a name="review-prometheus-data-usage"></a>Przegląd użycia danych Prometheus

Aby określić objętość pozyskiwania każdego rozmiaru metryki w GB dziennie, aby zrozumieć, czy jest ono wysokie, podano następujące zapytanie.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
Wyniki będą wyglądać podobnie do następujących:

![Wyniki zapytania dziennika woluminu pozyskiwania danych](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Aby oszacować każdy rozmiar metryk w GB w miesiącu, aby zrozumieć, czy ilość danych odebranych w obszarze roboczym jest wysoka, podano następujące zapytanie.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Wyniki będą wyglądać podobnie do następujących:

![Wyniki zapytania dziennika woluminu pozyskiwania danych](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Dodatkowe informacje na temat monitorowania użycia danych i analizowania kosztów są dostępne w temacie [Zarządzanie użyciem i kosztami przy użyciu dzienników Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Następne kroki

Więcej informacji o konfigurowaniu ustawień kolekcji agentów dla zmiennych stdout, stderr i środowiskowych w ramach obciążeń kontenera można znaleźć [tutaj](container-insights-agent-config.md). 
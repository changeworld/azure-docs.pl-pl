---
title: Konfigurowanie Azure Monitor na potrzeby zbierania danych przez agentów kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor dla agenta kontenerów w celu sterowania kolekcją strumienia stdout/stderr i zmiennych środowiskowych.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2019
ms.author: magoedte
ms.openlocfilehash: dfa823955cccba4ac7ec6859894a4562f0810d76
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248753"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurowanie zbierania danych agenta dla Azure Monitor kontenerów

Azure Monitor kontenerów zbiera zmienne stdout, stderr i środowiskowe z obciążeń kontenera wdrożonych do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS) z poziomu agenta kontenera. Ten agent może również zbierać dane szeregów czasowych (nazywane także metrykami) z Prometheus przy użyciu agenta kontenerowego bez konieczności konfigurowania serwera Prometheus i bazy danych oraz zarządzania nim. Ustawienia zbierania danych agentów można skonfigurować, tworząc niestandardowe Kubernetes ConfigMaps w celu kontrolowania tego środowiska. 

W tym artykule pokazano, jak utworzyć ConfigMap i skonfigurować zbieranie danych zgodnie z wymaganiami.

>[!NOTE]
>Obsługa usługi Prometheus jest w tej chwili funkcją w publicznej wersji zapoznawczej.
>

## <a name="configmap-file-settings-overview"></a>ConfigMap — Omówienie ustawień plików

Udostępniany jest plik ConfigMap szablonu, który umożliwia łatwe edytowanie go przy użyciu dostosowań bez konieczności tworzenia go od podstaw. Przed rozpoczęciem należy zapoznać się z dokumentacją Kubernetes dotyczącą [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) i zapoznać się z tematem jak tworzyć, konfigurować i wdrażać ConfigMaps. Pozwoli to na filtrowanie stderr i stdout dla przestrzeni nazw lub całego klastra oraz zmiennych środowiskowych dla dowolnego kontenera uruchomionego we wszystkich/wszystkich węzłach w klastrze.

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana w celu zbierania zmiennych stdout, stderr i środowiskowych z obciążeń kontenerów to ciprod06142019 lub nowszy. Minimalna wersja agenta obsługiwana w przypadku wycinków Prometheus ma wartość ciprod07092019 lub nowszą. Aby sprawdzić wersję agenta, z karty **węzeł** wybierz węzeł, a następnie w okienku właściwości wartość komentarza właściwości **tag obrazu agenta** .  

### <a name="data-collection-settings"></a>Ustawienia zbierania danych

Poniżej przedstawiono ustawienia, które można skonfigurować w celu kontrolowania zbierania danych.

|Klucz |Typ danych |Wartość |Opis |
|----|----------|------|------------|
|`schema-version` |Ciąg (z uwzględnieniem wielkości liter) |wersjach |Jest to wersja schematu używana przez agenta podczas analizowania tego ConfigMap. Obecnie obsługiwana wersja schematu to v1. Modyfikowanie tej wartości nie jest obsługiwane i zostanie odrzucone po obliczeniu ConfigMap.|
|`config-version` |Ciąg | | Program obsługuje możliwość śledzenia wersji tego pliku konfiguracji w systemie/repozytorium kontroli źródła. Maksymalna dozwolona liczba znaków wynosi 10, a wszystkie inne znaki są obcinane. |
|`[log_collection_settings.stdout] enabled =` |Wartość logiczna | prawda lub FAŁSZ | Ta funkcja kontroluje, czy jest włączone zbieranie dzienników strumienia stdout. W przypadku ustawienia wartości `true` i braku przestrzeni nazw nie są one wykluczone dla kolekcji dzienników stdout (ustawienie `log_collection_settings.stdout.exclude_namespaces` poniżej), dzienniki stdout będą zbierane ze wszystkich kontenerów we wszystkich węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Ciąg | Tablica rozdzielona przecinkami |Tablica przestrzeni nazw Kubernetes, dla których dzienniki stdout nie będą zbierane. To ustawienie obowiązuje tylko wtedy, gdy `log_collection_settings.stdout.enabled` jest ustawiona na `true`. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Wartość logiczna | prawda lub FAŁSZ |Ta funkcja kontroluje, czy jest włączona kolekcja dzienników kontenera stderr. Jeśli ustawiono wartość `true` i żadne obszary nazw nie są wykluczone na potrzeby zbierania dzienników stdout (ustawienie `log_collection_settings.stderr.exclude_namespaces`), dzienniki stderr będą zbierane ze wszystkich kontenerów we wszystkich węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Ciąg |Tablica rozdzielona przecinkami |Tablica przestrzeni nazw Kubernetes, dla których dzienniki stderr nie będą zbierane. To ustawienie obowiązuje tylko wtedy, gdy `log_collection_settings.stdout.enabled` jest ustawiona na `true`. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Wartość logiczna | prawda lub FAŁSZ | To ustawienie steruje kolekcją zmiennych środowiskowych we wszystkich zestawach/węzłach w klastrze i domyślnie `enabled = true`, gdy nie zostanie określony w ConfigMaps. Jeśli kolekcja zmiennych środowiskowych jest włączona globalnie, można ją wyłączyć dla określonego kontenera przez ustawienie zmiennej środowiskowej `AZMON_COLLECT_ENV` na **wartość FAŁSZ** przy użyciu ustawienia pliku dockerfile lub w [pliku](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) **konfiguracyjnym pod env:** sekcja. Jeśli zbieranie zmiennych środowiskowych jest wyłączone globalnie, nie można włączyć kolekcji dla określonego kontenera (oznacza to, że jedynym przesłonięciem, który można zastosować na poziomie kontenera, jest wyłączenie kolekcji, gdy jest ona już włączona globalnie). |

### <a name="prometheus-scraping-settings"></a>Ustawienia odpadków Prometheus

![Architektura monitorowania kontenerów dla Prometheus](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

Azure Monitor dla kontenerów zapewnia bezproblemowe środowisko umożliwiające zbieranie metryk Prometheus przez wiele wycinków przez następujące mechanizmy, jak pokazano w poniższej tabeli. Metryki są zbierane za pośrednictwem zestawu ustawień określonych w pojedynczym pliku ConfigMap, który jest ten sam plik, który służy do konfigurowania kolekcji stdout, stderr i zmiennych środowiskowych z obciążeń kontenerów. 

Aktywne odróżnienie metryk z Prometheus jest wykonywane z jednej z dwóch perspektyw:

* Adresy URL protokołu HTTP w całym klastrze i odnajdywanie elementów docelowych z listy punktów końcowych usługi, usług k8s, takich jak polecenia-DNS i polecenia-State-Metrics, a pod adnotacje specyficzne dla aplikacji. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus data_collection_settings. cluster]* .
* Adres URL protokołu HTTP w całym węźle i odnajdywanie obiektów docelowych z listy punktów końcowych usługi. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Punkt końcowy | Zakres | Przykład |
|----------|-------|---------|
| Pod adnotacją | Cały klaster | adnotacj <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Usługa Kubernetes | Cały klaster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| adres URL/punkt końcowy | Dla każdego węzła i/lub całego klastra | `http://myurl:9101/metrics` |

W przypadku określenia adresu URL Azure Monitor dla kontenerów odnosi się tylko do punktu końcowego. Gdy jest określona usługa Kubernetes, nazwa usługi jest rozpoznawana z serwerem DNS klastra w celu uzyskania adresu IP, a następnie rozwiązanej usługi.

|Zakres | Klucz | Typ danych | Wartość | Opis |
|------|-----|-----------|-------|-------------|
| Cały klaster | | | | Określ jedną z następujących trzech metod, aby wypróbować punkty końcowe dla metryk. |
| | `urls` | Ciąg | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| | `kubernetes_services` | Ciąg | Tablica rozdzielona przecinkami | Tablica usług Kubernetes Services do odporności metryk z metryk polecenia-State-Metrics. Na przykład `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Wartość logiczna | prawda lub FAŁSZ | Ustawienie `true` w ustawieniach całego klastra Azure Monitor dla agenta kontenerów spowoduje odróżnienie Kubernetesych zestawów w całym klastrze dla następujących adnotacji Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Wartość logiczna | prawda lub FAŁSZ | Włącza odpadków pod. `monitor_kubernetes_pods` musi być ustawiona na `true`. |
| | `prometheus.io/scheme` | Ciąg | http lub https | Wartość domyślna to złomowanie za pośrednictwem protokołu HTTP. W razie potrzeby ustaw wartość `https`. | 
| | `prometheus.io/path` | Ciąg | Tablica rozdzielona przecinkami | Ścieżka zasobu HTTP, z której mają zostać pobrane metryki. Jeśli ścieżka metryk nie jest `/metrics`, zdefiniuj ją z tą adnotacją. |
| | `prometheus.io/port` | Ciąg | 9102 | Określ port, z którego mają zostać wycinki. Jeśli port nie jest ustawiony, wartość domyślna to 9102. |
| Cały węzeł | `urls` | Ciąg | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| Cały węzeł lub cały klaster | `interval` | Ciąg | 60 s | Interwał kolekcji jest wartością domyślną 1 minuty (60 sekund). Kolekcję można modyfikować dla wartości *[prometheus_data_collection_settings. Node]* i/lub *[prometheus_data_collection_settings. cluster]* do jednostek czasu, takich jak s, m, h. |
| Cały węzeł lub cały klaster | `fieldpass`<br> `fielddrop`| Ciąg | Tablica rozdzielona przecinkami | Można określić, które metryki mają być zbierane lub nie z punktu końcowego, ustawiając listę dozwolonych (`fieldpass`) i nie zezwalaj (`fielddrop`). Należy najpierw ustawić listę dozwolonych. |

ConfigMaps jest globalną listą, a do agenta może być zastosowany tylko jeden ConfigMap. Nie można ConfigMaps kolekcji.

## <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie ConfigMaps

Wykonaj następujące kroki, aby skonfigurować i wdrożyć plik konfiguracyjny ConfigMap w klastrze.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) plik Template ConfigMap YAML i Zapisz go jako Container-AZM-MS-agentconfig. YAML.  

2. Edytuj plik YAML ConfigMap przy użyciu dostosowań, aby zbierać zmienne stdout, stderr i/lub środowiskowe.

    - Aby wykluczyć określone przestrzenie nazw dla zbierania dzienników stdout, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Aby wyłączyć kolekcję zmiennych środowiskowych dla określonego kontenera, ustaw wartość klucza/wartości `[log_collection_settings.env_var] enabled = true`, aby globalnie włączyć zbieranie zmiennych, a następnie wykonaj kroki opisane [tutaj](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , aby ukończyć konfigurację dla określonego kontenera.
    
    - Aby wyłączyć zbieranie dzienników stderr dla całego klastra, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stderr] enabled = false`.
    
3. Aby skonfigurować kolekcję Kubernetes Services Cluster-Wide, Skonfiguruj plik ConfigMap przy użyciu poniższego przykładu.

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
    ```

4. Aby skonfigurować oddzielanie metryk Prometheus z określonego adresu URL w klastrze, należy skonfigurować plik ConfigMap przy użyciu poniższego przykładu.

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
    ```

5. Aby skonfigurować oddzielanie metryk Prometheus z elementu daemonset agenta dla każdego węzła w klastrze, skonfiguruj następujące elementy w ConfigMap:
    
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
    >$NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi mieć wielkie litery. 

6. Aby skonfigurować braki metryk Prometheus przez określenie adnotacji pod, wykonaj następujące czynności:

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

7. Utwórz ConfigMap, uruchamiając następujące polecenie polecenia kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created`.

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana, użyj następującego polecenia, aby przejrzeć dzienniki z agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Jeśli wystąpiły błędy konfiguracji z omsagentch, w danych wyjściowych zostaną wyświetlone błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji dla Prometheus są również dostępne do przeglądu.  Z dzienników agenta pod użyciem tego samego polecenia `kubectl logs` lub z dzienników na żywo. Dzienniki na żywo pokazują błędy podobne do następujących:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Błędy uniemożliwiają programowi omsagent analizowanie pliku, powodując ponowne uruchomienie i użycie konfiguracji domyślnej. Po naprawieniu błędów w ConfigMap Zapisz plik YAML i Zastosuj zaktualizowany ConfigMaps, uruchamiając polecenie: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanych ConfigMap

Jeśli wdrożono już ConfigMap w klastrze i chcesz ją zaktualizować przy użyciu nowszej konfiguracji, możesz edytować wcześniej używany plik ConfigMap, a następnie zastosować go przy użyciu tego samego polecenia jak wcześniej, `kubectl apply -f <configmap_yaml_file.yaml`.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Weryfikowanie wersji schematu

Obsługiwane wersje schematu konfiguracji są dostępne jako adnotacja pod (wersje schematu) na omsagent pod. Można je wyświetlić za pomocą następującego polecenia polecenia kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Dane wyjściowe będą wyświetlane podobnie jak w przypadku następujących wersji schematu adnotacji:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="query-prometheus-metrics-data"></a>Zapytanie danych metryk Prometheus

Aby wyświetlić metryki Prometheus odpadków Azure Monitor, określ wartość "Prometheus" jako przestrzeń nazw. Oto przykładowe zapytanie umożliwiające wyświetlenie metryk Prometheus z przestrzeni nazw `default` Kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Dane Prometheus można także zbadać bezpośrednio według nazwy.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

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

![Wyniki zapytania dziennika woluminu pozyskiwania danych](./media/container-insights-agent-config/log-query-example-usage-03.png)

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

![Wyniki zapytania dziennika woluminu pozyskiwania danych](./media/container-insights-agent-config/log-query-example-usage-02.png)

Dodatkowe informacje na temat monitorowania użycia danych i analizowania kosztów są dostępne w temacie [Zarządzanie użyciem i kosztami przy użyciu dzienników Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Następne kroki

Azure Monitor kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dotyczących wydajności za pomocą Azure monitor dla kontenerów](container-insights-alerts.md) , aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego użycia procesora i pamięci, aby zapewnić obsługę procesów i procedur operacyjnych DevOps.

- Aby kontynuować uczenie się, jak używać Azure Monitor i monitorować inne aspekty klastra AKS, zobacz [Wyświetlanie usługi Azure Kubernetes Service Health](container-insights-analyze.md).

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.

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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 12010aaa7bc90bd200264549ad3efb79f46576c6
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867655"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurowanie zbierania danych agenta dla Azure Monitor kontenerów

Azure Monitor kontenerów zbiera zmienne stdout, stderr i środowiskowe z obciążeń kontenera wdrożonych do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS) z poziomu agenta kontenera. Ten agent może również zbierać dane szeregów czasowych (nazywane także metrykami) z Prometheus przy użyciu agenta kontenerowego bez konieczności konfigurowania serwera Prometheus i bazy danych oraz zarządzania nim. Ustawienia zbierania danych agentów można skonfigurować, tworząc niestandardowe Kubernetes ConfigMaps w celu kontrolowania tego środowiska. 

W tym artykule pokazano, jak utworzyć ConfigMap i skonfigurować zbieranie danych zgodnie z wymaganiami.

>[!NOTE]
>Obsługa usługi Prometheus jest w tej chwili funkcją w publicznej wersji zapoznawczej.
>

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Skonfiguruj klaster przy użyciu niestandardowych ustawień zbierania danych

Udostępniany jest plik ConfigMap szablonu, który umożliwia łatwe edytowanie go przy użyciu dostosowań bez konieczności tworzenia go od podstaw. Przed rozpoczęciem należy zapoznać się z dokumentacją Kubernetes dotyczącą [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) i zapoznać się z tematem jak tworzyć, konfigurować i wdrażać ConfigMaps. Pozwoli to na filtrowanie stderr i stdout dla przestrzeni nazw lub całego klastra oraz zmiennych środowiskowych dla dowolnego kontenera uruchomionego we wszystkich/wszystkich węzłach w klastrze.

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana w celu zbierania zmiennych stdout, stderr i środowiskowych z obciążeń kontenerów to ciprod06142019 lub nowszy. Minimalna wersja agenta obsługiwana w przypadku wycinków Prometheus ma wartość ciprod07092019 lub nowszą. Aby sprawdzić wersję agenta, z karty **węzeł** wybierz węzeł, a następnie w okienku właściwości wartość komentarza właściwości **tag obrazu agenta** .  

### <a name="overview-of-configurable-data-collection-settings"></a>Przegląd ustawień zbierania danych konfigurowalnych

Poniżej przedstawiono ustawienia, które można skonfigurować w celu kontrolowania zbierania danych.

|Klucz |Typ danych |Value |Opis |
|----|----------|------|------------|
|`schema-version` |Ciąg (z uwzględnieniem wielkości liter) |v1 |Jest to wersja schematu używana przez agenta podczas analizowania tego ConfigMap. Obecnie obsługiwana wersja schematu to v1. Modyfikowanie tej wartości nie jest obsługiwane i zostanie odrzucone po obliczeniu ConfigMap.|
|`config-version` |String | | Program obsługuje możliwość śledzenia wersji tego pliku konfiguracji w systemie/repozytorium kontroli źródła. Maksymalna dozwolona liczba znaków wynosi 10, a wszystkie inne znaki są obcinane. |
|`[log_collection_settings.stdout] enabled =` |Boolean | prawda lub FAŁSZ | Ta funkcja kontroluje, czy jest włączone zbieranie dzienników strumienia stdout. Po ustawieniu na `true` i nie są wykluczone przestrzenie nazw dla zbierania`log_collection_settings.stdout.exclude_namespaces` dzienników stdout (ustawienie poniżej), dzienniki stdout będą zbierane ze wszystkich kontenerów we wszystkich węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Tablica rozdzielona przecinkami |Tablica przestrzeni nazw Kubernetes, dla których dzienniki stdout nie będą zbierane. To ustawienie ma zastosowanie tylko wtedy `log_collection_settings.stdout.enabled` , gdy jest `true`ustawione na. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | prawda lub FAŁSZ |Ta funkcja kontroluje, czy jest włączona kolekcja dzienników kontenera stderr. Po ustawieniu na `true` i nie są wykluczone przestrzenie nazw dla zbierania`log_collection_settings.stderr.exclude_namespaces` dzienników stdout (ustawienie), dzienniki stderr będą zbierane ze wszystkich kontenerów na wszystkich zestawach/węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Tablica rozdzielona przecinkami |Tablica przestrzeni nazw Kubernetes, dla których dzienniki stderr nie będą zbierane. To ustawienie ma zastosowanie tylko wtedy `log_collection_settings.stdout.enabled` , gdy jest `true`ustawione na. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | prawda lub FAŁSZ | Ta funkcja kontroluje, czy jest włączona kolekcja zmiennych środowiskowych. Gdy jest ustawiona `false`na, żadne zmienne środowiskowe nie są zbierane dla żadnego kontenera uruchomionego we wszystkich zbiorach/węzłach w klastrze. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `enabled = true`. |

## <a name="overview-of-configurable-prometheus-scraping-settings"></a>Omówienie konfigurowalnych ustawień odpadków Prometheus

Aktywne wycinki metryk z Prometheus są wykonywane z jednej z dwóch perspektyw:

* Adresy URL protokołu HTTP w całym klastrze i odnajdywanie elementów docelowych z listy punktów końcowych usługi, usług k8s, takich jak polecenia-DNS i polecenia-State-Metrics, a pod adnotacje specyficzne dla aplikacji. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus data_collection_settings. cluster]* .
* Adres URL protokołu HTTP w całym węźle i odnajdywanie obiektów docelowych z listy punktów końcowych usługi. Metryki zebrane w tym kontekście zostaną zdefiniowane w sekcji ConfigMap *[Prometheus_data_collection_settings. Node]* .

|Scope | Klucz | Typ danych | Value | Opis |
|------|-----|-----------|-------|-------------|
| Cały klaster | | | | Określ jedną z następujących trzech metod, aby wypróbować punkty końcowe dla metryk. |
| | `urls` | String | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| | `kubernetes_services` | String | Tablica rozdzielona przecinkami | Tablica usług Kubernetes Services do odporności metryk z metryk polecenia-State-Metrics. Na przykład`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | prawda lub FAŁSZ | Po ustawieniu `true` na wartość w ustawieniach całego klastra, Azure monitor dla agenta kontenerów będą odporne na Kubernetesy w całym klastrze dla następujących adnotacji Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | prawda lub FAŁSZ | Włącza odpadków pod. |
| | `prometheus.io/scheme` | Ciąg | http lub https | Wartość domyślna to złomowanie za pośrednictwem protokołu HTTP. W `https`razie potrzeby ustaw wartość. | 
| | `prometheus.io/path` | Ciąg | Tablica rozdzielona przecinkami | Ścieżka zasobu HTTP, z której mają zostać pobrane metryki. Jeśli ścieżka metryk nie `/metrics`jest zdefiniowana, zdefiniuj ją z tą adnotacją. |
| | `prometheus.io/port` | Ciąg | 9102 | Określ port, na którym nasłuchuje nasłuchiwanie. Jeśli port nie jest ustawiony, wartość domyślna to 9102. |
| Cały węzeł | `urls` | Ciąg | Tablica rozdzielona przecinkami | Punkt końcowy HTTP (podano adres IP lub prawidłową ścieżkę URL). Na przykład: `urls=[$NODE_IP/metrics]`. ($NODE _IP jest określonym Azure Monitor dla parametru Containers i można go użyć zamiast adresu IP węzła. Musi zawierać wielkie litery). |
| Cały węzeł lub cały klaster | `interval` | Ciąg | 60 s | Interwał kolekcji jest wartością domyślną 1 minuty (60 sekund). Kolekcję można modyfikować w ramach jednostek czasu *[prometheus_data_collection_settings. Node]* i/lub *[prometheus_data_collection_settings. cluster]* , takich jak NS, US (lub Âμs), MS, s, m, h. |
| Cały węzeł lub cały klaster | `fieldpass`<br> `fielddrop`| String | Tablica rozdzielona przecinkami | Można określić, które metryki mają być zbierane lub nie z punktu końcowego przez ustawienie listy Zezwalaj`fieldpass`() i nie`fielddrop`Zezwalaj (). Należy najpierw ustawić listę dozwolonych. |

ConfigMap jest globalną listą, a do agenta może być zastosowany tylko jeden ConfigMap. Nie można ConfigMap kolekcji.

### <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie ConfigMaps

Wykonaj następujące kroki, aby skonfigurować i wdrożyć plik konfiguracyjny ConfigMap w klastrze.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) plik Template ConfigMap YAML i Zapisz go jako Container-AZM-MS-agentconfig. YAML.  
1. Edytuj plik YAML ConfigMap z własnymi dostosowaniami.

    - Aby wykluczyć określone przestrzenie nazw dla zbierania dzienników stdout, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Aby wyłączyć kolekcję zmiennych środowiskowych dla określonego kontenera, należy ustawić klucz/wartość `[log_collection_settings.env_var] enabled = true` , aby włączyć kolekcje zmiennych globalnie, a następnie wykonać kroki opisane [tutaj](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , aby ukończyć konfigurację dla określonego kontenera.
    - Aby wyłączyć zbieranie dzienników stderr dla całego klastra, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stderr] enabled = false`.

1. Utwórz ConfigMap, uruchamiając następujące polecenie polecenia kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created`.

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana, użyj następującego polecenia, aby przejrzeć dzienniki z agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Jeśli wystąpiły błędy konfiguracji z omsagentch, w danych wyjściowych zostaną wyświetlone błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji dla Prometheus są również dostępne do przeglądu.  Z dzienników agenta pod użyciem tego samego `kubectl logs` polecenia lub z dzienników na żywo. W dziennikach na żywo są wyświetlane błędy podobne do następujących:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Błędy uniemożliwiają programowi omsagent analizowanie pliku, powodując ponowne uruchomienie i użycie konfiguracji domyślnej. Po naprawieniu błędów w ConfigMap Zapisz plik YAML i Zastosuj zaktualizowany ConfigMaps, uruchamiając polecenie: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanych ConfigMap

Jeśli wdrożono już ConfigMap w klastrze i chcesz ją zaktualizować przy użyciu nowszej konfiguracji, możesz po prostu edytować plik ConfigMap, który został wcześniej użyty, a następnie zastosować go przy użyciu tego samego polecenia jak poprzednio `kubectl apply -f <configmap_yaml_file.yaml`.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Weryfikowanie wersji schematu

Obsługiwane wersje schematu konfiguracji są dostępne jako adnotacja pod (wersje schematu) na omsagent pod. Można je wyświetlić za pomocą następującego polecenia polecenia kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

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

## <a name="review-prometheus-data-usage"></a>Przegląd użycia danych Prometheus

Aby określić objętość pozyskiwania każdego rozmiaru metryki w GB dziennie, aby zrozumieć, czy jest ono wysokie, podano następujące zapytanie.

```
InsightsMetrics 
| where Namespace contains "prometheus"
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

- Aby kontynuować, jak używać usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.
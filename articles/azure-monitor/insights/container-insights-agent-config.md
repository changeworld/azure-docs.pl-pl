---
title: Konfigurowanie usługi Azure Monitor dla zbierania danych agenta kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak skonfigurować agenta usługi Azure Monitor dla kontenerów do kontrolowania kolekcji dziennika stdout/stderr i środowiska.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933023"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurowanie zbierania danych agenta dla usługi Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów zbiera zmienne stdout, stderr i środowiskowe z obciążeń kontenerów wdrożonych do zarządzanych klastrów kubernetes z konteneryzowanym agentem. Można skonfigurować ustawienia zbierania danych agenta, tworząc niestandardowe Kubernetes ConfigMaps do kontrolowania tego środowiska. 

W tym artykule pokazano, jak utworzyć configMap i skonfigurować zbieranie danych na podstawie wymagań.

>[!NOTE]
>W przypadku usługi Azure Red Hat OpenShift plik configmap szablonu jest tworzony w obszarze nazw *openshift-azure-logging.* 
>

## <a name="configmap-file-settings-overview"></a>Omówienie ustawień pliku ConfigMap

Szablon ConfigMap plik jest pod warunkiem, że pozwala na łatwe edytowanie go z dostosowań bez konieczności tworzenia go od podstaw. Przed rozpoczęciem należy przejrzeć dokumentację narzędzia Kubernetes dotyczącą [configmaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) i zapoznać się z instrukcjami tworzenia, konfigurowania i wdrażania configmaps. Umożliwi to filtrowanie stderr i stdout na obszar nazw lub w całym klastrze i zmiennych środowiskowych dla dowolnego kontenera działającego we wszystkich zasobnikach/węzłach w klastrze.

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana do zbierania zmiennych stdout, stderr i środowiskowych z obciążeń kontenerów to ciprod06142019 lub nowszy. Aby zweryfikować wersję agenta, na karcie **Węzeł** wybierz węzeł, a w okienku właściwości wartość notatki właściwości **Agent Image Tag.** Aby uzyskać dodatkowe informacje na temat wersji agenta i tego, co jest zawarte w każdej wersji, zobacz [informacje o wersji agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Ustawienia zbierania danych

Poniżej przedstawiono ustawienia, które można skonfigurować do kontrolowania zbierania danych.

|Klucz |Typ danych |Wartość |Opis |
|----|----------|------|------------|
|`schema-version` |Ciąg (z uwzględnieniem wielkości liter) |w wersji 1 |Jest to wersja schematu używana przez agenta podczas analizowania tego ConfigMap. Obecnie obsługiwana wersja schematu jest w wersji 1. Modyfikowanie tej wartości nie jest obsługiwane i zostanie odrzucone podczas oceny configmap.|
|`config-version` |Ciąg | | Obsługuje możliwość śledzenia wersji tego pliku konfiguracyjnego w systemie kontroli źródła / repozytorium. Maksymalne dozwolone znaki to 10, a wszystkie inne znaki obcięty. |
|`[log_collection_settings.stdout] enabled =` |Wartość logiczna | true lub false | To określa, czy stdout kolekcji dziennika kontenera jest włączona. Po ustawieniu `true` i nie obszarów nazw są wykluczone dla stdout log kolekcji (ustawienie`log_collection_settings.stdout.exclude_namespaces` poniżej), dzienniki stdout będą zbierane ze wszystkich kontenerów we wszystkich zasobników/węzłów w klastrze. Jeśli nie określono w ConfigMaps, `enabled = true`wartością domyślną jest . |
|`[log_collection_settings.stdout] exclude_namespaces =`|Ciąg | Tablica oddzielona przecinkami |Tablica obszarów nazw Kubernetes, dla których nie będą zbierane dzienniki stdout. To ustawienie jest `log_collection_settings.stdout.enabled` skuteczne tylko `true`wtedy, gdy jest ustawione na . Jeśli nie określono w ConfigMap, `exclude_namespaces = ["kube-system"]`wartość domyślna to .|
|`[log_collection_settings.stderr] enabled =` |Wartość logiczna | true lub false |To określa, czy stderr kolekcji dziennika kontenera jest włączona. Po ustawieniu `true` i nie obszarów nazw są wykluczone dla stdout log collection (ustawienie),`log_collection_settings.stderr.exclude_namespaces` dzienniki stderr będą zbierane ze wszystkich kontenerów we wszystkich zasobników/węzłów w klastrze. Jeśli nie określono w ConfigMaps, `enabled = true`wartością domyślną jest . |
|`[log_collection_settings.stderr] exclude_namespaces =` |Ciąg |Tablica oddzielona przecinkami |Tablica obszarów nazw Kubernetes, dla których nie będą zbierane dzienniki stderr. To ustawienie jest `log_collection_settings.stdout.enabled` skuteczne tylko `true`wtedy, gdy jest ustawione na . Jeśli nie określono w ConfigMap, `exclude_namespaces = ["kube-system"]`wartość domyślna to . |
| `[log_collection_settings.env_var] enabled =` |Wartość logiczna | true lub false | To ustawienie steruje kolekcją zmiennych środowiskowych we wszystkich zasobnikach/węzłach w klastrze i `enabled = true` domyślnie, gdy nie określono w ConfigMaps. Jeśli zbieranie zmiennych środowiskowych jest globalnie włączone, można wyłączyć go dla `AZMON_COLLECT_ENV` określonego kontenera, ustawiając zmienną środowiskową na **False** za pomocą ustawienia Dockerfile lub w [pliku konfiguracyjnym zasobnika](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) w sekcji **env:.** Jeśli zbieranie zmiennych środowiskowych jest globalnie wyłączone, nie można włączyć kolekcji dla określonego kontenera (oznacza to, że jedynym zastąpieniem, które można zastosować na poziomie kontenera jest wyłączenie kolekcji, gdy jest już włączona globalnie.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Wartość logiczna | true lub false | To ustawienie steruje wzbogacaniem dziennika kontenera w celu wypełnienia wartości właściwości Nazwa i Obraz dla każdego rekordu dziennika zapisanego w tabeli ContainerLog dla wszystkich dzienników kontenerów w klastrze. Domyślnie, `enabled = false` gdy nie określono w ConfigMap. |

ConfigMaps jest globalną listą i do agenta może być zastosowany tylko jeden ConfigMap. Nie można mieć innego ConfigMaps unieważniania kolekcji.

## <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie map konfiguratywnych

Wykonaj następujące kroki, aby skonfigurować i wdrożyć plik konfiguracyjny configmap w klastrze.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) szablon ConfigMap yaml i zapisz go jako container-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Ten krok nie jest wymagany podczas pracy z usługą Azure Red Hat OpenShift, ponieważ szablon ConfigMap już istnieje w klastrze.

2. Edytuj plik ConfigMap yaml z dostosowaniami, aby zebrać zmienne stdout, stderr i/lub środowiskowe. Jeśli edytujesz plik yaml ConfigMap dla usługi Azure Red `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Hat OpenShift, najpierw uruchom polecenie, aby otworzyć plik w edytorze tekstu.

    - Aby wykluczyć określone przestrzenie nazw dla kolekcji dzienników stdout, należy `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`skonfigurować klucz/wartość przy użyciu następującego przykładu: .
    
    - Aby wyłączyć zbieranie zmiennych środowiskowych dla określonego `[log_collection_settings.env_var] enabled = true` kontenera, ustaw klucz/wartość, aby włączyć zbieranie zmiennych globalnie, a następnie wykonaj kroki [w tym miejscu,](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) aby zakończyć konfigurację dla określonego kontenera.
    
    - Aby wyłączyć klaster kolekcji dzienników stderr, należy skonfigurować `[log_collection_settings.stderr] enabled = false`klucz/wartość przy użyciu następującego przykładu: .

3. W przypadku klastrów innych niż Azure Red Hat OpenShift utwórz ConfigMap, uruchamiając następujące polecenie kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` w klastrach innych niż Azure Red Hat OpenShift. 
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    W przypadku usługi Azure Red Hat OpenShift zapisz zmiany w edytorze.

Zmiana konfiguracji może potrwać kilka minut, zanim zacznie obowiązywać, a wszystkie zasobniki omsagent w klastrze zostanie ponownie uruchomiony. Ponowne uruchomienie jest stopniowym restartem dla wszystkich zasobników omsagent, nie wszystkie restartują się w tym samym czasie. Po zakończeniu ponownego uruchamiania wyświetlany jest komunikat podobny do następującego i `configmap "container-azm-ms-agentconfig" created`zawierający wynik: .

## <a name="verify-configuration"></a>Weryfikowanie konfiguracji

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana do klastra innego niż Azure Red Hat `kubectl logs omsagent-fdf58 -n=kube-system`OpenShift, użyj następującego polecenia, aby przejrzeć dzienniki z zasobnika agenta: . Jeśli występują błędy konfiguracji z zasobników omsagent, dane wyjściowe będą wyświetlane błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji są również dostępne do przeglądu. Dostępne są następujące opcje do wykonywania dodatkowych rozwiązywania problemów ze zmianami konfiguracji:

- Z zasobnika agenta loguje się przy użyciu tego samego `kubectl logs` polecenia. 

    >[!NOTE]
    >To polecenie nie ma zastosowania do klastra OpenShift usługi Azure Red Hat.
    > 

- Z dzienników na żywo. Dzienniki na żywo pokazują błędy podobne do następujących:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Z tabeli **KubeMonAgentEvents** w obszarze roboczym usługi Log Analytics. Dane są wysyłane co godzinę z *ważnością błędu* dla błędów konfiguracji. Jeśli nie ma żadnych błędów, wpis w tabeli będzie miał dane z *ważnością Info*, który zgłasza żadnych błędów. **Właściwość Tags** zawiera więcej informacji na temat identyfikatora zasobnika i kontenera, na którym wystąpił błąd, a także pierwsze wystąpienie, ostatnie wystąpienie i liczba w ostatniej godzinie.

- Za pomocą narzędzia Azure Red Hat OpenShift sprawdź dzienniki omsagent, przeszukując tabelę **ContainerLog,** aby sprawdzić, czy kolekcja dziennika rejestrowania openshift-azure jest włączona.

Po skorygowaniu błędów w configmap w klastrach innych niż Azure Red Hat OpenShift zapisz plik yaml i `kubectl apply -f <configmap_yaml_file.yaml`zastosuj zaktualizowane ConfigMaps, uruchamiając polecenie: . W przypadku usługi Azure Red Hat OpenShift edytuj i zapisz zaktualizowane mapy configmaps, uruchamiając polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanej mapy konfiguracyjnej

Jeśli configMap został już wdrożony w klastrach innych niż Azure Red Hat OpenShift i chcesz go zaktualizować za pomocą nowszej konfiguracji, możesz edytować wcześniej `kubectl apply -f <configmap_yaml_file.yaml`używany plik ConfigMap, a następnie zastosować go za pomocą tego samego polecenia, co poprzednio, . W przypadku usługi Azure Red Hat OpenShift edytuj i zapisz zaktualizowane mapy configmaps, uruchamiając polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Zmiana konfiguracji może potrwać kilka minut, zanim zacznie obowiązywać, a wszystkie zasobniki omsagent w klastrze zostanie ponownie uruchomiony. Ponowne uruchomienie jest stopniowym restartem dla wszystkich zasobników omsagent, nie wszystkie restartują się w tym samym czasie. Po zakończeniu ponownego uruchamiania wyświetlany jest komunikat podobny do następującego i `configmap "container-azm-ms-agentconfig" updated`zawierający wynik: .

## <a name="verifying-schema-version"></a>Weryfikowanie wersji schematu

Obsługiwane wersje schematu konfiguracji są dostępne jako adnotacja zasobnika (wersje schematu) w zasobniku omsagent. Można je zobaczyć za pomocą następującego polecenia kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

Dane wyjściowe będą wyświetlane podobnie do następującego z wersjami schematu adnotacji:

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

## <a name="next-steps"></a>Następne kroki

- Usługa Azure Monitor dla kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Przejrzyj [tworzenie alertów wydajności za pomocą usługi Azure Monitor dla kontenerów,](container-insights-alerts.md) aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego wykorzystania procesora CPU i pamięci w celu obsługi procesów i procedur operacyjnych.

- Dzięki możliwości monitorowania włączonemu do zbierania kondycji i wykorzystania zasobów usługi AKS lub klastra hybrydowego i uruchomionych na nich obciążeń, dowiedz się, [jak używać](container-insights-analyze.md) usługi Azure Monitor dla kontenerów.

- Wyświetlanie [przykładów zapytań dziennika,](container-insights-log-search.md#search-logs-to-analyze-data) aby wyświetlić wstępnie zdefiniowane kwerendy i przykłady do oceny lub dostosowania do alertów, wizualizacji lub analizowania klastrów.

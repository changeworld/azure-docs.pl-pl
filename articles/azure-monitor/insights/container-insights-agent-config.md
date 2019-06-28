---
title: Konfigurowanie usługi Azure Monitor do zbierania danych agenta kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można skonfigurować usługi Azure Monitor dla agenta kontenerów do kontrolowania stdout/stderr i zmiennych środowiskowych — zbierania dzienników.
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
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341658"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurowanie agenta zbierania danych dla usługi Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów umożliwia zbieranie informacji o stdout, stderr i zmienne środowiskowe z obciążeń kontenerów wdrożonych na zarządzane klastry Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS) z agenta konteneryzowanych. Ustawienia zbierania danych agenta można skonfigurować, tworząc niestandardowe Kubernetes ConfigMaps do kontrolowania tego środowiska. W tym artykule przedstawiono sposób tworzenia ConfigMap i Konfigurowanie zbierania danych, w zależności od wymagań.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Konfigurowanie klastra za pomocą ustawienia zbierania danych niestandardowych

Plik ConfigMap szablonu jest warunkiem, który pozwala łatwo edytować za pomocą Twoich dostosowań bez konieczności tworzenia jej od podstaw. Przed rozpoczęciem należy przejrzeć dokumentacją rozwiązania Kubernetes o [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) i zapoznać się ze sposobem tworzenia, konfigurowania i wdrażania ConfigMaps. Umożliwi to można filtrować, stderr i stdout na przestrzeń nazw lub dla całego klastra i zmiennych środowiskowych dla dowolnego kontenera z systemem we wszystkich zasobników/węzłach w klastrze.

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwane przez tę funkcję, firma microsoft / oms:ciprod06142019 lub nowszej. 

### <a name="overview-of-configurable-data-collection-settings"></a>Przegląd ustawień zbierania danych można skonfigurować

Poniżej przedstawiono ustawienia, które można skonfigurować, aby kontrolować zbieranie danych.

|Klucz |Typ danych |Value |Opis |
|----|----------|------|------------|
|`schema-version` |Ciąg (z uwzględnieniem wielkości liter) |v1 |To jest wersja schematu, używany przez agenta podczas analizowania tego ConfigMap. Aktualnie obsługiwana wersja schematu jest w wersji 1. Modyfikowania tej wartości nie jest obsługiwane i zostaną odrzucone podczas szacowania ConfigMap.|
|`config-version` |String | | Pozwala na śledzenie wersji tego pliku konfiguracji w Twoim repozytorium kontroli źródła systemu /. Maksymalną dozwoloną liczbę znaków są 10, a wszystkie inne znaki są obcinane. |
|`[log_collection_settings.stdout] enabled =` |Boolean | wartość PRAWDA lub FAŁSZ | Kontroluje to, czy jest włączone zbieranie danych dziennika stdout kontenera. Po ustawieniu `true` i nie przestrzeni nazw są wyłączone zbieranie danych dziennika stdout (`log_collection_settings.stdout.exclude_namespaces` ustawienie poniżej), dzienniki stdout zostaną zebrane od wszystkich kontenerów we wszystkich zasobników/węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Tablica rozdzielonych przecinkami |Tablica przestrzeni nazw Kubernetes, które strumienia wyjściowego stdout dzienniki nie będą zbierane. To ustawienie działa tylko wtedy, gdy `log_collection_settings.stdout.enabled` ustawiono `true`. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | wartość PRAWDA lub FAŁSZ |Kontroluje to, czy jest włączone zbieranie danych dziennika kontenera stderr. Po ustawieniu `true` i nie przestrzeni nazw są wyłączone zbieranie danych dziennika stdout (`log_collection_settings.stderr.exclude_namespaces` ustawienie), dzienniki stderr zostaną zebrane od wszystkich kontenerów we wszystkich zasobników/węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Tablica rozdzielonych przecinkami |Tablica przestrzeni nazw Kubernetes, dla których stderr dzienniki nie będą zbierane. To ustawienie działa tylko wtedy, gdy `log_collection_settings.stdout.enabled` ustawiono `true`. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | wartość PRAWDA lub FAŁSZ | Ta opcja kontroluje po włączeniu kolekcji zmiennych środowiskowych. Po ustawieniu `false`, żadne zmienne środowiskowe nie są zbierane dla dowolnego kontenera z systemem we wszystkich zasobników/węzłach w klastrze. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Skonfiguruj i Wdroż ConfigMaps

Wykonaj poniższe kroki, aby skonfigurować i wdrożyć ConfigMap pliku konfiguracji do klastra.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) yaml ConfigMap szablonu pliku i zapisz go jako kontener azm-ms-agentconfig.yaml.  
1. Edytuj plik yaml ConfigMap o dostosowania. 

    - Aby wykluczyć określone przestrzenie nazw do zbierania dzienników stdout, należy skonfigurować klucz/wartość, korzystając z następującego przykładu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Aby wyłączyć kolekcji zmiennych środowiskowych dla określonego kontenera, Ustaw klucz/wartość `[log_collection_settings.env_var] enabled = true` można włączyć ją globalnie zmiennych kolekcji, a następnie postępuj zgodnie z instrukcjami [tutaj](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) pełną konfigurację do określonego kontenera.
    - Aby wyłączyć zbieranie danych dziennika stderr całego klastra, należy skonfigurować klucz/wartość, korzystając z następującego przykładu: `[log_collection_settings.stderr] enabled = false`.

1. Utwórz ConfigMap, uruchamiając następujące polecenie kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Zmiana konfiguracji może potrwać kilka minut, aby zakończyć działanie przed zastosowaniem i wszystkich zasobników omsagent w klastrze zostanie uruchomiony ponownie. Ponowne uruchomienie jest ponowne uruchomienie wszystkich zasobników omsagent stopniowe, nie wszystkie uruchomione ponownie w tym samym czasie. Po zakończeniu powoduje ponowne uruchomienie, zostanie wyświetlony komunikat podobny do poniższego, który zawiera wynik: `configmap "container-azm-ms-agentconfig" created`.

Aby sprawdzić, konfiguracja została pomyślnie zastosowana, użyj następującego polecenia, aby przejrzeć dzienniki z zasobników agenta: `kubectl logs omsagent-fdf58 -n=kube-system`. Jeśli występują błędy konfiguracji z zasobników osmagent, dane wyjściowe będą wyświetlane błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy uniemożliwić omsagent podczas analizowania pliku, co powoduje ponowne uruchomienie i używania domyślnej konfiguracji. Po naprawieniu błędów na ConfigMap Zapisz plik yaml i Zastosuj zaktualizowane ConfigMaps, uruchamiając polecenie: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Stosowanie aktualizacji ConfigMap

Jeśli wdrożono już ConfigMap klastra i chce zaktualizować je przy użyciu nowszej konfiguracji, możesz po prostu plik można edytować ConfigMap była wcześniej używana, a następnie zastosować, tak jak poprzednio, za pomocą tego samego polecenia `kubectl apply -f <configmap_yaml_file.yaml`.

Zmiana konfiguracji może potrwać kilka minut, aby zakończyć działanie przed zastosowaniem i wszystkich zasobników omsagent w klastrze zostanie uruchomiony ponownie. Ponowne uruchomienie jest ponowne uruchomienie wszystkich zasobników omsagent stopniowe, nie wszystkie uruchomione ponownie w tym samym czasie. Po zakończeniu powoduje ponowne uruchomienie, zostanie wyświetlony komunikat podobny do poniższego, który zawiera wynik: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Sprawdzanie wersji schematu

Config obsługiwane wersje schematu są dostępne jako adnotacja zasobnika (wersje schematu) w zasobniku omsagent. Można je wyświetlić, używając następującego polecenia kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Dane wyjściowe będą pokazywały podobny do poniższego, przy użyciu wersji schematów adnotacji:

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

## <a name="next-steps"></a>Kolejne kroki

- Aby kontynuować, jak używać usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
- Widok [dziennika przykłady zapytań](container-insights-log-search.md#search-logs-to-analyze-data) wstępnie zdefiniowane zapytania i przykłady do oceny lub Dostosuj alerty, wizualizacji i analizowanie klastry usługi.
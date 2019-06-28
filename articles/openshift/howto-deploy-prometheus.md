---
title: Wdrażanie autonomicznego Prometheus w klastrze usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono sposób utworzyć wystąpienie Prometheus w klastrze usługi Azure Red Hat OpenShift, aby monitorować metryki aplikacji.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metryki i systemem red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342928"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Wdrażanie autonomicznego Prometheus w klastrze usługi Azure Red Hat OpenShift

Tego przewodnika opisano sposób konfigurowania autonomicznego Prometheus przy użyciu odnajdowania usług w klastrze usługi Azure Red Hat OpenShift.  "Klient admin" dostęp do klastra nie jest wymagana.

Konfiguracja docelowej jest w następujący sposób:

- jeden projekt (prometheus projekt), który zawiera Prometheus i Alertmanager
- dwa projekty (projektu project1 aplikacji i project2 aplikacji), zawierające aplikacje do monitorowania

Umożliwią przygotowanie niektórych Prometheus konfiguracji plików lokalnie. Utwórz nowy folder do ich przechowywania.
Te pliki konfiguracji będą przechowywane w klastrze jako wpisy tajne w przypadku, gdy tokeny klucza tajnego później zostaną dodane do nich.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>Krok 1: Zaloguj się do klastra przy użyciu `oc` narzędzia
Korzystając z przeglądarki internetowej przejdź do konsoli sieci web klastra (https://openshift. *losowy identyfikator*. *region*. azmosa.io).
Zaloguj się przy użyciu poświadczeń platformy Azure.
Kliknij swoją nazwę użytkownika w prawym górnym rogu i wybierz pozycję "Kopiuj polecenie logowania". Wklej go w terminalu, które będą używane.

Możesz sprawdzić, jeśli zalogowano do poprawne klastra za pomocą `oc whoami -c` polecenia.

## <a name="step-2-prepare-the-projects"></a>Krok 2: Przygotowanie projektów

Tworzenie projektów.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Można użyć `-n` lub `--namespace` parametru lub zaznacz aktywnego projektu o `oc project` polecenia

## <a name="step-3-prepare-prometheus-config"></a>Krok 3: Przygotowywanie konfiguracji Prometheus
Utwórz plik o nazwie prometheus.yml o następującej zawartości.
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Utwórz klucz tajny o nazwie "prom" z konfiguracją.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Plik wymienionych powyżej jest podstawowy plik config Prometheus.
On ustawia odstępach czasu i umożliwia skonfigurowanie automatycznego odnajdywania w trzy projekty (prometheus projektu, projektu project1 aplikacji project2 aplikacji).
W tym przykładzie automatycznie odnalezione punkty końcowe zostanie pozyskany za pośrednictwem protokołu HTTP, bez uwierzytelniania.
Aby uzyskać więcej informacji na temat oskrobaniu punktów końcowych, zobacz https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>Krok 4: Przygotowywanie konfiguracji Alertmanager
Utwórz plik o nazwie alertmanager.yml o następującej zawartości.
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Utwórz klucz tajny o nazwie "prom — alerty" z konfiguracją.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Plik wymienionych powyżej jest plikiem konfiguracji Menedżer alertów.

> [!NOTE]
> Możesz sprawdzić dwa poprzednie kroki z `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>Krok 5. Rozpocznij Prometheus i Alertmanager
Pobierz [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) szablonu z [openshift/repozytorium origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) i zastosować je w projekcie prometheus
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Plik prometheus standalone.yaml jest OpenShift szablon, który utworzy wystąpienie Prometheus przy użyciu serwera proxy protokołu oauth, przed jej i wystąpieniem Alertmanager, również jest zabezpieczony za pomocą serwera proxy protokołu oauth.  W tym szablonie serwera proxy protokołu oauth jest skonfigurowany do zezwalania, użytkownik może "get" przestrzeni nazw "prometheus projektu" (zobacz `-openshift-sar` flagi).

> [!NOTE]
> Możesz sprawdzić, czy "prom" StatefulSet ma taki sam *ŻĄDANEGO* i *bieżącego* liczba replik z `oc get statefulset -n prometheus-project` polecenia.
> Możesz również sprawdzić wszystkie zasoby w projekcie z `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>Krok 6: Dodaj uprawnienia, aby umożliwić odnajdowanie usługi
Utwórz prometheus sdrole.yml o następującej zawartości.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Zastosuj szablon do wszystkich projektów, w którym chcesz umożliwić odnajdowanie usługi.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Jeśli chcesz również Prometheus mieć możliwość gromadzenia metryk, od siebie samego, pamiętaj, aby zastosować uprawnienia w projekcie prometheus zbyt.

> [!NOTE]
> Możesz sprawdzić, jeśli rola i RoleBinding zostały prawidłowo utworzona za pomocą `oc get role` i `oc get rolebinding` odpowiednio poleceń

## <a name="optional-deploy-example-application"></a>Opcjonalnie: Wdrażanie przykładowej aplikacji
Wszystko działa, ale brak źródeł metryki. Przejdź do adresu URL Prometheus (https://prom-prometheus-project.apps. *losowy identyfikator*. *region*.azmosa.io/), które znajdują się za pomocą następującego polecenia.
```
oc get route prom -n prometheus-project
```
Pamiętaj, aby prefiks nazwy hosta z https://.

**Stan > odnajdowania usługi** strony wyświetli aktywnych elementów docelowych: 0/0.

Aby wdrożyć przykładową aplikacją, która udostępnia metryki podstawowe języka python w obszarze /metrics punktu końcowego, uruchom następujące polecenia.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nowe aplikacje pojawi się jako prawidłowy elementy docelowe, na stronie odnajdowania usług w ciągu 30 sekund po wdrożeniu. Więcej informacji można uzyskać **Stan > cele** strony.

> [!NOTE]
> Dla każdego celu pomyślnie scraped Prometheus dodaje metryki "nawet" punktu danych. Kliknij przycisk **Prometheus** w lewym górnym rogu, a następnie wprowadź "up" jako wyrażenie i kliknij przycisk **Execute**.

## <a name="next-steps"></a>Kolejne kroki

Niestandardowej Instrumentacji Prometheus można dodać do aplikacji.

Biblioteki klienckie Prometheus, która upraszcza przygotowywania metryki Prometheus, jest gotowy dla różnych języków programowania.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Z rzeczywistym użyciem https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby

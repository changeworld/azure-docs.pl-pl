---
title: Wdrażanie wystąpienia Prometheus autonomicznego klastra usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Utwórz wystąpienie Prometheus w klastrze usługi Azure Red Hat OpenShift w taki sposób, aby monitorować metryki aplikacji.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metryki i systemem red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827022"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Wdrażanie wystąpienia Prometheus autonomicznego klastra usługi Azure Red Hat OpenShift

W tym artykule opisano sposób konfigurowania wystąpienia Prometheus autonomicznego, który używa odnajdowania usług w klastrze usługi Azure Red Hat OpenShift.

> [!NOTE]
> Odbiorcy administratora dostęp do klastra Azure Red Hat OpenShift nie jest wymagana.

Konfiguracja docelowej:

- Jeden projekt (prometheus projekt), który zawiera Prometheus i Alertmanager.
- Dwa projekty projektu project1 aplikacji i project2 aplikacji, które zawierają aplikacji do monitorowania.

Umożliwią przygotowanie niektórych Prometheus konfiguracji plików lokalnie. Utwórz nowy folder do ich przechowywania. Pliki konfiguracji są przechowywane w klastrze jako wpisy tajne, w przypadku, gdy tokeny klucza tajnego później zostaną dodane do klastra.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Zaloguj się do klastra za pomocą narzędzia oC.

1. Otwórz przeglądarkę sieci web, a następnie przejdź do konsoli sieci web klastra (https://openshift. *losowy identyfikator*. *region*. azmosa.io).
2. Zaloguj się przy użyciu poświadczeń platformy Azure.
3. Wybierz swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz **polecenie logowania kopiowania**.
4. Wklej swoją nazwę użytkownika w terminalu, który będzie używany.

> [!NOTE]
> Aby zobaczyć, jeśli zarejestrujesz się w klastrze poprawne, uruchom `oc whoami -c` polecenia.

## <a name="prepare-the-projects"></a>Przygotowanie projektów

Do tworzenia projektów, uruchom następujące polecenia:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Można użyć `-n` lub `--namespace` parametru lub Wybierz aktywny projekt, uruchamiając `oc project` polecenia.

## <a name="prepare-the-prometheus-configuration-file"></a>Przygotuj plik konfiguracyjny Prometheus
Utwórz plik prometheus.yml, wprowadzając następującą zawartością:
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
Utwórz klucz tajny o nazwie Prom, wprowadzając następującą konfigurację:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Plik prometheus.yml jest podstawowy plik konfiguracji Prometheus. On ustawia odstępach czasu i umożliwia skonfigurowanie automatycznego odnajdywania w trzy projekty (prometheus projektu, projektu project1 aplikacji project2 aplikacji). Automatycznie wykryty punktów końcowych, które są w pliku konfiguracyjnym poprzedniej pozyskany za pośrednictwem protokołu HTTP bez uwierzytelniania.

Aby uzyskać więcej informacji na temat oskrobaniu punktów końcowych, zobacz [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Przygotowanie pliku config Alertmanager
Utwórz plik alertmanager.yml, wprowadzając następującą zawartością:
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
Utwórz klucz tajny o nazwie Prom alerty, wprowadzając następującą konfigurację:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml jest plikiem konfiguracji Menedżer alertów.

> [!NOTE]
> Aby sprawdzić, czy dwa poprzednie kroki, uruchom `oc get secret -n prometheus-project` polecenia.

## <a name="start-prometheus-and-alertmanager"></a>Rozpocznij Prometheus i Alertmanager
Przejdź do [openshift/repozytorium origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) i Pobierz [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) szablonu. Zastosuj szablon do projektu prometheus, wprowadzając następującą konfigurację:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Plik prometheus standalone.yaml jest szablonem OpenShift. Wystąpienie Prometheus zostanie utworzony przy użyciu serwera proxy protokołu oauth przed jej i wystąpieniem Alertmanager, również jest zabezpieczony za pomocą serwera proxy protokołu oauth. W tym szablonie serwera proxy protokołu oauth jest skonfigurowany do zezwalania, użytkownik może "get" przestrzeni nazw projektu prometheus (zobacz `-openshift-sar` flagi).

> [!NOTE]
> Aby sprawdzić, czy prom StatefulSet zawiera ŻĄDANEGO równe i BIEŻĄCĄ liczbę replik, uruchom `oc get statefulset -n prometheus-project` polecenia. Aby sprawdzić wszystkie zasoby w projekcie, należy uruchomić `oc get all -n prometheus-project` polecenia.

## <a name="add-permissions-to-allow-service-discovery"></a>Dodaj uprawnienia, aby umożliwić odnajdowanie usługi

Utwórz plik prometheus sdrole.yml, wprowadzając następującą zawartością:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
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
Aby zastosować szablon do wszystkich projektów, które chcesz, aby umożliwić odnajdowanie usługi, uruchom następujące polecenia:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Aby Prometheus zbieranie metryk z samego, zastosuj uprawnienia w projekcie prometheus.

> [!NOTE]
> Aby sprawdzić, czy rola i RoleBinding zostały utworzone prawidłowo, uruchom `oc get role` i `oc get rolebinding` poleceń.

## <a name="optional-deploy-example-application"></a>Opcjonalnie: Wdrażanie przykładowej aplikacji

Wszystko działa, ale brak źródeł metryki. Przejdź do adresu URL Prometheus (https://prom-prometheus-project.apps. *losowy identyfikator*. *region*.azmosa.io/). Możesz go znaleźć za pomocą następującego polecenia:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Pamiętaj, aby dodać prefiks https:// na początku nazwy hosta.

**Stan > odnajdowania usługi** strony wyświetli aktywnych elementów docelowych: 0/0.

Aby wdrożyć przykładową aplikacją, która udostępnia podstawowe metryki języka Python w ramach punktu końcowego /metrics, uruchom następujące polecenia:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nowe aplikacje powinna zostać wyświetlona jako prawidłowe elementy docelowe, na stronie odnajdowania usług w ciągu 30 sekund po wdrożeniu.

Aby uzyskać więcej informacji, wybierz **stan** > **cele**.

> [!NOTE]
> Dla każdego pomyślnie scraped obiektu docelowego Prometheus dodaje punkt danych w górę metryki. Wybierz **Prometheus** w lewym górnym rogu, wprowadź **się** jako wyrażenie, a następnie wybierz **Execute**.

## <a name="next-steps"></a>Następne kroki

Niestandardowej Instrumentacji Prometheus można dodać do aplikacji. Biblioteki klienckie Prometheus, która upraszcza Prometheus metryki przygotowania, jest gotowy dla różnych języków programowania.

Aby uzyskać więcej informacji zobacz następujące biblioteki usługi GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)

---
title: Wdrażanie wystąpienia Prometeusza w klastrze OpenShift usługi Azure Red Hat
description: Utwórz wystąpienie Prometheus w klastrze Azure Red Hat OpenShift, aby monitorować metryki aplikacji.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometeusz, aro, openshift, metryki, czerwony kapelusz
ms.openlocfilehash: 7f22df587f51af735e0ea663e53f6eef14d60692
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886892"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Wdrażanie autonomicznego wystąpienia Prometeusza w klastrze OpenShift usługi Azure Red Hat

W tym artykule opisano sposób konfigurowania autonomicznego wystąpienia Prometheus, które używa odnajdowania usługi w klastrze OpenShift usługi Azure Red Hat.

> [!NOTE]
> Dostęp administratora klienta do klastra OpenShift Red Hat platformy Azure nie jest wymagany.

Konfiguracja docelowa:

- Jeden projekt (prometheus-project), który zawiera Prometheus i Alertmanager.
- Dwa projekty (app-project1 i app-project2), które zawierają aplikacje do monitorowania.

Przygotujesz kilka plików konfiguracyjnych Prometheus lokalnie. Utwórz nowy folder, aby je przechowywać. Pliki konfiguracyjne są przechowywane w klastrze jako wpisy tajne, w przypadku, gdy tajne tokeny są dodawane później do klastra.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Logowanie się do klastra przy użyciu narzędzia OC

1. Otwórz przeglądarkę internetową, a następnie przejdź dohttps://openshiftkonsoli internetowej klastra ( .* random-id*. *(azmosa.io).*
2. Zaloguj się przy użyciu poświadczeń platformy Azure.
3. Wybierz swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz **polecenie kopiuj polecenie logowania**.
4. Wklej swoją nazwę użytkownika do terminalu, którego użyjesz.

> [!NOTE]
> Aby sprawdzić, czy zalogowano się do właściwego klastra, uruchom `oc whoami -c` polecenie.

## <a name="prepare-the-projects"></a>Przygotowanie projektów

Aby utworzyć projekty, uruchom następujące polecenia:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Można użyć `-n` lub `--namespace` parametru lub wybrać aktywny projekt, `oc project` uruchamiając polecenie.

## <a name="prepare-the-prometheus-configuration-file"></a>Przygotowanie pliku konfiguracyjnego Prometheus
Utwórz plik prometheus.yml, wprowadzając następującą zawartość:
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

Plik prometheus.yml jest podstawowym plikiem konfiguracyjnym Prometheus. Ustawia interwały i konfiguruje automatyczne odnajdowanie w trzech projektach (prometheus-project, app-project1, app-project2). W poprzednim pliku konfiguracji automatycznie odnalezione punkty końcowe są zeskrobane za pośrednictwem protokołu HTTP bez uwierzytelniania.

Aby uzyskać więcej informacji na temat skrobania punktów końcowych, zobacz [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Przygotowywanie pliku konfiguracyjnego alertmanager
Utwórz plik alertmanager.yml, wprowadzając następującą zawartość:
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
Utwórz klucz tajny o nazwie Prom-Alerts, wprowadzając następującą konfigurację:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml jest plikiem konfiguracyjnym Menedżera alertów.

> [!NOTE]
> Aby zweryfikować dwa poprzednie `oc get secret -n prometheus-project` kroki, uruchom polecenie.

## <a name="start-prometheus-and-alertmanager"></a>Start Prometeusz i Alertmanager
Przejdź do [repozytorium openshift/origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) i pobierz szablon [prometheus-standalone.yaml.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) Zastosuj szablon do projektu prometheus, wprowadzając następującą konfigurację:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Plik prometheus-standalone.yaml jest szablonem OpenShift. Utworzy wystąpienie Prometheus z oauth-proxy przed nim i wystąpienie Alertmanager, również zabezpieczone oauth-proxy. W tym szablonie oauth-proxy jest skonfigurowany tak, aby umożliwić każdemu użytkownikowi, który może `-openshift-sar` "uzyskać" obszar nazw projektu prometheus (zobacz flagę).

> [!NOTE]
> Aby sprawdzić, czy prom StatefulSet ma równe repliki `oc get statefulset -n prometheus-project` numerów DESIRED i CURRENT, uruchom polecenie. Aby sprawdzić wszystkie zasoby w `oc get all -n prometheus-project` projekcie, uruchom polecenie.

## <a name="add-permissions-to-allow-service-discovery"></a>Dodawanie uprawnień umożliwiających odnajdowanie usługi

Utwórz plik prometheus-sdrole.yml, wprowadzając następującą zawartość:
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
Aby zastosować szablon do wszystkich projektów, z których chcesz zezwolić na odnajdowanie usługi, uruchom następujące polecenia:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Aby sprawdzić, czy role i RoleBinding `oc get role` `oc get rolebinding` zostały utworzone poprawnie, uruchom polecenia i.

## <a name="optional-deploy-example-application"></a>Opcjonalnie: Wdrażanie przykładu aplikacji

Wszystko działa, ale nie ma źródeł metryk. Przejdź do adresu URL Prometheus (https://prom-prometheus-project.apps.* random-id*. *(azmosa.io/).* Można go znaleźć za pomocą następującego polecenia:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Pamiętaj, aby dodać prefiks https:// na początku nazwy hosta.

Na stronie **Odnajdowanie stanu > usługi** zostanie wyświetlona 0/0 aktywnych obiektów docelowych.

Aby wdrożyć przykładową aplikację, która udostępnia podstawowe metryki języka Python w punkcie końcowym /metrics, uruchom następujące polecenia:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nowe aplikacje powinny pojawić się jako prawidłowe obiekty docelowe na stronie odnajdywania usług w ciągu 30 sekund po wdrożeniu.

Aby uzyskać więcej informacji, wybierz pozycję**Cele** **stanu** > .

> [!NOTE]
> Dla każdego pomyślnie zeskrobane cel Prometheus dodaje punkt danych w metryki up. Wybierz **Prometeusz** w lewym górnym rogu, wprowadź **jako** wyrażenie, a następnie wybierz pozycję **Wykonaj**.

## <a name="next-steps"></a>Następne kroki

Do aplikacji można dodać niestandardowe instrumentamencie Prometheus. Biblioteka klienta Prometheus, która upraszcza przygotowanie metryk Prometheus, jest gotowa na różne języki programowania.

Aby uzyskać więcej informacji, zobacz następujące biblioteki GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Przejdź](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)

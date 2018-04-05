---
title: Samouczek dla usługi Kubernetes na platformie Azure — monitorowanie usługi Kubernetes
description: Samouczek AKS — monitorowanie usługi Kubernetes przy użyciu usługi Azure Log Analytics
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 86ae0c5ab302c49fa58df887d9dffef6cec31708
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-monitor-azure-container-service-aks"></a>Samouczek: monitorowanie usługi Azure Container Service (AKS)

Monitorowanie klastra i kontenerów usługi Kubernetes ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego o dużej skali i z wieloma aplikacjami.

W tym samouczku przedstawiono konfigurowanie monitorowania klastra usługi AKS przy użyciu [rozwiązania do obsługi kontenerów dla usługi Log Analytics][log-analytics-containers].

Ten samouczek (część 7 z 8) obejmuje następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie rozwiązania do monitorowania kontenerów
> * Konfigurowanie agentów monitorowania
> * Dostęp do informacji monitorowania w witrynie Azure Portal

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazów kontenera, obrazy zostały przekazane do usługi Azure Container Registry i utworzono klaster usługi Kubernetes.

Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

## <a name="configure-the-monitoring-solution"></a>Konfigurowanie rozwiązania do monitorowania

W witrynie Azure Portal wybierz polecenie **Utwórz zasób** i wyszukaj pozycję `Container Monitoring Solution`. Gdy ją znajdziesz, wybierz polecenie **Utwórz**.

![Dodanie rozwiązania](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Utwórz nowy obszar roboczy usługi Log Analytics lub wybierz istniejący obszar roboczy. Formularz obszaru roboczego usługi Log Analytics przeprowadzi Cię przez ten proces.

Podczas tworzenia obszaru roboczego wybierz polecenie **Przypnij do pulpitu nawigacyjnego**, co ułatwi dostęp do niego.

![Obszar roboczy usługi Log Analytics](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Po zakończeniu wybierz polecenie **Zamknij**. Po zakończeniu walidacji wybierz polecenie **Utwórz**, aby utworzyć rozwiązanie do monitorowania kontenerów.

Po utworzeniu obszaru roboczego będzie on dostępny w witrynie Azure Portal.

## <a name="get-workspace-settings"></a>Pobieranie ustawień obszaru roboczego

Identyfikator i klucz obszaru roboczego usługi Log Analytics są wymagane do skonfigurowania agenta rozwiązania w węzłach usługi Kubernetes.

Aby pobrać te wartości, wybierz pozycję **Obszar roboczy OMS** z menu po lewej stronie rozwiązania do monitorowania kontenerów. Wybierz pozycję **Ustawienia zaawansowane** i zanotuj wartości **IDENTYFIKATOR OBSZARU ROBOCZEGO** i **KLUCZ PODSTAWOWY**.

## <a name="create-kubernetes-secret"></a>Tworzenie wpisu tajnego rozwiązania Kubernetes

Zapisz ustawienia obszaru roboczego usługi Log Analytics we wpisie tajnym rozwiązania Kubernetes o nazwie `omsagent-secret` za pomocą polecenia [kubectl create secret][kubectl-create-secret]. Zaktualizuj element `WORKSPACE_ID` za pomocą identyfikatora obszaru roboczego usługi Log Analytics, a element `WORKSPACE_KEY` za pomocą klucza obszaru roboczego.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="configure-monitoring-agents"></a>Konfigurowanie agentów monitorowania

Do skonfigurowania agentów monitorowania kontenerów w klastrze Kubernetes można użyć następującego pliku manifestu usługi Kubernetes. Umożliwia on utworzenie elementu [DaemonSet][kubernetes-daemonset] usługi Kubernetes, który uruchamia pojedynczy zasobnik w każdym węźle klastra.

Zapisz poniższy tekst do pliku o nazwie `oms-daemonset.yaml`.

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers 
          name: containerlog-path  
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux    
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"     
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log 
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers    
```

Utwórz element DaemonSet przy użyciu następującego polecenia:

```azurecli
kubectl create -f oms-daemonset.yaml
```

Aby sprawdzić, czy element DaemonSet został utworzony, uruchom polecenie:

```azurecli
kubectl get daemonset
```

Dane wyjściowe będą podobne do następujących:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Po uruchomieniu agentów pozyskanie i przetworzenie danych przez usługę Log Analytics trwa kilka minut.

## <a name="access-monitoring-data"></a>Dostęp do danych monitorowania

W witrynie Azure Portal wybierz obszar roboczy usługi Log Analytics przypięty do pulpitu nawigacyjnego portalu. Kliknij kafelek **Rozwiązanie do monitorowania kontenerów**. Tutaj możesz znaleźć informacje o klastrze usługi AKS i kontenerach w klastrze.

![Pulpit nawigacyjny](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Szczegółowy przewodnik dotyczący odpytywania i analizowania danych monitorowania znajduje się w [dokumentacji usługi Azure Log Analytics][log-analytics-docs].

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób monitorowania klastra Kubernetes za pomocą usługi Log Analytics. Wykonano następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie rozwiązania do monitorowania kontenerów
> * Konfigurowanie agentów monitorowania
> * Dostęp do informacji monitorowania w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak uaktualnić usługę Kubernetes do nowej wersji.

> [!div class="nextstepaction"]
> [Upgrade Kubernetes (Uaktualnianie usługi Kubernetes)][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubectl-create-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml

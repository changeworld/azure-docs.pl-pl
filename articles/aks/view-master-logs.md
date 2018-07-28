---
title: Wyświetlanie dzienników kontrolera Azure Kubernetes Service (AKS)
description: Dowiedz się, jak włączanie i wyświetlanie dzienników dla węzła głównego Kubernetes w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 07/26/2018
ms.author: iainfou
ms.openlocfilehash: 05bfa4a65098245c43b97af296bf6f26950d018f
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330946"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Włączyć i przejrzeć Kubernetes węzła głównego dzienników w usłudze Azure Kubernetes Service (AKS)

Za pomocą usługi Azure Kubernetes Service (AKS), podstawowe składniki, takie jak *apiserver klastra kubernetes w usłudze* i *Menedżera klastra kubernetes w usłudze kontrolera* są dostarczane jako usługa zarządzana. Tworzenie i zarządzanie węzłami, które są uruchamiane *agenta kubelet* i kontener środowiska uruchomieniowego i wdrażanie aplikacji za pośrednictwem serwera zarządzanego interfejsu API rozwiązania Kubernetes. Aby ułatwić rozwiązywanie problemów z aplikacji i usług, może być konieczne wyświetlanie dzienników generowanych przez te składniki. W tym artykule pokazano, jak włączyć i wykonywania zapytań względem dzienników z podstawowe składniki usługi Kubernetes za pomocą usługi Azure Log Analytics.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga istniejącego klastra AKS uruchomiony na Twoim koncie platformy Azure. Jeśli nie masz już klaster AKS, utwórz ją przy użyciu [wiersza polecenia platformy Azure] [ cli-quickstart] lub [witryny Azure portal][portal-quickstart]. Usługa log Analytics współpracuje z obu RBAC i RBAC innych włączone klastrów usługi AKS.

## <a name="enable-diagnostics-logs"></a>Włączanie dzienników diagnostycznych

Aby zebrać i przejrzeć dane z wielu źródeł, usługa Log Analytics oferuje zapytania języka i aparat analityczny, które dają wgląd w danym środowisku. Obszar roboczy jest używana do sortowania i analizować dane i można integrować z innymi usługami platformy Azure, takich jak usługa Application Insights i Centrum zabezpieczeń. Aby użyć innej platformy do analizy dzienników, możesz zamiast tego Wyślij dzienniki diagnostyczne do Centrum konta lub zdarzenia usługi Azure storage. Aby uzyskać więcej informacji, zobacz [co to jest Azure Log Analytics?] [log-analytics-overview].

Usługa log Analytics jest włączona i zarządzane w witrynie Azure portal. Aby włączyć zbieranie dzienników dla składniki klastra AKS rozwiązania Kubernetes, otwórz Azure portal w przeglądarce sieci web, a następnie wykonaj następujące czynności:

1. Wybierz grupę zasobów klastra usługi AKS, takich jak *myResourceGroup*. Nie wybieraj grupę zasobów, która zawiera poszczególne zasoby klastra AKS, takie jak *MC_myResourceGroup_myAKSCluster_eastus*.
1. Po lewej stronie wybierz **ustawień diagnostycznych**.
1. Wybierz klaster AKS, taką jak *myAKSCluster*, następnie **Włącz diagnostykę**.
1. Wprowadź nazwę, taką jak *myAKSLogs*, następnie wybierz opcję, aby **wysyłanie do usługi Log Analytics**.
    * Możliwość *Konfiguruj* usługi Log Analytics, a następnie wybierz istniejący obszar roboczy lub **Utwórz nowy obszar roboczy**.
    * Jeśli musisz utworzyć obszar roboczy, podaj nazwę, grupę zasobów i lokalizacji.
1. Na liście dostępnych dzienników, wybierz pozycję dzienniki, które ma zostać włączone, takie jak *apiserver klastra kubernetes w usłudze*, *Menedżera klastra kubernetes w usłudze kontrolera*, i *klastra kubernetes w usłudze scheduler*. Możesz wrócić i zmienić zebranych dzienników po włączeniu usługi Log Analytics.
1. Gdy wszystko będzie gotowe, wybierz **Zapisz** Aby włączyć zbieranie dzienników wybrane.

W poniższym przykładzie przedstawiono zrzut ekranu portalu *ustawień diagnostycznych* okna, a następnie opcję utworzenia obszaru roboczego pakietu OMS:

![Włącz obszar roboczy pakietu OMS dla klastra usługi AKS w Log Analytics](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Harmonogram zasobnik testowych w klastrze usługi AKS

Aby wygenerować niektórych dzienników, należy utworzyć nowych zasobników w klastrze AKS. Następujące manifest YAML przykład może służyć do tworzenia podstawowe wystąpienie serwera NGINX. Utwórz plik o nazwie `nginx.yaml` w edytorze i wklej następującą zawartością:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: myfrontend
    image: nginx
    ports:
    - containerPort: 80
```

Utwórz zasobnik za pomocą [tworzenie kubectl] [ kubectl-create] polecenia i określić pliku YAML, jak pokazano w poniższym przykładzie:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Wyświetlanie zebranych dzienników

Może upłynąć kilka minut, zanim dzienniki diagnostyczne można włączyć i wyświetlane w obszarze roboczym pakietu OMS. W witrynie Azure portal, wybierz grupę zasobów dla Twojego obszaru roboczego usługi Log Analytics, takie jak *myResourceGroup*, następnie wybierz zasób usługi Log Analytics, takie jak *myAKSLogs*.

![Wybierz obszar roboczy usługi Log Analytics dla klastra usługi AKS](media/view-master-logs/select-log-analytics-workspace.png)

Po lewej stronie wybierz **wyszukiwanie w dzienniku**. Aby wyświetlić *apiserver klastra kubernetes w usłudze*, wprowadź następujące zapytanie w polu tekstowym:

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| project log_s
```

Wiele dzienników prawdopodobnie są zwracane do serwera interfejsu API. Aby określić zakres szczegółów zapytania, aby wyświetlić dzienniki dotyczące serwera NGINX pod, utworzony w poprzednim kroku, dodać kolejny *gdzie* instrukcję, aby wyszukać *zasobników/nginx* pokazany na poniższe przykładowe zapytanie:

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Określonych dzienników w celu tym zasobniku NGINX są wyświetlane, jak pokazano na poniższym zrzucie ekranu przykładu:

![Wyniki dla przykładowych serwera NGINX pod zapytań usługi log Analytics](media/view-master-logs/log-analytics-query-results.png)

Aby wyświetlić dodatkowe dzienniki, należy zaktualizować zapytanie dla *kategorii* nazwy do *Menedżera klastra kubernetes w usłudze kontrolera* lub *klastra kubernetes w usłudze scheduler*, zależności od tego, jakie dodatkowe loguje użytkownika Włącz. Dodatkowe *gdzie* instrukcji, następnie może służyć do zaktualizowania zdarzenia, którego szukasz.

Aby uzyskać więcej informacji na temat sposobu wykonywania zapytań i filtrowania danych dziennika, zobacz [wyświetlanie i analizowanie danych zebranych przez wyszukiwanie w dziennikach usługi Log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schemat zdarzeń dziennika

Aby ułatwić analizowanie danych dzienników, w poniższej tabeli przedstawiono Schemat używany podczas każdego zdarzenia:

| Nazwa pola               | Opis |
|--------------------------|-------------|
| *resourceId*             | Zasobów platformy Azure, który dziennika |
| *czas*                   | Sygnatura czasowa po dziennika został przekazany |
| *category*               | Nazwa kontenera/składnika Generowanie dziennika |
| *OperationName*          | Zawsze *Microsoft.ContainerService/managedClusters/diagnositicLogs/Read* |
| *Properties.log*         | Pełny tekst dziennika ze składnika |
| *Properties.stream*      | *strumienia wyjściowego stderr* lub *stdout* |
| *Properties.pod*         | Nazwa zasobnika dziennik pochodzi z |
| *properties.containerID* | Identyfikator kontenera platformy docker, z którego pochodzi ten dziennik |

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób włączyć i przejrzeć dzienniki Aby uzyskać składniki klastra AKS rozwiązania Kubernetes. Monitorowanie i rozwiązywanie problemów z dalszych, możesz również [wyświetlanie dzienników agenta Kubelet] [ kubelet-logs] i [włączyć dostęp do węzła SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../log-analytics/log-analytics-tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: aks-ssh.md
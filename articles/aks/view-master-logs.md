---
title: Wyświetlanie dzienników kontrolera usługi Azure Kubernetes (AKS)
description: Dowiedz się, jak włączyć i wyświetlić dzienniki węzła głównego usługi Kubernetes w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259385"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Włączanie i wyświetlanie dzienników węzła master platformy Kubernetes w usłudze Azure Kubernetes Service

Dzięki usłudze Azure Kubernetes Service (AKS) składniki główne, takie jak *kube-apiserver* i *kube-controller-manager,* są dostarczane jako usługa zarządzana. Tworzenie i zarządzanie węzłami, które uruchamiają środowisko uruchomieniowe *kubelet* i kontenera i wdrażaj aplikacje za pośrednictwem zarządzanego serwera interfejsu API kubernetes. Aby ułatwić rozwiązywanie problemów z aplikacją i usługami, może być konieczne wyświetlenie dzienników generowanych przez te składniki główne. W tym artykule pokazano, jak używać dzienników usługi Azure Monitor do włączania i przeszukiwania dzienników ze składników wzorcowych usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga istniejącego klastra AKS uruchomionego na koncie platformy Azure. Jeśli nie masz jeszcze klastra AKS, utwórz go przy użyciu interfejsu [wiersza polecenia platformy Azure][cli-quickstart] lub [witryny Azure portal][portal-quickstart]. Dzienniki usługi Azure Monitor działają zarówno z klastrami AKS obsługującymi rbac, jak i nieobjętym funkcją RBAC.

## <a name="enable-diagnostics-logs"></a>Włączanie dzienników diagnostycznych

Aby ułatwić zbieranie i przeglądanie danych z wielu źródeł, dzienniki usługi Azure Monitor udostępnia język zapytań i aparat analizy, który zapewnia szczegółowe informacje w danym środowisku. Obszar roboczy służy do sortowania i analizowania danych i może integrować się z innymi usługami platformy Azure, takimi jak usługa Application Insights i Centrum zabezpieczeń. Aby użyć innej platformy do analizowania dzienników, można zamiast tego wybrać opcję wysyłania dzienników diagnostycznych do konta magazynu platformy Azure lub centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Co to są dzienniki usługi Azure Monitor?][log-analytics-overview].

Dzienniki usługi Azure Monitor są włączone i zarządzane w witrynie Azure portal. Aby włączyć zbieranie dzienników dla składników wzorcowych usługi Kubernetes w klastrze AKS, otwórz witrynę Azure portal w przeglądarce sieci Web i wykonaj następujące kroki:

1. Wybierz grupę zasobów dla klastra AKS, taką jak *myResourceGroup*. Nie należy wybierać grupy zasobów zawierającej poszczególne zasoby klastra AKS, takie jak *MC_myResourceGroup_myAKSCluster_eastus*.
1. Po lewej stronie wybierz pozycję **Ustawienia diagnostyczne**.
1. Wybierz klaster AKS, na przykład *myAKSCluster,* a następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.
1. Wprowadź nazwę, taką jak *myAKSClusterLogs*, a następnie wybierz opcję **Wyślij do usługi Log Analytics**.
1. Wybierz istniejący obszar roboczy lub utwórz nowy. Jeśli utworzysz obszar roboczy, podaj nazwę obszaru roboczego, grupę zasobów i lokalizację.
1. Na liście dostępnych dzienników wybierz dzienniki, które chcesz włączyć. Typowe dzienniki obejmują *kube-apiserver*, *kube-controller-manager*i *kube-scheduler*. Można włączyć dodatkowe dzienniki, takie jak *kube-audit* i *cluster-autoscaler*. Po włączeniu obszarów roboczych usługi Log Analytics można zwracać i zmieniać zebrane dzienniki.
1. Gdy jest to gotowe, wybierz pozycję **Zapisz,** aby włączyć zbieranie wybranych dzienników.

Poniższy przykładowy zrzut ekranu portalu przedstawia okno *Ustawienia diagnostyki:*

![Włącz obszar roboczy usługi Log Analytics dla dzienników usługi Azure Monitor klastra AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Planowanie zasobnika testowego w klastrze AKS

Aby wygenerować niektóre dzienniki, utwórz nowy zasobnik w klastrze AKS. Poniższy przykład manifestu YAML może służyć do tworzenia podstawowego wystąpienia NGINX. Utwórz plik `nginx.yaml` o nazwie w wybranym edytorze i wklej następującą zawartość:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Utwórz zasobnik za pomocą polecenia [tworzenia kubectl][kubectl-create] i określ plik YAML, jak pokazano w poniższym przykładzie:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Wyświetlanie zebranych dzienników

Może upłynąć kilka minut, aby dzienniki diagnostyczne zostały włączone i wyświetlane w obszarze roboczym usługi Log Analytics. W witrynie Azure portal wybierz grupę zasobów dla obszaru roboczego usługi Log Analytics, na przykład *myResourceGroup,* a następnie wybierz zasób analizy dzienników, taki jak *myAKSLogs*.

![Wybieranie obszaru roboczego usługi Log Analytics dla klastra AKS](media/view-master-logs/select-log-analytics-workspace.png)

Po lewej stronie wybierz pozycję **Dzienniki**. Aby wyświetlić *plik kube-apiserver,* wprowadź w polu tekstowym następującą kwerendę:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Wiele dzienników są prawdopodobnie zwracane dla serwera interfejsu API. Aby wysunąć zakres kwerendy, aby wyświetlić dzienniki dotyczące zasobnika NGINX utworzonego w poprzednim kroku, dodaj dodatkową *instrukcję, w której* można *wyszukać zasobników/nginx,* jak pokazano w poniższej przykładowej kwerendzie:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Zostaną wyświetlone określone dzienniki zasobnika NGINX, jak pokazano na poniższym przykładzie zrzutu ekranu:

![Wyniki zapytań analizy dzienników dla przykładowego zasobnika NGINX](media/view-master-logs/log-analytics-query-results.png)

Aby wyświetlić dodatkowe dzienniki, można zaktualizować kwerendę o nazwie *kategorii* do *kube-controller-manager* lub *kube-scheduler*, w zależności od tego, jakie dodatkowe dzienniki można włączyć. Dodatkowe, *gdzie* instrukcje mogą być następnie używane do uściślania zdarzeń, których szukasz.

Aby uzyskać więcej informacji na temat sposobu wykonywania zapytań i filtrowania danych dziennika, zobacz [Wyświetlanie lub analizowanie danych zebranych za pomocą wyszukiwania dziennika analizy dzienników][analyze-log-analytics].

## <a name="log-event-schema"></a>Schemat zdarzenia dziennika

Aby ułatwić analizowanie danych dziennika, w poniższej tabeli opisano schemat używany dla każdego zdarzenia:

| Nazwa pola               | Opis |
|--------------------------|-------------|
| *Resourceid*             | Zasób platformy Azure, który wyprodukował dziennik |
| *Czas*                   | Sygnatura czasowa momentu przesłania dziennika |
| *Kategorii*               | Nazwa kontenera/składnika generującego dziennik |
| *Operationname*          | Zawsze *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *właściwości.log*         | Pełny tekst dziennika ze składnika |
| *polecenie properties.stream*      | *stderr* lub *stdout* |
| *właściwości.pod*         | Nazwa zasobnika, z których pochodzi dziennik |
| *właściwości.containerID* | Identyfikator kontenera platformy docker, z jakiego pochodzi ten dziennik |

## <a name="log-roles"></a>Rola dziennika

| Rola                     | Opis |
|--------------------------|-------------|
| *aksService (usługi)*             | Nazwa wyświetlana w dzienniku inspekcji dla operacji płaszczyzny sterowania (z usługi hcpService) |
| *masterclient (100 00*           | Nazwa wyświetlana w dzienniku inspekcji masterclientcertificate, certyfikat, który otrzymujesz z az aks get-credentials |
| *nodeclient (węzełclient)*             | Nazwa wyświetlana certyfikatu ClientCertificate, który jest używany przez węzły agenta |

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak włączyć i przejrzeć dzienniki składników wzorcowych usługi Kubernetes w klastrze AKS. Aby monitorować i rozwiązywać problemy, można również [wyświetlić dzienniki Kubelet][kubelet-logs] i [włączyć dostęp do węzła SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

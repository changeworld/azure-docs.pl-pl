---
title: Dostosowywanie usługi CoreDNS dla usługi Azure Kubernetes (AKS)
description: Dowiedz się, jak dostosować usługę CoreDNS w celu dodania poddomen lub rozszerzenia niestandardowych punktów końcowych DNS przy użyciu usługi Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595828"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Dostosowywanie serwera CoreDNS w usłudze Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) używa projektu [CoreDNS][coredns] do zarządzania i rozpoznawania serwera DNS klastra ze wszystkimi klastrami *1.12.x* i wyższymi. Wcześniej był używany projekt kube-dns. Ten projekt kube-dns jest teraz przestarzały. Aby uzyskać więcej informacji na temat dostosowywania CoreDNS i kubernetes, zobacz [oficjalną dokumentację nadrzędną][corednsk8s].

Ponieważ AKS jest usługą zarządzaną, nie można zmodyfikować głównej konfiguracji coredns *(CoreFile*). Zamiast tego należy użyć Kubernetes *ConfigMap* zastąpić ustawienia domyślne. Aby wyświetlić domyślne mapy konfiguracyjne AKS `kubectl get configmaps --namespace=kube-system coredns -o yaml` CoreDNS, użyj tego polecenia.

W tym artykule pokazano, jak używać ConfigMaps do podstawowych opcji dostosowywania CoreDNS w AKS. Takie podejście różni się od konfigurowania CoreDNS w innych kontekstach, takich jak przy użyciu CoreFile. Sprawdź wersję coredns, że są uruchomione, jak wartości konfiguracji może ulec zmianie między wersjami.

> [!NOTE]
> `kube-dns`oferowane różne [opcje dostosowywania][kubednsblog] za pośrednictwem mapy konfiguracji Kubernetes. CoreDNS **nie** jest wstecznie kompatybilny z kube-dns. Wszelkie wcześniej używane dostosowania muszą zostać zaktualizowane do użytku z CoreDNS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

## <a name="what-is-supportedunsupported"></a>Co jest obsługiwane/nieobsługiwało

Wszystkie wbudowane wtyczki CoreDNS są obsługiwane. Nie są obsługiwane wtyczki dodatkowe/zewnętrzne.

## <a name="rewrite-dns"></a>Przepisz dns

Jednym ze scenariuszy jest wykonanie na bieżąco nazw DNS przepisuje. W poniższym przykładzie zastąp `<domain to be written>` własną w pełni kwalifikowaną nazwą domeny. Utwórz plik `corednsms.yaml` o nazwie i wklej następującą przykładową konfigurację:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Utwórz configMap za pomocą [polecenia kubectl apply configmap][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f corednsms.yaml
```

Aby sprawdzić, czy dostosowania zostały zastosowane, użyj [kubectl get configmaps][kubectl-get] i określ *coredns-custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Teraz wymuś CoreDNS przeładowanie ConfigMap. Polecenie [kubectl delete pod][kubectl delete] nie jest destrukcyjne i nie powoduje przestoju. Zasobników `kube-dns` są usuwane, a Harmonogram Kubernetes następnie odtwarza je. Te nowe zasobniki zawierają zmianę wartości TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Powyższe polecenie jest poprawne. Podczas gdy zmieniamy `coredns`, wdrożenie jest pod nazwą **kube-dns.**

## <a name="custom-forward-server"></a>Niestandardowy serwer przesyłania dalej

Jeśli trzeba określić serwer przesyłania dalej dla ruchu sieciowego, można utworzyć ConfigMap, aby dostosować DNS. W poniższym przykładzie `forward` zaktualizuj nazwę i adres za pomocą wartości dla własnego środowiska. Utwórz plik `corednsms.yaml` o nazwie i wklej następującą przykładową konfigurację:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Podobnie jak w poprzednich przykładach, utwórz ConfigMap za pomocą [polecenia kubectl apply configmap][kubectl-apply] i określ nazwę manifestu YAML. Następnie wymuś CoreDNS ponowne załadowanie configmap przy użyciu [zasobnika usuwania kubectl][kubectl delete] dla Harmonogramu Kubernetes, aby je odtworzyć:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Używanie domen niestandardowych

Można skonfigurować domeny niestandardowe, które można rozpoznać tylko wewnętrznie. Na przykład można rozwiązać *puglife.local*domeny niestandardowej , która nie jest prawidłową domeną najwyższego poziomu. Bez domeny niestandardowej ConfigMap klastra AKS nie może rozpoznać adresu.

W poniższym przykładzie zaktualizuj domenę niestandardową i adres IP, aby kierować ruch z wartościami dla własnego środowiska. Utwórz plik `corednsms.yaml` o nazwie i wklej następującą przykładową konfigurację:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Podobnie jak w poprzednich przykładach, utwórz ConfigMap za pomocą [polecenia kubectl apply configmap][kubectl-apply] i określ nazwę manifestu YAML. Następnie wymuś CoreDNS ponowne załadowanie configmap przy użyciu [zasobnika usuwania kubectl][kubectl delete] dla Harmonogramu Kubernetes, aby je odtworzyć:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domeny skrótowe

CoreDNS może być również używany do konfigurowania domen skrótowych. W poniższym przykładzie zaktualizuj domeny niestandardowe i adresy IP za pomocą wartości dla własnego środowiska. Utwórz plik `corednsms.yaml` o nazwie i wklej następującą przykładową konfigurację:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Podobnie jak w poprzednich przykładach, utwórz ConfigMap za pomocą [polecenia kubectl apply configmap][kubectl-apply] i określ nazwę manifestu YAML. Następnie wymuś CoreDNS ponowne załadowanie configmap przy użyciu [zasobnika usuwania kubectl][kubectl delete] dla Harmonogramu Kubernetes, aby je odtworzyć:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Wtyczka Hosts

Ponieważ wszystkie wbudowane wtyczki są obsługiwane oznacza to, że wtyczka CoreDNS [Hosts][coredns hosts] jest dostępna do dostosowania również:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Włączanie rejestrowania debugowania kwerendy DNS 

Aby włączyć rejestrowanie zapytań DNS, zastosuj następującą konfigurację w niestandardowej configmapie coredns:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano kilka przykładowych scenariuszy dostosowywania CoreDNS. Aby uzyskać informacje na temat projektu CoreDNS, zobacz [CoreDNS upstream project page][coredns].

Aby dowiedzieć się więcej o pojęciach dotyczących sieci bazowej, zobacz [Pojęcia dotyczące sieci dla aplikacji w uzywniu usługi AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md

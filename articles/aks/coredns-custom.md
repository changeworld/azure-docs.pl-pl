---
title: Dostosowywanie CoreDNS dla usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak dostosować CoreDNS dodać poddomeny lub rozszerzyć pewne niestandardowe punkty końcowe DNS przy użyciu usługi Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 5ff1ee03b8ac170def03576d3bf99c70957b2a8b
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507979"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Dostosowywanie CoreDNS za pomocą usługi Azure Kubernetes Service

Używa usługi Azure Kubernetes Service (AKS) [CoreDNS] [ coredns] projekt do zarządzania usługą DNS klastra i rozwiązanie ze wszystkimi *1.12.x* i wyższych klastrów. Wcześniej był używany projekt klastra kubernetes w usłudze dns. Ten projekt klastra kubernetes w usłudze dns jest już przestarzały. Aby uzyskać więcej informacji na temat dostosowywania CoreDNS i Kubernetes, zobacz [oficjalnej dokumentacji nadrzędnego][corednsk8s].

Jak AKS to zarządzana usługa, nie można zmodyfikować konfiguracji głównego CoreDNS ( *Plik_podstawowy*). Zamiast tego należy użyć usługi Kubernetes *ConfigMap* do zastępują ustawienia domyślne. Aby wyświetlić domyślną AKS CoreDNS ConfigMaps, użyj `kubectl get configmaps coredns -o yaml` polecenia.

W tym artykule dowiesz się, jak używać ConfigMaps dla podstawowego dostosowywania opcji CoreDNS w usłudze AKS.

> [!NOTE]
> `kube-dns` oferowana na różnych [opcje dostosowywania] [ kubednsblog] za pomocą mapy konfiguracji usługi Kubernetes. Jest CoreDNS **nie** wstecznie zgodna z klastra kubernetes w usłudze dns. Wszelkie dostosowania, w której zostały musi zostać zaktualizowany do użytku z programem CoreDNS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [aks — Szybki Start cli] lub [przy użyciu witryny Azure portal] [aks — Szybki Start — portal].

## <a name="what-is-supportedunsupported"></a>Co to jest obsługiwane/nieobsługiwane

Obsługiwane są wszystkie wbudowane wtyczek CoreDNS. Nie wtyczek dodatkami na/innych firm są obsługiwane.

## <a name="rewrite-dns"></a>Ponowne zapisywanie adresów DNS

Jeden scenariusz, w których masz jest przeprowadzenie ponownego nazwy DNS na bieżąco. W poniższym przykładzie Zastąp `<domain to be written>` przy użyciu własnego w pełni kwalifikowaną nazwę domeny. Utwórz plik o nazwie `corednsms.json` i Wklej poniższą konfigurację do przykładu:

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
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Tworzenie za pomocą ConfigMap [kubectl zastosować configmap] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```console
kubectl apply -f corednsms.json
```

Aby sprawdzić, dostosowania zostały zastosowane, należy użyć [kubectl get-configmaps] [ kubectl-get] oraz określić swoje *coredns niestandardowe* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Teraz można wymusić CoreDNS, aby ponownie załadować ConfigMap. [Kubectl usunąć zasobnik] [ kubectl delete] polecenia nie jest destrukcyjne i nie powoduje czas przestoju. `kube-dns` Zasobników są usuwane, a ich utworzy ponownie harmonogram Kubernetes. Tych nowych zasobników zawierają zmianę wartości czasu wygaśnięcia.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Powyższe polecenie jest poprawna. Gdy Zmieniamy `coredns`, wdrożenie podlega **klastra kubernetes w usłudze dns** nazwy.

## <a name="custom-proxy-server"></a>Serwer proxy niestandardowe

Jeśli potrzebujesz określić serwer proxy na potrzeby ruchu sieciowego, można utworzyć ConfigMap, aby dostosować DNS. W poniższym przykładzie, należy zaktualizować `proxy` nazwy i adresu z wartościami dla Twojego środowiska. Utwórz plik o nazwie `corednsms.json` i Wklej poniższą konfigurację do przykładu:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    .:53 {
        proxy foo.com 1.1.1.1
    }
```

Tak jak w poprzednich przykładach, należy utworzyć przy użyciu ConfigMap [kubectl zastosować configmap] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML. Następnie należy wymusić CoreDNS, aby załadować ponownie używając ConfigMap [kubectl usunąć zasobnik] [ kubectl delete] usługi Kubernetes Scheduler je ponownie utworzyć:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Użyj domen niestandardowych

Można skonfigurować domen niestandardowych, które może zostać rozpoznana tylko wewnętrznie. Na przykład, można rozpoznać domeny niestandardowej *puglife.local*, który nie jest prawidłową domenę najwyższego poziomu. Bez domenę niestandardową ConfigMap klastra AKS, nie można rozpoznać adresu.

W poniższym przykładzie należy zaktualizować domenę niestandardową i adres IP do kierowania ruchu do wartościami dla Twojego środowiska. Utwórz plik o nazwie `corednsms.json` i Wklej poniższą konfigurację do przykładu:

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
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Tak jak w poprzednich przykładach, należy utworzyć przy użyciu ConfigMap [kubectl zastosować configmap] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML. Następnie należy wymusić CoreDNS, aby załadować ponownie używając ConfigMap [kubectl usunąć zasobnik] [ kubectl delete] usługi Kubernetes Scheduler je ponownie utworzyć:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Stub domains

CoreDNS można również konfigurowanie domen wycinka. W poniższym przykładzie należy zaktualizować domen niestandardowych i adresy IP z wartościami dla Twojego środowiska. Utwórz plik o nazwie `corednsms.json` i Wklej poniższą konfigurację do przykładu:

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
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Tak jak w poprzednich przykładach, należy utworzyć przy użyciu ConfigMap [kubectl zastosować configmap] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML. Następnie należy wymusić CoreDNS, aby załadować ponownie używając ConfigMap [kubectl usunąć zasobnik] [ kubectl delete] usługi Kubernetes Scheduler je ponownie utworzyć:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hosts plugin

Ponieważ wszystkie wbudowane dodatki plug-in są obsługiwane oznacza to, że CoreDNS [hostów] [ coredns hosts] dodatek jest dostępna w celu dostosowywania także:

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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano niektóre przykładowe scenariusze dotyczące dostosowywania CoreDNS. Aby uzyskać informacje na temat projektu CoreDNS, zobacz [strony nadrzędne projektu CoreDNS][coredns].

Aby dowiedzieć się więcej na temat podstawowych pojęć sieci, zobacz [sieci pojęcia związane z aplikacjami w usłudze AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - external -->
[concepts-network]: concepts-network.md

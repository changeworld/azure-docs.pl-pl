---
title: Zabezpieczanie zasobników za pomocą zasad sieciowych w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć ruch, który przepływa pojęcie zasobników za pomocą zasad sieciowych platformy Kubernetes w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: a0512806ec797f43fc54d8a28a7cbadf86faf1d9
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230016"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpieczanie ruchu sieciowego między zasobników za pomocą zasad sieciowych w usłudze Azure Kubernetes Service (AKS)

Po uruchomieniu aplikacji nowoczesnych, opartych na mikrousługach w usłudze Kubernetes, często zachodzi potrzeba kontroli, które składniki mogą komunikować się ze sobą. Zasadę najmniejszych uprawnień powinny być stosowane do jak ruch może przepływać między zasobników w klastrze usługi Azure Kubernetes Service (AKS). Załóżmy, że prawdopodobnie chcesz blokować ruch bezpośrednio do aplikacji zaplecza. *Zasad sieciowych* funkcji w usłudze Kubernetes pozwala zdefiniować reguły dla ruchu przychodzącego i wychodzącego między zasobników w klastrze.

W tym artykule pokazano, jak zainstalować aparatu zasad sieciowych i Kubernetes sieci utworzone zasady służące do sterowania przepływem ruchu między zasobników w usłudze AKS. Zasady sieci należy używać tylko dla węzłów opartych na systemie Linux i zasobników w usłudze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

> [!TIP]
> Jeśli funkcja zasad sieciowych jest używany w trakcie okresu zapoznawczego, zalecamy możesz [Utwórz nowy klaster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Jeśli chcesz nadal korzystać z istniejących klastrów testowych, które używane zasad sieciowych w trakcie okresu zapoznawczego uaktualnienia klastra do nowych wersji rozwiązania Kubernetes dla najnowszej wersji ogólnie dostępnej, a następnie wdrożyć następujące manifest YAML Aby rozwiązać problem, którym wystąpiła awaria serwera metryki i Kubernetes pulpit nawigacyjny. Ta poprawka jest tylko wymagane dla klastrów, które używały Calico aparatu zasad sieci.
>
> Ze względów bezpieczeństwa [Przejrzyj zawartość tego manifestu YAML] [ calico-aks-cleanup] Aby zrozumieć, co to jest wdrażana w klastrze AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Omówienie zasad sieciowych

Wszystkie zasobników w klastrze AKS umożliwia wysyłanie oraz odbieranie ruchu, bez ograniczenia, domyślnie. Aby zwiększyć bezpieczeństwo, można zdefiniować reguł, które kontrolują przepływu ruchu. Aplikacji zaplecza są często dostępne tylko do wymaganych usług frontonu, na przykład. Lub składników bazy danych dostępnych tylko warstwy aplikacji łączących się z nimi.

Zasady sieci jest specyfikacja Kubernetes, który definiuje zasady dostępu do komunikacji między zasobników. Za pomocą zasad sieciowych, należy zdefiniować uporządkowany zestaw reguł do wysyłania i odbierania ruchu i zastosować je do kolekcji zasobników, które odpowiadają co najmniej jeden selektory etykiety.

Te zasady sieciowe są definiowane jako manifesty YAML. Zasady sieciowe może być dołączane jako część szersze manifest, który tworzy także wdrożenia lub usługi.

### <a name="network-policy-options-in-aks"></a>Opcje zasad sieciowych w usłudze AKS

Platforma Azure udostępnia dwa sposoby zaimplementowania zasad sieciowych. Możesz wybrać opcję Zasady sieci, podczas tworzenia klastra usługi AKS. Nie można zmienić opcji zasad, po utworzeniu klastra:

* Wywołana implementacji własnych Azure *zasady sieciowe Azure*.
* *Zasady sieciowe calico*, typu open-source sieci i rozwiązań zabezpieczeń sieciowych przez [Tigera][tigera].

Obu implementacjach używać systemu Linux *IPTables* do wymuszania zasad określony. Zasady są tłumaczone na zestawy par adresów IP dozwolonych i niedozwolonych. Te pary następnie są programowane jako IPTable reguły filtrowania.

Zasady sieci działa tylko z opcją wtyczki Azure CNI (zaawansowane). Implementacja różni się dwie opcje:

* *Zasady sieciowe Azure* -wtyczki Azure CNI konfiguruje bridge w hoście maszyny Wirtualnej dla sieci wewnątrz węzła. Reguły filtrowania są stosowane, gdy pakiety przechodzą przez połączenie.
* *Zasady sieciowe calico* -wtyczki Azure CNI konfiguruje trasy lokalne jądra dla ruchu wewnątrz węzła. Zasady są stosowane w interfejsie sieciowym zasobników.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Różnice między platformą Azure i Calico zasady i ich możliwości

| Możliwości                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Obsługiwane platformy                      | Linux                      | Linux                       |
| Obsługiwane opcje sieciowe             | Azure CNI                  | Azure CNI                   |
| Zgodność ze specyfikacją rozwiązania Kubernetes | Wszystkie typy zasad obsługiwanych |  Wszystkie typy zasad obsługiwanych |
| Dodatkowe funkcje                      | Brak                       | Rozszerzony model zasad, składające się z globalnych zasad sieciowych, ustawienie globalne sieci i hosta punktu końcowego. Aby uzyskać więcej informacji na temat korzystania z `calicoctl` interfejsu wiersza polecenia do zarządzania są rozszerzone funkcje, zobacz [odwołań do użytkownika calicoctl][calicoctl]. |
| Pomoc techniczna                                  | Obsługiwane, a zespół inżynierów pomocy technicznej platformy Azure | Pomoc techniczna w społeczności calico. Aby uzyskać więcej informacji na temat dodatkowych płatnej pomocy technicznej, zobacz [opcje pomocy technicznej Calico projektu][calico-support]. |
| Rejestrowanie                                  | Zasady dodane / usunięte w IPTables są rejestrowane na każdym hoście, w obszarze */var/log/azure-npm.log* | Aby uzyskać więcej informacji, zobacz [Calico składnika dzienników][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Tworzenie klastra AKS i włączyć zasad sieciowych

Przyjrzyjmy się zasad sieciowych działa, Utwórz, a następnie rozwiń na zasady, które definiują przepływ ruchu:

* Odmowa cały ruch do zasobników.
* Zezwalać na ruch na podstawie etykiet zasobników.
* Zezwalać na ruch w oparciu o przestrzeni nazw.

Najpierw Utwórz klaster AKS, który obsługuje zasady sieci. Funkcja zasad sieci można włączyć tylko podczas tworzenia klastra. Nie można włączyć zasad sieciowych w istniejącym klastrze usługi AKS.

Aby z klastrem usługi AKS przy użyciu zasad sieci, należy użyć [wtyczki Azure CNI wtyczki] [ azure-cni] i zdefiniować własne sieci wirtualnej i podsieci. Aby uzyskać szczegółowe informacje na temat sposobu zaplanować zakresy wymagane podsieci, zobacz [skonfigurować zaawansowane funkcje sieciowe][use-advanced-networking].

Poniższy przykładowy skrypt:

* Tworzy sieć wirtualną i podsieć.
* Tworzy jednostkę usługi do użycia usługi Azure Active Directory (Azure AD), z klastrem usługi AKS.
* Przypisuje *Współautor* uprawnienia dla usługi AKS klastra nazwy głównej usługi w sieci wirtualnej.
* Tworzy klaster usługi AKS w zdefiniowanych sieci wirtualnej i włącza zasad sieciowych.
    * *Azure* używana jest opcja zasad sieci. Aby zamiast tego użyj Calico jako opcja zasad sieciowych, należy użyć `--network-policy calico` parametru.

Podaj własne bezpieczne *SP_PASSWORD*. Możesz zastąpić *RESOURCE_GROUP_NAME* i *nazwa_klastra* zmiennych:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Utworzenie klastra trwa kilka minut. Gdy klaster będzie gotowy, skonfiguruj `kubectl` nawiązać połączenia z klastrem Kubernetes za pomocą [az aks get-credentials] [ az-aks-get-credentials] polecenia. To polecenie umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes, aby ich używać:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odmów cały ruch przychodzący do zasobnik

Przed definiowania reguły, aby umożliwić określonego ruchu sieciowego, należy najpierw utworzyć zasady sieci w celu blokowania całego ruchu. Ta zasada zapewnia punkt początkowy, aby rozpocząć do listy dozwolonych tylko żądany ruch. Również wyraźnie widać, że ruch jest porzucany, po zastosowaniu zasad sieciowych.

Dla przykładowej aplikacji środowiska i reguły ruchu, najpierw utwórz przestrzeń nazwy wywołaną *rozwoju* do uruchomienia zasobników przykładu:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Utwórz pod serwer zaplecza w przykładzie, z systemem serwera NGINX. Pod tym zaplecza może służyć do symulacji przykładowej aplikacji sieci web zaplecza. Utwórz ten zasobnik w *rozwoju* przestrzeni nazw, a następnie otwórz port *80* do obsługi ruchu w sieci web. Etykiety zasobnik za pomocą *app = aplikacji sieci Web, rola = zaplecza* tak, aby firma Microsoft można wskazać za pomocą zasad sieciowych w następnej sekcji:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Utwórz pod innym i Dołącz sesji terminalowej, aby sprawdzić, czy pomyślnie dotrzeć do domyślnej strony serwera NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki, użyj `wget` aby upewnić się, że użytkownik ma dostęp serwer NGINX domyślnej strony sieci Web:

```console
wget -qO- http://backend
```

Następujące przykładowe dane wyjściowe pokazuje, że zwracane NGINX domyślnej strony sieci Web:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Tworzenie i stosowanie zasad sieciowych

Teraz, gdy zostało potwierdzone, których można użyć podstawowa strony sieci Web NGINX na zasobnik zaplecza próbki, należy utworzyć zasady sieci w celu blokowania całego ruchu. Utwórz plik o nazwie `backend-policy.yaml` i wklej następujące manifest YAML. Używa tego manifestu *podSelector* Dołącz zasady do zasobników, które mają *app:webapp, rola: zaplecza* etykiety, takie jak tym zasobniku NGINX próbki. Nie zdefiniowano reguł w ramach *ruch przychodzący*, dzięki czemu cały ruch przychodzący do zasobnika ustawiany jest odmowa dostępu:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Stosowanie zasad sieciowych przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testowanie zasad sieciowych


Zobaczmy, jeśli strona sieci Web NGINX w zasobniku zaplecza można użyć ponownie. Utwórz inny zasobnika testu i Dołącz sesji terminalowej:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki, użyj `wget` czy można uzyskać dostęp do domyślnej strony serwera NGINX. Tym razem ustawiono wartość limitu czasu *2* sekund. Zasady sieci teraz blokuje cały ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Zezwalaj na ruch przychodzący na podstawie etykiety zasobników

W poprzedniej sekcji serwer zaplecza w zasobniku NGINX zostało zaplanowane i zasad sieci został utworzony w celu blokowania ruchu wszystkich. Umożliwia tworzenie frontonu zasobnik i aktualizowanie zasad sieciowych, aby zezwolić na ruch z zasobników frontonu.

Aktualizowanie zasad sieciowych, aby zezwolić na ruch z zasobników z etykietami *app:webapp, rola: frontonu* i w dowolnym obszarze nazw. Edytuj poprzedniego *policy.yaml zaplecza* pliku i Dodaj *matchLabels* transferu danych przychodzących reguł, dzięki czemu manifeście wygląda podobnie jak w poniższym przykładzie:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Te zasady sieciowe używają *namespaceSelector* i *podSelector* elementu dla reguły ruchu przychodzącego. Składnia YAML jest ważne w przypadku reguł ruchu przychodzącego za dodatek. W tym przykładzie oba te elementy muszą być zgodne, reguły ruchu przychodzącego do zastosowania. Kubernetes wersji wcześniejszych niż *1.12* nie może zinterpretować tych elementów i ograniczanie ruchu sieciowego, zgodnie z oczekiwaniami. Aby uzyskać więcej informacji dotyczących tego zachowania, zobacz [zachowanie do i z selektory][policy-rules].

Stosowanie zasad sieciowych zaktualizowane przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Zaplanuj pod, która jest oznaczona jako *aplikacji = aplikacji sieci Web, rola = frontonu* i dołączyć do sesji terminalowej:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki, użyj `wget` czy można uzyskać dostęp do serwera NGINX domyślnej strony sieci Web:

```console
wget -qO- http://backend
```

Ponieważ reguły ruchu przychodzącego zezwala na ruch z zasobników, które mają etykiety *aplikacji: aplikacji sieci Web, rola: frontonu*, ruch z zasobników frontonu jest dozwolony. Następujące przykładowe dane wyjściowe pokazuje domyślnej strony serwera NGINX, zwrócone:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik jest automatycznie usuwany.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testowanie pod bez odpowiadających im etykiet

Zasady sieci zezwala na ruch z etykietą zasobników *aplikacji: aplikacji sieci Web, rola: frontonu*, ale należy odmówić cały pozostały ruch. Możemy sprawdzić, czy pod innym bez etykiety te mogą uzyskiwać dostęp pod NGINX zaplecza. Utwórz inny zasobnika testu i Dołącz sesji terminalowej:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki, użyj `wget` czy można uzyskać dostęp do domyślnej strony serwera NGINX. Zasady sieci blokuje ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Zezwalaj na ruch tylko z w ramach określonych przestrzeni nazw

W poprzednich przykładach utworzono zasady sieci, odmowa cały ruch, a następnie zaktualizowane zasady Aby zezwolić na ruch z zasobników z określoną etykietą. Jest innym potrzebują do ograniczania ruchu do tylko w ramach danego obszaru nazw. Gdyby poprzednich przykładach dla ruchu w *rozwoju* przestrzeni nazw, tworzenie zasad sieciowych, który uniemożliwia ruch z innej przestrzeni nazw, takich jak *produkcji*, zanim zasobników.

Najpierw utwórz nową przestrzeń nazw, aby zasymulować przestrzeni nazw w środowisku produkcyjnym:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Zaplanować zasobnik testu w *produkcji* przestrzeni nazw, która jest oznaczona jako *app = aplikacji sieci Web, rola = frontonu*. Dołącz sesję terminalu:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

W wierszu polecenia powłoki, użyj `wget` aby upewnić się, że użytkownik ma dostęp serwer NGINX domyślnej strony sieci Web:

```console
wget -qO- http://backend.development
```

Ponieważ etykiety zasobnik zgodne, co jest obecnie dozwolona w zasadach sieci, ruch jest dozwolony. Zasady sieci wygląda w przestrzeni nazw, tylko etykiety zasobników. Następujące przykładowe dane wyjściowe pokazuje domyślnej strony serwera NGINX, zwrócone:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizacja zasad sieciowych

Zaktualizujmy reguły ruchu przychodzącego *namespaceSelector* sekcji, aby zezwalać tylko na ruch z poziomu *rozwoju* przestrzeni nazw. Edytuj *policy.yaml zaplecza* plik manifestu, jak pokazano w poniższym przykładzie:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

W przykładach bardziej złożone, można zdefiniować wiele reguł ruchu przychodzącego, takie jak *namespaceSelector* i następnie *podSelector*.

Stosowanie zasad sieciowych zaktualizowane przy użyciu [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testowanie zasad sieciowych zaktualizowane

Zaplanuj pod innym w *produkcji* przestrzeni nazw i dołączyć do sesji terminalowej:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

W wierszu polecenia powłoki, użyj `wget` aby zobaczyć, że zasady sieci teraz nie zezwala na ruch:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Wyjście z zasobników testu:

```console
exit
```

Z ruchem odmowa *produkcji* przestrzeni nazw, harmonogram zasobnik testu z powrotem w *rozwoju* przestrzeni nazw i dołączyć do sesji terminalowej:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki, użyj `wget` aby zobaczyć, że zasady sieci zezwala na ruch:

```console
wget -qO- http://backend
```

Ruch jest dozwolony, ponieważ zasobnika ustawiany jest zaplanowane w przestrzeni nazw, dopasowuje co jest dozwolone w zasad sieci. Następujące przykładowe dane wyjściowe pokazuje domyślnej strony serwera NGINX, zwrócone:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane.

```console
exit
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule będziemy utworzone dwie przestrzenie nazw i stosowane zasady sieci. Aby wyczyścić te zasoby, należy użyć [Usuń kubectl] [ kubectl-delete] polecenia i określić nazwy zasobu:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o zasobach sieciowych, zobacz [sieci pojęcia związane z aplikacjami w usłudze Azure Kubernetes Service (AKS)][concepts-network].

Aby dowiedzieć się więcej na temat zasad, zobacz [zasad sieciowych Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

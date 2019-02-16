---
title: Zabezpieczanie zasobników za pomocą zasad sieciowych w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć ruch, który przepływa pojęcie zasobników za pomocą zasad sieciowych platformy Kubernetes w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: ade5a39273aa807f6c69f76342a0f715c7a96309
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327168"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpieczanie ruchu sieciowego między zasobników za pomocą zasad sieciowych w usłudze Azure Kubernetes Service (AKS)

Po uruchomieniu aplikacji nowoczesnych, opartych na mikrousługach w usłudze Kubernetes, często zachodzi potrzeba kontroli, które składniki mogą komunikować się ze sobą. Zasadę najmniejszych uprawnień powinny być stosowane do jak ruch może przepływać między zasobników w klastrze AKS. Na przykład prawdopodobnie chcesz zablokować ruch bezpośrednio do aplikacji zaplecza. W usłudze Kubernetes *zasad sieciowych* funkcja pozwala zdefiniować reguły dla ruchu przychodzącego i wychodzącego ruchu między zasobników w klastrze.

W tym artykule pokazano, jak sterować przepływem ruchu między zasobników w usłudze AKS za pomocą zasad sieciowych.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.56 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="overview-of-network-policy"></a>Omówienie zasad sieciowych

Domyślnie wszystkie zasobników w klastrze AKS umożliwia wysyłanie oraz odbieranie ruchu bez ograniczeń. Aby zwiększyć bezpieczeństwo, można zdefiniować reguł, które kontrolują przepływu ruchu. Na przykład aplikacji zaplecza są dostępne często tylko wymagane frontonu usług lub składników bazy danych dostępnych tylko warstwy aplikacji łączących się z nimi.

Zasady sieciowe to zasoby platformy Kubernetes, które pozwalają sterować przepływem ruchu między zasobników. Istnieje możliwość blokują lub zezwalają na ruch na podstawie ustawień, takich jak przypisać etykiety, przestrzeń nazw lub ruchu sieciowego port. Zasady sieciowe są zdefiniowane zgodnie z manifesty YAML i może być dołączane jako część szersze manifest, który tworzy także wdrożenia lub usługi.

Przyjrzyjmy się zasad sieciowych działa, Utwórz, a następnie rozwiń na zasady, które definiują przepływ ruchu w następujący sposób:

* Odmowa cały ruch do zasobników.
* Zezwalać na ruch na podstawie etykiet zasobników.
* Zezwalać na ruch w oparciu o przestrzeni nazw.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Tworzenie klastra AKS i włączyć zasad sieciowych

Zasady sieci można włączyć tylko podczas tworzenia klastra. Nie można włączyć zasad sieciowych w istniejącym klastrze usługi AKS. Aby utworzyć AKS za pomocą zasad sieciowych, należy najpierw włączyć flagi funkcji w ramach Twojej subskrypcji. Aby zarejestrować *EnableNetworkPolicy* flagę funkcji, należy użyć [az feature register] [ az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az] [ az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register] [ az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Aby z klastrem usługi AKS przy użyciu zasad sieci, należy użyć [wtyczki wtyczki Azure CNI] [ azure-cni] i zdefiniować własne sieci wirtualnej i podsieci. Aby uzyskać szczegółowe informacje na temat sposobu zaplanować zakresy wymagane podsieci, zobacz [skonfigurować zaawansowane funkcje sieciowe][use-advanced-networking]. Poniższy przykładowy skrypt:

* Tworzy sieć wirtualną i podsieć.
* Tworzy jednostkę usługi do użycia usługi Azure Active Directory (AD), z klastrem usługi AKS.
* Przypisuje *Współautor* uprawnienia dla usługi AKS klastra nazwy głównej usługi w sieci wirtualnej.
* Tworzy klaster usługi AKS w zdefiniowanych sieci wirtualnej i umożliwia zasad sieciowych.

Podaj własne bezpieczne *SP_PASSWORD*. Jeśli to konieczne, Zastąp *RESOURCE_GROUP_NAME* i *nazwa_klastra* zmiennych:

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
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

Utworzenie klastra trwa kilka minut. Po zakończeniu konfigurowania `kubectl` nawiązywania połączenia z klastrem Kubernetes za pomocą [az aks get-credentials] [ az-aks-get-credentials] polecenia. To polecenie umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes, aby ich używać:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odmów cały ruch przychodzący do zasobnik

Przed definiowania reguły, aby umożliwić określonego ruchu sieciowego, należy najpierw utworzyć zasady sieci w celu blokowania całego ruchu. Ta zasada zapewnia punkt początkowy, aby rozpocząć do listy dozwolonych tylko żądany ruch. Również wyraźnie widać, że ruch jest porzucany, po zastosowaniu zasad sieciowych.

Dla naszych przykładowych aplikacji środowiska i reguły ruchu, najpierw utwórz przestrzeń nazwy wywołaną *rozwoju* do uruchomienia zasobników w naszym przykładzie:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Teraz Utwórz zasobnika zaplecza przykład, z systemem serwera NGINX. Pod tym wewnętrznej bazy danych może służyć do symulacji przykładowej aplikacji sieci web zaplecza. Utwórz ten zasobnik w *rozwoju* przestrzeni nazw, a następnie otwórz port *80* do obsługi ruchu w sieci web. Etykiety zasobnik za pomocą *app = aplikacji sieci Web, rola = zaplecza* tak, aby firma Microsoft można wskazać za pomocą zasad sieciowych w następnej sekcji:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Aby sprawdzić, czy można pomyślnie dotrzeć do domyślnej strony internetowej serwera NGINX, Utwórz pod innym i dołączać sesji terminalowej:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Jeden raz w wierszu polecenia powłoki, należy użyć `wget` aby upewnić się, można uzyskać dostęp domyślną stronę sieci web NGINX:

```console
wget -qO- http://backend
```

Następujące przykładowe dane wyjściowe pokazuje, że domyślna strona sieci web NGINX zwrócone:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane:

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Tworzenie i stosowanie zasad sieciowych

Po potwierdzeniu, że mają dostęp podstawowa strona internetowa serwera NGINX w zasobniku wewnętrznej bazy danych przykładowych, utworzyć zasad sieciowych w celu blokowania ruchu wszystkich. Utwórz plik o nazwie `backend-policy.yaml` i wklej następujące manifest YAML. Używa tego manifestu *podSelector* Dołącz zasady do zasobników, które mają *app:webapp, rola: zaplecza* etykiety, takie jak tym zasobniku NGINX próbki. Nie zdefiniowano reguł w ramach *ruch przychodzący*, dzięki czemu cały ruch przychodzący do zasobnika ustawiany jest odmowa dostępu:

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

Zastosuj przy użyciu zasad sieci [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testowanie zasad sieciowych

Zobaczmy, można przejść do strony sieci Web NGINX w zasobniku wewnętrznej bazy danych ponownie. Utwórz inny zasobnika testu i Dołącz sesji terminalowej:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Jeden raz w wierszu polecenia powłoki, należy użyć `wget` czy można uzyskać dostęp do domyślnej strony internetowej serwera NGINX. Tym razem ustawiono wartość limitu czasu *2* sekund. Zasady sieci teraz blokuje cały ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane:

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Zezwalaj na ruch przychodzący na podstawie etykiety zasobników

W poprzedniej sekcji zasobnik NGINX wewnętrznej bazy danych zostało zaplanowane i zasad sieci został utworzony w celu blokowania ruchu wszystkich. Teraz możemy utworzyć zasobnik frontonu i aktualizacji zasad sieciowych, aby zezwolić na ruch z zasobników frontonu.

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

Zastosuj przy użyciu zasad sieci zaktualizowane [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Teraz zaplanować zasobników, która jest oznaczona jako *app = aplikacji sieci Web, rola = frontonu* i dołączyć do sesji terminalowej:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Jeden raz w wierszu polecenia powłoki, należy użyć `wget` czy można uzyskać dostęp do domyślnej strony internetowej serwera NGINX:

```console
wget -qO- http://backend
```

Podczas transferu danych przychodzących reguła zezwala na ruch z zasobników, które mają etykiety *aplikacji: aplikacja sieci Web, rola: frontonu*, ruch z zasobników frontend jest dozwolony. Następujące przykładowe dane wyjściowe przedstawia domyślna strona internetowa serwera NGINX, zwrócone:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik zostaną automatycznie usunięte:

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testowanie pod bez odpowiadających im etykiet

Zasady sieci zezwala na ruch z etykietą zasobników *aplikacji: aplikacji sieci Web, rola: frontonu*, ale należy odmówić cały pozostały ruch. Możemy przetestować, czy pod innym bez tych etykiet nie można uzyskać dostępu pod NGINX wewnętrznej bazy danych. Utwórz inny zasobnika testu i Dołącz sesji terminalowej:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Jeden raz w wierszu polecenia powłoki, należy użyć `wget` czy można uzyskać dostęp do domyślnej strony internetowej serwera NGINX. Zasady sieci blokuje ruch przychodzący, więc nie można załadować strony, jak pokazano w poniższym przykładzie:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane:

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Zezwalaj na ruch tylko z w ramach określonych przestrzeni nazw

W poprzednich przykładach utworzono zasady sieci, odmowa cały ruch, a następnie zaktualizowane zasady Aby zezwolić na ruch z zasobników z określoną etykietą. Co typowe potrzeby jest ograniczania ruchu do tylko w ramach danego obszaru nazw. Gdyby poprzednich przykładach dla ruchu w *rozwoju* przestrzeni nazw, możesz następnie utworzyć zasady sieci, która uniemożliwia ruch z innej przestrzeni nazw, takich jak *produkcji*, zanim zasobników.

Najpierw utwórz nową przestrzeń nazw, aby zasymulować przestrzeni nazw w środowisku produkcyjnym:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Zaplanować zasobnik testu w *produkcji* przestrzeni nazw, która jest oznaczona jako *app = aplikacji sieci Web, rola = frontonu*. Dołącz sesję terminalu:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Jeden raz w wierszu polecenia powłoki, należy użyć `wget` aby upewnić się, można uzyskać dostęp domyślną stronę sieci web NGINX:

```console
wget -qO- http://backend.development
```

Jak etykiety dla zasobnika ustawiany jest zgodny, co jest obecnie dozwolona w zasadach sieci, ruch jest dozwolony. Zasady sieci wygląda w przestrzeni nazw, tylko etykiety zasobników. Następujące przykładowe dane wyjściowe przedstawia domyślna strona internetowa serwera NGINX, zwrócone:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane:

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizacja zasad sieciowych

Teraz zaktualizujmy reguły ruchu przychodzącego *namespaceSelector* sekcji, aby zezwalać tylko na ruch z poziomu *rozwoju* przestrzeni nazw. Edytuj *policy.yaml zaplecza* plik manifestu, jak pokazano w poniższym przykładzie:

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

W przykładach bardziej złożone, można zdefiniować wiele reguł ruchu przychodzącego, takie jak używać *namespaceSelector* i następnie *podSelector*.

Zastosuj przy użyciu zasad sieci zaktualizowane [zastosować kubectl] [ kubectl-apply] polecenia i podaj nazwę manifeście YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testowanie zasad sieciowych zaktualizowane

Teraz zaplanować innej zasobnik w *produkcji* przestrzeni nazw i dołączyć sesję terminalu:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Jeden raz w wierszu polecenia powłoki, należy użyć `wget` Aby wyświetlić zasady sieci, teraz zezwalają na ruch:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Wyjście z zasobników testu:

```console
exit
```

Z ruchem odmowa *produkcji* przestrzeni nazw, Zaplanuj teraz zasobnik testu z powrotem w *rozwoju* przestrzeni nazw i dołączyć do sesji terminalowej:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Jeden raz w wierszu polecenia powłoki, należy użyć `wget` wyświetlić sieci zasady zezwalają na ruch:

```console
wget -qO- http://backend
```

Jak zasobnika ustawiany jest planowana w przestrzeni nazw, który dopasowuje co to jest dozwolona w zasadach sieci, ruch jest dozwolony. Następujące przykładowe dane wyjściowe przedstawia domyślna strona internetowa serwera NGINX, zwrócone:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjście z dołączonym sesji terminalowej. Zasobnik testu są automatycznie usuwane:

```console
exit
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule utworzymy dwie przestrzenie nazw i stosowane zasady sieci. Aby wyczyścić te zasoby, należy użyć [Usuń kubectl] [ kubectl-delete] polecenia i określić nazwy zasobu w następujący sposób:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat zasobów sieciowych, zobacz [sieci pojęcia związane z aplikacjami w usłudze Azure Kubernetes Service (AKS)][concepts-network].

Aby dowiedzieć się więcej o korzystaniu z zasad, zobacz [zasad sieciowych Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

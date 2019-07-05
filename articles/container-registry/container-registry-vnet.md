---
title: Ograniczanie dostępu do usługi Azure container registry z sieci wirtualnej
description: Zezwalaj na dostęp do usługi Azure container registry, tylko z zasobami w sieci wirtualnej platformy Azure lub zakresy publicznych adresów IP.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 06e45127f940e01de5f3ceeefc354014a88014db
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514398"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Ograniczanie dostępu do usługi Azure container registry przy użyciu sieci wirtualnej platformy Azure lub reguły zapory

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczny, prywatnych sieci na potrzeby subskrypcji platformy Azure i zasobów lokalnych. Ograniczanie dostępu do rejestru prywatnego kontenerów platformy Azure z siecią wirtualną platformy Azure, gwarantuje, że tylko zasoby w sieci wirtualnej dostępu do rejestru. W przypadku scenariuszy obejmujących wiele lokalizacji można również skonfigurować reguły zapory zezwalające na dostęp do rejestru, tylko z określonych adresów IP.

W tym artykule przedstawiono dwa scenariusze, aby utworzyć reguły dostępu do sieci, aby ograniczyć dostęp do usługi Azure container registry: maszynie wirtualnej wdrożonej w sieci wirtualnej lub z publicznym adresem IP maszyny Wirtualnej.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Tylko **Premium** rejestru kontenerów można skonfigurować reguły dostępu do sieci. Aby uzyskać informacji o warstwach usługi rejestru, zobacz [jednostki SKU rejestru kontenerów platformy Azure](container-registry-skus.md). 

* Tylko [usługi Azure Kubernetes Service](../aks/intro-kubernetes.md) klastra lub na platformie Azure [maszyny wirtualnej](../virtual-machines/linux/overview.md) może służyć jako host do dostępu do rejestru kontenerów w sieci wirtualnej. *Inne usługi platformy Azure, w tym usługi Azure Container Instances nie są obecnie obsługiwane.*

* [Zadania usługi ACR](container-registry-tasks-overview.md) operacje nie są obecnie obsługiwane w rejestrze kontenerów, dostępne w sieci wirtualnej.

* Każdy rejestr obsługuje maksymalnie 100 reguł sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

* Korzystanie z systemu Azure interfejsu wiersza polecenia kroki opisane w tym artykule, wiersza polecenia platformy Azure w wersji 2.0.58 lub nowszy jest wymagany. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

* Jeśli nie masz jeszcze rejestru kontenerów, utworzyć (jednostki SKU Premium wymagane) i wypchnąć obraz przykładowe, takie jak `hello-world` z usługi Docker Hub. Na przykład użyć [witryny Azure portal][quickstart-portal] or the [Azure CLI][quickstart-cli] tworzenia rejestru. 

## <a name="about-network-rules-for-a-container-registry"></a>O regułach sieciowe dla rejestru kontenerów

Rejestr kontenerów platformy Azure, domyślnie akceptuje połączenia przez internet z hostów w sieci. Z sieci wirtualnej można zezwolić tylko zasobów platformy Azure, takich jak klaster usługi AKS lub maszyny Wirtualnej platformy Azure bezpiecznego dostępu do rejestru, bez przekraczania granicy sieci. Możesz również skonfigurować reguły zapory sieci do listy dozwolonych określonych publicznej sieci internet zakresów adresów IP. 

Aby ograniczyć dostęp do rejestru, najpierw zmienić domyślnej akcji rejestru, aby go nie zezwala na wszystkie połączenia sieciowe. Następnie należy dodać reguły dostępu do sieci. Klienci udzielono dostępu za pomocą reguł sieci muszą w dalszym [uwierzytelniania rejestru kontenerów](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) i mieć możliwość dostępu do danych.

### <a name="service-endpoint-for-subnets"></a>Punkt końcowy usługi dla podsieci

Aby zezwolić na dostęp z podsieci w sieci wirtualnej, należy dodać [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla usługi Azure Container Registry. 

Wielodostępnych usług, takich jak usługa Azure Container Registry, użyj jeden zestaw adresów IP dla wszystkich klientów. Punkt końcowy usługi przypisuje punktu końcowego uzyskać dostępu do rejestru. Ten punkt końcowy umożliwia ruch optymalną trasę do zasobu za pośrednictwem sieci szkieletowej platformy Azure. Tożsamości w sieci wirtualnej i podsieci również są przesyłane z każdym żądaniem.

### <a name="firewall-rules"></a>Reguły zapory

Reguły sieciowych adresów IP, podaj internet dozwolonych zakresów adresów, takich jak za pomocą notacji CIDR *16.17.18.0/24* lub poszczególne adresy IP są takie jak *16.17.18.19*. Reguły sieciowych adresów IP są dozwolone tylko dla *publicznych* adresy IP w Internecie. Zakresy adresów IP zarezerwowanych dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) nie jest dozwolone w regułach adresów IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Utwórz maszynę wirtualną z obsługą platformy Docker

W tym artykule umożliwiają dostęp do usługi Azure container registry z obsługą platformy Docker maszyny Wirtualnej systemu Ubuntu. Aby użyć uwierzytelniania usługi Azure Active Directory do rejestru, należy także zainstalować [wiersza polecenia platformy Azure][azure-cli] na maszynie Wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, Pomiń ten krok tworzenia.

Może używać tej samej grupy zasobów dla maszyny wirtualnej i usługi container registry. Ten Instalator upraszcza Oczyszczanie na końcu, ale nie jest wymagane. Jeśli zdecydujesz się utworzyć oddzielnej grupy zasobów dla maszyny wirtualnej i sieci wirtualnej, uruchom [Tworzenie grupy az][az-group-create]. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *westcentralus* lokalizacji:

```azurecli
az group create --name myResourceGroup --location westus
```

Teraz Wdróż domyślne maszyny wirtualnej platformy Azure z systemem Ubuntu przy użyciu [tworzenie az vm][az-vm-create]. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu wykonywania polecenia Zwróć uwagę na `publicIpAddress` wyświetlane w interfejsie wiersza polecenia platformy Azure. Nawiązać połączenia SSH z maszyną wirtualną i, opcjonalnie nowsze ustawienia reguł zapory, należy użyć tego adresu.

### <a name="install-docker-on-the-vm"></a>Zainstalowanie platformy Docker na maszynie Wirtualnej

Po uruchomieniu maszyny Wirtualnej Utwórz połączenie SSH z maszyną wirtualną. Zastąp *publicznego adresu IP* z publicznym adresem IP swojej maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenie, aby zainstalować platformę Docker na maszynie Wirtualnej z systemem Ubuntu:

```bash
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy platformy Docker jest prawidłowo uruchomiona na maszynie Wirtualnej:

```bash
sudo docker run -it hello-world
```

Dane wyjściowe:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Postępuj zgodnie z instrukcjami w [zainstalować interfejs wiersza polecenia w usłudze Azure za pomocą narzędzia apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) do zainstalowania interfejsu wiersza polecenia platformy Azure na maszynie wirtualnej systemu Ubuntu. W tym artykule upewnij się, zainstalowanie wersji 2.0.58 lub nowszej.

Zamknij połączenie SSH.

## <a name="allow-access-from-a-virtual-network"></a>Zezwalaj na dostęp z sieci wirtualnej

W tej sekcji należy skonfigurować usługi container registry, aby zezwolić na dostęp z podsieci w sieci wirtualnej platformy Azure. Podano równoważne kroki przy użyciu wiersza polecenia platformy Azure i witryny Azure portal.

### <a name="allow-access-from-a-virtual-network---cli"></a>Zezwalaj na dostęp z sieci wirtualnej — interfejs wiersza polecenia

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Dodawanie punktu końcowego usługi do podsieci

Podczas tworzenia maszyny Wirtualnej, platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej opiera się na nazwę maszyny wirtualnej. Na przykład, jeśli nazwa maszyny wirtualnej *myDockerVM*, domyślna nazwa sieci wirtualnej to *myDockerVMVNET*, z podsiecią o nazwie *myDockerVMSubnet*. To sprawdzić w witrynie Azure portal lub za pomocą [az sieci vnet list][az-network-vnet-list] polecenia:

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Dane wyjściowe:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Użyj [aktualizacji podsieci sieci wirtualnej sieci az][az-network-vnet-subnet-update] polecenie, aby dodać **Microsoft.ContainerRegistry** punktu końcowego usługi do podsieci. Zastąp nazwy sieci wirtualnej i podsieci w następującym poleceniu:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Użyj [Pokaż podsieci sieci wirtualnej sieci az][az-network-vnet-subnet-show] polecenie, aby pobrać identyfikator zasobu podsieci. Jest to potrzebne na późniejszym etapie, aby skonfigurować regułę dostępu do sieci.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Dane wyjściowe:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Zmiana domyślnego dostępu sieciowego do rejestru

Domyślnie rejestr Azure container registry zezwala na połączenia z hostów w sieci. Aby ograniczyć dostęp do wybranej sieci, należy zmienić domyślnej akcji, aby odmówić dostępu. Zastąp nazwą swojego rejestru w następującym [az acr update][az-acr-update] polecenia:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguł sieci do rejestru

Użyj [dodać reguły sieciowej az acr][az-acr-network-rule-add] polecenie, aby dodać regułę sieciowej do rejestru, które zezwalają na dostęp z podsieci maszyny Wirtualnej. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci w następującym poleceniu: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

W dalszym ciągu [zweryfikować dostępu do rejestru](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Zezwalaj na dostęp z sieci wirtualnej — portal

#### <a name="add-service-endpoint-to-subnet"></a>Dodawanie punktu końcowego usługi do podsieci

Podczas tworzenia maszyny Wirtualnej, platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej opiera się na nazwę maszyny wirtualnej. Na przykład, jeśli nazwa maszyny wirtualnej *myDockerVM*, domyślna nazwa sieci wirtualnej to *myDockerVMVNET*, z podsiecią o nazwie *myDockerVMSubnet*.

Aby dodać punkt końcowy usługi dla usługi Azure Container Registry do podsieci:

1. W polu wyszukiwania w górnej części [witryny Azure portal][azure-portal], wprowadź *sieci wirtualnych*. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Z listy sieci wirtualnych, wybierz sieć wirtualną, gdzie maszyna wirtualna jest wdrażana, takich jak *myDockerVMVNET*.
1. W obszarze **ustawienia**, wybierz opcję **podsieci**.
1. Wybierz podsieć, gdzie maszyna wirtualna jest wdrażana, takich jak *myDockerVMSubnet*.
1. W obszarze **punkty końcowe usługi**, wybierz opcję **Microsoft.ContainerRegistry**.
1. Wybierz pozycję **Zapisz**.

![Dodawanie punktu końcowego usługi do podsieci][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurowanie dostępu do sieci dla rejestru

Domyślnie rejestr Azure container registry zezwala na połączenia z hostów w sieci. Aby ograniczyć dostęp do sieci wirtualnej:

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **ustawienia**, wybierz opcję **zapory i sieci wirtualne**.
1. Aby odmówić dostępu domyślnie, chce zezwolić na dostęp z **wybrane sieci**. 
1. Wybierz **Dodaj istniejącą sieć wirtualną**, a następnie wybierz sieć wirtualną i skonfigurowanej przy użyciu punktu końcowego usługi podsieci. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Zapisz**.

![Konfigurowanie sieci wirtualnej dla rejestru kontenerów][acr-vnet-portal]

W dalszym ciągu [zweryfikować dostępu do rejestru](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Zezwalaj na dostęp z adresu IP

W tej sekcji należy skonfigurować usługi container registry, aby zezwolić na dostęp z określonego adresu IP lub zakresu. Podano równoważne kroki przy użyciu wiersza polecenia platformy Azure i witryny Azure portal.

### <a name="allow-access-from-an-ip-address---cli"></a>Zezwalaj na dostęp z adresu IP — interfejs wiersza polecenia

#### <a name="change-default-network-access-to-registry"></a>Zmiana domyślnego dostępu sieciowego do rejestru

Jeśli jeszcze tego nie zrobiono, należy zaktualizować konfiguracji rejestru, aby odmówić dostępu domyślnie. Zastąp nazwą swojego rejestru w następującym [az acr update][az-acr-update] polecenia:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Usuń regułę sieci z rejestru

Jeśli dodano wcześniej regułę sieciowej, aby zezwolić na dostęp z podsieci maszyny Wirtualnej, należy usunąć punktu końcowego usługi podsieci i zasad sieci. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci, pobrać w poprzednim kroku w [Usuń reguły sieciowej az acr][az-acr-network-rule-remove] polecenia: 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguł sieci do rejestru

Użyj [dodać reguły sieciowej az acr][az-acr-network-rule-add] polecenie, aby dodać regułę sieciowej do rejestru, które zezwalają na dostęp z adresu IP maszyny Wirtualnej. Zastąp nazwę rejestru kontenerów i publiczny adres IP maszyny Wirtualnej w następującym poleceniu.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

W dalszym ciągu [zweryfikować dostępu do rejestru](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Zezwalaj na dostęp z adresu IP — portal

#### <a name="remove-existing-network-rule-from-registry"></a>Usuń istniejącą regułę sieci z rejestru

Jeśli dodano wcześniej regułę sieciowej, aby zezwolić na dostęp z podsieci maszyny Wirtualnej, należy usunąć istniejącą regułę. Pomiń tę sekcję, jeśli chcesz uzyskać dostęp do rejestru z innej maszyny Wirtualnej.

* Zaktualizuj ustawienia podsieci, aby usunąć punktu końcowego usługi podsieci dla usługi Azure Container Registry. 

  1. W [witryny Azure portal][azure-portal], przejdź do sieci wirtualnej, w którym wdrożono maszynę wirtualną.
  1. W obszarze **ustawienia**, wybierz opcję **podsieci**.
  1. Wybierz podsieć, w którym wdrożono maszynę wirtualną.
  1. W obszarze **punkty końcowe usługi**, usuń pole wyboru **Microsoft.ContainerRegistry**. 
  1. Wybierz pozycję **Zapisz**.

* Usuń regułę sieci, która umożliwia podsieci w celu dostępu do rejestru.

  1. W portalu przejdź do rejestru kontenerów.
  1. W obszarze **ustawienia**, wybierz opcję **zapory i sieci wirtualne**.
  1. W obszarze **sieci wirtualne**, wybierz nazwę sieci wirtualnej, a następnie wybierz **Usuń**.
  1. Wybierz pozycję **Zapisz**.

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguł sieci do rejestru

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **ustawienia**, wybierz opcję **zapory i sieci wirtualne**.
1. Jeśli nie zostało to jeszcze zrobione, chce zezwolić na dostęp z **wybrane sieci**. 
1. W obszarze **sieci wirtualne**, upewnij się, sieć nie jest zaznaczone.
1. W obszarze **zapory**, wprowadź publiczny adres IP maszyny wirtualnej. Lub wprowadź zakres adresów w notacji CIDR, który zawiera adres IP maszyny Wirtualnej.
1. Wybierz pozycję **Zapisz**.

![Konfigurowanie reguły zapory dla rejestru kontenerów][acr-vnet-firewall-portal]

W dalszym ciągu [zweryfikować dostępu do rejestru](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Weryfikowanie dostępu do rejestru

Po upływie kilku minut dla konfiguracji aktualizacji, upewnij się, że maszyny Wirtualnej można uzyskać dostęp rejestru kontenerów. Utwórz połączenie SSH z maszyną wirtualną, a następnie uruchom [az acr login][az-acr-login] polecenia do logowania do rejestru. 

```bash
az acr login --name mycontainerregistry
```

Można wykonywać operacje rejestru, takie jak uruchamianie `docker pull` do ściągania przykładowy obraz z rejestru. Zastąp wartość obrazu i tagu odpowiednią dla Twojego rejestru prefiksem (tylko małe litery) nazwę serwera logowania rejestru:

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker pomyślnie ściąga obraz na maszynie wirtualnej.

W przykładzie pokazano, że masz dostęp prywatnego rejestru kontenera za pomocą reguł dostępu do sieci. Jednak rejestru nie jest dostępny z hosta innej nazwy logowania, która nie ma skonfigurowane reguły dostępu do sieci. Jeśli próba logowania z innym hoście za pomocą `az acr login` polecenia lub `docker login` poleceń, dane wyjściowe będą podobne do następującego:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Przywróć domyślne uprawnienia dostępu z rejestru

Aby przywrócić rejestr, aby zezwolić na dostęp, domyślnie, Usuń wszystkie reguły sieci, które są skonfigurowane. Następnie ustaw domyślną akcję, aby zezwolić na dostęp. Podano równoważne kroki przy użyciu wiersza polecenia platformy Azure i witryny Azure portal.

### <a name="restore-default-registry-access---cli"></a>Przywróć domyślne uprawnienia dostępu rejestru — interfejs wiersza polecenia

#### <a name="remove-network-rules"></a>Usuwanie reguł sieci

Aby wyświetlić listę reguł sieci skonfigurowane dla rejestru, uruchom następujące polecenie [az acr reguły sieciowej listy][az-acr-network-rule-list] polecenia:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Dla każdej reguły, która jest skonfigurowana, uruchom [Usuń reguły sieciowej az acr][az-acr-network-rule-remove] polecenie, aby go usunąć. Na przykład:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Zezwalaj na dostęp

Zastąp nazwą swojego rejestru w następującym [az acr update][az-acr-update] polecenia:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Przywracanie domyślnego dostępu do rejestru — portal


1. W portalu, przejdź do usługi container registry, a następnie wybierz pozycję **zapory i sieci wirtualne**.
1. W obszarze **sieci wirtualne**wybierz każdej sieci wirtualnej, a następnie wybierz pozycję **Usuń**.
1. W obszarze **zapory**, zaznacz każdy zakres adresów, a następnie wybierz ikonę Usuń.
1. W obszarze **dostęp z**, wybierz opcję **wszystkie sieci**. 
1. Wybierz pozycję **Zapisz**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono wszystkie grupy zasobów platformy Azure, w tym samym zasobie i nie są już potrzebne, możesz opcjonalnie usunąć zasoby przy użyciu pojedynczej [usunięcie grupy az](/cli/azure/group) polecenia:

```azurecli
az group delete --name myResourceGroup
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów myResourceGroup. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** Aby usunąć grupę zasobów i zasoby tam przechowywane.

## <a name="next-steps"></a>Kolejne kroki

Kilka zasoby sieci wirtualnej i funkcje zostały omówionych w tym artykule, chociaż krótko. Dokumentacji usługi Azure Virtual Network, które często obejmuje następujące tematy:

* [Sieć wirtualna](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Podsieć](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Punkty końcowe usługi](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

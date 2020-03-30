---
title: Ograniczanie dostępu za pomocą sieci wirtualnej
description: Zezwalaj na dostęp do rejestru kontenerów platformy Azure tylko z zasobów w sieci wirtualnej platformy Azure lub z publicznych zakresów adresów IP.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454333"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Ograniczanie dostępu do rejestru kontenerów platformy Azure przy użyciu reguł sieci wirtualnej lub zapory platformy Azure

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną sieć prywatną dla zasobów platformy Azure i lokalnych. Ograniczając dostęp do prywatnego rejestru kontenerów platformy Azure z sieci wirtualnej platformy Azure, upewnij się, że tylko zasoby w sieci wirtualnej uzyskują dostęp do rejestru. W przypadku scenariuszy międzylokacjowych można również skonfigurować reguły zapory, aby zezwolić na dostęp do rejestru tylko z określonych adresów IP.

W tym artykule przedstawiono dwa scenariusze konfigurowania reguł dostępu do sieci przychodzącej w rejestrze kontenerów: z maszyny wirtualnej wdrożonej w sieci wirtualnej lub z publicznego adresu IP maszyny Wirtualnej.

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej i obowiązują pewne [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

Jeśli zamiast tego musisz skonfigurować reguły dostępu dla zasobów, aby uzyskać dostęp do rejestru kontenerów zza zapory, zobacz [Konfigurowanie reguł, aby uzyskać dostęp do rejestru kontenerów platformy Azure za zaporą](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Tylko rejestr kontenerów **premium** można skonfigurować z regułami dostępu do sieci. Aby uzyskać informacje dotyczące warstw usług rejestru, zobacz [Jednostki SKU rejestru kontenerów platformy Azure](container-registry-skus.md). 

* Tylko klaster [usługi Azure Kubernetes](../aks/intro-kubernetes.md) lub [maszyna wirtualna](../virtual-machines/linux/overview.md) platformy Azure może służyć jako host do uzyskiwania dostępu do rejestru kontenerów w sieci wirtualnej. *Inne usługi platformy Azure, w tym wystąpienia kontenera platformy Azure nie są obecnie obsługiwane.*

* [Operacje zadań usługi ACR](container-registry-tasks-overview.md) nie są obecnie obsługiwane w rejestrze kontenerów uzyskiwał dostęp w sieci wirtualnej.

* Każdy rejestr obsługuje maksymalnie 100 reguł sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby użyć kroki interfejsu wiersza polecenia platformy Azure w tym artykule, azure cli w wersji 2.0.58 lub nowszej jest wymagane. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

* Jeśli nie masz jeszcze rejestru kontenerów, utwórz jeden (wymagana jednostka SKU `hello-world` w wersji Premium) i wypchnij przykładowy obraz, taki jak z centrum platformy Docker Hub. Na przykład użyj [witryny Azure portal][quickstart-portal] lub interfejsu [wiersza polecenia platformy Azure,][quickstart-cli] aby utworzyć rejestr. 

* Jeśli chcesz ograniczyć dostęp do rejestru przy użyciu sieci wirtualnej w innej subskrypcji platformy Azure, musisz zarejestrować dostawcę zasobów dla rejestru kontenerów platformy Azure w tej subskrypcji. Przykład:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Reguły sieciowe rejestru kontenerów – informacje

Rejestr kontenerów platformy Azure domyślnie akceptuje połączenia przez Internet z hostów w dowolnej sieci. Za pomocą sieci wirtualnej można zezwolić tylko zasobom platformy Azure, takim jak klaster AKS lub maszyna wirtualna platformy Azure, na bezpieczny dostęp do rejestru bez przekraczania granicy sieci. Można również skonfigurować reguły zapory sieciowej, aby zezwalać tylko na określone zakresy publicznych adresów IP. 

Aby ograniczyć dostęp do rejestru, należy najpierw zmienić domyślną akcję rejestru, tak aby odmówiła ona wszystkich połączeń sieciowych. Następnie dodaj reguły dostępu do sieci. Klienci, którym udzielono dostępu za pośrednictwem reguł sieciowych, muszą nadal [uwierzytelniać się w rejestrze kontenerów](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) i być autoryzowani do uzyskiwania dostępu do danych.

### <a name="service-endpoint-for-subnets"></a>Punkt końcowy usługi dla podsieci

Aby zezwolić na dostęp z podsieci w sieci wirtualnej, należy dodać [punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla usługi Azure Container Registry. 

Usługi z wieloma dzierżawcami, takie jak usługa Azure Container Registry, używają jednego zestawu adresów IP dla wszystkich klientów. Punkt końcowy usługi przypisuje punkt końcowy, aby uzyskać dostęp do rejestru. Ten punkt końcowy zapewnia ruch optymalną trasę do zasobu za pośrednictwem sieci szkieletowej platformy Azure. Tożsamości sieci wirtualnej i podsieci są również przesyłane z każdym żądaniem.

### <a name="firewall-rules"></a>Reguły zapory

W przypadku reguł sieci IP podaj dozwolone zakresy adresów internetowych przy użyciu notacji CIDR, takie jak *16.17.18.0/24* lub indywidualne adresy IP, takie jak *16.17.18.19*. Reguły sieci IP są dozwolone tylko dla *publicznych* internetowych adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w RFC 1918) nie są dozwolone w regułach IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

W tym artykule użyj maszyny Wirtualnej Ubuntu z włączoną funkcją platformy Docker, aby uzyskać dostęp do rejestru kontenerów platformy Azure. Aby użyć uwierzytelniania usługi Azure Active Directory do rejestru, należy również zainstalować [interfejsu wiersza polecenia platformy Azure][azure-cli] na maszynie Wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, pomiń ten krok tworzenia.

Można użyć tej samej grupy zasobów dla maszyny wirtualnej i rejestru kontenerów. Ta konfiguracja upraszcza oczyszczanie na końcu, ale nie jest wymagana. Jeśli zdecydujesz się utworzyć oddzielną grupę zasobów dla maszyny wirtualnej i sieci wirtualnej, uruchom [grupę AZ Create][az-group-create]. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji westcentralus:*

```azurecli
az group create --name myResourceGroup --location westus
```

Teraz wdrożyć domyślną maszynę wirtualną Platformy Azure Ubuntu z [az vm create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu polecenia należy wziąć `publicIpAddress` pod uwagę wyświetlane przez interfejsu wiersza polecenia platformy Azure. Ten adres służy do nawiązywać połączenia SSH z maszyną wirtualną i opcjonalnie w celu późniejszej konfiguracji reguł zapory.

### <a name="install-docker-on-the-vm"></a>Instalowanie platformy Docker na maszynie Wirtualnej

Po uruchomieniu maszyny Wirtualnej nawiązać połączenie SSH z maszyną wirtualną. Zamień *publicIpAddress* na publiczny adres IP maszyny Wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenie, aby zainstalować dok na maszynie Wirtualnej Ubuntu:

```bash
sudo apt install docker.io -y
```

Po instalacji uruchom następujące polecenie, aby sprawdzić, czy docker działa poprawnie na maszynie Wirtualnej:

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

Wykonaj kroki opisane w [install Azure CLI z apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej Ubuntu. W tym artykule upewnij się, że instalujesz wersję 2.0.58 lub nowszą.

Wyjdź z połączenia SSH.

## <a name="allow-access-from-a-virtual-network"></a>Zezwalaj na dostęp z sieci wirtualnej

W tej sekcji skonfiguruj rejestr kontenerów, aby zezwolił na dostęp z podsieci w sieci wirtualnej platformy Azure. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i witryny Azure Portal.

### <a name="allow-access-from-a-virtual-network---cli"></a>Zezwalaj na dostęp z sieci wirtualnej — interfejs wiersza polecenia

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Dodawanie punktu końcowego usługi do podsieci

Podczas tworzenia maszyny Wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład, jeśli nazwa maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsieci o nazwie *myDockerVMSubnet*. Sprawdź to w witrynie Azure portal lub za pomocą polecenia [az network vnet list:][az-network-vnet-list]

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

Użyj polecenia [aktualizacji podsieci sieci AZ,][az-network-vnet-subnet-update] aby dodać punkt końcowy usługi **Microsoft.ContainerRegistry** do podsieci. Zastąp nazwy sieci wirtualnej i podsieci w następującym poleceniu:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Polecenie [pokaż podsieć sieci az umożliwia][az-network-vnet-subnet-show] pobranie identyfikatora zasobu podsieci. Jest to potrzebne w późniejszym kroku, aby skonfigurować regułę dostępu do sieci.

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

#### <a name="change-default-network-access-to-registry"></a>Zmienianie domyślnego dostępu do sieci w rejestrze

Domyślnie rejestr kontenerów platformy Azure umożliwia połączenia z hostów w dowolnej sieci. Aby ograniczyć dostęp do wybranej sieci, zmień akcję domyślną, aby odmówić dostępu. Zastąp nazwę rejestru [następującym poleceniem aktualizacji az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieciowej do rejestru

Użyj polecenia [az acr network-rule add,][az-acr-network-rule-add] aby dodać regułę sieci do rejestru, która umożliwia dostęp z podsieci maszyny Wirtualnej. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci w następującym poleceniu: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Kontynuuj [weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Zezwalaj na dostęp z sieci wirtualnej — portal

#### <a name="add-service-endpoint-to-subnet"></a>Dodawanie punktu końcowego usługi do podsieci

Podczas tworzenia maszyny Wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład, jeśli nazwa maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsieci o nazwie *myDockerVMSubnet*.

Aby dodać punkt końcowy usługi dla rejestru kontenerów platformy Azure do podsieci:

1. W polu wyszukiwania u góry [witryny Azure portal][azure-portal]wprowadź sieci *wirtualne*. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
1. Z listy sieci wirtualnych wybierz sieć wirtualną, w której jest wdrażana maszyna wirtualna, na przykład *myDockerVMVNET*.
1. W obszarze **Ustawienia**wybierz pozycję **Podsieci**.
1. Wybierz podsieć, w której jest wdrażana maszyna wirtualna, na przykład *myDockerVMSubnet*.
1. W obszarze **Punkty końcowe usługi**wybierz pozycję **Microsoft.ContainerRegistry**.
1. Wybierz **pozycję Zapisz**.

![Dodawanie punktu końcowego usługi do podsieci][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurowanie dostępu do sieci dla rejestru

Domyślnie rejestr kontenerów platformy Azure umożliwia połączenia z hostów w dowolnej sieci. Aby ograniczyć dostęp do sieci wirtualnej:

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz **pozycję Zapora i sieci wirtualne**.
1. Aby domyślnie odmówić dostępu, wybierz opcję zezwalania na dostęp z **wybranych sieci**. 
1. Wybierz **pozycję Dodaj istniejącą sieć wirtualną**i wybierz sieć wirtualną i podsieć skonfigurowaną z punktem końcowym usługi. Wybierz pozycję **Dodaj**.
1. Wybierz **pozycję Zapisz**.

![Konfigurowanie sieci wirtualnej dla rejestru kontenerów][acr-vnet-portal]

Kontynuuj [weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Zezwalaj na dostęp z adresu IP

W tej sekcji skonfiguruj rejestr kontenerów, aby zezwolił na dostęp z określonego adresu IP lub zakresu. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i witryny Azure Portal.

### <a name="allow-access-from-an-ip-address---cli"></a>Zezwalaj na dostęp z adresu IP — cli

#### <a name="change-default-network-access-to-registry"></a>Zmienianie domyślnego dostępu do sieci w rejestrze

Jeśli jeszcze tego nie zrobiono, należy zaktualizować konfigurację rejestru, aby domyślnie odmówić dostępu. Zastąp nazwę rejestru [następującym poleceniem aktualizacji az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Usuwanie reguły sieciowej z rejestru

Jeśli wcześniej dodano regułę sieciową, aby zezwolić na dostęp z podsieci maszyny Wirtualnej, usuń punkt końcowy usługi podsieci i regułę sieci. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci pobranej we wcześniejszym kroku w poleceniu [az acr network-rule remove:][az-acr-network-rule-remove] 

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

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieciowej do rejestru

Użyj polecenia [az acr network-rule add,][az-acr-network-rule-add] aby dodać regułę sieciową do rejestru, która umożliwia dostęp z adresu IP maszyny Wirtualnej. Zastąp nazwę rejestru kontenerów i publiczny adres IP maszyny Wirtualnej w następującym poleceniu.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Kontynuuj [weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Zezwalaj na dostęp z adresu IP - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Usuwanie istniejącej reguły sieciowej z rejestru

Jeśli wcześniej dodano regułę sieciową, aby zezwolić na dostęp z podsieci maszyny Wirtualnej, usuń istniejącą regułę. Pomiń tę sekcję, jeśli chcesz uzyskać dostęp do rejestru z innej maszyny Wirtualnej.

* Zaktualizuj ustawienia podsieci, aby usunąć punkt końcowy usługi podsieci dla rejestru kontenerów platformy Azure. 

  1. W [witrynie Azure portal][azure-portal]przejdź do sieci wirtualnej, w której jest wdrażana maszyna wirtualna.
  1. W obszarze **Ustawienia**wybierz pozycję **Podsieci**.
  1. Wybierz podsieć, w której jest wdrażana maszyna wirtualna.
  1. W obszarze **Punkty końcowe usługi**usuń pole wyboru dla **microsoft.ContainerRegistry**. 
  1. Wybierz **pozycję Zapisz**.

* Usuń regułę sieci, która umożliwia podsieci dostęp do rejestru.

  1. W portalu przejdź do rejestru kontenerów.
  1. W obszarze **Ustawienia**wybierz **pozycję Zapora i sieci wirtualne**.
  1. W **obszarze Sieci wirtualne**wybierz nazwę sieci wirtualnej, a następnie wybierz pozycję **Usuń**.
  1. Wybierz **pozycję Zapisz**.

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieciowej do rejestru

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz **pozycję Zapora i sieci wirtualne**.
1. Jeśli jeszcze tego nie zrobiono, wybierz opcję zezwalania na dostęp z **wybranych sieci**. 
1. W **obszarze Sieci wirtualne**upewnij się, że nie wybrano żadnej sieci.
1. W obszarze **Zapora**wprowadź publiczny adres IP maszyny Wirtualnej. Można też wprowadzić zakres adresów w notacji CIDR zawierający adres IP maszyny Wirtualnej.
1. Wybierz **pozycję Zapisz**.

![Konfigurowanie reguły zapory dla rejestru kontenerów][acr-vnet-firewall-portal]

Kontynuuj [weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Weryfikowanie dostępu do rejestru

Po odczekaniu kilku minut na aktualizację konfiguracji sprawdź, czy maszyna wirtualna może uzyskać dostęp do rejestru kontenerów. Nawiązuj połączenie SSH z maszyną wirtualną i uruchom polecenie [logowania az acr,][az-acr-login] aby zalogować się do rejestru. 

```bash
az acr login --name mycontainerregistry
```

Można wykonać operacje rejestru, `docker pull` takie jak uruchamianie, aby pobrać przykładowy obraz z rejestru. Zastąp obraz i wartość znacznika odpowiednią dla rejestru, poprzedzony nazwą serwera logowania rejestru (wszystkie małe litery):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker pomyślnie ściąga obraz do maszyny Wirtualnej.

W tym przykładzie pokazano, że można uzyskać dostęp do rejestru kontenerów prywatnych za pośrednictwem reguły dostępu do sieci. Jednak nie można uzyskać dostępu do rejestru z innego hosta logowania, który nie ma skonfigurowaną reguły dostępu do sieci. Jeśli spróbujesz zalogować się `az acr login` z `docker login` innego hosta za pomocą polecenia lub polecenia, dane wyjściowe są podobne do następujących:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Przywracanie domyślnego dostępu do rejestru

Aby przywrócić rejestr, aby domyślnie zezwolić na dostęp, usuń wszystkie skonfigurowane reguły sieciowe. Następnie ustaw domyślną akcję, aby zezwolić na dostęp. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i witryny Azure Portal.

### <a name="restore-default-registry-access---cli"></a>Przywracanie domyślnego dostępu do rejestru — cli

#### <a name="remove-network-rules"></a>Usuwanie reguł sieciowych

Aby wyświetlić listę reguł sieciowych skonfigurowanych dla rejestru, uruchom następujące polecenie [az acr network-rule list:][az-acr-network-rule-list]

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Dla każdej skonfigurowanyej reguły uruchom polecenie [az acr network-rule remove,][az-acr-network-rule-remove] aby ją usunąć. Przykład:

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

#### <a name="allow-access"></a>Zezwól na dostęp

Zastąp nazwę rejestru [następującym poleceniem aktualizacji az acr:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Przywracanie domyślnego dostępu do rejestru — portal


1. W portalu przejdź do rejestru kontenerów i wybierz **pozycję Zapora i sieci wirtualne**.
1. W **obszarze Sieci wirtualne**wybierz każdą sieć wirtualną, a następnie wybierz pozycję **Usuń**.
1. W **obszarze Zapora**wybierz każdy zakres adresów, a następnie wybierz ikonę Usuń.
1. W obszarze **Zezwalaj na dostęp z**wybierz **pozycję Wszystkie sieci**. 
1. Wybierz **pozycję Zapisz**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono wszystkie zasoby platformy Azure w tej samej grupie zasobów i nie są już potrzebne, można opcjonalnie usunąć zasoby za pomocą jednego polecenia [az delete grupy:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów myResourceGroup. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów,** aby usunąć grupę zasobów i przechowywane tam zasoby.

## <a name="next-steps"></a>Następne kroki

Kilka zasobów sieci wirtualnej i funkcje zostały omówione w tym artykule, choć krótko. Dokumentacja sieci wirtualnej platformy Azure obejmuje te tematy w szerokim zakresie:

* [Sieć wirtualna](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
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

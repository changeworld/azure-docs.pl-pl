---
title: Przechwytywanie maszyny Wirtualnej systemu Linux platformy Azure do użycia jako szablon | Dokumentacja firmy Microsoft
description: Informacje o sposobie przechwytywania i obraz opartych na systemie Linux Azure maszynę wirtualną (VM) utworzone za pomocą modelu wdrażania usługi Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 3b45f46197467dd7b83bd986604338e14daa8107
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Przechwytywanie maszyny wirtualnej systemu Linux działających na platformie Azure
Wykonaj kroki opisane w tym artykule, aby Uogólnij i Przechwyć maszyny wirtualnej systemu Linux platformy Azure (VM) w modelu wdrażania Menedżera zasobów. Uogólnienie maszynę Wirtualną, Usuń konto osobiste informacje i przygotowanie wirtualna do użycia jako obraz. Można następnie przechwytywania obrazu uogólniony wirtualny dysk twardy (VHD) dla systemu operacyjnego, dysków VHD dla dysków dołączonych danych i [szablonu usługi Resource Manager](../../azure-resource-manager/resource-group-overview.md) o nowych wdrożeniach maszyny Wirtualnej. Ten artykuł zawiera szczegóły dotyczące sposobu przechwytywania obrazu maszyny Wirtualnej z interfejsu wiersza polecenia platformy Azure w wersji 1.0 za pomocą niezarządzanych dysków maszyny wirtualnej. Możesz również [Przechwytywanie maszyny Wirtualnej za pomocą dysków zarządzanych Azure 2.0 interfejsu wiersza polecenia Azure](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Dyski zarządzane są obsługiwane przez platformę Azure i nie wymagają wszystkie lub lokalizację do przechowywania ich. Aby uzyskać więcej informacji, zobacz temat [Omówienie usługi Azure Managed Disks](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Do tworzenia maszyn wirtualnych przy użyciu obrazu, konfigurowanie zasobów sieciowych dla każdej nowej maszyny Wirtualnej, a następnie wdrożyć go z przechwyconych obrazów wirtualnego dysku twardego przy użyciu szablonu (plik JavaScript Object Notation lub formatu JSON,). W ten sposób można replikować maszyny Wirtualnej z bieżącej konfiguracji oprogramowania, podobnie jak używać obrazów w portalu Azure Marketplace.

> [!TIP]
> Jeśli chcesz utworzyć kopię istniejącej maszyny Wirtualnej systemu Linux jej stanem specjalne tworzenia kopii zapasowej i debugowania, zobacz [utworzenie kopii maszyny wirtualnej systemu Linux działających na platformie Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A jeśli chcesz przekazać wirtualnego dysku twardego z lokalnej maszyny Wirtualnej systemu Linux, zobacz [przekazywanie i Utwórz Maszynę wirtualną systemu Linux na podstawie obrazu niestandardowego dysku](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#before-you-begin) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami

## <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, że zostały spełnione następujące wymagania wstępne:

* **Maszyna wirtualna platformy Azure utworzonych w modelu wdrażania usługi Resource Manager** — Jeśli nie utworzono Maszynę wirtualną systemu Linux, możesz użyć [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [interfejsu wiersza polecenia Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), lub [szablony Menedżera zasobów ](create-ssh-secured-vm-from-template.md). 
  
    Konfigurowanie maszyny Wirtualnej, zgodnie z potrzebami. Na przykład [Dodaj dyski danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), stosowania aktualizacji i zainstalować aplikacje. 
* **Azure CLI** -Zainstaluj [interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md) na komputerze lokalnym.

## <a name="step-1-remove-the-azure-linux-agent"></a>Krok 1: Usuń agenta systemu Linux platformy Azure
Najpierw uruchom **agenta waagent** z **deprovision** parametru na Maszynie wirtualnej systemu Linux. To polecenie usuwa pliki i dane w celu przygotowania do uogólnianie maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika agenta systemu Linux Azure](../extensions/agent-windows.md).

1. Podłącz do sieci maszyny Wirtualnej systemu Linux przy użyciu klienta SSH.
2. W oknie SSH wpisz następujące polecenie:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > To polecenie można uruchamiać tylko na maszynie Wirtualnej, który ma zostać przechwytywanie w formie obrazu. Nie gwarantuje, że obraz jest wyczyszczone wszystkie informacje poufne lub nadaje się do ponownej dystrybucji.
 
3. Typ **y** aby kontynuować. Możesz dodać **-force** parametr, aby uniknąć tego kroku potwierdzenia.
4. Po zakończeniu wykonywania polecenia, wpisz **zakończyć**. Ten krok powoduje zamknięcie klienta SSH.

## <a name="step-2-capture-the-vm"></a>Krok 2: Przechwytywanie maszyny Wirtualnej
Użyj interfejsu wiersza polecenia Azure, aby Uogólnij i Przechwyć maszyny Wirtualnej. W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają **myResourceGroup**, **myVnet**, i **myVM**.

1. Na komputerze lokalnym, Otwórz wiersza polecenia platformy Azure i [logowania do subskrypcji platformy Azure](/cli/azure/authenticate-azure-cli). 
2. Upewnij się, że jesteś w trybie Menedżera zasobów.
   
    ```azurecli
    azure config mode arm
    ```
3. Zamknij maszynę Wirtualną, która już anulowana za pomocą następującego polecenia:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Generalize maszyny Wirtualnej za pomocą następującego polecenia:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Teraz uruchom **przechwytywania maszyna wirtualna platformy azure** polecenia, które znajdują się maszyny Wirtualnej. W poniższym przykładzie obrazu wirtualne dyski twarde są przechwytywane z nazwy, począwszy od **MyVHDNamePrefix**i **-t** opcja określa ścieżkę do szablonu **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > Pliki VHD obrazu tworzone domyślnie na tym samym koncie magazynu, która używana oryginalna maszyna wirtualna. Użyj *tego samego konta magazynu* do przechowywania dysków VHD dla wszelkie nowe maszyny wirtualne utworzysz z obrazu. 

6. Aby znaleźć lokalizację przechwycony obraz, otwórz szablon JSON w edytorze tekstów. W **storageProfile**, Znajdź **uri** z **obrazu** znajduje się w **systemu** kontenera. Na przykład jest podobny do identyfikatora URI obrazu dysku systemu operacyjnego `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Tworzenie maszyny Wirtualnej z przechwyconego obrazu
Teraz należy użyć obrazu przy użyciu szablonu, aby utworzyć Maszynę wirtualną systemu Linux. Te kroki pokazują, jak użyć wiersza polecenia platformy Azure i szablon pliku JSON, przechwyconych w celu utworzenia maszyny Wirtualnej w nowej sieci wirtualnej.

### <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Aby użyć szablonu, należy najpierw skonfigurować sieć wirtualną i karty Sieciowej dla nowej maszyny Wirtualnej. Zaleca się tworzenie grupy zasobów dla tych zasobów w lokalizacji przechowywania obrazu maszyny Wirtualnej. Uruchom polecenia podobne do następujących, zastępując nazwy zasobów i odpowiednie lokalizacji platformy Azure ("centralus" w tych poleceniach):

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Pobierz identyfikator karty Sieciowej
Aby wdrożyć Maszynę wirtualną z obrazu przy użyciu JSON zapisane podczas przechwytywania, potrzebny jest identyfikator karty sieciowej. Go uzyskać, uruchamiając następujące polecenie:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

**Identyfikator** w danych wyjściowych jest podobny do `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
Teraz uruchom następujące polecenie w celu utworzenia maszyny Wirtualnej z przechwyconego obrazu maszyny Wirtualnej. Użyj **-f** parametr, aby określić ścieżkę do pliku JSON szablonu został zapisany.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

W danych wyjściowych polecenia zostanie wyświetlony monit podaj nową nazwę maszyny Wirtualnej, nazwa użytkownika administratora i hasła oraz identyfikator karty Sieciowej, którą utworzono wcześniej.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

Poniższy przykład pokazuje, zobacz pomyślne wdrożenie:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Weryfikacja wdrażania
Teraz SSH dla maszyny wirtualnej utworzonej do weryfikowania wdrożenia i rozpocząć korzystanie z nowej maszyny Wirtualnej. Aby połączyć się za pośrednictwem protokołu SSH, znaleźć adres IP maszyny wirtualnej został utworzony za pomocą następującego polecenia:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

Publiczny adres IP znajduje się w danych wyjściowych polecenia. Domyślnie można podłączyć się do maszyny Wirtualnej systemu Linux przez SSH na port 22.

## <a name="create-additional-vms"></a>Utwórz dodatkowe maszyny wirtualne
Skorzystaj z przechwyconego obrazu i szablonu można wdrożyć dodatkowe maszyny wirtualne, korzystając z procedury w poprzedniej sekcji. Inne opcje do tworzenia maszyn wirtualnych z obrazu obejmują przy użyciu szablonu Szybki Start lub systemem **tworzenia maszyny wirtualnej azure** polecenia.

### <a name="use-the-captured-template"></a>Użyj szablonu przechwycony
Aby użyć przechwyconego obrazu szablonu, wykonaj następujące kroki, (szczegóły w poprzedniej sekcji):

* Upewnij się, że obraz maszyny Wirtualnej ma na tym samym koncie magazynu, który jest hostem wirtualnego dysku twardego maszyny Wirtualnej.
* Skopiuj plik JSON szablonu i określ unikatową nazwę dysku systemu operacyjnego, dysku VHD nowej maszyny Wirtualnej (lub wirtualne dyski twarde). Na przykład w **storageProfile**w obszarze **wirtualnego dysku twardego**w **uri**, określ unikatową nazwę dla **osDisk** wirtualnego dysku twardego, podobnie jak `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Utwórz kartę Sieciową, w tym samym lub różnych sieci wirtualnej.
* Przy użyciu pliku JSON zmodyfikowany szablon, Utwórz wdrożenie w grupie zasobów, w którym Konfigurowanie sieci wirtualnej.

### <a name="use-a-quickstart-template"></a>Szablon szybkiego startu
Jeśli chcesz skonfigurować automatycznie po utworzeniu maszyny Wirtualnej z obrazu sieci, można określić te zasoby w szablonie. Na przykład, zobacz [101 maszyny wirtualnej użytkownika obrazu z szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) z usługi GitHub. Ten szablon tworzy Maszynę wirtualną z niestandardowego obrazu i niezbędne sieci wirtualnej, publiczny adres IP i zasobów kart interfejsu Sieciowego. Przewodnik przy użyciu szablonu w portalu Azure, zobacz [tworzenia maszyny wirtualnej z obrazu niestandardowego za pomocą szablonu usługi Resource Manager](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Polecenie utworzenia Użyj maszyna wirtualna platformy azure
Zazwyczaj łatwiej jest utworzyć Maszynę wirtualną z obrazu za pomocą szablonu usługi Resource Manager. Można jednak utworzyć maszyny Wirtualnej *imperatively* przy użyciu **tworzenia maszyny wirtualnej azure** z **-Q** (**— urn obrazu**) parametru. Jeśli używasz tej metody należy również przekazać **-d** (**systemu operacyjnego —-dysk-dysk vhd**) parametr, aby określić lokalizację pliku VHD systemu operacyjnego dla nowej maszyny Wirtualnej. Ten plik musi znajdować się w kontenerze wirtualne dyski twarde konta magazynu, w którym jest przechowywany plik VHD obrazu. Polecenie kopiuje dysk VHD dla nowej maszyny Wirtualnej automatycznie **wirtualne dyski twarde** kontenera.

Przed uruchomieniem **tworzenia maszyny wirtualnej azure** przy użyciu obrazu, wykonaj następujące czynności:

1. Utwórz grupę zasobów lub zidentyfikować istniejącą grupę zasobów dla wdrożenia.
2. Utwórz zasób publiczny adres IP i zasobów kart interfejsu Sieciowego dla nowej maszyny Wirtualnej. Aby uzyskać kroki, aby utworzyć sieć wirtualną, publiczny adres IP i kart przy użyciu interfejsu wiersza polecenia Zobacz wcześniej w tym artykule. (**tworzenia maszyny wirtualnej azure** można również utworzyć karty Sieciowej, ale należy przekazywać dodatkowych parametrów dla sieci wirtualnej i podsieci.)

Następnie uruchom polecenie, które przekazuje identyfikatorów URI do nowego pliku wirtualnego dysku twardego systemu operacyjnego i istniejącego obrazu. W tym przykładzie rozmiar Standard_A1 maszyna wirtualna jest utworzona w regionie wschodnie stany USA.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Opcje dodatkowe polecenia, można uruchomić `azure help vm create`.

## <a name="next-steps"></a>Kolejne kroki
Aby zarządzać maszyn wirtualnych z poziomu interfejsu wiersza polecenia, zobacz zadań w [wdrażania i zarządzania maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia Azure](create-ssh-secured-vm-from-template.md).


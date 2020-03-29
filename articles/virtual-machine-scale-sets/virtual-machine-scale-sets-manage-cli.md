---
title: Zarządzanie zestawami skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
description: Typowe polecenia interfejsu wiersza polecenia platformy Azure do zarządzania zestawami skalowania maszyny wirtualnej, takie jak uruchamianie i zatrzymywania wystąpienia lub zmienianie pojemności zestawu skalowania.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: d8f40708cf5a9989146a72e2093de7bad47ffde9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275752"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Zarządzanie zestawem skalowania maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure
W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. W tym artykule opisano niektóre typowe polecenia interfejsu wiersza polecenia platformy Azure, które umożliwiają wykonywanie tych zadań.

Aby wykonać te zadania zarządzania, potrzebujesz najnowszego interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli chcesz utworzyć zestaw skalowania maszyny wirtualnej, możesz [utworzyć zestaw skalowania za pomocą interfejsu wiersza polecenia platformy Azure.](quick-create-cli.md)


## <a name="view-information-about-a-scale-set"></a>Wyświetlanie informacji o zestawie skalowania
Aby wyświetlić ogólne informacje o zestawie skalowania, użyj [programu AZ vmss show](/cli/azure/vmss). Poniższy przykład pobiera informacje o zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *myResourceGroup.* Wprowadź swoje własne nazwy w następujący sposób:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpienia maszyny Wirtualnej w zestawie skalowania, użyj [az vmss list-instances](/cli/azure/vmss). Poniższy przykład zawiera listę wszystkich wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *myResourceGroup.* Podaj własne wartości dla tych nazw:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Aby wyświetlić dodatkowe informacje o określonym `--instance-id` wystąpieniu maszyny Wirtualnej, dodaj parametr do [az vmss get-instance-view](/cli/azure/vmss) i określ wystąpienie do wyświetlenia. W poniższym przykładzie wyświetlają informacje o wystąpieniu maszyny Wirtualnej *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Wprowadź swoje własne nazwy w następujący sposób:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Lista informacji o połączeniu maszyn wirtualnych
Aby połączyć się z maszynami wirtualnymi w zestawie skalowania, można SSH lub RDP do przypisanego publicznego adresu IP i numeru portu. Domyślnie reguły translacji adresów sieciowych (NAT) są dodawane do modułu równoważenia obciążenia platformy Azure, który przekazuje ruch połączeń zdalnych do każdej maszyny Wirtualnej. Aby wyświetlić listę adresów i portów do łączenia się z wystąpieniami maszyn wirtualnych w zestawie [skalowania, użyj az vmss list-instance-connection-info](/cli/azure/vmss). Poniższy przykład zawiera listę informacji o połączeniu dla wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* i w grupie zasobów *myResourceGroup.* Podaj własne wartości dla tych nazw:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Poprzednie polecenia pokazywały informacje o zestawie skalowania i wystąpieniach maszyny Wirtualnej. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Zestaw skalowania tworzy lub usuwa wymaganą liczbę maszyn wirtualnych, a następnie konfiguruje maszyny wirtualne do odbierania ruchu aplikacji.

Aby wyświetlić liczbę bieżących wystąpień w zestawie skalowania, użyj polecenia [az vmss show](/cli/azure/vmss) i zapytania *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Następnie możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania za pomocą polecenia [az vmss scale](/cli/azure/vmss). W poniższym przykładzie ustawiono liczbę maszyn wirtualnych w skali ustawioną na *5:*

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Jeśli zmniejszysz pojemność zestawu skalowania, maszyny wirtualne o najwyższych identyfikatorach wystąpień zostaną najpierw usunięte.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywanie i uruchamianie maszyn wirtualnych w zestawie skalowania
Aby zatrzymać jedną lub więcej maszyn wirtualnych w zestawie skalowania, należy użyć [az vmss stop](/cli/azure/vmss#az-vmss-stop). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. Aby zatrzymać wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją.

Poniższy przykład zatrzymuje wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zatrzymane maszyny wirtualne pozostają przydzielone i nadal ponoszą opłaty obliczeniowe. Jeśli zamiast tego chcesz, aby maszyny wirtualne zostały cofnięte i ponieść tylko opłaty za magazyn, użyj [az vmss deallocate](/cli/azure/vmss). Aby zdelocytować wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją. Poniższy przykład zatrzymuje i przydziela wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Uruchamianie maszyn wirtualnych w zestawie skalowania
Aby uruchomić jedną lub więcej maszyn wirtualnych w zestawie skalowania, użyj [az vmss start](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. Aby uruchomić wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją.

W poniższym przykładzie rozpoczyna się wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchamianie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić jedną lub więcej maszyn wirtualnych w zestawie skalowania, należy użyć [ponownego uruchomienia az vmss](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. Aby ponownie uruchomić wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją.

W poniższym przykładzie wstępuje ponowne uruchomienie wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Usuwanie maszyn wirtualnych z zestawu skalowania
Aby usunąć jedną lub więcej maszyn wirtualnych w zestawie skalowania, użyj [az vmss delete-instances](/cli/azure/vmss). Parametr `--instance-ids` umożliwia określenie jednej lub więcej maszyn wirtualnych do usunięcia. Jeśli określisz * dla identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną usunięte. Aby usunąć wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją.

Poniższy przykład usuwa wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Następne kroki
Inne typowe zadania dotyczące zestawów skalowania obejmują sposób [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md)i [uaktualnianie wystąpień maszyn wirtualnych.](virtual-machine-scale-sets-upgrade-scale-set.md) Można również użyć narzędzia Azure CLI do [konfigurowania reguł automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md).

---
title: Zarządzanie zestawami skalowania maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Pojemność zestawu typowych poleceń interfejsu wiersza polecenia platformy Azure do zarządzania programem Virtual Machine Scale Sets, takie jak uruchamianie i zatrzymywanie wystąpienia lub zmienianie skali.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: b49182ebdcc93c4a51a55f27c3e0bf7a45307b7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618085"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Zarządzanie maszyny wirtualnej zestawu skalowania przy użyciu wiersza polecenia platformy Azure
W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Ten artykuł szczegółowo opisuje niektóre typowe polecenia interfejsu wiersza polecenia platformy Azure, które umożliwiają wykonywanie tych zadań.

Aby wykonać te zadania zarządzania, należy najnowszego interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli musisz utworzyć zestaw skalowania maszyn wirtualnych, możesz to zrobić [Tworzenie zestawu skalowania przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Wyświetl informacje o zestawie skalowania
Aby wyświetlić ogólne informacje o zestawie skalowania, użyj [az vmss show](/cli/azure/vmss). Poniższy przykład pobiera informacje o zestawu skalowania o nazwie *myScaleSet* w *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj [az vmss list-instances](/cli/azure/vmss). Poniższy przykład wyświetla listę wszystkich wystąpień maszyn wirtualnych w zestawie o nazwie skalowania *myScaleSet* w *myResourceGroup* grupy zasobów. Należy podać własne wartości dla tych nazw:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny Wirtualnej, Dodaj `--instance-id` parametr [az vmss get-instance-view](/cli/azure/vmss) i określ wystąpienie, aby wyświetlić. Poniższy przykład wyświetla informacje o wystąpieniu maszyny Wirtualnej *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Wyświetlanie informacji o połączeniu dla maszyn wirtualnych
Aby nawiązać połączenie maszyn wirtualnych w zestawie skalowania, możesz klienta SSH lub RDP do przypisany publiczny adres IP i port numer. Domyślnie reguły translacją adresów sieciowych są dodawane do równoważenia obciążenia platformy Azure, która przesyła dalej ruch połączenia zdalnego do każdej maszyny Wirtualnej. Aby wyświetlić listę adresów i portów służących do łączenia z wystąpieniami maszyn wirtualnych w zestawie skalowania, użyj [az zestawu skalowania maszyn wirtualnych list-instance-connection-info](/cli/azure/vmss). Poniższy przykład wyświetla informacje o połączeniu dla wystąpień maszyn wirtualnych w zestawie o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Należy podać własne wartości dla tych nazw:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Poprzednich poleceniach wykazało, że informacje o zestawie skalowania oraz wystąpień maszyn wirtualnych. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Zestaw skalowania tworzy lub usuwa wymaganą liczbę maszyn wirtualnych, a następnie konfiguruje maszynom wirtualnym odbierać ruchu aplikacji.

Aby wyświetlić liczbę bieżących wystąpień w zestawie skalowania, użyj polecenia [az vmss show](/cli/azure/vmss) i zapytania *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Następnie możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania za pomocą polecenia [az vmss scale](/cli/azure/vmss). W poniższym przykładzie ustawiono liczbę maszyn wirtualnych w zestawie skalowania jest ustawiony na *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Jeśli zmniejszyć pojemność skalowania ustawiony, maszyny wirtualne o najwyższym wystąpienie, które identyfikatory najpierw są usuwane.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywanie i uruchamianie maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedna maszyna wirtualna w zestawie skalowania, użyj [az vmss stop](/cli/azure/vmss#az-vmss-stop). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. Aby zatrzymać wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić spacjami.

W poniższym przykładzie zostaje zatrzymane wystąpienie *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zatrzymanych maszyn wirtualnych pozostają przydzielonego i będą nadal naliczane opłaty za zasoby obliczeniowe. Jeśli zamiast tego chcesz, aby maszyny wirtualne, należy cofnąć i tylko pociągnąć za sobą opłaty za magazyn, użyj [az vmss deallocate](/cli/azure/vmss). Można cofnąć alokacji wielu maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić spacjami. Poniższy przykład przedstawia zatrzymanie i cofnięcie przydziału wystąpienia *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Uruchom maszyny wirtualne w zestawie skalowania
Aby uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [az vmss start](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. Aby uruchomić wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić spacjami.

Poniższy przykład uruchamia wystąpienie *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchamianie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [ponowne uruchomienie az vmss](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. Aby ponownie uruchomić wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić spacjami.

Poniższy przykład powoduje ponowne uruchomienie wystąpienia *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Usuwanie maszyn wirtualnych w zestawie skalowania
Aby usunąć co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [az vmss delete-instances](/cli/azure/vmss). `--instance-ids` Parametr umożliwia określenie co najmniej jedną maszynę do usunięcia. Jeśli określisz * dla wystąpienia Identyfikatora, wszystkie maszyny wirtualne w zestawie skalowania są usuwane. Aby usunąć wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić spacjami.

Poniższy przykład usuwa wystąpienie *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Kolejne kroki
Inne typowe zadania dotyczące zestawów skalowania obejmują jak [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md), i [uaktualnić wystąpienia maszyny Wirtualnej](virtual-machine-scale-sets-upgrade-scale-set.md). Możesz również użyć wiersza polecenia platformy Azure, aby [Konfigurowanie reguł automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md).

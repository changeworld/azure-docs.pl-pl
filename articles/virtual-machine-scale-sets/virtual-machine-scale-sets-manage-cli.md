---
title: Zarządzanie zestawy skalowania maszyny wirtualnej z platformy Azure CLI 2.0 | Dokumentacja firmy Microsoft
description: Pojemność zestawu Typowe polecenia 2.0 interfejsu wiersza polecenia platformy Azure do zarządzania zestawy skalowania maszyny wirtualnej, takie jak jak uruchamianie i zatrzymywanie wystąpienie lub zmień skali.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 1afb43b65203406a7d49b0e3f641bc22d164a4a9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Zarządzaj skalowania maszyny wirtualnej, ustaw 2.0 interfejsu wiersza polecenia platformy Azure
W całym cyklu życia zestawu skali maszyny wirtualnej może być konieczne uruchomienie jednego lub więcej zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Ten artykuł zawiera szczegóły dotyczące niektórych typowych poleceń Azure CLI 2.0, które umożliwiają wykonywanie tych zadań.

Aby wykonać te zadania zarządzania, należy najnowsze 2.0 interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje, zobacz [zainstalować 2.0 interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli). Jeśli musisz utworzyć zestaw skali maszyny wirtualnej, możesz [tworzenia skali ustawić 2.0 interfejsu wiersza polecenia Azure](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Wyświetl informacje o zestawie skali
Aby wyświetlić ogólne informacje o zestawie skali, użyj [Pokaż vmss az](/cli/azure/vmss#az_vmss_show). Poniższy przykład pobiera informacje o zestaw o nazwie skalowania *myScaleSet* w *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpienia maszyny Wirtualnej w zestawie skalowania, użyj [wystąpienia listy az vmss](/cli/azure/vmss#list-instances). Poniższy przykład listy wszystkich wystąpień maszyn wirtualnych w zestaw o nazwie skalowania *myScaleSet* w *myResourceGroup* grupy zasobów. Należy podać własne wartości dla następujących nazw:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny Wirtualnej, należy dodać `--instance-id` parametr [az vmss get-— widok wystąpienia](/cli/azure/vmss#get-instance-view) i określ wystąpienie do wyświetlenia. Poniższy przykład wyświetla informacje o wystąpieniu maszyny Wirtualnej *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Lista informacji o połączeniu dla maszyn wirtualnych
Podłączanie do maszyn wirtualnych w zestawie skalowania, SSH lub RDP do przypisanej publiczny adres IP i port numer. Domyślnie zasady translatora adresów sieciowych (NAT) są dodawane do usługi równoważenia obciążenia Azure, który przesyła dalej ruch połączenia zdalnego na każdej maszynie Wirtualnej. Aby wyświetlić listę adresów i porty do łączenia z wystąpieniami maszyny Wirtualnej w zestawie skalowania, użyj [az vmss listy--połączenia — informacje o wystąpieniu](/cli/azure/vmss#list-instance-connection-info). Następujący przykład listy informacje o połączeniu dla maszyny Wirtualnej wystąpień w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Należy podać własne wartości dla następujących nazw:

```azurecli
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmień pojemność zestawu skalowania
Powyższych poleceń pokazano informacji o zestawie skali i wystąpień maszyny Wirtualnej. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Zestaw skali tworzy lub usuwa wymaganej liczby maszyn wirtualnych, a następnie konfiguruje maszyn wirtualnych na odbieranie ruchu w ramach aplikacji.

Aby wyświetlić liczbę bieżących wystąpień w zestawie skalowania, użyj polecenia [az vmss show](/cli/azure/vmss#az_vmss_show) i zapytania *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Następnie możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania za pomocą polecenia [az vmss scale](/cli/azure/vmss#az_vmss_scale). Poniższy przykład ustawia liczbę maszyn wirtualnych w skali, z ustawioną *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Jeśli ustawiona przyjmuje kilka minut, aby zaktualizować pojemności na skalę. Jeśli pojemność skali zestaw, maszyn wirtualnych o najwyższym wystąpienia, które identyfikatory są najpierw usunąć.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywania i uruchamiania maszyn wirtualnych w zestawie skalowania
Aby zatrzymać przynajmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [az vmss stop](/cli/azure/vmss/stop). `--instance-ids` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej, aby zatrzymać. Jeśli nie określisz Identyfikatora wystąpienia, wszystkich maszyn wirtualnych w zestawie skalowania zostały zatrzymane. Aby zatrzymać wiele maszyn wirtualnych, oddziel każdy identyfikator wystąpienia spacjami.

W następującym przykładzie zatrzymano wystąpienia *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zatrzymano maszyn wirtualnych pozostają przydzielone i nadal naliczenie opłat za obliczenia. Jeśli zamiast tego chcesz maszyn wirtualnych do cofnięcia alokacji i tylko naliczenie opłat za magazyn, użyj [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). Aby cofnąć wiele maszyn wirtualnych, oddziel każdy identyfikator wystąpienia spację. W poniższym przykładzie zatrzymuje i zwalnia wystąpienie *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Uruchom w zestawie skalowania maszyn wirtualnych
Aby uruchomić co najmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [az vmss start](/cli/azure/vmss#az_vmss_start). `--instance-ids` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej, aby rozpocząć. Jeśli nie określisz Identyfikatora wystąpienia, są uruchamiane wszystkie maszyny wirtualne w zestawie skalowania. Można uruchomić wiele maszyn wirtualnych, oddziel każdy identyfikator wystąpienia spacją.

Poniższy przykład uruchamia wystąpienie *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchomienie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [az vmss ponownego uruchomienia](/cli/azure/vmss#az_vmss_restart). `--instance-ids` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej, aby ponownie uruchomić. Jeśli nie określisz Identyfikatora wystąpienia, ponownego uruchomienia wszystkich maszyn wirtualnych w zestawie skalowania. Aby ponownie uruchomić wiele maszyn wirtualnych, oddziel każdy identyfikator wystąpienia spacją.

Poniższy przykład ponowne uruchomienie wystąpienia *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Usuń z zestawu skalowania maszyn wirtualnych
Aby usunąć przynajmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [vmss az delete wystąpienia](/cli/azure/vmss#delete-instances). `--instance-ids` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej do usunięcia. Jeśli określisz * dla tego wystąpienia Identyfikatora oraz wszystkich maszyn wirtualnych w zestawie skalowania są usuwane. Aby usunąć wiele maszyn wirtualnych, oddziel każdy identyfikator wystąpienia spacją.

Poniższy przykład umożliwia usunięcie wystąpienia *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Kolejne kroki
Obejmują innych typowych zadań dla zestawów skalowania jak [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md), i [uaktualnienia wystąpień maszyn wirtualnych](virtual-machine-scale-sets-upgrade-scale-set.md). Można również użyć wiersza polecenia platformy Azure do [Konfigurowanie reguł automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md).

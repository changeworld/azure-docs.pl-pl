---
title: Znajdowanie i usuwanie niedołączonych kart sieciowych platformy Azure
description: Jak znaleźć i usunąć karty sieciowe platformy Azure, które nie są dołączone do maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 3d7a0e3405dd87d17c9d7aac2e989ab82531d9b0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036132"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak znaleźć i usunąć niedołączone karty interfejsu sieciowego (nic) dla maszyn wirtualnych platformy Azure
Po usunięciu maszyny wirtualnej (VM) na platformie Azure domyślnie karty interfejsu sieciowego (nic) nie są usuwane. Jeśli utworzysz i usuniesz wiele maszyn wirtualnych, nieużywane karty sieciowe nadal będą używać wewnętrznych dzierżaw adresów IP. Podczas tworzenia innych kart sieciowych maszyn wirtualnych mogą nie być w stanie uzyskać dzierżawy IP w przestrzeni adresowej podsieci. W tym artykule pokazano, jak znaleźć i usunąć niedołączone karty sieciowe.

## <a name="find-and-delete-unattached-nics"></a>Znajdowanie i usuwanie niedołączonych kart sieciowych

Właściwość *virtualMachine* karty sieciowej przechowuje identyfikator i grupę zasobów maszyny wirtualnej, do której jest dołączona karta sieciowa. Poniższy skrypt pętli przez wszystkie karty sieciowe w subskrypcji i sprawdza, czy właściwość *virtualMachine* ma wartość null. Jeśli ta właściwość ma wartość null, karta sieciowa nie jest podłączona do maszyny wirtualnej.

Aby wyświetlić wszystkie niedołączone karty sieciowe, zdecydowanie zalecamy, aby najpierw uruchomić skrypt ze zmienną *deleteUnattachedNics* na *0*. Aby usunąć wszystkie niedołączone karty sieciowe po przejrzeniu danych wyjściowych na liście, uruchom skrypt z *deleteUnattachedNics* do *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych na platformie Azure i zarządzania nimi, zobacz [Tworzenie sieci VMNetwork i zarządzanie nimi](tutorial-virtual-network.md).

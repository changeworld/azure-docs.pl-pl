---
title: Znajdowanie i usuwanie niedołączonych kart sieciowych platformy Azure | Dokumentacja firmy Microsoft
description: Znajdowanie i usuwanie kart sieciowych platformy Azure, które nie są dołączone do maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d3fd807dcd920a951dcc5083022d4d264b5bdab7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60649404"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak wykryć i usunąć interfejsu sieciowego niedołączonych kart (NIC) maszyn wirtualnych platformy Azure
Jeśli usuniesz maszynę wirtualną (VM) na platformie Azure, kart interfejsu sieciowego (NIC) nie są usuwane domyślnie. Jeśli utworzysz i usuniesz wielu maszyn wirtualnych, nieużywanych kart sieciowych w dalszym ciągu używać wewnętrznego dzierżawy adresów IP. Jak utworzyć inne karty sieciowe maszyny Wirtualnej, może być nie można uzyskać dzierżawy adresów IP w przestrzeni adresowej podsieci. Ten artykuł pokazuje, jak Znajdowanie i usuwanie niedołączonych kart sieciowych.

## <a name="find-and-delete-unattached-nics"></a>Znajdowanie i usuwanie niedołączonych kart sieciowych

*VirtualMachine* właściwość dla karty Sieciowej zawiera identyfikator i grupę zasobów maszyny wirtualnej karty Sieciowej jest dołączony do. Poniższy skrypt w pętli wszystkich kart sieciowych w ramach subskrypcji i sprawdza, czy *virtualMachine* właściwość ma wartość null. Jeśli ta właściwość ma wartość null, karta sieciowa nie jest dołączony do maszyny Wirtualnej.

Aby wyświetlić wszystkie niedołączonych kart sieciowych, jego ma zdecydowanie zaleca się do pierwszego uruchomienia skryptu *deleteUnattachedNics* zmienną *0*. Aby usunąć wszystkie niedołączonych kart sieciowych, po przejrzeniu danych wyjściowych listy, uruchom skrypt przy użyciu *deleteUnattachedNics* do *1*.

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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat sposobu tworzenia i zarządzanie sieciami wirtualnymi na platformie Azure, zobacz [tworzenie i zarządzanie sieciami maszyn wirtualnych](tutorial-virtual-network.md).

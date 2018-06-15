---
title: Znajdowanie i usuwanie niedołączonej Azure kart sieciowych | Dokumentacja firmy Microsoft
description: Jak znaleźć i usunąć Azure kart sieciowych, które nie są dołączone do maszyn wirtualnych Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: c730866fe73305a37b37038699a7f729085a16aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31427143"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak znaleźć i usunąć interfejsu sieciowego niedołączonej karty (NIC) dla maszyn wirtualnych platformy Azure
Po usunięciu maszyny wirtualnej (VM) na platformie Azure kart interfejsu sieciowego (NIC) nie są usuwane domyślnie. Po utworzeniu i usunąć wielu maszyn wirtualnych, nieużywane karty sieciowe w dalszym ciągu używać wewnętrznego dzierżaw adresów IP. Jak utworzyć inne karty sieciowe maszyny Wirtualnej, może być nie można uzyskać dzierżawę adresu IP w przestrzeni adresowej podsieci. W tym artykule przedstawiono sposób Znajdowanie i usuwanie niedołączonej kart sieciowych.

## <a name="find-and-delete-unattached-nics"></a>Znajdowanie i usuwanie niedołączonej kart sieciowych

*VirtualMachine* właściwości do karty Sieciowej przechowuje identyfikator i zasobów grupy karty Sieciowej jest dołączony do maszyny wirtualnej. Poniższy skrypt wszystkie karty sieciowe w ramach subskrypcji w pętli i sprawdza, czy *virtualMachine* właściwość ma wartość null. Jeśli ta właściwość ma wartość null, karta sieciowa nie jest dołączony do maszyny Wirtualnej.

Aby wyświetlić wszystkie karty sieciowe odłączyć, jego ma zdecydowanie zaleca się do pierwszego uruchomienia skryptu *deleteUnattachedNics* zmienną *0*. Aby usunąć wszystkie karty sieciowe niedołączonej po przejrzeniu wyjście listy, uruchom skrypt z *deleteUnattachedNics* do *1*.

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

Aby uzyskać więcej informacji na temat sposobu tworzenia i zarządzania nimi sieci wirtualnych na platformie Azure, zobacz [tworzenie i zarządzanie nimi sieci maszyn wirtualnych](tutorial-virtual-network.md).

---
title: Znajdowanie i usuwanie nieprzywiązanych kart sieciowych platformy Azure
description: Jak znaleźć i usunąć karty sieciowe platformy Azure, które nie są dołączone do maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 8142b95ee666e205a8328eafd5930f1f386e49af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945140"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak znaleźć i usunąć niezałączone karty interfejsu sieciowego (NIC) dla maszyn wirtualnych platformy Azure
Po usunięciu maszyny wirtualnej (VM) na platformie Azure karty interfejsu sieciowego (NIC) nie są domyślnie usuwane. Jeśli utworzysz i usuniesz wiele maszyn wirtualnych, nieużywane karty sieciowe nadal będą korzystać z wewnętrznych dzierżaw adresów IP. Podczas tworzenia innych kart sieci wirtualnych karty sieciowe mogą nie być w stanie uzyskać dzierżawy IP w przestrzeni adresowej podsieci. W tym artykule pokazano, jak znaleźć i usunąć nieprzywiązane karty sieciowe.

## <a name="find-and-delete-unattached-nics"></a>Znajdowanie i usuwanie niedołączonych kart sieciowych

Właściwość *virtualMachine* dla karty sieciowej przechowuje grupę identyfikatorów i zasobów maszyny Wirtualnej, do na które jest dołączona karta wirtualna. Następujący skrypt pętli za pośrednictwem wszystkich kart sieciowych w subskrypcji i sprawdza, czy *właściwość virtualMachine* jest null. Jeśli ta właściwość ma wartość null, karta sieciowa nie jest dołączona do maszyny Wirtualnej.

Aby wyświetlić wszystkie nieprzyłączone karty sieciowe, zdecydowanie zaleca się najpierw uruchomić skrypt ze zmienną *deleteUnattachedNics* do *0*. Aby usunąć wszystkie niezałączone karty sieciowe po przejrzeniu danych wyjściowych listy, uruchom skrypt z *deleteUnattachedNics* do *1*.

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

Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych na platformie Azure i zarządzania nimi, zobacz [tworzenie sieci maszyn wirtualnych i zarządzanie nimi](tutorial-virtual-network.md).

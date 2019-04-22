---
title: Nawiązywanie połączenia usługi Azure Data Lake Storage Gen1 z sieciami wirtualnymi | Dokumentacja firmy Microsoft
description: Nawiązywanie połączenia usługi Azure Data Lake Storage Gen1 z sieci wirtualnych platformy Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878064"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Dostęp do usługi Azure Data Lake Storage Gen1 z maszyn wirtualnych w ramach sieci Wirtualnej platformy Azure
Azure Data Lake Storage Gen1 to usługa PaaS, która działa na publiczne adresy IP. Również punktami końcowymi usługi Azure Data Lake Storage Gen1 zazwyczaj nawiązać dowolnego serwera, które można podłączyć do publicznej sieci Internet. Domyślnie wszystkie maszyny wirtualne, które znajdują się w sieci wirtualnych platformy Azure mogą uzyskiwać dostęp do Internetu i dlatego mogą uzyskiwać dostęp do usługi Azure Data Lake Storage Gen1. Istnieje możliwość skonfigurowania maszyn wirtualnych w sieci Wirtualnej nie ma dostępu do Internetu. Dla takich maszyn wirtualnych dostęp do usługi Azure Data Lake Storage Gen1 jest ograniczony także. Blokowanie publicznego dostępu do Internetu dla maszyn wirtualnych w sieciach wirtualnych platformy Azure może odbywać się przy użyciu dowolnej z następujących metod:

* Konfigurując grupy zabezpieczeń sieci (NSG)
* Konfigurując zdefiniowaną przez użytkownika trasy (UDR)
* Przez wymianę tras za pośrednictwem protokołu BGP (branży standardowego protokołu routingu dynamicznego), gdy używane są połączenia ExpressRoute, tego bloku dostęp do Internetu

W tym artykule dowiesz się, jak zapewnić dostęp do usługi Azure Data Lake Storage Gen1 z maszyn wirtualnych platformy Azure, które zostały ograniczony dostęp do zasobów przy użyciu jednej z trzech metod wymienionych powyżej.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Włączanie łączność z usługi Azure Data Lake Storage Gen1 z maszyn wirtualnych z połączeniem z ograniczeniami
Dostęp do usługi Azure Data Lake Storage Gen1 z takich maszyn wirtualnych, należy skonfigurować je uzyskania dostępu do adresu IP dla regionu, w których konto usługi Azure Data Lake Storage Gen1 jest dostępna. Można zidentyfikować, adresy IP dla regionów konta usługi Data Lake Storage Gen1 rozpoznawania nazw DNS kont (`<account>.azuredatalakestore.net`). Rozpoznawanie nazw DNS kont, można użyć narzędzi takich jak **nslookup**. Otwórz wiersz polecenia na komputerze, a następnie uruchom następujące polecenie:

    nslookup mydatastore.azuredatalakestore.net

Dane wyjściowe podobne do następującego. Wartość względem **adres** właściwość jest adres IP skojarzony z kontem usługi Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Włączanie połączenia z poziomu maszyn wirtualnych można ograniczyć za pomocą sieciowej grupy zabezpieczeń
Gdy regułę sieciowej grupy zabezpieczeń jest używany do blokowania dostępu do Internetu, można utworzyć inną sieciowej grupy zabezpieczeń, które zezwalają na dostęp do adresu IP Data Lake magazynu Gen1. Aby uzyskać więcej informacji na temat reguł sieciowej grupy zabezpieczeń, zobacz [omówienie sieciowych grup zabezpieczeń](../virtual-network/security-overview.md). Aby uzyskać instrukcje dotyczące sposobu tworzenia sieciowych grup zabezpieczeń, zobacz [tworzenie sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Włączanie połączenia z maszynami wirtualnymi z ograniczeniami przy użyciu trasy zdefiniowanej przez użytkownika lub usługi ExpressRoute
Stosowania tras, tras zdefiniowanych przez użytkownika lub wymieniane BGP trasy, aby zablokować dostęp do Internetu specjalnych tras należy skonfigurować tak, aby maszyny wirtualne w tych podsieci mogą uzyskiwać dostęp do punktów końcowych Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [trasy zdefiniowane przez użytkownika omówienie](../virtual-network/virtual-networks-udr-overview.md). Aby uzyskać instrukcje na temat tworzenia tras zdefiniowanych przez użytkownika, zobacz [tworzenie tras zdefiniowanych przez użytkownika w usłudze Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Włączanie połączenia z poziomu maszyn wirtualnych można ograniczyć za pomocą usługi ExpressRoute
W przypadku skonfigurowania obwodu usługi ExpressRoute, serwerów lokalnych można uzyskać dostęp do Data Lake Storage Gen1 za pośrednictwem publicznej komunikacji równorzędnej. Więcej informacji na temat konfigurowania usługi ExpressRoute dla publicznej komunikacji równorzędnej znajduje się w temacie [ExpressRoute — często zadawane pytania](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake magazynu Gen1](data-lake-store-security-overview.md)


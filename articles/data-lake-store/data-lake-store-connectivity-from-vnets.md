---
title: Łączenie się z usługą Azure Data Lake Storage Gen1 z sieci wirtualnych | Dokumenty firmy Microsoft
description: Łączenie się z usługą Azure Data Lake Storage Gen1 z sieci wirtualnych platformy Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878872"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Uzyskiwanie dostępu do usługi Azure Data Lake Storage Gen1 z maszyn wirtualnych w sieci wirtualnej platformy Azure
Usługa Azure Data Lake Storage Gen1 to usługa PaaS, która działa na publicznych adresach IP internetu. Każdy serwer, który może łączyć się z publicznym Internetem, zazwyczaj może również łączyć się z punktami końcowymi usługi Azure Data Lake Storage Gen1. Domyślnie wszystkie maszyny wirtualne, które znajdują się w platformie Azure VNETs mogą uzyskać dostęp do Internetu, a tym samym można uzyskać dostęp do usługi Azure Data Lake Storage Gen1. Jednak istnieje możliwość skonfigurowania maszyn wirtualnych w sieci wirtualnej, aby nie miały dostępu do Internetu. W przypadku takich maszyn wirtualnych dostęp do usługi Azure Data Lake Storage Gen1 jest również ograniczony. Blokowanie publicznego dostępu do Internetu dla maszyn wirtualnych w sieciach wirtualnych platformy Azure można wykonać przy użyciu dowolnej z następujących metod:

* Konfigurując sieciowe grupy zabezpieczeń (NSG)
* Konfigurując trasy zdefiniowane przez użytkownika (UDR)
* Poprzez wymianę tras za pośrednictwem protokołu BGP (standardowego protokołu routingu dynamicznego), gdy używany jest protokół ExpressRoute, ten blokuje dostęp do Internetu

W tym artykule dowiesz się, jak włączyć dostęp do usługi Azure Data Lake Storage Gen1 z maszyn wirtualnych platformy Azure, które zostały ograniczone do dostępu do zasobów przy użyciu jednej z trzech metod wymienionych wcześniej.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Włączanie łączności z usługą Azure Data Lake Storage Gen1 z maszyn wirtualnych z ograniczoną łącznością
Aby uzyskać dostęp do usługi Azure Data Lake Storage Gen1 z takich maszyn wirtualnych, należy skonfigurować je, aby uzyskać dostęp do adresu IP dla regionu, w którym jest dostępne konto usługi Azure Data Lake Storage Gen1. Adresy IP regionów kont Usługi Data Lake Storage Gen1 można zidentyfikować, rozpoznając nazwy DNS kont (`<account>.azuredatalakestore.net`). Aby rozpoznać nazwy dns kont, można użyć narzędzi, takich jak **nslookup**. Otwórz wiersz polecenia na komputerze i uruchom następujące polecenie:

    nslookup mydatastore.azuredatalakestore.net

Dane wyjściowe przypominają następujące dane wyjściowe. Wartość względem **właściwości Address** to adres IP skojarzony z kontem Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Włączanie łączności z maszynami wirtualnymi ograniczonymi przy użyciu sieciowej grupy sieciowej
Gdy reguła sieciowej sieciowej sieciowej jest używana do blokowania dostępu do Internetu, można utworzyć inną grupę sieciową sieciowej, która umożliwia dostęp do adresu IP usługi Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat reguł sieciowej grupy zabezpieczeń, zobacz [Omówienie grup zabezpieczeń sieci](../virtual-network/security-overview.md). Aby uzyskać instrukcje dotyczące tworzenia sieciowych grup zabezpieczeń, zobacz [Jak utworzyć grupę zabezpieczeń sieci .](../virtual-network/tutorial-filter-network-traffic.md)

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Włączanie łączności z maszynami wirtualnymi ograniczonymi przy użyciu narzędzia UDR lub usługi ExpressRoute
Gdy trasy, udrr lub trasy wymieniane przez usługę BGP, są używane do blokowania dostępu do Internetu, należy skonfigurować specjalną trasę, aby maszyny wirtualne w takich podsieciach mogły uzyskiwać dostęp do punktów końcowych magazynu danych Lake Gen1. Aby uzyskać więcej informacji, zobacz [Omówienie tras zdefiniowanych przez użytkownika](../virtual-network/virtual-networks-udr-overview.md). Aby uzyskać instrukcje dotyczące tworzenia UDR, zobacz [Tworzenie UDR w Menedżerze zasobów](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Włączanie łączności z maszynami wirtualnymi ograniczonymi przy użyciu usługi ExpressRoute
Po skonfigurowaniu obwodu usługi ExpressRoute serwery lokalne mogą uzyskiwać dostęp do magazynu usługi Data Lake Storage Gen1 za pośrednictwem komunikacji równorzędnej publicznej. Więcej informacji na temat konfigurowania usługi ExpressRoute dla komunikacji równorzędnej dostępnej w [temacie Często zadawane pytania dotyczące usługi ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Securing data stored in Azure Data Lake Storage Gen1 (Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1)](data-lake-store-security-overview.md)


---
title: Nawiązywanie połączenia usługi Azure Data Lake Store z sieciami wirtualnymi | Dokumentacja firmy Microsoft
description: Nawiązywanie połączenia usługi Azure Data Lake Store z sieci wirtualnych Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: 4086ef6ce2a95e0467eda61116ac002cf53610b5
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300808"
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Dostęp do usługi Azure Data Lake Store z maszyn wirtualnych w ramach sieci Wirtualnej platformy Azure
Azure Data Lake Store jest usługą PaaS, która działa na publiczne adresy Internet IP. Dowolnego serwera, który może nawiązać połączenia z publicznego Internetu zwykle mogą łączyć się również punktów końcowych usługi Azure Data Lake Store. Domyślnie wszystkie maszyny wirtualne, które znajdują się w sieci wirtualnych platformy Azure mogą uzyskiwać dostęp do Internetu i dlatego mogą uzyskiwać dostęp do usługi Azure Data Lake Store. Istnieje możliwość skonfigurowania maszyn wirtualnych w sieci Wirtualnej nie ma dostępu do Internetu. Dla tych maszyn wirtualnych dostęp do usługi Azure Data Lake Store jest ograniczony również. Blokowanie publiczny dostęp do Internetu dla maszyn wirtualnych w sieci wirtualnych platformy Azure może odbywać się przy użyciu dowolnej z następujących metod:

* Konfigurując grupy zabezpieczeń sieci (NSG)
* Konfigurując użytkownika zdefiniowanych tras przez
* Przez wymianę tras za pomocą protokołu BGP (standardowego dynamiczny routing protokołu), gdy używane są połączenia ExpressRoute, że blokowanie dostępu do Internetu

W tym artykule dowiesz się, jak włączyć dostęp do usługi Azure Data Lake Store z maszyn wirtualnych platformy Azure, który został ograniczony dostęp do zasobów przy użyciu jednej z trzech metod wymienionych powyżej.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Włączanie łączności do usługi Azure Data Lake Store z maszyn wirtualnych z ograniczeniami łączności
Aby uzyskać dostęp do usługi Azure Data Lake Store z tych maszyn wirtualnych, należy skonfigurować je, aby uzyskać dostępu do adresu IP, w którym konta usługi Azure Data Lake Store jest dostępny. Możesz określić adresy IP dla kont usługi Data Lake Store za rozpoznawanie nazw DNS kont (`<account>.azuredatalakestore.net`). Do rozpoznawania nazw DNS kont, można użyć narzędzia takie jak **nslookup**. Otwórz wiersz polecenia na komputerze i uruchom następujące polecenie:

    nslookup mydatastore.azuredatalakestore.net

Dane wyjściowe podobne do następującego. Wartość względem **adres** właściwości to adres IP skojarzony z Twoim kontem usługi Data Lake Store.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Włączenie łączności z ograniczeniami przy użyciu NSG maszyn wirtualnych
Gdy reguły NSG jest używany do blokowania dostępu do Internetu, można utworzyć innej NSG, która zezwala na dostęp do adresu IP Data Lake magazynu. Aby uzyskać więcej informacji na temat reguł NSG, zobacz [Przegląd grup zabezpieczeń sieci](../virtual-network/security-overview.md). Aby uzyskać instrukcje na temat tworzenia grup NSG, zobacz [jak utworzyć grupę zabezpieczeń sieci](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Włączenie łączności z ograniczeniami przy użyciu przez lub ExpressRoute maszyn wirtualnych
W przypadku tras, Udr lub wymieniane BGP trasy są używane do blokowania dostępu do Internetu, specjalne trasy musi być skonfigurowana tak, aby maszyn wirtualnych w podsieciach takie mogą uzyskiwać dostęp do punktów końcowych usługi Data Lake Store. Aby uzyskać więcej informacji, zobacz [trasy zdefiniowane przez użytkownika — omówienie](../virtual-network/virtual-networks-udr-overview.md). Aby uzyskać instrukcje dotyczące tworzenia Udr, zobacz [utworzyć Udr w Menedżerze zasobów](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Włączenie łączności z ograniczeniami przy użyciu usługi ExpressRoute maszyn wirtualnych
Po skonfigurowaniu obwodu usługi ExpressRoute, serwerów lokalnych można uzyskać dostępu do usługi Data Lake Store za pośrednictwem publicznej komunikacji równorzędnej. Więcej informacji na temat konfigurowania usługi ExpressRoute dla publicznej komunikacji równorzędnej jest dostępne pod adresem [ExpressRoute — często zadawane pytania](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
* [Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Store](data-lake-store-security-overview.md)


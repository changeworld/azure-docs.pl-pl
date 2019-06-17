---
title: Określanie ustawień DNS w pliku konfiguracji sieci wirtualnej | Dokumentacja firmy Microsoft
description: Jak zmienić ustawienia serwera DNS w sieci wirtualnej przy użyciu pliku konfiguracji sieci wirtualnej w klasycznym modelu wdrażania
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60232858"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Określanie ustawień DNS w pliku konfiguracji sieci wirtualnej
Plik konfiguracji sieci ma dwa elementy, które służy do określania ustawień systemu nazw domen (DNS, Domain Name System): **DnsServers** i **DnsServerRef**. Możesz dodać listę serwerów DNS, określając ich adresy IP i odwoływać się do nazwy **DnsServers** elementu. Następnie można użyć **DnsServerRef** elementu, aby określić, które wpisami serwera DNS z elementu DnsServers służą do witryny innej sieci w ramach sieci wirtualnej.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania.

Plik konfiguracji sieci może zawierać następujące elementy. Tytuł każdy element jest połączony do strony, która zapewnia dodatkowe informacje na temat ustawienia wartości elementu.

> [!IMPORTANT]
> Aby uzyskać informacje o sposobie konfigurowania pliku konfiguracji sieci, zobacz [Konfigurowanie wirtualnych sieci przy użyciu pliku konfiguracyjnego sieci](virtual-networks-using-network-configuration-file.md). Aby uzyskać informacji na temat każdego z elementów znajdujących się w pliku konfiguracji sieci, zobacz [schemat konfiguracji sieci wirtualnej platformy Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS Element](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **Nazwa** atrybutu w **serwer DNS** element jest używany tylko jako odniesienie do **DnsServerRef** elementu. Go nie reprezentuje nazwę hosta dla serwera DNS. Każdy **serwer DNS** wartość atrybutu musi być unikatowa dla całej subskrypcji Microsoft Azure
> 
> 

[Element Lokacje sieci wirtualnej](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Aby można było określić, to ustawienie dla elementu wirtualnego Lokacje sieciowe, jego musi być wcześniej zdefiniowany w elemencie DNS. DnsServerRef *nazwa* w lokacji sieci wirtualnej element musi odwoływać się do wartości nazw określona w elemencie DNS dla serwer DNS *nazwa*.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie [schemat konfiguracji sieci wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkId=248093).
* Zrozumienie [schematu konfiguracji usługi platformy Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Konfigurowanie sieci wirtualnej za pomocą plików konfiguracji sieci](virtual-networks-using-network-configuration-file.md).


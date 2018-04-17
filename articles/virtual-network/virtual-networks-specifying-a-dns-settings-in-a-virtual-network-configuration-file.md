---
title: Określanie ustawień DNS w pliku konfiguracji sieci wirtualnej | Dokumentacja firmy Microsoft
description: Jak zmienić ustawienia serwera DNS w sieci wirtualnej przy użyciu pliku konfiguracji sieci wirtualnej w klasycznym modelu wdrażania
services: virtual-network
documentationcenter: na
author: genli
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
ms.openlocfilehash: 8e6cfc285b3fb23944dfa0189bb492a9b8fa9a88
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Określanie ustawień DNS w pliku konfiguracji sieci wirtualnej
Plik konfiguracji sieci ma dwa elementy, których można użyć do określenia ustawień systemu nazw domen (DNS, Domain Name System): **DnsServers** i **DnsServerRef**. Można dodać listę serwerów DNS, określając ich adresy IP i odwoływać się do nazw do **DnsServers** elementu. Następnie można użyć **DnsServerRef** element, aby określić, które wpisów serwera DNS z elementu DnsServers służą do innej sieci Lokacje w ramach sieci wirtualnej.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania.

Plik konfiguracji sieci może zawierać następujące elementy. Tytuł każdy element jest połączony do strony, która zapewnia dodatkowe informacje na temat ustawienia wartości elementu.

> [!IMPORTANT]
> Aby uzyskać informacje o sposobie konfigurowania pliku konfiguracji sieci, zobacz [konfigurowania wirtualnego za pomocą sieci pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Aby uzyskać informacje o każdym elemencie zawarte w pliku konfiguracji sieci, zobacz [schemat konfiguracji sieci wirtualnych Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS Element](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **Nazwa** atrybutu w **serwer DNS** element jest używany tylko jako punkt odniesienia dla **DnsServerRef** elementu. Ten element nie reprezentuje nazwę hosta dla serwera DNS. Każdy **serwer DNS** wartość atrybutu musi być unikatowy w całej subskrypcji Microsoft Azure
> 
> 

[Element Lokacje sieci wirtualnej](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Aby określić to ustawienie dla elementu witryn sieci wirtualnych, jego musi wcześniej zostać zdefiniowany w elemencie DNS. DnsServerRef *nazwa* w witrynach sieci wirtualnych element musi odwoływać się do wartości nazw określonej w elemencie DNS dla serwer DNS *nazwa*.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie [schemat konfiguracji sieci wirtualnej platformy Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
* Zrozumienie [schemat konfiguracji usługi Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Skonfiguruj sieć wirtualną przy użyciu plików konfiguracji sieci](virtual-networks-using-network-configuration-file.md).


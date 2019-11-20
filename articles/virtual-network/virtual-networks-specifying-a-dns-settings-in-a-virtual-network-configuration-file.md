---
title: Określanie ustawień DNS w pliku konfiguracji sieci wirtualnej platformy Azure
description: Jak zmienić ustawienia serwera DNS w sieci wirtualnej przy użyciu pliku konfiguracji sieci wirtualnej w klasycznym modelu wdrażania
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: db8b1817bb14d293632d16fe02792dbb85766559
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196653"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Określanie ustawień DNS w pliku konfiguracji sieci wirtualnej
Plik konfiguracji sieci ma dwa elementy, których można użyć do określenia ustawień systemu nazw domen (DNS): **DnsServers** i **DnsServerRef**. Listę serwerów DNS można dodać, określając ich adresy IP i nazwy odwołań do elementu **DnsServers** . Następnie można użyć elementu **DnsServerRef** , aby określić, które wpisy serwera DNS z elementu DnsServers są używane dla różnych lokacji sieciowych w sieci wirtualnej.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania.

Plik konfiguracji sieci może zawierać następujące elementy. Tytuł każdego elementu jest połączony ze stroną, która zawiera dodatkowe informacje o ustawieniach wartości elementu.

> [!IMPORTANT]
> Informacje o sposobie konfigurowania pliku konfiguracji sieci znajdują się w temacie [konfigurowanie Virtual Network przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Aby uzyskać informacje na temat każdego elementu zawartego w pliku konfiguracji sieci, zobacz [Azure Virtual Network Configuration Schema](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Element DNS](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> Atrybut **name** w elemencie **serwer DNS** jest używany tylko jako odwołanie do elementu **DnsServerRef** . Nie reprezentuje nazwy hosta serwera DNS. Każda wartość atrybutu **serwer DNS** musi być unikatowa w całej subskrypcji Microsoft Azure
> 
> 

[Virtual Network element Sitess](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> W celu określenia tego ustawienia dla elementu Virtual Network sites, musi być wcześniej zdefiniowany w elemencie DNS. *Nazwa* DnsServerRef w elemencie "Virtual Network sites" musi odwoływać się do wartości nazwy określonej w elemencie DNS dla *nazwy*serwer DNS.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Poznaj [Schemat konfiguracji usługi Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093).
* Informacje o [schemacie konfiguracji usługi platformy Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Skonfiguruj sieć wirtualną przy użyciu plików konfiguracji sieci](virtual-networks-using-network-configuration-file.md).


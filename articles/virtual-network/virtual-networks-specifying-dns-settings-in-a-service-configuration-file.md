---
title: Określanie ustawień DNS w pliku konfiguracji usługi | Microsoft Docs
description: Określanie niestandardowych ustawień DNS przy użyciu pliku konfiguracji usługi dla sieci wirtualnej
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: f27802d76a8b94a0d5f1eb0c35fd55c93712e557
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059080"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Określanie ustawień DNS w pliku konfiguracji usługi
## <a name="dns-elements"></a>Elementy DNS
Plik konfiguracji usługi może zawierać element DnsServers z listą adresów IPv4 serwerów systemu nazw domen (DNS), które będą używane przez usługę. Ustawienia w pliku konfiguracji usługi mają pierwszeństwo przed ustawieniami w pliku konfiguracji sieci. Aby uzyskać więcej informacji, zobacz [Schemat konfiguracji usługi platformy Azure (plik cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration, element**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Atrybut **name** w elemencie **serwer DNS** jest używany tylko jako Nazwa odwołania. Nie reprezentuje nazwy hosta serwera DNS. Każda wartość atrybutu **serwer DNS** musi być unikatowa w całej subskrypcji Microsoft Azure.
> 
> 

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi platformy Azure (cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schemat konfiguracji usługi Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurowanie Virtual Network przy użyciu plików konfiguracji sieci](https://go.microsoft.com/fwlink/?LinkId=248094)

[Informacje o ustawieniach Virtual Network w portal zarządzania](https://go.microsoft.com/fwlink/?LinkId=248092)


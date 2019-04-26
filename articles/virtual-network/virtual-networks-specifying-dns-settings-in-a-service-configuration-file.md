---
title: Określanie ustawień DNS w pliku konfiguracji usługi | Dokumentacja firmy Microsoft
description: Określanie niestandardowych ustawień DNS dla sieci wirtualnej przy użyciu pliku konfiguracji usługi
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232740"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Określanie ustawień DNS w pliku konfiguracji usługi
## <a name="dns-elements"></a>Elementy DNS
Plik konfiguracji usługi może zawierać element DnsServers z użyciem listy adresów IPv4 dla serwerów systemu nazw domen (DNS, Domain Name System), które będzie używane przez usługę. Ustawienia w pliku konfiguracji usługi mają pierwszeństwo przed ustawieniami w pliku konfiguracji sieci. Aby uzyskać więcej informacji, zobacz [schematu konfiguracji usługi platformy Azure (cscfg pliku)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Element Konfiguracja sieci**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> **Nazwa** atrybutu w **serwer DNS** element jest używany tylko jako nazwy odwołania. Go nie reprezentuje nazwę hosta dla serwera DNS. Każdy **serwer DNS** wartość atrybutu musi być unikatowa dla całej subskrypcji Microsoft Azure.
> 
> 

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi platformy Azure (cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schemat konfiguracji sieci wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Konfigurowanie sieci wirtualnej za pomocą plików konfiguracji sieci](https://go.microsoft.com/fwlink/?LinkId=248094)

[Informacje o ustawieniach sieci wirtualnej w portalu zarządzania](https://go.microsoft.com/fwlink/?LinkId=248092)


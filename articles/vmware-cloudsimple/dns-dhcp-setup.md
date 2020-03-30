---
title: Rozwiązanie Azure VMware by CloudSimple — konfigurowanie obciążenia DNS i DHCP dla chmury prywatnej
description: W tym artykule opisano sposób konfigurowania systemu DNS i DHCP dla aplikacji i obciążeń działających w środowisku cloudsimple private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024691"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Konfigurowanie aplikacji i obciążeń DNS i DHCP w chmurze cloudsimple private cloud

Aplikacje i obciążenia uruchomione w środowisku chmury prywatnej wymagają rozpoznawania nazw i usług DHCP do odnośnia i przypisywania adresów IP.  Do świadczenia tych usług wymagana jest odpowiednia infrastruktura DHCP i DNS.  Maszynę wirtualną można skonfigurować w celu świadczenia tych usług w środowisku chmury prywatnej.  

## <a name="prerequisites"></a>Wymagania wstępne

* Rozproszona grupa portów ze skonfigurowaną siecią VLAN
* Kierowanie konfiguracji do lokalnych lub internetowych serwerów DNS
* Szablon maszyny wirtualnej lub iso do tworzenia maszyny wirtualnej

## <a name="linux-based-dns-server-setup"></a>Konfiguracja serwera DNS oparta na systemie Linux

Linux oferuje różne pakiety do konfigurowania serwerów DNS.  Oto [przykładowa konfiguracja firmy DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) z instrukcjami konfigurowania serwera DNS BIND typu open source.

## <a name="windows-based-setup"></a>Konfiguracja oparta na systemie Windows

W tych tematach firmy Microsoft opisano sposób konfigurowania serwera Windows jako serwera DNS i serwera DHCP.

* [Windows Server jako serwer DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server jako serwer DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

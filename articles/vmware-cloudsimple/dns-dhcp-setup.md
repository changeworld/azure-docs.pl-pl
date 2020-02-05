---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Konfigurowanie obciążeń DNS i DHCP na potrzeby automatycznej synchronizacji chmury prywatnej
description: Opisuje sposób konfigurowania usług DNS i DHCP dla aplikacji i obciążeń działających w środowisku chmury prywatnej do automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024691"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>Skonfiguruj aplikacje i obciążenia systemu DNS i DHCP w chmurze prywatnej automatycznej synchronizacji

Aplikacje i obciążenia działające w środowisku chmury prywatnej do automatycznej synchronizacji wymagają rozpoznawania nazw i usług DHCP do wyszukiwania i przypisywania adresów IP. Aby zapewnić te usługi, wymagana jest właściwa infrastruktura DHCP i DNS. Można skonfigurować maszynę wirtualną, aby udostępnić te usługi w środowisku chmury prywatnej automatycznej. 

## <a name="prerequisites"></a>Wymagania wstępne

* Grupa portów rozproszonych z konfiguracją sieci VLAN
* Kierowanie instalacji do lokalnych lub internetowych serwerów DNS
* Szablon maszyny wirtualnej lub plik ISO do utworzenia maszyny wirtualnej

## <a name="linux-based-dns-server-setup"></a>Konfiguracja serwera DNS z systemem Linux

System Linux oferuje różne pakiety do konfigurowania serwerów DNS. Oto [Przykładowa konfiguracja z DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) z instrukcjami dotyczącymi KONFIGUROWANIA serwera DNS bind "open source".

## <a name="windows-based-setup"></a>Konfiguracja oparta na systemie Windows

W tych tematach firmy Microsoft opisano sposób konfigurowania serwera z systemem Windows jako serwera DNS i serwera DHCP.

* [Serwer z systemem Windows jako serwer DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Serwer z systemem Windows jako serwer DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

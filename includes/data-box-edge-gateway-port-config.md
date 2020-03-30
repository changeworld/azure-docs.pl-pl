---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183671"
---
| Nr portu| Wył. | Zakres portu| Wymagany|   Uwagi |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Out|Sieć WAN |Nie|Port wychodzący służy do pobierania aktualizacji przez dostęp do Internetu. <br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika. |
| Protokół TCP 443 (HTTPS)|Out|Sieć WAN|Tak|Port wychodzący służy do uzyskiwania dostępu do danych w chmurze.<br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika.|
| UDP 123 (NTP)|Out|Sieć WAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany tylko w przypadku korzystania z internetowego serwera NTP.  |   
| UDP 53 (DNS)|Out|Sieć WAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany tylko w przypadku korzystania z internetowego serwera DNS.<br>Zalecamy korzystanie z lokalnego serwera DNS. |
| TCP 5985 (WinRM)|Wyjście/Wyjęcie|Sieć LAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany do łączenia się z urządzeniem za pośrednictwem zdalnego programu PowerShell za pośrednictwem protokołu HTTP.  |
| UDP 67 (DHCP)|Out|Sieć LAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany tylko w przypadku korzystania z lokalnego serwera DHCP.  |
| TCP 80 (HTTP)|Wyjście/Wyjęcie|Sieć LAN|Tak|Ten port jest portem przychodzącym dla lokalnego interfejsu użytkownika na urządzeniu do zarządzania lokalnego. <br>Dostęp do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP zostanie automatycznie przekierowany do protokołu HTTPS.  |
| Protokół TCP 443 (HTTPS)|Wyjście/Wyjęcie|Sieć LAN|Tak|Ten port jest portem przychodzącym dla lokalnego interfejsu użytkownika na urządzeniu do zarządzania lokalnego. |
| TCP 445 (SMB)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany tylko wtedy, gdy łączysz się za pośrednictwem protokołu SMB. |
| TCP 2049 (KNF)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany tylko wtedy, gdy łączysz się za pośrednictwem systemu plików NFS. |

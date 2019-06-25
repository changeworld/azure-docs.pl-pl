---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183671"
---
| Nr portu.| Wewnątrz lub na zewnątrz | Zakres portów| Wymagane|   Uwagi |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|limit|WAN |Nie|Wychodząca przez port służy do dostępu do Internetu do pobierania aktualizacji. <br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika. |
| TCP 443 (HTTPS)|limit|WAN|Yes|Wychodząca przez port jest używany do uzyskiwania dostępu do danych w chmurze.<br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika.|
| UDP 123 (NTP)|limit|WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy używasz internetowego serwera NTP.  |   
| UDP 53 (DNS)|limit|WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy używasz internetowego serwera DNS.<br>Firma Microsoft zaleca korzystanie z lokalnego serwera DNS. |
| TCP 5985 (WinRM)|Poza/w|LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany do nawiązania połączenia z urządzeniem przy użyciu zdalnego programu PowerShell za pośrednictwem protokołu HTTP.  |
| UDP 67 (DHCP)|limit|LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest używany lokalny serwer DHCP.  |
| TCP 80 (HTTP)|Poza/w|LAN|Tak|Ten port jest port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu na potrzeby zarządzania lokalnego. <br>Uzyskiwanie dostępu do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP automatycznie spowoduje przekierowanie do protokołu HTTPS.  |
| TCP 443 (HTTPS)|Poza/w|LAN|Tak|Ten port jest port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu na potrzeby zarządzania lokalnego. |
| TCP 445 (SMB)|W|LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest nawiązywane za pośrednictwem protokołu SMB. |
| TCP 2049 (NFS)|W|LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest nawiązywane za pośrednictwem systemu plików NFS. |

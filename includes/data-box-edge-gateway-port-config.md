---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58431397"
---
| Nr portu.| Wewnątrz lub na zewnątrz | Zakres portów| Wymagane|   Uwagi |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Wyjście|Sieć WAN |Nie|Wychodząca przez port służy do dostępu do Internetu do pobierania aktualizacji. <br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika. |
| TCP 443 (HTTPS)|Wyjście|Sieć WAN|Yes|Wychodząca przez port jest używany do uzyskiwania dostępu do danych w chmurze.<br>Serwer proxy ruchu wychodzącego w sieci web jest użytkownika.|
| UDP 123 (NTP)|Wyjście|Sieć WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy używasz internetowego serwera NTP.  |   
| UDP 53 (DNS)|Wyjście|Sieć WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy używasz internetowego serwera DNS.<br>Firma Microsoft zaleca korzystanie z lokalnego serwera DNS. |
| TCP 5985 (WinRM)|Poza/w|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany do nawiązania połączenia z urządzeniem przy użyciu zdalnego programu PowerShell za pośrednictwem protokołu HTTP.  |
| UDP 67 (DHCP)|Wyjście|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest używany lokalny serwer DHCP.  |
| TCP 80 (HTTP)|Poza/w|Sieć LAN|Yes|Ten port jest port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu na potrzeby zarządzania lokalnego. <br>Uzyskiwanie dostępu do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP automatycznie spowoduje przekierowanie do protokołu HTTPS.  |
| TCP 443 (HTTPS)|Poza/w|Sieć LAN|Yes|Ten port jest port wejściowy dla lokalnego interfejsu użytkownika na urządzeniu na potrzeby zarządzania lokalnego. |
| TCP 445 (SMB)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest nawiązywane za pośrednictwem protokołu SMB. |
| TCP 2049 (NFS)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest nawiązywane za pośrednictwem systemu plików NFS. |

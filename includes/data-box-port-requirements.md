---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839763"
---
| Nr portu| Wył. | Zakres portu| Wymagany| Uwagi |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|W|Sieć LAN|Tak|Ten port jest używany do łączenia się z interfejsami API REST magazynu bloga dla pól danych za pośrednictwem protokołu HTTP. Jeśli nie łączy się z interfejsami API REST, automatycznie przekierowuje do lokalnego interfejsu użytkownika sieci web przez 8443. |
| Protokół TCP 443 (HTTPS)|W|Sieć LAN|Tak|Ten port jest używany do łączenia się z interfejsami API REST magazynu bloga dla pól danych za pośrednictwem protokołu HTTPS. Jeśli nie łączy się z interfejsami API REST, automatycznie przekierowuje do lokalnego interfejsu użytkownika sieci web przez 8443. |
| TCP 8443 (HTTPS-Alt)|W|Sieć LAN|Tak|Jest to alternatywny port dla protokołu HTTPS i jest używany podczas łączenia się z lokalnym interfejsem użytkownika sieci Web do zarządzania urządzeniami. |
| TCP 445 (SMB)|Wyjście/Wyjęcie|Sieć LAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany tylko wtedy, gdy łączysz się za pośrednictwem protokołu SMB. |
| TCP 2049 (KNF)|Wyjście/Wyjęcie|Sieć LAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest wymagany tylko wtedy, gdy łączysz się za pośrednictwem systemu plików NFS. |
| TCP 111 (nfs)|Wyjście/Wyjęcie|Sieć LAN|W niektórych przypadkach<br>Zobacz notatki|Ten port jest używany do mapowania rpcbind/port i jest wymagany tylko wtedy, gdy łączysz się za pośrednictwem systemu plików NFS.  |

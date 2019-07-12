---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839763"
---
| Nr portu.| Wewnątrz lub na zewnątrz | Zakres portów| Wymagane| Uwagi |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|W|LAN|Yes|Port ten jest używany do połączenia z magazynem danych pole blogu interfejsów API REST za pośrednictwem protokołu HTTP. Jeśli nie jest połączony z interfejsów API REST, to automatycznie przekierowuje do lokalnego Interfejsu w przeglądarce ponad 8443. |
| TCP 443 (HTTPS)|W|LAN|Yes|Port ten jest używany do połączenia z magazynem danych pole blogu interfejsów API REST przy użyciu protokołu HTTPS. Jeśli nie jest połączony z interfejsów API REST, to automatycznie przekierowuje do lokalnego Interfejsu w przeglądarce ponad 8443. |
| TCP 8443 (HTTPS-Alt)|W|LAN|Tak|To jest alternatywny port dla protokołu HTTPS i jest używany podczas nawiązywania połączenia z lokalnego interfejsu użytkownika sieci web do zarządzania urządzeniami. |
| TCP 445 (SMB)|Poza/w|LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy łączysz się za pośrednictwem protokołu SMB. |
| TCP 2049 (NFS)|Poza/w|LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy łączysz się przy użyciu systemu plików NFS. |
| TCP 111 (NFS)|Poza/w|LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest używany dla mapowanie usługi rpcbind/port i wymagane tylko, jeśli łączysz się przy użyciu systemu plików NFS.  |

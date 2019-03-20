---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125156"
---
Właściwy certyfikat SSL gwarantuje, że wysyłasz zaszyfrowanych informacji do odpowiedniego serwera. Oprócz szyfrowania certyfikat umożliwia również uwierzytelnianie. Możesz przekazać własne zaufany certyfikat SSL za pośrednictwem interfejsu programu PowerShell urządzenia.

1. [Nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj `Set-HcsCertificate` polecenia cmdlet w celu przekazania certyfikatu. Po wyświetleniu monitu podaj następujące parametry:

   - `CertificateFilePath` — Ścieżka do udziału zawierającego plik certyfikatu w *PFX* formatu.
   - `CertificatePassword` — Hasło używane do ochrony certyfikatu.
   - `Credentials` — Nazwa użytkownika i hasło dostępu do udziału, który zawiera certyfikat.

     Poniższy przykład przedstawia sposób użycia tego polecenia cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```


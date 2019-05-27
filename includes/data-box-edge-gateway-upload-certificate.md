---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161179"
---
Właściwy certyfikat SSL gwarantuje, że wysyłasz zaszyfrowanych informacji do odpowiedniego serwera. Oprócz szyfrowania certyfikat umożliwia również uwierzytelnianie. Możesz przekazać własne zaufany certyfikat SSL za pośrednictwem interfejsu programu PowerShell urządzenia.

1. [Nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj `Set-HcsCertificate` polecenia cmdlet w celu przekazania certyfikatu. Po wyświetleniu monitu podaj następujące parametry:

   - `CertificateFilePath` — Ścieżka do udziału zawierającego plik certyfikatu w *PFX* formatu.
   - `CertificatePassword` — Hasło używane do ochrony certyfikatu.
   - `Credentials` — Nazwa użytkownika i hasło dostępu do udziału, który zawiera certyfikat.

     Poniższy przykład przedstawia sposób użycia tego polecenia cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```


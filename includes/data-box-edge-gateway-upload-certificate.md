---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c689dc4f7e0957218d6504532c4b481d7673def8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555145"
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


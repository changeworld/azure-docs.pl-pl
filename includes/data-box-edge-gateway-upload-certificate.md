---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448622"
---
Odpowiedni certyfikat SSL zapewnia wysyłanie zaszyfrowanych informacji do odpowiedniego serwera. Oprócz szyfrowania certyfikat umożliwia również uwierzytelnianie. Możesz przekazać własny zaufany certyfikat SSL za pośrednictwem interfejsu programu PowerShell urządzenia.

1. [Połącz się z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj `Set-HcsCertificate` polecenia cmdlet, aby przekazać certyfikat. Po wyświetleniu monitu podaj następujące parametry:

   - `CertificateFilePath`- Ścieżka do udziału, który zawiera plik certyfikatu w formacie *.pfx.*
   - `CertificatePassword`- Hasło używane do ochrony certyfikatu.
   - `Credentials`- Nazwa użytkownika, aby uzyskać dostęp do udziału, który zawiera certyfikat. Po wyświetleniu monitu podaj hasło do udziału sieciowego.

     W poniższym przykładzie przedstawiono użycie tego polecenia cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```


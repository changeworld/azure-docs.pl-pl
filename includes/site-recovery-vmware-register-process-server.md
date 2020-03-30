---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67534700"
---
1. Ustanawianie połączenia pulpitu zdalnego z komputerem z uruchomionym serwerem przetwarzania. 
2. Uruchom plik cspsconfigtool.exe, aby uruchomić narzędzie konfiguracji serwera odzyskiwania lokacji platformy Azure.
    - Narzędzie jest uruchamiane automatycznie przy pierwszym logowaniach na serwerze przetwarzania.
    - Jeśli nie otwiera się automatycznie, kliknij skrót na pulpicie.

3. W **obszarze Nazwa FQDN lub IP serwera konfiguracji**określ nazwę lub adres IP serwera konfiguracji, za pomocą za pomocą którego ma się zarejestrować serwer przetwarzania.
4. W **porcie serwera konfiguracji**upewnij się, że określono 443. Jest to port, na którym serwer konfiguracji nasłuchuje żądań.
5. W **polu Hasło połączenia**określ hasło określone podczas konfigurowania serwera konfiguracji. Aby znaleźć hasło:
    -  Na serwerze konfiguracji przejdź do folderu instalacyjnego Odzyskiwania witryny **\home\svssystems\bin\**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Uruchom poniższe polecenie, aby wydrukować bieżące hasło:
    ```
    genpassphrase.exe -n
    ```

6. W **porcie transferu danych**pozostaw wartość domyślną, chyba że określono port niestandardowy.

7. Kliknij **pozycję Zapisz** zapisz ustawienia i zarejestruj serwer przetwarzania.

    
    ![Rejestrowanie serwera przetwarzania](./media/site-recovery-vmware-register-process-server/register-ps.png)

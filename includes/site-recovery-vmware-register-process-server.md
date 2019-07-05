---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534700"
---
1. Ustanowienia połączenia pulpitu zdalnego do maszyny z systemem serwera przetwarzania. 
2. Uruchom cspsconfigtool.exe, aby uruchomić narzędzie konfiguracji serwera procesu odzyskiwania lokacji platformy Azure.
    - Narzędzie jest uruchamiane automatycznie podczas pierwszego logowania się do serwera przetwarzania.
    - Jeśli go nie jest otwierany automatycznie, kliknij jego skrót na pulpicie.

3. W **serwera konfiguracji, nazwy FQDN lub adres IP**, określ nazwę lub adres IP serwera konfiguracji za pomocą którego ma zostać zarejestrowany na serwerze przetwarzania.
4. W **Port serwera konfiguracji**, upewnij się, że określono 443. Jest to port, na którym nasłuchuje serwer konfiguracji dla żądań.
5. W **hasło połączenia**, określ hasło określone podczas konfigurowania serwera konfiguracji. Aby znaleźć hasło:
    -  Na serwerze konfiguracji, przejdź do folderu instalacji usługi Site Recovery * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Uruchom poniższe polecenie, aby wydrukować bieżące hasło:
    ```
    genpassphrase.exe -n
    ```

6. W **Port transferu danych**, pozostaw wartość domyślną, chyba że został określony port niestandardowy.

7. Kliknij przycisk **Zapisz** zapisać ustawienia i rejestrowanie serwera przetwarzania.

    
    ![Rejestrowanie serwera przetwarzania](./media/site-recovery-vmware-register-process-server/register-ps.png)

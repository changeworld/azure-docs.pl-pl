---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908539"
---
Kroki wyrejestrowywania serwera przetwarzania różnią się zależnie od jego stanu połączenia z serwerem konfiguracji.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Wyrejestrowywanie serwera przetwarzania, który jest w stanie Połączono

1. Zaloguj się zdalnie do serwera przetwarzania jako administrator.
2. Uruchom **Panelu sterowania** , a następnie otwórz **programy > Odinstaluj program**.
3. Odinstaluj program o nazwie **Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy**.
4. Odinstaluj program o nazwie **serwera konfiguracji/przetwarzania usługi Microsoft Azure Site Recovery**.
5. Odinstalowanie programów w kroku 3 i 4, możesz odinstalować **zależności serwera konfiguracji/przetwarzania odzyskiwania witryny Microsoft Azure**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Wyrejestrowywanie serwera przetwarzania, który jest w stanie Rozłączono

> [!WARNING]
> Użyj poniższych kroków, jeśli ma sposobu reaktywowania maszyny wirtualnej, na którym zainstalowano serwer przetwarzania.

1. Zaloguj się do serwera konfiguracji jako Administrator.
2. Otwórz administracyjny wiersz polecenia i przejdź do katalogu `%ProgramData%\ASR\home\svsystems\bin`.
3. Teraz uruchom polecenie.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Powyższe polecenie będzie dostarczać listę serwery przetwarzania (może mieć więcej niż jednym przypadku zduplikowane wpisy) number(S.No) szeregowe, adres IP (IP), Nazwa maszyny Wirtualnej, na który serwer procesu wdrażania (nazwa), serce zapewniała maszyny wirtualnej (pulsu), jak pokazano poniżej.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Teraz wprowadź numer seryjny serwera przetwarzania, którą chcesz, aby wyrejestrować.
6. Spowoduje to wyczyszczenie informacji o serwerze przetwarzania z systemu i wyświetli komunikat: **Pomyślnie wyrejestrowano nazwa serwera > (-adres IP serwera-)**


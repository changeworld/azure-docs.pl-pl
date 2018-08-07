---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582753"
---
Kroki wyrejestrowywania serwera przetwarzania różnią się zależnie od jego stanu połączenia z serwerem konfiguracji.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Wyrejestrowywanie serwera przetwarzania, który jest w stanie Połączono

1. Zaloguj się zdalnie do serwera przetwarzania jako administrator.
2. Uruchom **Panel sterowania** i otwórz pozycje **Programy > Odinstaluj program**
3. Odinstaluj program o nazwie **Serwer konfiguracji/przetwarzania usługi Microsoft Azure Site Recovery**
4. Po ukończeniu kroku 3 możesz odinstalować **zależności serwera konfiguracji/przetwarzania usługi Microsoft Azure Site Recovery**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Wyrejestrowywanie serwera przetwarzania, który jest w stanie Rozłączono

> [!WARNING]
> Poniższych kroków należy użyć, jeśli nie ma sposobu reaktywowania maszyny wirtualnej, na której zainstalowano serwer przetwarzania.

1. Zaloguj się do serwera konfiguracji jako Administrator.
2. Otwórz administracyjny wiersz polecenia i przejdź do katalogu `%ProgramData%\ASR\home\svsystems\bin`.
3. Teraz uruchom polecenie.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Powyższe polecenie będzie dostarczać listę serwery przetwarzania (może mieć więcej niż jednym przypadku zduplikowane wpisy) number(S.No) szeregowe, adres IP (IP), Nazwa maszyny Wirtualnej, na który serwer procesu wdrażania (nazwa), serce zapewniała maszyny wirtualnej (pulsu), jak pokazano poniżej.
    ![Wyrejestruj cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Teraz wprowadź numer seryjny serwera przetwarzania, którą chcesz, aby wyrejestrować.
6. To spowoduje to wyczyszczenie informacji o serwerze przetwarzania z systemu i wyświetli komunikat o: **został pomyślnie wyrejestrowany nazwa serwera > (-adres IP serwera-)**


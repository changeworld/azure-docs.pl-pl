---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170023"
---
Wykonaj kroki dla Twojego określonych okolicznościach.

### <a name="unregister-a-connected-process-server"></a>Wyrejestrowywanie serwera przetwarzania połączone

1. Nawiąż połączenie zdalne do serwera przetwarzania jako Administrator.
2. W **Panelu sterowania**, otwórz **programy > Odinstaluj program**.
3. Odinstaluj program **Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy**.
4. Odinstaluj program **serwera konfiguracji/przetwarzania usługi Microsoft Azure Site Recovery**.
5. Po odinstalowaniu są programy w kroku 3 i 4, należy odinstalować **zależności serwera konfiguracji/przetwarzania odzyskiwania witryny Microsoft Azure**.

### <a name="unregister-a-disconnected-process-server"></a>Wyrejestrowywanie serwera przetwarzania odłączony

Te kroki należy używać tylko, jeśli nie sposobu reaktywowania maszyny, na którym jest zainstalowany na serwerze przetwarzania.

1. Zaloguj się na serwerze konfiguracji jako Administrator.
2. Otwórz administracyjny wiersz polecenia i przejdź do `%ProgramData%\ASR\home\svsystems\bin`.
3. Uruchom następujące polecenie w celu uzyskania listy jeden lub więcej serwerów przetwarzania.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - Mag. Nie: numer seryjny serwera przetwarzania.
    - IP/nazwa: Adres IP i nazwę maszyny z uruchomionym serwerem przetwarzania.
    - Pulsu: Ostatni Puls z komputera serwera przetwarzania.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Określ numer seryjny serwera przetwarzania, które chcesz wyrejestrować.
5. Wyrejestrowywanie serwera przetwarzania usunąć wszystkie szczegółowe informacje z systemu i wyświetla komunikat: **Pomyślnie wyrejestrowano nazwa serwera > (-adres IP serwera-)**


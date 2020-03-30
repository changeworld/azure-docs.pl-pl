---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183759"
---
Postępuj zgodnie z instrukcjami dotyczącymi konkretnych okoliczności.

### <a name="unregister-a-connected-process-server"></a>Wyrejestrowywać podłączony serwer procesów

1. Nawiązuj zdalne połączenie z serwerem przetwarzania jako administrator.
2. W **Panelu sterowania**otwórz program **> Odinstaluj program**.
3. Odinstaluj program **Usługi mobilności odzyskiwania witryny platformy Microsoft Azure/głównego serwera docelowego**.
4. Odinstaluj program **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Po odinstalowaniu programów w krokach 3 i 4 odinstaluj **konfigurację/zależności serwera przetwarzania usługi Microsoft Azure Site Recovery**.

### <a name="unregister-a-disconnected-process-server"></a>Wyrejestrowywał odłączony serwer procesów

Tych kroków należy wykonać tylko wtedy, gdy nie ma możliwości ożywienia komputera, na którym jest zainstalowany serwer przetwarzania.

1. Zaloguj się na serwerze konfiguracji jako administrator.
2. Otwórz wiersz polecenia Administracyjne i `%ProgramData%\ASR\home\svsystems\bin`przejdź do .
3. Uruchom to polecenie, aby uzyskać listę co najmniej jednego serwera przetwarzania.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - Mag. Nie: numer seryjny serwera przetwarzania.
    - Adres IP/Nazwa: Adres IP i nazwa komputera z serwerem przetwarzania.
    - Puls: Ostatnie bicie serca z komputera serwera przetwarzania.
    ![Wyrejestrować cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Określ numer seryjny serwera przetwarzania, który ma zostać wyrejestrowany.
5. Wyrejestrowanie serwera przetwarzania powoduje usunięcie wszystkich jego szczegółów z systemu i wyświetlenie komunikatu: **Pomyślnie wyrejestrowana nazwa serwera> (adres IP serwera)**


---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166454"
---
* Połącz się z maszyną wirtualną serwera przetwarzania za pomocą Podłączania pulpitu zdalnego.
* Narzędzie cspsconfigtool.exe można uruchamiać przez kliknięcie skrótu dostępnego na pulpicie. (Narzędzie zostanie automatycznie uruchomiony, jeśli to po raz pierwszy logujesz się do serwera przetwarzania).
  - W pełni kwalifikowana nazwa domeny (FQDN) lub adres IP.
  - Port, na którym nasłuchuje serwer konfiguracji. Ta wartość powinna być równa 443.
  - Hasło połączenia do łączenia się z serwerem konfiguracji.
  - Port transferu danych do skonfigurowania dla tego serwera przetwarzania. Pozostaw wartość domyślną, chyba że w swoim środowisku masz ustawiony inny numer portu.

    ![Rejestrowanie serwera przetwarzania](./media/site-recovery-vmware-register-process-server/register-ps.png)
* Kliknij przycisk Zapisz, aby zapisać konfigurację i zarejestrować serwer przetwarzania.

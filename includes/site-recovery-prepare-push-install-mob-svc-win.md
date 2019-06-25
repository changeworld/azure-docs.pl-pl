---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: ffc9b09c72ef1bf5180a0d626908d09b6fdd41ca
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203726"
---
### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Przygotowanie do instalacji wypychanej na komputerze Windows

1. Upewnij się, że istnieje połączenie sieciowe między komputerem Windows i serwerem przetwarzania.
1. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno mieć uprawnień administratora lokalnego lub domeny. Tylko w przypadku instalacji wypychanej i aktualizacji agenta, należy używać tego konta.

   > [!NOTE]
   > Jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym. Aby wyłączyć kontrolę dostępu użytkowników zdalnych, w kluczu rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System Dodaj nową wartość typu DWORD: **LocalAccountTokenFilterPolicy**. Ustaw wartość **1**. Aby wykonać to zadanie w wierszu polecenia, uruchom następujące polecenie:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. W Zaporze Windows na komputerze, który chcesz chronić, wybierz **Zezwalaj aplikacji lub funkcji za pośrednictwem zapory**. Włącz **udostępnianie plików i drukarek** i **Instrumentacji zarządzania Windows (WMI)** . Dla komputerów, które należą do domeny można skonfigurować ustawienia zapory przy użyciu obiektu zasad grupy (GPO).

   ![Ustawienia zapory](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. Dodaj konto utworzone w narzędziu CSPSConfigtool. Wykonaj następujące kroki:

    a. Zaloguj się do serwera konfiguracji.

    b. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze %ProgramData%\home\svsystems\bin.

    c. Na **Zarządzanie kontami** zaznacz **Dodaj konto**.

    d. Dodaj utworzone konto.

    e. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.

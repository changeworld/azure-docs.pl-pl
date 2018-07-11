---
title: Dodawanie skryptu do planu odzyskiwania, w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wymagań wstępnych dotyczących dodawania nowego skryptu programu System Center Virtual Machine Manager (VMM) do planu odzyskiwania na platformie Azure.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: rochakm
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 71991347ffaf036065aae9e1a93b7eb83a14b15c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917344"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Skrypt programu VMM do planu odzyskiwania

W tym artykule opisano sposób tworzenia skryptów programu System Center Virtual Machine Manager (VMM) i dodać ją do planu odzyskiwania w [usługi Azure Site Recovery](site-recovery-overview.md).

Zamieść wszelkie komentarze lub pytania pod tym artykułem lub na [forum usług Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Wymagania wstępne

Można użyć skryptów programu PowerShell w planów odzyskiwania. Było dostępne z planu odzyskiwania, możesz utworzyć skrypt i umieścić skrypt w bibliotece programu VMM. Podczas pisania skryptu, należy pamiętać o następujących kwestiach:

* Upewnij się, że skrypty używają bloków try-catch tak, aby bezpiecznie obsługi wyjątków.
    - Jeśli wystąpi wyjątek w skrypcie, skrypt przestanie działać, a zadanie jest wyświetlana jako nie powiodła się.
    - Jeśli wystąpi błąd, nie zostanie uruchomiony w pozostałej części skryptu.
    - Jeśli błąd wystąpi po uruchomieniu nieplanowanego trybu failover, plan odzyskiwania jest kontynuowane.
    - Jeśli wystąpi błąd podczas uruchamiania planowanego trybu failover, nie będzie możliwy planu odzyskiwania. Napraw skrypt, upewnij się, że działa zgodnie z oczekiwaniami i następnie uruchom ponownie planu odzyskiwania.
        - `Write-Host` Polecenie nie działa w skrypcie planu odzyskiwania. Jeśli używasz `Write-Host` polecenie w skrypcie, skrypt zakończy się niepowodzeniem. Aby utworzyć dane wyjściowe, Utwórz skrypt serwera proxy, który z kolei jest uruchamiany skrypt głównego. Aby upewnić się, że wszystkie dane wyjściowe w potoku się, należy użyć **\> \>** polecenia.
        - Limit czasu skryptu, jeśli nie zwraca w ramach 600 sekund.
        - Jeśli wszystko jest zapisywany do strumienia wyjściowego STDERR, skrypt jest klasyfikowany jako zakończony niepowodzeniem. Te informacje są wyświetlane szczegóły wykonywania skryptu.

* Skrypty w planie odzyskiwania są uruchamiane w kontekście konta usługi programu VMM. Upewnij się, że to konto ma uprawnienia do udziału zdalnego, na którym zlokalizowany jest skrypt do odczytu. Przetestuj skrypt do uruchomienia na tym samym poziomie uprawnień użytkownika, co konto usługi programu VMM.
* Poleceń cmdlet programu VMM są dostarczane w module Windows PowerShell. Moduł jest zainstalowany podczas instalacji konsoli programu VMM. Aby załadować moduł do skryptu, wpisz następujące polecenie w skrypcie: 

    `Import-Module -Name virtualmachinemanager`

    Aby uzyskać więcej informacji, zobacz [rozpoczęcie korzystania z programu Windows PowerShell i VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Upewnij się, że masz co najmniej jeden serwer biblioteki w danym wdrożeniu programu VMM. Domyślnie ścieżki udziału bibliotecznego dla serwera programu VMM znajduje się lokalnie na serwerze programu VMM. Nazwa folderu jest MSCVMMLibrary.

  W przypadku zdalnego (lub jeśli jest lokalny, ale nie są udostępniane MSCVMMLibrary) Twojej ścieżki udziału bibliotecznego Konfigurowanie udziału w następujący sposób, przy użyciu \\libserver2.contoso.com\share\ jako przykładu:
  
  1. Otwórz Edytor rejestru, a następnie przejdź do **Recovery\Registration witryny HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure**.

  2. Zmień wartość **ScriptLibraryPath** do  **\\\libserver2.contoso.com\share\\**. Podaj pełną nazwę FQDN. Podaj uprawnienia do lokalizacji udziału. Jest to węzeł główny udziału. Aby sprawdzić, czy węzeł główny w programie VMM, przejdź do węzła głównego w bibliotece. Ścieżka, która zostanie otwarta jest poziom główny ścieżki. Jest to ścieżka, którego należy użyć w zmiennej.

  3. Testowanie skryptu przy użyciu konta użytkownika, który ma ten sam poziom uprawnień użytkownika, co konto usługi programu VMM. Za pomocą tych praw użytkownika sprawdza ten autonomicznej, przetestowane skrypty uruchamiania tak samo jak działają usługi planów odzyskiwania. Na serwerze programu VMM należy ustawić zasady wykonywania na obejście w następujący sposób:

     a. Otwórz **64-bitowych Windows PowerShell** konsoli jako administrator.
     
     b. Wprowadź **obejścia Set-executionpolicy**. Aby uzyskać więcej informacji, zobacz [przy użyciu polecenia cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Ustaw **obejścia Set-executionpolicy** tylko w konsoli programu PowerShell w 64-bitowych. Jeśli zostanie ustawiona dla 32-bitowych konsoli programu PowerShell, nie uruchamiaj skrypty.

## <a name="add-the-script-to-the-vmm-library"></a>Dodawanie skryptu do biblioteki programu VMM

W przypadku lokacji źródłowej programu VMM, możesz utworzyć skrypt na serwerze programu VMM. Następnie dołącz skryptu planu odzyskiwania.

1. W bibliotece udostępnionej Utwórz nowy folder. Na przykład \<nazwa serwera VMM > \MSSCVMMLibrary\RPScripts. Lokalizację folderu źródłowego i docelowych serwerów programu VMM.
2. Utwórz skrypt. Na przykład nazwę skryptu RPScript. Sprawdź, czy skrypt działa zgodnie z oczekiwaniami.
3. Umieść skrypt w \<nazwa serwera VMM > \MSSCVMMLibrary folder na serwerach VMM źródłowym i docelowym.

## <a name="add-the-script-to-a-recovery-plan"></a>Dodawanie skryptu do planu odzyskiwania

Po dodaniu maszyny wirtualne lub grupy replikacji do planu odzyskiwania, a dla planu można dodać skrypt do grupy.

1. Otwórz plan odzyskiwania.
2. W **kroku** , wybierz element na liście. Następnie wybierz opcję **skryptu** lub **Akcja ręczna**.
3. Określ, czy można dodać skrypt lub akcji przed lub po wybranego elementu. Aby przenieść pozycja skrypt w górę lub w dół, wybierz pozycję **Przenieś w górę** i **Przenieś w dół** przycisków.
4. Jeśli dodasz skryptu programu VMM, zaznacz **przejściu w tryb Failover skryptu VMM programu**. W **ścieżka skryptu**, wprowadź ścieżkę względną do udziału. Na przykład, wprowadź **\RPScripts\RPScript.PS1**.
5. Jeśli dodasz element runbook usługi Automation, należy określić konto usługi Automation, w którym znajduje się element runbook. Następnie wybierz skrypt elementów runbook platformy Azure, którego chcesz użyć.
6. Aby upewnić się, że skrypt działa zgodnie z oczekiwaniami, należy wykonać test trybu failover planu odzyskiwania.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [uruchamiania trybu failover](site-recovery-failover.md).


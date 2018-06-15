---
title: Skrypt do planu odzyskiwania w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wymagań wstępnych dotyczących dodawania nowego skryptu programu System Center Virtual Machine Manager (VMM) do planu odzyskiwania na platformie Azure.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: shons
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 2e00f812fb35ac9a0cb390fc6a3ba40a8678f8dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402433"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Skrypt programu VMM do planu odzyskiwania

W tym artykule opisano sposób tworzenia skryptu programu System Center Virtual Machine Manager (VMM) i dodaj go do planu odzyskiwania w [usługi Azure Site Recovery](site-recovery-overview.md).

Zamieść wszelkie komentarze lub pytania w dolnej części tego artykułu lub na [forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Wymagania wstępne

Można użyć skryptów środowiska PowerShell w planów odzyskiwania. Mają być dostępne z planu odzyskiwania, należy utworzyć skrypt i umieść skrypt w bibliotece programu VMM. Podczas pisania skryptu, należy pamiętać o następujących kwestiach:

* Upewnij się, że skrypty używać bloków try-catch tak, aby bezpiecznie obsługi wyjątków.
    - Jeśli wystąpi wyjątek w skrypcie, skrypt przestanie działać, i pokazuje zadania nie powiodło się.
    - Jeśli wystąpi błąd, nie działa w pozostałej części skryptu.
    - Jeśli błąd wystąpi po uruchomieniu nieplanowanego trybu failover, nadal planu odzyskiwania.
    - Jeśli wystąpi błąd podczas uruchamiania planowanego trybu failover, zatrzymuje planu odzyskiwania. Napraw skrypt, sprawdź, czy działa zgodnie z oczekiwaniami, a następnie uruchom odzyskiwanie Zaplanuj ponownie.
        - `Write-Host` Polecenie nie działa w skryptu planu odzyskiwania. Jeśli używasz `Write-Host` polecenie w skrypcie, skrypt zakończy się niepowodzeniem. Aby utworzyć dane wyjściowe, należy utworzyć skrypt serwera proxy, który z kolei uruchamia skrypt głównego. Aby upewnić się, że wszystkie dane wyjściowe jest przekazywany w potoku limit, użyj  **\> \>**  polecenia.
        - Limit czasu skryptu, jeśli nie zwraca w 600 sekund.
        - Jeśli niczego są zapisywane do strumienia wyjściowego STDERR, skrypt zostanie sklasyfikowany jako zakończony niepowodzeniem. Te informacje są wyświetlane w szczegółach wykonanie skryptu.

* Skrypty w planie odzyskiwania są uruchamiane w kontekście konta usługi programu VMM. Upewnij się, że to konto ma uprawnienia do udziału zdalnego, na którym zlokalizowany jest skrypt do odczytu. Testowanie skryptu do uruchomienia z tym samym poziomie uprawnień użytkownika, co konto usługi VMM.
* Poleceń cmdlet programu VMM są dostarczane w moduł programu Windows PowerShell. Moduł jest zainstalowany, po zainstalowaniu konsoli programu VMM. Aby załadować moduł do skrypt, wpisz następujące polecenie w skrypcie: 

    `Import-Module -Name virtualmachinemanager`

    Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Windows PowerShell i program VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Upewnij się, że masz co najmniej jednego serwera biblioteki w danym wdrożeniu programu VMM. Domyślnie ścieżkę udziału biblioteki na serwerze VMM znajduje się lokalnie na serwerze programu VMM. Nazwa folderu jest MSCVMMLibrary.

  Jeśli Twoje ścieżki udziału bibliotecznego jest zdalne (lub jeśli jest lokalny, ale nie są udostępniane MSCVMMLibrary), skonfiguruj udział, przy użyciu \\libserver2.contoso.com\share\ na przykład:
  
  1. Otwórz Edytor rejestru, a następnie przejdź do **Recovery\Registration lokacji HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure**.

  2. Zmień wartość atrybutu **ScriptLibraryPath** do  **\\\libserver2.contoso.com\share\\**. Określ pełną nazwę FQDN. Podaj uprawnieniami do lokalizacji udziału. To jest węzeł główny udziału. Aby sprawdzić, czy węzeł główny w programie VMM, przejdź do węzła głównego w bibliotece. Ścieżka, którego kliknięcie spowoduje otwarcie jest główny ścieżki. Jest to ścieżka, którego musisz użyć w zmiennej.

  3. Testowanie skryptu przy użyciu konta użytkownika, który ma taki sam poziom uprawnień użytkownika, jako konto usługi VMM. Przy użyciu tych praw użytkownika sprawdza tego autonomiczny przetestowany skryptów działać tak samo, jak działają w planie odzyskiwania. Na serwerze programu VMM ustawienie zasad wykonywania, aby pominąć, w następujący sposób:

     a. Otwórz **64-bitowego systemu Windows PowerShell** konsoli jako administrator.
     
     b. Wprowadź **obejścia Set-executionpolicy**. Aby uzyskać więcej informacji, zobacz [przy użyciu polecenia cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Ustaw **obejścia Set-executionpolicy** tylko w konsoli programu PowerShell 64-bitowych. Jeśli zostanie ustawiona dla konsoli programu PowerShell 32-bitowy, nie uruchamiaj skrypty.

## <a name="add-the-script-to-the-vmm-library"></a>Dodaj skrypt do biblioteki programu VMM

Jeśli masz lokację źródłową programu VMM, można utworzyć skrypt na serwerze programu VMM. Następnie wprowadzić skrypt do planu odzyskiwania.

1. W udziale biblioteki Utwórz nowy folder. Na przykład \<nazwa serwera VMM > \MSSCVMMLibrary\RPScripts. Lokalizację folderu źródłowego i docelowego serwerów programu VMM.
2. Utwórz skrypt. Na przykład nazwę skryptu RPScript. Sprawdź, czy skrypt działa zgodnie z oczekiwaniami.
3. Umieść skrypt w \<nazwa serwera VMM > folderu \MSSCVMMLibrary na serwerach VMM źródłowych i docelowych.

## <a name="add-the-script-to-a-recovery-plan"></a>Dodaj skrypt do planu odzyskiwania

Po dodaniu maszyny wirtualne lub grup replikacji w ramach planu odzyskiwania i dla planu, można dodać skrypt do grupy.

1. Otwórz planu odzyskiwania.
2. W **krok** , wybierz element na liście. Następnie wybierz opcję **skryptu** lub **Akcja ręczna**.
3. Określ, czy można dodać skrypt lub akcji przed lub po wybranego elementu. Aby przemieścić skryptu w górę lub w dół, wybierz **Przenieś w górę** i **Przenieś w dół** przycisków.
4. Jeśli dodasz skryptu programu VMM, wybierz **trybu Failover do skryptu VMM**. W **ścieżka skryptu**, wprowadź ścieżkę względną do udziału. Na przykład wprowadź **\RPScripts\RPScript.PS1**.
5. Jeśli dodasz element runbook usługi Automatyzacja Azure, należy określić konto automatyzacji, w której znajduje się element runbook. Wybierz skrypt elementów runbook platformy Azure, którego chcesz użyć.
6. Aby upewnić się, że skrypt działa zgodnie z oczekiwaniami, należy wykonać test trybu failover planu odzyskiwania.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [uruchamiania trybu failover](site-recovery-failover.md).


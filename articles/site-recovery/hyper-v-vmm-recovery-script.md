---
title: Dodawanie skryptu do planu odzyskiwania w usłudze Azure Site Recovery
description: Dowiedz się, jak dodać skrypt programu VMM do planu odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084870"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Dodawanie skryptu programu VMM do planu odzyskiwania

W tym artykule opisano sposób tworzenia skryptu Menedżera maszyn wirtualnych (System Center Virtual Machine Manager) i dodawania go do planu odzyskiwania w [usłudze Azure Site Recovery](site-recovery-overview.md).

Wszelkie uwagi lub pytania można zadać na dole tego artykułu lub na [forum usługi Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Wymagania wstępne

Skryptów programu PowerShell można używać w planach odzyskiwania. Aby można było udostępnić go z planu odzyskiwania, należy go autoryzować i umieścić w bibliotece programu VMM. Podczas pisania skryptu należy pamiętać o następujących zagadnieniach:

* Upewnij się, że skrypty używają try-catch bloków, tak aby wyjątki są obsługiwane bezpiecznie.
    - Jeśli w skrypcie wystąpi wyjątek, skrypt przestanie działać, a zadanie jest wyświetlane jako nieudane.
    - Jeśli wystąpi błąd, pozostała część skryptu nie zostanie uruchomiony.
    - Jeśli wystąpi błąd po uruchomieniu nieplanowanej pracy awaryjnej, plan odzyskiwania jest kontynuowany.
    - Jeśli wystąpi błąd podczas uruchamiania planowanej pracy awaryjnej, plan odzyskiwania zostanie zatrzymany. Napraw skrypt, sprawdź, czy działa zgodnie z oczekiwaniami, a następnie uruchom plan odzyskiwania ponownie.
        - Polecenie `Write-Host` nie działa w skrypcie planu odzyskiwania. Jeśli używasz `Write-Host` polecenia w skrypcie, skrypt kończy się niepowodzeniem. Aby utworzyć dane wyjściowe, utwórz skrypt proxy, który z kolei uruchamia skrypt główny. Aby upewnić się, że wszystkie ** \> ** dane wyjściowe są wykreślane, należy użyć polecenia.
        - Czas skryptu, jeśli nie powróci w ciągu 600 sekund.
        - Jeśli cokolwiek jest napisane do STDERR, skrypt jest klasyfikowany jako nie powiodło się. Te informacje są wyświetlane w szczegółach wykonywania skryptu.

* Skrypty w planie odzyskiwania są uruchamiane w kontekście konta usługi VMM. Upewnij się, że to konto ma uprawnienia do odczytu dla udziału zdalnego, na którym znajduje się skrypt. Przetestuj skrypt, aby uruchomić z tym samym poziomem praw użytkownika co konto usługi VMM.
* Polecenia cmdlet programu VMM są dostarczane w module programu Windows PowerShell. Moduł jest instalowany podczas instalowania konsoli programu VMM. Aby załadować moduł do skryptu, użyj następującego polecenia w skrypcie: 

    `Import-Module -Name virtualmachinemanager`

    Aby uzyskać więcej informacji, zobacz [Wprowadzenie do programów Windows PowerShell i VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Upewnij się, że masz co najmniej jeden serwer biblioteki we wdrożeniu programu VMM. Domyślnie ścieżka udziału biblioteki dla serwera programu VMM znajduje się lokalnie na serwerze programu VMM. Nazwa folderu to MSCVMMLibrary.

  Jeśli ścieżka udziału biblioteki jest zdalna (lub jeśli jest lokalna, ale nie jest współużytkowana z programem MSCVMMLibrary), skonfiguruj udział w następujący sposób, używając \\libserver2.contoso.com\share\ jako przykładu:
  
  1. Otwórz Edytor rejestru, a następnie przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Zmień wartość **ScriptLibraryPath** na ** \\\libserver2.contoso.com\share\\**. Określ pełną nazwę FQDN. Podaj uprawnienia do lokalizacji udziału. Jest to węzeł główny udziału. Aby sprawdzić, czy węzeł główny, w programu VMM przejdź do węzła głównego w bibliotece. Ścieżka, która się otworzy, jest katalogiem głównym ścieżki. Jest to ścieżka, która musi być używana w zmiennej.

  1. Przetestuj skrypt przy użyciu konta użytkownika, które ma taki sam poziom praw użytkownika jak konto usługi VMM. Korzystanie z tych praw użytkownika weryfikuje, że autonomiczne, przetestowane skrypty działają w taki sam sposób, jak są one uruchamiane w planach odzyskiwania. Na serwerze programu VMM ustaw zasady wykonywania, aby obejść, w następujący sposób:

     a. Otwórz **64-bitową** konsolę programu Windows PowerShell jako administrator.
     
     b. Wprowadź **obejście zasady ustawiania wykonania**. Aby uzyskać więcej informacji, zobacz [Korzystanie z polecenia cmdlet Ustawianie-WykonaniePolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Ustaw **obejście zasady set-execution** tylko w 64-bitowej konsoli programu PowerShell. Jeśli ustawisz go dla 32-bitowej konsoli programu PowerShell, skrypty nie będą uruchamiane.

## <a name="add-the-script-to-the-vmm-library"></a>Dodawanie skryptu do biblioteki programu VMM

Jeśli masz witrynę źródłową programu VMM, możesz utworzyć skrypt na serwerze programu VMM. Następnie dołącz skrypt do planu odzyskiwania.

1. W udziale biblioteki utwórz nowy folder. Na przykład \<nazwa serwera programu VMM>\MSSCVMMLibrary\RPScripts. Umieść folder na źródłowych i docelowych serwerach programu VMM.
1. Utwórz skrypt. Na przykład nazwa skryptu RPScript. Sprawdź, czy skrypt działa zgodnie z oczekiwaniami.
1. Umieść skrypt w \<folderze>\MSSCVMMLibrary na serwerach źródłowych i docelowych programu VMM.

## <a name="add-the-script-to-a-recovery-plan"></a>Dodawanie skryptu do planu odzyskiwania

Po dodaniu maszyn wirtualnych lub grup replikacji do planu odzyskiwania i utworzeniu planu można dodać skrypt do grupy.

1. Otwórz plan odzyskiwania.
1. Na liście **Krok** wybierz element. Następnie wybierz opcję **Skrypt** lub **Akcja ręczna**.
1. Określ, czy skrypt lub akcja ma być dodana przed lub po wybranym elemencie. Aby przesunąć położenie skryptu w górę lub w dół, wybierz przyciski **Przenieś w górę** i Przenieś w **dół.**
1. Jeśli dodasz skrypt programu VMM, wybierz opcję **Failover do skryptu programu VMM**. W **ścieżce skryptu**wprowadź ścieżkę względną do udziału. Na przykład wprowadź **\RPScripts\RPScript.PS1**.
1. Jeśli dodasz element runbook usługi Azure Automation, określ konto automatyzacji, w którym znajduje się element runbook. Następnie wybierz skrypt podręcznika azure, którego chcesz użyć.
1. Aby upewnić się, że skrypt działa zgodnie z oczekiwaniami, wykonaj testowy testowy plan odzyskiwania.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [uruchamianiu pracy w pracy awaryjnej](site-recovery-failover.md).


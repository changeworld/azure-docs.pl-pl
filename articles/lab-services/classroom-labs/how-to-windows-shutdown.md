---
title: Przewodnik dotyczący kontrolowania zachowania zamknięcia systemu Windows w usługach Azure Lab Services | Dokumenty firmy Microsoft
description: Kroki, aby automatycznie zamknąć bezczynną maszynę wirtualną systemu Windows i usunąć polecenie zamykania systemu Windows.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522240"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Przewodnik dotyczący kontrolowania zachowania zamknięcia systemu Windows

Usługa Azure Lab Services udostępnia kilka kontroli kosztów, aby upewnić się, że maszyny wirtualne systemu Windows (VM) nie są uruchamiane nieoczekiwanie:
 - [Ustawianie harmonogramu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Ustawianie przydziałów dla użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Włączanie automatycznego zamykania po rozłączeniu](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Nawet w przypadku tych kontroli kosztów istnieją sytuacje, w których maszyna wirtualna systemu Windows może nieoczekiwanie kontynuować pracę; i w rezultacie odejmują od kwoty studenta:

- **Okno RDP pozostaje otwarte**
  
    Gdy uczeń łączy się z maszyną wirtualną przy użyciu protokołu RDP, może przypadkowo pozostawić otwarte okno PROW.  Dopóki okno RDP pozostanie otwarte, automatyczne wyłączenie po **wyłączeniu po wyłączeniu** nigdy nie zacznie obowiązywać, ponieważ jest wyzwalane dopiero po rozłączeniu sesji RDP.

- **Polecenie zamykania systemu Windows służy do wyłączania maszyny Wirtualnej**
  
    Student może użyć polecenia zamknięcia systemu Windows lub innych mechanizmów zamykania systemu Windows, aby wyłączyć maszynę wirtualną zamiast używać [przycisku zatrzymania usługi Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  W takim przypadku z punktu widzenia usługi Azure Lab Services maszyna wirtualna jest nadal używana.
    
Aby zapobiec takim sytuacjom, w tym przewodniku przedstawiono kroki automatycznego zamykania bezczynnej maszyny Wirtualnej systemu Windows i usuwania polecenia zamykania systemu Windows z menu **Start.**  

> [!NOTE]
> Maszyna wirtualna może również nieoczekiwanie odliczyć od przydziału, gdy student uruchamia maszynę wirtualną, ale nigdy nie łączy się z nim przy użyciu protokołu RDP.  Ten przewodnik *nie* zajmuje się obecnie tym scenariuszem.  Zamiast tego należy przypomnieć uczniom, aby natychmiast połączyli się z maszyną wirtualną za pomocą protokołu RDP po jej uruchomieniu; lub powinny zatrzymać maszynę wirtualną.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatyczne rozłączanie i wyłączanie prow dla bezczynnej maszyny Wirtualnej

System Windows udostępnia ustawienia **zasad grupy lokalnej,** których można użyć do ustawienia limitu czasu automatycznego rozłączania sesji RDP, gdy stanie się ona bezczynna.  Sesja jest określana jako bezczynna, gdy *nie* ma żadnych danych wejściowych myszy\klawiatury.  Wszelkie długotrwałe działania, które nie wymagają myszki \klawiatury wprowadzania danych powodują, że maszyna wirtualna jest w stanie bezczynnym.  Obejmuje to wykonywanie długiego zapytania, strumieniowe przesyłanie wideo, kompilowanie itp.  W zależności od potrzeb klasy można ustawić limit czasu bezczynności, tak aby był wystarczająco długi, aby obsłużyć tego typu działania.  Na przykład można ustawić limit czasu bezczynności na 1 lub więcej godzin, jeśli to konieczne.

Oto doświadczenie ucznia podczas konfigurowania **limitu bezczynności sesji** w połączeniu z [**automatycznym zamknięciem przy rozłączeniu:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. Uczeń łączy się z maszyną wirtualną systemu Windows przy użyciu protokołu RDP.
 2. Gdy uczeń opuści okno RDP otwarte, a maszyna wirtualna jest bezczynna dla określonego **limitu sesji bezczynności** (na przykład 5 minut), uczeń zobaczy następujące okno dialogowe:

    ![Okno dialogowe Przekroczenie limitu czasu bezczynności](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Jeśli uczeń *nie* kliknie **przycisku OK,** jego sesja RDP zostanie automatycznie rozłączona po 2 minutach.
2. Po rozłączeniu sesji RDP, po osiągnięciu określonego przedziału czasu **automatycznego zamykania na ustawienie rozłączania,** maszyna wirtualna jest automatycznie zamykana przez usługi Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Ustawianie limitu czasu bezczynności sesji protokołu RDP na szablonie maszyny Wirtualnej

Aby ustawić limit czasu bezczynności sesji RDP, można połączyć się z szablonem maszyny Wirtualnej i wykonać poniższy skrypt programu PowerShell.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Możesz też wykonać następujące ręczne kroki przy użyciu szablonu maszyny Wirtualnej:

1. Naciśnij klawisz Windows, wpisz **gpedit**, a następnie wybierz pozycję **Edytuj zasady grupy (panel Sterowania).**

1. Przejdź do funkcji **Konfiguracja komputera > szablonów administracyjnych > składniki systemu Windows > usługi pulpitu zdalnego > limity czasu sesji hosta sesji usług pulpitu zdalnego > sesji**.  

    ![Edytor lokalnych zasad grupy](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Kliknij prawym przyciskiem myszy pozycję **Ustaw limit czasu dla aktywnych, bezczynnych sesji usług pulpitu zdalnego,** a następnie kliknij polecenie **Edytuj**.

1. Wprowadź następujące ustawienia, a następnie kliknij przycisk **OK**:
   1. Wybierz pozycję **Włączone**.
   1. W obszarze **Opcje**określ **limit sesji bezczynności**.

    ![Limit bezczynności sesji](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Na koniec, aby połączyć to zachowanie z **automatycznym zamknięciem przy rozłączeniu,** należy wykonać kroki opisane w artykule in jak to zrobić: [Włącz automatyczne zamykanie maszyn wirtualnych przy odłączaniu](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Polecenie Usuń zamknięcie systemu Windows z menu Start

Ustawienia **zasad grupy lokalnej** systemu Windows umożliwiają również usunięcie polecenia zamknięcia z menu **Start.**

Aby usunąć polecenie zamknięcia, można połączyć się z szablonem maszyny Wirtualnej i wykonać poniższy skrypt programu PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Możesz też wykonać następujące ręczne kroki przy użyciu szablonu maszyny Wirtualnej:

1. Naciśnij klawisz Windows, wpisz **gpedit**, a następnie wybierz pozycję **Edytuj zasady grupy (panel Sterowania).**

1. Przejdź do > **konfiguracji komputera szablonów administracyjnych > menu Start i pasku zadań**.  

    ![Edytor lokalnych zasad grupy](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Kliknij prawym przyciskiem myszy pozycję **Usuń i uniemożliwij dostęp do poleceń Zamknij, Uruchom ponownie, Uśpij i Hibernacja,** a następnie kliknij polecenie **Edytuj**.

1. Wybierz ustawienie **Włączone,** a następnie kliknij przycisk **OK**:
 
   ![Ustawienie zamykania](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Należy zauważyć, że polecenie zamknięcia nie jest już wyświetlane w menu **Start** systemu Windows; pojawi się tylko polecenie **Rozłącz.**

    ![Zamknięcie, polecenie](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Następne kroki
Zobacz artykuł dotyczący przygotowania szablonu maszyny Wirtualnej systemu Windows: [Przewodnik dotyczący konfigurowania komputera z szablonami systemu Windows w usłudze Azure Lab Services](how-to-prepare-windows-template.md)
---
title: Jak uaktualnić agenta zależności Azure Monitor dla maszyn wirtualnych | Microsoft Docs
description: W tym artykule opisano sposób uaktualniania agenta zależności Azure Monitor dla maszyn wirtualnych przy użyciu wiersza polecenia, Kreatora instalacji i innych metod.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2019
ms.author: magoedte
ms.openlocfilehash: 37dcd21e17a47827f9bd4f6e5a756b321cf94495
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809840"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Jak uaktualnić agenta zależności Azure Monitor dla maszyn wirtualnych

Po początkowym wdrożeniu agenta zależności Azure Monitor dla maszyn wirtualnych zostaną wydane aktualizacje zawierające poprawki błędów lub obsługę nowych funkcji lub funkcji.  Ten artykuł ułatwia zapoznanie się z dostępnymi metodami oraz jak przeprowadzić uaktualnienie ręcznie lub za pomocą automatyzacji.

## <a name="upgrade-options"></a>Opcje uaktualniania 

Agenta zależności dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie, w zależności od scenariusza wdrażania i środowiska, w którym jest uruchomiona maszyna. Do uaktualnienia agenta można użyć poniższych metod.

|Środowisko |Metoda instalacji |Metoda uaktualniania |
|------------|--------------------|---------------|
|Maszyna wirtualna platformy Azure | Rozszerzenie maszyny wirtualnej agenta zależności dla [systemów Windows](../../virtual-machines/extensions/agent-dependency-windows.md) i [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agent jest automatycznie uaktualniany, chyba że skonfigurowano szablon Azure Resource Manager, aby zrezygnował z ustawienia właściwości *włączoną flagą autoupgrademinorversion* na **wartość false**. Uaktualnienie wersji pomocniczej, w której funkcja autouaktualnienia jest wyłączona, a uaktualnienie wersji głównej jest zgodne z tą samą metodą — Odinstaluj i ponownie zainstaluj rozszerzenie. |
| Niestandardowe obrazy maszyn wirtualnych platformy Azure | Ręczna instalacja agenta zależności dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta należy wykonać z poziomu wiersza polecenia z uruchomionym pakietem Instalatora Windows lub z samowyodrębniającym się zestawem skryptów powłoki programu Linux.|
| Maszyny wirtualne spoza platformy Azure | Ręczna instalacja agenta zależności dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta należy wykonać z poziomu wiersza polecenia z uruchomionym pakietem Instalatora Windows lub z samowyodrębniającym się zestawem skryptów powłoki programu Linux. |

## <a name="upgrade-windows-agent"></a>Uaktualnij agenta systemu Windows 

Aby zaktualizować agenta na maszynie wirtualnej z systemem Windows do najnowszej wersji, która nie jest zainstalowana przy użyciu rozszerzenia maszyny wirtualnej agenta zależności, można uruchomić polecenie z wiersza polecenia, skryptu lub innego rozwiązania do automatyzacji lub przy użyciu Kreatora instalacji programu InstallDependencyAgent-Windows. exe.  

W [tym miejscu](https://aka.ms/dependencyagentwindows)możesz pobrać najnowszą wersję agenta systemu Windows.

### <a name="using-the-setup-wizard"></a>Korzystanie z Kreatora instalacji

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Wykonaj **InstallDependencyAgent-Windows. exe** , aby uruchomić Kreatora instalacji.

3. W oknie dialogowym **Agent zależności konfiguracja 9.9.1** kliknij przycisk **zgadzam** się, aby zaakceptować umowę licencyjną.

5. W **Agent zależności 9.9.0 Odinstaluj** okno dialogowe, kliknij przycisk **dalej**. Na stronie stan zostanie wyświetlony postęp odinstalowywania poprzedniej wersji.

6. W oknie dialogowym **Agent zależności Odinstaluj 9.9.0** kliknij przycisk **Odinstaluj** , aby kontynuować Odinstalowywanie poprzedniej wersji ze ścieżki określonej w oknie dialogowym. 

7. W oknie dialogowym **Agent zależności Odinstalowywanie 9.9.0** zostanie wyświetlony postęp odinstalowywania i po zakończeniu zostanie wyświetlona strona **ukończ Odinstalowywanie Agent zależności** . Kliknij przycisk **Finish** (Zakończ).

8. W oknie dialogowym **Agent zależności Instalatora 9.9.1** zostanie wyświetlony postęp instalacji. Gdy zostanie wyświetlona strona **kończenie Agent zależności dezinstalacji** , kliknij przycisk **Zakończ**. 

### <a name="from-the-command-line"></a>Z wiersza polecenia

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom następujące polecenie.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual` parametr uniemożliwia automatyczne ponowne uruchomienie komputera, jeśli niektóre procesy używają plików z poprzedniej wersji i mają blokadę. 

3. Aby upewnić się, że uaktualnienie zakończyło się pomyślnie, sprawdź `install.log`, aby uzyskać szczegółowe informacje dotyczące instalacji. Katalog dziennika to *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Uaktualnij agenta systemu Linux 

Uaktualnienie z wcześniejszych wersji agenta zależności w systemie Linux jest obsługiwane i wykonywane przy użyciu tego samego polecenia, co w przypadku nowej instalacji.

W [tym miejscu](https://aka.ms/dependencyagentlinux)możesz pobrać najnowszą wersję agenta systemu Windows.

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom następujące polecenie jako główne`sh InstallDependencyAgent-Linux64.bin -s`. 

Jeśli uruchomienie agenta zależności nie powiedzie się, Sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W przypadku agentów systemu Linux katalog dziennika to */var/opt/Microsoft/Dependency-Agent/log*. 

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zatrzymać monitorowanie maszyn wirtualnych przez pewien czas lub całkowicie usunąć Azure Monitor dla maszyn wirtualnych, zobacz temat [wyłączanie monitorowania maszyn wirtualnych w Azure monitor dla maszyn wirtualnych](vminsights-optout.md).
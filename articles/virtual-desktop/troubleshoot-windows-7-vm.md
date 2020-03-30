---
title: Maszyny wirtualne systemu Windows 7 Pulpit wirtualny systemu Windows — Azure
description: Jak rozwiązać problemy dla maszyn wirtualnych systemu Windows 7 (VM) w środowisku pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127392"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Rozwiązywanie problemów z maszynami wirtualnymi z systemem Windows 7 w usłudze Windows Virtual Desktop

Ten artykuł służy do rozwiązywania problemów z konfigurowaniem maszyn wirtualnych hosta sesji pulpitu wirtualnego systemu Windows.

## <a name="known-issues"></a>Znane problemy

Windows 7 na pulpitach wirtualnych systemu Windows nie obsługuje następujących funkcji:

- Zwirtualizowane aplikacje (RemoteApps)
- Przekierowanie strefy czasowej
- Automatyczne skalowanie DPI

Pulpit wirtualny systemu Windows może wirtualizować tylko pełne pulpity dla systemu Windows 7.

Chociaż automatyczne skalowanie DPI nie jest obsługiwane, można ręcznie zmienić rozdzielczość na maszynie wirtualnej, klikając prawym przyciskiem myszy ikonę na kliencie pulpitu zdalnego i wybierając **opcję Rozdzielczość**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Błąd: nie można uzyskać dostępu do grupy Użytkownicy pulpitu zdalnego

Jeśli pulpit wirtualny systemu Windows nie może znaleźć twoich lub poświadczeń użytkowników w grupie Użytkownicy pulpitu zdalnego, może zostać wyświetlony jeden z następujących komunikatów o błędach:

- "Ten użytkownik nie należy do grupy Użytkownicy pulpitu zdalnego"
- "Musisz mieć uprawnienia do logowania się za pośrednictwem usług pulpitu zdalnego"

Aby naprawić ten błąd, dodaj użytkownika do grupy Użytkownicy pulpitu zdalnego:

1. Otwórz witrynę Azure Portal.
2. Wybierz maszynę wirtualną, na której był wyświetlany komunikat o błędzie.
3. Wybierz **pozycję Uruchom polecenie**.
4. Uruchom następujące polecenie, którego `<username>` nazwa użytkownika ma zostać zastąpiona przez nazwę użytkownika, którego chcesz dodać:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```
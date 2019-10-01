---
title: Rozwiązywanie problemów z maszynami wirtualnymi systemu Windows 7 w programie Virtual Desktop systemu Windows — Azure
description: Jak rozwiązywać problemy dotyczące maszyn wirtualnych z systemem Windows 7 w środowisku pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: 7fdb46ac7cffc4eb6a791304c7a6b58378806296
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679895"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Rozwiązywanie problemów z maszynami wirtualnymi systemu Windows 7 w systemie Windows Virtual Desktop

Ten artykuł służy do rozwiązywania problemów występujących podczas konfigurowania maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows.

## <a name="known-issues"></a>Znane problemy

System Windows 7 na pulpitach wirtualnych systemu Windows nie obsługuje następujących funkcji:

- Zwirtualizowane aplikacje (RemoteApp)
- Przekierowanie strefy czasowej
- Automatyczne skalowanie DPI

Pulpit wirtualny systemu Windows umożliwia wirtualizację pełnych pulpitów dla systemu Windows 7.

Chociaż automatyczne skalowanie DPI nie jest obsługiwane, można ręcznie zmienić rozdzielczość na maszynie wirtualnej, klikając prawym przyciskiem myszy ikonę w Pulpit zdalny klienta i wybierając pozycję **rozdzielczość**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Błąd: nie można uzyskać dostępu do grupy użytkowników Pulpit zdalny

Jeśli pulpit wirtualny systemu Windows nie może odnaleźć użytkownika lub poświadczeń użytkowników w Pulpit zdalny grupie użytkowników, może zostać wyświetlony jeden z następujących komunikatów o błędach:

- "Ten użytkownik nie jest członkiem grupy użytkowników Pulpit zdalny"
- "Musisz mieć przyznane uprawnienia, aby zalogować się za Usługi pulpitu zdalnego"

Aby naprawić ten błąd, Dodaj użytkownika do grupy użytkowników Pulpit zdalny:

1. Otwórz Azure Portal.
2. Wybierz maszynę wirtualną, na której zostanie wyświetlony komunikat o błędzie.
3. Wybierz pozycję **Uruchom polecenie**.
4. Uruchom następujące polecenie z `<username>` zastąpione nazwą użytkownika, który chcesz dodać:
   
   ```cmd
   localgroup "Remote Desktop Users" <username> /add
   ```
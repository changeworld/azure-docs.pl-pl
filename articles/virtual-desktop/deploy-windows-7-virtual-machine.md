---
title: Wdrażanie maszyny wirtualnej systemu Windows 7 Pulpit wirtualny systemu Windows — Azure
description: Jak skonfigurować i wdrożyć maszynę wirtualną systemu Windows 7 na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366687"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Wdrażanie maszyny wirtualnej z systemem Windows 7 w usłudze Windows Virtual Desktop

Proces wdrażania maszyny wirtualnej systemu Windows 7 (VM) na pulpicie wirtualnym systemu Windows jest nieco inny niż w przypadku maszyn wirtualnych z nowszymi wersjami systemu Windows. W tym przewodniku dowiesz się, jak wdrożyć system Windows 7.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy postępować zgodnie z instrukcjami w [programie Utwórz pulę hosta za pomocą programu PowerShell,](create-host-pools-powershell.md) aby utworzyć pulę hosta. Następnie postępuj zgodnie z instrukcjami w [obszarze Tworzenie pul hostów w portalu Azure Marketplace,](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) aby przypisać jednego lub więcej użytkowników do grupy aplikacji klasycznych.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurowanie maszyny wirtualnej systemu Windows 7

Po wykonaniu wymagań wstępnych można skonfigurować maszynę wirtualną systemu Windows 7 do wdrożenia na pulpicie wirtualnym systemu Windows.

Aby skonfigurować maszynę wirtualną systemu Windows 7 na pulpicie wirtualnym systemu Windows:

1. Zaloguj się do witryny Azure portal i wyszukaj obraz systemu Windows 7 Enterprise lub przekaż własny dostosowany obraz systemu Windows 7 Enterprise (x64).  
2. Wdrażaj jedną lub wiele maszyn wirtualnych z systemem Windows 7 Enterprise jako systemem operacyjnym hosta. Upewnij się, że maszyny wirtualne zezwalają na protokół RDP (Remote Desktop Protocol) (port TCP/3389).
3. Połącz się z hostem windows 7 Enterprise przy użyciu protokołu RDP i uwierzytelnij się przy użyciu poświadczeń zdefiniowanych podczas konfigurowania wdrożenia. 
4. Dodaj konto używane podczas łączenia się z hostem z protokołu RDP do grupy "Użytkownik pulpitu zdalnego". Jeśli tego nie zrobisz, połączenie z maszyną wirtualną może nie być możliwe po dołączeniu jej do domeny usługi Active Directory.
5. Przejdź do witryny Windows Update na maszynie Wirtualnej.
6. Zainstaluj wszystkie aktualizacje systemu Windows w kategorii Ważne.
7. Zainstaluj wszystkie aktualizacje systemu Windows w kategorii Opcjonalne (z wyłączeniem pakietów językowych). Spowoduje to zainstalowanie aktualizacji protokołu 8.0 usługi Pulpitu zdalnego[(KB2592687),](https://www.microsoft.com/download/details.aspx?id=35387)która jest wymagana do wykonania tych instrukcji.
8. Otwórz Edytor zasad grupy lokalnej i przejdź do pozycji**Szablony administracyjne** >  **konfiguracji** > komputera**Składniki pulpitu** > **zdalnego usługi** > pulpitu zdalnego Usługi**pulpitu** > zdalnego Host**sesji Remote Session Environment**.
9. Włącz zasady protokołu 8.0 usług pulpitu zdalnego.
10. Dołącz tę maszynę wirtualną do domeny usługi Active Directory.
11. Uruchom ponownie maszynę wirtualną, uruchamiając następujące polecenie:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Postępuj zgodnie z instrukcjami [tutaj,](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) aby uzyskać token rejestracji.
13. [Pobierz agenta pulpitu wirtualnego systemu Windows dla systemu Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Pobierz Menedżera agentów pulpitu wirtualnego systemu Windows dla systemu Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Otwórz instalator programu Windows Virtual Desktop Agent i postępuj zgodnie z instrukcjami. Po wyświetleniu monitu podaj klucz rejestracji utworzony w kroku 12.
16. Otwórz instalator pulpitu wirtualnego systemu Windows i postępuj zgodnie z instrukcjami.
17. Opcjonalnie zablokuj port TCP/3389, aby usunąć bezpośredni dostęp protokołu pulpitu zdalnego do maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

Wdrożenie pulpitu wirtualnego systemu Windows jest teraz gotowe do użycia. [Aby rozpocząć, pobierz najnowszą wersję klienta pulpitu wirtualnego systemu Windows.](https://aka.ms/wvd/clients/windows)

Aby uzyskać listę znanych problemów i instrukcji rozwiązywania problemów dla systemu Windows 7 na pulpicie wirtualnym systemu Windows, zobacz nasz artykuł dotyczący rozwiązywania problemów z [maszynami wirtualnymi systemu Windows 7 na pulpicie wirtualnym systemu Windows](troubleshoot-windows-7-vm.md).

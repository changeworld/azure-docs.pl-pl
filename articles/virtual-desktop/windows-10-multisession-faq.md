---
title: Często zadawane pytania dotyczące wielu sesji systemu Windows 10 Enterprise — Azure
description: Często zadawane pytania i najlepsze rozwiązania dotyczące korzystania z wielu sesji systemu Windows 10 Enterprise dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4301aaed0152e07eb2a941b56637239b66c33878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127355"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Wiele sesji systemu Windows 10 Enterprise — często zadawane pytania

W tym artykule zawiera odpowiedzi na często zadawane pytania i wyjaśniono najlepsze rozwiązania dotyczące wielu sesji systemu Windows 10 Enterprise.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Co to jest windows 10 Enterprise multi-session?

Windows 10 Enterprise multi-session, dawniej znany jako Windows 10 Enterprise for Virtual Desktops (EVD), to nowy host sesji pulpitu zdalnego, który umożliwia wiele równoczesnych sesji interaktywnych. Wcześniej tylko system Windows Server mógł to zrobić. Ta funkcja zapewnia użytkownikom znane środowisko systemu Windows 10, podczas gdy it może korzystać z zalet kosztów wielu sesji i korzystać z istniejących licencji dla systemu Windows dla użytkownika zamiast licencji dostępu klienta usług pulpitu zdalnego (CAL). Aby uzyskać więcej informacji na temat licencji i cen, zobacz [Ceny pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Ilu użytkowników może jednocześnie mieć interaktywną sesję w systemie Windows 10 Enterprise w wielu sesjach?

Liczba sesji interaktywnych, które mogą być aktywne w tym samym czasie, zależy od zasobów sprzętowych systemu (vCPU, pamięci, dysku i vGPU), sposobu, w jaki użytkownicy korzystają z aplikacji po zalogowaniu się do sesji i jak duże jest obciążenie systemu. Zalecamy sprawdzenie poprawności wydajności systemu, aby dowiedzieć się, ilu użytkowników możesz mieć w wielu sesjach systemu Windows 10 Enterprise. Aby dowiedzieć się więcej, zobacz [Ceny pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Dlaczego moja aplikacja raportuje windows 10 Enterprise multi-session jako system operacyjny Server?

Windows 10 Enterprise multi-session to wirtualna wersja systemu Windows 10 Enterprise. Jedną z różnic jest to, że ten system operacyjny (OS) zgłasza [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) jako o wartości 3, taką samą wartość jak Windows Server. Ta właściwość utrzymuje system operacyjny zgodny z istniejącymi narzędziami do zarządzania RDSH, aplikacjami obsługującymi wiele sesji RDSH i głównie optymalizacjami wydajności systemu niskiego poziomu dla środowisk RDSH. Niektóre instalatory aplikacji mogą blokować instalację w systemie Windows 10 w wielu sesjach w zależności od tego, czy wykrywają ProductType jest ustawiona na Klienta. Jeśli aplikacja nie zostanie zainstalowana, skontaktuj się z dostawcą aplikacji, aby uzyskać zaktualizowaną wersję. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Czy mogę uruchamiać w środowisku lokalnym system Windows 10 Enterprise wielosesyjny?

Windows 10 Enterprise multi-session nie można uruchomić w lokalnych środowiskach produkcyjnych, ponieważ jest zoptymalizowany pod kątem usługi pulpitu wirtualnego systemu Windows dla platformy Azure. Jest to sprzeczne z umową licencyjną, aby uruchomić windows 10 Enterprise multi-session poza platformą Azure do celów produkcyjnych. Wielosesyjna sesja systemu Windows 10 Enterprise nie zostanie aktywowana w lokalnych usługach zarządzania kluczami (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Jak dostosować obraz wielosesyjny systemu Windows 10 Enterprise dla mojej organizacji?

Maszynę wirtualną (VM) można uruchomić na platformie Azure za pomocą systemu Windows 10 Windows 10 Enterprise i dostosować ją, instalując aplikacje LOB, sysprep/generalize, a następnie utworzyć obraz za pomocą portalu Azure.  
 
Aby rozpocząć, utwórz maszynę wirtualną na platformie Azure z systemem Windows 10 Windows 10 Enterprise multi-session. Zamiast uruchamiania maszyny Wirtualnej na platformie Azure, można pobrać wirtualną wersję dysku twardego bezpośrednio. Następnie możesz użyć pobranego dysku VHD, aby utworzyć nową maszynę wirtualną generacji 1 na komputerze z systemem Windows 10 z włączoną funkcją Hyper-V.

Dostosuj obraz do swoich potrzeb, instalując aplikacje LOB i sysprep obrazu. Po zakończeniu dostosowywania przekaż obraz na platformę Azure z wirtualnym dyskiem wirtualnym wewnątrz. Następnie pobierz pulpit wirtualny systemu Windows z portalu Azure Marketplace i użyj go do wdrożenia nowej puli hostów z obrazem dostosowanym.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Jak zarządzać wieloma sesjami systemu Windows 10 Enterprise po wdrożeniu?

Można użyć dowolnego obsługiwanego narzędzia konfiguracyjnego, ale zalecamy program Menedżer konfiguracji w wersji 1906, ponieważ obsługuje on wiele sesji systemu Windows 10 Enterprise. Obecnie pracujemy nad pomocą techniczną usługi Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Czy system Windows 10 Enterprise może być przyłączony do usługi Azure Active Directory (AD)?

Windows 10 Enterprise multi-session jest obecnie obsługiwany jako hybrydowy azure ad-joined. Po przyłączeniu do wielu sesji systemu Windows 10 Enterprise użyj istniejącego obiektu zasad grupy, aby włączyć rejestrację usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Planowanie implementacji hybrydowego dołączania usługi Azure Active Directory.](../active-directory/devices/hybrid-azuread-join-plan.md)
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Gdzie można znaleźć obraz wielosesyjny systemu Windows 10 Enterprise?

Windows 10 Enterprise multi-session znajduje się w galerii platformy Azure. Aby go znaleźć, przejdź do witryny Azure portal i wyszukaj wersję Windows 10 Enterprise for Virtual Desktops. Aby uzyskać obraz zintegrowany z pakietem Office Pro Plus, przejdź do portalu Azure i wyszukaj system Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Którego obrazu wielosesyjnego systemu Windows 10 Enterprise należy użyć?

Galeria platformy Azure zawiera kilka wersji, w tym windows 10 Enterprise multi-session, wersja 1809 i Windows 10 Enterprise multi-session, wersja 1903. Zalecamy użycie najnowszej wersji w celu zwiększenia wydajności i niezawodności.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Które wersje wielosesyjne dla systemu Windows 10 Enterprise są obsługiwane?

Windows 10 Enterprise multi-session, wersje 1809 i nowsze są obsługiwane i są dostępne w galerii platformy Azure. Te wersje są zgodne z tą samą zasadą pomocy technicznej co windows 10 Enterprise, co oznacza, że wiosenna wersja jest obsługiwana przez 18 miesięcy, a wersja jesienna przez 30 miesięcy.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Którego rozwiązania do zarządzania profilami należy używać w systemie Windows 10 Enterprise w wielu sesjach?

Zaleca się używanie kontenerów profilu FSLogix podczas konfigurowania systemu Windows 10 Enterprise w środowiskach nietrwałych lub innych scenariuszach, które wymagają profilu przechowywanego centralnie. FSLogix zapewnia, że profil użytkownika jest dostępny i aktualny dla każdej sesji użytkownika. Firma Microsoft zaleca również użycie kontenera profilu FSLogix do przechowywania profilu użytkownika w dowolnym udziale SMB z odpowiednimi uprawnieniami, ale w razie potrzeby można przechowywać profile użytkowników w magazynie obiektów blob strony platformy Azure. Użytkownicy pulpitów wirtualnych systemu Windows mogą korzystać z FSLogix bez dodatkowych kosztów.
 
Aby uzyskać więcej informacji na temat konfigurowania kontenera profilu FSLogix, zobacz [Konfigurowanie kontenera profilu FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Której licencji potrzebuję, aby uzyskać dostęp do wielu sesji systemu Windows 10 Enterprise?

Aby uzyskać pełną listę odpowiednich licencji, zobacz [Ceny pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Dlaczego moje aplikacje znikają po wylogowanie?

Dzieje się tak, ponieważ używasz systemu Windows 10 Enterprise multi-session z rozwiązaniem do zarządzania profilem, takim jak FSLogix. Rozwiązanie administratora lub profilu skonfigurowało system do usuwania profilów użytkowników po wylogowywania się użytkowników. Ta konfiguracja oznacza, że gdy system usuwa profil użytkownika po wylogowywania się, usuwa również wszystkie aplikacje zainstalowane podczas sesji. Jeśli chcesz zachować zainstalowane aplikacje, musisz poprosić administratora o udostępnienie tych aplikacji wszystkim użytkownikom w środowisku pulpitu wirtualnego systemu Windows.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Jak upewnić się, że aplikacje nie znikają po wylogowanie się użytkowników?

Większość zwirtualizowanych środowisk jest domyślnie skonfigurowana, aby uniemożliwić użytkownikom instalowanie dodatkowych aplikacji w swoich profilach. Jeśli chcesz upewnić się, że aplikacja nie zniknie, gdy użytkownik wyloguje się z pulpitu wirtualnego systemu Windows, musisz udostępnić tę aplikację dla wszystkich profili użytkowników w twoim środowisku. Aby uzyskać więcej informacji na temat inicjowania obsługi administracyjnej aplikacji, zapoznaj się z tymi zasobami:

- [Publikowanie wbudowanych aplikacji na pulpicie wirtualnym systemu Windows](publish-apps.md)
- [Opcje wiersza wiersza polecenia obsługi pakietu aplikacji DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Pakiet Add-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Jak upewnić się, że użytkownicy nie pobierają i nie instalują aplikacji ze sklepu Microsoft Store?

Możesz wyłączyć aplikację Ze sklepu Microsoft Store, aby upewnić się, że użytkownicy nie pobierają dodatkowych aplikacji poza aplikacjami, które zostały już dla nich aprowizowane.

Aby wyłączyć aplikację Ze Sklepu:

1. Utwórz nową zasadę grupy.
2. Wybierz pozycję**Szablony administracyjne** >  **konfiguracji** > komputera**Składniki systemu Windows**.
3. Wybierz **pozycję Sklep**.
4. Wybierz **aplikację sklepu**.
5. Wybierz **wyłączone**, a następnie wybierz **przycisk OK**.
6. Wybierz przycisk **Zastosuj**.
 
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o pulpitach wirtualnych systemu Windows i wielu sesjach systemu Windows 10 Enterprise:

- Przeczytaj naszą [dokumentację podglądu pulpitu wirtualnego systemu Windows](overview.md)
- Odwiedź naszą [maszynę do pracy w systemie Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Konfigurowanie wdrożenia pulpitu wirtualnego systemu Windows za pomocą [samouczków pulpitu wirtualnego systemu Windows](tenant-setup-azure-active-directory.md)

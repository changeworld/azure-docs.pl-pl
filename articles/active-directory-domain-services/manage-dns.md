---
title: Zarządzanie DNS dla usług domenowych Azure AD | Dokumentacja firmy Microsoft
description: Zarządzanie systemem DNS dla usługi Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 3c844b6ede63b2e036f05b5b7f6cb3803eb4b47c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245882"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrowanie systemem DNS w domenie zarządzanej usług domenowych Azure AD
Usługa Azure Active Directory Domain Services zawiera serwer DNS (rozpoznawanie nazw domen), która umożliwia rozpoznawanie nazw DNS dla domeny zarządzanej. Od czasu do czasu może być konieczne skonfigurowanie DNS w domenie zarządzanej. Może być konieczne, Utwórz rekordy DNS dla maszyn, które nie są przyłączone do domeny, skonfigurować wirtualne adresy IP usługi równoważenia obciążenia lub skonfigurować zewnętrznego usług przesyłania dalej DNS. Z tego powodu użytkownicy, którzy należą do grupy "Administratorzy usługi AAD DC" otrzymują uprawnienia do administrowania DNS w domenie zarządzanej.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:

1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).
4. A **przyłączone do domeny maszyny wirtualnej** z którego administrowanie domeną zarządzaną usług domenowych Azure AD. Jeśli nie masz maszynę wirtualną, wykonaj wszystkie zadania, które są opisane w artykule o nazwie [Dołącz maszynę wirtualną Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Musisz mieć poświadczenia z **konta użytkownika należącego do grupy "Administratorzy usługi AAD DC"** w katalogu, do administrowania systemem DNS dla domeny zarządzanej.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Zadanie 1 — Tworzenie maszyny wirtualnej z przyłączonym do domeny na zdalne administrowanie systemem DNS dla domeny zarządzanej
Usługa Azure AD Domain Services domeny zarządzane mogą być zarządzane zdalnie przy użyciu znanych narzędzi administracyjnych usługi Active Directory, takie jak Active Directory administracyjne Center (ADAC) lub programu PowerShell usługi AD. Podobnie serwera DNS dla domeny zarządzanej można administrować zdalnie za pomocą narzędzia administracji serwera DNS.

Administratorzy w katalogu usługi Azure AD nie mają uprawnień do połączenia z kontrolerami domeny w domenie zarządzanej za pośrednictwem pulpitu zdalnego. Członkowie grupy "Administratorzy usługi AAD DC" mogą administrować DNS dla domeny zarządzane zdalnie za pomocą narzędzia serwera DNS z komputera systemu Windows Server/kliencie, który jest przyłączony do domeny zarządzanej. Narzędzia serwera DNS są częścią opcjonalna funkcja narzędzi administracji zdalnej serwera (RSAT).

Pierwszym zadaniem jest tworzenie maszyny wirtualnej systemu Windows Server, który jest przyłączony do domeny zarządzanej. Instrukcje można znaleźć w artykule o nazwie [dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Zadanie 2 — narzędzia do instalacji serwera DNS na maszynie wirtualnej
Wykonaj poniższe kroki, aby zainstalować narzędzia do administrowania systemem DNS na maszynie wirtualnej przyłączonych do domeny. Aby uzyskać więcej informacji na temat [instalowaniu i używaniu narzędzi administracji zdalnej serwera](https://technet.microsoft.com/library/hh831501.aspx), zobacz temat Technet.

1. Przejdź do witryny Azure portal. Kliknij przycisk **wszystkie zasoby** w panelu po lewej stronie. Zlokalizuj i kliknij maszynę wirtualną, którą utworzono w zadaniu 1.
2. Kliknij przycisk **Connect** przycisku na karcie Przegląd. Plik Remote Desktop Protocol (RDP), zostanie utworzony i pobrany.

    ![Łączenie z maszyną wirtualną Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. Użyj poświadczeń użytkownika należącego do grupy "Administratorzy usługi AAD DC". Na przykład "bob@domainservicespreview.onmicrosoft.com". Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk Tak, lub w dalszym ciągu połączenia.

4. Na ekranie startowym Otwórz **Menedżera serwera**. Kliknij przycisk **Dodaj role i funkcje** w środkowym okienkiem okna Menedżera serwera.

    ![Uruchamianie Menedżera serwera na maszynie wirtualnej](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **przed rozpoczęciem** strony **Kreatora dodawania ról i funkcji**, kliknij przycisk **dalej**.

    ![Zanim rozpoczniesz](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typu instalacji** zostaw **Instalacja oparta na rolach lub oparta na funkcjach** zaznaczoną opcją i kliknij przycisk **dalej**.

    ![Strona typu instalacji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **wybór dotyczący serwera** strony, wybierz bieżącej maszyny wirtualnej z puli serwerów, a następnie kliknij przycisk **dalej**.

    ![Strona wybierania serwera](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **ról serwera** kliknij **dalej**.
9. Na **funkcji** strony, kliknij, aby rozwinąć **narzędzia administracji zdalnej serwera** węzła a następnie kliknij przycisk, aby rozwinąć **narzędzia do administrowania rolami** węzła. Wybierz **narzędzia serwera DNS** funkcję z listy narzędzia do administrowania rolami.

    ![Strona funkcje](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Na **potwierdzenie** kliknij **zainstalować** do zainstalowania funkcji narzędzia serwera DNS na maszynie wirtualnej. Po pomyślnym ukończeniu instalacji funkcji kliknij przycisk **Zamknij** aby zakończyć działanie **Dodaj role i funkcje** kreatora.

    ![Strona potwierdzenia](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Zadanie 3 — uruchamianie konsoli zarządzania systemem DNS do administrowania systemem DNS
Teraz można użyć narzędzia DNS systemu Windows Server do administrowania systemem DNS w domenie zarządzanej.

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy usługi AAD DC" do administrowania systemem DNS w domenie zarządzanej.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Powinien zostać wyświetlony **DNS** Konsola zainstalowana na maszynie wirtualnej.

    ![Narzędzia administracyjne - konsoli DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Kliknij przycisk **DNS** można uruchomić konsoli zarządzania usługą DNS.
3. W **Połącz z serwerem DNS** okno dialogowe, kliknij przycisk **następujący komputer**, a następnie wprowadź nazwę domeny DNS z domeny zarządzanej (na przykład "contoso100.com").

    ![Konsolę DNS — Połącz się z domeną](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. Łączy konsoli DNS do domeny zarządzanej.

    ![Konsolę DNS — administrowanie domeną](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Można teraz używać konsoli DNS można dodać wpisy DNS dla komputerów w sieci wirtualnej, w której włączono usługi AAD Domain Services.

> [!WARNING]
> Należy zachować ostrożność, administrowanie systemem DNS dla domeny zarządzanej przy użyciu narzędzi administracyjnych DNS. Upewnij się, że **nieusuwanie i niemodyfikowanie wbudowanych rekordów DNS, które są używane przez usługi domenowe w domenie**. Wbudowane rekordy DNS obejmują rekordy serwera nazw, inne rekordy użycia dla lokalizacji kontrolera domeny i rekordów DNS domeny. Jeśli zmodyfikujesz te rekordy, usługi domenowe są zakłócone w sieci wirtualnej.
>
>

Aby uzyskać więcej informacji o zarządzaniu DNS, zobacz [narzędzia DNS artykuł w witrynie Technet](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](create-instance.md)
* [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Zarządzaj domeną usługi Azure AD Domain Services](manage-domain.md)
* [Narzędzia administracji systemu DNS](https://technet.microsoft.com/library/cc753579.aspx)

---
title: 'Azure Active Directory Domain Services: Administrowanie zasadami grupy w domenach zarządzanych | Dokumentacja firmy Microsoft'
description: Domen zarządzanych przez zasady grupy administrowania w usłudze Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: deaa6a9ff49fea2093c94a039aab0d7de0c95359
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418071"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrowanie zasadami grupy w domenie zarządzanej usług domenowych Azure AD
Usługa Azure Active Directory Domain Services zawiera wbudowane obiekty zasad grupy (GPO) dla kontenerów "Użytkownicy usługi AAD DC" i "Komputery usługi AAD DC". Można dostosować te wbudowane obiekty zasad grupy do skonfigurowania zasad grupy w domenie zarządzanej. Ponadto członkowie grupy "Administratorzy usługi AAD DC" mogą tworzyć własnych niestandardowych jednostkach organizacyjnych w domenie zarządzanej. Mogą także tworzyć niestandardowe obiekty zasad grupy i łączą je z tych niestandardowych jednostkach organizacyjnych. Użytkownicy, którzy należą do grupy "Administratorzy usługi AAD DC" otrzymują uprawnienia administracyjne zasad grupy w domenie zarządzanej.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:

1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](active-directory-ds-getting-started.md).
4. A **przyłączone do domeny maszyny wirtualnej** z którego administrowanie domeną zarządzaną usług domenowych Azure AD. Jeśli nie masz maszynę wirtualną, wykonaj wszystkie zadania, które są opisane w artykule o nazwie [Dołącz maszynę wirtualną Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Musisz mieć poświadczenia z **konta użytkownika należącego do grupy "Administratorzy usługi AAD DC"** w katalogu, administrowanie zasadami grupy dla domeny zarządzanej.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Zadanie 1 — Aprowizowanie maszyny wirtualnej z przyłączonym do domeny zdalne administrowanie zasad grupy dla domeny zarządzanej
Usługa Azure AD Domain Services domeny zarządzane mogą być zarządzane zdalnie przy użyciu znanych narzędzi administracyjnych usługi Active Directory, takie jak Active Directory administracyjne Center (ADAC) lub programu PowerShell usługi AD. Podobnie zasady grupy dla domeny zarządzanej można administrować zdalnie za pomocą narzędzi administracyjnych zasad grupy.

Administratorzy w katalogu usługi Azure AD nie mają uprawnień do połączenia z kontrolerami domeny w domenie zarządzanej za pośrednictwem pulpitu zdalnego. Członkowie grupy "Administratorzy usługi AAD DC" można zasad grupy dla domeny zarządzane zdalnie administrować. Mogą oni za pomocą narzędzi zasad grupy na komputerze serwera/klienta systemu Windows przyłączone do domeny zarządzanej. Narzędzia zasad grupy można zainstalować jako część zarządzania zasadami grupy, opcjonalna funkcja w systemie Windows Server i komputerów klienckich, przyłączone do domeny zarządzanej.

Pierwsze zadanie jest aprowizowanie maszyny wirtualnej systemu Windows Server, który jest przyłączony do domeny zarządzanej. Instrukcje można znaleźć w artykule o nazwie [dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Zadanie 2 — narzędzi zasad grupy do instalacji na maszynie wirtualnej
Wykonaj poniższe kroki, aby zainstalować narzędzia administracyjne zasad grupy na maszynie wirtualnej przyłączonych do domeny.

1. Przejdź do witryny Azure portal. Kliknij przycisk **wszystkie zasoby** w panelu po lewej stronie. Zlokalizuj i kliknij maszynę wirtualną, którą utworzono w zadaniu 1.
2. Kliknij przycisk **Connect** przycisku na karcie Przegląd. Plik Remote Desktop Protocol (RDP), zostanie utworzony i pobrany.

    ![Łączenie z maszyną wirtualną Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. W monicie o zalogowanie Użyj poświadczeń użytkownika należącego do grupy "Administratorzy usługi AAD DC". Na przykład, możemy użyć "bob@domainservicespreview.onmicrosoft.com" w tym przypadku. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk Tak, lub Kontynuuj nawiązać połączenie.
4. Na ekranie startowym Otwórz **Menedżera serwera**. Kliknij przycisk **Dodaj role i funkcje** w środkowym okienkiem okna Menedżera serwera.

    ![Uruchamianie Menedżera serwera na maszynie wirtualnej](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **przed rozpoczęciem** strony **Kreatora dodawania ról i funkcji**, kliknij przycisk **dalej**.

    ![Zanim rozpoczniesz](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typu instalacji** zostaw **Instalacja oparta na rolach lub oparta na funkcjach** zaznaczoną opcją i kliknij przycisk **dalej**.

    ![Strona typu instalacji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **wybór dotyczący serwera** strony, wybierz bieżącej maszyny wirtualnej z puli serwerów, a następnie kliknij przycisk **dalej**.

    ![Strona wybierania serwera](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **ról serwera** kliknij **dalej**. Ponieważ firma Microsoft nie instalowania wszystkich ról na serwerze, Pomiń tę stronę.
9. Na **funkcji** wybierz opcję **Zarządzanie zasadami grupy** funkcji.

    ![Strona funkcje](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na **potwierdzenie** kliknij **zainstalować** można zainstalować funkcję Zarządzanie zasadami grupy na maszynie wirtualnej. Po pomyślnym ukończeniu instalacji funkcji kliknij przycisk **Zamknij** aby zakończyć działanie **Dodaj role i funkcje** kreatora.

    ![Strona potwierdzenia](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Zadanie 3 — uruchamianie konsoli zarządzania zasadami grupy do administrowania zasad grupy
Konsola zarządzania zasadami grupy na maszynie wirtualnej przyłączone do domeny umożliwia administrowanie zasadami grupy w domenie zarządzanej.

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy usługi AAD DC" administrowanie zasadami grupy w domenie zarządzanej.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Powinien zostać wyświetlony **Zarządzanie zasadami grupy** Konsola zainstalowana na maszynie wirtualnej.

    ![Zarządzanie zasadami grupy uruchamiania](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Kliknij przycisk **Zarządzanie zasadami grupy** można uruchomić konsoli zarządzania zasadami grupy.

    ![Konsola zasad grupy](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Zadanie 4 — Dostosowywanie wbudowane obiekty zasad grupy
Istnieją dwa wbudowane obiekty zasad grupy (GPO) — jeden dla kontenerów "Komputery usługi AAD DC" i "Użytkownicy usługi AAD DC" w domenie zarządzanej. Można dostosować te obiekty zasad grupy, aby skonfigurować zasady grupy w domenie zarządzanej.

1. W **Zarządzanie zasadami grupy** konsoli, kliknij, aby rozwinąć **lasu: contoso100.com** i **domen** węzłów, aby zobaczyć zasad grupy dla domeny zarządzanej.

    ![Wbudowane obiekty zasad grupy](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Można dostosować te wbudowane obiekty zasad grupy do konfigurowania zasad grupy w domenie zarządzanej. Kliknij prawym przyciskiem myszy obiekt zasad grupy, a następnie kliknij przycisk **Edytuj...**  dostosować wbudowanego obiektu zasad grupy. Narzędzie edytora konfiguracji zasad grupy umożliwia dostosowanie obiektu zasad grupy.

    ![Edytuj wbudowanego obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Teraz możesz używać **Edytor zarządzania zasadami grupy** konsoli do edytowania wbudowanego obiektu zasad grupy. Na przykład poniższy zrzut ekranu pokazuje, jak dostosować wbudowanego obiektu zasad grupy "Komputery usługi AAD DC".

    ![Dostosowywanie obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Zadanie 5 — Tworzenie niestandardowego obiektu zasad grupy (GPO)
Można utworzyć lub zaimportować własne obiekty zasad grupy niestandardowe. Możesz również połączyć niestandardowych obiektów zasad grupy, do niestandardowej jednostki Organizacyjnej, utworzonych przez siebie w Twojej domeny zarządzanej. Aby uzyskać więcej informacji na temat tworzenia niestandardowych jednostkach organizacyjnych, zobacz [Tworzenie niestandardowej jednostki Organizacyjnej w domenie zarządzanej](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy usługi AAD DC" administrowanie zasadami grupy w domenie zarządzanej.
>
>

1. W **Zarządzanie zasadami grupy** konsoli, kliknij, aby wybrać niestandardowy jednostkę organizacyjną (OU). Kliknij prawym przyciskiem myszy jednostkę Organizacyjną, a następnie kliknij przycisk **Utwórz obiekt GPO w tej domenie i umieść tu łącze...** .

    ![Utworzenie niestandardowego obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Podaj nazwę nowego obiektu zasad grupy, a następnie kliknij przycisk **OK**.

    ![Określ nazwę dla obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Nowy obiekt zasad grupy jest utworzony i połączony z niestandardowej jednostki Organizacyjnej. Kliknij prawym przyciskiem myszy obiekt zasad grupy, a następnie kliknij przycisk **Edytuj...**  z menu.

    ![Nowo utworzony obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Można dostosować do nowo utworzonego obiektu zasad grupy przy użyciu **Edytor zarządzania zasadami grupy**.

    ![Dostosuj nowy obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Więcej informacji o używaniu [konsoli zarządzania zasadami grupy](https://technet.microsoft.com/library/cc753298.aspx) jest dostępna w witrynie Technet.

## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](active-directory-ds-getting-started.md)
* [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrowanie domeną zarządzaną usług Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Konsola zarządzania zasadami grupy](https://technet.microsoft.com/library/cc753298.aspx)

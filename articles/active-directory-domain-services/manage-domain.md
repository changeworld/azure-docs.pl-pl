---
title: 'Azure Active Directory Domain Services: Administrowanie domeną zarządzaną | Dokumentacja firmy Microsoft'
description: Administrowanie domen zarządzanych w usłudze Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4095161cd35ff610b357196c91e71256a8bc0263
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473021"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrowanie domeną zarządzaną usług domenowych Azure Active Directory
W tym artykule pokazano, jak administrowanie domeną zarządzaną usług domenowych Azure Active Directory (AD).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:

1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).
4. A **przyłączone do domeny maszyny wirtualnej** z którego administrowanie domeną zarządzaną usług domenowych Azure AD. Jeśli nie masz maszynę wirtualną, wykonaj wszystkie zadania, które są opisane w artykule o nazwie [Dołącz maszynę wirtualną Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Musisz mieć poświadczenia z **konta użytkownika należącego do grupy "Administratorzy usługi AAD DC"** w katalogu, Administruj swoją domeną zarządzaną.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Zadania administracyjne, które można wykonywać w domenie zarządzanej
Członkowie grupy "Administratorzy usługi AAD DC" otrzymują uprawnienia w domenie zarządzanej, umożliwiające wykonywanie zadań takich jak:

* Przyłączanie maszyn do domeny zarządzanej.
* Konfigurowanie wbudowanego obiektu zasad grupy dla kontenerów „Komputery usługi AAD DC” i „Użytkownicy usługi AAD DC” w domenie zarządzanej.
* Administrowanie systemem DNS w domenie zarządzanej.
* Tworzyć i administrować niestandardowe jednostki organizacyjne (OU) w domenie zarządzanej.
* Uzyskiwanie dostępu administracyjnego do komputerów przyłączonych do domeny zarządzanej.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Uprawnienia administracyjne, które nie znajdują się w domenie zarządzanej
Domena jest zarządzany przez firmę Microsoft z uwzględnieniem działań, takich jak stosowanie poprawek i monitorowania oraz wykonywania kopii zapasowych. Domena jest zablokowana i nie masz uprawnień do wykonywania określonych zadań administracyjnych w domenie. Przykłady zadań, które nie są poniżej.

* Nie masz uprawnienia administratora domeny lub administratora przedsiębiorstwa dla domeny zarządzanej.
* Nie można rozszerzyć schemat domeny zarządzanej.
* Nie można połączyć się z kontrolerami domeny dla domeny zarządzanej przy użyciu pulpitu zdalnego.
* Nie można dodać kontrolerów domeny do domeny zarządzanej.

## <a name="task-1---create-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Zadanie 1 — Tworzenie maszyny wirtualnej systemu Windows Server przyłączone do domeny zdalne administrowanie domeną zarządzaną
Usługa Azure AD Domain Services domeny zarządzane mogą być zarządzane przy użyciu znanych narzędzi administracyjnych usługi Active Directory, takie jak Active Directory administracyjne Center (ADAC) lub programu PowerShell usługi AD. Administratorzy dzierżawy nie mają uprawnień do połączenia z kontrolerami domeny w domenie zarządzanej za pośrednictwem pulpitu zdalnego. Członkowie grupy "Administratorzy usługi AAD DC" mogą administrować domeny zarządzane zdalnie za pomocą narzędzia administracyjnego usług AD z komputera systemu Windows Server/kliencie, który jest przyłączony do domeny zarządzanej. Narzędzia administracyjne AD można zainstalować jako część narzędzi administracji zdalnej serwera (RSAT), opcjonalna funkcja w systemie Windows Server i komputerów klienckich, przyłączone do domeny zarządzanej.

Pierwszym krokiem jest, aby skonfigurować maszynę wirtualną systemu Windows Server, który jest przyłączony do domeny zarządzanej. Instrukcje można znaleźć w artykule o nazwie [dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Zdalne administrowanie domeną zarządzaną z poziomu komputera klienckiego (na przykład system Windows 10)
Domeny zarządzanej przez instrukcje w tym artykule maszynę wirtualną systemu Windows Server do administrowania DS usługi AAD. Jednak możesz również używać maszynę wirtualną Windows klienta (na przykład system Windows 10), aby to zrobić.

Możesz [zainstalować narzędzia administracji zdalnej serwera (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) na maszynie wirtualnej klienta Windows, postępując zgodnie z instrukcjami w witrynie TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Zadanie 2 — narzędzia administracyjne usługi Active Directory w instalacji na maszynie wirtualnej
Wykonaj poniższe kroki, aby zainstalować narzędzia Administrowanie usługą Active Directory na maszynie wirtualnej przyłączonych do domeny. Aby uzyskać więcej informacji zobacz temat Technet [informacji o instalowaniu i używaniu narzędzi administracji zdalnej serwera](https://technet.microsoft.com/library/hh831501.aspx).

1. Przejdź do witryny Azure portal. Kliknij przycisk **wszystkie zasoby** w panelu po lewej stronie. Zlokalizuj i kliknij maszynę wirtualną, którą utworzono w zadaniu 1.
2. Kliknij przycisk **Connect** przycisku na karcie Przegląd. Plik Remote Desktop Protocol (RDP), zostanie utworzony i pobrany.

    ![Łączenie z maszyną wirtualną Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. Użyj poświadczeń użytkownika należącego do grupy "Administratorzy usługi AAD DC". Na przykład "bob@domainservicespreview.onmicrosoft.com". Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk Tak, lub Kontynuuj nawiązać połączenie.
4. Na ekranie startowym Otwórz **Menedżera serwera**. Kliknij przycisk **Dodaj role i funkcje** w środkowym okienkiem okna Menedżera serwera.

    ![Uruchamianie Menedżera serwera na maszynie wirtualnej](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **przed rozpoczęciem** strony **Kreatora dodawania ról i funkcji**, kliknij przycisk **dalej**.

    ![Zanim rozpoczniesz](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typu instalacji** zostaw **Instalacja oparta na rolach lub oparta na funkcjach** zaznaczoną opcją i kliknij przycisk **dalej**.

    ![Strona typu instalacji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **wybór dotyczący serwera** strony, wybierz bieżącej maszyny wirtualnej z puli serwerów, a następnie kliknij przycisk **dalej**.

    ![Strona wybierania serwera](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **ról serwera** kliknij **dalej**.
9. Na **funkcji** strony, kliknij, aby rozwinąć **narzędzia administracji zdalnej serwera** węzła a następnie kliknij przycisk, aby rozwinąć **narzędzia do administrowania rolami** węzła. Wybierz **usług AD DS i AD LDS narzędzia** funkcję z listy narzędzia do administrowania rolami.

    ![Strona funkcje](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Na **potwierdzenie** kliknij **zainstalować** do instalacji usług AD i AD LDS narzędzia funkcji na maszynie wirtualnej. Po pomyślnym ukończeniu instalacji funkcji kliknij przycisk **Zamknij** aby zakończyć działanie **Dodaj role i funkcje** kreatora.

    ![Strona potwierdzenia](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Zadanie 3 — nawiązywanie połączenia i zapoznaj się z domeną zarządzaną
Teraz można użyć narzędzia administracyjne systemu Windows Server AD, aby eksplorować i administrowanie domeną zarządzaną.

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy usługi AAD DC" administrowanie domeną zarządzaną.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Powinieneś zobaczyć AD narzędzia administracyjne zainstalowane na maszynie wirtualnej.

    ![Narzędzia administracyjne zainstalowane na serwerze](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kliknij przycisk **Centrum administracyjne usługi Active Directory**.

    ![Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Aby zapoznać się z domeny, kliknij nazwę domeny w okienku po lewej stronie (na przykład "contoso100.com"). Należy zauważyć dwa kontenery odpowiednio o nazwie "Komputery usługi AAD DC" i "Użytkownicy usługi AAD DC".

    ![Usługach ADAC — widok domeny](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Kliknij kontener o nazwie **użytkownicy usługi AAD DC** Aby wyświetlić wszystkich użytkowników i grup należących do domeny zarządzanej. Powinien zostać wyświetlony kont użytkowników i grup z usługi Azure AD dzierżawy show się w tym kontenerze. Zwróć uwagę, w tym przykładzie, konto użytkownika dla użytkownika o nazwie "bob" i grupę o nazwie "Administratorzy usługi AAD DC" są dostępne w tym kontenerze.

    ![Usługach ADAC — użytkownicy domeny](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Kliknij kontener o nazwie **komputery usługi AAD DC** umożliwia wyświetlenie listy komputerów, przyłączone do tej domeny zarządzanej. Powinien zostać wyświetlony wpis dla bieżącej maszyny wirtualnej, który jest przyłączony do domeny. Konta komputerów dla wszystkich komputerów, które są przyłączone do domeny zarządzanej usług domenowych Azure AD są przechowywane w tym kontenerze "Komputery usługi AAD DC".

    ![Usługach ADAC — komputery przyłączone do domeny](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](create-instance.md)
* [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Wdrażanie narzędzi administracji zdalnej serwera](https://technet.microsoft.com/library/hh831501.aspx)

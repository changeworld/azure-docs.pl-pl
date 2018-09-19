---
title: Jak dodać lub usunąć użytkowników w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać nowych użytkowników lub usunąć istniejących użytkowników przy użyciu usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 782363144a6b1dd87aff515c38588b6ce70b61bd
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295108"
---
# <a name="how-to-add-or-delete-users-using-azure-active-directory"></a>Porady: Dodawanie lub usuwanie użytkowników za pomocą usługi Azure Active Directory
Dodaj nowych użytkowników lub usuń istniejący użytkownicy z dzierżawy usługi Azure Active Directory (Azure AD) przy użyciu usługi Azure AD.

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika
Można utworzyć nowego użytkownika za pomocą usługi Azure Active Directory.

### <a name="to-add-a-new-user"></a>Aby dodać nowego użytkownika
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny lub administrator użytkowników w katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **nowego użytkownika**.

    ![Użytkownicy — Wszyscy użytkownicy strony za pomocą nowego użytkownika z wyróżnioną pozycją](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na **użytkownika** strony, wprowadź wymagane informacje.

    ![Dodawanie nowego użytkownika, strona użytkownika o informacje o użytkowniku](media/add-users-azure-active-directory/new-user-user-blade.png)

    - **Nazwa (wymagane).** Imię i nazwisko nowego użytkownika. Na przykład Joanna Parker.

    - **Nazwa użytkownika (wymagane).** Nazwa użytkownika nowego użytkownika. Na przykład mary@contoso.com. 
    
        Składowa domeny nazwa użytkownika, należy użyć albo nazwa wstępnej domyślnej domeny, <_NazwaDomeny_>. onmicrosoft.com lub niestandardową nazwę domeny, taką jak contoso.com. Aby uzyskać więcej informacji na temat tworzenia niestandardowej nazwy domeny, zobacz [sposób dodawania niestandardowej nazwy domeny do usługi Azure Active Directory](add-custom-domain.md).

    - **Profil.** Opcjonalnie można dodać więcej informacji o użytkowniku. Można również dodać informacje o użytkowniku w późniejszym czasie. Aby uzyskać więcej informacji na temat dodawania informacji o użytkowniku, zobacz [jak dodać lub zmienić informacje o profilu użytkownika](active-directory-users-profile-azure-portal.md).

    - **Grupy.** Opcjonalnie można dodać użytkownika do co najmniej jeden z istniejących grup. Można również dodać użytkownika do grup w późniejszym czasie. Aby uzyskać więcej informacji na temat dodawania użytkowników do grup, zobacz [jak utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md).

    - **Rola katalogu.** Opcjonalnie można dodać użytkownika do roli katalogu. Można przypisać użytkownika jako administrator globalny, lub do jednego lub kilku innych ról administratora w usłudze Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [jak przypisać role do użytkowników](active-directory-users-assign-role-azure-portal.md).

4. Skopiuj wygenerowany automatycznie hasło, podane we **hasło** pole. Należy podać to hasło użytkownikowi początkowego procesu logowania.

5. Wybierz pozycję **Utwórz**.

    Użytkownik zostanie utworzony i dodany do dzierżawy usługi Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Dodawanie nowego użytkownika w środowisku hybrydowym
Jeśli masz środowisko z usługi Azure Active Directory (w chmurze) i Active Directory systemu Windows Server (lokalnie), można dodać nowych użytkowników, synchronizowanie danych istniejącego konta użytkownika. Aby uzyskać więcej informacji na temat środowisk hybrydowych i użytkowników, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Usuwanie użytkownika
Można usunąć istniejącego użytkownika przy użyciu usługi Azure Active Directory.

### <a name="to-delete-a-user"></a>Aby usunąć użytkownika
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wyszukaj i wybierz użytkownika, aby usunąć z dzierżawą usługi Azure AD. Na przykład _Mary Parker_.

3. Wybierz **usunięcie użytkownika**.

    ![Użytkownicy — Wszyscy użytkownicy strony usunięcie użytkownika z wyróżnioną pozycją](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    Użytkownik został usunięty i nie będzie już wyświetlany na **Użytkownicy — Wszyscy użytkownicy** strony. Użytkownik będą widoczne na **usuniętych użytkowników** stronie przez 30 dni i mogą zostać przywrócone w tym samym czasie. Aby uzyskać więcej informacji o przywracaniu użytkownika, zobacz [jak przywrócić lub usunąć trwale ostatnio usuniętego użytkownika](active-directory-users-restore.md).

    >[!Note]
    >Aby zaktualizować tożsamości, informacje kontaktowe i informacje o zadaniu dla użytkowników, którego źródłem urzędu jest Windows Server Active Directory, należy użyć usługi Active Directory systemu Windows Server. Po ukończeniu aktualizacji należy poczekać na następny cykl synchronizacji zakończyć, zanim zmiany będą widoczne.

## <a name="next-steps"></a>Kolejne kroki
Po dodaniu użytkowników, należy wykonać następujące procesy basic:

- [Dodać lub zmienić informacje o profilu](active-directory-users-profile-azure-portal.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md)

- [Praca z grupami dynamicznymi i użytkowników](../users-groups-roles/groups-create-rule.md)

Lub można wykonać inne zadania zarządzania użytkownika, takie jak [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md) lub [przywróceniem usuniętego użytkownika](active-directory-users-restore.md). Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [dokumentacja zarządzania użytkownika usługi Azure Active Directory](../users-groups-roles/index.yml).
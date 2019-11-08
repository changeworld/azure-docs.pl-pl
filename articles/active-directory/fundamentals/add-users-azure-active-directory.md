---
title: Dodawanie lub usuwanie użytkowników — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące dodawania nowych użytkowników lub usuwania istniejących użytkowników przy użyciu Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e98b09d8b07c625613e3be149e64ac8f06adc089
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805560"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Dodawanie lub usuwanie użytkowników przy użyciu Azure Active Directory
Dodaj nowych użytkowników lub Usuń istniejących użytkowników z organizacji usługi Azure Active Directory (Azure AD). Aby dodać lub usunąć użytkowników, musisz być administratorem użytkownika lub administratorem globalnym. 

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika
Nowy użytkownik można utworzyć przy użyciu portalu Azure Active Directory.

### <a name="to-add-a-new-user"></a>Aby dodać nowego użytkownika
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator użytkownika w organizacji.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **nowy użytkownik**.

    ![Użytkownicy — wszyscy użytkownicy z wyróżnionym nowym użytkownikiem](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na stronie **Użytkownik** podaj wymagane informacje.

    ![Dodawanie nowego użytkownika, Strona użytkownika z informacjami o użytkowniku](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nazwa (wymagana).** Imię i nazwisko nowego użytkownika. Na przykład, Maria Parker.

   - **Nazwa użytkownika (wymagana).** Nazwa użytkownika nowego użytkownika. Na przykład mary@contoso.com.
    
       Część domeny nazwa użytkownika musi używać początkowej domyślnej nazwy domeny <_nazwadomeny_>. onmicrosoft. com lub niestandardowej nazwy domeny, takiej jak contoso.com. Aby uzyskać więcej informacji o sposobie tworzenia niestandardowej nazwy domeny, zobacz [jak dodać niestandardową nazwę domeny do Azure Active Directory](add-custom-domain.md).

   - **Profilu.** Opcjonalnie możesz dodać więcej informacji o użytkowniku. Możesz również dodać informacje o użytkowniku w późniejszym czasie. Aby uzyskać więcej informacji na temat dodawania informacji o użytkowniku, zobacz [jak dodać lub zmienić informacje o profilu użytkownika](active-directory-users-profile-azure-portal.md).

   - **Grupowania.** Opcjonalnie możesz dodać użytkownika do jednej lub kilku istniejących grup. Możesz również dodać użytkownika do grup w późniejszym czasie. Aby uzyskać więcej informacji na temat dodawania użytkowników do grup, zobacz [jak utworzyć podstawową grupę i dodać członków](active-directory-groups-create-azure-portal.md).

   - **Rola katalogu.** Opcjonalnie możesz dodać użytkownika do roli administratora usługi Azure AD. Użytkownika można przypisać do administratora globalnego lub co najmniej jednej z ograniczonych ról administratora w usłudze Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Jak przypisać role do użytkowników](active-directory-users-assign-role-azure-portal.md).

4. Skopiuj automatycznie generowane hasło podane w polu **hasło** . Musisz podać to hasło użytkownikowi dla początkowego procesu logowania.

5. Wybierz pozycję **Utwórz**.

    Użytkownik zostanie utworzony i dodany do dzierżawy usługi Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Dodawanie nowego użytkownika w środowisku hybrydowym
Jeśli masz środowisko z zarówno Azure Active Directory (Cloud), jak i Active Directory systemu Windows Server (lokalnie), możesz dodać nowych użytkowników, synchronizując istniejące dane konta użytkownika. Aby uzyskać więcej informacji na temat środowisk hybrydowych i użytkowników, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Usuwanie użytkownika
Istniejącego użytkownika można usunąć przy użyciu portalu Azure Active Directory.

### <a name="to-delete-a-user"></a>Aby usunąć użytkownika
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora użytkownika w organizacji.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wyszukaj i wybierz użytkownika, który chcesz usunąć z dzierżawy usługi Azure AD. Na przykład, _Maria Parker_.

3. Wybierz pozycję **Usuń użytkownika**.

    ![Użytkownicy — wszyscy użytkownicy z wyróżnioną pozycją Usuń użytkownika](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    Użytkownik zostanie usunięty i nie będzie już wyświetlany na stronie **Użytkownicy — wszyscy użytkownicy** . Użytkownik może być widoczny na stronie **usunięci użytkownicy** przez następne 30 dni i może zostać przywrócony w tym czasie. Aby uzyskać więcej informacji o przywracaniu użytkownika, zobacz [przywracanie lub trwałe usuwanie ostatnio usuniętego użytkownika](active-directory-users-restore.md). Po usunięciu użytkownika wszystkie licencje używane przez użytkownika są udostępniane innym użytkownikom do użycia.

    >[!Note]
    >Aby zaktualizować tożsamość, informacje kontaktowe lub informacje o zadaniu dla użytkowników, których źródłem urzędu jest Active Directory Windows Server, należy użyć Active Directory systemu Windows Server. Po zakończeniu aktualizacji należy poczekać na zakończenie następnego cyklu synchronizacji, zanim zobaczysz zmiany.

## <a name="next-steps"></a>Następne kroki

Po dodaniu użytkowników można wykonywać następujące podstawowe procesy:

- [Dodawanie lub zmiana informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Tworzenie grupy podstawowej i Dodawanie członków](active-directory-groups-create-azure-portal.md)

- [Współpraca z grupami dynamicznymi i użytkownikami](../users-groups-roles/groups-create-rule.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak [Dodawanie użytkowników-Gości z innego katalogu](../b2b/what-is-b2b.md) lub [Przywracanie usuniętego użytkownika](active-directory-users-restore.md). Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Azure Active Directory dokumentacja dotycząca zarządzania użytkownikami](../users-groups-roles/index.yml).

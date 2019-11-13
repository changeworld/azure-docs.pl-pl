---
title: Dodawanie lub usuwanie użytkowników — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania nowych użytkowników lub usunąć istniejących użytkowników przy użyciu usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01eb7730290fbf7340fc0a6d8cac8157498f64a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013615"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Dodawanie lub usuwanie użytkowników za pomocą usługi Azure Active Directory

Dodaj nowych użytkowników lub Usuń istniejących użytkowników z organizacji usługi Azure Active Directory (Azure AD). Aby dodać lub usunąć użytkowników, musisz być administratorem użytkownika lub administratorem globalnym.

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika

Można utworzyć nowego użytkownika przy użyciu portalu Azure Active Directory.

### <a name="to-add-a-new-user"></a>Aby dodać nowego użytkownika

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator użytkownika w organizacji.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **nowego użytkownika**.

    ![Użytkownicy — Wszyscy użytkownicy strony za pomocą nowego użytkownika z wyróżnioną pozycją](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na stronie **nowy użytkownik** wybierz pozycję **Utwórz użytkownika** , a następnie Dodaj informacje o użytkowniku.

    ![Dodawanie nowego użytkownika, strona użytkownika o informacje o użytkowniku](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nazwa (wymagana)** : imię i nazwisko nowego użytkownika. Na przykład Krzysztof zielony.

   - **Nazwa użytkownika (wymagana)** : Nazwa użytkownika nowego użytkownika. Na przykład chris@contoso.com.

     Część domeny nazwa użytkownika musi używać początkowej domyślnej nazwy domeny <_nazwadomeny_>. onmicrosoft. com lub niestandardowej nazwy domeny w organizacji usługi Azure AD, takiej jak contoso.com. Możesz wybrać z listy dostępnych domen. Możesz również filtrować listę, wpisując część nazwy domeny. Aby uzyskać więcej informacji na temat tworzenia niestandardowej nazwy domeny, zobacz [sposób dodawania niestandardowej nazwy domeny do usługi Azure Active Directory](add-custom-domain.md).

   - **Grupy**: można dodać użytkownika do jednej lub kilku istniejących grup lub później. Aby uzyskać więcej informacji na temat dodawania użytkowników do grup, zobacz [jak utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md).

   - **Rola katalogu**: Jeśli wymagane są uprawnienia administracyjne usługi Azure AD dla użytkownika, można je dodać do roli usługi Azure AD. Użytkownika można przypisać do administratora globalnego lub co najmniej jednej z ograniczonych ról administratora w usłudze Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [jak przypisać role do użytkowników](active-directory-users-assign-role-azure-portal.md).

   - **Informacje o zadaniu**: więcej informacji o użytkowniku można dodać tutaj lub później. Aby uzyskać więcej informacji na temat dodawania informacji o użytkowniku, zobacz [jak dodać lub zmienić informacje o profilu użytkownika](active-directory-users-profile-azure-portal.md).

4. Skopiuj wygenerowany automatycznie hasło, podane we **hasło** pole. Możesz wybrać opcję użycia automatycznie generowanego hasła podanego w polu hasło lub utworzyć niestandardowe hasło. Należy podać to hasło użytkownikowi początkowego procesu logowania.

5. Wybierz pozycję **Utwórz**.

Użytkownik zostanie utworzony i dodany do organizacji usługi Azure AD.

## <a name="add-a-new-guest-user"></a>Dodawanie nowego użytkownika-gościa

Możesz również zaprosić nowego użytkownika-gościa do współpracy z organizacją, wybierając pozycję **Zaproś użytkownika** na stronie **nowego użytkownika** . Jeśli ustawienia współpracy zewnętrznej w organizacji są skonfigurowane tak, aby można było zapraszać Gości, użytkownik otrzyma wiadomość e-mail z zaproszeniem, które musi zaakceptować, aby rozpocząć współpracę. Aby uzyskać więcej informacji na temat zapraszania użytkowników współpracy B2B, zobacz [Zapraszanie użytkowników B2B do Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Dodawanie użytkownika odbiorcy

Mogą istnieć scenariusze, w których chcesz ręcznie utworzyć konta konsumentów w katalogu Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji na temat tworzenia kont klientów, zobacz [Tworzenie i usuwanie użytkowników konsumentów w Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Dodawanie nowego użytkownika w środowisku hybrydowym

Jeśli masz środowisko z usługi Azure Active Directory (w chmurze) i Active Directory systemu Windows Server (lokalnie), można dodać nowych użytkowników, synchronizowanie danych istniejącego konta użytkownika. Aby uzyskać więcej informacji na temat środowisk hybrydowych i użytkowników, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Usuwanie użytkownika

Można usunąć istniejącego użytkownika przy użyciu portalu Azure Active Directory.

### <a name="to-delete-a-user"></a>Aby usunąć użytkownika

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora użytkownika w organizacji.

1. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wyszukaj i wybierz użytkownika, aby usunąć z dzierżawą usługi Azure AD. Na przykład _Mary Parker_.

1. Wybierz **usunięcie użytkownika**.

    ![Użytkownicy — Wszyscy użytkownicy strony usunięcie użytkownika z wyróżnioną pozycją](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    Użytkownik został usunięty i nie będzie już wyświetlany na **Użytkownicy — Wszyscy użytkownicy** strony. Użytkownik będą widoczne na **usuniętych użytkowników** stronie przez 30 dni i mogą zostać przywrócone w tym samym czasie. Aby uzyskać więcej informacji o przywracaniu użytkownika, zobacz [jak przywrócić lub usunąć trwale ostatnio usuniętego użytkownika](active-directory-users-restore.md). Po usunięciu użytkownika wszystkie licencje używane przez użytkownika są udostępniane innym użytkownikom do użycia.

    >[!Note]
    >Aby zaktualizować tożsamości, informacje kontaktowe i informacje o zadaniu dla użytkowników, którego źródłem urzędu jest Windows Server Active Directory, należy użyć usługi Active Directory systemu Windows Server. Po ukończeniu aktualizacji należy poczekać na następny cykl synchronizacji zakończyć, zanim zmiany będą widoczne.

## <a name="next-steps"></a>Następne kroki

Po dodaniu użytkowników, należy wykonać następujące procesy basic:

- [Dodać lub zmienić informacje o profilu](active-directory-users-profile-azure-portal.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md)

- [Praca z grupami dynamicznymi i użytkowników](../users-groups-roles/groups-create-rule.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak [Dodawanie użytkowników-Gości z innej organizacji usługi Azure AD](../b2b/what-is-b2b.md) lub [Przywracanie usuniętego użytkownika](active-directory-users-restore.md). Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [dokumentacja zarządzania użytkownika usługi Azure Active Directory](../users-groups-roles/index.yml).

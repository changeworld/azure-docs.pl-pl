---
title: Dodawanie lub usuwanie użytkowników — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące dodawania nowych użytkowników lub usuwania istniejących użytkowników przy użyciu usługi Azure Active Directory.
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
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262115"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Dodawanie lub usuwanie użytkowników przy użyciu usługi Azure Active Directory

Dodaj nowych użytkowników lub usuń istniejących użytkowników z organizacji usługi Azure Active Directory (Azure AD). Aby dodawać lub usuwać użytkowników, musisz być administratorem użytkownika lub administratorem globalnym.

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika

Możesz utworzyć nowego użytkownika za pomocą portalu usługi Azure Active Directory.

Aby dodać nowego użytkownika, wykonaj następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator użytkownika w organizacji.

1. Wyszukaj i wybierz *usługę Azure Active Directory* z dowolnej strony.

1. Wybierz **pozycję Użytkownicy**, a następnie wybierz pozycję **Nowy użytkownik**.

    ![Dodawanie użytkownika za pośrednictwem użytkowników — wszyscy użytkownicy w usłudze Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na stronie **Użytkownik** wprowadź informacje dla tego użytkownika:

   - **Nazwa**. Wymagany. Imię i nazwisko nowego użytkownika. Na przykład *Mary Parker*.

   - **Nazwa użytkownika**. Wymagany. Nazwa użytkownika nowego użytkownika. Na przykład `mary@contoso.com`.

     Część domeny nazwy użytkownika musi używać początkowej domyślnej nazwy domeny, *contoso.com* * \<nazwy domeny>.onmicrosoft.com*lub niestandardowej nazwy domeny, takiej jak contoso.com . Aby uzyskać więcej informacji na temat tworzenia niestandardowej nazwy domeny, zobacz [Dodawanie niestandardowej nazwy domeny przy użyciu portalu Azure Active Directory](add-custom-domain.md).

   - **Grupy**. Opcjonalnie można dodać użytkownika do jednej lub więcej istniejących grup. Można również dodać użytkownika do grup w późniejszym czasie. Aby uzyskać więcej informacji na temat dodawania użytkowników do grup, zobacz [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Rola katalogu:** Jeśli potrzebujesz uprawnień administracyjnych usługi Azure AD dla użytkownika, możesz dodać je do roli usługi Azure AD. Użytkownik można przypisać do administratora globalnego lub co najmniej jednej z ról administratora ograniczonego w usłudze Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Jak przypisać role do użytkowników](active-directory-users-assign-role-azure-portal.md).

   - **Informacje o**zadaniu : Możesz dodać więcej informacji o użytkowniku tutaj lub zrobić to później. Aby uzyskać więcej informacji na temat dodawania informacji o użytkowniku, zobacz [Jak dodać lub zmienić informacje o profilu użytkownika](active-directory-users-profile-azure-portal.md).

1. Skopiuj automatycznie wygenerowane hasło podane w polu **Hasło.** Musisz podać to hasło do użytkownika, aby zalogować się po raz pierwszy.

1. Wybierz **pozycję Utwórz**.

Użytkownik jest tworzony i dodawany do organizacji usługi Azure AD.

## <a name="add-a-new-guest-user"></a>Dodawanie nowego użytkownika-gościa

Możesz również zaprosić nowego użytkownika-gościa do współpracy z organizacją, wybierając **pozycję Zaproś użytkownika** ze strony **Nowy użytkownik.** Jeśli ustawienia współpracy zewnętrznej w organizacji są skonfigurowane w taki sposób, że możesz zapraszać gości, użytkownik otrzyma wiadomość e-mail z zaproszeniem, które musi zaakceptować, aby rozpocząć współpracę. Aby uzyskać więcej informacji na temat zapraszania użytkowników do współpracy B2B, zobacz [Zapraszanie użytkowników B2B do usługi Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Dodawanie użytkownika konsumenckiego

Mogą istnieć scenariusze, w których chcesz ręcznie utworzyć konta konsumentów w katalogu B2C usługi Azure Active Directory (Azure AD B2C). Aby uzyskać więcej informacji na temat tworzenia kont konsumenckich, zobacz [Tworzenie i usuwanie użytkowników konsumenckich w usłudze Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Dodawanie nowego użytkownika w środowisku hybrydowym

Jeśli masz środowisko z usługą Azure Active Directory (chmura) i windows server Active Directory (lokalnie), możesz dodać nowych użytkowników, synchronizując istniejące dane konta użytkownika. Aby uzyskać więcej informacji na temat środowisk hybrydowych i użytkowników, zobacz [Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Usuwanie użytkownika

Istniejącego użytkownika można usunąć za pomocą portalu usługi Azure Active Directory.

Aby usunąć użytkownika, wykonaj następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta administratora użytkownika dla organizacji.

1. Wyszukaj i wybierz *usługę Azure Active Directory* z dowolnej strony.

1. Wyszukaj i wybierz użytkownika, którego chcesz usunąć z dzierżawy usługi Azure AD. Na przykład _Mary Parker_.

1. Wybierz pozycję **Usuń użytkownika**.

    ![Użytkownicy — strona Wszyscy użytkownicy z wyróżnioną pozycją Usuń użytkownika](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Użytkownik zostanie usunięty i nie pojawi się już na stronie **Użytkownicy — Wszyscy użytkownicy.** Użytkownik może być widoczny na stronie **Usunięci użytkownicy** przez następne 30 dni i może zostać przywrócony w tym czasie. Aby uzyskać więcej informacji na temat przywracania użytkownika, zobacz [Przywracanie lub usuwanie ostatnio usuniętego użytkownika przy użyciu usługi Azure Active Directory](active-directory-users-restore.md).

Po usunięciu użytkownika wszelkie licencje używane przez użytkownika są udostępniane innym użytkownikom.

>[!Note]
>Za pomocą usługi Windows Server Active Directory należy zaktualizować tożsamość, informacje kontaktowe lub informacje o zadaniach dla użytkowników, których źródłem uprawnień jest usługa Active Directory systemu Windows Server. Po zakończeniu aktualizacji należy poczekać na zakończenie następnego cyklu synchronizacji, zanim zobaczysz zmiany.

## <a name="next-steps"></a>Następne kroki

Po dodaniu użytkowników można wykonać następujące podstawowe procesy:

- [Dodawanie lub zmienianie informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

- [Praca z grupami dynamicznymi i użytkownikami](../users-groups-roles/groups-create-rule.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak [dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md) lub [przywracanie usuniętego użytkownika.](active-directory-users-restore.md) Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Dokumentacja zarządzania użytkownikami usługi Azure Active Directory](../users-groups-roles/index.yml).

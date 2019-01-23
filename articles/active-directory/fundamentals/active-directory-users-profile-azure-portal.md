---
title: Dodaj lub zaktualizuj informacje o profilu użytkownika - usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące dodawania informacji o profilu użytkownika usługi Azure Active Directory, w tym szczegółów obrazu i zadań.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: df0f4fac471192c7e8745c07423857ea3c3d906b
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450851"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Dodaj lub zaktualizuj informacje o profilu użytkownika przy użyciu usługi Azure Active Directory
Dodaj informacje o profilu użytkownika, w tym zdjęcie profilowe, informacje specyficzne dla zadania i niektóre ustawienia za pomocą usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat dodawania nowych użytkowników, zobacz [jak dodawanie lub usuwanie użytkowników w usłudze Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Dodać lub zmienić informacje o profilu
Jak można zauważyć, ma więcej dostępnych informacji w profilu użytkownika, od czego możesz dodać podczas tworzenia użytkownika. Są to informacje dodatkowe jest opcjonalne i można dodać odpowiednio do potrzeb swojej organizacji.

## <a name="to-add-or-change-profile-information"></a>Aby dodać lub zmienić informacje o profilu
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny lub administrator użytkowników w katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz użytkownika. Na przykład _Alain Charon_.

    **Alain Charon — profil** zostanie wyświetlona strona.

    ![Stronie profilu użytkownika, w tym informacje do edycji](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Wybierz **Edytuj** można opcjonalnie dodać lub zaktualizować informacje zawarte w poszczególnych sekcjach dostępne.

    ![Strony profilu użytkownika, wyświetlanie edytowalnego obszarów](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Zdjęcie profilowe.** Wybierz obraz miniatury dla konta użytkownika. Ten obraz jest wyświetlany w usłudze Azure Active Directory i na stronach osobiste użytkownika, takich jak strony myapps.microsoft.com.

    - **Tożsamość.** Dodaj żadnych informacji związanych z kontem, takich jak zamężną nazwisko lub nazwę użytkownika zmienione. 

    - **Informacje o zadaniu.** Dodać informacje dotyczące zadania, takie jak stanowisko, dział lub menedżera użytkownika.

    - **Ustawienia.** Zdecyduj, czy użytkownik może zalogować się do dzierżawy usługi Azure Active Directory. Można również określić globalnej lokalizacji użytkownika.

    - **Informacje kontaktowe.** Dodaj wszystkie odpowiednie informacje kontaktowe dla użytkownika. Na przykład adres lub numer telefonu komórkowego.

    - **Informacje kontaktowe uwierzytelniania.** Sprawdź tych informacji do upewnij się, że istnieje aktywne telefonu i adresu e-mail użytkownika. Te informacje jest używane przez usługę Azure Active Directory do upewnij się, że użytkownik jest naprawdę użytkownika podczas logowania. Informacje kontaktowe uwierzytelniania mogą być aktualizowane tylko przez administratora globalnego.

4. Wybierz pozycję **Zapisz**.

    Wszystkie zmiany są zapisywane dla użytkownika.

    >[!Note]
    >Aby zaktualizować tożsamości, informacje kontaktowe i informacje o zadaniu dla użytkowników, którego źródłem urzędu jest Windows Server Active Directory, należy użyć usługi Active Directory systemu Windows Server. Po ukończeniu aktualizacji należy poczekać na następny cykl synchronizacji zakończyć, zanim zmiany będą widoczne.

## <a name="next-steps"></a>Kolejne kroki
Po zaktualizowaniu profile użytkowników, należy wykonać następujące procesy basic:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md)

Lub można wykonywać inne zadania zarządzania użytkownika, takich jak przypisanie delegatów, przy użyciu zasad oraz udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [dokumentacja zarządzania użytkownika usługi Azure Active Directory](../users-groups-roles/index.yml).

---
title: Dodaj lub zaktualizuj informacje o profilu użytkownika - usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące dodawania informacji o profilu użytkownika usługi Azure Active Directory, w tym szczegółów obrazu i zadań.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4002c14ed6340a0e1a89ee295d47c98436515775
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561783"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Dodaj lub zaktualizuj informacje o profilu użytkownika przy użyciu usługi Azure Active Directory
Dodaj informacje o profilu użytkownika, w tym zdjęcie profilowe, informacje specyficzne dla zadania i niektóre ustawienia za pomocą usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat dodawania nowych użytkowników, zobacz [jak dodawanie lub usuwanie użytkowników w usłudze Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Dodać lub zmienić informacje o profilu
Jak można zauważyć, ma więcej dostępnych informacji w profilu użytkownika, od czego możesz dodać podczas tworzenia użytkownika. Są to informacje dodatkowe jest opcjonalne i można dodać odpowiednio do potrzeb swojej organizacji.

## <a name="to-add-or-change-profile-information"></a>Aby dodać lub zmienić informacje o profilu
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator użytkownika w organizacji.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz użytkownika. Na przykład _Alain Charon_.

    **Alain Charon — profil** zostanie wyświetlona strona.

    ![Stronie profilu użytkownika, w tym informacje do edycji](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Wybierz **Edytuj** można opcjonalnie dodać lub zaktualizować informacje zawarte w poszczególnych sekcjach dostępne.

    ![Strony profilu użytkownika, wyświetlanie edytowalnego obszarów](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Zdjęcie profilowe.** Wybierz obraz miniatury dla konta użytkownika. Ten obraz jest wyświetlany w usłudze Azure Active Directory i na stronach osobiste użytkownika, takich jak strony myapps.microsoft.com.

    - **Tożsamość.** Dodaj lub zaktualizuj dodatkową wartość tożsamości dla użytkownika, na przykład nazwisko ślubu. Tę nazwę można ustawić niezależnie od wartości imię i nazwisko. Na przykład można użyć go do uwzględnienia inicjałów, nazwy firmy lub zmiany sekwencji wyświetlanych nazw. W innym przykładzie dla dwóch użytkowników, których nazwy są "Krzysztof Green", można użyć ciągu tożsamości w celu ustawienia ich nazw na "Krzysztof B. zielony" "Krzysztof R. Green (contoso)".

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

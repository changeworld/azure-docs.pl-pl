---
title: Dodawanie lub aktualizowanie informacji o profilu użytkownika — usługa Azure AD
description: Instrukcje dotyczące dodawania informacji do profilu użytkownika w usłudze Azure Active Directory, w tym obrazu i szczegółów zadania.
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
ms.openlocfilehash: 8ba51b0ad7f314058f34092dff4e26411020086a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422879"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Dodawanie lub aktualizowanie informacji w profilu użytkownika przy użyciu usługi Azure Active Directory
Dodaj informacje o profilu użytkownika, w tym obraz profilu, informacje specyficzne dla zadania i niektóre ustawienia przy użyciu usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat dodawania nowych użytkowników, zobacz [Jak dodawać lub usuwać użytkowników w usłudze Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Dodawanie lub zmienianie informacji o profilu
Jak zobaczysz, w profilu użytkownika jest więcej informacji niż te, które możesz dodać podczas tworzenia użytkownika. Wszystkie te dodatkowe informacje są opcjonalne i mogą być dodawane zgodnie z potrzebami organizacji.

## <a name="to-add-or-change-profile-information"></a>Aby dodać lub zmienić informacje o profilu
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator użytkownika w organizacji.

2. Wybierz **pozycję Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz użytkownika. Na przykład _Alain Charon_.

    Zostanie wyświetlona strona **Alain Charon - Profil.**

    ![Strona profilu użytkownika, w tym edytowalne informacje](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Wybierz **pozycję Edytuj,** aby opcjonalnie dodać lub zaktualizować informacje zawarte w każdej z dostępnych sekcji.

    ![Strona profilu użytkownika z obszarami edytowalnymi](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Zdjęcie profilowe.** Wybierz obraz miniatury konta użytkownika. Ten obraz jest wyświetlany w usłudze Azure Active Directory i na stronach osobistych użytkownika, takich jak strona myapps.microsoft.com.

    - **Tożsamości.** Dodaj lub zaktualizuj dodatkową wartość tożsamości dla użytkownika, taką jak nazwisko małżeńskie. Tę nazwę można ustawić niezależnie od wartości imienia i nazwiska. Można go na przykład użyć do uwzględnienia inicjałów, nazwy firmy lub do zmiany kolejności wyświetlanych nazw. W innym przykładzie dla dwóch użytkowników, których nazwy to "Chris Green", można użyć ciągu tożsamości, aby ustawić ich nazwy na "Chris B. Green" "Chris R. Green (Contoso)."

    - **Informacje o pracy.** Dodaj wszelkie informacje związane z zadaniami, takie jak stanowisko użytkownika, dział lub menedżer.

    - **Ustawienia.** Zdecyduj, czy użytkownik może zalogować się do dzierżawy usługi Azure Active Directory. Można również określić lokalizację globalną użytkownika.

    - **Dane kontaktowe.** Dodaj wszelkie istotne informacje kontaktowe dla użytkownika, z wyjątkiem informacji o telefonie lub kontakcie z telefonem komórkowym niektórych użytkowników (tylko administrator globalny może aktualizować dla użytkowników w rolach administratora).

    - **Informacje kontaktowe uwierzytelniania.** Sprawdź te informacje, aby upewnić się, że istnieje aktywny numer telefonu i adres e-mail użytkownika. Te informacje są używane przez usługę Azure Active Directory, aby upewnić się, że użytkownik jest naprawdę użytkownikiem podczas logowania. Informacje kontaktowe uwierzytelniania mogą być aktualizowane tylko przez administratora globalnego.

4. Wybierz **pozycję Zapisz**.

    Wszystkie zmiany są zapisywane dla użytkownika.

    >[!Note]
    >Za pomocą usługi Windows Server Active Directory należy zaktualizować tożsamość, informacje kontaktowe lub informacje o zadaniach dla użytkowników, których źródłem uprawnień jest usługa Active Directory systemu Windows Server. Po zakończeniu aktualizacji należy poczekać na zakończenie następnego cyklu synchronizacji, zanim zobaczysz zmiany.

## <a name="next-steps"></a>Następne kroki
Po zaktualizowaniu profili użytkowników można wykonać następujące podstawowe procesy:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak przypisywanie pełnomocników, korzystanie z zasad i udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Dokumentacja zarządzania użytkownikami usługi Azure Active Directory](../users-groups-roles/index.yml).

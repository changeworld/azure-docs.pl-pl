---
title: Resetowanie hasła użytkownika — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące resetowania hasła użytkownika przy użyciu usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4fdbbd4d71a9c97259678413cd9e59ee8aeae6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69032663"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Resetowanie hasła użytkownika przy użyciu usługi Azure Active Directory

Jako administrator możesz zresetować hasło użytkownika, jeśli hasło zostanie zapomniane, jeśli użytkownik zostanie zablokowany z urządzenia lub jeśli użytkownik nigdy nie otrzymał hasła.

>[!Note]
>Jeśli dzierżawa usługi Azure AD nie jest katalogiem macierzystym dla użytkownika, nie będzie można zresetować hasła. Oznacza to, że jeśli użytkownik loguje się do organizacji przy użyciu konta innej organizacji, konta Microsoft lub konta Google, nie będzie można zresetować hasła.<br><br>Jeśli użytkownik ma źródło uprawnień jako usługa Windows Server Active Directory, hasło będzie można zresetować tylko wtedy, gdy hasło zostało włączone.<br><br>Jeśli użytkownik ma źródło uprawnień jako zewnętrzna usługa Azure AD, nie będzie można zresetować hasła. Tylko użytkownik lub administrator w zewnętrznej usłudze Azure AD może zresetować hasło.

>[!Note]
>Jeśli nie jesteś administratorem i zamiast tego szukasz instrukcji dotyczących resetowania hasła służbowego, zobacz [Resetowanie hasła służbowego lub szkolnego](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Aby zresetować hasło

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator użytkownika lub administrator haseł. Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Wybierz **pozycję Azure Active Directory**, wybierz pozycję **Użytkownicy**, wyszukaj i wybierz użytkownika, który wymaga zresetowania, a następnie wybierz pozycję **Resetuj hasło**.

    Zostanie wyświetlona strona **Alain Charon - Profil** z opcją **Resetuj hasło.**

    ![Strona profilu użytkownika z wyróżnioną opcją Resetowanie hasła](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na stronie **Resetowanie hasła** wybierz pozycję **Resetuj hasło**.

    > [!Note]
    > Podczas korzystania z usługi Azure Active Directory tymczasowe hasło jest generowane automatycznie dla użytkownika. Korzystając z usługi Active Directory lokalnie, należy utworzyć hasło dla użytkownika.

4. Skopiuj hasło i przekaż go użytkownikowi. Użytkownik będzie musiał zmienić hasło podczas następnego procesu logowania.

    >[!Note]
    >Hasło tymczasowe nigdy nie wygasa. Następnym razem, gdy użytkownik zaloguje się, hasło będzie nadal działać, niezależnie od tego, ile czasu minęło od wygenerowania tymczasowego hasła.

## <a name="next-steps"></a>Następne kroki

Po zresetowaniu hasła użytkownika można wykonać następujące podstawowe procesy:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Dodawanie lub zmienianie informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

Można też wykonywać bardziej złożone scenariusze użytkowników, takie jak przypisywanie pełnomocników, korzystanie z zasad i udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Dokumentacja zarządzania użytkownikami usługi Azure Active Directory](../users-groups-roles/index.yml).

---
title: Resetowanie hasła użytkownika — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące resetowania hasła użytkownika przy użyciu usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db6554e86cef61f2fc8e7a466919d2ce723f0e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60248026"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Zresetuj hasło użytkownika przy użyciu usługi Azure Active Directory

Jako administrator możesz zresetować hasła użytkownika, czy zapomniane hasło, jeśli użytkownik pobiera blokada urządzenia, czy hasło nigdy nie odebrane przez użytkownika.

>[!Note]
>Chyba że dzierżawy usługi Azure AD jest katalogu macierzystego użytkownika, nie można zresetować swoje hasło. Oznacza to, że jeśli użytkownik loguje się do swojej organizacji za pomocą konta z innej organizacji, konta Microsoft lub konto Google, nie można zresetować swoje hasło.<br><br>Jeśli użytkownik ma źródło Urząd jako Windows Server Active Directory, tylko wtedy będzie można zresetować hasła, jeśli włączono funkcję zapisywania zwrotnego haseł.<br><br>Jeśli użytkownik ma źródło Urząd jako zewnętrzne usługi Azure AD, nie można zresetować hasło. Tylko użytkownik lub Konfigurator w zewnętrznej usłudze Azure AD, można zresetować hasła.

>[!Note]
>Jeśli nie jesteś administratorem i zamiast tego szukasz instrukcje dotyczące resetowania hasła firmowego lub szkolnego, zobacz [Resetowanie hasła służbowego](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Aby zresetować hasło

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako użytkownika administrator lub administrator haseł. Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**Wyszukaj i wybierz użytkownika, który potrzebuje resetowania, a następnie wybierz **resetowania hasła**.

    **Alain Charon — profil** zostanie wyświetlona strona z **Resetuj hasło** opcji.

    ![Strony profilu użytkownika, z podświetloną opcją hasło resetowania](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. W **Resetuj hasło** wybierz opcję **Resetuj hasło**.

    Tymczasowe hasło jest generowane automatycznie dla użytkownika.

4. Skopiuj hasło i przekaż go do użytkownika. Będzie wymagane wprowadzenie przez użytkownika, aby zmienić hasło podczas następnego logowania.

    >[!Note]
    >Tymczasowe hasło nigdy nie wygasa. Podczas następnego logowania użytkownika, hasła będzie nadal działać, bez względu na to ile czasu minęło od wygenerowano hasło tymczasowe.

## <a name="next-steps"></a>Kolejne kroki

Po po zresetowaniu hasła użytkownika, należy wykonać następujące procesy basic:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Dodać lub zmienić informacje o profilu](active-directory-users-profile-azure-portal.md)

- [Utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md)

Lub można wykonywać bardziej złożonych scenariuszy użytkownika, takich jak przypisanie delegatów, przy użyciu zasad oraz udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [dokumentacja zarządzania użytkownika usługi Azure Active Directory](../users-groups-roles/index.yml).

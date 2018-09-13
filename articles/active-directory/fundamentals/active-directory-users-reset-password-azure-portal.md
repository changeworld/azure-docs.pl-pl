---
title: Resetowanie haseł w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Administrator zainicjował resetowania hasła dla użytkownika w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
editor: ''
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: 689ab264e56bc8559db6237eee19566e9e3c429f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648440"
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Resetowanie hasła dla użytkownika w usłudze Azure Active Directory

Administratorzy może być konieczne zresetowanie hasła użytkownika w przypadkach, gdzie one nie pamiętam, zostały zablokowane out lub innych scenariuszy. Poniższe kroki ułatwiają procedurę resetowania hasła użytkownika.

## <a name="how-to-reset-the-password-for-a-user"></a>Jak zresetować hasło dla użytkownika

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) za pomocą konta mającego uprawnienia katalogu resetowania haseł użytkowników.
2. Wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz użytkownika, czy chcesz zresetować hasło.
2. Dla wybranego użytkownika wybierz **Resetuj hasło**.

    ![Resetowanie hasła dla użytkownika z profilu użytkownika w usłudze Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. Na **Resetuj hasło**, wybierz opcję **Resetuj hasło**.
7. Hasło tymczasowe zostanie wyświetlona, które następnie można udostępniać użytkownikowi. Użytkownik będzie monitowany następnie zmiany hasła przy następnym ich logowania. 

   > [!NOTE]
   > To hasło tymczasowe nie ma czasu wygaśnięcia więc będzie obowiązywać do momentu logowania i są następnie muszą go zmienić. 

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie użytkownika](../add-users-azure-active-directory.md)
* [Przypisywanie ról administratora użytkownikowi](active-directory-users-assign-role-azure-portal.md)
* [Zarządzanie profilami użytkowników](active-directory-users-profile-azure-portal.md)
* [Usuwanie użytkownika w usłudze Azure AD](../add-users-azure-active-directory.md)

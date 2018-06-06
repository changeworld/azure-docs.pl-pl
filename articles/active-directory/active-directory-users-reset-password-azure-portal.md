---
title: Resetowanie w usłudze Azure Active Directory hasła | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: faeb66c9d0638b13c4716062b768a9c6742edcd1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714157"
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Resetowanie hasła dla użytkownika w usłudze Azure Active Directory

Administratorzy może być konieczne zresetowanie hasła użytkownika w przypadku, gdy ich nie pamiętasz, zostały zablokowane out lub innych scenariuszy. Czynności, które wykonują pomocne przy resetowania hasła użytkownika.

## <a name="how-to-reset-the-password-for-a-user"></a>Jak można zresetować hasła dla użytkownika

1. Zaloguj się do [Centrum administracyjnego usługi AD Azure](https://aad.portal.azure.com) przy użyciu konta z uprawnieniami katalogu resetowania haseł użytkowników.
2. Wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz użytkownika, aby zresetować hasło.
2. Dla wybranego użytkownika, wybierz **resetowania hasła**.

    ![Resetowanie hasła dla użytkownika z profilu użytkownika w usłudze Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. Na **resetowania hasła**, wybierz pozycję **resetowania hasła**.
7. Tymczasowe hasło jest wyświetlane, które można następnie udostępniać dla użytkownika. Użytkownik jest proszony o następnie zmiany hasła podczas następnego logowania ich. 

   > [!NOTE]
   > To hasło tymczasowe nie ma czasu wygaśnięcia będzie obowiązywać do momentu logowania i są następnie muszą go zmienić. 

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie użytkownika](active-directory-users-create-azure-portal.md)
* [Przypisywanie ról administratora do użytkownika](active-directory-users-assign-role-azure-portal.md)
* [Zarządzanie profilami użytkowników](active-directory-users-profile-azure-portal.md)
* [Usunięcie użytkownika w usłudze Azure AD](active-directory-users-delete-user-azure-portal.md)

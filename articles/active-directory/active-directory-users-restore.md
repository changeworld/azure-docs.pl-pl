---
title: Przywróć lub trwale usunąć ostatnio usunięto użytkownika w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Przywracanie usuniętych użytkowników, wyświetlanie dostępnych użytkowników i trwale usunąć użytkownika w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/28/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 9eea61cc1b735c6505df87526cd526a9a4a6eed8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Przywracanie usuniętych użytkowników w usłudze Azure Active Directory

Ten artykuł zawiera instrukcje, aby przywrócić lub trwałe usunięcie wcześniej usuniętych użytkowników. Po usunięciu użytkownika w usłudze Azure Active Directory (Azure AD), usunięto użytkownika są przechowywane przez 30 dni od daty usunięcia. W tym czasie można przywrócić użytkownika i jego właściwości. 

> [!wARNING]
> Po jego trwałe skasowanie, nie można przywrócić użytkownika.


## <a name="how-to-restore-a-recently-deleted-user"></a>Jak przywrócić ostatnio usunięto użytkownika
Użytkownik niedawno zostanie usunięty, zostaną zachowane wszystkie informacje w katalogu. Jeśli użytkownik zostanie przywrócona, również zostanie przywrócona tych informacji.

1. W [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com), wybierz pozycję **użytkowników i grup** &gt; **wszyscy użytkownicy**. 
2. W obszarze **Pokaż**, filtrować stronę, aby przedstawić **niedawno usunięta użytkowników**. 
3. Wybierz co najmniej jeden użytkownik niedawno usunięte.
4. Wybierz **użytkownika przywracania**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Sposób trwałego usuwania ostatnio usunięto użytkownika

1. W [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com), wybierz pozycję **użytkowników i grup** &gt; **wszyscy użytkownicy**. 
2. W obszarze **Pokaż**, filtrować stronę, aby przedstawić **niedawno usunięta użytkowników**. 
3. Wybierz co najmniej jeden użytkownik niedawno usunięte.
4. Wybierz **trwale usunąć**.

## <a name="required-permissions"></a>Wymagane uprawnienia
Następujące uprawnienia są wystarczające przywrócić użytkownika.

Rola  | Uprawnienia 
--------- | ---------
Administrator firmy<p>Pomoc techniczna dla partnerów (warstwa 1)<p>Pomoc techniczna dla partnerów (warstwa 2)<p>Administrator kont użytkowników | Można przywrócić usunięci użytkownicy 
Administrator firmy<p>Pomoc techniczna dla partnerów (warstwa 1)<p>Pomoc techniczna dla partnerów (warstwa 2)<p>Administrator kont użytkowników | Trwale usunąć użytkowników

## <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory Zarządzanie użytkownikami.

* [Szybki Start: Dodawanie lub usuwanie użytkowników do usługi Azure Active Directory](add-users-azure-active-directory.md)
* [Zarządzanie profilami użytkowników](active-directory-users-profile-azure-portal.md)
* [Dodaj gości z innego katalogu](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Przypisanie użytkownika do roli w usługi Azure AD](active-directory-users-assign-role-azure-portal.md)

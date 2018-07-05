---
title: Jak dodać lub usunąć rolę użytkownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać role do uprzywilejowanymi tożsamościami przy użyciu aplikacji Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 01/03/2018
ms.author: rolyon
ms.openlocfilehash: 438a2fcd511fe62fa1dc7ba603c3770d5bcb56a4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446847"
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: dodawanie i usuwanie roli użytkownika
Za pomocą usługi Azure Active Directory (AD), administratora globalnego (lub administrator firmy) można zaktualizować których użytkownicy **trwale** przypisane do ról w usłudze Azure AD. Jest to zrobić za pomocą poleceń cmdlet programu PowerShell, takie jak `Add-MsolRoleMember` i `Remove-MsolRoleMember`. Lub można użyć witryny Azure portal, zgodnie z opisem w [przypisywanie ról administratorów w usłudze Azure Active Directory](active-directory-assign-admin-roles.md).

Aplikacja usługi Azure AD Privileged Identity Management umożliwia Administratorzy ról uprzywilejowanych się przypisania roli trwałe, jak również. Ponadto administratorzy ról uprzywilejowanych uczynić użytkowników **kwalifikujących się** dla ról administratora. Administratora uprawnionego można aktywować rolę, gdy ich potrzebują, a następnie ich uprawnienia wygasają po one gotowe.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Zarządzanie rolami za pomocą usługi PIM w witrynie Azure portal
W Twojej organizacji można przypisać użytkowników do różnych ról administracyjnych w usłudze Azure AD, Office 365 i innych usług firmy Microsoft i aplikacji.  Szczegółowe informacje na temat dostępnych ról można znaleźć w [ról w usłudze Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Aby dodać lub usunąć użytkownika w roli przy użyciu Privileged Identity Management, wyświetlić pulpit nawigacyjny usługi PIM. Następnie kliknij przycisk **użytkowników w rolach administratora** przycisku, lub wybierz określoną rolę (na przykład Administrator globalny) z tabeli ról.

> [!NOTE]
> Jeśli nie zostały jeszcze włączone PIM w witrynie Azure portal, przejdź do strony [Rozpoczynanie pracy z usługą Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) Aby uzyskać szczegółowe informacje.

Jeśli chcesz zapewnić innym użytkownikom dostęp do usługi PIM, sama, ról, które PIM wymaga od użytkownika posiadania są dokładniejszym opisem zawartym w [udzielanie dostępu do usługi PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Dodawanie użytkownika do roli
1. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **usługi Azure AD Privileged Identity Management** Kafelek na pulpicie nawigacyjnym.
2. Wybierz **Zarządzanie rolami uprzywilejowanymi**.
3. W **Podsumowanie ról** tabeli, wybierz rolę, którą chcesz zarządzać.
4. W bloku ról wybierz **Dodaj**.
5. Kliknij przycisk **wybranym użytkownikom** i Wyszukaj użytkownika na **wybranym użytkownikom** bloku.  
6. Wybierz użytkownika z listy wyników wyszukiwania, a następnie kliknij przycisk **gotowe**.
7. Kliknij przycisk **OK** Aby zapisać wybrane opcje. Użytkownik, który wybrano pojawi się na liście jako uprawnieni do roli.

> [!NOTE]
> Nowi użytkownicy z rolą są uprawnione tylko dla roli, domyślnie. Kliknij użytkownika na liście były trwałe roli. Informacje użytkownika będą wyświetlane w nowym bloku. Wybierz **jako trwałą upewnij** w menu informacji użytkownika.  
> Jeśli użytkownik nie można zarejestrować usługi Azure Multi-Factor Authentication (MFA) lub jest za pomocą konta Microsoft (zazwyczaj @outlook.com), należy wprowadzić je stałe w ich ról. Uprawnionymi administratorami monit rejestracji usługi MFA podczas aktywacji.

Teraz, że użytkownik ma prawo dla roli, daj znać, czy użytkownik może dokonać aktywacji go zgodnie z instrukcjami w [jak aktywować lub dezaktywować rolę](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Usuwanie użytkownika z roli
Możesz usunąć użytkowników z kwalifikujących się przypisań ról, ale upewnij się, że zawsze jest co najmniej jednego użytkownika, który jest trwały administratora globalnego.

Wykonaj następujące kroki, aby usunąć określonego użytkownika z roli:

1. Przejdź do roli na liście ról, wybierając rolę na pulpicie nawigacyjnym usługi Azure AD PIM lub przez kliknięcie **użytkowników w rolach administratora** przycisku.
2. Kliknij użytkownika na liście użytkowników.
3. Kliknij przycisk **Usuń**. Komunikat zostanie wyświetlony monit o potwierdzenie.
4. Kliknij przycisk **tak** do usuwania roli użytkownika.

Jeśli nie masz pewności, które użytkownicy nadal potrzebują swoje przypisania roli, a następnie możesz [Rozpoczynanie przeglądu dostępu dla roli](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


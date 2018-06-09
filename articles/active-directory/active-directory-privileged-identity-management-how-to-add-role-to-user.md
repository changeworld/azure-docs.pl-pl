---
title: Jak dodać lub usunąć rolę użytkownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać ról uprzywilejowanych tożsamości z aplikacją Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 01/03/2018
ms.author: rolyon
ms.openlocfilehash: 856fdc69bd5ce582ca772c01f8af615fbc455887
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233224"
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: dodawanie i usuwanie roli użytkownika
Z usługi Azure Active Directory (AD), administrator globalny (lub administrator firmy) można aktualizować której użytkownicy są **trwale** przypisane do ról w usłudze Azure AD. Jest to zrobić za pomocą poleceń cmdlet programu PowerShell, takie jak `Add-MsolRoleMember` i `Remove-MsolRoleMember`. Można też używać portalu Azure, zgodnie z opisem w [przypisywanie ról administratorów w usłudze Azure Active Directory](active-directory-assign-admin-roles.md).

Aplikacja Azure AD Privileged Identity Management umożliwia administratorom ról uprzywilejowanych upewnij przypisań ról trwałe, jak również. Ponadto administratorzy ról uprzywilejowanych ułatwia użytkownikom **kwalifikujących się** dla ról administratora. Administrator kwalifikujących się może aktywować rolę, gdy potrzebują, a następnie ich uprawnienia wygasają po ich wszystko gotowe.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Zarządzanie rolami PIM w portalu Azure
W organizacji można przypisać użytkowników do różnych ról administracyjnych w usłudze Azure AD, Office 365 i innych usług firmy Microsoft i aplikacji.  Więcej informacji na temat dostępnych ról można znaleźć w folderze [role w programie Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Aby dodać lub usunąć użytkownika w roli przy użyciu Privileged Identity Management, przełącz się na pulpicie nawigacyjnym usługi PIM. Następnie kliknij przycisk **użytkownicy o rolach administratora** przycisk lub wybierz określoną rolę (na przykład Administrator globalny) z tabeli ról.

> [!NOTE]
> Jeśli nie włączono jeszcze usługi PIM w portalu Azure, przejdź do [wprowadzenie do usługi Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) szczegółowe informacje.

Jeśli chcesz udostępnić innego użytkownika PIM sam ról, które PIM wymaga od użytkownika posiadania opisano dalsze w [sposób udzielić dostępu do usługi PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Dodawanie użytkownika do roli
1. W [portalu Azure](https://portal.azure.com/), wybierz pozycję **Azure AD Privileged Identity Management** kafelka na pulpicie nawigacyjnym.
2. Wybierz **Zarządzanie ról uprzywilejowanych**.
3. W **Podsumowanie ról** tabeli, wybierz rolę, którymi chcesz zarządzać.
4. W bloku roli wybierz **Dodaj**.
5. Kliknij przycisk **Wybierz użytkowników** i Wyszukaj użytkownika na **Wybierz użytkowników** bloku.  
6. Wybierz użytkownika z listy wyników wyszukiwania, a następnie kliknij przycisk **gotowe**.
7. Kliknij przycisk **OK** Aby zapisać wybrane opcje. Użytkownik, który wybrano będą wyświetlane na liście jako kwalifikujący się do roli.

> [!NOTE]
> Nowi użytkownicy z rolą tylko kwalifikują się do roli domyślnie. Utrwalenie rolę, kliknij przycisk użytkownika na liście. Informacje użytkownika będą wyświetlane w nowym bloku. Wybierz **Sprawdź uprawnienie** w menu informacji użytkownika.  
> Jeśli użytkownik nie można zarejestrować usługi Azure Multi-Factor Authentication (MFA) lub korzystanie z konta Microsoft (zazwyczaj @outlook.com), które należy rozważyć ich stałe w ich ról. Administratorzy kwalifikujących się monit o zarejestrować w usłudze MFA podczas aktywacji.

Teraz, że użytkownik jest uprawniona do roli, daj znać, aby aktywować go zgodnie z instrukcjami w [jak aktywować lub dezaktywować rolę](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Usuwa użytkownika z roli
Możesz usunąć użytkowników z przypisania ról kwalifikujących się, ale upewnij się, że zawsze jest co najmniej jednego użytkownika, który jest stały administratora globalnego.

Wykonaj następujące kroki, aby usunąć użytkowników z roli:

1. Przejdź do roli na liście roli, wybierając rolę w pulpicie nawigacyjnym usługi Azure AD PIM lub klikając **użytkownicy o rolach administratora** przycisku.
2. Kliknij na użytkownika na liście użytkowników.
3. Kliknij przycisk **Usuń**. Komunikat zostanie wyświetlony monit o potwierdzenie.
4. Kliknij przycisk **tak** do usunięcia roli użytkownika.

Jeśli nie masz pewności, którzy użytkownicy nadal muszą ich przypisania ról, a następnie możesz [rozpocząć Przegląd dostępu dla roli](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


---
title: Przypisywanie lub usuwanie licencji — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące przypisywania lub usuwania licencji usługi Azure Active Directory z użytkowników lub grup.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128829"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Przypisywanie lub usuwanie licencji w portalu usługi Azure Active Directory

Wiele usług usługi Azure Active Directory (Azure AD) wymaga licencjonowannia każdego z użytkowników lub grup (i skojarzonych członków) dla tej usługi. Tylko użytkownicy z aktywnymi licencjami będą mogli uzyskiwać dostęp do licencjonowanych usług Azure AD i korzystać z nich, dla których jest to prawdą. Licencje są stosowane dla dzierżawy i nie są przenoszone do innych dzierżawców. 

## <a name="available-license-plans"></a>Dostępne plany licencji

Istnieje kilka planów licencji dostępnych dla usługi Azure AD, w tym:

- Usługa Azure AD — warstwa Bezpłatna

- Usługa Azure AD — warstwa Premium P1

- Usługa Azure AD — warstwa Premium P2

Aby uzyskać szczegółowe informacje na temat każdego planu licencji i powiązanych szczegółów licencjonowania, zobacz [Jakiej licencji potrzebuję?](https://azure.microsoft.com/pricing/details/active-directory/).

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do grupy, należy określić **lokalizację użycia** dla wszystkich członków. Tę wartość można ustawić w obszarze **Ustawienia &gt; profilu użytkowników &gt; &gt; usługi Azure Active Directory** w usłudze Azure AD. Każdy użytkownik, którego lokalizacja użycia nie jest określona dziedziczy lokalizację organizacji usługi Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Wyświetlanie planów licencji i szczegółów planu

Można wyświetlić dostępne plany usług, w tym poszczególne licencje, sprawdzić oczekujące daty wygaśnięcia i wyświetlić liczbę dostępnych przydziałów.

### <a name="to-find-your-service-plan-and-plan-details"></a>Aby znaleźć plan usług i szczegóły planu

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu konta administratora licencji w organizacji usługi Azure AD.

1. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Licencje**.

    ![Strona Licencje z liczbą zakupionych usług i przypisanych licencji](media/license-users-groups/license-details-blade.png)

1. Wybierz **łącze Zakupione,** aby wyświetlić stronę **Produkty** i wyświetlić **przypisane**numery **dostępne**i **wygasające wkrótce** dla planów licencji.

    ![strona usług, z planami licencji serwisowych i powiązanymi informacjami o licencji](media/license-users-groups/license-products-blade-with-products.png)

1. Wybierz nazwę planu, aby wyświetlić licencjonowanych użytkowników i grupy.

## <a name="assign-licenses-to-users-or-groups"></a>Przypisywanie licencji użytkownikom lub grupom

Upewnij się, że każda osoba, która potrzebuje korzystać z licencjonowanej usługi Azure AD, ma odpowiednią licencję. Prawa licencyjne można dodać użytkownikom lub całej grupie.

### <a name="to-assign-a-license-to-a-user"></a>Aby przypisać licencję do użytkownika

1. Na stronie **Produkty** wybierz nazwę planu licencji, który chcesz przypisać użytkownikowi.

    ![strona usług, z wyróżnionym planem licencji na usługi](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stronie Przegląd planu licencji wybierz pozycję **Przypisz**.

    ![strona usług z wyróżnioną opcją Przypisz](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stronie **Przypisywanie** wybierz pozycję **Użytkownicy i grupy**, a następnie wyszukaj i wybierz przypisanego użytkownika.

    ![Przypisywanie strony licencji z wyróżnionymi opcjami wyszukiwania i wybierz](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Wybierz **pozycję Opcje przydziału,** upewnij się, że masz włączone odpowiednie opcje licencji, a następnie wybierz przycisk **OK**.

    ![Strona opcji licencji ze wszystkimi opcjami dostępnymi w planie licencji](media/license-users-groups/license-option-blade-assignments.png)

    Strona **Przypisz licencję** zostanie zaktualizowana, aby pokazać, że użytkownik jest zaznaczony i że przypisania są skonfigurowane.

    > [!NOTE]
    > Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, należy określić **lokalizację użycia**. Tę wartość można ustawić w obszarze **Ustawienia &gt; profilu użytkowników &gt; &gt; usługi Azure Active Directory** w usłudze Azure AD. Każdy użytkownik, którego lokalizacja użycia nie jest określona dziedziczy lokalizację organizacji usługi Azure AD.

1. Wybierz opcję **Przypisz**.

    Użytkownik jest dodawany do listy licencjonowanych użytkowników i ma dostęp do dołączonych usług Azure AD.
    > [!NOTE]
    > Licencje można również przypisać bezpośrednio do użytkownika na stronie **Licencje** użytkownika. Jeśli użytkownik ma licencję przypisaną za pośrednictwem członkostwa w grupie i chcesz przypisać tę samą licencję do użytkownika bezpośrednio, można to zrobić tylko ze strony **Produkty** wymienionej tylko w kroku 1.

### <a name="to-assign-a-license-to-a-group"></a>Aby przypisać licencję do grupy

1. Na stronie **Produkty** wybierz nazwę planu licencji, który chcesz przypisać użytkownikowi.

    ![Ostrze produktów z wyróżnionym planem licencji na produkty](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stronie **Planu Premium usługi Azure Active Directory 2** wybierz pozycję **Przypisz**.

    ![Strona Produkty z wyróżnioną opcją Przypisz](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stronie **Przypisywanie** wybierz pozycję **Użytkownicy i grupy**, a następnie wyszukaj i wybierz grupę, do której przypisujesz licencję.

    ![Przypisywanie strony licencji z wyróżnionymi opcjami wyszukiwania i wybierz](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Wybierz **pozycję Opcje przydziału,** upewnij się, że masz włączone odpowiednie opcje licencji, a następnie wybierz przycisk **OK**.

    ![Strona opcji licencji ze wszystkimi opcjami dostępnymi w planie licencji](media/license-users-groups/license-option-blade-group-assignments.png)

    Strona **Przypisz licencję** zostanie zaktualizowana, aby pokazać, że użytkownik jest zaznaczony i że przypisania są skonfigurowane.

1. Wybierz opcję **Przypisz**.

    Grupa zostanie dodana do listy grup licencjonowanych i wszyscy członkowie mają dostęp do dołączonych usług Azure AD.

## <a name="remove-a-license"></a>Usuwanie licencji

Licencję można usunąć ze strony użytkownika usługi Azure AD użytkownika, ze strony przeglądu grupy dla przypisania grupy lub od strony **Licencje** usługi Azure AD, aby wyświetlić użytkowników i grupy licencji.

### <a name="to-remove-a-license-from-a-user"></a>Aby usunąć licencję od użytkownika

1. Na stronie **Licencjonowani użytkownicy** planu usług wybierz użytkownika, który nie powinien już mieć licencji. Na przykład _Alain Charon_.

1. Wybierz pozycję **Usuń licencję**.

    ![Strona Licencjonowani użytkownicy z wyróżnioną opcją Usuń licencję](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licencji, które użytkownik dziedziczy z grupy, nie można usunąć bezpośrednio. Zamiast tego należy usunąć użytkownika z grupy, z której dziedziczą licencję.

### <a name="to-remove-a-license-from-a-group"></a>Aby usunąć licencję z grupy

1. Na stronie **Grupy licencjonowane** planu licencji wybierz grupę, która nie powinna już mieć licencji.

1. Wybierz pozycję **Usuń licencję**.

    ![Strona Grupy licencjonowane z wyróżnioną opcją Usuń licencję](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Gdy lokalne konto użytkownika zsynchronizowane z usługą Azure AD wypada poza zakres synchronizacji lub gdy synchronizacja zostanie usunięta, użytkownik jest usuwany programem nietrwale w usłudze Azure AD. W takim przypadku licencje przypisane użytkownikowi bezpośrednio lub za pośrednictwem licencji grupowych zostaną oznaczone jako **zawieszone,** a nie **usunięte.**

## <a name="next-steps"></a>Następne kroki

Po przypisaniu licencji można wykonać następujące procesy:

- [Identyfikowanie i rozwiązywanie problemów z przypisywaniem licencji](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Dodawanie licencjonowanych użytkowników do grupy w celu licencjonowania](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenariusze, ograniczenia i znane problemy przy użyciu grup do zarządzania licencjonowaniem w usłudze Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Dodawanie lub zmienianie informacji o profilu](active-directory-users-profile-azure-portal.md)

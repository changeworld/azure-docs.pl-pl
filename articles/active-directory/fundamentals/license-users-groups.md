---
title: Przypisywanie lub usuwanie licencji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu przypisywania i usuwania licencji usługi Azure Active Directory użytkowników lub grup.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e7a3f80067adb3093bd27e34a45b3afd72b4993
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60247648"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory
Wiele usług Azure Active Directory (Azure AD) wymagają aktywacji produktu usługi Azure AD i wszystkich użytkowników lub grup (i skojarzone elementy członkowskie) licencji dla tego produktu. Tylko użytkownicy z liczby aktywnych licencji będą mogli uzyskać dostęp do licencjonowanych usługi Azure AD.

## <a name="available-product-editions"></a>Wersje dostępnych produktów
Istnieją różne wersje produktu usługi Azure AD.

- Usługa Azure AD — warstwa Bezpłatna

- Usługa Azure AD — warstwa Podstawowa

- Usługa Azure AD Premium 1 (subskrypcja P1 usługi Azure AD)

- Usługa Azure AD Premium 2 (Azure AD P2)

Aby uzyskać szczegółowe informacje o poszczególnych wersji produktu i skojarzone licencjonowania szczegółowe informacje, zobacz [jaka licencja jest potrzebne?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Wyświetl szczegóły wersji i licencji produktu
Możesz wyświetlić dostępne produkty, w poszczególnych licencji, w tym sprawdzania pod kątem dowolnej daty wygaśnięcia oczekujące i liczba przypisań dostępne.

### <a name="to-find-your-product-and-license-details"></a>Aby uzyskać szczegółowe informacje produktu i licencji
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **licencji**.

    **Licencji** zostanie wyświetlona strona.

    ![Strona licencji, o liczbie zakupionych produktów i przypisanych licencji](media/license-users-groups/license-details-blade.png)
    
3. Wybierz **zakupionych produktów** link, aby wyświetlić **produktów** strony i zobaczyć **przypisane**, **dostępne**, i  **Wkrótce wygaśnie** szczegółów dla każdej wersji określonego produktu.

    ![Strona produkty, wersje produktów i informacji związanych z licencją](media/license-users-groups/license-products-blade-with-products.png)

4. Wybierz nazwę wersji produktu, aby zobaczyć jej licencjonowanych użytkowników i grup.

## <a name="assign-licenses-to-users-or-groups"></a>Przypisywanie licencji do użytkowników lub grup
Upewnij się, że każdy konieczności korzystania z licencjonowanego usługi Azure AD dysponuje odpowiednią licencją. Jest do Ciebie czy chcesz dodać prawa licencyjne do poszczególnych użytkowników lub całych grup.

>[!Note]
>Licencjonowanie na podstawie grupy jest funkcją publicznej wersji zapoznawczej usługi Azure AD i jest dostępna z dowolnej płatnych planu licencjonowania usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Aby uzyskać szczegółowe informacje o tym, jak dodać użytkowników, zobacz [jak dodawanie lub usuwanie użytkowników w usłudze Azure Active Directory](add-users-azure-active-directory.md). Aby uzyskać szczegółowe informacje o tym, jak tworzyć grupy i dodawać członków, zobacz [utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Aby przypisać licencję do określonego użytkownika
1. Na **produktów** stronie, wybierz nazwę wersji, którą chcesz przypisać do użytkownika. Na przykład _Azure Active Directory — wersja Premium (Plan 2)_ .

    ![Strona produktów, z wersją produktu wyróżniony](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Na **Azure Active Directory — wersja Premium (Plan 2)** wybierz opcję **przypisać**.

    ![Strona produktów, opcją Przypisz wyróżniony](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Na **przypisać** wybierz opcję **użytkowników i grup**, a następnie wyszukaj i wybierz użytkownika, w przypadku przypisywania licencji. Na przykład _Mary Parker_.

    ![Przypisywanie licencji zawierającej podświetlony wyszukiwany i wybrania opcji](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Wybierz **opcje przydziału**, upewnij się, że masz odpowiednią licencję opcje włączona, a następnie wybierz **OK**.

    ![Strona opcji licencji, wszystkie opcje dostępne w wersji](media/license-users-groups/license-option-blade-assignments.png)

    **Przypisywanie licencji** aktualizacji, aby pokazać, że użytkownik jest zaznaczone, a przydziały są skonfigurowane stron.

    >[!NOTE]
    >Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, należy określić **lokalizacji użytkowania**. Tę wartość można ustawić **usługi Azure Active Directory &gt; użytkowników &gt; profilu &gt; ustawienia** obszaru w usłudze Azure AD.

5. Wybierz opcję **Przypisz**.

    Użytkownik zostanie dodany do listy użytkowników z licencjami i dostępem do dołączonej usługi Azure AD.

### <a name="to-assign-a-license-to-an-entire-group"></a>Aby przypisać licencję do całej grupy
1. Na **produktów** stronie, wybierz nazwę wersji, którą chcesz przypisać do użytkownika. Na przykład _Azure Active Directory — wersja Premium (Plan 2)_ .

    ![Blok produkty, wydanie wyróżnione produktu](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Na **Azure Active Directory — wersja Premium (Plan 2)** wybierz opcję **przypisać**.

    ![Strona produktów, opcją Przypisz wyróżniony](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Na **przypisać** wybierz opcję **użytkowników i grup**, a następnie wyszukaj i wybierz grupę, w przypadku przypisywania licencji. Na przykład _zasad zarządzania urządzeniami Przenośnymi - Zachodnia_.

    ![Przypisywanie licencji zawierającej podświetlony wyszukiwany i wybrania opcji](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Wybierz **opcje przydziału**, upewnij się, że masz odpowiednią licencję opcje włączona, a następnie wybierz **OK**.

    ![Strona opcji licencji, wszystkie opcje dostępne w wersji](media/license-users-groups/license-option-blade-group-assignments.png)

    **Przypisywanie licencji** aktualizacji, aby pokazać, że użytkownik jest zaznaczone, a przydziały są skonfigurowane stron.

    >[!NOTE]
    >Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do grupy, należy określić **lokalizacji użytkowania** wszystkim jej członkom. Tę wartość można ustawić **usługi Azure Active Directory &gt; użytkowników &gt; profilu &gt; ustawienia** obszaru w usłudze Azure AD. Żaden użytkownik nie określono lokalizacji użytkowania — której dziedziczy lokalizacji dzierżawy.

5. Wybierz opcję **Przypisz**.

    Grupa jest dodawana do listy grup licencji, i wszyscy członkowie mają dostęp do dołączonej usługi Azure AD.

## <a name="remove-a-license"></a>Usuwanie licencji
Możesz usunąć licencję dla użytkownika lub grupy z **licencji** strony.

### <a name="to-remove-a-license-from-a-specific-user"></a>Aby usunąć licencję z określonego użytkownika
1. Na **licencjonowani użytkownicy** stronie wersję produktu, wybierz użytkownika, którego nie powinni mieć licencję. Na przykład _Alain Charon_.

2. Wybierz **usuwanie licencji**.

    ![Strona licencjonowanych użytkowników z podświetloną opcją licencji Remove](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Aby usunąć licencję z grupy
1. Na **licencjonowane grupy** stronie wersję produktu, wybierz grupę, która już powinna mieć licencję. Na przykład _zasad zarządzania urządzeniami Przenośnymi - Zachodnia_.

2. Wybierz **usuwanie licencji**.

    ![Strony grup licencji z podświetloną opcją licencji Remove](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Dziedziczone przez użytkownika z grupy licencji nie można bezpośrednio usunąć. Zamiast tego należy usunąć użytkownika z grupy, z którego jest dziedziczenie licencji.

## <a name="next-steps"></a>Kolejne kroki
Po przypisaniu licencji, należy wykonać następujące procesy:

- [Identyfikowanie i rozwiązywanie problemów z licencją przypisania](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Dodawanie licencjonowanych użytkowników do grupy dla licencjonowania](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenariusze, ograniczenia i znane problemy, używanie grup do zarządzania, Licencjonowanie w usłudze Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Dodać lub zmienić informacje o profilu](active-directory-users-profile-azure-portal.md)

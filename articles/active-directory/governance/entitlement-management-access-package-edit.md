---
title: Edytowanie i zarządzanie nią istniejący pakiet dostępu w usłudze Azure AD uprawnienie management (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się, jak edytowanie i zarządzanie nią istniejący pakiet dostępu w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c1717249abac30847f96073ee6daf7d98112d7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190335"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Edytowanie i zarządzanie nią istniejący pakiet dostępu w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pakiet dostępu umożliwia wykonywanie jednorazowej konfiguracji zasobów i zasad, który automatycznie zarządza dostępu przez cały okres istnienia pakiet dostępu. Jako Menedżer dostępu do pakietu możesz zmienić zasoby w pakiecie dostęp w dowolnym momencie bez martwienia się o aprowizacji dany użytkownik ma dostęp do nowych zasobów lub usunięcie ich dostęp z poprzednie zasoby. Zasady można aktualizować w dowolnym momencie, zmiany zasad dotyczą tylko nowych dostęp.

W tym artykule opisano jak edytowanie i zarządzanie nią istniejące pakiety dostępu.

## <a name="add-resource-roles"></a>Dodawanie ról zasobów

Rola zasobów to kolekcja uprawnień skojarzonych z zasobem. Sposób, w jaki można udostępnić zasoby żądania przez użytkowników jest przez dodanie ról zasobów do dostępu do pakietu. Możesz dodać role zasobów do grup, aplikacji i witryn programu SharePoint.

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij **role zasobów**.

1. Kliknij przycisk **Dodaj role zasobów** otworzyć Dodaj role zasobów dostępu do strony pakietu.

    ![Dostęp do pakietu — Dodawanie ról zasobów](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. W zależności od tego, czy chcesz dodać grupy, aplikacji lub witryny programu SharePoint wykonaj kroki w jednym z następujących sekcji zasobów w roli.

### <a name="add-a-group-resource-role"></a>Dodawanie roli grupy zasobów

Może mieć Zarządzanie uprawnieniami automatyczne dodawanie użytkowników do grupy przy ich przypisywaniu pakietu dostępu. 

- Gdy grupa jest częścią pakietu dostępu i użytkownik jest przypisany do tego pakietu dostęp, użytkownik zostanie dodany do tej grupy, jeśli nie są jeszcze zainstalowane.
- Po wygaśnięciu przypisywania pakietów dostępu użytkownika są usuwane z grupy, chyba że mają obecnie przypisania do innego pakietu dostępu, który zawiera tę samą grupę.

Możesz wybrać wszystkie grupy usługi Office 365 lub grupy zabezpieczeń usługi Azure AD.  Administratorzy mogą dodawać dowolną grupę do wykazu; właścicieli katalogu można dodać żadnej grupy do katalogu, jeśli są one właściciela grupy. Po wybraniu grupy, należy pamiętać o następujących ograniczeń usługi Azure AD:

- Po dodaniu użytkownika jako gościa, w tym jako członka do grupy Użytkownicy widzą wszystkich członków tej grupy.
- Usługa Azure AD nie można zmienić członkostwo grupy, która była synchronizowana z systemu Windows Server Active Directory za pomocą usługi Azure AD Connect.  
- Nie można zaktualizować członkostwa w grupach dynamicznych, dodając lub usuwając elementu członkowskiego, więc członkostwa w grupach dynamicznych nie są odpowiednie do użycia z usługą Zarządzanie uprawnieniami.

1. Na **Dodawanie ról zasobów w celu dostępu do pakietu** kliknij **grup** aby otworzyć okienko grupy.

1. Wybierz grupy, które mają zostać uwzględnione w pakiecie dostępu.

    ![Dostęp do pakietu — Dodaj role zasobów — wybierz grupy](./media/entitlement-management-access-package-edit/group-select.png)

1. Kliknij pozycję **Wybierz**.

1. W **roli** listy wybierz **właściciela** lub **elementu członkowskiego**.

    Przeważnie roli członka. Jeśli zostanie wybrana rola właściciela, które umożliwi użytkownikom dodać lub usunąć inne członkowie lub właściciele.

    ![Dostęp do pakietu — Dodaj rolę zasobu dla grupy](./media/entitlement-management-access-package-edit/group-role.png)

1. Kliknij pozycję **Add** (Dodaj).

    Każdy użytkownik z istniejących przypisań do dostępu do pakietu zostaną automatycznie stają się one Członkowie tej grupy, gdy jest ona dodawana.

### <a name="add-an-application-resource-role"></a>Dodaj rolę zasobów aplikacji

Program może automatycznie przypisywać użytkownikom dostępu do aplikacji dla przedsiębiorstw usługi Azure AD, w tym aplikacje SaaS i aplikacje w organizacji Sfederowane z usługą Azure AD, gdy użytkownik jest przypisany pakietu dostępu z usługi Azure AD. W przypadku aplikacji, które integrują się z usługą Azure AD za pomocą federacyjnego logowania jednokrotnego usługi Azure AD będzie wystawiać tokeny federacyjnego dla użytkowników przypisanych do aplikacji.

Aplikacje mogą mieć wiele ról. Podczas dodawania aplikacji do pakietu dostępu, jeśli ta aplikacja ma więcej niż jednej roli, należy określić odpowiednią rolę dla tych użytkowników.  Jeśli tworzysz aplikacje, możesz przeczytać więcej o jak te role są dostarczane do aplikacji w artykule na temat sposobu [Konfigurowanie oświadczenia roli wystawionych w tokenie SAML](../develop/active-directory-enterprise-app-role-management.md).

Gdy roli aplikacji jest częścią pakietu dostępu:

- Gdy użytkownik jest przypisany ten pakiet dostęp, użytkownik zostanie dodany do roli aplikacji, jeśli nie jeszcze nie istnieje.
- Po wygaśnięciu przypisywania pakietów dostępu użytkownika ich dostępu zostaną usunięte z aplikacji, chyba że mają oni przypisanie do innego pakietu dostępu, który zawiera roli tej aplikacji.

Poniżej przedstawiono kilka kwestii, podczas wybierania aplikacji:

- Aplikacje mogą również mieć grup przypisanych do ich ról także.  Możesz jednak dodać grupę zamiast roli aplikacji w pakiecie dostępu, a następnie aplikacja nie będzie widoczny dla użytkownika jako część pakietu dostępu w portalu Moje dostępu.

1. Na **Dodawanie ról zasobów w celu dostępu do pakietu** kliknij **aplikacje** aby otworzyć okienko Wybierz aplikacje.

1. Wybierz aplikacje, które chcesz uwzględnić w pakiecie dostępu.

    ![Dostęp do pakietu — Dodaj role zasobów — wybierz aplikacje](./media/entitlement-management-access-package-edit/application-select.png)

1. Kliknij pozycję **Wybierz**.

1. W **roli** , wybierz rolę aplikacji na liście.

    ![Dostęp do pakietu — Dodawanie roli zasobów dla aplikacji](./media/entitlement-management-access-package-edit/application-role.png)

1. Kliknij pozycję **Add** (Dodaj).

    Każdy użytkownik z istniejących przypisań do dostępu do pakietu automatycznie otrzymają dostęp do tej aplikacji, gdy jest ona dodawana.

### <a name="add-a-sharepoint-site-resource-role"></a>Dodawanie roli zasobów witryny programu SharePoint

Usługi Azure AD można automatycznie przypisywać użytkownikom dostęp do witryny usługi SharePoint Online lub kolekcji witryn usługi SharePoint Online przy ich przypisywaniu pakietu dostępu.

1. Na **Dodawanie ról zasobów w celu dostępu do pakietu** kliknij **witryny programu SharePoint** aby otworzyć okienko witryny wybierz opcję usługi SharePoint Online.

1. Wybierz witryny usługi SharePoint Online, które chcesz uwzględnić w pakiecie dostępu.

    ![Dostęp do pakietu — Dodaj role zasobów — wybierz witryn usługi SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Kliknij pozycję **Wybierz**.

1. W **roli** , wybierz rolę witryny usługi SharePoint Online na liście.

    ![Dostęp do pakietu — Dodaj rolę zasobu w witrynie usługi SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Kliknij pozycję **Add** (Dodaj).

    Każdy użytkownik z istniejących przypisań do dostępu do pakietu, automatycznie otrzymają dostęp do tej witryny usługi SharePoint Online po dodaniu.

## <a name="remove-resource-roles"></a>Usuwanie ról zasobów

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij **role zasobów**.

1. Lista ról zasobów zawiera rolę zasobu, który chcesz usunąć.

1. Kliknij przycisk wielokropka ( **...** ) a następnie kliknij przycisk **Usuń zasób roli**.

    Każdy użytkownik z istniejących przypisań do dostępu do pakietu, będą automatycznie dostępne ich odwoływane do tej roli zasobu, gdy zostanie on usunięty.

## <a name="add-a-new-policy"></a>Dodaj nowe zasady

Sposób, możesz określić, kto może żądać dostępu do pakietu jest tworzenie zasad. Jeśli chcesz zezwolić na różnych zestawów użytkowników uzyskają przydziałów do innego zatwierdzenia i ustawienia wygaśnięcia, można utworzyć wiele zasad dostępu jednego pakietu. Jednych zasad nie można przypisać do tego samego pakietu dostępu użytkowników wewnętrznych i zewnętrznych. Jednak można utworzyć dwie zasady w tym samym pakiecie dostępu — jeden dla użytkowników wewnętrznych i jeden dla użytkowników zewnętrznych. W przypadku wielu zasad, które są stosowane do użytkownika zostanie wyświetlony monit w momencie ich żądania wybierz zasady że chcieliby ma być przypisane do.

Na poniższym diagramie przedstawiono procedurę wysokiego poziomu służącą do utworzenia zasad dla istniejącego pakietu dostępu.

![Utworzenie procesu zasad](./media/entitlement-management-access-package-edit/policy-process.png)

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Kliknij przycisk **zasady** i następnie **Dodaj zasady**.

1. Wpisz nazwę i opis zasad.

    ![Tworzenie zasad za pomocą nazwy i opisu](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Na podstawie wybranych dla **użytkowników, którzy mogą zażądać dostępu**, wykonaj kroki w jednym z następujących sekcji zasady.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Edytuj istniejące zasady

Możesz edytować zasady w dowolnym momencie. Jeśli zmienisz zasadę datę wygaśnięcia, datę wygaśnięcia żądania, które są już oczekuje na zatwierdzenie lub zatwierdzony stan nie zmieni się.

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Kliknij przycisk **zasady** a następnie kliknij pozycję zasady, którą chcesz edytować.

    **Szczegóły zasad** zostanie otwarte okienko w dolnej części strony.

    ![Pakiet dostępu — okienko szczegółów zasad](./media/entitlement-management-access-package-edit/policy-details.png)

1. Kliknij przycisk **Edytuj** Aby edytować zasady.

    ![Pakiet dostępu — edytowanie zasad](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Po zakończeniu kliknij przycisk **aktualizacji**.

## <a name="directly-assign-a-user"></a>Bezpośrednio przypisać użytkownika

W niektórych przypadkach można bezpośrednio przypisać określonych użytkowników do pakietów programu access, tak, aby użytkownicy nie musieli przejść przez proces żądania dostępu do pakietu. Aby bezpośrednio przypisać użytkowników, pakiet dostępu musi mieć zasady, które umożliwia administratorowi przypisań bezpośrednich.

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij **przypisania**.

1. Kliknij przycisk **nowe przypisanie** otworzyć Dodaj użytkownika do dostępu do pakietu.

    ![Przypisania — Dodawanie użytkownika do dostępu do pakietu](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Kliknij przycisk **dodawania użytkowników** wybranym użytkownikom, którą chcesz przypisać dostęp do pakietu.

1. W **wybierz zasady** , wybierz zasady, które ma na liście [None (administrator przypisań bezpośrednich tylko)](#policy-none-administrator-direct-assignments-only) ustawienie.

    Jeśli ten pakiet dostępu nie ma tego typu zasad, możesz kliknąć **Utwórz nowe zasady** dodanie.

1. Ustaw datę i godzinę, chcesz, aby przypisania wybranych użytkowników w celu rozpoczęcia i zakończenia. Jeśli data zakończenia nie zostanie podany, ustawienia wygasania zasad będzie używany.

1. Opcjonalnie podaj uzasadnienie dla bezpośredniego przypisania dotyczących przechowywania danych.

1. Kliknij przycisk **Dodaj** bezpośrednie przypisywanie wybranych użytkowników do dostępu do pakietu.

    Po kilku chwilach kliknij **Odśwież** Aby wyświetlić użytkowników na liście przypisań.

## <a name="view-who-has-an-assignment"></a>Widok, kto ma przypisania

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Kliknij przycisk **przypisania** umożliwia wyświetlenie listy aktywne przypisania.

1. Kliknij określone przypisanie, aby wyświetlić dodatkowe szczegóły.

1. Umożliwia wyświetlenie listy przypisań, które nie miały wszystkie role zasobów poprawnie aprowizowana, kliknij stan filtr, a następnie wybierz **dostarczanie**.

    Można wyświetlić dodatkowe szczegóły na błędy dostarczania, znajdując odpowiednie żądanie użytkownika na **żądań** strony.

1. Aby wyświetlić wygasłe przypisania, kliknij stan filtr, a następnie wybierz **wygasłe**.

1. Plik CSV z listy filtrowanej kliknij, aby pobrać **Pobierz**.

## <a name="view-requests"></a>Wyświetlanie żądań

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Kliknij przycisk **żądań**.

1. Kliknij przycisk konkretnego żądania, aby wyświetlić dodatkowe szczegóły.

## <a name="view-a-requests-delivery-errors"></a>Wyświetl błędy dostarczania żądania

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Kliknij przycisk **żądań**.

1. Wybierz żądanie, które chcesz wyświetlić.

    Jeśli żądanie ma błędy dostarczania, będzie w stanie żądania **Undelivered** podstany. zostanie ona **dostarczony częściowo**.

    Jeśli występują błędy dostarczania w okienku szczegółów żądania, nastąpi liczbę błędów dostarczania.

1. Kliknij liczbę, aby zobaczyć wszystkie błędy dostarczania żądania.

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Możesz tylko anulować oczekujące żądanie, które jeszcze nie zostały dostarczone.

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Kliknij przycisk **żądań**.

1. Kliknij żądanie, którą chcesz anulować

1. W okienku szczegółów żądania kliknij **żądanie anulowania**.

## <a name="copy-my-access-portal-link"></a>Skopiuj link do portalu Moje dostępu

Większość użytkowników w katalogu można zalogować się do portalu Moje dostępu i automatycznie wyświetlić listę pakietów dostępu, które mogą żądać. Jednak dla zewnętrznych partnerów biznesowych, które nie są jeszcze w katalogu, należy wysłać im link, który może służyć do żądania dostępu do pakietu. Tak długo, jak pakiet dostępu jest włączona dla użytkowników zewnętrznych i mieć zasady dla katalogu użytkownika zewnętrznego, użytkownika zewnętrznego służy link do portalu Moje dostępu do żądania dostępu do pakietu.

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd Skopiuj **link do portalu Moje dostępu**.

    ![Omówienie pakietu dostępu — link do portalu Moje dostępu](./media/entitlement-management-shared/my-access-portal-link.png)

1. Wiadomości e-mail lub wysłać łącze do partnera biznesowego zewnętrznych. Mogą oni udostępnić link przy użyciu swoich użytkowników, aby zażądać dostępu do pakietu.

## <a name="change-the-hidden-setting"></a>Zmień ustawienie ukryty

Dostęp do pakietów są wykrywalni domyślnie. Oznacza to, jeśli zasady umożliwia użytkownikowi żądanie dostępu do pakietu, automatycznie uzyskają oni pakiet dostępu, na liście w portalu Moje dostępu.

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd kliknij **Edytuj**.

1. Ustaw **ukryty** ustawienie.

    Jeśli ustawiono **nie**, pakiet dostępu zostaną wyświetlone w portalu Moje dostępu użytkownika.

    Jeśli ustawiono **tak**, pakiet dostępu, nie będą wyświetlane w portalu Moje dostępu użytkownika. Jedynym sposobem, użytkownik może wyświetlić pakiet dostępu jest, czy mają one bezpośrednio **link do portalu Moje dostępu** do dostępu do pakietu.

## <a name="delete"></a>Usuwanie

Pakiet dostępu można usunąć tylko, jeśli go nie ma aktywnego użytkownika przypisań.

**Rola wymagań wstępnych:** Administrator użytkowników, właściciel katalogu lub dostęp do Menedżera pakietów

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **uzyskiwać dostęp do pakietów** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij **przypisania** i spowodować usunięcie dostępu dla wszystkich użytkowników.

1. W menu po lewej stronie kliknij **Przegląd** a następnie kliknij przycisk **Usuń**.

1. W wyświetlonym komunikat do usunięcia, kliknij przycisk **tak**.

## <a name="when-are-changes-applied"></a>Po zastosowaniu zmiany

W przystawce Zarządzanie uprawnieniem usługi Azure AD będzie przetwarzać zmiany zbiorcze dla przypisania i zasobów w pakietach dostęp kilka razy dziennie. Więc jeśli Przypisywanie lub zmienić role zasobów pakietu dostęp, może upłynąć do 24 godzin, ta zmiana ma zostać wykonane w usłudze Azure AD, oraz czas jego propagacji tych zmian do innych Microsoft Online Services lub połączonych aplikacji SaaS s. Jeśli zmiana wpływa na kilka obiektów, zmiana prawdopodobnie zajmie tylko kilka minut, aby zastosować w usłudze Azure AD, po którym inne składniki usługi Azure AD wykryje następnie ulec zmianie, a także zaktualizować aplikacji SaaS. Jeśli zmiana wpływa na tysiące obiektów, zmiana będzie trwać dłużej. Na przykład jeśli masz pakiet dostępu przy użyciu 2 aplikacji i przypisań 100 użytkowników i zdecydować dodać rolę witryny programu SharePoint do dostępu do pakietu, prawdopodobnie opóźnienie aż wszyscy użytkownicy należą do tej roli w lokacji programu SharePoint. Można monitorować postęp za pomocą dziennika inspekcji usługi Azure AD, dziennik inicjowania obsługi usługi Azure AD i dzienniki inspekcji w witrynie programu SharePoint.

## <a name="next-steps"></a>Kolejne kroki

- [Dodawanie właściciela katalogu lub dostęp do Menedżera pakietów](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Żądanie procesu i powiadomień e-mail](entitlement-management-process.md)

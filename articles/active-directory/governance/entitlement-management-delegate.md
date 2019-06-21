---
title: Deleguj zadania zarządzania uprawnień usługi Azure AD (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się więcej na temat ról, które można przypisać do delegowania zadań w usłudze Azure Active Directory, zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa0be8e2af7644564ba27e6d58fda09b1ae7bc7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191501"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Deleguj zadania zarządzania uprawnień usługi Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domyślnie administratorzy globalni i Administratorzy użytkowników, można tworzyć i zarządzać wszystkimi aspektami Zarządzanie uprawnieniami w usłudze Azure AD. Jednak użytkowników w ramach tych ról może nie wiedzieć, wszystkie scenariusze, w których wymagane są dostępu do pakietów. Zazwyczaj jest użytkowników w obrębie działów kto wiedzieć, którzy muszą współpracować.

Zamiast udzielania nieograniczonych uprawnień użytkowników niebędących administratorami, możesz nadawać użytkownikom najniższych uprawnień, których potrzebują do wykonywania swoich zadań i unikanie tworzenia powodujące konflikt lub prawa dostępu nieodpowiednie. W tym artykule opisano role, które można przypisać do różnych zadań w przystawce Zarządzanie uprawnieniem delegowania.

## <a name="delegate-example-for-departmental-adoption"></a>Przykład delegata dla działów wdrożenia

Aby dowiedzieć się, jak może delegować zadań Zarządzanie uprawnieniami, warto należy wziąć pod uwagę przykładem. Załóżmy, że Twoja organizacja ma pięć następujących użytkowników:

| Użytkownik | Dział | Uwagi |
| --- | --- | --- |
| Alicja | IT | Administrator globalny |
| Bob | Badania | Robert jest również właścicielem grupy badań |
| Carole | Badania |  |
| Dave | Marketing |  |
| ELISA | Marketing | ELISA jest również właścicielem aplikacji marketingu |

Badania i działów marketingu mają używać Zarządzanie uprawnieniami dla użytkowników. Alicja nie jest jeszcze gotowy do innych działów umożliwia zarządzanie uprawnieniami. Oto jeden ze sposobów, że Alicja może delegowania zadań w celu badania i działów marketingu.

1. Gdy Alicja tworzy nową zabezpieczeń usługi Azure AD grupy dla twórców wykazu i dodaje Roberta, Karolinę, Dave i Elisa jako członkowie tej grupy.

1. Alicja używa ustawień zarządzania uprawnienie, aby dodać tę grupę do roli Twórcy katalogu.

1. Karolina tworzy **badań** katalogu, a następnie dodaje Bob jako współwłaściciela tego katalogu. Bob dodaje grupy badań, którego właścicielem jest on w katalogu jako zasób, dzięki czemu może służyć w pakiecie dostęp do współpracy badań.

1. Tworzy Dave **marketingu** katalogu, a następnie dodaje Elisa jako współwłaściciela tego katalogu. ELISA dodaje marketingu aplikacji, którą jest ona właścicielem do katalogu jako zasób, dzięki czemu może służyć w pakiecie dostępu marketingowych współpracy.

Research oraz działów marketingu mogą teraz wykorzystywać Zarządzanie uprawnieniami. Bob, Karolinę, Dave i Elisa można tworzyć i zarządzać dostępu do pakietów w ich odpowiednich katalogów.

![Przykład delegat Zarządzanie uprawnieniem](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Uprawnienie Zarządzanie rolami

Zarządzanie uprawnieniami ma następujące role, które są specyficzne dla Zarządzanie uprawnieniami.

| Rola | Opis |
| --- | --- |
| Twórca katalogu | Tworzenie i Zarządzanie katalogami. Zazwyczaj IT administrator, który nie jest administratorem globalnym lub właściciel zasobu dla kolekcji zasobów. Osoba, która automatycznie tworzy wykaz staje się pierwszym właścicielem katalogu wykazu i można dodać katalogu dodatkowych właścicieli. |
| Właściciel katalogu | Edytowanie i zarządzanie nią istniejących wykazów. Zazwyczaj administrator IT lub właścicieli zasobów lub użytkownik, który został wyznaczony właściciel katalogu. |
| Dostęp do Menedżera pakietów | Edytowanie i zarządzanie nią wszystkie istniejące pakiety dostępu w katalogu. |

Ponadto wyznaczoną osobę zatwierdzającą i obiekt żądający pakietu dostępu również mieć uprawnienia, mimo że nie są to role.
 
* Osoba zatwierdzająca: Autoryzowane przy użyciu zasad, które akceptują lub odrzucają żądania, uzyskiwania dostępu do pakietów, mimo że nie mogą zmieniać definicji pakietu dostępu.
* Obiekt żądający: Uprawnień przy użyciu zasad dostępu do pakietu, aby zażądać dostępu do pakietu.

Poniższa tabela zawiera listę zadań, wykonujących dla tych ról.

| Zadanie | Twórca katalogu | Właściciel katalogu | Dostęp do Menedżera pakietów | Osoby zatwierdzającej |
| --- | :---: | :---: | :---: | :---: |
| [Utwórz nowy katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Dodaj zasób do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Edytuj katalog](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Usuwanie katalogu](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Dodawanie właściciela katalogu lub Menedżera pakietów dostępu do wykazu](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md) |  | :heavy_check_mark: |  |  |
| [Zarządzanie rolami zasobu w pakiecie dostępu](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Określ, kto może żądać dostępu do pakietu](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Bezpośrednio przypisać użytkownika do dostępu do pakietu](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Widok, kto ma przypisania dostępu do pakietu](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Wyświetlanie żądań pakietu dostępu](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Wyświetl błędy dostarczania żądania](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Anulowanie oczekującego żądania](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ukryj pakietu dostępu](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Usuń pakiet dostępu](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zatwierdź żądanie dostępu](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Wymagane role, aby dodać zasoby do wykazu

Administrator globalny można dodać lub usunąć wszystkie grupy (grupy zabezpieczeń utworzonej w chmurze lub utworzyć chmury grup usługi Office 365), aplikacji lub witryny usługi SharePoint Online w wykazie. Administrator użytkowników można dodać lub usunąć grupę lub aplikację w wykazie.

Dla użytkownika, który nie jest administratorem globalnym lub administratorem użytkownika, aby dodać grupy, aplikacji lub witryn usługi SharePoint Online do wykazu, że użytkownik musi mieć *zarówno* wymagane usługi Azure AD directory rola i wykaz właściciela uprawnień Rola zarządzania. W poniższej tabeli przedstawiono kombinacje roli, które są wymagane, aby dodać zasoby do wykazu. Aby usunąć zasoby z katalogu, musi mieć te same role.

| Rola katalogu usługi Azure AD | Uprawnienia roli zarządzania | Można dodać grupy zabezpieczeń | Można dodać grupy usługi Office 365 | Można dodać aplikację | Można dodać do witryny usługi SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrator globalny](../users-groups-roles/directory-assign-admin-roles.md) | Nie dotyczy |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrator użytkowników](../users-groups-roles/directory-assign-admin-roles.md) | Nie dotyczy |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrator usługi Intune](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrator programu Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator usługi teams](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator programu SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| [Administrator aplikacji w chmurze](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| Użytkownik | Właściciel katalogu | Tylko wtedy, gdy grupa właściciela | Tylko wtedy, gdy grupa właściciela | Tylko wtedy, gdy właściciel aplikacji |  |

## <a name="add-a-catalog-creator"></a>Dodaj twórcy katalogu

Aby delegować tworzenie wykazu, możesz dodać użytkowników do roli Twórcy katalogu.  Możesz dodać poszczególnych użytkowników lub dla wygody można dodać grupy, której członkowie będą w stanie utworzyć katalogów. Wykonaj następujące kroki, aby przypisać użytkownika do roli Twórcy katalogu.

**Rola wymagań wstępnych:** Administrator globalny lub administrator użytkowników

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie w **Zarządzanie uprawnieniami** kliknij **ustawienia**.

1. Kliknij pozycję **Edytuj**.

1. W **delegować Zarządzanie uprawnieniami** kliknij **Dodaj katalog dla twórców** do wybierz użytkowników lub grupy, którzy będą członkami tej roli zarządzania uprawnienie.

1. Kliknij pozycję **Wybierz**.

1. Kliknij pozycję **Zapisz**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Dodawanie właściciela katalogu lub dostęp do Menedżera pakietów

Jeśli chcesz delegować Zarządzanie katalogu lub dostępu do pakietów w katalogu, możesz dodać użytkowników do właściciela katalogu lub dostępu do pakietu menedżera ról. Kto tworzy wykaz staje się pierwszym właściciela katalogu. Wykonaj następujące kroki, aby przypisać użytkownika do właściciela katalogu lub rolę Menedżera pakietów dostępu.

**Rola wymagań wstępnych:** Administrator użytkownika lub właściciela katalogu

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **katalogi** , a następnie otwórz katalogu, którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij **role i Administratorzy**.

1. Kliknij przycisk **Dodawanie właścicieli** lub **dodać menedżerów pakietów dostępu** zaznacz składniki dla tych ról.

1. Kliknij przycisk **wybierz** można dodać te składowe.

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj osoby zatwierdzające](entitlement-management-access-package-edit.md#policy-request)
- [Dodawanie zasobów do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

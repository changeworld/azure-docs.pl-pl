---
title: Delegowanie zadań w usłudze Azure AD uprawnień zarządzanie (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się więcej na temat ról, które można przypisać w celu delegowania zadań w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4e21fdef5be09148f001ab6f66f87dd270ccf54
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618266"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegowanie zadań w usłudze Azure AD uprawnień zarządzanie (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domyślnie Administratorzy globalni i Administratorzy użytkowników mogą tworzyć wszystkie aspekty zarządzania prawami usługi Azure AD i zarządzać nimi. Jednak użytkownicy z tych ról mogą nie wiedzieć o wszystkich scenariuszach, w których wymagane są pakiety dostępu. Zazwyczaj są to użytkownicy w działach, którzy wiedzą, kto musi współpracować.

Zamiast udzielania nieograniczonych uprawnień administratorom niebędącym administratorami, można udzielić użytkownikom najniższych uprawnień, których potrzebują do wykonywania swoich zadań, i uniknąć tworzenia sprzecznych lub nieodpowiednich praw dostępu. W tym artykule opisano role, które można przypisać w celu delegowania różnych zadań w usłudze zarządzania uprawnieniami.

## <a name="delegate-example-for-departmental-adoption"></a>Przykład delegata dla przyjęcia działu

Aby zrozumieć, jak można delegować zadania w usłudze zarządzania uprawnieniami, warto rozważyć przykład. Załóżmy, że Twoja organizacja ma pięć następujących użytkowników:

| Użytkownik | Dział | Uwagi |
| --- | --- | --- |
| Alicja | IT | Administrator globalny |
| Bob | Badania | Robert jest również właścicielem grupy badań |
| Carole | Badania |  |
| Dave | Marketing |  |
| Antygen | Marketing | Test ELISA jest również właścicielem aplikacji marketingowej |

Działy badawcze i marketingowe chcą korzystać z zarządzania prawami dla swoich użytkowników. Alicja nie jest jeszcze gotowa do użycia przez inne działy zarządzania prawami. Oto jeden ze sposobów, aby Alicja mógł delegować zadania do działów badawczych i marketingowych.

1. Alicja tworzy nową grupę zabezpieczeń usługi Azure AD dla twórców wykazu i dodaje Robert, Karoliny, Dave i test ELISA jako członków tej grupy.

1. Alicja używa ustawień zarządzania uprawnieniami do dodawania tej grupy do roli twórców katalogu.

1. Karoliny tworzy katalog **badań** i dodaje Robert jako współwłaściciel tego katalogu. Robert dodaje grupę badawczą, do której należy katalog jako zasób, dzięki czemu można jej używać w pakiecie dostępu do współpracy badawczej.

1. Dave tworzy katalog **marketingu** i dodaje test ELISA jako współwłaściciel tego katalogu. Test ELISA dodaje aplikację marketingową do katalogu jako zasób, dzięki czemu można jej używać w pakiecie dostępu do współpracy marketingowej.

Teraz działy badawcze i marketingowe mogą korzystać z zarządzania prawami. Robert, Karoliny, Dave i ELISA mogą tworzyć i zarządzać pakietami dostępu w odpowiednich katalogach.

![Przykład delegata zarządzania uprawnieniami](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Role zarządzania uprawnieniami

Zarządzanie prawami obejmuje następujące role, które są specyficzne dla zarządzania prawami.

| Role | Opis |
| --- | --- |
| Twórca katalogu | Utwórz wykazy i zarządzaj nimi. Zazwyczaj administrator IT, który nie jest administratorem globalnym lub właścicielem zasobu dla kolekcji zasobów. Osoba tworząca katalog automatycznie stanie się pierwszym właścicielem katalogu i może dodać dodatkowych właścicieli katalogu. |
| Właściciel katalogu | Edytuj istniejące wykazy i zarządzaj nimi. Zazwyczaj administrator IT lub właściciele zasobów lub użytkownik, który wyznaczył właściciela katalogu. |
| Menedżer pakietów dostępu | Edytuj wszystkie istniejące pakiety dostępu i zarządzaj nimi w ramach wykazu. |

Ponadto Wyznaczeni osoby zatwierdzające i osoba żądająca pakietu dostępu mają również prawa, chociaż nie są rolami.
 
* Osoby zatwierdzającej Autoryzowany przez zasady do zatwierdzania lub odrzucania żądań dostępu do pakietów, chociaż nie mogą zmieniać definicji pakietów dostępu.
* Obiektu żądającego Autoryzowane przez zasady pakietu dostępu, aby zażądać tego pakietu dostępu.

Poniższa tabela zawiera listę zadań, które mogą wykonywać te role.

| Zadanie | Twórca katalogu | Właściciel katalogu | Menedżer pakietów dostępu | Osoby zatwierdzającej |
| --- | :---: | :---: | :---: | :---: |
| [Utwórz nowy katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Dodawanie zasobu do wykazu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Edytowanie katalogu](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Usuwanie wykazu](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Dodawanie właściciela katalogu lub Menedżera pakietów dostępu do wykazu](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Zarządzanie rolami zasobów w pakiecie dostępu](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Określ, kto może zażądać pakietu dostępu](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Bezpośrednie przypisywanie użytkownika do pakietu dostępu](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Wyświetl osoby, które mają przypisanie do pakietu dostępu](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Wyświetlanie żądań pakietu dostępu](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Wyświetl błędy dostarczania żądania](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Anulowanie oczekującego żądania](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ukryj pakiet dostępu](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Usuwanie pakietu dostępu](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zatwierdź żądanie dostępu](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Role wymagane do dodawania zasobów do wykazu

Administrator globalny może dodawać lub usuwać dowolną grupę (grupy zabezpieczeń utworzone w chmurze lub grupy Office 365 utworzone w chmurze), aplikację lub witrynę usługi SharePoint Online w wykazie. Administrator użytkowników może dodać lub usunąć dowolną grupę lub aplikację w wykazie.

Użytkownik, który nie jest administratorem globalnym lub administratorem użytkownika, w celu dodania grup, aplikacji lub witryn usługi SharePoint Online do wykazu, musi mieć *zarówno* wymaganą rolę w katalogu usługi Azure AD, jak i rolę zarządzania uprawnieniem właściciela katalogu. W poniższej tabeli wymieniono kombinacje ról, które są wymagane do dodawania zasobów do wykazu. Aby usunąć zasoby z wykazu, musisz mieć te same role.

| Rola katalogu usługi Azure AD | Rola zarządzania uprawnieniami | Może dodać grupę zabezpieczeń | Może dodać grupę Office 365 | Można dodać aplikację | Może dodać witrynę usługi SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrator globalny](../users-groups-roles/directory-assign-admin-roles.md) | Nie dotyczy |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrator użytkowników](../users-groups-roles/directory-assign-admin-roles.md) | Nie dotyczy |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrator usługi Intune](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrator programu Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator usługi Teams](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator programu SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| [Administrator aplikacji w chmurze](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| Użytkownik | Właściciel katalogu | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel aplikacji |  |

## <a name="add-a-catalog-creator"></a>Dodaj twórcę katalogu

Jeśli chcesz delegować tworzenie wykazu, Dodaj użytkowników do roli twórcy katalogu.  Można dodać poszczególnych użytkowników lub wygodę może dodać grupę, której członkowie są w stanie tworzyć wykazy. Wykonaj następujące kroki, aby przypisać użytkownika do roli twórcy katalogu.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie w sekcji **Zarządzanie prawami** kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **Edytuj**.

1. W sekcji **Zarządzanie uprawnieniami delegowania** kliknij pozycję **Dodaj twórców wykazu** , aby wybrać użytkowników lub grupy, którzy będą członkami tej roli zarządzania uprawnieniem.

1. Kliknij przycisk **wybierz**.

1. Kliknij polecenie **Zapisz**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Dodawanie właściciela katalogu lub Menedżera pakietów programu Access

Aby delegować Zarządzanie wykazem lub pakietami dostępu do katalogu, należy dodać użytkowników do właściciela katalogu lub uzyskać dostęp do ról Menedżera pakietów. Użytkownik, który tworzy katalog, zostaje pierwszym właścicielem katalogu. Wykonaj następujące kroki, aby przypisać użytkownika do roli właściciela katalogu lub Menedżera pakietów dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję wykazy, a następnie otwórz wykaz, do którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij pozycję **role i Administratorzy**.

1. Kliknij pozycję **Dodaj właścicieli** lub **Dodaj menedżerów pakietów dostępu** , aby wybrać członków dla tych ról.

1. Kliknij pozycję **Wybierz** , aby dodać tych członków.

## <a name="next-steps"></a>Następne kroki

- [Dodaj osoby zatwierdzające](entitlement-management-access-package-edit.md#policy-request)
- [Dodawanie zasobów do wykazu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

---
title: Delegowanie i role w zarządzaniu uprawnieniami — usługa Azure AD
description: Dowiedz się, jak delegować zarządzanie dostępem od administratorów IT do menedżerów działów i menedżerów projektów, aby mogli samodzielnie zarządzać dostępem.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261842"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegowanie i role w zarządzaniu uprawnieniami usługi Azure AD

Domyślnie administratorzy globalni i administratorzy użytkowników mogą tworzyć wszystkie aspekty zarządzania uprawnieniami usługi Azure AD i zarządzać nimi. Jednak użytkownicy w tych rolach mogą nie znać wszystkich sytuacji, w których pakiety dostępu są wymagane. Zazwyczaj to użytkownicy w odpowiednich działach, zespołach lub projektach wiedzą, z kim współpracują, korzystając z jakich zasobów i jak długo. Zamiast udzielać nieograniczonych uprawnień użytkownikom niebędącym administratorami, można przyznać użytkownikom najmniejsze uprawnienia potrzebne do wykonania zadania i uniknąć tworzenia sprzecznych lub nieodpowiednich praw dostępu.

W tym klipie wideo przedstawiono informacje na temat sposobu delegowania nadzoru dostępu od administratora IT do użytkowników, którzy nie są administratorami.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Przykład delegowania

Aby zrozumieć, jak można delegować nadzoru dostępu w zarządzaniu uprawnieniami, pomaga rozważyć przykład. Załóżmy, że organizacja ma następującego administratora i menedżerów.

![Delegowanie z administratora IT do menedżerów](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Jako administrator IT, Hana ma kontakty w każdym dziale - Mamta w marketingu, Mark in Finance i Joe in Legal, którzy są odpowiedzialni za zasoby swojego działu i krytyczne treści biznesowe.

Za pomocą zarządzania uprawnieniami można delegować zarządzanie dostępem do tych nie-administratorów, ponieważ to oni wiedzą, którzy użytkownicy potrzebują dostępu, jak długo i do jakich zasobów. Dzięki temu odpowiednie osoby zarządzają dostępem do swoich działów.

Oto jeden ze sposobów, że Hana może delegować zarządzanie dostępem do działu marketingu, finansów i prawnych.

1. Hana tworzy nową grupę zabezpieczeń usługi Azure AD i dodaje Mamta, Mark i Joe jako członków grupy.

1. Hana dodaje tę grupę do roli twórców katalogu.

    Mamta, Mark i Joe mogą teraz tworzyć katalogi dla swoich działów, dodawać zasoby potrzebne ich działom i wykonywać dalsze delegowanie w katalogu.

    Należy zauważyć, że Mamta, Mark i Joe nie widzą siebie nawzajem katalogów.

1. Mamta tworzy katalog **marketingowy,** który jest kontenerem zasobów.

1. Mamta dodaje zasoby, które jej dział marketingu posiada do tego katalogu.

1. Mamta może dodać dodatkowe osoby ze swojego działu jako właścicieli katalogów dla tego katalogu. Pomaga to współużytkować obowiązki związane z zarządzaniem katalogiem.

1. Mamta może dodatkowo delegować tworzenie i zarządzanie pakietami dostępu w katalogu Marketing do kierowników projektów w dziale marketingu. Może to zrobić, przypisując je do roli menedżera pakietów dostępu. Menedżer pakietów dostępu może tworzyć pakiety dostępu i zarządzać nimi. 

Na poniższym diagramie przedstawiono katalogi z zasobami dla działów marketingu, finansów i działów prawnych. Korzystając z tych katalogów, menedżerowie projektów mogą tworzyć pakiety dostępu dla swoich zespołów lub projektów.

![Przykład delegata zarządzania uprawnieniami](./media/entitlement-management-delegate/elm-delegate.png)

Po przekazaniu dział marketingu może mieć role podobne do poniższej tabeli.

| Użytkownik | Rola zadania | Rola usługi Azure AD | Rola zarządzania uprawnieniami |
| --- | --- | --- | --- |
| Hana | Administrator IT | Administrator globalny lub administrator użytkownika |  |
| Mamta (Mamta) | Kierownik marketingu | Użytkownik | Twórca katalogu i właściciel katalogu |
| Bob | Kierownik marketingu | Użytkownik | Właściciel katalogu |
| Jessica | Kierownik projektu marketingowego | Użytkownik | Menedżer pakietów dostępu |

## <a name="entitlement-management-roles"></a>Role zarządzania uprawnieniami

Zarządzanie uprawnieniami ma następujące role, które są specyficzne dla zarządzania uprawnieniami.

| Rola zarządzania uprawnieniami | Opis |
| --- | --- |
| Twórca katalogu | Tworzenie katalogów i zarządzanie nimi. Zazwyczaj administrator IT, który nie jest administratorem globalnym lub właścicielem zasobów dla kolekcji zasobów. Osoba, która tworzy katalog automatycznie staje się pierwszym właścicielem katalogu i może dodać dodatkowych właścicieli katalogu. Twórca katalogu nie może zarządzać katalogami, których nie jest właścicielem, i nie może dodawać zasobów, których nie posiada, do katalogu. Jeśli twórca katalogu musi zarządzać innym katalogiem lub dodawać zasoby, których nie jest właścicielem, może zażądać, aby był współwłaścicielem tego katalogu lub zasobu. |
| Właściciel katalogu | Edytowanie istniejących katalogów i zarządzanie nimi. Zazwyczaj administrator IT lub właściciele zasobów lub użytkownik, którego wyznaczył właściciel katalogu. |
| Menedżer pakietów dostępu | Edytuj wszystkie istniejące pakiety dostępu i zarządzaj nimi w katalogu. |

Ponadto wyznaczona osoba zatwierdzająca i osoba żądająca pakietu dostępu również mają prawa, chociaż nie są to role.

| Prawe | Opis |
| --- | --- |
| Approver | Autoryzowane przez zasady do zatwierdzania lub odrzucania żądań dostępu do pakietów, chociaż nie mogą zmienić definicji pakietów dostępu. |
| Requestor | Autoryzowany przez zasady pakietu dostępu do żądania tego pakietu dostępu. |

W poniższej tabeli wymieniono zadania, które mogą wykonywać role zarządzania uprawnieniami.

| Zadanie | Administrator | Twórca katalogu | Właściciel katalogu | Menedżer pakietów dostępu |
| --- | :---: | :---: | :---: | :---: |
| [Delegowanie do twórcy katalogu](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Dodawanie połączonej organizacji](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Tworzenie nowego katalogu](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Dodawanie zasobu do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Dodawanie właściciela katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Edytowanie katalogu](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Usuwanie katalogu](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegowanie do menedżera pakietów dostępu](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Usuwanie menedżera pakietów dostępu](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Tworzenie nowego pakietu dostępu w katalogu](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Zmienianie ról zasobów w pakiecie dostępu](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Tworzenie i edytowanie zasad](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Bezpośrednie przypisywanie użytkownika do pakietu dostępu](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Wyświetlanie, kto ma przypisanie do pakietu dostępu](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Wyświetlanie żądań pakietu dostępu](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Wyświetlanie błędów dostarczenia żądania](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ponowne przetwarzanie żądania](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Anulowanie oczekującego żądania](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ukrywanie pakietu dostępu](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Usuwanie pakietu dostępu](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Wymagane role do dodawania zasobów do katalogu

Administrator globalny może dodać lub usunąć dowolną grupę (utworzone w chmurze grupy zabezpieczeń lub utworzone w chmurze grupy usługi Office 365), aplikację lub witrynę usługi SharePoint Online w katalogu. Administrator użytkownika może dodać lub usunąć dowolną grupę lub aplikację w katalogu.

Aby użytkownik, który nie jest administratorem globalnym ani administratorem użytkownika, aby dodać grupy, aplikacje lub witryny usługi SharePoint Online do katalogu, musi mieć zarówno wymaganą rolę katalogu usługi Azure AD, *jak* i rolę zarządzania uprawnieniami właściciela katalogu. W poniższej tabeli wymieniono kombinacje ról, które są wymagane do dodania zasobów do katalogu. Aby usunąć zasoby z katalogu, musisz mieć te same role.

| Rola katalogu usługi Azure AD | Rola zarządzania uprawnieniami | Można dodać grupę zabezpieczeń | Można dodać grupę usługi Office 365 | Można dodać aplikację | Można dodać witrynę usługi SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrator globalny](../users-groups-roles/directory-assign-admin-roles.md) | Nie dotyczy |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrator użytkownika](../users-groups-roles/directory-assign-admin-roles.md) | Nie dotyczy |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrator usługi Intune](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrator programu Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator usługi Teams](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator programu SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrator aplikacji](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| [Administrator aplikacji w chmurze](../users-groups-roles/directory-assign-admin-roles.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| Użytkownik | Właściciel katalogu | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel aplikacji |  |

Aby określić najmniej uprzywilejowaną rolę zadania, można również odwołać się do [ról administratora według zadań administratora w usłudze Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Następne kroki

- [Delegowanie nadzoru dostępu do twórców katalogu](entitlement-management-delegate-catalog.md)
- [Tworzenie katalogu zasobów i zarządzanie nim](entitlement-management-catalog-create.md)

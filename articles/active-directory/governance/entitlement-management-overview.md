---
title: Co to jest zarządzanie uprawnieniami w usłudze Azure AD? (Wersja zapoznawcza) — usługa Azure Active Directory
description: Omówienie usługi Azure Active Directory, zarządzanie uprawnieniami i jak go używać do zarządzania dostępem do grup, aplikacji i witryn usługi SharePoint Online dla użytkowników wewnętrznych i zewnętrznych.
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
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efd3ff8a6e7ddf2aa6242cc322d8a6536a6bd26b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474070"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Co to jest zarządzanie uprawnieniami w usłudze Azure AD? (Wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pracownikom w organizacjach muszą mieć dostęp do różnych grup, aplikacji i witryn do wykonania swojej pracy. Zarządzanie ten dostęp jest trudne. W większości przypadków istnieje nie zorganizowanym lista wszystkich zasobów, użytkownik musi dla projektu. Menedżer projektu ma dobrą znajomością zasoby potrzebne, osoby zaangażowane oraz jak długo ważny projekt. Jednak Menedżer projektu zwykle nie ma uprawnienia do zatwierdzania lub udostępnienia innym osobom. W tym scenariuszu stanie się bardziej skomplikowany, podczas pracy z zewnętrznego osób i firm.

Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) ułatwia zarządzanie dostępem do grup, aplikacji i witryn usługi SharePoint Online dla użytkowników wewnętrznych, a także użytkownikom spoza organizacji.

## <a name="why-use-entitlement-management"></a>Dlaczego warto używać uprawnienia zarządzania?

Przedsiębiorstwa to organizacje często stają przed wyzwaniem zarządzanie dostępem do zasobów, takich jak:

- Użytkownicy może nie wiedzieć, jakiego dostępu powinny mieć
- Użytkownicy mogą mieć problemy z lokalizowaniem prawa osób lub odpowiednich zasobów
- Gdy użytkownikom znajdowanie i uzyskują dostęp do zasobu, mogą mieć dłużej, niż jest to wymagane do celów biznesowych dostęp do

Te problemy są stwarzane dla użytkowników, którzy muszą mieć dostęp z innego katalogu, na przykład użytkowników zewnętrznych, które pochodzą z organizacji łańcucha dostaw lub innych partnerów biznesowych. Na przykład:

- Organizacje mogą nie wiedzieć, konkretne osoby w innych katalogach, aby można było zaprosić ich
- Nawet wtedy, gdy organizacjach udało się zaprosić tych użytkowników, organizacje mogą nie Pamiętaj, aby zarządzać wszystkimi dostęp użytkownika spójnie

Zarządzanie uprawnieniami w usłudze Azure AD może pomóc wyeliminować te problemy.

## <a name="what-can-i-do-with-entitlement-management"></a>Co można zrobić za pomocą funkcji zarządzania uprawnień?

Oto kilka możliwości zarządzania uprawnień:

- Tworzenie pakietów powiązane zasoby, które użytkownicy mogą żądać
- Zdefiniuj reguły dotyczące żądania zasobów i wygaśnięcia dostępu
- Zarządzanie cyklem życia dostępu dla użytkowników wewnętrznych i zewnętrznych
- Delegowanie zarządzania zasobami
- Wyznaczanie osoby zatwierdzające zatwierdzanie żądań
- Utwórz raporty w celu śledzenia historii

Omówienie zarządzania tożsamościami oraz zarządzanie uprawnieniami Obejrzyj poniższy klip wideo z konferencji Ignite 2018 r.:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Jakie zasoby można zarządzać?

Poniżej przedstawiono typy zasobów, możesz zarządzać dostępem do zarządzania uprawnień:

- Grupy zabezpieczeń usługi Azure AD
- Grupy usługi Office 365
- Usługa Azure AD aplikacje dla przedsiębiorstw, w tym aplikacji SaaS i zintegrowane niestandardowe obsługujących aplikacji federacyjnych lub inicjowania obsługi administracyjnej
- I SharePoint Online zbiorów witryn

Można także kontrolować dostęp do innych zasobów, które korzystają z grup zabezpieczeń usługi Azure AD lub grup usługi Office 365.  Na przykład:

- Możesz nadać użytkownikom licencje usługi Microsoft Office 365 przy użyciu grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i konfigurowanie [Licencjonowanie na podstawie grupy](../users-groups-roles/licensing-groups-assign.md) dla tej grupy
- Można umożliwić użytkownikom dostęp do zarządzania zasobami platformy Azure przy użyciu grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i utworzenie [przypisania roli Azure](../../role-based-access-control/role-assignments-portal.md) dla tej grupy

## <a name="what-are-access-packages-and-policies"></a>Co to są pakiety dostępu i zasad?

Zarządzanie uprawnieniami wprowadzono pojęcie *dostępu do pakietu*. Pakiet dostępu to zbiór wszystkie zasoby, które użytkownik chce pracować nad projektem lub wykonywać swoje zadania. Zasoby obejmują dostęp do grup, aplikacji lub witryny. Dostępu do pakietów umożliwiają zarządzanie dostępem dla pracowników wewnętrznych i również użytkownikom spoza organizacji. Dostęp do pakietów są zdefiniowane w kontenery wywoływane *katalogi*.

Dostęp do pakietów również zawierać co najmniej jeden *zasady*. Zasady określają reguły lub guardrails dostępu pakietu dostępu do. Włączanie zasad wymusza, że tylko użytkownicy przyznano im dostęp do odpowiednich zasobów i odpowiednią ilość czasu.

![Pakiet dostępu i zasad](./media/entitlement-management-overview/elm-overview-access-package.png)

Przy użyciu pakietu dostęp i jej zasad definiuje Menedżera pakietów dostępu:

- Zasoby
- Role użytkowników muszą dla zasobów
- Użytkowników wewnętrznych i zewnętrznych użytkowników, którzy są uprawnione do żądania dostępu
- Proces zatwierdzania i użytkowników, którzy mogli zatwierdzać lub odrzucać dostępu
- Czas trwania dostępu użytkownika

Na poniższym diagramie przedstawiono przykład różne elementy w przystawce Zarządzanie uprawnieniem. Pokazuje dwa pakiety dostępu do przykładu.

- **Dostęp do pakietu 1** zawiera pojedynczą grupę jako zasób. Dostęp jest zdefiniowana za pomocą zasad, umożliwiająca zestaw użytkowników w katalogu, aby poprosić o dostęp.
- **Dostęp do pakietu 2** obejmuje grupę, aplikacji i witryną usługi SharePoint Online jako zasoby. Dostęp jest zdefiniowana z dwóch różnych zasad. Pierwszym zasada włącza zbiór użytkowników w katalogu, aby poprosić o dostęp. Drugie zasady umożliwia użytkownikom w katalogu usługi zewnętrzne żądanie dostępu.

![Uprawnienie do zarządzania — omówienie](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Użytkownicy zewnętrzni

Korzystając z [usługi Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) zaprosić doświadczenia, musi już wiedzieć, że adresy e-mail użytkowników zewnętrznego gościa chcesz przenieść do katalogu zasobów i pracować. Ta działa wspaniałe, gdy pracujesz nad projektem mniejszych lub krótkoterminowej i wiesz już, wszystkich uczestników, ale jest trudniejsze do zarządzania, jeśli masz wielu użytkowników, którą chcesz pracować, lub jeśli uczestników zmieniać wraz z upływem czasu.  Na przykład użytkownik może być Praca z innej organizacji i mieć jeden punkt kontaktu z tej organizacji, ale wraz z upływem czasu dodatkowe użytkownicy z organizacji będą także potrzebować dostępu.

Za pomocą funkcji zarządzania uprawnienie można zdefiniować zasady, które zezwalają na użytkowników z organizacji, które określisz, które także używają usługi Azure AD, aby mieć możliwość żądania dostępu do pakietu. Można określić, czy zatwierdzenie jest wymagane i uzyskać dostęp do daty wygaśnięcia. Jeśli zatwierdzenie jest wymagane, można również wyznaczyć jako osoba zatwierdzająca jeden lub więcej użytkowników z organizacji zewnętrznego, który użytkownik wcześniej zaproszony — ponieważ mogą one mieć wiedzieć, których użytkowników zewnętrznych z organizacji muszą mieć dostęp. Po skonfigurowaniu pakietu dostępu, możesz wysłać łącze do dostępu do pakietu do osoby kontaktowej w organizację zewnętrzną. Ten kontakt można udostępniać innym użytkownikom w organizacji zewnętrznych i używają tego linku, aby zażądać dostępu do pakietu.  Użytkownicy z organizacji, którzy już zostali zaproszeni do katalogu umożliwia również ten link.

Gdy żądanie zostanie zatwierdzone, zarządzanie uprawnieniami przydzieli użytkownikowi wystarczających praw dostępu, które mogą obejmować zapraszania użytkownika, jeśli tak nie jest już w katalogu. Usługa Azure AD automatycznie utworzy konto B2B dla nich.  Należy pamiętać, że administrator może być wcześniej bardzo ograniczona organizacje, które są dozwolone dla pracy zespołowej, ustawiając [B2B blokują lub zezwalają na liście](../b2b/allow-deny-list.md) do zezwalania lub blokowania zaproszenia do innych organizacji.  Jeśli użytkownik nie jest dozwolona przez listę dozwolonych lub zablokowanych, a następnie nie będzie można zaprosić.

Ponieważ nie mają dostępu użytkownika zewnętrznego do ostatniego nieskończoność, można określić datę wygaśnięcia w zasadach, takie jak 180 dni. Po upływie 180 dni jeśli ich dostęp nie zostanie odnowiona, zarządzanie uprawnieniami spowoduje usunięcie wszystkich dostęp skojarzony z tym pakietem dostępu.  Jeśli użytkownik został zaproszony za pośrednictwem zarządzania uprawnienie nie ma innych dostępu do pakietu przypisań, następnie po utracą swoje ostatnie przypisanie ich B2B konto będzie być zablokowane z logowania przez 30 dni i później usunięte.  Zapobiega to niepotrzebne konta.  

## <a name="terminology"></a>Terminologia

Aby lepiej zrozumieć, zarządzanie uprawnieniami i jego dokumentacji, należy przejrzeć następujące warunki.

| Termin lub pojęcie | Opis |
| --- | --- |
| Zarządzanie uprawnieniami | Usługa, która przypisuje odwołuje i zarządza dostępu do pakietów. |
| dostęp do pakietu | Kolekcja uprawnień i zasad do zasobów, które użytkownicy mogą żądać. Pakiet dostępu zawsze znajduje się w katalogu. |
| żądanie dostępu | Żądanie dostępu do pakietu dostępu. Żądanie jest zazwyczaj przechodzi przez przepływ pracy. |
| policy | Zestaw reguł definiuje cyklu życia dostępu, np. jak użytkownicy uzyskują dostęp, kto może zatwierdzać i jak długo użytkownicy mają dostęp. Przykładowe zasady umożliwiają dostęp pracowników i dostępu zewnętrznego. |
| catalog | Kontener powiązane zasoby i dostęp do pakietów. |
| Ogólne katalogu | Wbudowane wykaz, który jest zawsze dostępna. Aby dodać zasoby do katalogu ogólne wymaga pewnych uprawnień. |
| resource | Zasób lub usługę (np. grupy, aplikacji lub witryny), który użytkownik może mieć uprawnienia do. |
| Typ zasobu | Typ zasobu, który zawiera grupy, aplikacje i witryny usługi SharePoint Online. |
| zasób roli | Kolekcja uprawnień skojarzonych z zasobem. |
| Katalog zasobów | Katalog, który ma co najmniej jeden zasób do udostępniania. |
| przypisani użytkownicy | Przypisanie pakietu dostęp do użytkownika lub grupy. |
| Włącz | Proces udostępniania dla użytkowników zażądać dostępu do pakietu. |

## <a name="roles-and-permissions"></a>Role i uprawnienia

Zarządzanie uprawnieniami ma różne role, w oparciu o funkcję zadania.

| Rola | Opis |
| --- | --- |
| [Administrator użytkowników](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) | Zarządzaj wszystkimi aspektami Zarządzanie uprawnieniami.<br/>Tworzenie użytkowników i grup. |
| Twórca katalogu | Tworzenie i Zarządzanie katalogami. Zazwyczaj administrator IT lub właściciel zasobu. Osoba, która automatycznie tworzy wykaz staje się pierwszym właściciela katalogu wykazu. |
| Właściciel katalogu | Edytowanie i zarządzanie nią istniejących wykazów. Zazwyczaj administrator IT lub właściciel zasobu. |
| Dostęp do Menedżera pakietów | Edytowanie i zarządzanie nią wszystkie istniejące pakiety dostępu w katalogu. |
| Osoby zatwierdzającej | Zatwierdź żądania, uzyskiwania dostępu do pakietów. |
| Obiekt żądający | Żądanie dostępu do pakietów. |

W poniższej tabeli wymieniono uprawnienia dla każdej z tych ról.

| Zadanie | Administrator użytkownika | Twórca katalogu | Właściciel katalogu | Dostęp do Menedżera pakietów | Osoby zatwierdzającej |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Utwórz nowy pakiet dostępu w wykazie ogólne](entitlement-management-access-package-create.md) | :heavy_check_mark: |  :heavy_check_mark: |  |  |  |
| [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md) | :heavy_check_mark: |   | :heavy_check_mark: |  |  |
| [Dodaj/Usuń role zasobów z pakietu dostępu](entitlement-management-access-package-edit.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Określ, kto może żądać dostępu do pakietu](entitlement-management-access-package-edit.md#add-a-new-policy) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Bezpośrednio przypisać użytkownika do dostępu do pakietu](entitlement-management-access-package-edit.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Widok, kto ma przypisania dostępu do pakietu](entitlement-management-access-package-edit.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Wyświetlanie żądań pakietu dostępu](entitlement-management-access-package-edit.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Wyświetl błędy dostarczania żądania](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Anulowanie oczekującego żądania](entitlement-management-access-package-edit.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ukryj pakietu dostępu](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Usuń pakiet dostępu](entitlement-management-access-package-edit.md#delete) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zatwierdź żądanie dostępu](entitlement-management-request-approve.md) |  |  |  |  | :heavy_check_mark: |
| [Tworzenie katalogu](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Dodawanie/Usuwanie zasobów z katalogu ogólne](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  |  |  |  |
| [Dodawanie/Usuwanie zasobów z katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Dodawanie właścicieli katalogu ani uzyskać dostępu do menedżerów pakietów](entitlement-management-catalog-create.md#add-catalog-owners-or-access-package-managers) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Edytowanie/usuwanie katalogu](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Wyspecjalizowane chmury, takich jak platforma Azure Government, Azure (Niemcy) i Azure China 21Vianet nie są obecnie dostępne w tej wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Utworzenie pierwszego pakietu dostępu](entitlement-management-access-package-first.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)

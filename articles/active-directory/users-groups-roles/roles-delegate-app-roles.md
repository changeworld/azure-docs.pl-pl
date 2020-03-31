---
title: Delegowanie trwałej zarządzania administratorami aplikacji — usługa Azure AD | Dokumenty firmy Microsoft
description: Udzielanie uprawnień do zarządzania dostępem do aplikacji w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253041"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegowanie uprawnień rejestracji aplikacji w usłudze Azure Active Directory

W tym artykule opisano sposób używania uprawnień przyznanych przez role niestandardowe w usłudze Azure Active Directory (Azure AD) w celu zaspokojenia potrzeb związanych z zarządzaniem aplikacjami. W usłudze Azure AD można delegować uprawnienia do tworzenia i zarządzania aplikacjami w następujący sposób:

- [Ograniczanie, kto może tworzyć aplikacje](#restrict-who-can-create-applications) i zarządzać utworzonymi przez nie aplikacjami. Domyślnie w usłudze Azure AD wszyscy użytkownicy mogą rejestrować rejestracje aplikacji i zarządzać wszystkimi aspektami aplikacji, które tworzą. Może to być ograniczone tylko do zezwalania tylko wybranym osobom, które mają uprawnienia.
- [Przypisywanie jednego lub większej liczby właścicieli do aplikacji](#assign-application-owners). Jest to prosty sposób, aby przyznać komuś możliwość zarządzania wszystkimi aspektami konfiguracji usługi Azure AD dla określonej aplikacji.
- [Przypisywanie wbudowanej roli administracyjnej,](#assign-built-in-application-admin-roles) która udziela dostępu do zarządzania konfiguracją w usłudze Azure AD dla wszystkich aplikacji. Jest to zalecany sposób udzielania ekspertom IT dostępu do zarządzania szerokimi uprawnieniami konfiguracji aplikacji bez udzielania dostępu do zarządzania innymi częściami usługi Azure AD, które nie są związane z konfiguracją aplikacji.
- [Tworzenie roli niestandardowej](#create-and-assign-a-custom-role-preview) definiującej bardzo określone uprawnienia i przypisującej ją do kogoś do zakresu pojedynczej aplikacji jako ograniczonego właściciela lub w zakresie katalogu (wszystkie aplikacje) jako administratora ograniczonego.

Ważne jest, aby rozważyć przyznanie dostępu przy użyciu jednej z powyższych metod z dwóch powodów. Po pierwsze, delegowanie możliwości wykonywania zadań administracyjnych zmniejsza obciążenie administratora globalnego. Po drugie, przy użyciu ograniczonych uprawnień poprawia poziom zabezpieczeń i zmniejsza ryzyko nieautoryzowanego dostępu. Problemy z delegowaniem i ogólne wskazówki są omawiane w [administracji delegowania w usłudze Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Ogranicz, kto może tworzyć aplikacje

Domyślnie w usłudze Azure AD wszyscy użytkownicy mogą rejestrować rejestracje aplikacji i zarządzać wszystkimi aspektami aplikacji, które tworzą. Każdy ma również możliwość wyrażenia zgody na dostęp do danych firmy w ich imieniu. Można wybrać selektywne przyznanie tych uprawnień, ustawiając przełączniki globalne na "Nie" i dodając wybranych użytkowników do roli dewelopera aplikacji.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Aby wyłączyć domyślną możliwość tworzenia rejestracji aplikacji lub zgody na aplikacje

1. Zaloguj się do organizacji usługi Azure AD przy pomocy konta kwalifikującego się do roli administratora globalnego w organizacji usługi Azure AD.
1. Ustaw jedną lub obie z następujących czynności:

    - Na [stronie Ustawienia użytkownika dla organizacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)ustaw ustawienie **Użytkownicy mogą rejestrować aplikacje** na Nr. Spowoduje to wyłączenie domyślnej możliwości tworzenia rejestracji aplikacji przez użytkowników.
    - W [ustawieniach użytkownika dla aplikacji korporacyjnych](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)ustaw **ustawienia Użytkownicy mogą wyrażać zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu** na Nr. Spowoduje to wyłączenie domyślnej możliwości zgody użytkowników na aplikacje uzyskujące dostęp do danych firmy w ich imieniu.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Udzielanie indywidualnych uprawnień do tworzenia aplikacji i wyrażanie zgody, gdy domyślna możliwość jest wyłączona

Przypisz rolę dewelopera aplikacji, aby przyznać możliwość tworzenia rejestracji aplikacji, gdy **użytkownicy mogą rejestrować aplikacje** ustawienie jest ustawiona na Nie. Ta rola udziela również zgody na wyrażenie zgody we własnym imieniu, gdy **Użytkownicy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmowych w ich imieniu** ustawienie jest ustawione na Nie. Jako zachowanie systemu, gdy użytkownik tworzy nową rejestrację aplikacji, są one automatycznie dodawane jako pierwszy właściciel. Uprawnienia własności dają użytkownikowi możliwość zarządzania wszystkimi aspektami rejestracji aplikacji lub aplikacji przedsiębiorstwa, które są ich własnością.

## <a name="assign-application-owners"></a>Przypisywanie właścicieli aplikacji

Przypisywanie właścicieli to prosty sposób na przyznanie możliwości zarządzania wszystkimi aspektami konfiguracji usługi Azure AD dla rejestracji określonej aplikacji lub aplikacji przedsiębiorstwa. Jako zachowanie systemu, gdy użytkownik tworzy nową rejestrację aplikacji są automatycznie dodawane jako pierwszy właściciel. Uprawnienia własności dają użytkownikowi możliwość zarządzania wszystkimi aspektami rejestracji aplikacji lub aplikacji przedsiębiorstwa, które są ich własnością. Pierwotny właściciel może zostać usunięty i mogą zostać dodani dodatkowi właściciele.

### <a name="enterprise-application-owners"></a>Właściciele aplikacji dla przedsiębiorstw

Jako właściciel użytkownik może zarządzać konfiguracją aplikacji przedsiębiorstwa specyficzną dla organizacji, taką jak konfiguracja logowania jednokrotnego, inicjowanie obsługi administracyjnej i przypisania użytkowników. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko aplikacjami dla przedsiębiorstw, których są właścicielami.

W niektórych przypadkach aplikacje dla przedsiębiorstw utworzone z galerii aplikacji obejmują zarówno aplikację przedsiębiorstwa, jak i rejestrację aplikacji. Jeśli to prawda, dodanie właściciela do aplikacji przedsiębiorstwa automatycznie dodaje właściciela do odpowiedniej rejestracji aplikacji jako właściciela.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Aby przypisać właściciela do aplikacji przedsiębiorstwa

1. Zaloguj się do [organizacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) usługi Azure AD przy pomocy konta kwalifikującego się do administratora aplikacji lub administratora aplikacji w chmurze dla organizacji.
1. Na [stronie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Rejestracje aplikacji dla organizacji wybierz aplikację, aby otworzyć stronę Przegląd aplikacji.
1. Wybierz **pozycję Właściciele,** aby wyświetlić listę właścicieli aplikacji.
1. Wybierz **pozycję Dodaj,** aby wybrać jednego lub więcej właścicieli do dodania do aplikacji.

> [!IMPORTANT]
> Użytkownicy i podmioty usługi mogą być właścicielami rejestracji aplikacji. Tylko użytkownicy mogą być właścicielami aplikacji dla przedsiębiorstw. Grupy nie mogą być przypisane jako właściciele albo.
>
> Właściciele mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Aplikacja może mieć więcej uprawnień niż właściciel, a zatem będzie podniesienie uprawnień nad tym, co właściciel ma dostęp do jako użytkownik lub jednostki usługi. Właściciel aplikacji może potencjalnie utworzyć lub zaktualizować użytkowników lub inne obiekty podczas personifikacji aplikacji, w zależności od uprawnień aplikacji.

## <a name="assign-built-in-application-admin-roles"></a>Przypisywanie wbudowanych ról administratora aplikacji

Usługa Azure AD ma zestaw wbudowanych ról administratora do udzielania dostępu do zarządzania konfiguracją w usłudze Azure AD dla wszystkich aplikacji. Te role są zalecanym sposobem udzielania ekspertom IT dostępu do zarządzania szerokimi uprawnieniami konfiguracji aplikacji bez udzielania dostępu do zarządzania innymi częściami usługi Azure AD, które nie są związane z konfiguracją aplikacji.

- Administrator aplikacji: Użytkownicy w tej roli mogą tworzyć i zarządzać wszystkimi aspektami aplikacji korporacyjnych, rejestracji aplikacji i ustawień serwera proxy aplikacji. Ta rola daje również możliwość wyrażenia zgody na uprawnienia delegowane i uprawnienia aplikacji z wyłączeniem programu Microsoft Graph. Użytkownicy przypisani do tej roli nie są dodawane jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji przedsiębiorstwa.
- Administrator aplikacji w chmurze: Użytkownicy w tej roli mają takie same uprawnienia jak rola Administrator aplikacji, z wyłączeniem możliwości zarządzania serwerem proxy aplikacji. Użytkownicy przypisani do tej roli nie są dodawane jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji przedsiębiorstwa.

Aby uzyskać więcej informacji i wyświetlić opis tych ról, zobacz [Dostępne role](directory-assign-admin-roles.md#available-roles).

Postępuj zgodnie z instrukcjami w przewodniku Przypisywanie ról do użytkowników za pomocą przewodnika instrukcjowego usługi [Azure Active Directory,](../fundamentals/active-directory-users-assign-role-azure-portal.md) aby przypisać role administratora aplikacji lub administratora aplikacji w chmurze.

> [!IMPORTANT]
> Administratorzy aplikacji i administratorzy aplikacji w chmurze mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Aplikacja może mieć uprawnienia, które są podniesienie uprawnień nad uprawnieniami roli administratora. Administrator w tej roli może potencjalnie utworzyć lub zaktualizować użytkowników lub inne obiekty podczas personifikacji aplikacji, w zależności od uprawnień aplikacji.
> Żadna z tych ról nie umożliwia zarządzania ustawieniami dostępu warunkowego.

## <a name="create-and-assign-a-custom-role-preview"></a>Tworzenie i przypisywanie roli niestandardowej (wersja zapoznawcza)

Tworzenie ról niestandardowych i przypisywanie ról niestandardowych to oddzielne kroki:

- [Utwórz niestandardową *definicję roli* ](roles-create-custom.md) i [dodaj do niej uprawnienia z listy predefiniowanych](roles-custom-available-permissions.md). Są to te same uprawnienia używane w wbudowanych rolach.
- [Utwórz *przypisanie roli,* ](roles-assign-powershell.md) aby przypisać rolę niestandardową.

To oddzielenie umożliwia utworzenie definicji pojedynczej roli, a następnie przypisanie jej wiele razy w różnych *zakresach*. Rolę niestandardową można przypisać w zakresie całej organizacji lub można ją przypisać w zakresie, jeśli pojedynczy obiekt usługi Azure AD. Przykładem zakresu obiektu jest rejestracja pojedynczej aplikacji. Przy użyciu różnych zakresów, tej samej definicji roli można przypisać do Sally dla wszystkich rejestracji aplikacji w organizacji, a następnie do Naveen tylko contoso raporty wydatków rejestracji aplikacji.

Wskazówki dotyczące tworzenia i używania ról niestandardowych do delegowannia zarządzania aplikacjami:
- Role niestandardowe udzielają dostępu tylko w większości bieżących bloków rejestracji aplikacji w portalu usługi Azure AD. Nie udzielają dostępu w starszych identyfikatorach rejestracji aplikacji.
- Role niestandardowe nie udzielają dostępu do portalu usługi Azure AD, gdy ustawienie użytkownika "Ogranicz dostęp do portalu administracyjnego usługi Azure AD" jest ustawione na Tak.
- Rejestracje aplikacji, do które użytkownik ma dostęp do przy użyciu przypisań ról, są wyświetlane tylko na karcie "Wszystkie aplikacje" na stronie Rejestracja aplikacji. Nie są one wyświetlane w zakładce "Posiadane aplikacje".

Aby uzyskać więcej informacji na temat podstaw ról niestandardowych, zobacz [omówienie ról niestandardowych,](roles-custom-overview.md)a także sposób [tworzenia roli niestandardowej](roles-create-custom.md) i [przypisywania roli](roles-assign-powershell.md).

## <a name="next-steps"></a>Następne kroki

- [Podtypy i uprawnienia rejestracji aplikacji](roles-custom-available-permissions.md)
- [Odwołanie do roli administratora usługi Azure AD](directory-assign-admin-roles.md)

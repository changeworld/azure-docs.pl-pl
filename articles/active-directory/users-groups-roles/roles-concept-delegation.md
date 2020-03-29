---
title: Opis delegowania ról administratora — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Modele delegowania, przykłady i zabezpieczenia ról w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fa3c6bf39dbef601fe64e125999f519f725f2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67083771"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegowanie administrowania w usłudze Azure Active Directory

Wraz ze wzrostem organizacyjnym przychodzi złożoność. Jedną z typowych odpowiedzi jest zmniejszenie niektórych obciążeń zarządzania dostępem za pomocą ról administratora usługi Azure Active Directory (AD). Można przypisać użytkownikom najmniejsze możliwe uprawnienia do uzyskiwania dostępu do swoich aplikacji i wykonywania zadań. Nawet jeśli rola administratora globalnego nie zostanie przypisana do każdego właściciela aplikacji, obowiązki związane z zarządzaniem aplikacjami są umieszczane na istniejących administratorach globalnych. Istnieje wiele powodów, dla organizacji przenieść w kierunku bardziej zdecentralizowanej administracji. Ten artykuł może pomóc w planowaniu delegowania w organizacji.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Uprawnienia scentralizowane i delegowane

Wraz z rozwojem organizacji może być trudno śledzić, którzy użytkownicy mają określone role administratora. Jeśli pracownik ma prawa administratora, których nie powinien, twoja organizacja może być bardziej podatna na naruszenia zabezpieczeń. Ogólnie rzecz biorąc, liczba obsługiwanych administratorów i szczegółowe ich uprawnienia zależą od rozmiaru i złożoności wdrożenia.

* W przypadku małych lub weryfikacji koncepcji jeden lub kilku administratorów robi wszystko; nie ma delegacji. W takim przypadku należy utworzyć każdego administratora z rolą administratora globalnego.
* W większych wdrożeniach z większą liczą maszyn, aplikacji i komputerów stacjonarnych, więcej delegowania jest potrzebne. Kilku administratorów może mieć bardziej szczegółowe obowiązki funkcjonalne (role). Na przykład niektóre mogą być administratorami tożsamości uprzywilejowanych, a inne administratorami aplikacji. Ponadto administrator może zarządzać tylko niektórymi grupami obiektów, takimi jak urządzenia.
* Nawet większe wdrożenia mogą wymagać jeszcze bardziej szczegółowych uprawnień, a także ewentualnie administratorów z rolami niekonwencjonalnymi lub hybrydowymi.

W portalu usługi Azure AD można [wyświetlić wszystkich członków dowolnej roli,](directory-manage-roles-portal.md)co może pomóc szybko sprawdzić wdrożenia i delegować uprawnienia.

Jeśli chcesz delegować dostęp do zasobów platformy Azure zamiast dostępu administracyjnego w usłudze Azure AD, zobacz [Przypisywanie roli kontroli dostępu opartej na rolach opartych na rolach kontroli dostępu opartej na rolach.](../../role-based-access-control/role-assignments-portal.md)

## <a name="delegation-planning"></a>Planowanie delegacji

To praca nad opracowaniem modelu delegowania, który pasuje do Twoich potrzeb. Tworzenie modelu delegowania jest iteracyjnym procesem projektowania i zalecamy wykonać następujące kroki:

* Definiowanie potrzebnych ról
* Delegowanie administracji aplikacji
* Możliwość rejestrowania wniosków
* Delegowanie własności aplikacji
* Opracowanie planu bezpieczeństwa
* Zakładanie kont awaryjnych
* Zabezpiecz role administratora
* Tymczasowe podniesienie uprzywilejowane

## <a name="define-roles"></a>Definiowanie ról

Określ zadania usługi Active Directory wykonywane przez administratorów oraz sposób ich mapowania do ról. Szczegółowe [opisy ról](directory-manage-roles-portal.md) można wyświetlić w witrynie Azure portal.

Każde zadanie powinno być oceniane pod kątem częstotliwości, ważności i trudności. Kryteria te są istotnymi aspektami definicji zadań, ponieważ regulują, czy należy przekazać uprawnienie:

* Zadania, które wykonujesz rutynowo, mają ograniczone ryzyko i są banalne do wykonania, są doskonałymi kandydatami do delegowania.
* Zadania, które są rzadko wykonywane, ale mają duży wpływ na całej organizacji i wymagają wysokiego poziomu umiejętności, należy rozważyć bardzo ostrożnie przed delegowaniem. Zamiast tego można [tymczasowo podnieść poziom konta do wymaganej roli](../active-directory-privileged-identity-management-configure.md) lub ponownie przypisać zadanie.

## <a name="delegate-app-administration"></a>Delegowanie administracji aplikacji

Rozprzestrzenianie się aplikacji w organizacji może obciążać model delegowania. Jeśli nakłada obciążenie dla zarządzania dostępem do aplikacji na administratora globalnego, jest prawdopodobne, że model zwiększa obciążenie w miarę upływu czasu. Jeśli przyznano osobom rolę administratora globalnego w takich działaniach, jak konfigurowanie aplikacji dla przedsiębiorstwa, można teraz odciążyć je do następujących ról o mniej uprzywilejowanych uprawnieniach. Pomaga to poprawić postawę bezpieczeństwa i zmniejsza ryzyko niefortunnych błędów. Najbardziej uprzywilejowane role administratora aplikacji to:

* Rola **Administrator aplikacji,** która umożliwia zarządzanie wszystkimi aplikacjami w katalogu, w tym rejestracjami, ustawieniami logowania jednokrotnego, przypisaniami użytkowników i grup oraz licencjonowaniem, ustawieniami serwera proxy aplikacji i zgodą. Nie daje możliwość zarządzania dostępem warunkowym.
* Rola **Administrator aplikacji w chmurze,** która udziela wszystkich możliwości administratora aplikacji, z tą różnicą, że nie udziela dostępu do ustawień serwera proxy aplikacji (ponieważ nie ma uprawnień lokalnych).

## <a name="delegate-app-registration"></a>Delegowanie rejestracji aplikacji

Domyślnie wszyscy użytkownicy mogą tworzyć rejestracje aplikacji. Aby wybiórczo przyznać możliwość tworzenia rejestracji aplikacji:

* Ustaw **użytkowników można zarejestrować aplikacje** nie w **ustawieniach użytkownika**
* Przypisywanie użytkownika do roli dewelopera aplikacji

Aby wybiórczo udzielić zgody na zezwolenie aplikacji na dostęp do danych:

* Ustaw **użytkowników można wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu** Nie w **ustawieniach użytkownika**
* Przypisywanie użytkownika do roli dewelopera aplikacji

Gdy deweloper aplikacji tworzy nową rejestrację aplikacji, są one automatycznie dodawane jako pierwszy właściciel.

## <a name="delegate-app-ownership"></a>Delegowanie własności aplikacji

W przypadku delegowania dostępu do aplikacji o bardziej szczegółowym ziarnistym można przypisać własność poszczególnym aplikacjom korporacyjnym. Uzupełnia to istniejącą obsługę przypisywania właścicieli rejestracji aplikacji. Własność jest przypisywana na podstawie aplikacji dla przedsiębiorstw w bloku Aplikacje dla przedsiębiorstw. Korzyścią jest właściciele mogą zarządzać tylko aplikacje przedsiębiorstwa, które posiadają. Na przykład można przypisać właściciela dla aplikacji Salesforce, a ten właściciel może zarządzać dostępem i konfiguracją dla Salesforce i żadnych innych aplikacji. Aplikacja dla przedsiębiorstw może mieć wielu właścicieli, a użytkownik może być właścicielem wielu aplikacji dla przedsiębiorstw. Istnieją dwie role właściciela aplikacji:

* Rola **Właściciel aplikacji przedsiębiorstwa** umożliwia zarządzanie "aplikacjami dla przedsiębiorstw, które jest właścicielem, w tym ustawienia logowania jednokrotnego, przypisania użytkowników i grup oraz dodawanie dodatkowych właścicieli. Nie daje możliwość zarządzania ustawieniami serwera proxy aplikacji lub dostępu warunkowego.
* Rola **właściciel rejestracji aplikacji** daje możliwość zarządzania rejestracjami aplikacji dla aplikacji, która jest właścicielem, w tym manifestu aplikacji i dodawanie dodatkowych właścicieli.

## <a name="develop-a-security-plan"></a>Opracowanie planu bezpieczeństwa

Usługa Azure AD zawiera obszerny przewodnik po planowaniu i wykonywaniu planu zabezpieczeń w rolach administratorów usługi Azure AD, [zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i chmurowych.](directory-admin-roles-secure.md)

## <a name="establish-emergency-accounts"></a>Zakładanie kont awaryjnych

Aby zachować dostęp do magazynu zarządzania tożsamościami w przypadku wystąpienia problemu, przygotuj konta dostępu awaryjnego zgodnie z [tworzeniem kont administracyjnych dostępu awaryjnego.](directory-emergency-access.md)

## <a name="secure-your-administrator-roles"></a>Zabezpiecz role administratora

Osoby atakujące, które uzyskują kontrolę nad kontami uprzywilejowanymi, mogą wyrządzić ogromne szkody, dlatego najpierw chroń te konta, korzystając z [zasad dostępu bazowego,](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) które są domyślnie dostępne dla wszystkich dzierżaw usługi Azure AD (w publicznej wersji zapoznawczej). Zasada wymusza uwierzytelnianie wieloskładnikowe na uprzywilejowanych kontach usługi Azure AD. Następujące role usługi Azure AD są objęte zasadami planu bazowego usługi Azure AD:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń

## <a name="elevate-privilege-temporarily"></a>Tymczasowe podniesienie uprawnień podniosły

W przypadku większości codziennych działań nie wszyscy użytkownicy potrzebują praw administratora globalnego i nie wszystkie z nich powinny być trwale przypisane do roli administratora globalnego. Gdy użytkownicy potrzebują uprawnień administratora globalnego, powinni aktywować przypisanie roli w usłudze Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) na własnym koncie lub alternatywnym koncie administracyjnym.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać odwołanie do opisów ról usługi Azure AD, zobacz [Przypisywanie ról administratora w usłudze Azure AD](directory-assign-admin-roles.md)

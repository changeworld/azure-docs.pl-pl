---
title: Informacje o delegowaniu roli administratora — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Modele delegowanie, przykłady i roli zabezpieczeń w usłudze Azure Active Directory
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083771"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegowanie administracji w usłudze Azure Active Directory

Wzrost organizacji pochodzi złożoności. Jednej wspólnej odpowiedzi jest zmniejszyć obciążenie zarządzanie dostępem przy użyciu ról administratora w usłudze Azure Active Directory (AD). Najniższe możliwe uprawnienia można przypisać użytkownikom dostęp do swoich aplikacji i wykonywać swoje zadania. Nawet wtedy, gdy rola administratora globalnego nie przypisuj do każdego właściciela aplikacji, umieszczając aplikacji stanowiskach kierowniczych w istniejących administratorów globalnych. Istnieje wiele funkcji przemawiających za przeniesienie organizacji kierunku bardziej zdecentralizowane administracji. Ten artykuł może pomóc Ci zaplanować działania związane z delegowania w Twojej organizacji.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Scentralizowane i delegowane uprawnienia

Wraz z rozwojem organizacji może być trudne do śledzenia użytkowników, którzy mają konkretny administrator ról. Jeśli pracownik ma uprawnienia administratora, które nie powinny one, Twoja organizacja może być bardziej narażony na naruszenia zabezpieczeń. Ogólnie rzecz biorąc, ile Administratorzy obsługujesz i ich uprawnienia szczegółowość zależy od rozmiaru i stopień złożoności wdrożenia.

* W przypadku wdrożeń w małych i weryfikacji koncepcji Administratorzy jednego lub kilku dokładać; nie ma żadnych delegowania. W takim przypadku należy utworzyć każdy administrator z rolą administratora globalnego.
* W przypadku większych wdrożeń za pomocą więcej maszyn, aplikacji i komputerów stacjonarnych więcej delegowania jest wymagana. Kilku administratorów może być bardziej szczegółowe obowiązki funkcjonalności (role). Na przykład niektóre mogą być uprzywilejowanych administratorów tożsamości, a inne mogą działać administratorów aplikacji. Ponadto administrator może zarządzać tylko określone grupy obiektów, takich jak urządzenia.
* Nawet większych wdrożeń może wymagać, jeszcze bardziej szczegółowych uprawnień, oraz prawdopodobnie administratorom niekonwencjonalne lub hybrydowego ról.

W portalu usługi Azure AD możesz [Wyświetlanie członków dowolnej roli](directory-manage-roles-portal.md), które pomogą Ci szybko sprawdzić uprawnienia wdrożenia i delegata.

Jeśli interesuje Cię Delegowanie dostępu do zasobów platformy Azure zamiast dostępem administracyjnym w usłudze Azure AD, zobacz [przypisania roli kontroli dostępu opartej na rolach](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planowanie delegowania

Jego praca opracowanie modelu delegowania, który odpowiada Twoim potrzebom. Tworzenie modelu delegowania jest procesem iteracyjne projektowania i Sugerujemy, wykonaj następujące kroki:

* Definiowanie ról, których potrzebujesz
* Delegowanie administracji w aplikacji
* Zezwolić na rejestrowanie aplikacji
* Delegowanie własności aplikacji
* Opracowywanie planu zabezpieczeń
* Zakładać kont awaryjnych
* Zabezpieczanie usługi ról administratora
* Wprowadzić tymczasowe uprzywilejowanych podniesienia uprawnień

## <a name="define-roles"></a>Definiowanie ról

Należy określić zadania usługi Active Directory, które są wykonywane przez administratorów oraz sposobu mapowania ich do ról. Możesz [wyświetlić opisy ról szczegółowe](directory-manage-roles-portal.md) w witrynie Azure portal.

Każde zadanie ma zostać obliczona dla częstotliwości, znaczenia i trudności. Te kryteria są kluczowe aspekty definicji zadania, ponieważ ich decydowania, czy mają być delegowane uprawnienia:

* Zadania są regularnie, mają ograniczone ryzyko i są proste do pełną są doskonałymi kandydatami do delegowania.
* Zadania są rzadko, ale mają duży wpływ na całą organizację i wymagają poziomów wysokie umiejętności należy rozważyć dokładnie przed przekazaniem. Zamiast tego można [tymczasowo podnieść poziom konta do roli wymagane](../active-directory-privileged-identity-management-configure.md) lub ponownym przypisaniu zadania.

## <a name="delegate-app-administration"></a>Delegowanie administracji w aplikacji

Powszechne stosowanie aplikacji w Twojej organizacji może być związana modelu delegowania. Jeśli obciążenie dla zarządzania dostępem do aplikacji umieszcza na administratora globalnego, prawdopodobnie modelu zwiększa jego obciążenie w. Jeśli przyznano osób rolę administratora globalnego dla elementów, takich jak konfigurowanie aplikacji dla przedsiębiorstw, możesz je teraz odciążyć do następujących ról mniej uprzywilejowane. To pomaga zwiększyć poziom bezpieczeństwa i zmniejsza ryzyko pomyłek niefortunne. Role administratorów aplikacji najbardziej uprzywilejowanych są:

* **Administrator aplikacji** roli, co daje możliwość zarządzania wszystkich aplikacji w katalogu, w tym rejestracji, ustawienia rejestracji jednokrotnej, użytkownika i przypisania grupy i licencjonowania, aplikacji ustawienia serwera Proxy i zgody. Go nie są przyznawane możliwość zarządzania dostępem warunkowym.
* **Administrator aplikacji w chmurze** roli, która przyznaje wszystkie możliwości programu Administrator aplikacji, z wyjątkiem go nie przyznać dostęp do ustawień serwera Proxy aplikacji (ponieważ go nie ma lokalnych uprawnień).

## <a name="delegate-app-registration"></a>Delegowanie rejestracji aplikacji

Domyślnie wszyscy użytkownicy mogą tworzyć rejestracje aplikacji. Aby selektywnie udzielić możliwość tworzenia rejestracji aplikacji:

* Ustaw **użytkownicy mogą rejestrować aplikacje** nie w **ustawień użytkownika**
* Przypisz użytkownika do roli aplikacji dla deweloperów

Aby selektywnie udzielić możliwość zezwalać aplikacji na dostęp do danych:

* Ustaw **użytkownicy mogą wyrazić zgodę aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu** na nie w **ustawień użytkownika**
* Przypisz użytkownika do roli aplikacji dla deweloperów

Gdy deweloper aplikacji powoduje utworzenie nowej rejestracji aplikacji, ich są automatycznie dodawane jako pierwszy właściciela.

## <a name="delegate-app-ownership"></a>Delegowanie własności aplikacji

Delegowanie dostępu nawet bardziej szczegółowej aplikacji można przypisać prawa własności do aplikacji przedsiębiorstwa poszczególnych. To uzupełnienie istniejących obsługę przypisywanie właścicieli rejestracji aplikacji. Na podstawie aplikacji dla przedsiębiorstw w bloku aplikacje w przedsiębiorstwie jest przypisany właściciel. Korzyścią jest, że właściciele mogą zarządzać tylko te aplikacje przedsiębiorstwa, które są właścicielami. Na przykład możesz przypisać właściciela aplikacji Salesforce i tego właściciela można zarządzać, dostęp do i Konfiguracja usługi Salesforce i żadne inne aplikacje. Aplikacja dla przedsiębiorstw mogą mieć wiele właścicieli, a użytkownik może być właścicielem, wiele aplikacji dla przedsiębiorstw. Dostępne są dwie role właściciela aplikacji:

* **Właściciela aplikacji przedsiębiorstwa** rola zapewnia możliwość zarządzania "aplikacje dla przedsiębiorstw, które należą do użytkownika, takich jak ustawienia rejestracji jednokrotnej, użytkownika i przypisania grupy oraz dodawanie dodatkowych właścicieli. Go nie są przyznawane umożliwia zarządzanie ustawieniami serwera Proxy aplikacji lub dostępu warunkowego.
* **Właściciel rejestracji aplikacji** rola zapewnia możliwość zarządzania rejestracji aplikacji dla aplikacji, które należą do użytkownika, łącznie z manifestu aplikacji i dodawanie dodatkowych właścicieli.

## <a name="develop-a-security-plan"></a>Opracowywanie planu zabezpieczeń

Usługa Azure AD zapewnia rozbudowane przewodnik planowania i wykonywania plan ochrony na podstawie poszczególnych ról administratora usługi Azure AD [zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze](directory-admin-roles-secure.md).

## <a name="establish-emergency-accounts"></a>Zakładać kont awaryjnych

Aby zachować dostęp do sklepu zarządzania tożsamościami, gdy pojawia się problem, należy przygotować kont dostępu awaryjnego zgodnie z opisem w [tworzenie kont administracyjnych dostępu awaryjnego](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Zabezpieczanie usługi ról administratora

Osoby atakujące, które Pełna kontrola nad wykorzystaniem kont uprzywilejowanych można zrobić ogromną uszkodzenia, więc chronić te konta, za pomocą [zasad dostępu bazowego](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) , jest dostępna domyślnie dla wszystkich dzierżaw usługi Azure AD (w publicznej wersji zapoznawczej). Zasada wymusza uwierzytelnianie wieloskładnikowe na uprzywilejowany kont usługi Azure AD. Następujące role usługi Azure AD są objęte zasad linii bazowej usługi Azure AD:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń

## <a name="elevate-privilege-temporarily"></a>Tymczasowo podniesienie poziomu uprawnień

W przypadku większości codziennych działań nie wszyscy użytkownicy muszą mieć uprawnienia administratora globalnego i nie wszystkie z nich powinna zostać trwale przypisany do roli administratora globalnego. Gdy użytkownicy potrzebują uprawnień administratora globalnego, powinna być aktywowana się przypisanie roli w usłudze Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) własnego konta lub alternatywnego konta administracyjnego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać odwołanie do opisy ról usługi Azure AD, zobacz [przypisywanie ról administratora w usłudze Azure AD](directory-assign-admin-roles.md)

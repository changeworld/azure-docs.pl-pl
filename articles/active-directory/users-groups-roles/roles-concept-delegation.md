---
title: Delegowanie ról administratora w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Modele delegowanie, przykłady i roli zabezpieczeń w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/09/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: ad772a2e0c036c30aca2918496ac01f31157fc64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208576"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegowanie administracji w usłudze Azure Active Directory

Wzrost organizacji jest dalsze złożoności i jednej wspólnej odpowiedź jest zmniejszyć obciążenie zarządzanie dostępem przy użyciu ról administratora w usłudze Azure Active Directory (AD). Najniższe możliwe uprawnienia można przypisać użytkownikom dostęp do swoich aplikacji i wykonywać swoje zadania. Możesz nie chcieć przypisać rolę administratora globalnego do każdego właściciela aplikacji, ale jego wadą może być wymuszenie aplikacji obowiązków w zakresie zarządzania na istniejących administratorów globalnych. Istnieje wiele funkcji przemawiających za przeniesienie organizacji kierunku bardziej zdecentralizowane administracji. Ten artykuł może pomóc Ci zaplanować działania związane z delegowania w Twojej organizacji.

<!--What about reporting? Who has which role and how do I audit?-->


## <a name="centralized-versus-delegated-permissions"></a>Scentralizowane i delegowane uprawnienia

Wraz z rozwojem organizacji może być trudne do śledzenia użytkowników, którzy mają konkretny administrator ról. Jeśli pracownik ma uprawnienia administratora, który nie należy ich organizacji może być narażony na naruszenia zabezpieczeń. Ogólnie rzecz biorąc liczbę administratorów oraz stopień szczegółowości ich uprawnień zależy od rozmiar i złożoność wdrożenia.

* W przypadku wdrożeń w małych i weryfikacji koncepcji Administratorzy jednego lub kilku dokładać; nie ma żadnych delegowania. W takim przypadku należy utworzyć każdy administrator z rolą administratora globalnego.
* W przypadku większych wdrożeń za pomocą więcej maszyn, aplikacji i komputerów stacjonarnych więcej delegowania jest wymagana. Kilku administratorów może być bardziej szczegółowe obowiązki funkcjonalności (role). Na przykład niektóre mogą być uprzywilejowanych administratorów tożsamości, a inne mogą działać administratorów aplikacji. Ponadto administrator może zarządzać tylko określone grupy obiektów, takich jak urządzenia.
* Nawet większych wdrożeń może wymagać, jeszcze bardziej szczegółowych uprawnień, oraz prawdopodobnie administratorom niekonwencjonalne lub hybrydowego ról.

W portalu usługi Azure AD możesz [Wyświetlanie członków dowolnej roli](directory-manage-roles-portal.md), które pomogą Ci szybko sprawdzić uprawnienia wdrożenia i delegata.

Jeśli interesuje Cię Delegowanie dostępu do zasobów platformy Azure i nie administracyjne dostępu w usłudze Azure AD, zobacz [przypisania roli kontroli dostępu opartej na rolach](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planowanie delegowania

Próg wykluczający jest opracowanie modelu delegowania, która pasuje do unikatowych potrzeb organizacji, prawdę jest bardzo prosty modeli, które mogą być stosowane z niewielkim modyfikacji. Tworzenie modelu delegowania jest procesem iteracyjne projektowania i Sugerujemy, wykonaj następujące kroki:

* Definiowanie ról, których potrzebujesz
* Delegowanie administracji w aplikacji
* Zezwolić na rejestrowanie aplikacji
* Delegowanie własności aplikacji
* Opracowywanie planu zabezpieczeń
* Zakładać kont awaryjnych
* Zabezpieczanie usługi ról administratora
* Wprowadzić tymczasowe uprzywilejowanych podniesienia uprawnień

## <a name="define-roles"></a>Definiowanie ról

Określ zadania usługi Active Directory, które są wykonywane przez administratorów i jak te zadania są mapowane na role. Na przykład tworzenia witryn usługi Active Directory jest zadaniem administracji usługi podczas zmiany członkostwa w grupie zabezpieczeń znajduje się zwykle w obszarze Administracja danych. Możesz [wyświetlić opisy ról szczegółowe](directory-manage-roles-portal.md) w witrynie Azure portal.

Każde zadanie ma zostać obliczona dla częstotliwości, znaczenia i trudności. Są to kluczowe aspekty definicji zadania, ponieważ one decydowania, czy mają być delegowane uprawnienia. Zadania, które odbywają się regularnie, mają ograniczone ryzyko i są proste do wykonania są doskonałymi kandydatami do delegowania. Z drugiej strony zadania, które odbywają się rzadko, ale mają duży wpływ całej organizacji i wymagają poziomów wysokie umiejętności należy rozważyć dokładnie przed przekazaniem. Zamiast tego można [tymczasowo podnieść poziom konta do roli wymagane](../active-directory-privileged-identity-management-configure.md) lub ponownym przypisaniu zadania.

## <a name="delegate-app-administration"></a>Delegowanie administracji w aplikacji

Powszechne stosowanie aplikacji w Twojej organizacji może być związana modelu delegowania. Jeśli obciążenie dla zarządzania dostępem do aplikacji umieszcza na administratora globalnego, prawdopodobnie modelu zwiększa jego obciążenie w. Jeśli przyznano osób rolę administratora globalnego dla elementów, takich jak konfigurowanie aplikacji dla przedsiębiorstw, możesz je teraz odciążyć do następujących ról mniej uprzywilejowane. To pomaga zwiększyć poziom bezpieczeństwa i zmniejsza ryzyko pomyłek niefortunne. Role administratorów aplikacji najbardziej uprzywilejowanych są:

* **Administrator aplikacji** roli, co daje możliwość zarządzania wszystkich aplikacji w katalogu, w tym rejestracji, ustawienia rejestracji jednokrotnej, użytkownika i przypisania grupy i licencjonowania, aplikacji ustawienia serwera Proxy i zgody. Nie zapewnia możliwość zarządzania dostępem warunkowym.
* **Administrator aplikacji w chmurze** roli, która przyznaje wszystkie możliwości programu Administrator aplikacji, z wyjątkiem go nie może udzielać dostępu do ustawień serwera Proxy aplikacji (ponieważ go nie ma lokalnych uprawnień). ### delegata aplikacji Właściciel uprawnień dla aplikacji

## <a name="delegate-app-registration"></a>Delegowanie rejestracji aplikacji

Domyślnie wszyscy użytkownicy mogą tworzyć rejestracje aplikacji. Jeśli chcesz selektywnie udzielić możliwość tworzenia rejestracje aplikacji, musisz ustawić **użytkownicy mogą rejestrować aplikacje** na nie w ustawień użytkownika, a następnie przypisz rolę dewelopera aplikacji. Ta rola umożliwia tworzenie rejestracji aplikacji tylko wtedy, gdy **użytkownicy mogą rejestrować aplikacje** jest wyłączona. Deweloperzy aplikacji również mogą wyrazić zgodę dla siebie podczas **użytkownicy mogą wyrazić zgodę aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu** jest ustawiona na nie. Gdy deweloper aplikacji powoduje utworzenie nowej rejestracji aplikacji, ich są automatycznie dodawane jako pierwszy właściciela.

## <a name="delegate-app-ownership"></a>Delegowanie własności aplikacji

Delegowanie dostępu nawet bardziej szczegółowej aplikacji można przypisać prawa własności do aplikacji przedsiębiorstwa poszczególnych. To uzupełnienie istniejących obsługę przypisywanie właścicieli rejestracji aplikacji. Na podstawie aplikacji dla przedsiębiorstw, w bloku aplikacji przedsiębiorstwa jest przypisany właściciel. Korzyścią jest, że właściciele mogą zarządzać tylko te aplikacje przedsiębiorstwa, które są właścicielami. Na przykład możesz przypisać właściciela aplikacji Salesforce i tego właściciela można zarządzać, dostęp do i Konfiguracja usługi Salesforce i żadne inne aplikacje. Aplikacja dla przedsiębiorstw mogą mieć wiele właścicieli, a użytkownik może być właścicielem, wiele aplikacji dla przedsiębiorstw. Dostępne są dwie role właściciela aplikacji:

* **Właściciela aplikacji przedsiębiorstwa** rola zapewnia możliwość zarządzania "aplikacje dla przedsiębiorstw, które należą do użytkownika, takich jak ustawienia rejestracji jednokrotnej, użytkownika i przypisania grupy oraz dodawanie dodatkowych właścicieli. Nie zapewnia możliwość zarządzania serwera Proxy aplikacji, ustawień lub dostępu warunkowego.
* **Właściciel rejestracji aplikacji** rola zapewnia możliwość zarządzania rejestracji aplikacji dla aplikacji, które należą do użytkownika, łącznie z manifestu aplikacji i dodawanie dodatkowych właścicieli.

## <a name="develop-a-security-plan"></a>Opracowywanie planu zabezpieczeń

Usługa Azure AD zapewnia rozbudowane przewodnik planowania i wykonywania plan ochrony na podstawie poszczególnych ról administratora usługi Azure AD [zabezpieczania uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze](directory-admin-roles-secure.md). 

## <a name="establish-emergency-accounts"></a>Zakładać kont awaryjnych

Aby zachować dostęp do sklepu zarządzania tożsamościami w przypadku wystąpienia problemu, należy przygotować kont dostępu awaryjnego zgodnie z opisem w [tworzenie kont administracyjnych dostępu awaryjnego](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Zabezpieczanie usługi ról administratora

Osoby atakujące, które Pełna kontrola nad wykorzystaniem kont uprzywilejowanych można zrobić ogromną uszkodzenia, więc chronić te konta, za pomocą [zasad dostępu bazowego](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) , jest dostępna domyślnie dla wszystkich dzierżaw usługi Azure AD (w publicznej wersji zapoznawczej). Zasada wymusza uwierzytelnianie wieloskładnikowe na uprzywilejowany kont usługi Azure AD. Następujące role usługi Azure AD są objęte zasad linii bazowej usługi Azure AD:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń

## <a name="elevate-privilege-temporarily"></a>Tymczasowo podniesienie poziomu uprawnień

Dla większości codziennych działań nie wszyscy użytkownicy muszą uprawnienia administratora globalnego. I nie wszyscy użytkownicy, które powinny być trwale przypisana do roli administratora globalnego. Gdy użytkownicy muszą pełnić rolę administratora globalnego, powinna być aktywowana się przypisanie roli w usłudze Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) własnego konta lub alternatywnego konta administracyjnego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać odwołanie do opisy ról usługi Azure AD, zobacz [przypisywanie ról administratora w usłudze Azure AD](directory-assign-admin-roles.md)
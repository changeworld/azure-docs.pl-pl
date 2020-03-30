---
title: Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory | Microsoft Docs
description: W tym artykule opisano korzyści wynikające z integracji usługi Azure Active Directory z aplikacjami lokalnymi, chmurowymi i SaaS.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca73fac06649f801461e53130a67aa9ec0ad0d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063326"
---
# <a name="application-management-with-azure-active-directory"></a>Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory

Usługa Azure Active Directory (Azure AD) upraszcza sposób zarządzania aplikacjami, udostępniając system tożsamości dla aplikacji w chmurze i lokalnych. Do usługi Azure AD można dodać oprogramowanie jako aplikacje usługi (SaaS), aplikacje lokalne i aplikacje biznesowe (LOB). Następnie użytkownicy logują się raz, aby bezpiecznie i bezproblemowo uzyskać dostęp do tych aplikacji, wraz z usługą Office 365 i innymi aplikacjami biznesowymi firmy Microsoft. Można zmniejszyć koszty [administracyjne, automatyzując inicjowanie obsługi administracyjnej przez użytkowników](../app-provisioning/user-provisioning.md). Można również użyć uwierzytelniania wieloskładnikowego i zasad dostępu warunkowego, aby zapewnić bezpieczny dostęp do aplikacji.

![Diagram przedstawiający aplikacje sfederowane za pośrednictwem usługi Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Dlaczego warto zarządzać aplikacjami przy użyciu rozwiązania w chmurze?

Organizacje mają często setki aplikacji, których użytkownicy potrzebują do wykonania swojej pracy. Użytkownicy uzyskują dostęp do tych aplikacji z wielu urządzeń i lokalizacji. Nowe aplikacje są dodawane, tworzone i wycofywane każdego dnia. Przy tak wielu aplikacjach i punktach dostępu bardziej niż kiedykolwiek ma zastosowanie rozwiązania chmurowego do zarządzania dostępem użytkowników do wszystkich aplikacji.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jakie typy aplikacji można zintegrować z usługą Azure AD?

Istnieją cztery główne typy aplikacji, które można dodać do **aplikacji enterprise** i zarządzać za pomocą usługi Azure AD:

- **Aplikacje usługi Azure AD Gallery** — usługa Azure AD ma galerię zawierającą tysiące aplikacji, które zostały wstępnie zintegrowane dla logowania jednokrotnego za pomocą usługi Azure AD. Niektóre z aplikacji używanych w Twojej organizacji prawdopodobnie znajdują się w galerii. [Dowiedz się więcej o planowaniu integracji z aplikacjami](plan-an-application-integration.md)lub uzyskaj szczegółowe kroki integracji poszczególnych aplikacji w [samouczkach aplikacji SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Aplikacje lokalne z serwerem proxy aplikacji** — za pomocą serwera proxy aplikacji usługi Azure AD można zintegrować lokalne aplikacje sieci web z usługą Azure AD w celu obsługi logowania jednokrotnego. Następnie użytkownicy końcowi mogą uzyskiwać dostęp do lokalnych aplikacji sieci Web w taki sam sposób, w jaki uzyskują dostęp do usługi Office 365 i innych aplikacji SaaS. [Dowiedz się, dlaczego warto korzystać z serwera proxy aplikacji i jak to działa.](what-is-application-proxy.md)

- **Aplikacje opracowane na zamówienie** — podczas tworzenia własnych aplikacji biznesowych można zintegrować je z usługą Azure AD do obsługi logowania jednokrotnego. Rejestrując aplikację w usłudze Azure AD, masz kontrolę nad zasadami uwierzytelniania dla aplikacji. Aby uzyskać więcej informacji, zobacz [wskazówki dla deweloperów](developer-guidance-for-integrating-applications.md).

- **Aplikacje spoza galerii** — przynieś własne aplikacje! Obsługa logowania jednokrotnego dla innych aplikacji przez dodanie ich do usługi Azure AD. Można zintegrować dowolne łącze internetowe lub dowolną aplikację, która renderuje pole nazwy użytkownika i hasła, obsługuje protokoły SAML lub OpenID Connect lub obsługuje scim. Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego dla aplikacji innych niż galeria](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Zarządzanie ryzykiem za pomocą zasad dostępu warunkowego

Łączenie usługi Azure AD logowania jednokrotnego (SSO) z [dostępem warunkowym](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) zapewnia wysoki poziom zabezpieczeń dostępu do aplikacji. Funkcje zabezpieczeń obejmują ochronę tożsamości w skali chmury, kontrolę dostępu opartą na ryzyku, natywne uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Te funkcje pozwalają na stosowanie zasad szczegółowej kontroli w oparciu o aplikacje lub grupy, które potrzebują wyższego poziomu zabezpieczeń.

## <a name="improve-productivity-with-single-sign-on"></a>Zwiększanie produktywności dzięki logowaniu jednokrotnemu

Włączenie logowania jednokrotnego w aplikacjach i usłudze Office 365 zapewnia doskonałe środowisko logowania dla istniejących użytkowników dzięki ograniczeniu lub wyeliminowaniu monitów dotyczących logowania. Środowisko użytkownika wydaje się bardziej spójne i mniej rozpraszające bez wielu monitów i potrzeby zarządzania wieloma hasłami. Grupa biznesowa może zarządzać dostępem i zatwierdzać go za pośrednictwem samoobsługi i członkostwa dynamicznego. Zezwolenie odpowiednim osobom osób w firmie na zarządzanie dostępem do aplikacji zwiększa bezpieczeństwo systemu tożsamości.

Logowanie jednokrotne zwiększa bezpieczeństwo. *Bez logowania jednokrotnego* administratorzy muszą tworzyć i aktualizować konta użytkowników dla poszczególnych aplikacji, co zajmuje czas. Ponadto użytkownicy muszą śledzić wiele poświadczeń, aby uzyskiwać dostęp do swoich aplikacji. W rezultacie użytkownicy zwykle notują hasła lub używają innych rozwiązań do zarządzania hasłami, które wprowadzają zagrożenia bezpieczeństwa danych. [Dowiedz się więcej o logowanie jednokrotne](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Obsługa zarządzania i zgodności

Dzięki usłudze Azure AD można monitorować logowania do aplikacji za pomocą raportów korzystających z narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Raporty są dostępne z poziomu portalu lub interfejsów API. Można również programowo przeprowadzać inspekcję użytkowników z dostępem do aplikacji i usuwać prawa dostępu do nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="manage-costs"></a>Zarządzanie kosztami

Dzięki migracji do usługi Azure AD można obniżyć koszty i rozwiązać problemy związane z zarządzaniem infrastrukturą lokalną. Usługa Azure AD oferuje również samoobsługowy dostęp do aplikacji, co pozwala zaoszczędzić czas pracy administratorów i użytkowników. Logowanie jednokrotne eliminuje hasła specyficzne dla aplikacji. Możliwość zalogowania się tylko raz pozwala zmniejszyć koszty związane z resetowaniem haseł aplikacji oraz utratą produktywności podczas pobierania haseł.

## <a name="next-steps"></a>Następne kroki

- [Co to jest serwer proxy aplikacji?](what-is-application-proxy.md)
- [Szybki start: dodawanie aplikacji galerii do dzierżawy usługi Azure AD](add-application-portal.md)

---
title: Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory | Microsoft Docs
description: W tym artykule przedstawiono korzyści wynikające z integracji usługi Azure Active Directory z aplikacji lokalnymi, w chmurze i SaaS.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/30/2018
ms.author: celested
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a65c42ff2d03946c021497e350836623a408ce7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291057"
---
# <a name="application-management-with-azure-active-directory"></a>Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory

Usługa Azure Active Directory (Azure AD) zapewnia bezpieczny i bezproblemowy dostęp do aplikacji lokalnych i działających w chmurze. Użytkownicy mogą logować się raz w celu uzyskania dostępu do usługi Office 365 i innych aplikacji biznesowych firmy Microsoft oraz aplikacji typu SaaS (oprogramowanie jako usługa), aplikacji lokalnych i aplikacji biznesowych (LOB). Aby zmniejszyć koszty administracyjne, można zautomatyzować aprowizowanie użytkowników. Użycie uwierzytelniania wieloskładnikowego i zasad dostępu warunkowego pozwoli zagwarantować bezpieczny dostęp do aplikacji.

![Aplikacje sfederowane przy użyciu usługi Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Dlaczego warto zarządzać aplikacjami przy użyciu rozwiązania w chmurze?

Organizacje mają często setki aplikacji, których użytkownicy potrzebują do wykonania swojej pracy. Użytkownicy uzyskują dostęp do tych aplikacji z wielu urządzeń i lokalizacji. Nowe aplikacje są dodawane, tworzone i wycofywane każdego dnia. Przy tak wielu aplikacjach i punktach dostępu zarządzanie dostępem użytkowników do wszystkich aplikacji przy użyciu rozwiązania opartego na chmurze jest teraz ważniejsze niż kiedykolwiek wcześniej.

## <a name="manage-risk-with-conditional-access-policies"></a>Zarządzanie ryzykiem przy użyciu zasad dostępu warunkowego
Połączenie logowania jednokrotnego do usługi Azure AD z zasadami dostępu warunkowego zapewnia wysoki poziom zabezpieczeń podczas uzyskiwania dostępu do aplikacji. Możliwości zabezpieczeń obejmują ochronę tożsamości w skali chmury, kontrolę dostępu opartą na ryzyku, natywne uwierzytelnianie wieloskładnikowe oraz zasady dostępu warunkowego. Te funkcje pozwalają na stosowanie zasad szczegółowej kontroli w oparciu o aplikacje lub grupy, które potrzebują wyższego poziomu zabezpieczeń.

## <a name="improve-productivity-with-single-sign-on"></a>Zwiększanie produktywności dzięki logowaniu jednokrotnemu
Włączenie logowania jednokrotnego w aplikacjach i usłudze Office 365 zapewnia doskonałe środowisko logowania dla istniejących użytkowników dzięki ograniczeniu lub wyeliminowaniu monitów dotyczących logowania. Środowisko użytkownika wydaje się bardziej spójne i mniej rozpraszające bez wielu monitów i potrzeby zarządzania wieloma hasłami. Grupa biznesowa może zarządzać dostępem i zatwierdzać go za pośrednictwem samoobsługi i członkostwa dynamicznego. Zezwolenie odpowiednim osobom osób w firmie na zarządzanie dostępem do aplikacji zwiększa bezpieczeństwo systemu tożsamości.

Logowanie jednokrotne zwiększa bezpieczeństwo. *Bez logowania jednokrotnego* administratorzy muszą tworzyć i aktualizować konta użytkowników dla poszczególnych aplikacji, co zajmuje czas. Ponadto użytkownicy muszą śledzić wiele poświadczeń, aby uzyskiwać dostęp do swoich aplikacji. W rezultacie użytkownicy zwykle notują hasła lub używają innych rozwiązań do zarządzania hasłami, które wprowadzają zagrożenia bezpieczeństwa danych. 

## <a name="address-governance-and-compliance"></a>Obsługa zarządzania i zgodności
Dzięki usłudze Azure AD można monitorować logowania do aplikacji za pomocą raportów korzystających z narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Raporty są dostępne z poziomu portalu lub interfejsów API. Można również programowo przeprowadzać inspekcję użytkowników z dostępem do aplikacji i usuwać prawa dostępu do nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="manage-costs"></a>Zarządzanie kosztami
Dzięki migracji do usługi Azure AD można obniżyć koszty i rozwiązać problemy związane z zarządzaniem infrastrukturą lokalną. Usługa Azure AD oferuje również samoobsługowy dostęp do aplikacji, co pozwala zaoszczędzić czas pracy administratorów i użytkowników. Logowanie jednokrotne eliminuje hasła specyficzne dla aplikacji. Możliwość zalogowania się tylko raz pozwala zmniejszyć koszty związane z resetowaniem haseł aplikacji oraz utratą produktywności podczas pobierania haseł.


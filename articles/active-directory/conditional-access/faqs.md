---
title: Często zadawane pytania dotyczące dostępu warunkowego usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące dostępu warunkowego w usłudze Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368090"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Często zadawane pytania dotyczące dostępu warunkowego usługi Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Które aplikacje współpracują z zasadami dostępu warunkowego?

Aby uzyskać informacje o aplikacjach korzystających z zasad dostępu warunkowego, zobacz [Aplikacje i przeglądarki korzystające z reguł dostępu warunkowego w usłudze Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Czy zasady dostępu warunkowego są wymuszane dla użytkowników współpracujących b2b i użytkowników-gości?

Zasady są wymuszane dla użytkowników współpracy między przedsiębiorstwami (B2B). Jednak w niektórych przypadkach użytkownik może nie być w stanie spełnić wymagań zasad. Na przykład organizacja użytkownika-gościa może nie obsługiwać uwierzytelniania wieloskładnikowego. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Czy zasady usługi SharePoint Online dotyczą również usługi OneDrive dla Firm?

Tak. Zasady usługi SharePoint Online dotyczą również usługi OneDrive dla Firm.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Dlaczego nie mogę ustawić zasad bezpośrednio w aplikacjach klienckich, takich jak Word lub Outlook?

Zasady dostępu warunkowego ustawia wymagania dotyczące uzyskiwania dostępu do usługi. Jest wymuszany, gdy nastąpi uwierzytelnianie do tej usługi. Zasady nie są ustawiane bezpośrednio w aplikacji klienckiej. Zamiast tego jest stosowany, gdy klient wywołuje usługę. Na przykład zestaw zasad w programie SharePoint ma zastosowanie do klientów wywołujących program SharePoint. Zestaw zasad w programie Exchange ma zastosowanie do programu Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Czy zasady dostępu warunkowego mają zastosowanie do kont usług?

Zasady dostępu warunkowego mają zastosowanie do wszystkich kont użytkowników. Obejmuje to konta użytkowników, które są używane jako konta usługi. Często konto usługi, które działa bez nadzoru, nie może spełnić wymagań zasad dostępu warunkowego. Na przykład może być wymagane uwierzytelnianie wieloskładnikowe. Konta usług można wykluczyć z zasad przy użyciu ustawień zarządzania zasadami dostępu warunkowego. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Czy interfejsy API programu Microsoft Graph są dostępne do konfigurowania zasad dostępu warunkowego?

Obecnie nie. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Jakie są domyślne zasady wykluczania nieobsługiwały platformy urządzeń?

Obecnie zasady dostępu warunkowego są selektywnie wymuszane na użytkownikach urządzeń z systemem iOS i Android. Domyślnie zasady dostępu warunkowego dla urządzeń z systemem iOS i Android nie mają wpływu na aplikacje na innych platformach urządzeń. Administrator dzierżawy może zastąpić zasady globalne, aby nie zezwalać na dostęp do użytkowników na platformach, które nie są obsługiwane.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak działają zasady dostępu warunkowego dla usługi Microsoft Teams?

Usługa Microsoft Teams w dużym stopniu opiera się na usłudze Exchange Online i usłudze SharePoint Online w przypadku podstawowych scenariuszy produktywności, takich jak spotkania, kalendarze i udostępnianie plików. Zasady dostępu warunkowego ustawione dla tych aplikacji w chmurze mają zastosowanie do usługi Microsoft Teams, gdy użytkownik loguje się bezpośrednio do usługi Microsoft Teams.

Usługa Microsoft Teams jest również obsługiwana oddzielnie jako aplikacja w chmurze w zasadach dostępu warunkowego usługi Azure Active Directory. Zasady dostępu warunkowego ustawione dla aplikacji w chmurze mają zastosowanie do usługi Microsoft Teams, gdy użytkownik się zaloguje. Jednak bez odpowiednich zasad w innych aplikacjach, takich jak Exchange Online i SharePoint Online użytkownicy mogą nadal mieć dostęp do tych zasobów bezpośrednio.

Klienci klasyczni Microsoft Teams dla systemów Windows i Mac obsługują nowoczesne uwierzytelnianie. Nowoczesne uwierzytelnianie powoduje, że logowanie oparte na bibliotece uwierzytelniania usługi Azure Active Directory (ADAL) jest dostępne dla aplikacji klienckich pakietu Microsoft Office na różnych platformach.

## <a name="next-steps"></a>Następne kroki

- Aby skonfigurować zasady dostępu warunkowego dla swojego środowiska, zobacz [Najważniejsze wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md). 

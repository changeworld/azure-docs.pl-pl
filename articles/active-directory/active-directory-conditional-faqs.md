---
title: Dostęp warunkowy usługi Azure Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące dostępu warunkowego w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ea93dab7df920f5a8ff8a1ae518b0bdbdcc80ae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723817"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Dostęp warunkowy usługi Azure Active Directory — często zadawane pytania

## <a name="which-applications-work-with-conditional-access-policies"></a>Aplikacje, które współpracuje z zasad dostępu warunkowego?

Aby uzyskać informacje o aplikacjach, które współpracują z zasadami dostępu warunkowego, zobacz [aplikacji i przeglądarki, które używały zasady dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Zasady dostępu warunkowego są wymuszane dla współpracy B2B i gości?

Zasady są wymuszane dla biznesowych między firmami (B2B) współpracy użytkowników. Jednak w niektórych przypadkach użytkownik może nie umożliwiać spełnia wymagania zasad. Na przykład organizacja użytkownika gościa mogą nie obsługiwać uwierzytelnianie wieloskładnikowe. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Czy zasady usługi SharePoint Online dotyczy również do usługi OneDrive dla firm?

Tak. Zasady usługi SharePoint Online ma również zastosowanie do usługi OneDrive dla firm.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Dlaczego nie można ustawić zasad w aplikacjach klienckich, takich jak Word i Outlook?

Zasady dostępu warunkowego Ustawia wymagania dotyczące uzyskiwania dostępu do usługi. Te funkcje są wprowadzane podczas uwierzytelniania do tej usługi. Nie ustawiono zasad bezpośrednio w aplikacji klienta. Zamiast tego jest stosowana, gdy klient wywołuje usługę. Na przykład zasadami ustawionymi w usłudze SharePoint ma zastosowanie do klientów podczas wywoływania programu SharePoint. Zestaw na serwerze Exchange zasad dotyczy programu Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Zasady dostępu warunkowego ma zastosowania do kont usług?

Zasady dostępu warunkowego są stosowane do wszystkich kont użytkowników. Obejmuje to konta użytkowników, które są używane jako konta usługi. Często konta usługi, który uruchamia instalacji nienadzorowanej nie może spełnić wymagań zasad dostępu warunkowego. Na przykład uwierzytelnianie wieloskładnikowe może być wymagane. Konta usług można wykluczyć z zasady przy użyciu ustawień zarządzania zasad dostępu warunkowego. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Interfejsy API Graph są dostępne do konfigurowania zasad dostępu warunkowego?

Aktualnie nie. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Co to jest domyślne zasady wykluczania dla platform nieobsługiwanego urządzenia?

Obecnie zasady dostępu warunkowego selektywnie są wymuszane dla użytkowników urządzeń iOS i Android. Aplikacje na innych platformach urządzeń domyślnie nie dotyczy zasady dostępu warunkowego dla urządzeń iOS i Android. Administrator dzierżawy możliwość zastąpienia globalnych zasad, aby uniemożliwić dostęp do użytkowników na platformach, które nie są obsługiwane.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak działają zasady dostępu warunkowego dla Teams firmy Microsoft?

Microsoft Teams zależy od silnie usługi Exchange Online i SharePoint Online w podstawowe scenariusze wydajności, takich jak spotkania, kalendarzy i udostępnianie plików. Zasady dostępu warunkowego, które są ustawione dla tych aplikacji w chmurze dotyczą Teams firmy Microsoft podczas loguje użytkownika bezpośrednio do Teams firmy Microsoft.

Teams Microsoft również jest obsługiwane oddzielnie jako aplikacji w chmurze w usłudze Azure Active Directory zasady dostępu warunkowego. Zasady dostępu warunkowego, które są ustawione dla aplikacji w chmurze dotyczą Teams firmy Microsoft, gdy użytkownik zaloguje się. Jednak bez poprawnych zasad na inne aplikacje, takie jak Exchange Online i SharePoint Online użytkownicy mogą nadal mieć możliwość bezpośrednio uzyskać dostęp do tych zasobów.

Klienci usług pulpitu Teams firmy Microsoft dla systemu Windows i Mac obsługuje nowoczesnego uwierzytelniania. Nowoczesne uwierzytelniane umożliwia logowanie oparte na Azure Active Directory Authentication Library (ADAL) dla aplikacji klienckich programu Microsoft Office na platformach.
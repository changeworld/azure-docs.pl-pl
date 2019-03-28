---
title: Dostęp warunkowy usługi Azure Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące dostępu warunkowego w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21c89d9d686c4af594aa7a0fd921471105669e02
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518154"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Dostęp warunkowy usługi Azure Active Directory — często zadawane pytania

## <a name="which-applications-work-with-conditional-access-policies"></a>Aplikacji, które współpracuje z zasad dostępu warunkowego?

Aby uzyskać informacje o aplikacjach, które działają z zasadami dostępu warunkowego, zobacz [aplikacji i przeglądarki, korzystających z zasad dostępu warunkowego w usłudze Azure Active Directory](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Zasady dostępu warunkowego obowiązują do współpracy B2B i użytkowników (gości)?

Zasady są wymuszane dla użytkowników współpracy, business-to-business (B2B). Jednak w niektórych przypadkach użytkownik może nie móc spełnia wymagania zasad. Na przykład organizacja użytkownika-gościa może nie obsługiwać uwierzytelnianie wieloskładnikowe. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Czy zasad usługi SharePoint Online dotyczy również do usługi OneDrive dla firm?

Tak. Zasady usługi SharePoint Online ma zastosowanie również do usługi OneDrive dla firm.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Dlaczego nie można ustawić zasady dla aplikacji klienckich, takich jak Word lub Outlook?

Zasady dostępu warunkowego Ustawia wymagania dotyczące dostępu do usługi. Wymuszeniu sytuacji uwierzytelniania dla danej usługi. Nie ustawiono zasad bezpośrednio w aplikacji klienckiej. Zamiast tego jest stosowane, gdy klient wywołuje usługę. Na przykład zestaw zasad w programie SharePoint dotyczy klientów wywołujących programu SharePoint. Zestaw zasad w Exchange ma zastosowanie do programu Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Zasady dostępu warunkowego ma zastosowania do kont usługi?

Zasady dostępu warunkowego mają zastosowanie do wszystkich kont użytkowników. Obejmuje to konta użytkowników, które są używane jako konta usług. Często konta usługi, które uruchamia instalacji nienadzorowanej nie może spełnić wymagań zasad dostępu warunkowego. Na przykład uwierzytelnianie wieloskładnikowe może być wymagane. Konta usług mogą być wykluczone z zasad za pomocą ustawienia zarządzania zasad dostępu warunkowego. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Interfejsy API programu Graph są dostępne do konfigurowania zasad dostępu warunkowego?

Aktualnie nie. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Co to jest domyślne zasady wykluczania dla platform nieobsługiwanego urządzenia?

Obecnie zasady dostępu warunkowego selektywnie są wymuszane dla użytkowników systemu iOS i android. Aplikacje na innych platformach domyślnie nie dotyczy zasad dostępu warunkowego dla systemu iOS i android. Administrator dzierżawy można wybrać opcję zastąpienia globalnych zasad, aby uniemożliwić dostęp do użytkowników na platformach, które nie są obsługiwane.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak działają zasady dostępu warunkowego dla Microsoft Teams

Microsoft Teams zależy od intensywnie usługi Exchange Online i SharePoint Online dla podstawowych scenariuszy wydajności, takich jak spotkania, kalendarze i udostępnianie plików. Zasady dostępu warunkowego, które są ustawione dla tych aplikacji w chmurze dotyczy Microsoft Teams po użytkownik zalogował bezpośrednio do Microsoft Teams.

Microsoft Teams także jest obsługiwany oddzielnie jako aplikacja w chmurze w ramach zasad dostępu warunkowego usługi Azure Active Directory. Zasady dostępu warunkowego, które są ustawione dla aplikacji w chmurze dotyczy Microsoft Teams, gdy użytkownik loguje się. Jednak bez prawidłowe zasady na inne aplikacje, takie jak Exchange Online i SharePoint Online użytkownicy mogą nadal mieć możliwość bezpośrednio dostęp do tych zasobów.

Klienci stacjonarni Microsoft Teams dla Windows i komputerów Mac obsługuje nowoczesnego uwierzytelniania. Nowoczesne uwierzytelniane umożliwia logowanie oparte na Azure Active Directory Authentication Library (ADAL) dla aplikacji klienckich Microsoft Office na różnych platformach.

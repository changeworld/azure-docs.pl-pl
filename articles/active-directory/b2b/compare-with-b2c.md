---
title: Porównanie współpracy B2B i B2C w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jaka jest różnica między współpracy B2B usługi Azure Active Directory i Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b59dba541394c105370cfd3af0768a8477ddb4e6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348388"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Porównanie współpracy B2B i B2C w usłudze Azure Active Directory

Współpraca B2B w usłudze Azure Active Directory (Azure AD) i usługi Azure AD B2C umożliwiają pracę użytkownikom zewnętrznym w usłudze Azure AD. Ale jak ich porównanie?


Funkcje współpracy B2B |     Autonomiczny oferty dla platformy Azure AD B2C
-------- | --------
Przeznaczony dla: organizacje, które mają mieć możliwość uwierzytelniania użytkowników z organizacji partnerskiej, niezależnie od dostawcy tożsamości. | Przeznaczony dla: zapraszanie klientów aplikacji mobilnych i aplikacji sieci web, czy osoby, użyteczności publicznej lub organizacji klientów w usłudze Azure AD.
Obsługiwane tożsamości: pracownikom lub kont służbowych, partnerzy korzystający z konta służbowego lub szkolnego bądź dowolnego adresu e-mail. Wkrótce umożliwiają bezpośrednie federacji.  | Obsługiwane tożsamości: konsumenta użytkowników za pomocą kont lokalnych aplikacji (wszystkie wiadomości e-mail adres lub nazwa użytkownika) lub dowolny obsługiwany tożsamości społecznościowych z Federacją bezpośrednich.
Katalog użytkowników z firm partnerskich, należą: użytkowników z firm partnerskich z zewnętrznego organizacji są zarządzane w tym samym katalogu co pracowników, ale specjalnie adnotację. Taki sam sposób jak pracownicy mogą być zarządzane, można dodać do tej samej grupy i tak dalej  | Jednostki użytkownika klienta znajdują się w katalogu: W katalogu aplikacji. Zarządzana oddzielnie od pracowników i partnerów katalogu organizacji (jeśli istnieje.
Logowanie jednokrotne (SSO) do wszystkich aplikacji platformy Azure połączonych AD jest obsługiwany. Na przykład można zapewnić dostęp do usługi Office 365 lub aplikacji lokalnych i innych aplikacji SaaS, takich jak Salesforce lub Workday.  |  Logowania jednokrotnego aplikacji w ramach dzierżawy usługi Azure AD B2C należących do klienta jest obsługiwane. Usługa rejestracji Jednokrotnej usługi Office 365 lub innych aplikacji firmy Microsoft i SaaS innych firm nie jest obsługiwane.
Cykl życia partnera: zarządzana przez hosta/zapraszanie organizacji.  | Cykl życia klienta: samoobsługowego lub zarządzane przez aplikację.
Zasady zabezpieczeń i zgodności: zarządzana przez hosta/zapraszanie organizacji (na przykład za pomocą [zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Zasady zabezpieczeń i zgodności: zarządzane przez aplikację.
Znakowanie: Marka hosta/zapraszanie organizacji jest używany.  |    Znakowanie: Zarządzane przez aplikację. Zazwyczaj jest zwykle produktu marki dzięki zanikanie organizacji w tle.
Więcej informacji: [wpis w blogu](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentacji](what-is-b2b.md)  | Więcej informacji: [stronę produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentacji](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Właściwości użytkowników współpracy B2B](user-properties.md)


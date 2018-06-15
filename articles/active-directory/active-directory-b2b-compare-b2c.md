---
title: Porównaj współpracy B2B i B2C w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jaka jest różnica między współpracy usługi Azure Active Directory B2B i usługi Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0e8781fcf2710b00ba352bff29a370cbb73e0648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33927887"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Porównaj współpracy B2B i B2C w usłudze Azure Active Directory

Zarówno współpracy B2B usługi Azure Active Directory (Azure AD), jak i usługi Azure AD B2C umożliwiają pracę z użytkowników zewnętrznych w usłudze Azure AD. Jednak sposób ich porównanie?


Możliwości współpracy B2B |     Azure AD B2C autonomicznej oferty.
-------- | --------
Przeznaczony dla: organizacji, które ma być możliwe do uwierzytelniania użytkowników w organizacji partnerskiej, niezależnie od dostawcy tożsamości. | Przeznaczony dla: zapraszanie klientów przenośnych i aplikacji sieci web, czy osoby, klienci użyteczności publicznej lub organizacji do usługi Azure AD.
Obsługiwane tożsamości: pracownikom pracy lub kont służbowych, partnerów z konta służbowego lub dowolnego adresu e-mail. Wkrótce w celu obsługi federacji bezpośredniego.  | Obsługiwane tożsamości: konsumenta użytkownikom kont lokalnych aplikacji (wszystkie wiadomości e-mail adres lub użytkownika nazwę) lub dowolny obsługiwany społecznościowych tożsamość w usłudze federacyjnej bezpośredniego.
Użytkowników z firm partnerskich znajdują się w katalogu: użytkowników z firm partnerskich z zewnętrznego organizacji są zarządzane w tym samym katalogu co pracowników, ale specjalnie adnotacji. Można zarządzać w taki sam sposób jak pracowników, można dodać do tej samej grupy i tak dalej  | Jednostkami użytkownika klienta znajdują się w katalogu: W katalogu aplikacji. Zarządzana oddzielnie od organizacji pracowników i partnerów katalog (jeśli istnieje.
Logowanie jednokrotne (SSO) we wszystkich aplikacjach Azure AD, połączony jest obsługiwana. Na przykład można zapewnić dostęp do lokalnych aplikacji lub usługi Office 365 i do innych aplikacji SaaS, takie jak Salesforce lub produktu Workday.  |  Logowanie Jednokrotne klienta należące do aplikacji w ramach dzierżaw usługi Azure AD B2C jest obsługiwana. Usługa rejestracji Jednokrotnej usługi Office 365 lub innych aplikacji firmy Microsoft i innych niż Microsoft SaaS nie jest obsługiwane.
Cykl życia partnera: zarządzane przez hosta/zapraszanie organizacji.  | Cykl życia klienta: Samoobsługowe lub zarządzanych przez aplikację.
Zasady zabezpieczeń i zgodności: zarządzane przez hosta/zapraszanie organizacji.  | Zasady zabezpieczeń i zgodności: zarządzane przez aplikację.
Znakowanie: Brand hosta/zapraszanie organizacji jest używany.  |    Znakowanie: Zarządzane przez aplikację. Zwykle zwykle jest produktu marki z Znikająca organizacji w tle.
Aby dowiedzieć się więcej: [wpis w blogu](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentacji](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Aby dowiedzieć się więcej: [stronę produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentacji](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Właściwości użytkownika współpracy B2B](active-directory-b2b-user-properties.md)


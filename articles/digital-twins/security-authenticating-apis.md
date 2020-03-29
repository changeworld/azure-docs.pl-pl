---
title: Opis uwierzytelniania interfejsu API — cyfrowe bliźniacze dane usługi Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak łączyć się z interfejsami API i uwierzytelniać się za pomocą usługi Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513007"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Łączenie się z interfejsami API i uwierzytelnianie ich

Usługa Azure Digital Twins używa usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników i ochrony aplikacji. Usługa Azure AD obsługuje uwierzytelnianie dla różnych nowoczesnych architektur. Wszystkie z nich są oparte na standardowych protokołach OAuth 2.0 lub OpenID Connect. Ponadto deweloperzy mogą używać usługi Azure AD do tworzenia aplikacji z jedną dzierżawą i usługą biznesowymi (LOB). Deweloperzy mogą również używać usługi Azure AD do tworzenia [aplikacji wielodostępnych.](how-to-multitenant-applications.md)

Aby zapoznać się z omówieniem usługi Azure AD, odwiedź [stronę podstawy,](https://docs.microsoft.com/azure/active-directory/fundamentals/) aby zapoznać się z przewodnikami krok po kroku, pojęciami i przewodnikami szybki start.

> [!TIP]
> Postępuj zgodnie z [samouczkiem,](tutorial-facilities-setup.md) aby skonfigurować i uruchomić przykładową aplikację usługi Azure Digital Twins.

Aby zintegrować aplikację lub usługę z usługą Azure AD, deweloper musi najpierw zarejestrować aplikację w usłudze Azure AD. Aby uzyskać szczegółowe instrukcje i zrzuty ekranu, przeczytaj [ten przewodnik Szybki start](../active-directory/develop/quickstart-register-app.md).

[Pięć scenariuszy aplikacji podstawowych](../active-directory/develop/v2-app-types.md) są obsługiwane przez usługę Azure AD:

* Aplikacja jednostronicowa (SPA): Użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczona przez usługę Azure AD.
* Przeglądarka sieci Web do aplikacji sieci Web: użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczona przez usługę Azure AD.
* Natywna aplikacja do interfejsu API sieci Web: natywna aplikacja uruchamiana na telefonie, tablecie lub komputerze musi uwierzytelnić użytkownika, aby uzyskać zasoby z internetowego interfejsu API zabezpieczonego usługą Azure AD.
* Aplikacja sieci Web do interfejsu API sieci Web: aplikacja sieci web musi uzyskać zasoby z interfejsu API sieci web zabezpieczone przez usługę Azure AD.
* Daemon lub aplikacja serwera do interfejsu API sieci web: aplikacja demona lub aplikacji serwera bez interfejsu użytkownika sieci web musi uzyskać zasoby z interfejsu API sieci web zabezpieczone przez usługę Azure AD.

> [!IMPORTANT]
> Usługa Azure Digital Twins obsługuje obie następujące biblioteki uwierzytelniania:
> * Nowsza [biblioteka uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Biblioteka uwierzytelniania usługi Azure Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Wywoływanie cyfrowych bliźniąt z interfejsu API sieci web klasy środkowej

Gdy deweloperzy twórz rozwiązania Digital Twins, zazwyczaj tworzą aplikację warstwy środkowej lub interfejs API. Następnie aplikacja lub interfejs API wywołuje interfejs API wiązki i obrazów cyfrowych. Aby obsługiwać tę standardową architekturę rozwiązania sieci Web, upewnij się, że użytkownicy najpierw:

1. Uwierzytelnij się za pomocą aplikacji warstwy środkowej

1. Token OAuth 2.0 w imieniu jest nabywany podczas uwierzytelniania

1. Nabyty token jest następnie używany do uwierzytelniania za pomocą interfejsów API lub wywoływania ich, które znajdują się dalej w dalszej części rzeki przy użyciu przepływu w imieniu

Aby uzyskać instrukcje dotyczące sposobu organizowania przepływu w imieniu, przeczytaj [OAuth 2.0 W imieniu przepływu](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Można również wyświetlić przykłady kodu w [wywoływaniu podrzędnego interfejsu API sieci web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować i przetestować usługę Azure Digital Twins przy użyciu niejawnego przepływu dotacji OAuth 2.0, przeczytaj artykuł [Konfigurowanie programu Postman](./how-to-configure-postman.md).

Aby dowiedzieć się więcej o zabezpieczeniach usługi Azure Digital Twins, przeczytaj artykuł [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md).

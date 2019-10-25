---
title: Informacje o uwierzytelnianiu interfejsu API za pomocą usługi Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się, jak łączyć się z interfejsami API i uwierzytelniać je za pomocą usługi Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 53b53fa5185ade87dfdd21f0805641657d49d676
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800214"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Nawiązywanie połączenia z interfejsami API i uwierzytelnianie przy użyciu

Usługa Azure Digital bliźniaczych reprezentacji korzysta z usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników i ochrony aplikacji. Usługa Azure AD obsługuje uwierzytelnianie w różnych nowoczesnych architekturach. Wszystkie z nich są oparte na standardach branżowych protokołu OAuth 2,0 lub OpenID Connect Connect. Ponadto deweloperzy mogą korzystać z usługi Azure AD w celu tworzenia aplikacji z jedną dzierżawą i aplikacjami biznesowymi (LOB). Deweloperzy mogą również używać usługi Azure AD do tworzenia aplikacji wielodostępnych.

Aby zapoznać się z omówieniem usługi Azure AD, odwiedź [stronę podstawy](https://docs.microsoft.com/azure/active-directory/fundamentals/) , aby uzyskać instrukcje krok po kroku, koncepcje i Przewodniki Szybki Start.

> [!TIP]
> Postępuj zgodnie z [samouczkiem](tutorial-facilities-setup.md) , aby skonfigurować i uruchomić przykładową aplikację usługi Azure Digital bliźniaczych reprezentacji.

Aby zintegrować aplikację lub usługę z usługą Azure AD, deweloper musi najpierw zarejestrować aplikację w usłudze Azure AD. Szczegółowe instrukcje i zrzuty ekranu znajdują się w [tym przewodniku szybki start](../active-directory/develop/quickstart-register-app.md).

Usługa Azure AD obsługuje [pięć scenariuszy aplikacji podstawowych](../active-directory/develop/v2-app-types.md) :

* Aplikacja jednostronicowa (SPA): użytkownik musi zalogować się do aplikacji jednostronicowej zabezpieczonej przez usługę Azure AD.
* Przeglądarka sieci Web do aplikacji sieci Web: użytkownik musi zalogować się do aplikacji sieci Web, która jest zabezpieczona przez usługę Azure AD.
* Natywna aplikacja do interfejsu API sieci Web: Natywna aplikacja działająca na telefonie, tablecie lub komputerze musi uwierzytelnić użytkownika w celu pobierania zasobów z internetowego interfejsu API, który jest zabezpieczony przez usługę Azure AD.
* Aplikacja sieci Web do interfejsu API sieci Web: aplikacja sieci Web musi pobrać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD.
* Demon lub aplikacja serwera do interfejsu API sieci Web: aplikacja demona lub aplikacja serwera bez interfejsu użytkownika sieci Web musi pobierać zasoby z internetowego interfejsu API zabezpieczonego przez usługę Azure AD.

> [!IMPORTANT]
> Usługa Azure Digital bliźniaczych reprezentacji obsługuje obie następujące biblioteki uwierzytelniania:
> * Najnowsza [Biblioteka uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Biblioteka Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Wywoływanie Digital bliźniaczych reprezentacji z poziomu internetowego interfejsu API sieci Web

Gdy deweloperzy korzystają z rozwiązań bliźniaczych reprezentacji Digital, zazwyczaj tworzą aplikację warstwy środkowej lub interfejs API. Aplikacja lub interfejs API wywołuje następnie interfejs API Digital bliźniaczych reprezentacji. Aby zapewnić obsługę tej standardowej architektury rozwiązania sieci Web, należy najpierw upewnić się, że użytkownicy:

1. Uwierzytelnianie za pomocą aplikacji warstwy środkowej

1. Token OAuth 2,0 w imieniu użytkownika jest uzyskiwany podczas uwierzytelniania

1. Uzyskany token jest następnie używany do uwierzytelniania za pomocą lub wywoływania interfejsów API, które są w pełni podrzędne przy użyciu przepływu.

Aby uzyskać instrukcje dotyczące sposobu organizowania przepływu w imieniu, zobacz [uwierzytelnianie OAuth 2,0 w imieniu użytkownika](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Możesz również wyświetlić przykłady kodu w [wywołaniu podrzędnego interfejsu API sieci Web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować i przetestować usługę Azure Digital bliźniaczych reprezentacji przy użyciu niejawnego przepływu OAuth 2,0, przeczytaj artykuł [Konfigurowanie wpisu](./how-to-configure-postman.md).

Aby dowiedzieć się więcej o zabezpieczeniach usługi Azure Digital bliźniaczych reprezentacji, przeczytaj artykuł [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md).
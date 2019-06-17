---
title: Omówienie uwierzytelniania interfejsu API Azure cyfrowego Twins | Dokumentacja firmy Microsoft
description: Użyj Twins cyfrowych platformy Azure, aby się i została uwierzytelniona do interfejsów API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533832"
---
# <a name="connect-and-authenticate-to-apis"></a>Się i została uwierzytelniona do interfejsów API

Azure Twins cyfrowego używa usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników oraz chronić aplikacji. Usługa Azure AD obsługuje uwierzytelnianie dla szerokiego zakresu nowoczesnych architektur. Wszystkie z nich są oparte na standardowych protokołów uwierzytelniania OAuth 2.0 lub OpenID Connect. Ponadto deweloperzy mogą używać usługi Azure AD, do tworzenia aplikacji pojedynczej dzierżawy i line-of-business (LOB) aplikacji. Tworzenie aplikacji wielodostępnych deweloperzy również mogą używać usługi Azure AD.

Omówienie usługi Azure AD, odwiedź stronę [strony podstawy](https://docs.microsoft.com/azure/active-directory/fundamentals/index) przewodniki krok po kroku, pojęcia i przewodników Szybki Start.

Aby zintegrować aplikację lub usługę z usługą Azure AD, deweloper musi najpierw zarejestrować aplikację w usłudze Azure AD. Aby uzyskać szczegółowe informacje i zrzuty ekranu, zobacz [ten przewodnik Szybki Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Pięciu scenariuszy aplikacji głównej](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) są obsługiwane przez usługę Azure AD:

* Aplikacja jednostronicowa (SPA): Użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczony przez usługę Azure AD.
* Przeglądarka sieci Web do aplikacji sieci web: Użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczony przez usługę Azure AD.
* Aplikacji natywnej do internetowego interfejsu API: Natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z interfejsu API sieci web, która jest zabezpieczony przez usługę Azure AD.
* Aplikacja sieci Web do internetowego interfejsu API: Aplikacja sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.
* Demon lub serwera aplikacji do internetowego interfejsu API: Aplikacji demona lub serwera z sieci web interfejsu użytkownika wymaga można pobrać zasobów z internetowego interfejsu API zabezpieczony przez usługę Azure AD.

Biblioteki uwierzytelniania usługi Windows Azure oferuje wiele sposobów uzyskania tokenów usługi Active Directory. Aby uzyskać szczegółowe informacje dotyczące biblioteki oraz przykłady kodu, zobacz [w tym artykule](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Wywoływanie cyfrowego bliźniaczych elementów z internetowego interfejsu API warstwy środkowej

Gdy deweloperom tworzyć architektury rozwiązań cyfrowych Twins, zwykle tworzą aplikacji warstwy środkowej lub interfejsu API. Aplikacji lub interfejsu API wywołuje cyfrowego API Twins podrzędne. Aby zapewnić obsługę tej architektury rozwiązania standardowe sieci web, upewnij się, że użytkownicy pierwszy:

1. Uwierzytelnianie za pomocą aplikacji warstwy środkowej

1. Token OAuth 2.0 "w imieniu" jest uzyskiwany podczas uwierzytelniania

1. Uzyskano token jest następnie używany do uwierzytelniania za pomocą lub wywoływania interfejsów API, które są podrzędne za pomocą przepływu w imieniu z

Aby uzyskać instrukcje dotyczące sposobu organizowania przepływu w imieniu z, zobacz [przepływ OAuth 2.0 "w imieniu"](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Możesz również wyświetlić przykłady kodu w [wywoływanie podrzędnego interfejsu web API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować i przetestować przy użyciu usługi flow przyznawanie niejawne protokołu OAuth 2.0 reprezentacji urządzeń cyfrowych platformy Azure, przeczytaj [Konfigurowanie narzędzia Postman](./how-to-configure-postman.md).

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).
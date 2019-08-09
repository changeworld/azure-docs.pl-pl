---
title: Omówienie scenariusza aplikacji obsługujących skrypty JavaScript — Microsoft Identity platform
description: Dowiedz się, jak utworzyć aplikację jednostronicową (omówienie scenariusza), która integruje platformę tożsamości firmy Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852513"
---
# <a name="scenario-single-page-application"></a>Scenariusz: Aplikacja jednostronicowa

Dowiedz się wszystkiego, co musisz zrobić, aby skompilować aplikację jednostronicową (SPA).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Możesz utworzyć swoją pierwszą aplikację, postępując zgodnie z przewodnikiem Autostart JavaScript:

> [!div class="nextstepaction"]
> [Szybki start: Aplikacja jednostronicowa](./quickstart-v2-javascript.md)

## <a name="overview"></a>Omówienie

Wiele nowoczesnych aplikacji sieci Web jest zbudowanych jako aplikacje jednostronicowe po stronie klienta, które są zapisywane przy użyciu języka JavaScript lub środowiska SPA, takiego jak kątowy, Vue. js i reaguje. js. Aplikacje te działają w przeglądarce internetowej i mają różne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera. Platforma tożsamości firmy Microsoft umożliwia aplikacjom jednostronicowym Logowanie użytkowników i uzyskiwanie tokenów w celu uzyskania dostępu do usług zaplecza lub interfejsów API sieci Web przy użyciu niejawnego [przepływu OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Niejawny przepływ umożliwia aplikacji uzyskanie tokenów identyfikatora reprezentujących uwierzytelnionego użytkownika, a także dostęp do tokenów wymaganych do wywołania chronionych interfejsów API.

![Aplikacje jednostronicowe](./media/scenarios/spa-app.svg)

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji wykorzystujących Międzyplatformowe platformy JavaScript, takie jak elektron, reagowanie na działania i tak dalej. ponieważ wymagają one dalszych możliwości interakcji z natywnymi platformami.

## <a name="specifics"></a>Szczegółowych informacji

Aby włączyć ten scenariusz dla aplikacji, wymagane są następujące aspekty:

* Rejestracja aplikacji w usłudze Azure AD obejmuje włączenie niejawnego przepływu i ustawienie identyfikatora URI przekierowania, do którego zwracane są tokeny.
* Konfiguracja aplikacji ze zarejestrowanymi właściwościami aplikacji, takimi jak identyfikator aplikacji.
* Używanie biblioteki MSAL do wykonywania przepływu uwierzytelniania w celu logowania i uzyskiwania tokenów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-spa-app-registration.md)

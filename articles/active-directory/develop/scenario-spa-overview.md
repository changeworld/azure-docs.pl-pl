---
title: Omówienie scenariusza aplikacji jednostronicowej JavaScript — platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji jednostronicowych (omówienie scenariusza), która integruje się z platformą Microsoft identity.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076363"
---
# <a name="scenario-single-page-application"></a>Scenariusz: Aplikacja jednostronicowa

Dowiedz się, wszystko, czego potrzebujesz do tworzenia aplikacji jednostronicowej (SPA).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Można utworzyć swoją pierwszą aplikację, postępując zgodnie z tego przewodnika Szybki Start JavaScript SPA:

> [!div class="nextstepaction"]
> [Szybki start: Aplikacja jednostronicowa](./quickstart-v2-javascript.md)

## <a name="overview"></a>Omówienie

Wiele nowoczesnych aplikacji sieci web są tworzone w aplikacji jednostronicowej po stronie klienta, napisane przy użyciu języka JavaScript lub SPA framework, takich jak Angular, Vue.js i React.js. Te aplikacje działają w przeglądarce sieci web i mają właściwości uwierzytelniania innego niż tradycyjne po stronie serwera sieci web aplikacji. Platforma tożsamości firmy Microsoft umożliwia aplikacji jednej strony logowania użytkowników i uzyskiwanie tokenów dostępu do usług zaplecza lub interfejsów API sieci web przy użyciu [niejawnego przepływu OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Niejawny przepływ umożliwia aplikacji w celu uzyskania tokenów Identyfikatora reprezentują uwierzytelnionego użytkownika, a także dostęp do tokenów potrzebnych do wywoływania interfejsów API chronionego.

![Aplikacje jednej strony](./media/scenarios/spa-app.svg)

Ten przebieg uwierzytelniania nie obejmuje scenariusze aplikacji przy użyciu platform JavaScript dla wielu platform, takich jak elektronów, React Native i tak dalej. ponieważ wymagają one więcej możliwości interakcji z natywnych platform.

## <a name="specifics"></a>Szczegółowe informacje

Następujące aspekty są wymagane do realizacji tego scenariusza aplikacji:

* Rejestrowanie aplikacji w usłudze Azure AD obejmuje włączenie niejawny przepływ i ustawienia identyfikatora URI przekierowania, do którego są zwracane tokenów.
* Konfiguracja aplikacji za pomocą właściwości zarejestrowanej aplikacji, takie jak identyfikator aplikacji.
* Czy przepływu autoryzacji logować się i uzyskać tokeny przy użyciu biblioteki MSAL.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-spa-app-registration.md)

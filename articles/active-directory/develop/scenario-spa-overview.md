---
title: Scenariusz aplikacji jednostronicowej JavaScript — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć jednostronicową aplikację (omówienie scenariusza) przy użyciu platformy tożsamości firmy Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885178"
---
# <a name="scenario-single-page-application"></a>Scenariusz: Aplikacja jednostronicowa

Dowiedz się wszystkiego, czego potrzebujesz, aby utworzyć jednostronicową aplikację (SPA).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Pierwszą aplikację można utworzyć, wykonując szybki start javascript spa:

> [!div class="nextstepaction"]
> [Szybki start: aplikacja jednostronicowa](./quickstart-v2-javascript.md)

## <a name="overview"></a>Omówienie

Wiele nowoczesnych aplikacji sieci web są tworzone jako aplikacje po stronie klienta jednostronicowe. Deweloperzy piszą je przy użyciu języka JavaScript lub struktury SPA, takich jak Angular, Vue.js i React.js. Te aplikacje są uruchamiane w przeglądarce sieci Web i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci web po stronie serwera. 

Platforma tożsamości firmy Microsoft umożliwia aplikacjom jednostronicowym logowanie się do użytkowników i uzyskiwanie tokenów w celu uzyskania dostępu do usług zaplecza lub internetowych interfejsów API przy użyciu [niejawnego przepływu OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Przepływ niejawny umożliwia aplikacji, aby uzyskać tokeny identyfikatora do reprezentowania uwierzytelnionego użytkownika, a także tokeny dostępu potrzebne do wywołania chronionych interfejsów API.

![Aplikacje jednostronicowe](./media/scenarios/spa-app.svg)

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które używają międzyplatformowych struktur JavaScript, takich jak Electron i React-Native. Wymagają one dalszych możliwości interakcji z platformami macierzystymi.

## <a name="specifics"></a>Specyfiki

Aby włączyć ten scenariusz dla aplikacji, należy:

* Rejestracja aplikacji w usłudze Azure Active Directory (Azure AD). Ta rejestracja obejmuje włączenie przepływu niejawnego i ustawienie identyfikatora URI przekierowania, do którego tokeny są zwracane.
* Konfiguracja aplikacji z zarejestrowanymi właściwościami aplikacji, takimi jak identyfikator aplikacji.
* Korzystanie z biblioteki uwierzytelniania firmy Microsoft (MSAL) w celu wykonania przepływu uwierzytelniania w celu zalogowania się i uzyskania tokenów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestracja aplikacji](scenario-spa-app-registration.md)

---
title: Rejestrowanie aplikacji z punktem końcowym v2.0 usługi Azure AD przy użyciu portalu | Dokumentacja firmy Microsoft
description: Jak zarejestrować aplikację z firmą Microsoft umożliwia włączenie logowania i uzyskiwania dostępu do usług firmy Microsoft przy użyciu punktu końcowego v2.0
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506549"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Jak zarejestrować aplikację za pośrednictwem punktu końcowego v2.0
Aby utworzyć aplikację, która akceptuje konto Microsoft (MSA) osobiste & pracy lub szkołą logowania konta (Azure AD), musisz najpierw zarejestrować aplikację w firmie Microsoft. W tej chwili nie można używać żadnych istniejących aplikacji, które mogą wiązać Ciebie z usługi Azure AD lub konta Microsoft — należy utworzyć zupełnie nowy.

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure Active Directory i funkcje są obsługiwane przez punkt końcowy w wersji 2.0. Aby ustalić, należy użyć punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Odwiedź portal rejestracji aplikacji firmy Microsoft
Przejdź do portalu rejestracji aplikacji firmy Microsoft pod adresem [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Logować się za pomocą osobistego i służbowego konta Microsoft. Jeśli nie masz żadnego z nich, załóż nowe konto osobiste.

Gotowe? Powinny teraz być patrzy lista aplikacji firmy Microsoft, czyli prawdopodobnie pusta. Teraz zmienimy.

Kliknij przycisk **Dodaj aplikację**i nadaj mu nazwę. Portalu spowoduje przypisanie globalnie unikatowy identyfikator aplikacji, której użyjesz później w kodzie aplikacji. Jeśli aplikacja zawiera składnik po stronie serwera wymaga tokeny dostępu dla wywoływaniu interfejsów API (myśl: Office, Azure lub własnego interfejsu API sieci web), należy utworzyć **klucz tajny aplikacji** również w tym miejscu.

Następnie dodaj **platform** używanego przez aplikację.

* W przypadku aplikacji opartych na sieci web, podaj **identyfikator URI przekierowania** wysyłania wiadomości do logowania.
* Dla aplikacji mobilnych należy skopiować przekierowania domyślne, których identyfikator URI utworzony automatycznie.
* Interfejsy API sieci web domyślny zakres dostępu do interfejsu API sieci Web jest tworzony automatycznie dla Ciebie. Możesz dodać dodatkowe zakresy, za pomocą **Dodaj zakres** przycisku. Wszystkie aplikacje, które są wstępnie autoryzowane do użycia przy użyciu interfejsu API sieci Web można również dodać **wstępnie autoryzowane aplikacje** formularza. 

Opcjonalnie można dostosować wygląd i działanie strony logowania w **profilu** sekcji. Upewnij się, że kliknij **Zapisz** przed kontynuowaniem.

> [!NOTE]
> Po utworzeniu aplikacji za pomocą [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), aplikacja zostanie zarejestrowany w głównej dzierżawy konta, którego używasz do logowania się do portalu. Oznacza to, że nie można zarejestrować aplikację w dzierżawie usługi Azure AD przy użyciu osobistego konta Microsoft. Jeśli chcesz jawnie zarejestrować aplikację w określonej dzierżawie, zaloguj się przy użyciu konta pierwotnie został utworzony w tej dzierżawie.


## <a name="build-a-quickstart-app"></a>Tworzenie aplikacji Szybki Start
Teraz masz aplikację firmy Microsoft, możesz wykonać czynności opisane jednym z samouczków szybkiego startu w wersji 2.0. Poniżej przedstawiono kilka zaleceń:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]


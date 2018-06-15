---
title: Rejestrowanie aplikacji z punktem końcowym v2.0 usługi Azure AD przy użyciu portalu | Dokumentacja firmy Microsoft
description: Jak zarejestrować aplikację w firmie Microsoft za włączanie logowania i uzyskiwanie dostępu do usług firmy Microsoft przy użyciu punktu końcowego v2.0
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
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157847"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Jak zarejestrować aplikację z punktem końcowym v2.0
Aby utworzyć aplikację, która akceptuje konto Microsoft (MSA) osobiste & pracy lub szkołą logowania konta (Azure AD), musisz najpierw zarejestrować aplikację w firmie Microsoft. W tej chwili nie można używać żadnych istniejących aplikacji, które mogą wiązać Ciebie z usługi Azure AD lub MSA — należy utworzyć nowy.

> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariuszy i funkcji obsługiwanych przez punktu końcowego v2.0. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Odwiedź portal rejestracji aplikacji firmy Microsoft
Po pierwsze, przejdź do portalu rejestracji aplikacji firmy Microsoft pod adresem [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Zaloguj się przy użyciu albo osobistego lub służbowe konto Microsoft. Jeśli nie masz albo utworzyć nowe konto osobiste.

Zrobić? Powinny teraz można patrzy listę aplikacji firmy Microsoft, który jest prawdopodobnie puste. Ta funkcja pozwala zmienić.

Kliknij przycisk **Dodaj aplikację**i nadaj mu nazwę. Portalu przypisze globalnie unikatowy identyfikator aplikacji, który będzie potrzebny później w kodzie aplikacji. Jeśli aplikacja zawiera składnik po stronie serwera wymaga tokenów dostępu dla wywołania interfejsów API (wziąć pod uwagę: Office, Azure lub własnego interfejsu API sieci web), należy utworzyć **klucz tajny aplikacji** również w tym miejscu.

Następnie dodaj **platform** używanego przez aplikację.

* W przypadku aplikacji sieci web, podaj **identyfikator URI przekierowania** wysyłania komunikatów do logowania.
* Dla aplikacji mobilnych skopiować przekierowania domyślny identyfikator URI utworzony automatycznie.
* Interfejsów API sieci web domyślny zakres na dostęp do interfejsu API sieci Web jest tworzona automatycznie dla Ciebie. Możesz dodać dodatkowe zakresy przy użyciu **Dodaj zakres** przycisku. Możesz także dodać wszystkie aplikacje, które są wstępnie autoryzowane do używania, przy użyciu interfejsu API sieci Web **wstępnie autoryzowanych aplikacji** formularza. 

Opcjonalnie można dostosować wygląd i działanie strony logowania w **profilu** sekcji. Upewnij się, że **zapisać** przed kontynuowaniem.

> [!NOTE]
> Po utworzeniu aplikacji przy użyciu [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), aplikacja zostanie zarejestrowana w domu dzierżawy konta, którego używasz do logowania się do portalu. Oznacza to, że nie można zarejestrować aplikację w dzierżawie usługi Azure AD za pomocą osobistego konta Microsoft. Jeśli chcesz jawnie zarejestrować aplikację w określonym dzierżawcy, zaloguj się przy użyciu konta pierwotnie utworzone w tej dzierżawie.


## <a name="build-a-quickstart-app"></a>Tworzenie aplikacji Szybki Start
Teraz, gdy masz aplikacji firmy Microsoft, można wykonać samouczków szybkiego startu w wersji 2.0. Poniżej przedstawiono kilka zaleceń:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]


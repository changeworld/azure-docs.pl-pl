---
title: Konfigurowanie uwierzytelniania konta Microsoft — Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie konta Microsoft dla aplikacji App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 5ef0cb2da26fcc00d1daf4b2dd0faf8bde8b743b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098520"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Jak skonfigurować aplikację App Service do korzystania z logowania do konta Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service do korzystania z konta Microsoft jako dostawcy uwierzytelniania. 

## <a name="register-microsoft-account"> </a>Zarejestruj aplikację na koncie Microsoft
1. Zaloguj się do [Azure Portal]i przejdź do swojej aplikacji. 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. Po zażądaniu przejdź do [**rejestracje aplikacji**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)i zaloguj się przy użyciu konto Microsoft.

1. Kliknij pozycję **Nowa rejestracja**, a następnie wpisz nazwę aplikacji.

1. W obszarze **identyfikatory URI przekierowania**wybierz pozycję **Sieć Web**, a następnie wpisz `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`polecenie. Zastąp  *\<ciąg App-Domain-Name >* nazwą domeny aplikacji.  Na przykład `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. 

   > [!NOTE]
   > Użyj schematu HTTPS w adresie URL.

1. Wybierz pozycję **zarejestruj**. 

1. Skopiuj **Identyfikator aplikacji (klienta)** . Będzie potrzebna później. 
   
7. W lewym panelu nawigacyjnym nowej rejestracji aplikacji wybierz pozycję **Certyfikaty & tajemnice** > **nowy klucz tajny klienta**. Podaj opis, wybierz okres ważności, a następnie wybierz pozycję **Dodaj**.

1. Skopiuj wartość, która pojawia się na stronie **certyfikaty &** wpisy tajne. Po opuszczeniu strony nie zostanie ona wyświetlona ponownie.

    > [!IMPORTANT]
    > Hasło jest ważnym poświadczeniem zabezpieczeń. Nie udostępniaj nikomu hasła ani nie rozpowszechniaj go w aplikacji klienckiej.

## <a name="secrets"> </a>Dodawanie informacji o koncie Microsoft do aplikacji App Service
1. W [Azure Portal]przejdź do swojej aplikacji. W lewym okienku nawigacji kliknij pozycję **uwierzytelnianie/autoryzacja**.

2. Jeśli funkcja uwierzytelniania/autoryzacji nie jest włączona, wybierz pozycję **włączone**.

3. W obszarze **dostawcy uwierzytelniania**wybierz pozycję **konto Microsoft**. Wklej w ramach identyfikatora aplikacji (klienta) i klucza tajnego klienta, który został uzyskany wcześniej, i opcjonalnie Włącz wszystkie zakresy wymagane przez aplikację. Następnie kliknij przycisk **OK**.

    Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.

4. Obowiązkowe Aby ograniczyć dostęp do konto Microsoft użytkowników, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** w celu zalogowania się **przy użyciu konta Microsoft**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do konto Microsoft na potrzeby uwierzytelniania.

> [!CAUTION]
> Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być odpowiednie dla aplikacji, które chcą korzystać z publicznie dostępnej strony głównej, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (nie akcja)** może być preferowane, z aplikacją ręcznie rozpoczynającą logowanie, zgodnie z opisem w [tym miejscu](overview-authentication-authorization.md#authentication-flow).

5. Kliknij polecenie **Zapisz**.

Teraz możesz przystąpić do uwierzytelniania w aplikacji za pomocą konta Microsoft.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/

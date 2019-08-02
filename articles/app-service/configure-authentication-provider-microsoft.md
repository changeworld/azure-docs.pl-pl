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
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 10b661f0c4b7dc45284b907e83df3c0372f97cab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561542"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Jak skonfigurować aplikację App Service do korzystania z logowania do konta Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service do korzystania z konta Microsoft jako dostawcy uwierzytelniania. 

## <a name="register-microsoft-account"> </a>Zarejestruj aplikację na koncie Microsoft
1. Zaloguj się do [Azure Portal]i przejdź do swojej aplikacji. Skopiuj **adres URL**, który później zostanie użyty do skonfigurowania aplikacji przy użyciu konta Microsoft.
2. Po zażądaniu przejdź do [**rejestracje aplikacji**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)i zaloguj się przy użyciu konto Microsoft.
3. Kliknij pozycję **Dodaj aplikację**, a następnie wpisz nazwę aplikacji, a następnie kliknij pozycję **Utwórz**.
4. Zanotuj **Identyfikator aplikacji**, ponieważ będzie on potrzebny później. 
5. W obszarze "platformy" kliknij pozycję **Dodaj platformę** i wybierz pozycję "Web".
6. W obszarze "Przekieruj identyfikatory URI" Podaj punkt końcowy dla aplikacji, a następnie kliknij przycisk **Zapisz**. 
   
   > [!NOTE]
   > Identyfikator URI przekierowania to adres URL aplikacji dołączonej ze ścieżką */.auth/login/MicrosoftAccount/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Upewnij się, że używasz schematu HTTPS.
   
7. W obszarze "wpisy tajne aplikacji" kliknij pozycję **Generuj nowe hasło**. Zanotuj wartość, która zostanie wyświetlona. Po opuszczeniu strony nie zostanie ona wyświetlona ponownie.

    > [!IMPORTANT]
    > Hasło jest ważnym poświadczeniem zabezpieczeń. Nie udostępniaj nikomu hasła ani nie rozpowszechniaj go w aplikacji klienckiej.
    
8. Kliknij polecenie **Zapisz**.

## <a name="secrets"> </a>Dodawanie informacji o koncie Microsoft do aplikacji App Service
1. Wróć do [Azure Portal], przejdź do aplikacji, kliknij pozycję **Ustawienia** > **uwierzytelnianie/autoryzacja**.
2. Jeśli funkcja uwierzytelniania/autoryzacji nie jest włączona, **Włącz ją.**
3. Kliknij pozycję **konto Microsoft**. Wklej wartości identyfikatora aplikacji i hasła, które zostały uzyskane wcześniej, i opcjonalnie Włącz wszystkie zakresy wymagane przez aplikację. Następnie kliknij przycisk **OK**.
   
    ![][1]
   
    Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
4. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez konto Microsoft, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** do **konta Microsoft**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do konto Microsoft na potrzeby uwierzytelniania.
5. Kliknij polecenie **Zapisz**.

Teraz możesz przystąpić do uwierzytelniania w aplikacji za pomocą konta Microsoft.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/

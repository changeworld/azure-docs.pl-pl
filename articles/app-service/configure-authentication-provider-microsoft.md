---
title: Konfigurowanie uwierzytelniania Account Microsoft — usłudze Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie Account Microsoft dla aplikacji usługi App Services.
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
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850959"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Jak skonfigurować aplikację App Service, aby używała Microsoft Account login
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie przedstawiono sposób konfigurowania usługi Azure App Service, aby użyć Account Microsoft jako dostawcy uwierzytelniania. 

## <a name="register-microsoft-account"> </a>Zarejestruj swoją aplikację przy użyciu konta Microsoft
1. Zaloguj się do [Azure Portal], a następnie przejdź do aplikacji. Kopiuj usługi **adresu URL**, umożliwiający później skonfigurować aplikację za pomocą Account Microsoft.
2. Przejdź do [Moje aplikacje] strony w programie Microsoft Account Developer Center, a następnie zaloguj się przy użyciu konta Microsoft, jeśli jest to wymagane.
3. Kliknij przycisk **Dodaj aplikację**, a następnie wpisz nazwę aplikacji i kliknij przycisk **Utwórz**.
4. Zwróć uwagę na **identyfikator aplikacji**, ponieważ będzie on potrzebny później. 
5. W obszarze "Platforms", kliknij **Dodaj platformy** i wybierz pozycję "Web".
6. W obszarze "Identyfikatory URI przekierowań" podać punktu końcowego dla aplikacji, a następnie kliknij przycisk **Zapisz**. 
   
   > [!NOTE]
   > Twojego przekierowania URI jest adres URL aplikacji dołączany ścieżki, */.auth/login/microsoftaccount/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Upewnij się, że będą używać schematu HTTPS.
   
7. W obszarze "Wpisów tajnych aplikacji," kliknij **wygenerować nowe hasło**. Zanotuj wartość, która jest wyświetlana. Gdy zechcesz opuścić stronę, go nie pojawi się ponownie.

    > [!IMPORTANT]
    > Hasło jest ważnym poświadczeniem zabezpieczeń. Nie udostępnić innym osobom hasło i rozpowszechnić ją w aplikacji klienckiej.
    
8. Kliknij pozycję **Zapisz**

## <a name="secrets"> </a>Dodaj informacje o Account firmy Microsoft do aplikacji usługi App Service
1. Ponownie [Azure Portal], przejdź do aplikacji, kliknij przycisk **ustawienia** > **uwierzytelniania / autoryzacji**.
2. Jeśli uwierzytelnianie / autoryzacja funkcja nie jest włączona, przełącz go **na**.
3. Kliknij przycisk **konta Microsoft**. Wklej wartości Identyfikatora aplikacji i hasło, które uzyskany wcześniej, a opcjonalnie włączyć wszystkie zakresy, wymaganych przez aplikację. Następnie kliknij przycisk **OK**.
   
    ![][1]
   
    Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. W kodzie aplikacji musi autoryzować użytkowników.
4. (Opcjonalnie) Aby ograniczyć dostęp do witryny użytkownikom tylko uwierzytelnione przez konto Microsoft, należy ustawić **akcji do wykonania w przypadku nieuwierzytelnionego żądania** do **Account Microsoft**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do konta Microsoft w celu uwierzytelniania.
5. Kliknij pozycję **Zapisz**.

Teraz można przystąpić do Account Microsoft uwierzytelniania w aplikacji.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Moje aplikacje]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/

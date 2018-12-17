---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory — usłudze Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie usługi Azure Active Directory dla aplikacji usługi App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: ae3460a7a513238613ac6b668b48cce747b96b23
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411058"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Skonfiguruj aplikację usługi App Service, aby używała logowania do usługi Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule przedstawiono sposób konfigurowania usługi Azure App Services do użycia usługi Azure Active Directory jako dostawcy uwierzytelniania.

## <a name="express"> </a>Konfigurowanie usługi Azure Active Directory przy użyciu ustawień ekspresowych
1. W [Azure Portal], przejdź do aplikacji usługi app Service. Na lewym pasku nawigacyjnym wybierz **uwierzytelniania / autoryzacji**.
2. Jeśli **uwierzytelniania / autoryzacji** nie jest włączone, wybierz opcję **na**.
3. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **Express** w obszarze **tryb zarządzania**.
4. Wybierz **OK** Aby zarejestrować aplikację usługi App Service w usłudze Azure Active Directory. Spowoduje to utworzenie nowej rejestracji aplikacji. Jeśli chcesz zamiast tego wybierz rejestracja istniejącej aplikacji, kliknij przycisk **wybierz istniejącą aplikację** a następnie wyszukaj nazwę rejestracji aplikacji utworzony wcześniej w ramach dzierżawy.
   Kliknij przycisk z rejestracji aplikacji, aby go zaznaczyć, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **OK** na stronie Ustawienia usługi Azure Active Directory.
   Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. W kodzie aplikacji musi autoryzować użytkowników.
5. (Opcjonalnie) Aby ograniczyć dostęp do witryny tylko użytkownikom uwierzytelniony przez usługę Azure Active Directory, należy ustawić **akcji do wykonania w przypadku nieuwierzytelnionego żądania** do **Zaloguj się przy użyciu usługi Azure Active Directory**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Azure Active Directory do uwierzytelniania.
6. Kliknij pozycję **Zapisz**.

Teraz można przystąpić do użycia usługi Azure Active Directory do uwierzytelniania w aplikacji usługi app Service.

## <a name="advanced"> </a>(Metoda alternatywna) Ręczne konfigurowanie usługi Azure Active Directory za pomocą ustawień zaawansowanych
Możesz również podać ustawienia konfiguracji ręcznie. Jest to preferowane rozwiązanie, jeśli dzierżawy usługi AAD, do której chcesz użyć różni się od dzierżawcy za pomocą którego zalogujesz się do platformy Azure. Aby ukończyć konfigurację, należy najpierw utworzyć rejestracji w usłudze Azure Active Directory, a następnie należy podać szczegóły rejestracji w usłudze App Service.

### <a name="register"> </a>Zarejestruj swoją aplikację usługi App Service z usługą Azure Active Directory
1. Zaloguj się do [Azure Portal], a następnie przejdź do aplikacji usługi app Service. Kopiowanie aplikacji **adresu URL**. Użytkownik zostanie ona użyta do skonfigurowania usługi rejestracji aplikacji usługi Azure Active Directory.
2. Przejdź do **usługi Active Directory**, a następnie wybierz **rejestracje aplikacji**, następnie kliknij przycisk **rejestrowanie nowej aplikacji** u góry, aby rozpocząć rejestrowanie nowej aplikacji. 
3. W **Utwórz** wpisz **nazwa** dla Twojej rejestracji aplikacji wybierz **aplikacji sieci Web / interfejs API** wpisz w **adres URL logowania** polu Wklej adres URL aplikacji (z kroku 1). Kliknij, aby **Utwórz**.
4. W ciągu kilku sekund powinien zostać wyświetlony Rejestracja nowej aplikacji, który został utworzony.
5. Po dodaniu rejestracji aplikacji, kliknij nazwę rejestracji aplikacji, kliknij pozycję **ustawienia** u góry strony, następnie kliknij pozycję **właściwości** 
6. W **identyfikator URI Identyfikatora aplikacji** pole, wklej adres URL aplikacji (z kroku 1), również w **adres URL strony głównej** Wklej adres URL aplikacji (z kroku 1), następnie kliknij przycisk **Zapisz**
7. Teraz kliknąć **adresy URL odpowiedzi**, Edytuj **adres URL odpowiedzi**, wklej adres URL aplikacji (z kroku 1), zmodyfikuj protokół, aby upewnić się, że masz **https://** protokołu (nie http://) następnie dołączany na końcu adresu URL, */.auth/login/aad/callback* (na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Kliknij pozycję **Zapisz**.   
8.  W tym momencie Skopiuj **identyfikator aplikacji** dla aplikacji. Utrzymaj w celu późniejszego użycia. Należy skonfigurować aplikację App Service.
9. Zamknij **zarejestrowana aplikacja** strony. Na **rejestracje aplikacji** kliknij na **punktów końcowych** przycisk u góry, a następnie skopiuj **dokument metadanych Federacji** adresu URL. 
10. Otwórz nowe okno przeglądarki i przejdź do adresu URL, wklejanie i przechodząc do strony XML. W górnej części dokumentu jest **EntityDescriptor** elementu. Znajdź **entityID** atrybutu i skopiuj jego wartość. Służy ona jako swojej **adres URL wystawcy**. Skonfiguruj aplikację do późniejszego użycia.

### <a name="secrets"> </a>Dodawanie informacji o usłudze Azure Active Directory do aplikacji usługi app Service
1. Ponownie [Azure Portal], przejdź do aplikacji usługi app Service. Kliknij przycisk **uwierzytelniania/autoryzacji**. Jeśli nie włączono funkcji uwierzytelniania/autoryzacji, ustaw przełącznik na **na**. Kliknij pozycję **usługi Azure Active Directory**, w obszarze dostawców uwierzytelniania, aby skonfigurować aplikację. (Opcjonalnie) Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. W kodzie aplikacji musi autoryzować użytkowników. Ustaw **akcji do wykonania w przypadku nieuwierzytelnionego żądania** do **Zaloguj się przy użyciu usługi Azure Active Directory**. Ta opcja wymaga, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Azure Active Directory do uwierzytelniania.
2. W konfiguracji uwierzytelniania usługi Active Directory, kliknij przycisk **zaawansowane** w obszarze **tryb zarządzania**. Wklej identyfikator aplikacji w polu Identyfikator klienta (z kroku 8) i wklej identyfikator jednostki (z kroku 10) na wartość adres URL wystawcy. Następnie kliknij przycisk **OK**.
3. Na stronie Konfiguracja uwierzytelniania usługi Active Directory kliknij **Zapisz**.

Teraz można przystąpić do użycia usługi Azure Active Directory do uwierzytelniania w aplikacji usługi app Service.

## <a name="optional-configure-a-native-client-application"></a>(Opcjonalnie) Konfigurowanie natywnej aplikacji klienckiej
Usługa Azure Active Directory umożliwia również zarejestrować natywnych klientów, co zapewnia większą kontrolę nad uprawnieniami mapowania. Jest to potrzebne, jeśli chcesz wykonać logowania przy użyciu biblioteki, takie jak **Active Directory Authentication Library**.

1. Przejdź do **usługi Azure Active Directory** w [Azure Portal].
2. Na lewym pasku nawigacyjnym wybierz **rejestracje aplikacji**. Kliknij przycisk **Rejestracja nowej aplikacji** u góry.
4. W **Utwórz** wpisz **nazwa** dla Twojej rejestracji aplikacji. Wybierz **natywnych** w **typ aplikacji**.
5. W **identyfikator URI przekierowania** wprowadź witryny */.auth/login/done* punktu końcowego, przy użyciu schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done*. W przypadku tworzenia aplikacji Windows, zamiast tego użyj [identyfikator SID pakietu](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identyfikator URI.
5. Kliknij pozycję **Utwórz**.
6. Po dodaniu rejestracji aplikacji, wybierz ją, aby go otworzyć. Znajdź **identyfikator aplikacji** i zanotuj tę wartość.
7. Kliknij przycisk **wszystkie ustawienia** > **wymagane uprawnienia** > **Dodaj** > **wybierz interfejs API**.
8. Wpisz nazwę aplikacji usługi App Service wcześniej zarejestrowany do wyszukiwania, a następnie wybierz ją i kliknij **wybierz**. 
9. Wybierz **dostępu \<nazwa_aplikacji >**. Następnie kliknij pozycję **Wybierz**. Następnie kliknij przycisk **Gotowe**.

Skonfigurowano natywnej aplikacji klienckiej, mogą uzyskiwać dostęp do aplikacji usługi app Service.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced

---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory dla aplikacji usługi aplikacji
description: Dowiedz się, jak skonfigurować uwierzytelnianie w usłudze Azure Active Directory dla aplikacji usługi aplikacji.
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
ms.openlocfilehash: 2530cb55cb054c02df5d55ccb86e959a061e2499
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155300"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Skonfiguruj aplikację usługi aplikacji, aby użyć nazwy logowania w usłudze Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule przedstawiono sposób konfigurowania usługi aplikacji Azure do użycia usługi Azure Active Directory jako dostawcy uwierzytelniania.

## <a name="express"> </a>Konfigurowanie usługi Azure Active Directory przy użyciu ustawień ekspresowych
1. W [portalu Azure], przejdź do aplikację usługi aplikacji. W lewym obszarze nawigacji, wybierz **uwierzytelniania / autoryzacji**.
2. Jeśli **uwierzytelniania / autoryzacji** nie jest włączony, wybierz opcję **na**.
3. Wybierz **usługi Azure Active Directory**, a następnie wybierz **Express** w obszarze **tryb zarządzania**.
4. Wybierz **OK** do rejestrowania aplikacji usługi aplikacji w usłudze Azure Active Directory. Spowoduje to utworzenie nowej rejestracji aplikacji. Jeśli chcesz zamiast tego wybrać rejestracja istniejącej aplikacji, kliknij przycisk **wybierz istniejącą aplikację** , a następnie wyszukaj nazwę rejestracji aplikacji wcześniej utworzonej w ramach dzierżawy.
   Kliknij przycisk rejestracji aplikacji, aby go zaznaczyć, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **OK** na stronie ustawień usługi Azure Active Directory.
   Domyślnie usługi App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do interfejsów API i zawartości witryny. Musisz zezwolić użytkownikom w kodzie aplikacji.
5. (Opcjonalnie) Aby ograniczyć dostęp do witryny użytkownikom tylko uwierzytelniony przez usługę Azure Active Directory, należy ustawić **działania należy podjąć w przypadku nieuwierzytelnionego żądania** do **Zaloguj się za pomocą usługi Azure Active Directory**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Azure Active Directory do uwierzytelniania.
6. Kliknij pozycję **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikację usługi aplikacji Azure Active Directory.

## <a name="advanced"> </a>(Metoda alternatywna) Ręczne konfigurowanie usługi Azure Active Directory z ustawieniami zaawansowanymi
Można również podać ustawienia konfiguracji ręcznie. Jest to preferowane rozwiązanie, jeśli dzierżawę usługi AAD, którego chcesz użyć różni się od dzierżawcy, z którym należy zalogować się do platformy Azure. W celu ukończenia konfiguracji, należy najpierw utworzyć rejestracji w usłudze Azure Active Directory, a następnie podaj niektórych szczegółów rejestracji usługi aplikacji.

### <a name="register"> </a>Zarejestrować aplikację usługi aplikacji w usłudze Azure Active Directory
1. Zaloguj się do [portalu Azure], a następnie przejdź do aplikację usługi aplikacji. Kopiowanie aplikacji **adres URL**. Umożliwia to skonfigurować rejestrację aplikacji usługi Azure Active Directory.
2. Przejdź do **usługi Active Directory**, a następnie wybierz pozycję **rejestracji aplikacji**, następnie kliknij przycisk **nowej rejestracji aplikacji** u góry, aby rozpocząć nowej rejestracji aplikacji. 
3. W **Utwórz** wprowadź **nazwa** dla rejestracji aplikacji, wybierz **aplikacji sieci Web / interfejs API** wpisz w **adres URL logowania** polu Wklej adres URL aplikacji (z kroku 1). Kliknij, aby **Utwórz**.
4. Powinna zostać wyświetlona nowej rejestracji aplikacji utworzony w ciągu kilku sekund.
5. Po dodaniu rejestracji aplikacji, kliknij na nazwie rejestracji aplikacji i kliknij pozycję **ustawienia** u góry, następnie kliknij polecenie **właściwości** 
6. W **identyfikator URI aplikacji** Wklej adres URL aplikacji (z kroku 1), również w **adres URL strony głównej** Wklej adres URL aplikacji (z kroku 1) oraz, następnie kliknij przycisk **Zapisz**
7. Teraz kliknij **adresy URL odpowiedzi**, Edytuj **adres URL odpowiedzi**, wklej adres URL aplikacji (z kroku 1), zmodyfikuj protokół upewnij się, że masz **https://** protokołu (nie http://) następnie jest dołączany na końcu adresu URL, */.auth/login/aad/callback* (na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Kliknij pozycję **Zapisz**.   
8.  W tym momencie Skopiuj **identyfikator aplikacji** dla aplikacji. Zachowaj go do późniejszego użycia. Należy go skonfigurować aplikację usługi aplikacji.
9. Zamknij **aplikacji zarejestrowanej** strony. Na **rejestracji aplikacji** kliknij pozycję **punkty końcowe** przycisk u góry, skopiuj **dokument metadanych usług federacyjnych** adresu URL. 
10. Otwórz nowe okno przeglądarki i przejdź do adresu URL wklejanie i przechodzenia do pliku XML. W górnej części dokumentu jest **EntityDescriptor** element, należy **entityID** atrybut w postaci `https://sts.windows.net/` następuje GUID przeznaczonych dla Twojej dzierżawy (nazywanych "identyfikator dzierżawy"). Skopiuj tę wartość — służy jako sieci **adres URL wystawcy**. Skonfiguruj aplikację do użycia w przyszłości.

### <a name="secrets"> </a>Dodawanie informacji o usłudze Azure Active Directory do aplikacji usługi aplikacji
1. W [portalu Azure], przejdź do aplikację usługi aplikacji. Kliknij przycisk **uwierzytelniania/autoryzacji**. Jeśli nie włączono funkcji uwierzytelniania/autoryzacji, włącz przełącznik do **na**. Polecenie **usługi Azure Active Directory**, w obszarze dostawcy uwierzytelniania, aby skonfigurować aplikację. (Opcjonalnie) Domyślnie usługi App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do interfejsów API i zawartości witryny. Musisz zezwolić użytkownikom w kodzie aplikacji. Ustaw **działania należy podjąć w przypadku nieuwierzytelnionego żądania** do **Zaloguj się za pomocą usługi Azure Active Directory**. Ta opcja wymaga, aby wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Azure Active Directory do uwierzytelniania.
2. W konfiguracji uwierzytelniania w usłudze Active Directory, kliknij przycisk **zaawansowane** w obszarze **tryb zarządzania**. Wklej identyfikator aplikacji w polu Identyfikatora klienta (z kroku 8) i Wklej entityId (z kroku 10) na wartość adres URL wystawcy. Następnie kliknij przycisk **OK**.
3. Na stronie konfiguracji uwierzytelniania w usłudze Active Directory, kliknij przycisk **zapisać**.

Teraz można przystąpić do uwierzytelniania w aplikację usługi aplikacji Azure Active Directory.

## <a name="optional-configure-a-native-client-application"></a>(Opcjonalnie) Skonfiguruj aplikację native client
Usługa Azure Active Directory umożliwia również zarejestrować klientach natywnych, co zapewnia większą kontrolę nad uprawnieniami mapowania. To konieczne, jeśli chcesz wykonać logowania przy użyciu biblioteki, takich jak **biblioteki uwierzytelniania usługi Active Directory**.

1. Przejdź do **usługi Azure Active Directory** w [portalu Azure].
2. W lewym obszarze nawigacji, wybierz **rejestracji aplikacji**. Kliknij przycisk **nowej rejestracji aplikacji** u góry.
4. W **Utwórz** wprowadź **nazwa** dla rejestracji aplikacji. Wybierz **natywnego** w **typu aplikacji**.
5. W **identyfikator URI przekierowania** wprowadź witryny */.auth/login/done* punktu końcowego, za pomocą schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done*. W przypadku tworzenia aplikacji systemu Windows, zamiast tego użyj [identyfikatora SID pakietu](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identyfikator URI.
5. Kliknij przycisk **Utwórz**.
6. Po dodaniu rejestracji aplikacji, wybierz, aby go otworzyć. Znajdź **identyfikator aplikacji** i zanotuj tę wartość.
7. Kliknij przycisk **wszystkie ustawienia** > **wymagane uprawnienia** > **Dodaj** > **wybierz interfejs API**.
8. Wpisz nazwę aplikacji usługi aplikacji został wcześniej zarejestrowany do wyszukiwania, a następnie zaznacz go i kliknij **wybierz**. 
9. Wybierz **dostępu \<nazwa_aplikacji >**. Następnie kliknij przycisk **wybierz**. Następnie kliknij przycisk **Gotowe**.

Aplikację klienta natywnego, które mogą uzyskiwać dostęp do aplikacji usługi aplikacji został skonfigurowany.

## <a name="related-content"> </a>Zawartość pokrewna
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
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[portalu Azure]: https://portal.azure.com/
[alternative method]:#advanced

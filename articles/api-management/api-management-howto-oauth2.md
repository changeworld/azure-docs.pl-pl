---
title: Autoryzuj konta deweloperów przy użyciu usługi OAuth 2.0 w usłudze API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak autoryzować użytkowników korzystających z usługi OAuth 2.0 w usłudze API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 90c890925378c30ce5688d2713990b4b2cdd20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430687"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Jak autoryzować konta deweloperów przy użyciu OAuth 2.0 w usłudze Azure API Management

Wiele interfejsów API obsługuje [OAuth 2.0,](https://oauth.net/2/) aby zabezpieczyć interfejs API i zapewnić dostęp tylko prawidłowym użytkownikom i mają dostęp tylko do zasobów, do których są uprawnieni. Aby korzystać z interaktywnej konsoli deweloperów usługi Azure API Management z takimi interfejsami API, usługa umożliwia skonfigurowanie wystąpienia usługi do pracy z interfejsem API obsługującym protokół OAuth 2.0.

> [!IMPORTANT]
> Autoryzacja OAuth 2.0 nie jest jeszcze dostępna w interaktywnej konsoli nowego portalu dewelopera.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Wymagania wstępne

W tym przewodniku pokazano, jak skonfigurować wystąpienie usługi zarządzania interfejsem API do używania autoryzacji OAuth 2.0 dla kont deweloperów, ale nie pokazuje, jak skonfigurować dostawcę OAuth 2.0. Konfiguracja dla każdego dostawcy OAuth 2.0 jest inna, chociaż kroki są podobne, a wymagane informacje używane do konfigurowania OAuth 2.0 w wystąpieniu usługi zarządzania interfejsami API są takie same. W tym temacie przedstawiono przykłady przy użyciu usługi Azure Active Directory jako dostawcy OAuth 2.0.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania OAuth 2.0 przy użyciu usługi Azure Active Directory, zobacz [przykład WebApp-GraphAPI-DotNet.][WebApp-GraphAPI-DotNet]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Konfigurowanie serwera autoryzacji OAuth 2.0 w usłudze API Management

> [!NOTE]
> Jeśli nie utworzono jeszcze wystąpienia usługi zarządzanie interfejsami API, zobacz [Tworzenie wystąpienia usługi zarządzania interfejsami API][Create an API Management service instance].

1. Kliknij kartę OAuth 2.0 w menu po lewej stronie i kliknij **+Dodaj**.

    ![Menu OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Wprowadź nazwę i opis opcjonalny w polach **Nazwa** i **Opis.**

    > [!NOTE]
    > Te pola są używane do identyfikowania serwera autoryzacji OAuth 2.0 w bieżącym wystąpieniu usługi zarządzania interfejsami API, a ich wartości nie pochodzą z serwera OAuth 2.0.

3. Wprowadź **adres URL strony rejestracji klienta**. Ta strona jest, gdzie użytkownicy mogą tworzyć i zarządzać swoimi kontami i różni się w zależności od używanego dostawcy OAuth 2.0. **Adres URL strony rejestracji klienta** wskazuje stronę, której użytkownicy mogą używać do tworzenia i konfigurowania własnych kont dla dostawców OAuth 2.0, które obsługują zarządzanie kontami przez użytkowników. Niektóre organizacje nie konfigurują ani nie używają tej funkcji, nawet jeśli dostawca OAuth 2.0 ją obsługuje. Jeśli dostawca OAuth 2.0 nie ma skonfigurowanego zarządzania kontami przez użytkowników, wprowadź tutaj zastępczy adres `https://placeholder.contoso.com`URL, taki jak adres URL firmy lub adres URL, taki jak .

    ![OAuth 2.0 nowy serwer](./media/api-management-howto-oauth2/oauth-02.png)

4. Następna sekcja formularza zawiera **typy dotacji autoryzacji**, **adres URL punktu końcowego autoryzacji**i ustawienia metody żądania **autoryzacji.**

    Określ **typy udzielania uprawnień,** sprawdzając żądane typy. **Kod autoryzacji** jest określony domyślnie.

    Wprowadź **adres URL punktu końcowego autoryzacji**. W przypadku usługi Azure Active Directory ten adres `<tenant_id>` URL będzie podobny do następującego adresu URL, gdzie jest zastępowany identyfikatorem dzierżawy usługi Azure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **Metoda żądania autoryzacji** określa sposób wysyłania żądania autoryzacji do serwera OAuth 2.0. Domyślnie jest zaznaczona opcja **GET.**

5. Następnie należy określić **adres URL punktu końcowego tokenu,** metody **uwierzytelniania klienta,** **metodę wysyłania tokenu dostępu** i zakres **domyślny.**

    ![OAuth 2.0 nowy serwer](./media/api-management-howto-oauth2/oauth-03.png)

    W przypadku serwera usługi Azure Active Directory OAuth 2.0 **adres URL punktu końcowego tokenu** będzie miał następujący format, w którym `<TenantID>` ma format . `yourapp.onmicrosoft.com`

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Domyślnym ustawieniem **metod uwierzytelniania klienta** jest **Basic**, a **metodą wysyłania tokenu dostępu** jest **nagłówek Autoryzacja**. Te wartości są konfigurowane w tej sekcji formularza wraz z **zakresem domyślnym**.

6. Sekcja **Poświadczenia klienta** zawiera identyfikator **klienta** i klucz **tajny klienta,** które są uzyskiwane podczas procesu tworzenia i konfigurowania serwera OAuth 2.0. Po określeniu **identyfikatora klienta** i **klucza tajnego klienta** generowany jest **redirect_uri** **kodu autoryzacji.** Ten identyfikator URI służy do konfigurowania adresu URL odpowiedzi w konfiguracji serwera OAuth 2.0.

    ![OAuth 2.0 nowy serwer](./media/api-management-howto-oauth2/oauth-04.png)

    Jeśli **typy udzielania dotacji autoryzacji** jest **ustawiona na hasło właściciela zasobu,** sekcja **Poświadczenia hasła właściciela zasobu** jest używana do określania tych poświadczeń; w przeciwnym razie można pozostawić puste.

    Po zakończeniu formularza kliknij przycisk **Utwórz,** aby zapisać konfigurację serwera autoryzacji API Management OAuth 2.0. Po zapisaniu konfiguracji serwera można skonfigurować interfejsy API do korzystania z tej konfiguracji, jak pokazano w następnej sekcji.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Konfigurowanie interfejsu API do korzystania z autoryzacji użytkownika OAuth 2.0

1. Kliknij pozycję **Interfejsy API** z menu **Zarządzanie interfejsami API** po lewej stronie.

    ![Interfejsy API OAuth 2.0](./media/api-management-howto-oauth2/oauth-05.png)

2. Kliknij nazwę żądanego interfejsu API i kliknij pozycję **Ustawienia**. Przewiń do sekcji **Zabezpieczenia,** a następnie zaznacz pole **wyboru OAuth 2.0**.

    ![Ustawienia OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Wybierz żądany **serwer autoryzacji** z listy rozwijanej i kliknij przycisk **Zapisz**.

    ![Ustawienia OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Starszy portal dla deweloperów — testowanie autoryzacji użytkownika OAuth 2.0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Po skonfigurowaniu serwera autoryzacji OAuth 2.0 i skonfigurowaniu interfejsu API do używania tego serwera można go przetestować, przechodząc do portalu dewelopera i wywołując interfejs API. Kliknij **pozycję Portal deweloperów (starszy)** w górnym menu na stronie **Omówienie** wystąpienia usługi Azure API Management.

Kliknij pozycję **Interfejsy API** w górnym menu i wybierz polecenie **Echo API**.

![Interfejs Echo API][api-management-apis-echo-api]

> [!NOTE]
> Jeśli istnieje tylko jeden interfejs API skonfigurowany lub widoczny dla Twojego konta, kliknięcie opcji Interfejsy API powoduje przejście bezpośrednio do operacji dla tego interfejsu API.

Wybierz operację **POBIERZ zasób,** kliknij pozycję **Otwórz konsolę**, a następnie wybierz z listy rozwijanej wybierz **pozycję Kod autoryzacji.**

![Otwarta konsola][api-management-open-console]

Po wybraniu **kodu autoryzacji** wyświetlane jest wyskakujące okno z formularzem logowania dostawcy OAuth 2.0. W tym przykładzie formularz logowania jest dostarczany przez usługę Azure Active Directory.

> [!NOTE]
> Jeśli masz wyłączone wyskakujące okienka, zostaniesz poproszony o włączenie ich przez przeglądarkę. Po ich włączeniu wybierz **kod autoryzacji** ponownie i zostanie wyświetlony formularz logowania.

![Logowanie][api-management-oauth2-signin]

Po zalogowaniu się **nagłówki żądania** są wypełniane nagłówkiem, `Authorization : Bearer` który autoryzuje żądanie.

![Token nagłówka żądania][api-management-request-header-token]

W tym momencie można skonfigurować żądane wartości dla pozostałych parametrów i przesłać żądanie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z OAuth 2.0 i API Management, zobacz poniższy klip wideo i towarzyszący [mu artykuł](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps


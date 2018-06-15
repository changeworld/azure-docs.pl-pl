---
title: Autoryzowanie konta dewelopera przy użyciu protokołu OAuth 2.0 w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i autoryzacji użytkowników w usłudze API Management przy użyciu protokołu OAuth 2.0.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: f3611fa4da571dd74d844c7fad45788ece372be4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937236"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Sposób autoryzowania konta dewelopera w usłudze Azure API Management przy użyciu protokołu OAuth 2.0
Obsługuje wiele interfejsów API [OAuth 2.0](http://oauth.net/2/) Zabezpiecz interfejs API i sprawdź, czy tylko prawidłowe użytkowników i ich tylko dostęp do zasobów, do których jest uprawniony. Aby można było używać interakcyjne konsoli dewelopera usługi Azure API Management z tych interfejsów API, usługę pozwala na skonfigurowanie wystąpienia usługi do pracy z Twojej OAuth 2.0 włączone interfejsu API.

## <a name="prerequisites"> </a>Wymagania wstępne
W tym przewodniku opisano sposób skonfigurować wystąpienie usługi Zarządzanie interfejsami API autoryzacji OAuth 2.0 na potrzeby konta dewelopera, ale nie opisano, jak skonfigurować dostawcę uwierzytelniania OAuth 2.0. Różni konfiguracji dla każdego dostawcy OAuth 2.0, mimo że te kroki są podobne, a informacje używane do konfigurowania protokołu OAuth 2.0 w wystąpieniu usługi Zarządzanie interfejsami API wymagane elementy są takie same. W tym temacie przedstawiono przykłady użycia usługi Azure Active Directory jako dostawcy uwierzytelniania OAuth 2.0.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania OAuth 2.0 przy użyciu usługi Azure Active Directory, zobacz [aplikacji sieci Web-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] próbki.
> 
> 

## <a name="step1"> </a>Konfigurowanie serwera autoryzacji OAuth 2.0 w usłudze API Management
Na początku kliknij opcję **Portal wydawcy** w klasycznej witrynie Azure Portal dla usługi API Management.

![Publisher portal][api-management-management-console]

> [!NOTE]
> Jeśli jeszcze nie utworzono wystąpienie usługi API Management, zobacz [Utwórz wystąpienie usługi Zarządzanie interfejsami API][Create an API Management service instance].
> 
> 

Kliknij przycisk **zabezpieczeń** z **zarządzanie interfejsami API** menu po lewej stronie, kliknij **OAuth 2.0**, a następnie kliknij przycisk **serwera autoryzacji Dodaj**.

![Uwierzytelnianie OAuth 2.0][api-management-oauth2]

Po kliknięciu przycisku **serwera autoryzacji Dodaj**, zostanie wyświetlony nowy formularz serwera autoryzacji.

![Nowy serwer][api-management-oauth2-server-1]

Wprowadź nazwę i opcjonalny opis w **nazwa** i **opis** pola. 

> [!NOTE]
> Te pola są używane do identyfikowania serwera autoryzacji OAuth 2.0, w ramach bieżącego wystąpienia usługi API Management i ich wartości nie pochodzą z serwera OAuth 2.0.
> 
> 

Wprowadź **adres URL strony rejestracji klienta**. Ta strona jest, gdzie użytkownicy mogą tworzyć i zarządzać ich kont i może być różna w zależności od dostawcy uwierzytelniania OAuth 2.0 używane. **Adres URL strony rejestracji klienta** wskazuje strona, do której użytkownicy mogą używać, aby utworzyć i skonfigurować swoje własne konta dla dostawcy OAuth 2.0, które obsługują zarządzanie kontami użytkowników. Niektóre organizacje nie Konfiguruj lub korzystać z tej funkcji, nawet jeśli obsługuje dostawcy OAuth 2.0. Jeśli dostawcy OAuth 2.0 nie zarządzania skonfigurowano kont użytkownika, wprowadź symbolu zastępczego adresu URL w tym miejscu, takie jak adres URL firmy, lub takie jak `https://placeholder.contoso.com`.

Następna sekcja formularza zawiera **typy przydziałów kod autoryzacji**, **adres URL punktu końcowego autoryzacji**, i **metoda żądania autoryzacji** ustawienia.

![Nowy serwer][api-management-oauth2-server-2]

Określ **typy przydziałów kod autoryzacji** sprawdzając odpowiednie typy. **Kod autoryzacji** jest określony, domyślnie.

Wprowadź **adres URL punktu końcowego autoryzacji**. Dla usługi Azure Active Directory, ten adres URL powinien być podobny do następującego adresu URL, gdzie `<client_id>` jest zastępowany identyfikatora klienta, który identyfikuje aplikację na serwerze OAuth 2.0.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

**Metoda żądania autoryzacji** Określa, jak żądania autoryzacji jest wysyłane do serwera OAuth 2.0. Domyślnie **UZYSKAĆ** jest zaznaczone.

Następna sekcja jest, gdy **tokenu końcowego adresu URL**, **metody uwierzytelniania klienta**, **token dostępu metody wysyłania**, i **zakresudomyślnego**zostały określone.

![Nowy serwer][api-management-oauth2-server-3]

Dla serwera usługi Azure Active Directory OAuth 2.0 **tokenu końcowego adresu URL** będzie mieć następujący format, gdzie `<APPID>` ma format `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

Ustawieniem domyślnym dla **metody uwierzytelniania klienta** jest **podstawowe**, i **token dostępu metody wysyłania** jest **nagłówek autoryzacji**. Te wartości są skonfigurowane w tej sekcji formularza, wraz z **zakresu domyślnego**.

**Poświadczeń klienta** sekcja zawiera **identyfikator klienta** i **klucz tajny klienta**, które są uzyskiwane podczas procesu tworzenia i konfiguracji serwera OAuth 2.0 . Raz **identyfikator klienta** i **klucz tajny klienta** są określone, **redirect_uri** dla **kod autoryzacji** jest generowany. Ten identyfikator URI jest używane do konfigurowania adresu URL odpowiedzi w konfiguracji serwera OAuth 2.0.

![Nowy serwer][api-management-oauth2-server-4]

Jeśli **typy przydziałów kod autoryzacji** ustawiono **hasło właściciela zasobów**, **poświadczenia hasła właściciela zasobu** sekcji służy do określania tych poświadczeń; w przeciwnym razie można pozostawić on pusty.

![Nowy serwer][api-management-oauth2-server-5]

Po zakończeniu formularza, kliknij przycisk **zapisać** Aby zapisać konfigurację serwera autoryzacji interfejsu API zarządzania OAuth 2.0. Po zapisaniu konfiguracji serwera, można skonfigurować interfejsów API do używania tej konfiguracji, jak pokazano w następnej sekcji.

## <a name="step2"> </a>Skonfiguruj interfejs API do używania uwierzytelniania użytkownika OAuth 2.0
Kliknij przycisk **interfejsów API** z **zarządzanie interfejsami API** menu po lewej stronie, kliknij nazwę żądanego interfejsu API, kliknij przycisk **zabezpieczeń**, a następnie zaznacz pole wyboru, aby uzyskać **OAuth 2.0**.

![Uwierzytelnianie użytkownika][api-management-user-authorization]

Wybierz żądaną **serwera autoryzacji** z listy rozwijanej, a następnie kliknij przycisk **zapisać**.

![Uwierzytelnianie użytkownika][api-management-user-authorization-save]

## <a name="step3"> </a>Testowanie autoryzacji użytkownika OAuth 2.0 w portalu dla deweloperów
Po skonfigurowaniu serwera autoryzacji OAuth 2.0 i skonfigurowany interfejs API do używania tego serwera, przetestować go, przechodząc do portalu dla deweloperów i wywoływanie interfejsu API.  Kliknij przycisk **Portal dla deweloperów** w prawym górnym menu.

![Portal dla deweloperów][api-management-developer-portal-menu]

Kliknij przycisk **interfejsów API** w menu u góry, a następnie wybierz **Echo API**.

![Interfejs Echo API][api-management-apis-echo-api]

> [!NOTE]
> Jeśli istnieje tylko jeden interfejs API skonfigurowany lub widoczny dla Twojego konta, kliknięcie opcji Interfejsy API powoduje przejście bezpośrednio do operacji dla tego interfejsu API.
> 
> 

Wybierz **UZYSKAĆ zasobu** operacji, kliknij przycisk **Otwórz konsolę**, a następnie wybierz **kod autoryzacji** z listy rozwijanej.

![Otwarta konsola][api-management-open-console]

Gdy **kod autoryzacji** jest zaznaczone, okno podręczne jest wyświetlana w formularzu logowania dostawcy OAuth 2.0. W tym przykładzie formularz logowania są udostępniane przez usługi Azure Active Directory.

> [!NOTE]
> Jeśli masz wyskakujące okienka wyłączone pojawi się monit je włączyć w przeglądarce. Po włączeniu je wybrać **kod autoryzacji** ponownie i zostanie wyświetlony formularz logowania.
> 
> 

![Logowanie][api-management-oauth2-signin]

Po zalogowaniu, **nagłówki żądań** są wypełniane przy użyciu `Authorization : Bearer` nagłówek, który autoryzuje żądanie.

![Token nagłówka żądania][api-management-request-header-token]

W tym momencie można skonfigurować odpowiednie wartości dla pozostałych parametrów i prześlij żądanie. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o korzystaniu z protokołu OAuth 2.0 i zarządzanie interfejsami API, zobacz następujące tematy wideo i towarzyszące [artykułu](api-management-howto-protect-backend-with-aad.md).


[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps


---
title: Autoryzowanie kont deweloperów za pomocą protokołu OAuth 2.0 w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak autoryzować użytkowników w usłudze API Management przy użyciu protokołu OAuth 2.0.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: b195271edeea6cd5ea527454ad1615ac85a32138
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658687"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Jak autoryzowanie kont deweloperów w usłudze Azure API Management przy użyciu protokołu OAuth 2.0

Obsługuje wiele interfejsów API [OAuth 2.0](https://oauth.net/2/) zabezpieczanie interfejsu API i upewnij się, że tylko uprawnieni użytkownicy mają dostęp i mają dostęp tylko zasoby, do których masz prawo. Aby można było używać interakcyjnej konsoli dla deweloperów usługi Azure API Management za pomocą tych interfejsów API, usługa pozwala na skonfigurowanie wystąpienia usługi chcesz pracować z protokołu OAuth 2.0 włączony interfejs API.

## <a name="prerequisites"> </a>Wymagania wstępne

Ten przewodnik pokazuje, jak skonfigurować wystąpienia usługi API Management na potrzeby autoryzacji OAuth 2.0 konta dewelopera, ale nie pokazują, jak skonfigurować dostawcę uwierzytelniania OAuth 2.0. Konfiguracja dla każdego dostawcy uwierzytelniania OAuth 2.0 jest inny, chociaż kroki są podobne i wymagane elementy informacje używane do konfigurowania protokołu OAuth 2.0 w wystąpienia usługi API Management są takie same. W tym temacie przedstawiono przykłady użycia usługi Azure Active Directory jako dostawcy uwierzytelniania OAuth 2.0.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania OAuth 2.0 przy użyciu usługi Azure Active Directory, zobacz [aplikacji sieci Web-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] próbki.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Skonfiguruj serwer autoryzacji OAuth 2.0 w usłudze API Management

> [!NOTE]
> Jeśli jeszcze nie utworzono wystąpienia usługi API Management, zobacz [Tworzenie wystąpienia usługi API Management][Create an API Management service instance].

1. Kliknij kartę OAuth 2.0, w menu po lewej stronie i kliknij **+ Dodaj**.

    ![Menu OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Wprowadź nazwę i opcjonalny opis w **nazwa** i **opis** pola.

    > [!NOTE]
    > Te pola są używane do identyfikowania serwera autoryzacji OAuth 2.0, w ramach bieżącego wystąpienia usługi API Management i ich wartości nie są udzielane przez firmę serwer OAuth 2.0.

3. Wprowadź **adres URL strony rejestracji klienta**. Ta strona jest, gdzie użytkownicy mogą tworzyć i zarządzać swoimi kontami i różni się zależnie od dostawcy uwierzytelniania OAuth 2.0 używane. **Adres URL strony rejestracji klienta** wskazuje stronę która umożliwia użytkownikom tworzenie i konfigurowanie własnych kont dla dostawców OAuth 2.0, które obsługują zarządzanie kontami użytkowników. W niektórych organizacjach skonfigurować lub nie korzystać z tej funkcji, nawet wtedy, gdy dostawca uwierzytelniania OAuth 2.0 obsługuje tę funkcję. Jeśli Twój dostawca uwierzytelniania OAuth 2.0 nie Zarządzanie skonfigurowanych kont, wprowadź symbolu zastępczego adresu URL w tym miejscu takie jak adres URL Twojej firmy lub adresu URL takich jak `https://placeholder.contoso.com`.

    ![Nowy serwer OAuth 2.0](./media/api-management-howto-oauth2/oauth-02.png)

4. Następna sekcja formularza zawiera **typy przydziałów autoryzacji**, **adresu URL punktu końcowego autoryzacji**, i **metoda żądania autoryzacji** ustawienia.

    Określ **typy przydziałów autoryzacji** , sprawdzając żądanego typu. **Kod autoryzacji** jest określony, domyślnie.

    Wprowadź **adresu URL punktu końcowego autoryzacji**. Usługi Azure Active Directory, ten adres URL będzie podobny do następującego adresu URL, gdzie `<client_id>` jest zastępowana identyfikatorem klienta, który identyfikuje aplikację, aby serwer OAuth 2.0.

    `https://login.microsoftonline.com/<client_id>/oauth2/authorize`

    **Metoda żądania autoryzacji** Określa, jak żądanie autoryzacji są wysyłane do serwera protokołu OAuth 2.0. Domyślnie **UZYSKAĆ** jest zaznaczone.

5. Następnie **adresu URL punktu końcowego tokenu**, **metod uwierzytelniania klienta**, **Metoda wysyłania tokenu dostępu** i **zakresu domyślnego** muszą być określona.

    ![Nowy serwer OAuth 2.0](./media/api-management-howto-oauth2/oauth-03.png)

    Dla serwera usługi Azure Active Directory OAuth 2.0 **adresu URL punktu końcowego tokenu** będzie mieć następujący format, gdzie `<TenantID>` ma format `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Ustawieniem domyślnym dla **metod uwierzytelniania klienta** jest **podstawowe**, i **Metoda wysyłania tokenu dostępu** jest **nagłówek autoryzacji**. Te wartości są skonfigurowane w tej części formularza, wraz z **zakresu domyślnego**.

6. **Poświadczeń klienta** sekcja zawiera **identyfikator klienta** i **klucz tajny klienta**, które są uzyskiwane podczas procesu tworzenia i konfiguracji serwera protokołu OAuth 2.0 . Raz **identyfikator klienta** i **klucz tajny klienta** są określone, **redirect_uri** dla **kod autoryzacji** jest generowany. Ten identyfikator URI jest używany do konfigurowania adresu URL odpowiedzi w konfiguracji serwera protokołu OAuth 2.0.

    ![Nowy serwer OAuth 2.0](./media/api-management-howto-oauth2/oauth-04.png)

    Jeśli **typy przydziałów autoryzacji** ustawiono **hasło właściciela zasobu**, **poświadczenia hasła właściciela zasobu** sekcja jest używana do określenia tych poświadczeń, a w przeciwnym może pozostać puste.

    Po zakończeniu formularza kliknij **Utwórz** Aby zapisać konfigurację serwera autoryzacji interfejsu API zarządzania protokołu OAuth 2.0. Po zapisaniu konfiguracji serwera można skonfigurować interfejsów API, aby użyć tej konfiguracji, jak pokazano w następnej sekcji.

## <a name="step2"> </a>Konfigurowanie interfejsu API do użycia autoryzacji użytkownika OAuth 2.0

1. Kliknij przycisk **interfejsów API** z **usługi API Management** menu po lewej stronie.

    ![Interfejsy API protokołu OAuth 2.0](./media/api-management-howto-oauth2/oauth-05.png)

2. Kliknij nazwę żądanego interfejsu API i kliknij przycisk **ustawienia**. Przewiń do **zabezpieczeń** sekcji, a następnie zaznacz pole wyboru, aby uzyskać **OAuth 2.0**.

    ![Ustawienia protokołu OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Wybierz żądany **serwera autoryzacji** z listy rozwijanej, a następnie kliknij przycisk **Zapisz**.

    ![Ustawienia protokołu OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Testowanie autoryzacji użytkownika OAuth 2.0 w portalu dla deweloperów

Po skonfigurowaniu serwera autoryzacji OAuth 2.0 i skonfigurować ten serwer jest używany przez interfejs API, można ją przetestować, przechodząc do portalu dla deweloperów i wywoływanie interfejsu API.  Kliknij przycisk **portalu dla deweloperów** w górnym menu z wystąpienia usługi Azure API Management **Przegląd** strony.

![Portal dla deweloperów][api-management-developer-portal-menu]

Kliknij przycisk **interfejsów API** w górnym menu i wybierz pozycję **interfejsu Echo API**.

![Interfejs Echo API][api-management-apis-echo-api]

> [!NOTE]
> Jeśli istnieje tylko jeden interfejs API skonfigurowany lub widoczny dla Twojego konta, kliknięcie opcji Interfejsy API powoduje przejście bezpośrednio do operacji dla tego interfejsu API.

Wybierz **GET Resource** operacji, kliknij przycisk **Otwórz konsolę**, a następnie wybierz pozycję **kod autoryzacji** z listy rozwijanej.

![Otwarta konsola][api-management-open-console]

Gdy **kod autoryzacji** jest zaznaczone, okno podręczne jest wyświetlany z formularzem logowania dostawcy uwierzytelniania OAuth 2.0. W tym przykładzie formularzem logowania jest zapewniana przez usługę Azure Active Directory.

> [!NOTE]
> Jeśli masz wyłączone wyskakujące okienka zostanie wyświetlony monit, aby je włączyć przez przeglądarkę. Po włączeniu ich wybierz **kod autoryzacji** ponownie i zostanie wyświetlony formularz logowania.

![Logowanie][api-management-oauth2-signin]

Po zalogowaniu, **nagłówki żądań** są wypełniane przy użyciu `Authorization : Bearer` nagłówek, który autoryzuje żądanie.

![Token nagłówka żądania][api-management-request-header-token]

W tym momencie możesz skonfigurować odpowiednie wartości dla pozostałych parametrów i prześlij żądanie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat korzystania z protokołu OAuth 2.0 i usługi API Management, zapoznaj się z poniższymi, wideo i towarzyszące [artykułu](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
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


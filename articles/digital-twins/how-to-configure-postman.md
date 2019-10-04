---
title: Jak skonfigurować usługę Poster dla usługi Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Jak skonfigurować usługę Poster dla usługi Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 14e6a52f86586eaae019d9658c2f813a15fc3474
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949204"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Jak skonfigurować usługę Poster dla usługi bliźniaczych reprezentacji Digital

W tym artykule opisano sposób konfigurowania klienta REST programu post na potrzeby współdziałania z interfejsami API zarządzania usługą Azure Digital bliźniaczych reprezentacji oraz ich testowania. W szczegółach opisano:

* Jak skonfigurować aplikację Azure Active Directory tak, aby korzystała z niejawnego przepływu uwierzytelniania OAuth 2,0.
* Jak używać klienta REST programu post do tworzenia żądań HTTP z tokenami do interfejsów API zarządzania.
* Jak za pomocą programu Ogłoś utworzyć wieloczęściowe żądania POST do interfejsów API zarządzania.

## <a name="postman-summary"></a>Podsumowanie ogłoszeń

Zacznij korzystać z usługi Azure Digital bliźniaczych reprezentacji, używając narzędzia klienta REST, takiego jak program do [publikowania](https://www.getpostman.com/) w celu przygotowania lokalnego środowiska testowania. Klient programu Poster pomaga szybko utworzyć złożone żądania HTTP. Pobierz wersję Desktop klienta programu Poster, przechodząc do [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Poster](https://www.getpostman.com/) to narzędzie do testowania REST, które lokalizuje kluczowe funkcje żądania HTTP w UŻYTECZNym interfejsie użytkownika opartym na komputerach stacjonarnych i wtyczkach.

Za pośrednictwem klienta programu Poster deweloperzy rozwiązań mogą określić rodzaj żądania HTTP (*post*, *Get*, *Update*, *patch*i *delete*), punkt końcowy interfejsu API do wywołania i użycie protokołu SSL. Program pocztowy obsługuje także Dodawanie nagłówków żądań HTTP, parametrów, formularzy i danych oraz treści.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurowanie Azure Active Directory do użycia niejawnego przepływu uwierzytelniania OAuth 2,0

Skonfiguruj aplikację Azure Active Directory tak, aby korzystała z niejawnego przepływu uwierzytelniania OAuth 2,0.

1. Otwórz okienko **uprawnień interfejsu API** dla rejestracji aplikacji. Wybierz przycisk **Dodaj uprawnienie** . W okienku **uprawnienia żądania interfejsu API** wybierz pozycję **interfejsy API Moja organizacja używa** karty, a następnie wyszukaj:
    
    1. `Azure Digital Twins`., Wybierz interfejs API **Digital bliźniaczych reprezentacji platformy Azure** .

        [![Search API lub Azure Digital bliźniaczych reprezentacji](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Możesz też wyszukać `Azure Smart Spaces Service`. Wybierz interfejs API **usługi Azure Smart Spaces** .

        [@no__t — interfejs API 1Search dla usługi Azure Smart Spaces](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Nazwa i identyfikator interfejsu API usługi Azure AD, który będzie wyświetlany, zależy od dzierżawcy:
    > * Test dzierżawy i konta klienta powinny wyszukiwać `Azure Digital Twins`.
    > * Inne konta Microsoft powinny szukać `Azure Smart Spaces Service`.

1. Wybrany interfejs API jest wyświetlany jako **usługa Azure Digital bliźniaczych reprezentacji** w tym samym okienku **uprawnień interfejsu API żądania** . Wybierz listę rozwijaną **Odczyt (1)** , a następnie wybierz pole wyboru **Odczyt. zapis** . Wybierz przycisk **Dodaj uprawnienia** .

    [uprawnienia interfejsu API @no__t 1Add](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. W zależności od ustawień organizacji może być konieczne wykonanie dodatkowych czynności w celu udzielenia administratorowi dostępu do tego interfejsu API. Aby uzyskać więcej informacji, skontaktuj się z administratorem. Po zatwierdzeniu dostępu administratora kolumna **wymagana zgoda administratora** w okienku **uprawnienia interfejsu API** będzie wyglądać podobnie do poniższego dla interfejsów API:

    [uprawnienia interfejsu API @no__t 1Add](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)


1. Wybierz pozycję **manifest** , aby otworzyć manifest aplikacji dla aplikacji. Ustaw wartość *oauth2AllowImplicitFlow* na `true`.

    [![Azure Active Directory niejawny przepływ](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Skonfiguruj **adres URL odpowiedzi** na `https://www.getpostman.com/oauth2/callback`.

    [adres URL odpowiedzi @no__t 1Azure Active Directory](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Skopiuj i Zachowaj **Identyfikator aplikacji** Azure Active Directory aplikacji. Jest on używany w kolejnych krokach.

   [@no__t — identyfikator aplikacji Active Directory 1Azure](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>Uzyskaj token OAuth 2,0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Skonfiguruj i skonfiguruj aplikację do ogłaszania w celu uzyskania tokenu Azure Active Directory. Następnie należy wykonać uwierzytelnione żądanie HTTP na platformie Azure Digital bliźniaczych reprezentacji przy użyciu uzyskanego tokenu:

1. Przejdź do [www.getpostman.com](https://www.getpostman.com/) , aby pobrać aplikację.
1. Sprawdź, czy **adres URL autoryzacji** jest poprawny. Powinien on mieć format:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nazwa  | Zamień na | Przykład |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Nazwa dzierżawy lub organizacji | `microsoft` |

1. Wybierz kartę **autoryzacja** , wybierz pozycję **OAuth 2,0**, a następnie wybierz pozycję **Pobierz nowy token dostępu**.

    | Pole  | Wartość |
    |---------|---------|
    | Typ udzielania | `Implicit` |
    | Adres URL wywołania zwrotnego | `https://www.getpostman.com/oauth2/callback` |
    | Adres URL uwierzytelniania | Użyj **adresu URL autoryzacji** z **kroku 2** |
    | Identyfikator klienta | Użyj **identyfikatora aplikacji** dla aplikacji Azure Active Directory, która została utworzona lub ponownie użyta w poprzedniej sekcji |
    | Zakres | Pozostaw puste |
    | Stan | Pozostaw puste |
    | Uwierzytelnianie klienta | `Send as Basic Auth header` |

1. Klient powinien teraz wyglądać następująco:

    [przykład klienta ![Postman](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Wybierz **token żądania**.

    >[!TIP]
    >Jeśli zostanie wyświetlony komunikat o błędzie "nie można ukończyć uwierzytelniania OAuth 2", spróbuj wykonać następujące czynności:
    > * Zamknij program Poster i otwórz go ponownie i ponów próbę.
  
1. Przewiń w dół i wybierz pozycję **Użyj tokenu**.

## <a name="make-a-multipart-post-request"></a>Utwórz wieloczęściowe żądanie POST

Po wykonaniu powyższych kroków Skonfiguruj program do ogłaszania w celu wypróbowania uwierzytelnionego żądania protokołu HTTP wieloczęściowego:

1. Na karcie **nagłówek** Dodaj **Typ zawartości** klucz nagłówka żądania HTTP z wartością `multipart/mixed`.

   [![Content typ wieloczęściowy/mieszany](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serializacja danych nietekstowych w plikach. Dane JSON zostałyby zapisane jako plik JSON.
1. Na karcie **treść** wybierz pozycję `form-data`. 
1. Dodaj każdy plik, przypisując nazwę **klucza** , wybierając `file`.
1. Następnie zaznacz każdy plik za pomocą przycisku **Wybierz plik** .

   [przykład klienta ![Postman](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Klient programu Poster nie wymaga, aby fragmenty wieloczęściowe miały ręcznie przypisane **elementy Content-Type** i **Content-Dyspozycja**.
   > * Nie trzeba określać tych nagłówków dla każdej części.
   > * Musisz wybrać `multipart/mixed` lub inną odpowiednią **zawartość** dla całego żądania.

1. Na koniec wybierz pozycję **Wyślij** , aby przesłać wieloczęściowe żądanie HTTP POST.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o interfejsach API usługi Digital bliźniaczych reprezentacji Management i sposobach ich użycia, przeczytaj artykuł [jak korzystać z interfejsów API zarządzania usługą Azure Digital bliźniaczych reprezentacji](how-to-navigate-apis.md).

- Użyj wieloczęściowych żądań, aby [dodać obiekty blob do jednostek usługi Azure Digital bliźniaczych reprezentacji](./how-to-add-blobs.md).

- Aby dowiedzieć się więcej o uwierzytelnianiu za pomocą interfejsów API zarządzania, przeczytaj artykuł [uwierzytelnianie za pomocą interfejsów API](./security-authenticating-apis.md).

---
title: Jak skonfigurować listonosza — Azure Digital Twins | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować i używać listonosza do testowania interfejsów API usługi Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297170"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Jak skonfigurować listonosz dla usług Azure Digital Twins

W tym artykule opisano sposób konfigurowania klienta USŁUGI POSTMAN REST do interakcji z interfejsami API zarządzania cyfrowymi obrazkami azure i testowania ich. W szczególności opisano w nim:

* Jak skonfigurować aplikację usługi Azure Active Directory do używania niejawnego przepływu dotacji OAuth 2.0.
* Jak używać klienta REST listonosza do wysyłania żądań HTTP z tokenami do interfejsów API zarządzania.
* Jak używać listonosza do wieloczęściowych żądań POST do interfejsów API zarządzania.

## <a name="postman-summary"></a>Podsumowanie listonosza

Rozpocznij pracę nad usługą Azure Digital Twins przy użyciu narzędzia klienta REST, takiego jak [Postman,](https://www.getpostman.com/) aby przygotować lokalne środowisko testowe. Klient poczty pomaga szybko tworzyć złożone żądania HTTP. Pobierz wersję pulpitu klienta Listonosza, przechodząc do [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) to narzędzie do testowania REST, które lokalizuje kluczowe funkcje żądania HTTP w użytecznym graficznym i opartym na wtyczce.

Za pośrednictwem klienta Postman deweloperzy rozwiązań mogą określić rodzaj żądania HTTP (*POST*, *GET*, *UPDATE*, *PATCH*i *DELETE*), punkt końcowy interfejsu API do wywołania i użycie protokołu TLS. Listonosz obsługuje również dodawanie nagłówków żądań HTTP, parametrów, danych formularza i obiektów.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurowanie usługi Azure Active Directory do używania niejawnego przepływu dotacji OAuth 2.0

1. Wykonaj kroki opisane w [przewodniku Szybki start,](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) aby utworzyć i skonfigurować aplikację usługi Azure Active Directory. Alternatywnie można ponownie użyć istniejącej rejestracji aplikacji.

    [![Konfigurowanie nowego identyfikatora URI przekierowania listonosza](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Teraz dodaj **identyfikator URI przekierowania** do `https://www.getpostman.com/oauth2/callback`pliku .

1. Zaznacz pole wyboru **Niejawne** > **tokeny programu Access,** aby zezwolić na użycie niejawnego przepływu dotacji OAuth 2.0. Wybierz **pozycję Konfiguruj**, a następnie **zapisz**.

1. Skopiuj **identyfikator klienta** aplikacji usługi Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Uzyskaj token OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Skonfiguruj i skonfiguruj listonosza w celu uzyskania tokenu usługi Azure Active Directory. Następnie skonsuj uwierzytelnione żądanie HTTP do usługi Azure Digital Twins przy użyciu nabytego tokenu:

1. Sprawdź, czy **adres URL autoryzacji** jest poprawny. Powinien on przyjąć format:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nazwa  | Zamień na | Przykład |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Nazwa dzierżawy lub organizacji. Użyj nazwy przyjaznej dla człowieka zamiast alfanumerycznego **identyfikatora dzierżawy** rejestracji aplikacji usługi Azure Active Directory. | `microsoft` |

1. Przejdź do [www.getpostman.com,](https://www.getpostman.com/) aby pobrać aplikację.

1. Chcemy złożyć wniosek GET. Wybierz kartę **Autoryzacja,** wybierz pozycję OAuth 2.0, a następnie wybierz pozycję **Pobierz nowy token dostępu**.

    | Pole  | Wartość |
    |---------|---------|
    | Typ udzielenia | `Implicit` |
    | Adres URL wywołania zwrotnego | `https://www.getpostman.com/oauth2/callback` |
    | Adres URL uwierzytelniania | Użyj **adresu URL autoryzacji** z **kroku 1** |
    | Identyfikator klienta | Użyj **identyfikatora aplikacji** dla aplikacji Usługi Azure Active Directory, która została utworzona lub ponownie użyta z poprzedniej sekcji |
    | Zakres | Pozostaw puste |
    | Stan | Pozostaw puste |
    | Uwierzytelnianie klienta | `Send as Basic Auth header` |

1. Klient powinien teraz pojawić się jako:

    [![Przykład tokenu klienta listonosza](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Wybierz pozycję **Request Token** (Zażądaj tokenu).
  
1. Przewiń w dół i wybierz pozycję **Użyj tokenu**.

## <a name="make-a-multipart-post-request"></a>Złożyć wieloczęściowe żądanie POST

Po wykonaniu poprzednich kroków skonfiguruj listonosza, aby zrobił uwierzytelnione wieloczęściowe żądanie POST HTTP:

1. Na karcie **Nagłówki** dodaj klucz nagłówka żądania HTTP `multipart/mixed`Typ **zawartości** z wartością .

   [![Określanie typu zawartości wieloczęściowej/mieszanej](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serializuj dane nietekstowe w plikach. Dane JSON zostaną zapisane jako plik JSON.
1. W **Body** obszarze karty `form-data`Ciało wybierz pozycję . 
1. Dodaj każdy plik, przypisując nazwę `File` **klucza,** wybierając opcję .
1. Następnie zaznacz każdy plik za pomocą przycisku **Wybierz plik.**

   [![Przykład treści formularza klienta listonosza](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Klient listonosz nie wymaga, aby fragmenty wieloczęściowe miały ręcznie przypisane **typ zawartości** lub **dyspozycję zawartości**.
   > * Nie trzeba określać tych nagłówków dla każdej części.
   > * Należy wybrać `multipart/mixed` lub inny odpowiedni **typ zawartości** dla całego żądania.

1. Nareszcie wybierz **pozycję Wyślij,** aby przesłać wieloczęściowe żądanie HTTP POST. Kod stanu `200` lub `201` wskazuje pomyślne żądanie. Odpowiedni komunikat odpowiedzi pojawi się w interfejsie klienta.

1. Sprawdź poprawność danych żądania HTTP POST, wywołując punkt końcowy interfejsu API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o interfejsach API zarządzania cyfrowymi bliźniakami i jak z nich korzystać, przeczytaj [artykuł Jak korzystać z interfejsów API zarządzania usługą Azure Digital Twins](how-to-navigate-apis.md).

- Żądania wieloczęściowe [umożliwia dodawanie obiektów blob do encji usługi Azure Digital Twins.](./how-to-add-blobs.md)

- Aby dowiedzieć się więcej o uwierzytelnieniu za pomocą interfejsów API zarządzania, przeczytaj artykuł [Uwierzytelnianie przy użyciu interfejsów API](./security-authenticating-apis.md).

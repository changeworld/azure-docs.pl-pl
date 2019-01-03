---
title: Konfigurowanie narzędzia Postman bliźniaki cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie narzędzia Postman bliźniaki cyfrowych platformy Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: adgera
ms.openlocfilehash: 92ff8cb732c7c10c525d8a8ec76180cb435bd466
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975019"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Konfigurowanie narzędzia Postman bliźniaki cyfrowych platformy Azure

W tym artykule opisano sposób konfigurowania aplikacji usługi Azure Active Directory (Azure AD) przy użyciu usługi flow przyznawanie niejawne protokołu OAuth 2.0. Następnie omówiono sposób konfigurowania klienta REST narzędzia Postman, aby wysyłać żądania HTTP żadnego wpływu na token do interfejsów API zarządzania.

## <a name="postman-summary"></a>Podsumowanie narzędzia postman

Rozpocznij pracę w reprezentacji urządzeń cyfrowych platformy Azure przy użyciu narzędzia klienta REST takiego jak [Postman](https://www.getpostman.com/) przygotowanie lokalnego środowiska testowego. Klient narzędzia Postman ułatwia szybkie tworzenie złożonych żądań HTTP. Pobierz klasycznej wersji klienta programu Postman, przechodząc do [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) REST testuje narzędzia, która lokalizuje kluczowych funkcjach żądania HTTP do przydatne pulpitu i na podstawie wtyczki graficznego interfejsu użytkownika. Za pomocą klienta programu Postman deweloperzy rozwiązań można określić rodzaj żądania HTTP (POST, GET, UPDATE, PATCH i DELETE), punkt końcowy interfejsu API do wywołania i korzystania z protokołu SSL. Postman obsługuje również dodawanie nagłówków żądań HTTP, parametry, dane formularza i treści.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurowanie usługi Azure Active Directory, aby korzystać z tego przepływu przyznawanie niejawne protokołu OAuth 2.0

Konfigurowanie aplikacji usługi Azure AD, aby korzystać z tego przepływu przyznawanie niejawne protokołu OAuth 2.0.

1. Postępuj zgodnie z instrukcjami w [ten przewodnik Szybki Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) do tworzenia aplikacji usługi Azure AD typu natywnego. Lub możesz ponownie użyć istniejącej rejestracji aplikacji natywnej.

1. W obszarze **wymagane uprawnienia**, wybierz opcję **Dodaj** i wprowadź **Twins cyfrowego Azure** w obszarze **dostępu Dodaj interfejs API**. Jeśli wyszukiwanie nie znajdzie interfejsu API, zamiast tego wyszukaj frazę **Azure Smart Spaces**. Następnie wybierz **Udziel uprawnień > delegowane uprawnienia** i **gotowe**.

    ![Rejestracje aplikacji w usłudze Azure AD, Dodaj interfejs api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Kliknij przycisk **manifestu** otworzyć manifest aplikacji dla aplikacji. Ustaw *oauth2AllowImplicitFlow* do `true`.

      ![Usługa Azure AD niejawny przepływ][1]

1. Konfigurowanie **adres URL odpowiedzi** do [ `https://www.getpostman.com/oauth2/callback` ](https://www.getpostman.com/oauth2/callback).

      ![Adres URL odpowiedzi usługi Azure AD][2]

1. Skopiuj i Zachowaj **identyfikator aplikacji** aplikacji usługi Azure AD. Jest on używany poniżej.

## <a name="configure-the-postman-client"></a>Konfigurowanie klienta narzędzia Postman

Następnie instalowanie i Konfigurowanie narzędzia Postman w celu uzyskania tokenu usługi Azure AD. Później dokonać uwierzytelnionego żądania HTTP przy użyciu tokenu uzyskano reprezentacji urządzeń cyfrowych platformy Azure:

1. Przejdź do [www.getpostman.com]([https://www.getpostman.com/) pobrać aplikację.
1. Upewnij się, że Twoje **adres URL autoryzacji** jest poprawna. Powinno zająć format:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name (Nazwa)  | Zamień na | Przykład |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Nazwa dzierżawy lub organizacji | `microsoft` |

1. Wybierz **autoryzacji** zaznacz **OAuth 2.0**, a następnie wybierz pozycję **uzyskać nowy Token dostępu**.

    | Pole  | Wartość |
    |---------|---------|
    | Typ udzielania | `Implicit` |
    | Adres URL wywołania zwrotnego | `https://www.getpostman.com/oauth2/callback` |
    | Adres URL uwierzytelniania | Użyj **adres URL autoryzacji** z kroku 2 powyżej |
    | Identyfikator klienta | Użyj **identyfikator aplikacji** dla aplikacji usługi Azure AD, który został utworzony lub zmodyfikowany w poprzedniej sekcji |
    | Zakres | Pozostaw puste |
    | Stan | Pozostaw puste |
    | Uwierzytelnianie klienta | `Send as Basic Auth header` |

1. Klient powinien teraz wyglądać podobnie jak:

   ![Przykład klienta narzędzia postman][3]

1. Wybierz **wysłania żądania tokenu**.

    >[!NOTE]
    >Jeśli zostanie wyświetlony komunikat o błędzie "Nie można ukończyć OAuth 2", spróbuj wykonać następujące czynności:
    > * Narzędzia Postman, zamknij i otwórz go ponownie i spróbuj ponownie.
  
1. Przewiń w dół i wybierz **użycia tokenu**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o uwierzytelnianiu przy użyciu interfejsów API Management, przeczytaj [Uwierzytelnij za pomocą interfejsów API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png

---
title: Warunków użytkowania usługi i zasady zachowania poufności dla aplikacji | Azure
description: Dowiedz się, jak można skonfigurować warunki usługi i zasady zachowania poufności dla aplikacji zarejestrowana do korzystania z usługi Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2242c6fc46f5556de0b0dd63659670e9c3f998d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540187"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Instrukcje: Konfigurowanie warunków użytkowania usługi i zasady zachowania poufności dla aplikacji

Deweloperzy, którzy tworzenie i zarządzanie aplikacjami, które integrują się z kontami Microsoft i usługi Azure Active Directory (Azure AD) powinien zawierać linki do warunków aplikacji usługi i zasady zachowania poufności. Warunki usługi i zasady zachowania poufności są udostępniane użytkownikom za pośrednictwem środowiska zgody użytkownika. One pomóc użytkownikom dowiedzieć się, że ich zaufania aplikacji. Warunki usługi i zasady zachowania poufności są szczególnie istotne w przypadku aplikacji wielodostępnych widocznych dla użytkownika — aplikacje, które są używane przez wiele katalogów, lub są dostępne dla dowolnego konta Microsoft.

Odpowiedzialność za tworzenie warunków użytkowania usługi i zasad zachowania poufności dokumentów dla twojej aplikacji i zapewniające adresy URL do tych dokumentów. Dla aplikacji wielodostępnych, które nie zapewniają te linki środowisko zgody użytkownika dla aplikacji wyświetli alert, który może zniechęcić użytkowników do wyrażanie zgody na aplikację.

> [!NOTE]
> * Aplikacje z jedną dzierżawą nie wyświetli alert.
> * Jeśli brakuje co najmniej jeden z dwóch łączy, aplikacja wyświetli alert.

## <a name="user-consent-experience"></a>Środowisko zgody użytkownika

W poniższych przykładach pokazano użytkownika zgody środowiska, gdy skonfigurowano warunki usługi i zasady zachowania poufności i kiedy te łącza nie są skonfigurowane.

![Zrzuty ekranu z lub bez zasad zachowania poufności informacji i warunki usługi](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatowanie linki do warunków usługi i ochrony prywatności dokumentów — instrukcja

Przed dodaniem linki do warunków Twojej aplikacji, usług i ochrony prywatności dokumentów instrukcji, upewnij się, że adresy URL przestrzegać następujących wytycznych.

| Wytyczna     | Opis                           |
|---------------|---------------------------------------|
| Format        | Nieprawidłowy adres URL                             |
| Prawidłowe schematy | HTTP i HTTPS<br/>Firma Microsoft zaleca protokołu HTTPS |
| Maksymalna długość    | 2048 znaków                       |

Przykłady: `https://myapp.com/terms-of-service` i `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Dodawanie linków do warunków usługi i zasady zachowania poufności

Jeśli warunki usługi i zasady zachowania poufności są gotowe, można dodać łącza do tych dokumentów, w swojej aplikacji za pomocą jednej z następujących metod:

* [W witrynie Azure portal](#registered-in-azure-portal)
* [W portalu rejestracji aplikacji lub Centrum deweloperów](#registered-in-app-reg-portal)
* [Za pomocą aplikacji obiektu JSON](#app-object-json)
* [Za pomocą programu MSGraph beta interfejsu API REST](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Jeśli aplikacja jest zarejestrowana w witrynie Azure portal

Jeśli aplikacja jest zarejestrowana w witrynie Azure portal, wykonaj następujące kroki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **rejestracje aplikacji** sekcji i wybierz swoją aplikację.
3. Otwórz **właściwości** części aplikacji.
4. Wypełnij **URL warunki usługi** i **adres URL zasad zachowania poufności informacji** pola.
5. Zapisz zmiany.

    ![Sekcja właściwości aplikacji z warunkami adresy URL poufności informacji usług i ochrony prywatności](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Jeśli aplikacja jest zarejestrowana w portalu rejestracji aplikacji

Wykonaj następujące kroki, jeśli aplikacja jest zarejestrowana w portalu rejestracji aplikacji lub Centrum deweloperów.

1. Zaloguj się do [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/).
2. Wybierz aplikację, a następnie przewiń listę do **profilu** sekcji.
3. Wypełnij **URL warunki usługi** i **adres URL zasad zachowania poufności informacji** pola.
4. Zapisz zmiany.

    ![Sekcji profilu aplikacji z warunkami usługi i ochrona prywatności adresów URL, instrukcja](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Za pomocą aplikacji obiektu JSON

Jeśli wolisz bezpośrednio zmodyfikować obiekt aplikacji JSON umożliwia Edytor manifestu w witrynie Azure portal lub portalu rejestracji aplikacji zawierają łącza do aplikacji warunków usługi i zasady zachowania poufności.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Za pomocą programu MSGraph beta interfejsu API REST

Programowe aktualizację wszystkich aplikacji, można użyć MSGraph wersji beta interfejsu API REST można zaktualizować wszystkie Twoje aplikacje zawierają linki do warunków usługi i ochrony prywatności dokumentów instrukcji.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Uważaj, aby nie zastąpić wszelkie istniejące wartości, które zostały przypisane do żadnej z tych pól: `supportUrl`, `marketingUrl`, i `logoUrl`
> * Program MSGraph wersji beta interfejsu API REST działa tylko po zalogowaniu się przy użyciu konta usługi Azure AD. Osobistych kont Microsoft nie są obsługiwane.

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
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a01b50573405964b09339d03e84c62dbdd8582
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482863"
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

* [W witrynie Azure portal](#azure-portal)
* [Za pomocą aplikacji obiektu JSON](#app-object-json)
* [Za pomocą programu MSGraph beta interfejsu API REST](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Za pomocą witryny Azure portal
Wykonaj następujące kroki w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **rejestracje aplikacji** sekcji i wybierz swoją aplikację.
3. Otwórz **znakowania** okienka.
4. Wypełnij **URL warunki usługi** i **adres URL zasad zachowania poufności informacji** pola.
5. Zapisz zmiany.

    ![Właściwości aplikacji zawiera warunki usług i ochrony prywatności adresów URL, instrukcja](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

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

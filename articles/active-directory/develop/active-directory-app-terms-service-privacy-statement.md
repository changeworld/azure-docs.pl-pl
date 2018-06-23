---
title: Warunki usługi i ochrona prywatności dla aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można skonfigurować warunki instrukcji usługi i ochrona prywatności dla aplikacji w zarejestrowany do korzystania z usługi Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 2dff8c25ae0704a15cf02eec7a380b6d894b9ce4
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316454"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>Warunki usługi i ochrona prywatności dla zarejestrowanych aplikacji usługi Azure Active Directory

Deweloperów, którzy tworzenie i zarządzanie aplikacjami, które integrują się z kontami Microsoft i Azure Active Directory (Azure AD) powinna zawierać łącza do warunki aplikacji usługi i ochrony prywatności. Warunki usługi i ochrona prywatności instrukcji są udostępniane użytkownikom za pośrednictwem środowiska zgody użytkownika. Pomaga on użytkowników, że ich zaufania aplikacji. Warunki usługi i ochrona prywatności instrukcji są krytyczne znaczenie dla użytkownika wielodostępnych aplikacji — aplikacje, które są używane przez wiele katalogów lub są dostępne z żadnym kontem Microsoft.

Jest odpowiedzialny za tworzenie warunków usługi i ochrony prywatności dokumentów instrukcji dla aplikacji, a także udostępnia adresy URL do tych dokumentów. Dla wielodostępnych aplikacji, które nie udostępniają te linki środowisko zgody użytkownika dla aplikacji wyświetli alert, który może zniechęcić użytkowników zgodę aplikacji.

> [!NOTE]
> * Aplikacje pojedynczej dzierżawy nie wyświetli alert.
> * Jeśli brakuje co najmniej jeden z dwóch łączy, aplikacja wyświetli alert.

## <a name="user-consent-experience"></a>Środowisko zgody użytkownika

W poniższych przykładach pokazano użytkownika zgody środowisko po skonfigurowaniu postanowienia instrukcji usługi i ochrona prywatności i po tych linków nie są skonfigurowane.

![Zrzuty ekranu z włączonymi i wyłączonymi poufności informacji oraz postanowienia usługi](./media/active-directory-integrating-applications/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatowanie łącza na warunki usługi i ochrony prywatności dokumentów — instrukcja

Przed dodaniem łącza do warunków aplikacji usługi i ochrony prywatności dokumentów instrukcji upewnij się, że adresy URL przestrzegać następujących wytycznych.

| Wytyczna     | Opis                           |
|---------------|---------------------------------------|
| Format        | Prawidłowy adres URL                             |
| Nieprawidłowa schematów | HTTP i HTTPS</br>Firma Microsoft zaleca HTTPS |
| Maksymalna długość    | 2048 znaków                       |

Przykłady: `https://myapp.com/terms-of-service` i `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Dodawanie łączy na warunki usługi i ochrona prywatności — instrukcja

Po zakończeniu warunki usługi i ochrona prywatności instrukcji można dodać łącza do tych dokumentów w aplikacji przy użyciu jednej z następujących metod:
* [Za pomocą portalu Azure](#registered-in-azure-portal)
* [W portalu rejestracji aplikacji lub Centrum deweloperów](#registered-in-app-reg-portal)
* [Przy użyciu obiektu JSON aplikacji](#app-object-json)
* [Przy użyciu wersji beta MSGraph interfejsu API REST](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Jeśli aplikacja jest zarejestrowany w portalu Azure
Jeśli aplikacja jest zarejestrowany w portalu Azure, wykonaj następujące kroki.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Przejdź do **rejestracji aplikacji** sekcji, a następnie wybierz aplikację.
3. Otwórz **właściwości** części aplikacji.
4. Wypełnianie **URL warunki usługi** i **adres URL prywatności** pól.
5. Zapisz zmiany.

![Sekcja właściwości aplikacji z warunkami instrukcji adresów URL usługi i ochrony prywatności](./media/active-directory-integrating-applications/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Jeśli aplikacja jest zarejestrowany w portalu rejestracji aplikacji
Jeśli aplikacja jest zarejestrowany w portalu rejestracji aplikacji lub Centrum deweloperów, wykonaj następujące kroki.

1. Zaloguj się do [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/).
2. Wybierz aplikację, a następnie przewiń do **profilu** sekcji.
3. Wypełnianie **URL warunki usługi** i **adres URL prywatności** pól.
4. Zapisz zmiany.

![Profil aplikacji sekcji z warunkami instrukcji adresów URL usługi i ochrony prywatności](./media/active-directory-integrating-applications/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Przy użyciu obiektu JSON aplikacji
Jeśli wolisz bezpośrednio zmodyfikować obiekt aplikacji JSON można manifestu edytora w portalu Azure lub portalu rejestracji aplikacji zawierają łącza do warunki aplikacji usługi i ochrony prywatności.

```json
    "informationalUrls": { 
        "termsOfService": “<your_terms_of_service_url>”, 
        "privacy": “<your_privacy_statement_url>” 
    }
```

### <a name="msgraph-beta-rest-api"></a>Przy użyciu wersji beta MSGraph interfejsu API REST
Programowo aktualizacji wszystkich aplikacji, umożliwia beta MSGraph interfejsu API REST zaktualizować wszystkie Twoje aplikacje zawierają łącza do warunków usługi i ochrony prywatności dokumentów instrukcji.

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
> * Nie można więc zastąpić wszystkie istniejące wartości zostały przypisane do żadnej z tych pól: `supportUrl`, `marketingUrl`, i `logoUrl`
> * Beta MSGraph interfejsu API REST działa tylko, gdy zalogujesz się przy użyciu konta usługi Azure AD. Osobistego konta Microsoft nie są obsługiwane.
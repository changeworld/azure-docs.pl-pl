---
title: Warunki korzystania z usługi i zasady zachowania poufności informacji dotyczące aplikacji | Azure
description: Dowiedz się, jak skonfigurować warunki korzystania z usługi i zasady zachowania poufności informacji dla aplikacji zarejestrowanych do korzystania z usługi Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 40e7a05505bc501c1c622e627a6d97cc57db1cfa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884243"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Jak: Konfigurowanie warunków korzystania z usługi i zasad zachowania poufności informacji dla aplikacji

Deweloperzy, którzy twórz aplikacje integrują się z usługą Azure Active Directory (Azure AD) i kontami Microsoft oraz zarządzają nimi, powinni zawierać łącza do warunków korzystania z usługi i zasad zachowania poufności informacji aplikacji. Warunki korzystania z usługi i zasady zachowania poufności informacji są ujmowania użytkowników za pośrednictwem środowiska zgody użytkownika. Pomagają one użytkownikom wiedzieć, że mogą ufać Twojej aplikacji. Warunki korzystania z usługi i zasady zachowania poufności informacji są szczególnie ważne dla aplikacji z wieloma dzierżawcami przeznaczonych dla użytkowników — aplikacji, które są używane przez wiele katalogów lub są dostępne dla dowolnego konta Microsoft.

Użytkownik jest odpowiedzialny za tworzenie warunków korzystania z usługi i dokumentów oświadczenia o ochronie prywatności dla aplikacji oraz za dostarczanie adresów URL do tych dokumentów. W przypadku aplikacji z wieloma dzierżawami, które nie udostępnią tych łączy, środowisko zgody użytkownika dla aplikacji wyświetli alert, który może zniechęcić użytkowników do wyrażenia zgody na aplikację.

> [!NOTE]
> * Aplikacje z jedną dzierżawą nie będą wyświetlać alertu.
> * Jeśli brakuje jednego lub obu z tych dwóch łączy, aplikacja wyświetli alert.

## <a name="user-consent-experience"></a>Zgoda użytkownika

Poniższe przykłady pokazują środowisko zgody użytkownika, gdy warunki świadczenia usług i zasady zachowania poufności informacji są skonfigurowane i gdy te łącza nie są skonfigurowane.

![Zrzuty ekranu z oświadczeniem o ochronie prywatności i bez nich](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatowanie linków do warunków korzystania z usługi i dokumentów oświadczenia o ochronie prywatności

Zanim dodasz linki do warunków korzystania z usługi i dokumentów dotyczących zachowania poufności informacji w aplikacji, upewnij się, że adresy URL są zgodne z tymi wskazówkami.

| Wytyczna     | Opis                           |
|---------------|---------------------------------------|
| Format        | Prawidłowy adres URL                             |
| Prawidłowe schematy | HTTP i HTTPS<br/>Zalecamy HTTPS |
| Długość maksymalna    | 2048 znaków                       |

Przykłady: `https://myapp.com/terms-of-service` i`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Dodawanie linków do warunków korzystania z usługi i oświadczenia o ochronie prywatności

Gdy warunki świadczenia usług i zasady zachowania poufności informacji są gotowe, można dodać łącza do tych dokumentów w aplikacji przy użyciu jednej z następujących metod:

* [Za pośrednictwem portalu Azure](#azure-portal)
* [Korzystanie z obiektu aplikacji JSON](#app-object-json)
* [Korzystanie z interfejsu API programu Microsoft Graph](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Korzystanie z witryny Azure Portal
Wykonaj następujące kroki w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do sekcji **Rejestracje aplikacji** i wybierz aplikację.
3. Otwórz **okienko Znakowanie.**
4. Wypełnij pola **Adresu URL warunków korzystania z usługi** i zasady zachowania poufności **informacji.**
5. Zapisz zmiany.

    ![Właściwości aplikacji zawierają warunki korzystania z usługi i adresy URL zasad zachowania poufności informacji](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Korzystanie z obiektu aplikacji JSON

Jeśli wolisz zmodyfikować obiekt aplikacji JSON bezpośrednio, można użyć edytora manifestów w witrynie Azure portal lub portal rejestracji aplikacji, aby dołączyć łącza do warunków korzystania z usługi aplikacji i zasad zachowania poufności informacji.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Korzystanie z interfejsu API programu Microsoft Graph

Aby programowo zaktualizować wszystkie aplikacje, można użyć interfejsu API programu Microsoft Graph, aby zaktualizować wszystkie aplikacje w celu uwzględnienia łączy do warunków świadczenia usług i dokumentów zasad zachowania poufności informacji.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
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
> * Należy uważać, aby nie zastąpić żadnych wcześniej istniejących wartości przypisanych `supportUrl` `marketingUrl`do któregokolwiek z tych pól: , , i`logoUrl`
> * Interfejs API programu Microsoft Graph działa tylko po zalogowaniu się za pomocą konta usługi Azure AD. Osobiste konta Microsoft nie są obsługiwane.

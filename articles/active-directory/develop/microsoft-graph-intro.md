---
title: Interfejs API programu Microsoft Graph | Dokumenty firmy Microsoft
description: Interfejs API programu Microsoft Graph to internetowy interfejs API restful, który umożliwia dostęp do zasobów usługi Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136504"
---
# <a name="microsoft-graph-api"></a>Interfejs API programu Microsoft Graph

Interfejs API programu Microsoft Graph to internetowy interfejs API restful, który umożliwia dostęp do zasobów usługi Microsoft Cloud. Po zarejestrowaniu aplikacji i pobierz tokeny uwierzytelniania dla użytkownika lub usługi, można składać żądania do interfejsu API programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [Omówienie programu Microsoft Graph](https://docs.microsoft.com/graph/overview).

Program Microsoft Graph udostępnia interfejsy API rest i biblioteki klienckie w celu uzyskania dostępu do danych w następujących usługach Microsoft 365:
- Usługi Office 365: Aplikacja Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner i SharePoint
- Usługi mobilności i zabezpieczeń w przedsiębiorstwie: zaawansowana analiza zagrożeń, zaawansowana ochrona przed zagrożeniami, usługa Azure Active Directory, Menedżer tożsamości i usługa Intune
- Usługi systemu Windows 10: działania, urządzenia, powiadomienia
- Dynamics 365 Business Central

## <a name="versions"></a>Wersje

Program Microsoft Graph obsługuje obecnie dwie wersje: v1.0 i beta. Wersja w wersji 1.0 zawiera ogólnie dostępne interfejsy API. Użyj wersji 1.0 dla wszystkich aplikacji produkcyjnych. Wersja beta zawiera interfejsy API, które są obecnie w wersji zapoznawczej. Ponieważ możemy wprowadzić przełomowe zmiany do naszych interfejsów API w wersji beta, zalecamy używanie wersji beta tylko do testowania aplikacji, które są w fazie rozwoju; nie używaj interfejsów API w aplikacjach produkcyjnych. Aby uzyskać więcej informacji, zobacz [Przechowywanie wersji, obsługa techniczna i łamanie zasad zmian dla programu Microsoft Graph](https://docs.microsoft.com/graph/versioning-and-support).

Aby rozpocząć korzystanie z interfejsów API w wersji beta, zobacz [odwołanie do punktu końcowego wersji beta programu Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

Aby rozpocząć korzystanie z interfejsów API w wersji 1.0, zobacz [Odwołanie interfejsu API REST systemu Microsoft Graph w wersji 1.0](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Wprowadzenie

Aby odczytać lub zapisać do zasobu, takiego jak użytkownik lub wiadomość e-mail, należy utworzyć żądanie, które wygląda następująco:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Aby uzyskać więcej informacji na temat elementów skonstruowanego żądania, zobacz [Korzystanie z interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)

Dostępne są przykłady przewodnika Szybki start, aby pokazać, jak uzyskać dostęp do mocy interfejsu API programu Microsoft Graph. Przykłady, które są dostępne dostęp do dwóch usług z jednym uwierzytelnianiem: konto Microsoft i Outlook. Każdy szybki start uzyskuje dostęp do informacji z profili użytkowników konta Microsoft i wyświetla zdarzenia z ich kalendarza.
Przewodniki Szybki start obejmują cztery kroki:
- Wybierz swoją platformę
- Pobierz identyfikator aplikacji (identyfikator klienta)
- Tworzenie próbki
- Zaloguj się i wyświetl wydarzenia w kalendarzu

Po zakończeniu szybkiego startu masz aplikację, która jest gotowa do uruchomienia. Aby uzyskać więcej informacji, zobacz często [zadawane pytania dotyczące szybkiego startu programu Microsoft Graph](https://docs.microsoft.com/graph/quick-start-faq). Aby rozpocząć korzystanie z przykładów, zobacz [Szybki start programu Microsoft Graph](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Narzędzia

Microsoft Graph Explorer to narzędzie internetowe, którego można używać do tworzenia i testowania żądań przy użyciu interfejsów API programu Microsoft Graph. Dostęp do Eksploratora `https://developer.microsoft.com/graph/graph-explorer`grafów firmy Microsoft można uzyskać pod adresem: .

Postman to narzędzie, którego można również używać do tworzenia i testowania żądań przy użyciu interfejsów API programu Microsoft Graph. Listonosz można pobrać `https://www.getpostman.com/`pod adresem: . Aby wchodzić w interakcje z programem Microsoft Graph w programie Postman, należy użyć kolekcji Microsoft Graph w programie Postman. Aby uzyskać więcej informacji, zobacz [Używanie listonosza z interfejsem API programu Microsoft Graph](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).

---
title: Zarządzanie danymi użytkownika za pomocą uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak zarządzać danych użytkownika za pomocą uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d42f903559a1e07b42ded33972be4b552f21b5e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348601"
---
# <a name="managing-user-data"></a>Zarządzanie danymi użytkownika

Na tej stronie opisano, co usługa w chmurze uczeń konwersacji rejestruje podczas przeprowadzania okien dialogowych z użytkownikami końcowymi.  Zawiera również opis sposobu kojarzenia dzienniki uczeń konwersacji z identyfikatory użytkowników, dzięki czemu można pobrać lub usunąć wszystkie dzienniki skojarzone z określonym użytkownikiem.

## <a name="overview-of-end-user-data-logging"></a>Omówienie rejestrowania danych przez użytkownika końcowego

Domyślnie usługa w chmurze uczeń konwersacji rejestruje interakcje między użytkowników końcowych i z bot.  Te dzienniki są ważne dla poprawy bot sieci, co umożliwia zidentyfikowanie przypadków, w której wyjęto niepoprawne jednostki lub wybrane błędną akcję w Twojej bot.  Następnie można usunąć te błędy, przechodząc do strony "Dziennika okna" interfejsu użytkownika, korekt i przechowywanie poprawiony okna dialogowego jako nowe okno dialogowe pociągu. Aby uzyskać więcej informacji zobacz samouczek dotyczący "Dziennika okien dialogowych."

## <a name="how-to-disable-logging"></a>Jak wyłączyć rejestrowanie

Można kontrolować, czy konwersacje użytkownikom końcowym, znajdują się na stronie "Ustawienia" aplikacji uczeń konwersacji.  Brak wyboru "Dziennika konwersacji."  Przez usunięcie zaznaczenia tego pola, konwersacji z użytkownicy końcowi nie będą rejestrowane.

## <a name="what-is-logged"></a>Co to jest zalogowany 

W oknach dialogowych dziennika uczeń konwersacji przechowuje dane wejściowe użytkownika, wartości jednostki, wybrane akcje i sygnatury czasowe każda Włączanie.  Te dzienniki są przechowywane w danym okresie czasu, a następnie zostaje odrzucone (patrz strona pomocy na "wartości domyślnej i granice" Aby uzyskać szczegółowe informacje).  

Uczeń konwersacji tworzy unikatowy identyfikator każdego zalogowanego oknie dialogowym.  Uczeń konwersacji jest *nie* przechowywania identyfikatora użytkownika z zarejestrowanych okien dialogowych.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Kojarzenie rejestrowane okien dialogowych z Identyfikatorem użytkownika

Często jest to ważne można było, aby skojarzyć zarejestrowane okien dialogowych z Identyfikatorem użytkownika — na przykład, aby można było pobrać lub usunąć zalogowanego okien dialogowych konkretnego użytkownika.  Ponieważ uczeń konwersacji nie przechowuje identyfikator użytkownika, to skojarzenie musi być obsługiwany przez dewelopera kodu.  

Aby utworzyć to mapowanie, należy uzyskać identyfikator zalogowanego oknie dialogowym w `EntityDetectionCallback`; następnie w magazynie użytkownika bot przechowywania skojarzenie Identyfikatora użytkownika w tym zalogowanego oknie dialogowym.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific datastore, store an association
    // bewteen your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Nagłówki dla połączeń HTTP

W każdym poniżej połączeń HTTP Dodaj następujący nagłówek:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

gdzie `<LUIS_AUTHORING_KEY>` jest LUIS tworzenia klucz używany do uzyskania dostępu do Twojej aplikacji uczeń konwersacji.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Jak uzyskać danych pierwotnych dla zalogowanego oknie dialogowym

Aby uzyskania danych pierwotnych dla okna dialogowego dziennika, można użyć tego wywołania HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Gdzie `<appId>` jest identyfikator GUID dla tej aplikacji uczeń konwersacji i `<logDialgoId>` to identyfikator okna dialogowego dziennika do pobrania.  

Należy pamiętać, że okna dialogowe dziennika może można edytowane przez dewelopera, a następnie zapisywana jako uczenia okien dialogowych.  Po zakończeniu uczeń konwersacji przechowuje identyfikator okna dialogowego dziennika "source" w oknie dialogowym pociągu.  Dalsze okno dialogowe pociągu może być "zgodnie" w Interfejsie użytkownika; Jeśli okno dialogowe train ma identyfikator okna dialogowego skojarzonego źródła dziennika, następnie zostaną oznaczone oddziałów z tego okna dialogowego pociągu z tego samego identyfikatora dziennika okna dialogowego.

Aby uzyskać wszystkie okna dialogowe train utworzone na podstawie okna dialogowego dziennika, wykonaj następujące kroki.

Najpierw należy pobrać wszystkie okna dialogowe pociągu:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Gdzie `<appId>` to identyfikator GUID dla tej aplikacji uczeń konwersacji.  

To polecenie zwróci wszystkie okna dialogowe pociągu.  Wyszukaj na tej liście skojarzonych z nim `sourceLogDialogId`i zanotuj skojarzony `trainDialogId`. 

Do pojedynczego uczenia okna dialogowego za pomocą Identyfikatora:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Gdzie `<appId>` jest identyfikator GUID dla tej aplikacji uczeń konwersacji i `<trainDialogId>` to identyfikator okna dialogowego pociągu do pobrania.  

## <a name="how-to-delete-a-logged-dialog"></a>Jak usunąć zalogowanego oknie dialogowym

Jeśli chcesz usunąć okna dialogowego dziennika podany jego identyfikator, można użyć tego wywołania HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Gdzie `<appId>` jest identyfikator GUID dla tej aplikacji uczeń konwersacji i `<logDialogId>` to identyfikator okna dialogowego dziennika do usunięcia. 

Jeśli chcesz usunąć okna dialogowego train podany jego identyfikator, można użyć tego wywołania HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Gdzie `<appId>` jest identyfikator GUID dla tej aplikacji uczeń konwersacji i `<trainDialogId>` to identyfikator okna dialogowego pociągu do usunięcia. 

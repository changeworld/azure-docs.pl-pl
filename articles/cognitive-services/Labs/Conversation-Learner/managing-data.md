---
title: Zarządzanie danymi użytkowników za pomocą uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak zarządzać danymi użytkownika przy użyciu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83c7e808e48733487e84d668236cdb327c21c44c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688542"
---
# <a name="managing-user-data"></a>Zarządzanie danymi użytkowników

Na tej stronie opisano, co usługa w chmurze uczeń konwersacji dzienniki podczas przeprowadzania okien dialogowych użytkownikom końcowym.  Zawiera również opis sposobu kojarzenia dzienniki uczeń konwersacji z identyfikatorów użytkowników, tak że można pobrać lub usunąć wszystkie dzienniki skojarzony z określonym użytkownikiem.

## <a name="overview-of-end-user-data-logging"></a>Omówienie rejestrowania danych przez użytkownika końcowego

Domyślnie usługa w chmurze uczeń konwersacji rejestruje interakcje między użytkownikami końcowymi a bota.  Te dzienniki są ważne dla poprawy bota, dzięki któremu można zidentyfikować przypadki, gdzie Twój bot wyodrębnione niepoprawne jednostki lub wybrano nieprawidłowe działanie.  Należy poprawić te błędy, przechodząc do strony "Okien dialogowych dziennika" interfejsu użytkownika, wprowadzanie poprawek i przechowywania to poprawione okno dialogowe jako nowe okno dialogowe szkolenie. Aby uzyskać więcej informacji zobacz samouczek dotyczący "Log wyświetlanymi w oknach dialogowych."

## <a name="how-to-disable-logging"></a>Jak wyłączyć rejestrowanie

Można kontrolować, czy rozmów z użytkownikami końcowymi znajdują się na stronie "Ustawienia" dla modelu uczeń konwersacji.  Brak pola wyboru dla "Log konwersacji."  Przez usunięcie zaznaczenia tego pola, rozmów z użytkownicy końcowi nie będą rejestrowane.

## <a name="what-is-logged"></a>Co to jest zalogowany 

W oknach dialogowych dziennika dla każdego Włącz uczeń konwersacji przechowuje dane wejściowe użytkownika, wartości jednostki, wybrane akcje i sygnatury czasowe.  Te dzienniki są przechowywane przez czas, a następnie zostaje odrzucone (patrz strona pomocy na "wartości domyślnej i granice" Aby uzyskać szczegółowe informacje).  

Uczeń konwersacji tworzy unikatowy identyfikator dla każdego zalogowanego okna dialogowego.  Uczeń konwersacji jest *nie* przechowywanie identyfikator użytkownika przy użyciu okien dialogowych rejestrowane.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Skojarzenie rejestrowany okien dialogowych za pomocą Identyfikatora użytkownika

Często jest to ważne można było skojarzyć zalogowanego okien dialogowych z Identyfikatorem użytkownika — na przykład, aby można było pobrać lub usunąć zarejestrowane okien dialogowych konkretnego użytkownika.  Ponieważ uczeń konwersacji nie przechowuje identyfikator użytkownika, to skojarzenie musi być utrzymywane przez kod dewelopera.  

Aby utworzyć to mapowanie, Uzyskaj identyfikator rejestrowane okna dialogowego w `EntityDetectionCallback`; następnie w magazynie usługi bot zapisać skojarzenie Identyfikatora użytkownika i rejestrowane okna dialogowego.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
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

W każdym z poniższych połączeń HTTP Dodaj następujący nagłówek:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

gdzie `<LUIS_AUTHORING_KEY>` jest LUIS tworzenia klucz umożliwiający dostęp do aplikacji uczeń konwersacji.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Jak uzyskać dane pierwotne dotyczące zarejestrowanych okna dialogowego

Aby uzyskać dane pierwotne dotyczące okna dialogowego dziennika, służy to wywołania HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Gdzie `<appId>` to identyfikator GUID dla tego modelu uczeń konwersacji i `<logDialgoId>` to identyfikator okna dialogowego dziennika, które mają zostać pobrane.  

> [!NOTE]
> Okna dialogowe dziennika może być edytowane przez dewelopera, a następnie zapisywana jak szkolenie w oknach dialogowych.  Po zakończeniu tej operacji uczeń konwersacji przechowuje identyfikator okna dialogowego dziennika "źródło" za pomocą okna dialogowego pociągu.  Dalsze okno dialogowe train może być "zgodnie" w Interfejsie użytkownika; Jeśli okno dialogowe train ma identyfikator okna dialogowego dziennika skojarzone źródło, następnie oddziały z tego okna dialogowego train są oznaczane za pomocą tego samego identyfikatora dziennika okna dialogowego.

Aby uzyskać wszystkie okna dialogowe train utworzone na podstawie okna dialogowego dziennika, wykonaj następujące kroki.

Najpierw należy pobrać wszystkie train okna dialogowe:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Gdzie `<appId>` to identyfikator GUID dla tego modelu uczeń konwersacji.  

Spowoduje to zwrócenie wszystkich train okien dialogowych.  Wyszukaj na tej liście skojarzonych z nim `sourceLogDialogId`i zanotuj skojarzonego `trainDialogId`. 

Do pojedynczego uczenie okna dialogowego za pomocą Identyfikatora:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Gdzie `<appId>` to identyfikator GUID dla tego modelu uczeń konwersacji i `<trainDialogId>` to identyfikator okna dialogowego train ma zostać pobrane.  

## <a name="how-to-delete-a-logged-dialog"></a>Jak usunąć zarejestrowane okna dialogowego

Jeśli chcesz usunąć dziennik okna dialogowego, biorąc pod uwagę jego Identyfikatora, można użyć tego wywołania HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Gdzie `<appId>` to identyfikator GUID dla tego modelu uczeń konwersacji i `<logDialogId>` to identyfikator okna dialogowego dziennika, które chcesz usunąć. 

Jeśli chcesz usunąć train okna dialogowego, biorąc pod uwagę jego Identyfikatora, można użyć tego wywołania HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Gdzie `<appId>` to identyfikator GUID dla tego modelu uczeń konwersacji i `<trainDialogId>` to identyfikator okna dialogowego train, którą chcesz usunąć. 

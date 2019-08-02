---
title: Zarządzanie danymi użytkowników przy użyciu Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak zarządzać danymi użytkowników przy użyciu Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 857e899764d284e2d78f1172fa8eeac04c57d618
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705193"
---
# <a name="managing-user-data"></a>Zarządzanie danymi użytkowników

Na tej stronie opisano, co Conversation Learner usługa w chmurze rejestruje podczas przeprowadzania okien dialogowych z użytkownikami końcowymi.  Opisano w nim również sposób kojarzenia dzienników Conversation Learner z identyfikatorami użytkowników, dzięki czemu można pobrać lub usunąć wszystkie dzienniki skojarzone z określonym użytkownikiem.

## <a name="overview-of-end-user-data-logging"></a>Omówienie rejestrowania danych użytkowników końcowych

Domyślnie usługa w chmurze Conversation Learner rejestruje interakcje między użytkownikami końcowymi i bot.  Te dzienniki są ważne dla usprawnienia bot, co pozwala identyfikować przypadki, w których bot wyodrębniono niepoprawną jednostkę lub wybrano niepoprawną akcję.  Te błędy można następnie skorygować, przechodząc do strony okna dialogowe logowania w interfejsie użytkownika, wprowadzając poprawki i przechowując to poprawione okno dialogowe jako nowe okno dialogowe szkolenia. Aby uzyskać więcej informacji, zobacz Samouczek dotyczący okna dialogowego dzienników.

## <a name="how-to-disable-logging"></a>Jak wyłączyć rejestrowanie

Możesz kontrolować, czy konwersacje z użytkownikami końcowymi znajdują się na stronie "Ustawienia" dla modelu Conversation Learner.  W przypadku "konwersacji dzienników" istnieje pole wyboru.  Po usunięciu zaznaczenia tego pola konwersacje z użytkownikami końcowymi nie będą rejestrowane.

## <a name="what-is-logged"></a>Co jest rejestrowane 

W oknie dialogowym dzienników Conversation Learner przechowuje dane wejściowe użytkownika, wartości jednostek, wybrane akcje i sygnatury czasowe dla każdego z nich.  Te dzienniki są przechowywane przez pewien czas, a następnie odrzucane (zobacz stronę pomocy na "wartość domyślna i granice", aby uzyskać szczegółowe informacje).  

Conversation Learner tworzy unikatowy identyfikator dla każdego rejestrowanego okna dialogowego.  Conversation Learner nie *przechowuje identyfikatora* użytkownika w zarejestrowanych oknach dialogowych.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Kojarzenie zalogowanych okien dialogowych z IDENTYFIKATORem użytkownika

Często ważne jest, aby można było skojarzyć zarejestrowane okna dialogowe z IDENTYFIKATORem użytkownika — na przykład, aby można było pobrać lub usunąć zarejestrowane okna dialogowe z określonego użytkownika.  Ponieważ Conversation Learner nie przechowuje identyfikatora użytkownika, to skojarzenie musi być obsługiwane przez kod dewelopera.  

Aby utworzyć to mapowanie, Uzyskaj identyfikator zarejestrowanego okna dialogowego `EntityDetectionCallback`, a następnie w magazynie bot, Zapisz skojarzenie między identyfikatorem użytkownika i tym zarejestrowanym oknem dialogowym.  

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

## <a name="headers-for-http-calls"></a>Nagłówki wywołań HTTP

W każdym z wywołań HTTP poniżej Dodaj następujący nagłówek:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

gdzie `<LUIS_AUTHORING_KEY>` jest używany klucz autorstwa Luis w celu uzyskania dostępu do aplikacji Conversation Learner.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Jak uzyskać dane pierwotne dla rejestrowanego okna dialogowego

Aby uzyskać dane pierwotne dla okna dialogowego dziennika, można użyć tego wywołania HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Gdzie `<appId>` jest identyfikator GUID tego modelu Conversation Learner i `<logDialgoId>` jest identyfikatorem okna dialogowego dziennika, które chcesz pobrać.  

> [!NOTE]
> Okna dialogowe dzienników mogą być edytowane przez dewelopera, a następnie przechowywane jako okna dialogowe szkolenia.  Gdy to zrobisz, Conversation Learner przechowuje identyfikator okna dialogowego dziennika "Źródło" w oknie dialogowym uczenie.  Dodatkowo okno dialogowe szkolenia może być "rozgałęzine" w interfejsie użytkownika; Jeśli okno dialogowe szkolenia ma skojarzony identyfikator okna dialogowego dziennika źródła, rozgałęzienia z tego okna dialogowego pouczenia zostaną oznaczone przy użyciu tego samego identyfikatora okna dialogowego dziennika.

Aby uzyskać wszystkie okna dialogowe uczenia, które zostały uzyskane z okna dialogowego dziennika, wykonaj następujące czynności.

Najpierw Pobierz wszystkie okna dialogowe szkolenia:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Gdzie `<appId>` jest identyfikator GUID dla tego Conversation Learnergo modelu.  

Spowoduje to zwrócenie wszystkich okien dialogowych uczenia.  Wyszukaj w tej liście skojarzone `sourceLogDialogId`i zanotuj skojarzone. `trainDialogId` 

Do okna dialogowego pojedynczego szkolenia według identyfikatora:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Gdzie `<appId>` jest identyfikator GUID tego Conversation Learnergo modelu i `<trainDialogId>` jest identyfikatorem okna dialogowego szkolenia, które chcesz pobrać.  

## <a name="how-to-delete-a-logged-dialog"></a>Jak usunąć zarejestrowane okno dialogowe

Jeśli chcesz usunąć okno dialogowe dziennika z IDENTYFIKATORem, możesz użyć tego wywołania HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Gdzie `<appId>` jest identyfikator GUID tego modelu Conversation Learner i `<logDialogId>` jest identyfikatorem okna dialogowego dziennika, które chcesz usunąć. 

Jeśli chcesz usunąć okno dialogowe uczenia z IDENTYFIKATORem, możesz użyć tego wywołania HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Gdzie `<appId>` jest identyfikator GUID tego Conversation Learnergo modelu i `<trainDialogId>` jest identyfikatorem okna dialogowego szkolenia, które chcesz usunąć. 

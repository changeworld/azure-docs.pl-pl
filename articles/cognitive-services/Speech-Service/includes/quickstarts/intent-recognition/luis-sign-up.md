---
title: 'Szybki Start: Rozpoznawanie mowy, intencji i jednostek, usługa Python-Speech'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660580"
---
Aby ukończyć funkcję szybkiego startu rozpoznawania intencji, musisz utworzyć konto LUIS i projekt przy użyciu portalu LUIS w wersji zapoznawczej. Ten przewodnik Szybki Start wymaga tylko subskrypcji LUIS. Subskrypcja usługi mowy nie jest wymagana.

Pierwszą czynnością, którą należy wykonać, jest utworzenie konta LUIS i aplikacji za pomocą portalu LUIS w wersji zapoznawczej. Utworzona aplikacja LUIS będzie używać prekompilowanej domeny do automatyzacji domowej, która zapewnia intencje, jednostki i przykład wyrażenia długości. Po zakończeniu będziesz mieć punkt końcowy LUIS uruchomiony w chmurze, który można wywołać przy użyciu zestawu Speech SDK. 

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć aplikację LUIS: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Szybki Start: Tworzenie wbudowanej aplikacji domeny</a>

Gdy skończysz, będziesz potrzebować trzech rzeczy: 

* Twój klucz LUIS
* Region LUIS
* Identyfikator aplikacji LUIS

Oto, gdzie można znaleźć te informacje w [portalu Luis w wersji zapoznawczej](https://preview.luis.ai/):

1. W portalu LUIS w wersji zapoznawczej wybierz pozycję **Zarządzaj**, a następnie wybierz pozycję **zasoby platformy Azure**. Na tej stronie znajdziesz klucz LUIS i lokalizację (czasami określany jako _region_).  

   > [!div class="mx-imgBorder"]
   > ![klucz LUIS i lokalizację](../../../media/luis/luis-key-region.png)

2. Po uzyskaniu klucza i lokalizacji będziesz potrzebować identyfikatora aplikacji. Wybierz pozycję **Ustawienia aplikacji** — na tej stronie jest dostępny identyfikator aplikacji.

   > [!div class="mx-imgBorder"]
   > ![identyfikator aplikacji LUIS](../../../media/luis/luis-app-id.png)
---
title: Migrowanie z usługi Custom Speech Service do usługi rozpoznawania mowy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak migracja do usługi Custom Speech do usługi rozpoznawania mowy.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: db09a85daff553dc911d039d37c826343e93d240
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338526"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrowanie z usługi Custom Speech usługi mowy

W tym artykule należy użyć do migracji aplikacji z usługi Custom Speech do usługi rozpoznawania mowy.

Custom Speech Service jest teraz częścią usługi mowy. Przełącz się do usługi rozpoznawania mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.
 
## <a name="migration-for-new-customers"></a>Migracja dla nowych klientów

Model cen jest prostsze, przejście do modelu cen godziny usługi mowy.   

1. Utwórz zasób usługi platformy Azure, w każdym regionie, w którym aplikacja jest dostępna. Nazwa zasobu platformy Azure jest **mowy**. Dla następujących usług w tym samym regionie, zamiast tworzyć oddzielne zasoby, można użyć jednego zasobu platformy Azure:

    * Zamiany mowy na tekst
    * Niestandardowe mowy na tekst
    * Zamiana tekstu na mowę
    * Tłumaczenie mowy

2. Pobierz [mowy SDK](speech-sdk.md). 

3. Postępuj zgodnie z przewodników Szybki Start i przykładowych zestawach SDK przy użyciu interfejsów API poprawne. Korzystając z interfejsów API REST, należy użyć poprawne punkty końcowe i klucze zasobu. 

4. Aktualizowanie aplikacji klienta do korzystania z usługi rozpoznawania mowy i interfejsów API. 

> [!NOTE]
> * Usługi LUIS — Jeśli włączono mowy w Language Understanding (LUIS), pojedynczego zasobu usługi LUIS w tym samym regionie będzie działać w przypadku usługi LUIS, a także wszystkich usług mowy. Zobacz [rozpoznawanie intencji z wypowiedzi](how-to-recognize-intents-from-speech-csharp.md) dokumentacji.
> * Tłumaczenie tekstu na tekst, który nie jest częścią usługi mowy. Musi ona własnej subskrypcji zasobu platformy Azure.
  


## <a name="migration-for-existing-customers"></a>Migracja istniejących klientów

Istniejący klienci są wymagane do migracji ich istniejące klucze zasobu z usługą mowy w portalu usługi mowy. Wykonaj następujące czynności: 

> [!NOTE] 
> Klucze zasobów można migrować tylko w obrębie tego samego regionu. 

1. Zaloguj się do [cris.ai](http://www.cris.ai) portalu i wybierz subskrypcję, w prawym górnym menu. 

2. Wybierz **migracji wybranych subskrypcji**.

3. Wprowadź klucz subskrypcji w polu tekstowym, a następnie wybierz pozycję **migracji**.

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md)
* Dowiedz się, [zamiana mowy na tekst](./speech-to-text.md) pojęcia

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi rozpoznawania mowy i zestawu SDK](speech-sdk.md#get-the-sdk)
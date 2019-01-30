---
title: Migrowanie z usługi Custom Speech do usług przetwarzania mowy
titlesuffix: Azure Cognitive Services
description: Custom Speech Service jest teraz częścią usługi mowy. Przełącz się do usługi rozpoznawania mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 594233b9e345f9578c218b042a64ea167d50addb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211036"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migracja z Custom Speech Service do usługi rozpoznawania mowy

W tym artykule należy użyć do migracji aplikacji z Custom Speech Service do usługi rozpoznawania mowy.

Custom Speech Service jest teraz częścią usługi mowy. Przełącz się do usługi rozpoznawania mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.

## <a name="migration-for-new-customers"></a>Migracja dla nowych klientów

Model cen jest prostsze, używając model cen oparty na godzinę, aby usługi mowy.  

1. Utwórz zasób usługi platformy Azure, w każdym regionie, w którym aplikacja jest dostępna. Nazwa zasobu platformy Azure jest **mowy**. Dla następujących usług w tym samym regionie, zamiast tworzyć oddzielne zasoby, można użyć jednego zasobu platformy Azure:

    * Zamiana mowy na tekst
    * Niestandardowe mowy na tekst
    * Zamiana tekstu na mowę
    * Tłumaczenie mowy

2. Pobierz [mowy SDK](speech-sdk.md).

3. Postępuj zgodnie z przewodników Szybki Start i przykładowych zestawach SDK przy użyciu interfejsów API poprawne. Korzystając z interfejsów API REST, należy użyć poprawne punkty końcowe i klucze zasobu.

4. Aktualizowanie aplikacji klienta do korzystania z usługi rozpoznawania mowy i interfejsów API.

> [!NOTE]
> * Włączenie funkcji rozpoznawania mowy w Language Understanding (LUIS) pojedynczy zasób usługi LUIS w tym samym regionie będzie działać w przypadku usługi LUIS, a także wszystkich usług mowy. Aby uzyskać więcej informacji, zobacz [rozpoznawanie intencji z wypowiedzi](how-to-recognize-intents-from-speech-csharp.md).
> * Tłumaczenie tekstu na tekst, który nie jest częścią usługi mowy. Ta funkcja wymaga własnej subskrypcji zasobu platformy Azure.
 


## <a name="migration-for-existing-customers"></a>Migracja istniejących klientów

Migruj istniejące klucze zasobu z usługą mowy w portalu usługi mowy. Wykonaj następujące czynności:

> [!NOTE]
> Klucze zasobów można migrować tylko w obrębie tego samego regionu.

1. Zaloguj się do [cris.ai](http://www.cris.ai) portalu, a następnie wybierz subskrypcję, w prawym górnym menu.

2. Wybierz **migracji wybranych subskrypcji**.

3. Wprowadź klucz subskrypcji w polu tekstowym, a następnie wybierz pozycję **migracji**.

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj bezpłatnie usługi mowy](get-started.md).
* Dowiedz się, [zamiana mowy na tekst](./speech-to-text.md) pojęcia.

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi rozpoznawania mowy i zestawu SDK](speech-sdk.md#get-the-sdk)

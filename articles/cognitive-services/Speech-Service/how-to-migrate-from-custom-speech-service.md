---
title: Migrowanie z usługi Custom Speech do usług przetwarzania mowy
titlesuffix: Azure Cognitive Services
description: Custom Speech Service jest teraz częścią usług przetwarzania mowy. Przełącz się do usług przetwarzania mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 501555c68b7b5922bbf5a74d2a7ed5e548b900c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57833541"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migracja z Custom Speech Service do usługi rozpoznawania mowy

W tym artykule należy użyć do migracji aplikacji z Custom Speech Service do usługi rozpoznawania mowy.

Custom Speech Service jest teraz częścią usługi mowy. Przełącz się do usług przetwarzania mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.

## <a name="migration-for-new-customers"></a>Migracja dla nowych klientów

Model cen jest prostsze, używając model cen oparty na godzinę, aby usługi mowy.  

1. Utwórz zasób usługi platformy Azure, w każdym regionie, w którym aplikacja jest dostępna. Nazwa zasobu platformy Azure jest **mowy**. Dla następujących usług w tym samym regionie, zamiast tworzyć oddzielne zasoby, można użyć jednego zasobu platformy Azure:

    * Zamiana mowy na tekst
    * Niestandardowe mowy na tekst
    * Zamiana tekstu na mowę
    * Tłumaczenie mowy

2. Pobierz [mowy SDK](speech-sdk.md).

3. Postępuj zgodnie z przewodników Szybki Start i przykładowych zestawach SDK przy użyciu interfejsów API poprawne. Korzystając z interfejsów API REST, należy użyć poprawne punkty końcowe i klucze zasobu.

4. Aktualizowanie aplikacji klienta do korzystania z usług przetwarzania mowy i interfejsów API.

## <a name="migration-for-existing-customers"></a>Migracja istniejących klientów

Migruj istniejące klucze zasobów do usług mowy w portalu usług przetwarzania mowy. Wykonaj następujące czynności:

> [!NOTE]
> Klucze zasobów można migrować tylko w obrębie tego samego regionu.

1. Zaloguj się do [cris.ai](https://www.cris.ai) portalu, a następnie wybierz subskrypcję, w prawym górnym menu.

2. Wybierz **migracji wybranych subskrypcji**.

3. Wprowadź klucz subskrypcji w polu tekstowym, a następnie wybierz pozycję **migracji**.

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj bezpłatnie usługi mowy](get-started.md).
* Dowiedz się, [zamiana mowy na tekst](./speech-to-text.md) pojęcia.

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usług przetwarzania mowy i zestaw SDK rozpoznawania mowy](speech-sdk.md#get-the-sdk)

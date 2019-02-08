---
title: Migrowanie z usługi Custom Speech do usług przetwarzania mowy
titlesuffix: Azure Cognitive Services
description: Custom Speech Service jest teraz częścią usługi mowy. Przełącz się do usługi rozpoznawania mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 698962aa0e3d72b204c4e990aa1384b44bf3896f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856900"
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

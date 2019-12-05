---
title: Migrowanie z Custom Speech Service do usługi mowy
titleSuffix: Azure Cognitive Services
description: Custom Speech Service jest teraz częścią usługi mowy. Przejdź do usługi mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805930"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrowanie z Custom Speech Service do usługi mowy

Ten artykuł służy do migrowania aplikacji z Custom Speech Service do usługi mowy.

Custom Speech Service jest teraz częścią usługi mowy. Przejdź do usługi mowy, aby korzystać z najnowszych aktualizacji jakości i funkcji.

## <a name="migration-for-new-customers"></a>Migracja dla nowych klientów

Model cen jest prostszy, przy użyciu opartego na godzinie modelu cen usługi mowy.  

1. Utwórz zasób usługi platformy Azure, w każdym regionie, w którym aplikacja jest dostępna. Nazwa zasobu platformy Azure jest **mowy**. Dla następujących usług w tym samym regionie, zamiast tworzyć oddzielne zasoby, można użyć jednego zasobu platformy Azure:

    * Zamiana mowy na tekst
    * Niestandardowe mowy na tekst
    * Zamiana tekstu na mowę
    * Tłumaczenie mowy

2. Pobierz [mowy SDK](speech-sdk.md).

3. Postępuj zgodnie z przewodników Szybki Start i przykładowych zestawach SDK przy użyciu interfejsów API poprawne. Korzystając z interfejsów API REST, należy użyć poprawne punkty końcowe i klucze zasobu.

4. Zaktualizuj aplikację kliencką tak, aby korzystała z usługi mowy i interfejsów API.

## <a name="migration-for-existing-customers"></a>Migracja istniejących klientów

Przeprowadź migrację istniejących kluczy zasobów do usługi mowy w portalu usługi Speech Service. Wykonaj następujące czynności:

> [!NOTE]
> Klucze zasobów można migrować tylko w obrębie tego samego regionu.

1. Zaloguj się do [cris.ai](https://cris.ai/Home/CustomSpeech) portalu, a następnie wybierz subskrypcję, w prawym górnym menu.

2. Wybierz **migracji wybranych subskrypcji**.

3. Wprowadź klucz subskrypcji w polu tekstowym, a następnie wybierz pozycję **migracji**.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj bezpłatnie usługę Speech](get-started.md).
* Dowiedz się, [zamiana mowy na tekst](./speech-to-text.md) pojęcia.

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi mowy i zestawu Speech SDK](speech-sdk.md#get-the-sdk)

---
title: Dowiedz się, jak przeglądać pojęcia narzędzi — Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o narzędziu Content Moderator Review, witrynie sieci Web, która koordynuje połączone działania w zakresie moderowania siarki i oceny człowieka.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169503"
---
# <a name="content-moderator-review-tool"></a>Narzędzie Do przeglądania moderatora zawartości

Moderator zawartości platformy Azure udostępnia usługi łączenia moderowania zawartości uczenia maszynowego z recenzjami dla ludzi, a [witryna](https://contentmoderator.cognitive.microsoft.com) sieci Web narzędzia Recenzja jest przyjaznym dla użytkownika frontoniakiem, który zapewnia szczegółowy dostęp do tych usług.

![Pulpit nawigacyjny narzędzia do przeglądania w przeglądarce](./images/0-dashboard.png)

## <a name="what-it-does"></a>Wyniki działania

[Narzędzie Recenzja](https://contentmoderator.cognitive.microsoft.com), używane w połączeniu z interfejsami API moderowania wspomaganego maszynowo, umożliwia wykonywanie następujących zadań w procesie moderowania zawartości:

- Użyj jednego zestawu narzędzi, aby moderować zawartość w wielu formatach (tekst, obraz i wideo).
- Zautomatyzuj tworzenie [ludzkich recenzji,](../review-api.md#reviews) gdy pojawiają się wyniki interfejsu API moderowania.
- Przypisywanie lub eskalacja recenzji zawartości do wielu zespołów recenzentów, uporządkowanych według kategorii zawartości lub poziomu doświadczenia.
- Użyj domyślnych lub niestandardowych filtrów logicznych[(przepływów pracy)](../review-api.md#workflows)do sortowania i śledzenia zawartości bez pisania kodu.
- [Łączniki](./configure.md#connectors) można używać do przetwarzania zawartości za pomocą usług Microsoft PhotoDNA, Text Analytics i Face oprócz interfejsów API moderatora zawartości.
- Tworzenie własnego łącznika do tworzenia przepływów pracy dla dowolnego interfejsu API lub procesu biznesowego.
- Uzyskaj kluczowe dane dotyczące wydajności procesów moderowania zawartości.

## <a name="review-tool-dashboard"></a>Pulpit nawigacyjny narzędzia przeglądu

Na karcie **Pulpit nawigacyjny** możesz zobaczyć kluczowe metryki dotyczące recenzji zawartości wykonanych w narzędziu. Zobacz liczbę pełnych, kompletnych i oczekujących recenzji zawartości obrazu, tekstu i wideo. Możesz również zobaczyć podział użytkowników i zespołów, które ukończyły recenzje, a także tagi moderowania, które zostały zastosowane.

![Wyświetl pulpit nawigacyjny](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Przeglądanie poświadczeń narzędzia

Po zarejestrowaniu się za pomocą [narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com)zostanie wyświetlony monit o wybranie regionu platformy Azure dla ciebie. Jest to spowodowane [narzędzie przegląd](https://contentmoderator.cognitive.microsoft.com) generuje bezpłatny klucz próbny dla usług Azure Content Moderator usług; ten klucz będzie potrzebny do uzyskania dostępu do dowolnej usługi z połączenia REST lub sdk klienta. Możesz wyświetlić adres URL klucza i punktu końcowego interfejsu API, wybierając **pozycję Ustawienia** > **poświadczeń**.

![Poświadczenia moderatora zawartości](images/settings-6-credentials.png)

## <a name="next-steps"></a>Następne kroki

Zobacz [Konfigurowanie narzędzia recenzji,](./configure.md) aby dowiedzieć się, jak uzyskać dostęp do zasobów narzędzia przeglądu i zmienić ustawienia.
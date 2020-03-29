---
title: Ponowienie prób po błędzie
description: Sprawdź błędy i ponów próbę.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919995"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Wykrywanie i obsługa błędów usługi Batch

Należy pamiętać, aby sprawdzić błędy podczas pracy z interfejsem API usługi REST. Nie jest rzadkością, aby błędy występowały podczas uruchamiania zadań wsadowych.

## <a name="common-errors"></a>Typowe błędy 

- Awarie sieci — są to żądania, które nigdy nie osiągnęły usługi Batch lub odpowiedź batch nie dotarły do klienta na czas.
- Wewnętrzne błędy serwera - są to standardowe 5xx kod stanu odpowiedź HTTP.
- Ograniczanie może powodować błędy, takie jak 429 lub 503 kod stanu odpowiedzi HTTP z ponów ponawiania nagłówka.
- 4xx błędy, które zawierają takie błędy jak JużExists i InvalidOperation. Oznacza to, że zasób nie jest w prawidłowym stanie przejścia stanu.

Aby uzyskać szczegółowe informacje na temat różnych typów kodów błędów i określonych kodów błędów, zobacz [Stan partii i kody błędów](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Kiedy ponowić próbę

Wsadowe interfejsy API powiadomią cię, jeśli wystąpi błąd. Wszystkie one mogą być ponowione i wszystkie one obejmują globalnego programu obsługi ponawiania prób w tym celu. Najlepiej jest użyć tego wbudowanego mechanizmu.

Po awarii należy poczekać trochę (kilka sekund między ponownych prób) przed ponowieniem próby. Jeśli ponowisz próbę zbyt często lub zbyt szybko, program obsługi ponawiania będzie ograniczać.

### <a name="for-more-information"></a>Więcej informacji  

[Wsadowe interfejsy API i narzędzia](batch-apis-tools.md) łącza do informacji referencyjnych interfejsu API. Interfejs API platformy .NET, na przykład, ma [RetryPolicyProvider klasy,]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) gdzie należy określić wymagane zasady ponawiania próby. 

Aby uzyskać szczegółowe informacje na temat każdego interfejsu API i ich domyślnych zasad ponawiania prób, przeczytaj [artykuł Stan partii i kody błędów](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

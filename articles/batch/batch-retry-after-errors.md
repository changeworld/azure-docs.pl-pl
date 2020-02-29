---
title: Ponawianie próby wykonania zadań po wystąpieniu błędu
description: Sprawdź błędy i spróbuj ponownie.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919995"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Wykrywanie i obsługa błędów usługi Batch

Ważne jest, aby pamiętać o błędach podczas pracy z interfejsem API usługi REST. Nierzadko występują błędy podczas uruchamiania zadań wsadowych.

## <a name="common-errors"></a>Typowe błędy 

- Awarie sieci — te żądania, które nigdy nie dotarły do partii lub nie dotarły do klienta w czasie.
- Błędy wewnętrznego serwera — jest to standardowy kod stanu 5xx odpowiedź HTTP.
- Ograniczanie wydajności może spowodować błędy, takie jak 429 lub 503, kod stanu odpowiedzi HTTP przy użyciu nagłówka retry-After.
- 4xx błędy, które zawierają takie błędy jak już istnieje i InvalidOperation. Oznacza to, że zasób nie jest w poprawnym stanie dla przejścia stanu.

Aby uzyskać szczegółowe informacje o różnych typach kodów błędów i określonych kodach błędów, zobacz [stan partii i kody błędów](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Kiedy ponowić próbę

Interfejsy API partii będą powiadamiane o wystąpieniu błędu. Wszystkie te możliwości są ponawiane i wszystkie obejmują globalną procedurę obsługi ponowień. Najlepszym rozwiązaniem jest użycie tego wbudowanego mechanizmu.

Po awarii należy poczekać chwilę (kilka sekund między ponownymi próbami) przed ponowną próbą. W przypadku ponawiania próby zbyt częste lub zbyt szybko, procedura obsługi ponowień będzie ograniczać.

### <a name="for-more-information"></a>Więcej informacji  

[Interfejsy API i narzędzia usługi Batch](batch-apis-tools.md) zawierają linki do informacji referencyjnych interfejsu API. Interfejs API platformy .NET, na przykład, ma [klasę RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) , w której należy określić wymagane zasady ponawiania. 

Aby uzyskać szczegółowe informacje na temat poszczególnych interfejsów API i ich domyślnych zasad ponawiania, Przeczytaj [stan partii i kody błędów](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

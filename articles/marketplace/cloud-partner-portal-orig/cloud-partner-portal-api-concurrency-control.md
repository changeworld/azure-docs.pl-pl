---
title: Kontrola współbieżności | Azure Marketplace
description: Strategie kontroli współbieżności dla portalu partnerów w chmurze publikowania interfejsów API.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256403"
---
# <a name="concurrency-control"></a>Kontrola współbieżności

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Każde wywołanie interfejsu API publikowania interfejsów API portalu partnerów w chmurze musi jawnie określać, której strategii kontroli współbieżności użyć. Niepowodzenie podania nagłówka **If-Match** spowoduje odpowiedź na błąd HTTP 400. Oferujemy dwie strategie kontroli współbieżności.

-   **Optymistyczne** — klient wykonujący aktualizację sprawdza, czy dane uległy zmianie od czasu ostatniego odczytu danych.
-   **Ostatni wygrywa** — klient bezpośrednio aktualizuje dane, niezależnie od tego, czy inna aplikacja zmodyfikowała je od czasu ostatniego odczytu.

<a name="optimistic-concurrency-workflow"></a>Optymistyczny przepływ pracy współbieżności
-------------------------------

Zaleca się użycie strategii optymistycznej współbieżności z następującym przepływem pracy, aby zagwarantować, że nie zostaną wprowadzone żadne nieoczekiwane zmiany w zasobach.

1.  Pobieranie jednostki przy użyciu interfejsów API. Odpowiedź zawiera wartość ETag, która identyfikuje aktualnie przechowywaną wersję jednostki (w momencie odpowiedzi).
2.  W momencie aktualizacji należy uwzględnić tę samą wartość ETag w nagłówku żądania obowiązkowego **if-match.**
3.  Interfejs API porównuje wartość ETag otrzymaną w żądaniu z bieżącą wartością ETag jednostki w transakcji niepodzielnej.
    *   Jeśli wartości ETag są różne, `412 Precondition Failed` interfejs API zwraca odpowiedź HTTP. Ten błąd wskazuje, że inny proces zaktualizował jednostkę od czasu ostatniego pobrania jej przez klienta lub że wartość ETag określona w żądaniu jest niepoprawna.
    *  Jeśli wartości ETag są takie same lub nagłówek **If-Match** zawiera znak`*`gwiazdki symboli wieloznacznych ( ), interfejs API wykonuje żądaną operację. Operacja interfejsu API aktualizuje również przechowywaną wartość ETag jednostki.


> [!NOTE]
> Określenie symbolu wieloznacznego (*) w nagłówku **If-Match** powoduje w interfejsie API przy użyciu strategii współbieżności last-one-wins. W takim przypadku porównanie ETag nie występuje i zasób jest aktualizowany bez żadnych kontroli. 

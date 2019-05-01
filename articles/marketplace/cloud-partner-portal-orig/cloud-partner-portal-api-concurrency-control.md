---
title: Kontrola współbieżności | Portal Azure Marketplace
description: Strategie kontroli współbieżności portalu Cloud Partner, publikowanie interfejsów API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8cdcfd84a2f3bd4f920b97392255237db173cbf9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935592"
---
# <a name="concurrency-control"></a>Kontrola współbieżności

Każde wywołanie do portalu Cloud Partner, publikowanie interfejsów API, należy jawnie określić które strategii kontroli współbieżności. Nie można podać **If-Match** nagłówka spowoduje odpowiedź o błędzie HTTP 400. Firma Microsoft oferuje dwa strategie do sterowania współbieżnością.

-   **Optymistyczna** — klient wykonywanie aktualizacji sprawdza, jeśli dane zostały zmienione od czasu ostatniego odczytu danych.
-   **Ostatnie jeden serwer wins** — klient aktualizuje bezpośrednio danych, niezależnie od tego, czy inna aplikacja został on zmodyfikowany od czasu ostatniego odczytu czasu.

<a name="optimistic-concurrency-workflow"></a>Optymistyczna współbieżność przepływu pracy
-------------------------------

Zalecamy używanie strategię optymistycznej współbieżności z poniższym przepływie pracy w celu zagwarantowania, czy nie nieoczekiwane zmiany zostały wprowadzone do zasobów.

1.  Pobierać jednostki za pomocą interfejsów API. Odpowiedź zawiera element ETag określająca aktualnie przechowywana wersja jednostki (na czas odpowiedzi).
2.  W momencie aktualizacji objęte ta sama wartość elementu ETag obowiązkowy **If-Match** nagłówek żądania.
3.  Interfejs API porównuje wartość elementu ETag została odebrana w żądaniu z bieżącą wartością elementu ETag jednostki w transakcji niepodzielnej.
    *   Jeśli element ETag wartości są różne, interfejs API zwraca `412 Precondition Failed` odpowiedzi HTTP. Ten błąd wskazuje, albo inny proces został zaktualizowany jednostkę od czasu ostatniego pobrania klienta lub że wartość elementu ETag określony w żądaniu jest nieprawidłowe.
    *  Jeśli element ETag wartości są takie same, lub **If-Match** nagłówek zawiera znak symbolu wieloznacznego gwiazdki (`*`), interfejs API, wykonuje żądaną operację. Operacja interfejsu API aktualizuje również przechowywaną wartość elementu ETag jednostki.


> [!NOTE]
> Określanie symbol wieloznaczny (*) w **If-Match** nagłówka wyniki w interfejsie API za pomocą strategii współbieżności ostatni jednej usługi wins. W tym przypadku porównanie element ETag nie występuje i zasób jest aktualizowany bez żadnych testów. 

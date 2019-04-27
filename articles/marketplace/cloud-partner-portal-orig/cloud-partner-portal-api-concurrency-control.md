---
title: Kontrola współbieżności | Dokumentacja firmy Microsoft
description: Strategie kontroli współbieżności portalu Cloud Partner, publikowanie interfejsów API.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ecf0bb6ac7fc77e804c9fc8d62aba52810de5640
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625010"
---
<a name="concurrency-control"></a>Kontrola współbieżności
===================

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

---
title: Kontrola współbieżności | Portal Azure Marketplace
description: Strategie kontroli współbieżności dla interfejsów API publikowania portal Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6e2f8922d42e40d14338f06be983d3913b20859d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819745"
---
# <a name="concurrency-control"></a>Kontrola współbieżności

Każde wywołanie interfejsów API publikowania portal Cloud Partner musi jawnie określić strategię kontroli współbieżności, która ma być używana. Niedostarczenie nagłówka **if-Match** spowoduje wystąpienie błędu HTTP 400. Oferujemy dwie strategie kontroli współbieżności.

-   **Optymistyczny** — klient wykonujący aktualizację weryfikuje, czy dane zostały zmienione od czasu ostatniego odczytu danych.
-   **Ostatni serwer WINS** — klient bezpośrednio aktualizuje dane, bez względu na to, czy inna aplikacja zmodyfikował ją od czasu ostatniego odczytu.

<a name="optimistic-concurrency-workflow"></a>Optymistyczny przepływ pracy współbieżności
-------------------------------

Zalecamy korzystanie z strategii optymistycznej współbieżności z następującym przepływem pracy w celu zagwarantowania, że w Twoich zasobach nie wprowadzono nieoczekiwanych zmian.

1.  Pobieranie jednostki przy użyciu interfejsów API. Odpowiedź zawiera wartość ETag, która identyfikuje aktualnie przechowywaną wersję jednostki (w czasie odpowiedzi).
2.  W czasie aktualizacji należy uwzględnić tę samą wartość ETag w nagłówku żądania obowiązkowego **if-Match** .
3.  Interfejs API porównuje wartość ETag odebraną w żądaniu z bieżącą wartością ETag jednostki w transakcji niepodzielnej.
    *   Jeśli wartości ETag są różne, interfejs API zwraca `412 Precondition Failed` odpowiedzi HTTP. Ten błąd wskazuje, że inny proces zaktualizował jednostkę od momentu ostatniego pobrania przez klienta lub że wartość ETag określona w żądaniu jest niepoprawna.
    *  Jeśli wartości ETag są takie same lub nagłówek **if-Match** zawiera symbol wieloznaczny gwiazdki (`*`), interfejs API wykonuje żądaną operację. Operacja interfejsu API aktualizuje także przechowywaną wartość ETag obiektu.


> [!NOTE]
> Określenie symbolu wieloznacznego (*) w nagłówku **if-Match** spowoduje użycie interfejsu API w ramach strategii współbieżności z ostatnich jednego serwera WINS. W takim przypadku porównanie ETag nie występuje, a zasób jest aktualizowany bez żadnych testów. 

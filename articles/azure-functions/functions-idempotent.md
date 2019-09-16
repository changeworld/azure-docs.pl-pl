---
title: Projektowanie Azure Functions dla identycznych danych wejściowych
description: Kompilowanie Azure Functions idempotentne
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 39e785a1ca7a158ddb90a3e6ba914582c405612a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997395"
---
# <a name="designing-azure-functions-for-identical-input"></a>Projektowanie Azure Functions dla identycznych danych wejściowych

W rzeczywistości opartej na zdarzeniach i architekturze opartych na komunikatach jest konieczne zaakceptowanie identycznych żądań podczas zachowywania integralności danych i stabilności systemu.

Aby to zilustrować, weź pod uwagę przycisk wywoływania Wind. Po naciśnięciu przycisku zostanie on wyróżniony, a winda jest wysyłana do Twojego podłogi. Kilka chwil później ktoś inny dołączy cię w poczekalni. Ta osoba uśmiechuje Cię i naciska po raz drugi przycisk światła. Powrócisz i chuckle się do siebie, ponieważ przypominasz, że polecenie wywołujące windę jest idempotentne.

Naciśnięcie przycisku wywołania podnośnika w drugim, trzecim lub czwartym czasie nie ma wpływu na wynik końcowy. Po naciśnięciu przycisku, bez względu na liczbę razy, winda jest wysyłana do podłogi. Systemy idempotentne, takie jak windy, dają ten sam wynik niezależnie od tego, ile razy są wydawane te same polecenia.

Gdy chodzi o tworzenie aplikacji, należy wziąć pod uwagę następujące scenariusze:

- Co się stanie, jeśli aplikacja kontroli spisu próbuje usunąć ten sam produkt więcej niż raz?
- Jak działa aplikacja kadrowa, jeśli istnieje więcej niż jedno żądanie utworzenia rekordu pracownika dla tej samej osoby?
- Gdzie działa pieniądze, jeśli aplikacja bankowa uzyska 100 żądań, aby wykonać to samo wycofanie?

Istnieje wiele kontekstów, w których żądania do funkcji mogą otrzymać identyczne polecenia. Niektóre sytuacje obejmują:

- Ponawianie próby wysłania tego samego żądania przez wiele razy
- Zbuforowane polecenia są odtwarzane w aplikacji
- Błędy aplikacji wysyłające wiele identycznych żądań

Aby chronić integralność danych i kondycję systemu, aplikacja idempotentne zawiera logikę, która może zawierać następujące zachowania:

- Weryfikowanie istnienia danych przed próbą wykonania operacji usuwania
- Sprawdzanie, czy dane już istnieją, przed podjęciem próby wykonania akcji tworzenia
- Uzgadnianie logiki służącej do tworzenia ostatecznej spójności danych
- Kontrolki współbieżności
- Wykrywanie duplikacji
- Weryfikacja Aktualności danych
- Logika ochrony do weryfikowania danych wejściowych

Ostatecznie idempotentności osiąga się, upewniając się, że dana akcja jest możliwa i jest wykonywana tylko raz.

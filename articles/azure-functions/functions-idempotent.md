---
title: Projektowanie usług Azure Functions dla identycznych danych wejściowych
description: Tworzenie funkcji platformy Azure w celu idempotentu
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226871"
---
# <a name="designing-azure-functions-for-identical-input"></a>Projektowanie usług Azure Functions dla identycznych danych wejściowych

Rzeczywistość architektury opartej na zdarzeniach i opartych na wiadomościach nakazuje akceptowanie identycznych żądań przy jednoczesnym zachowaniu integralności danych i stabilności systemu.

Aby zilustrować, należy wziąć pod uwagę przycisk połączenia windy. Po naciśnięciu przycisku zapala się i winda jest wysyłana na podłogę. Kilka chwil później ktoś inny dołącza do Ciebie w holu. Ta osoba uśmiecha się do ciebie i naciska podświetlany przycisk po raz drugi. Uśmiechasz się i chichoczysz do siebie, gdy przypominasz sobie, że polecenie wezwania windy jest idempotentne.

Naciśnięcie przycisku połączenia windy po raz drugi, trzeci lub czwarty nie ma wpływu na wynik końcowy. Po naciśnięciu przycisku, niezależnie od tego, ile razy winda jest wysyłana na twoje piętro. Systemy idempotentne, takie jak winda, skutkują takim samym wynikiem, bez względu na to, ile razy wydawane są identyczne polecenia.

Jeśli chodzi o tworzenie aplikacji, należy wziąć pod uwagę następujące scenariusze:

- Co się stanie, jeśli aplikacja do kontroli zapasów spróbuje usunąć ten sam produkt więcej niż jeden raz?
- Jak zachowuje się aplikacja zasobów ludzkich, jeśli istnieje więcej niż jedno żądanie utworzenia rekordu pracownika dla tej samej osoby?
- Gdzie idą pieniądze, jeśli aplikacja bankowa dostaje 100 wniosków o taką samą wypłatę?

Istnieje wiele kontekstów, w których żądania do funkcji mogą odbierać identyczne polecenia. Niektóre sytuacje obejmują:

- Ponowij zasady ponawiania prób, wysyłając to samo żądanie wiele razy
- Buforowane polecenia odtwarzane w aplikacji
- Błędy aplikacji wysyłające wiele identycznych żądań

Aby chronić integralność danych i kondycję systemu, aplikacja idempotentna zawiera logikę, która może zawierać następujące zachowania:

- Sprawdzenie istnienia danych przed próbą wykonania usunięcia
- Sprawdzanie, czy dane już istnieją przed próbą wykonania akcji tworzenia
- Uzgadnianie logiki, która tworzy spójność ostateczną w danych
- Kontrole współbieżności
- Wykrywanie duplikacji
- Walidacja świeżości danych
- Logika ochrony do weryfikacji danych wejściowych

Ostatecznie idempotency uzyskuje się poprzez zapewnienie danego działania jest możliwe i jest wykonywany tylko raz.

---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77474129"
---
Zapytania do kontenera są rozliczane w warstwie cenowej zasobu `ApiKey`platformy Azure, który jest używany dla .

Kontenery usługi Azure Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z punktem końcowym pomiaru / rozliczeń. Kontenery muszą przez cały czas przekazywać informacje rozliczeniowe z punktem końcowym rozliczeń. Kontenery usług Cognitive Services nie wysyłają do firmy Microsoft danych klientów, takich jak analizowany obraz lub tekst.

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Kontener wymaga wartości argumentu rozliczeń do uruchomienia. Te wartości umożliwiają kontenerowi łączenie się z punktem końcowym rozliczeń. Kontener raportuje użycie co 10 do 15 minut. Jeśli kontener nie łączy się z platformą Azure w dozwolonym przedziale czasu, kontener będzie nadal uruchamiany, ale nie obsługuje kwerend, dopóki nie zostanie przywrócony punkt końcowy rozliczeń. Próba połączenia jest podejmowana 10 razy w tym samym przedziale czasu od 10 do 15 minut. Jeśli nie można połączyć się z punktem końcowym rozliczeń w ciągu 10 prób, kontener przestaje obsługiwać żądania.

### <a name="billing-arguments"></a>Argumenty rozliczeniowe

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` Polecenie <span class="docon docon-navigate-external x-hidden-focus"></span> </a> uruchomi kontener, gdy wszystkie trzy z następujących opcji zostaną dostarczone z prawidłowymi wartościami:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu usług Cognitive Services, który jest używany do śledzenia informacji rozliczeniowych.<br/>Wartość tej opcji musi być ustawiona na klucz interfejsu API dla `Billing`aprowizowanego zasobu określonego w pliku . |
| `Billing` | Punkt końcowy zasobu usług Cognitive Services, który jest używany do śledzenia informacji rozliczeniowych.<br/>Wartość tej opcji musi być ustawiona na identyfikator URI punktu końcowego aprowizowanego zasobu platformy Azure.|
| `Eula` | Wskazuje, że licencja dla kontenera została zaakceptowana.<br/>Wartość tej opcji musi być ustawiona na **accept**. |

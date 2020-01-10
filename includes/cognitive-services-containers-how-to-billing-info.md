---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704226"
---
Zapytania do kontenera są rozliczane zgodnie z warstwą cenową zasobu platformy Azure, który jest używany dla `<ApiKey>`.

Kontenery usługi Azure Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z punktem końcowym rozliczeń w celu pomiaru. Należy włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą punktu końcowego rozliczeń. Kontenery Cognitive Services nie wysyłają danych klienta, takich jak obraz lub tekst, który jest analizowany, do firmy Microsoft. 

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Kontener wymaga wartości argumentu rozliczenia do uruchomienia. Te wartości umożliwiają kontenerowi nawiązanie połączenia z punktem końcowym rozliczeń. Kontener zgłasza użycie co 10 do 15 minut. Jeśli kontener nie łączy się z platformą Azure w dozwolonym przedziale czasu, kontener nadal działa, ale nie obsługuje zapytań do momentu przywrócenia punktu końcowego rozliczania. Próbuje nawiązać połączenie 10 razy w tym samym przedziale czasu wynoszącym od 10 do 15 minut. Jeśli nie można nawiązać połączenia z punktem końcowym rozliczeń w 10 próbach, kontener przestanie działać. 

### <a name="billing-arguments"></a>Argumenty rozliczeń

Aby można było uruchomić polecenie `docker run`, należy określić wszystkie trzy z następujących opcji z prawidłowymi wartościami:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu Cognitive Services, który służy do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji musi być ustawiona na klucz interfejsu API dla zasobu, który jest określony w `Billing`. |
| `Billing` | Punkt końcowy zasobu Cognitive Services, który służy do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji musi być ustawiona na identyfikator URI punktu końcowego zainicjowanego zasobu platformy Azure.|
| `Eula` | Oznacza zaakceptowanie licencji dla kontenera.<br/>Wartość tej opcji musi być ustawiona na **Zaakceptuj**. |



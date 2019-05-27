---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: 05961f8dd2788179a15e6bfe094484cf4770067b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124342"
---
Zapytania do kontenera są rozliczane według warstwa cenowa zasobu platformy Azure używane do `<ApiKey>`.

Kontenery usługi Azure Cognitive Services nie są licencjonowane do uruchomienia bez połączenia z punktem końcowym rozliczeń do zbierania danych. Należy włączyć kontener, aby komunikować informacje rozliczeniowe z punktem końcowym rozliczeń przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta, takich jak obraz lub tekst, który jest analizowana, do firmy Microsoft. 

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Kontener musi rozliczeń wartości argumentu do uruchomienia. Wartości te umożliwiają kontener, aby nawiązać połączenie z punktem końcowym rozliczeń. Kontener raportów użycia dotyczących co 10 do 15 minut. Jeśli kontener nie łączenie z platformą Azure w przedziale czasu dozwolony, kontenera będzie nadal działać, ale nie obsługuje zapytań do momentu przywrócenia rozliczeń punktu końcowego. Połączenie jest próba 10 razy o takim samym interwale czasowym z 10 do 15 minut. Jeśli nie można połączyć z próbuje rozliczeń punktu końcowego w ciągu 10, kontener przestanie działać. 

### <a name="billing-arguments"></a>Argumenty rozliczeń

Wszystkich trzech z następujących opcji, należy określić prawidłowymi wartościami, aby `docker run` polecenie, aby uruchomić kontenera.

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu usług Cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla aprowizowanego zasobu określonego w `Billing`. |
| `Billing` | Punkt końcowy zasobu usług Cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu platformy Azure aprowizowane.|
| `Eula` | Wskazuje, że akceptowane licencji dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |



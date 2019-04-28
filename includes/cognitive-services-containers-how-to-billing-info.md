---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598913"
---
Zapytania do kontenera są rozliczane według warstwa cenowa zasobu platformy Azure używane do `<ApiKey>`.

Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z punktem końcowym rozliczeń do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe z punktem końcowym rozliczeń przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta (na przykład obraz lub tekst, który jest analizowana) do firmy Microsoft. 

### <a name="connecting-to-azure"></a>Łączenie z platformą Azure

Kontener musi rozliczeń wartości argumentu do uruchomienia. Wartości te umożliwiają kontener, aby nawiązać połączenie z punktu końcowego rozliczeń. Kontener raportów użycia dotyczących co 10 do 15 minut. Jeśli kontener nie łączą się w przedziale czasu dozwolony na platformie Azure, kontenera będzie działać, ale będzie nie obsługiwać stałe żądania do momentu przywrócenia rozliczeń punktu końcowego. Połączenie jest próba 10 razy o takim samym interwale czasowym z 10 do 15 minut. Jeśli nie może nawiązać połączenia rozliczeń punktu końcowego w ciągu 10 prób, kontener przestanie działać. 

### <a name="billing-arguments"></a>Argumenty rozliczeń

Wszystkich trzech z następujących opcji, należy określić prawidłowymi wartościami, aby `docker run` polecenie, aby uruchomić kontener:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu usługi cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla aprowizowanego zasobu określonego w `Billing`. |
| `Billing` | Punkt końcowy zasobu usługi cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu platformy Azure aprowizowane.|
| `Eula` | Wskazuje, że zaakceptowano licencję dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |



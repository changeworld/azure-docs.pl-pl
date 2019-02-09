---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: ce7d8628c28a4a202a05aeea60e71655b4b7f1a2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984923"
---
Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta (wypowiedź) do firmy Microsoft. Kontener raportów użycia dotyczących co 10 do 15 minut.

`docker run` Używa następujących argumentów na potrzeby rozliczeń:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu usługi cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla aprowizowanego zasobu określonego w `Billing`. |
| `Billing` | Punkt końcowy zasobu usługi cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu LUIS Azure elastycznie.|
| `Eula` | Wskazuje, że zaakceptowano licencję dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |

> [!IMPORTANT]
> Prawidłowe wartości należy określić wszystkie trzy opcje, lub uruchomić kontenera.

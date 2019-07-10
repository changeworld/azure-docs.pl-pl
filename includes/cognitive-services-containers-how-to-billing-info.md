---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704226"
---
Zapytania do kontenera są rozliczane według warstwa cenowa zasobu platformy Azure, który służy do `<ApiKey>`.

Kontenery usługi Azure Cognitive Services nie są licencjonowane do uruchomienia bez połączenia z punktem końcowym rozliczeń do zbierania danych. Należy włączyć kontener, aby komunikować informacje rozliczeniowe z punktem końcowym rozliczeń przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta, takich jak obraz lub tekst, który jest analizowana, do firmy Microsoft. 

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Kontener musi rozliczeń wartości argumentu do uruchomienia. Wartości te umożliwiają kontener, aby nawiązać połączenie z punktem końcowym rozliczeń. Kontener raportów użycia dotyczących co 10 do 15 minut. Jeśli kontener nie łączenie z platformą Azure w przedziale czasu dozwolony, kontenera będzie nadal działać, ale nie obsługuje zapytań do momentu przywrócenia rozliczeń punktu końcowego. Połączenie jest próba 10 razy o takim samym interwale czasowym z 10 do 15 minut. Jeśli nie można połączyć z próbuje rozliczeń punktu końcowego w ciągu 10, kontener przestanie działać. 

### <a name="billing-arguments"></a>Argumenty rozliczeń

Dla `docker run` polecenie, aby uruchomić kontenera, należy określić wszystkie trzy następujące opcje prawidłowe wartości:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu usług Cognitive Services, które jest używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla elastycznie zasobu, który jest określony w `Billing`. |
| `Billing` | Punkt końcowy zasobu usług Cognitive Services, które jest używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu platformy Azure aprowizowane.|
| `Eula` | Wskazuje, że akceptowane licencji dla kontenera.<br/>Wartość ta opcja musi być równa **zaakceptować**. |



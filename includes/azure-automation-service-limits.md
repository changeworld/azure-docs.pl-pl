---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 11/07/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 70cdd5a9d0482c24dfeb2037ae56b86cd9339fcf
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285795"
---
| Zasób | Limit maksymalny |Uwagi|
| --- | --- |---|
| Maksymalna liczba nowych zadań, które mogą być przesyłane co 30 sekund na konto usługi Automation (innych niż zaplanowane zadania) |100 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
| Maksymalna liczba równoczesnych zadań uruchomionych na tym samym wystąpieniu czas na konto usługi Automation (innych niż zaplanowane zadania) |200 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
| Maksymalny rozmiar magazynu metadanych zadania dla 30-dniowego okresu kroczącego. | 10GB (około 4 mln zadań)|Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. |
| Maksymalna liczba modułów, które można importować co 30 sekund na konto usługi Automation |5 ||
| Maksymalny rozmiar modułu |100 MB ||
| Czas wykonywania zadania — warstwa bezpłatna |500 minut na subskrypcję na miesiąc kalendarzowy ||
| Maksymalna ilość miejsca na dysku, które może istnieć piaskownicy  **<sup>1</sup>** |1 GB |Dotyczy tylko piaskownic platformy Azure|
| Maksymalna ilość pamięci do piaskownicy  **<sup>1</sup>** |400 MB |Dotyczy tylko piaskownic platformy Azure|
| Maksymalna liczba gniazd sieciowych może istnieć piaskownicy  **<sup>1</sup>** |1000 |Dotyczy tylko piaskownic platformy Azure|
| Maksymalny czas wykonywania dozwolonych dla jednego elementu runbook  **<sup>1</sup>** |3 godziny |Dotyczy tylko piaskownic platformy Azure|
| Maksymalna liczba kont usługi Automation w ramach subskrypcji |Bez ograniczeń ||
|Maksymalna liczba równoczesnych zadań, które można uruchomić na jednym hybrydowego procesu roboczego elementu Runbook|50 ||
| Maksymalna liczba zadania elementu Runbook parametry rozmiar   | 512 kb||
| Maksymalna liczba parametrów   | 50|Można przekazać ciągu JSON lub XML do parametru i przeanalizować za pomocą elementu runbook, jeśli osiągnięty limit liczby 50 parametrów|
| Maksymalny rozmiar ładunku elementu webhook |  512 kb|
| Maksymalna liczba dni, które są przechowywane dane zadania|30 dni|

**<sup>1</sup>**  piaskownicy jest udostępnionym środowiskiem, które mogą być używane przez wiele zadań, zadań przy użyciu tego samego piaskownicy są powiązane przez ograniczenia zasobów piaskownicy.

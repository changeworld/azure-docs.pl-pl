---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: abe7668ed16ddd5cc1b247207d3e80f7c1118137
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964569"
---
| Zasób | Limit maksymalny |Uwagi|
| --- | --- |---|
| Maksymalna liczba nowych zadań, które mogą być przesyłane co 30 sekund na konto usługi Automation (innych niż zaplanowane zadania) |100 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
| Maksymalna liczba równoczesnych zadań uruchomionych na tym samym wystąpieniu czas na konto usługi Automation (innych niż zaplanowane zadania) |200 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
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

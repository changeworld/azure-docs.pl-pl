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
ms.openlocfilehash: b71e6d41dcdd7efb2d179486f9195c14dae97194
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037519"
---
| Zasób | Limit maksymalny |Uwagi|
| --- | --- |---|
| Maksymalna liczba nowych zadań, które mogą być przesyłane co 30 sekund na konto usługi Automation (innych niż zaplanowane zadania) |100 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
| Maksymalna liczba równoczesnych zadań uruchomionych na tym samym wystąpieniu czas na konto usługi Automation (innych niż zaplanowane zadania) |200 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
| Maksymalna liczba modułów, które można importować co 30 sekund na konto usługi Automation |5 ||
| Maksymalny rozmiar modułu |100 MB ||
| Czas wykonywania zadania — warstwa bezpłatna |500 minut na subskrypcję na miesiąc kalendarzowy ||
| Maksymalna ilość miejsca na dysku, które może istnieć piaskownicy**<sup>1</sup>** |1 GB |Dotyczy tylko piaskownic platformy Azure|
| Maksymalna ilość pamięci do piaskownicy  **<sup>1</sup>** |400 MB |Dotyczy tylko piaskownic platformy Azure|
| Maksymalna liczba gniazd sieciowych może istnieć piaskownicy  **<sup>1</sup>** |1000 |Dotyczy tylko piaskownic platformy Azure|
| Maksymalny czas wykonywania dozwolonych dla jednego elementu runbook  **<sup>1</sup>** |3 godziny |Dotyczy tylko piaskownic platformy Azure|
| Maksymalna liczba kont usługi Automation w ramach subskrypcji |Bez ograniczeń ||
|Maksymalna liczba równoczesnych zadań, które można uruchomić na jednym hybrydowego procesu roboczego elementu Runbook|50 ||

**<sup>1</sup>**  piaskownicy jest udostępnionym środowiskiem, które mogą być używane przez wiele zadań, zadań przy użyciu tego samego piaskownicy są powiązane przez ograniczenia zasobów piaskownicy.

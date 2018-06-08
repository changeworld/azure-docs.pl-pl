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
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852045"
---
| Zasób | Limit maksymalny |Uwagi|
| --- | --- |---|
| Maksymalna liczba nowe zadania, które można przesłać co 30 sekund na konto automatyzacji (z systemem innym niż harmonogram zadań) |100 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
| Maksymalna liczba jednoczesnych uruchomionych zadań na tym samym wystąpieniu czas na konto automatyzacji (z systemem innym niż harmonogram zadań) |200 |Ten limit jest trafień, kolejne żądania, aby utworzyć zadanie zakończyć się niepowodzeniem. Klient odbiera odpowiedź o błędzie.|
| Maksymalna liczba modułów, które można importować co 30 sekund na konto automatyzacji |5 ||
| Maksymalny rozmiar modułu |100 MB ||
| Czas uruchomienia zadania — warstwa bezpłatna |500 minut na subskrypcję na miesiąc kalendarzowy ||
| Maksymalna ilość wolnego miejsca dozwolony piaskownicy**<sup>1</sup>** |1 GB |Dotyczy tylko piaskownic Azure|
| Maksymalna ilość pamięci do piaskownicy**<sup>1</sup>** |400 MB |Dotyczy tylko piaskownic Azure|
| Maksymalna liczba dozwolonych dla jednej piaskownicy gniazda sieci**<sup>1</sup>** |1000 |Dotyczy tylko piaskownic Azure|
| Maksymalna liczba kont automatyzacji w ramach subskrypcji |Bez ograniczeń ||
|Maksymalna liczba równoczesnych zadań można uruchamiać na pojedynczego procesu roboczego elementu Runbook hybrydowego|50 ||

**<sup>1</sup>**  piaskownicy jest środowiskiem udostępnionych, które mogą być używane przez wiele zadań, zadań przy użyciu tego samego piaskownicy są powiązane przez ograniczenia zasobów piaskownicy.

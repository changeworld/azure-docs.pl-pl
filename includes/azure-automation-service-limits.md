---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334598"
---
#### <a name="process-automation"></a>Automatyzacja procesów

| Zasób | Limit |Uwagi|
| --- | --- |---|
| Maksymalna liczba nowych zadań, które można przesyłać co 30 sekund na konto usługi Azure Automation (zadania nieplanowane) |100 |Po osiągnięciu tego limitu kolejne żądania utworzenia zadania nie powiodą się. Klient odbiera odpowiedź na błąd.|
| Maksymalna liczba równoczesnych uruchomionych zadań w tym samym wystąpieniu czasu na konto automatyzacji (zadania nieplanowane) |200 |Po osiągnięciu tego limitu kolejne żądania utworzenia zadania nie powiodą się. Klient odbiera odpowiedź na błąd.|
| Maksymalny rozmiar metadanych zadania dla 30-dniowego okresu stopniowego | 10 GB (około 4 mln miejsc pracy)|Po osiągnięciu tego limitu kolejne żądania utworzenia zadania nie powiodą się. |
| Maksymalny limit strumienia zadań|1 MB|Pojedynczy strumień nie może być większy niż 1 MB.|
| Maksymalna liczba modułów, które można importować co 30 sekund na konto automatyzacji |5 ||
| Maksymalny rozmiar modułu |100 MB ||
| Czas wykonywania zadania, warstwa bezpłatna |500 minut na subskrypcję w miesiącu kalendarzowym ||
| Maksymalna ilość miejsca na dysku dozwolonym w piaskownicy<sup>1</sup> |1 GB |Dotyczy tylko skrzynek izolerów platformy Azure.|
| Maksymalna ilość pamięci przekazyty do piaskownicy<sup>1</sup> |400 MB |Dotyczy tylko skrzynek izolerów platformy Azure.|
| Maksymalna liczba gniazd sieciowych dozwolonych na piaskownicę<sup>1</sup> |1000 |Dotyczy tylko skrzynek izolerów platformy Azure.|
| Maksymalny czas wykonywania dozwolony na runbook<sup>1</sup> |3 godziny |Dotyczy tylko skrzynek izolerów platformy Azure.|
| Maksymalna liczba kont automatyzacji w ramach subskrypcji |Bez ograniczeń ||
| Maksymalna liczba hybrydowych grup roboczych na konto automatyzacji|4000||
|Maksymalna liczba równoczesnych zadań, które można uruchomić w jednym hybrydowym uliczce przewodowej|50 ||
| Maksymalny rozmiar parametru zadania umnienia   | 512 kilobitów||
| Maksymalne parametry umnienia ego   | 50|Po osiągnięciu limitu 50 parametrów można przekazać ciąg JSON lub XML do parametru i przeanalizować go z uruchomieniu. ścieleń.|
| Maksymalny rozmiar ładunku elementu webhook |  512 kilobitów|
| Maksymalna liczba dni przechowywania danych zadania|30 dni|
| Maksymalny rozmiar stanu przepływu pracy programu PowerShell |5 MB| Dotyczy śmięty przepływu pracy programu PowerShell podczas przepływu pracy w punktach kontrolnych.|

<sup>1.</sup> Piaskownica jest środowiskiem współużytkowanym, które może być używane przez wiele zadań. Zadania korzystające z tego samego piaskownicy są powiązane ograniczeniami zasobów w piaskownicy.

#### <a name="change-tracking-and-inventory"></a>Śledzenie zmian i spis

W poniższej tabeli przedstawiono limity śledzonych towarów na maszynę w celu śledzenia zmian.

| **Zasobów** | **Limit**| **Uwagi** |
|---|---|---|
|Plik|500||
|Rejestr|250||
|Oprogramowanie systemu Windows|250|Nie zawiera aktualizacji oprogramowania.|
|Pakiety Linuksa|1,250||
|Usługi|250||
|Daemon|250||

#### <a name="update-management"></a>Zarządzanie aktualizacjami

W poniższej tabeli przedstawiono limity zarządzania aktualizacjami.

| **Zasobów** | **Limit**| **Uwagi** |
|---|---|---|
|Liczba maszyn na wdrożenie aktualizacji|1000||
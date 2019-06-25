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
ms.openlocfilehash: 2823a33b25812a69ad463433bacd9710655c9176
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183638"
---
#### <a name="process-automation"></a>Automatyzacja procesów

| Resource | Limit maksymalny |Uwagi|
| --- | --- |---|
| Maksymalna liczba nowych zadań, które mogą być przesyłane co 30 sekund na konto usługi Azure Automation (nonscheduled zadań) |100 |Po osiągnięciu tego limitu, kolejne żądania, aby utworzyć zadanie się nie powieść. Klient odbiera odpowiedź o błędzie.|
| Maksymalna liczba równoczesnych zadań uruchomionych na tym samym wystąpieniu czas na konto usługi Automation (nonscheduled zadań) |200 |Po osiągnięciu tego limitu, kolejne żądania, aby utworzyć zadanie się nie powieść. Klient odbiera odpowiedź o błędzie.|
| Maksymalny rozmiar magazynu metadanych zadania dla 30-dniowego okresu kroczącego | 10 GB (około 4 mln zadań)|Po osiągnięciu tego limitu, kolejne żądania, aby utworzyć zadanie się nie powieść. |
| Osiągnięto limit maksymalny|1MB|Jeden strumień nie może być większa niż 1 MB.|
| Maksymalna liczba modułów, które można importować co 30 sekund na konto usługi Automation |5 ||
| Maksymalny rozmiar modułu |100 MB ||
| W warstwie bezpłatna czas uruchomienia zadania |500 minut na subskrypcję na miesiąc kalendarzowy ||
| Maksymalna ilość miejsca na dysku, może istnieć piaskownicy<sup>1</sup> |1 GB |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalna ilość pamięci do piaskownicy<sup>1</sup> |400 MB |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalna liczba gniazd sieciowych może istnieć piaskownicy<sup>1</sup> |1000 |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalny czas wykonywania dozwolonych dla jednego elementu runbook<sup>1</sup> |3 godziny |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalna liczba kont usługi Automation w ramach subskrypcji |Bez ograniczeń ||
| Maksymalna liczba grupy hybrydowych procesów roboczych na konto usługi Automation|4,000||
|Maksymalna liczba równoczesnych zadań, które mogą być uruchamiane na jednym hybrydowego procesu roboczego elementu Runbook|50 ||
| Rozmiar parametru zadania maksymalna elementu runbook   | wynosi 512 kilobitów||
| Parametry maksymalna elementu runbook   | 50|W przypadku osiągnięcia limitu 50 parametrów można przekazywać ciąg JSON lub XML do parametru i go przeanalizować przy użyciu elementu runbook.|
| Rozmiar ładunku maksymalna elementu webhook |  wynosi 512 kilobitów|
| Maksymalna liczba dni, które są przechowywane dane zadania|30 dni|
| Maksymalny rozmiar danych stanu przepływu pracy programu PowerShell |5 MB| Ma zastosowanie do elementów runbook przepływu pracy programu PowerShell podczas procesu tworzenia punktów kontrolnych w przepływie pracy.|

<sup>1</sup>piaskownicy jest udostępnionym środowiskiem, które mogą być używane przez wiele zadań. Ograniczenia zasobów piaskownicy związane są zadania korzystające z tego samego piaskownicy.

#### <a name="change-tracking-and-inventory"></a>Śledzenie zmian i spis

W poniższej tabeli przedstawiono limity śledzonych elementów jednej maszyny na potrzeby śledzenia zmian.

| **Zasób** | **Limit**| **Uwagi** |
|---|---|---|
|Plik|500||
|Rejestr|250||
|Oprogramowanie Windows|250|Nie zawiera aktualizacji oprogramowania.|
|Pakiety systemu Linux|1,250||
|Usługi|250||
|Demon|250||

#### <a name="update-management"></a>Zarządzanie aktualizacjami

W poniższej tabeli przedstawiono limity do zarządzania aktualizacjami.

| **Zasób** | **Limit**| **Uwagi** |
|---|---|---|
|Liczba maszyn na wdrożenie aktualizacji|1000||
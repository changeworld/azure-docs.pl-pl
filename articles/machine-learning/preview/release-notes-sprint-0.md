---
title: "Azure ML Workbench informacje o wersji dla przebiegu 0 2017 października"
description: "Ten dokument zawiera szczegóły aktualizacje dla wersji przebiegu 0 uczenie Maszynowe Azure"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>Przebieg 0 — październik 2017 

**Numer wersji: 0.1.1710.04013**

Witamy w pierwszej aktualizacji Azure Machine Learning środowiska roboczego po naszej początkowej publicznej wersji zapoznawczej na konferencji Ignite firmy Microsoft w 2017 r. Główne aktualizacje w tej wersji są niezawodność i stabilizacji poprawki.  Krytyczne problemy, które rozwiązaliśmy między innymi:

## <a name="new-features"></a>Nowe funkcje
- System macOS wysokiej Sierra obecnie jest obsługiwany.

## <a name="bug-fixes"></a>Poprawki błędów
### <a name="workbench-experience"></a>Środowisko robocze
- Przeciąganie i upuszczanie plików do narzędzia Workbench powoduje, że Workbench awarię.
- Okno terminalu w kodzie VS skonfigurowane jako IDE Workbench nie rozpoznaje _az ml_ poleceń.

### <a name="workbench-authentication"></a>Workbench uwierzytelniania
Wprowadziliśmy wiele aktualizacje do poprawy różnych uwierzytelniania logowania i problemach raportowanych.
- Okno uwierzytelniania zachowuje wyświetlanie w górę, zwłaszcza w przypadku połączenia internetowego nie jest stabilna.
- Problemy z większą niezawodność wokół wygaśnięcia tokenu uwierzytelniania.
- W niektórych przypadkach okno uwierzytelniania występuje dwa razy.
- Główne okno środowiska roboczego nadal wyświetla komunikat "uwierzytelnianie" po zakończeniu procesu uwierzytelniania zostanie faktycznie i wyświetlonym oknie dialogowym już odrzucono.
- W przypadku połączenia internetowego, dialog uwierzytelniania pojawia się pusty ekran.

### <a name="data-preparation"></a>Przygotowywanie danych 
- Jeśli określona wartość jest filtrowany, błędy i brakujące wartości są również odfiltrowane.
- Zmiana strategii próbkowania usuwa kolejnych istniejące operacje sprzężenia.
- Zastąpienie wartości Brak przekształcenie nie uwzględnia NaN.
- Wnioskowanie o typie data zgłasza wyjątek w przypadku wartości null.

### <a name="job-execution"></a>Wykonanie zadania
- Nie ma wyczyść błędu podczas wykonywania zadania nie powiedzie się załadować folderu projektu, ponieważ został przekroczony limit rozmiaru.
- Jeśli skrypt w języku Python użytkownika zmieni się katalog roboczy, pliki zapisywane do folderów dane wyjściowe nie są śledzone. 
- Jeśli aktywną subskrypcją platformy Azure jest inna niż ten, który należy do bieżącego projektu, przesyłanie zadań powoduje błąd 403.
- Gdy Docker nie jest obecny, żaden komunikat wyczyść błąd jest zwracany, gdy użytkownik próbuje użyć Docker jako obiekt docelowy wykonywania.
- plik .runconfig nie jest zapisywany automatycznie, gdy użytkownik kliknie _Uruchom_ przycisku.

### <a name="jupyter-notebook"></a>Jupyter Notebook
- Nie można uruchomić serwera notesu, jeśli użytkownik korzysta z określonymi typami logowania.
- Komunikaty o błędach notesu serwera nie powierzchni w dziennikach widoczny dla użytkownika.

### <a name="azure-portal"></a>Azure Portal
- Zaznaczenie ciemnym motywem portalu Azure powoduje, że blok zarządzania modelu, który będzie wyświetlany jako czarne pole.

### <a name="operationalization"></a>Operationalization
- Ponowne wykorzystywanie manifestu do aktualizowania usługi sieci web powoduje, że nowy obraz Docker wbudowane losowe nazwą.
- Dzienniki usługi sieci Web nie można pobrać z Kubernetes klastra.
- Błąd komunikat o błędzie jest drukowany, gdy użytkownik próbuje utworzyć konto zarządzania modelu lub konto obliczeniowe ML i napotkał problemy z uprawnieniami.

---
title: Kreator usługi Azure Machine Learning źródła danych platformy Azure | Dokumentacja firmy Microsoft
description: Wyjaśnia, w Kreatorze źródła danych z aplikacji workbench AML
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a3aeb021d5d3726a31b62d66efc44dc29f3dded0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959652"
---
# <a name="data-source-wizard"></a>Kreator źródła danych #

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



W Kreatorze źródła danych jest szybki i łatwy sposób wprowadzenia zestawu danych w aplikacji Azure ML Workbench, bez konieczności pisania kodu. To, gdzie możesz również wybrać strategię Przykładowy zestaw danych i typy danych dla każdej kolumny. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Krok 1: Wyzwalacza w Kreatorze źródła danych ## 

Aby przenieść dane do projektu przy użyciu Kreatora źródła danych. Wybierz **+** znajdujący się obok pola wyszukiwania w widoku danych i wybierz polecenie Dodaj źródło danych. 

![Dodawanie źródła danych](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Krok 2: Wybierz, w którym są przechowywane dane ##
Najpierw określ, jak Twoje dane są obecnie w. Mogą być przechowywane w pliku prostego lub katalogu, pliku parquet, pliku programu Excel lub bazy danych. Aby uzyskać więcej informacji, zobacz [obsługiwanych źródeł danych](data-prep-appendix2-supported-data-sources.md).

![Krok 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Krok 3: Wybierz plik danych ##
Dla pliku lub katalogu Określ ścieżkę pliku. Wybierz z listy rozwijanej lokalizacji danych — może to być ścieżka do pliku lokalnego lub usługi Azure Blob Storage. 

Wpisz ścieżkę, wpisując ją lub klikając **Przeglądaj...** przycisk, aby przejść do niego. Można przeglądać w poszukiwaniu katalogu lub jeden lub więcej plików.

Kliknij przycisk **Zakończ** Zaakceptuj ustawienia domyślne dla pozostałych kroków lub następnie przejść do następnego kroku.


![Krok 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Krok 4: Wybór parametrów w pliku ##

W Kreatorze źródła danych może automatycznie wykrywać pliku typu, separatory i kodowania. Spowoduje to pokazanie również przykładowy wygląd danych. Można również zmienić dowolne z tych parametrów ręcznie. 

![Krok 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Krok 5: Ustawianie typu danych dla kolumn ##

W Kreatorze źródła danych automatycznie wykrywa typy danych kolumn typizowanego zestawu danych. Chybień on jeden lub chcesz wymusić typ danych, typ danych można zmienić ręcznie. **Przykładowe dane wyjściowe** kolumna zawiera przykłady wyglądać dane.

Dla kolumn, które wnioskuje przygotowywanie danych, aby zawierała daty może być prośba o wybierz kolejność miesiąc i dzień w formacie daty. Na przykład, może reprezentować 2 stycznia 2013-1/2 (w tym celu wybierz *dnia miesiąca*) lub 1 lutego (wybierz *dnia miesiąca*).

![Krok 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Krok 6: Wybierz strategię próbkowanie danych ##

Można określić co najmniej jeden strategii próbkowania dla zestawu danych i wybierz jedną z nich jako aktywnej strategii. Wartość domyślna to załadować wierszy Top 10000. W tym przykładzie można edytować, klikając **Edytuj** przycisk na pasku narzędzi lub dodawania nowej strategii przez kliknięcie + nowy. Strategie, które są aktualnie obsługuje są

-     Liczbę pierwszych wierszy
-     Liczba wierszy
-     Losowe procent wierszy
-     Cały plik

Można określić dowolną liczbę strategie, ale jest tylko jedna, która może być ustawiona jako aktywna podczas przygotowywania danych. Można ustawić każdej strategii jako aktywna przez wybranie strategii i kliknąć pozycję Ustaw jako aktywny, na pasku narzędzi.

W zależności od tego, skąd pochodzą dane kilka przykładowych strategii mogą nie być obsługiwane. Aby uzyskać więcej informacji na temat próbkowania, Przyjrzyj się z sekcją próbkowania w [tego dokumentu](data-prep-user-guide.md) 

![Krok 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Krok 7: Obsługa kolumny ścieżki ##

Ścieżka pliku zawiera ważne dane, można dołączyć ją jako pierwszej kolumny w zestawie danych. Ta opcja może być przydatny, jeśli także w wielu plikach. W przeciwnym razie można dołączaj go.

![Krok 7](media/data-source-wizard/step6.png)

Po kliknięciu przycisku Zakończ, nowe źródło danych zostanie dodany do projektu. Możesz go znaleźć w ramach grupy źródeł danych w widoku danych lub jako plik dsource w **widok pliku**.

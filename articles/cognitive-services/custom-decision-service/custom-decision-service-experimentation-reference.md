---
title: Eksperymentowanie w usłudze — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Ten artykuł to podręcznik eksperymentów przy użyciu usługi Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b5f8c853218a1db53f4dd23e7254b35990a7132b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829178"
---
# <a name="experimentation"></a>Eksperymentowanie

Po teorii [kontekstowych bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), Custom Decision Service wielokrotnie przestrzega kontekst, wykonują akcję i przestrzega nagradzania dla wybranej akcji. Przykładem jest personalizacja zawartości możliwej do: kontekst w tym artykule opisano użytkownika akcje są scenariusze Release candidate i nagrody mierzy użytkownikami zbędne zalecane wątku.

Usługa Custom Decision Service tworzy zasady, ponieważ jest on mapowany w kontekstach akcji. Za pomocą zasad określony element docelowy chcesz wiedzieć jego oczekiwanego nagrody. Jednym ze sposobów, aby oszacować nagrody jest online przy użyciu zasad i pozwól mu wybierz akcje (na przykład, zaleca się wątków dla użytkowników). Jednak takie online oceny może być kosztowne, dwóch powodów:

* Udostępnia ona użytkownikom nieprzetestowanych zasad eksperymentalne.
* Go nie przeprowadzać skalowanie do obliczenia wielu zasad docelowej.

Wyłącz zasady oceny jest paradygmat alternatywne. W przypadku dzienników z istniejącego systemu online, które obowiązują zasady rejestrowania oceny wyłączanie zasad można oszacować oczekiwane korzyści wynikające z nowych zasad docelowej.

Za pomocą pliku dziennika, eksperymentowanie ma na celu znaleźć zasady korzystając z najwyższej nagradzania szacowany, oczekiwano. Określać zasady są sparametryzowany przez [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumentów. W domyślnym trybie skryptu testów przez dołączenie do różnych argumenty Vowpal Wabbit `--base_command`. Skrypt wykonuje następujące czynności:

* Automatycznie wykryje, funkcje przestrzeni nazw z pierwszego `--auto_lines` wiersze z pliku wejściowego.
* Wykonuje pierwszy czyszczenia hiperparametrów (`learning rate`, `L1 regularization`, i `power_t`).
* Testuje oceny zasad `--cb_type` (odwrotność tendencje wynik (`ips`) lub podwójnie niezawodne (`dr`). Aby uzyskać więcej informacji, zobacz [przykład kontekstowych Bandit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testy notatek.
* Funkcje interakcji drugiego stopnia testów:
   * **Faza atak metodą siłową**: Sprawdza wszystkie kombinacje z `--q_bruteforce_terms` pary lub mniej.
   * **Faza zachłanne**: Dodaje parę najlepsze, dopóki nie istnieje żadne improvement dla `--q_greedy_stop` zaokrągla.
* Wykonuje drugi czyszczenia hiperparametrów (`learning rate`, `L1 regularization`, i `power_t`).

Parametry sterujące te kroki obejmują niektóre argumenty Vowpal Wabbit:
- Przykład manipulowania opcje:
  - przestrzenie nazw udostępnionych
  - przestrzenie nazw akcji
  - przestrzenie nazw brzegowych
  - Funkcje drugiego stopnia
- Opcje regułę aktualizacji
  - tempo uczenia
  - Uregulowania L1
  - wartość typu t. zasilania

Szczegółowe objaśnienia dotyczące powyższych argumentów, zobacz [argumenty wiersza polecenia Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Wymagania wstępne
- Vowpal Wabbit: Zainstalowana i w zmiennej path.
  - W systemie Windows: [Użyj `.msi` Instalatora](https://github.com/eisber/vowpal_wabbit/releases).
  - Inne platformy: [Uzyskaj kod źródłowy](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Zainstalowana i w zmiennej path.
- NumPy: Użyj Menedżera pakietów wybranych przez użytkownika.
- *O Microsoft-ds* repozytorium: [Sklonuj repozytorium](https://github.com/Microsoft/mwt-ds).
- Plik dziennika JSON usługi decyzji: Domyślnie polecenie podstawowy zawiera `--dsjson`, które umożliwia analizowanie JSON usługi decyzji w pliku danych wejściowych. [Pobierz przykład ten format](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Sposób użycia
Przejdź do `mwt-ds/DataScience` i uruchom `Experimentation.py` z odpowiednimi argumentami, zgodnie z opisem w poniższym kodzie:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Dziennik wyników jest dołączana do *mwt-ds/DataScience/experiments.csv* pliku.

### <a name="parameters"></a>Parametry
| Dane wejściowe | Opis | Domyślne |
| --- | --- | --- |
| `-h`, `--help` | Pokaż komunikat pomocy i zakończenia. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Ścieżka pliku danych (`.json` lub `.json.gz` format — każdy wiersz jest `dsjson`). | Wymagane |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Podstawowy polecenie Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Liczba równoległych procesów do użycia. | Procesory logiczne |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Udostępnione funkcji w przestrzeni nazw (na przykład `abc` oznacza, że przestrzenie nazw `a`, `b`, i `c`).  | Automatyczne wykrywanie z pliku danych |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Przestrzenie nazw funkcji akcji. | Automatyczne wykrywanie z pliku danych |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Przestrzenie nazw funkcji brzegowych. | Automatyczne wykrywanie z pliku danych |  
| `--auto_lines AUTO_LINES` | Liczba wierszy pliku danych można przeskanować Autowykrywanie funkcje przestrzeni nazw. | `100` |  
| `--only_hp` | Odchylenia tylko za pośrednictwem hiper parametrami (`learning rate`, `L1 regularization`, i `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Learning współczynnik zakresu jako wartości dodatnich `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Zakres uregulowania L1 jako wartości dodatnich `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Zakres Power_t jako wartości dodatnich `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Liczba par drugiego stopnia do testowania w fazie atak metodą siłową. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Zaokrągla liczbę bez ulepszenia, po upływie których fazy drugiego stopnia zachłanne wyszukiwania jest zatrzymywana. | `3` |  

### <a name="examples"></a>Przykłady
Aby użyć wartości domyślnych wstępnie zdefiniowane:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Ekwiwalentnie Vowpal Wabbit może również obsługiwać `.json.gz` plików:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Aby odchylenia tylko za pośrednictwem hiper parametrami (`learning rate`, `L1 regularization`, i `power_t`, zatrzymywanie po wykonaniu kroku 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```

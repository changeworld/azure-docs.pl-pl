---
title: Eksperymentowanie — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Ten artykuł zawiera Przewodnik dotyczący eksperymentów z Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: e6e8e7d0d5b969464ba9183ccae9080f58f786a0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707283"
---
# <a name="experimentation"></a>Eksperymentowanie

Po teorii [Bandits kontekstowych (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/)Custom Decision Service wielokrotnie obserwuje kontekst, podejmuje akcję i obserwuje wynagrodzenie dla wybranej akcji. Przykładem jest Personalizacja zawartości: kontekst opisuje użytkownika, akcje są wątkiem kandydata, a nagroda wskazuje, jak dużo użytkownik polubił ten scenariusz.

Custom Decision Service tworzy zasady, ponieważ mapuje z kontekstów do akcji. Z określonymi zasadami docelowymi chcesz znać oczekiwane wynagrodzenie. Jednym ze sposobów oszacowania nagrody jest użycie zasad online i wybranie akcji (na przykład zalecane historie dla użytkowników). Jednak Ocena w trybie online może być kosztowna z dwóch powodów:

* Udostępnia ona użytkownikom nietestowe, eksperymentalne zasady.
* Nie skaluje się do oceny wielu zasad docelowych.

Obliczanie poza zasadami jest alternatywnym modelem. Jeśli masz dzienniki z istniejącego systemu online, który przestrzega zasad rejestrowania, szacowanie zasad może oszacować oczekiwane korzyści wynikające z nowych zasad docelowych.

Korzystając z pliku dziennika, eksperymentowanie szuka zasad o najwyższej szacowanej, oczekiwanym stopniu. Zasady docelowe są sparametryzowane przez argumenty [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) . W trybie domyślnym skrypt testuje różne argumenty Vowpal Wabbit przez dołączenie do `--base_command`. Skrypt wykonuje następujące czynności:

* Funkcja autowykrywania przestrzeni nazw funkcji z pierwszego `--auto_lines` wiersza pliku wejściowego.
* Wykonuje pierwsze odchylenia przez parametry funkcji Hyper-`learning rate`Parameters `L1 regularization`(, `power_t`i).
* Testuje ocenę `--cb_type` zasad (odwrotny wynik (`ips`) lub podwójnie niezawodny (`dr`). Aby uzyskać więcej informacji, zobacz [przykład kontekstowy Bandit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testuje marginesy.
* Testuje funkcje interakcji kwadratowej:
   * **faza**wymuszania rozłożenia: Testuje wszystkie kombinacje z `--q_bruteforce_terms` parami lub mniej.
   * **faza zachłanne**: Dodaje najlepszą parę do momentu, gdy nie będzie `--q_greedy_stop` żadnych ulepszeń w przypadku zaokrąglania.
* Wykonuje drugie wyczyszczenie parametrów funkcji Hyper-Parameters`learning rate`( `L1 regularization`, i `power_t`).

Parametry kontrolujące te kroki zawierają kilka argumentów Vowpal Wabbit:
- Przykładowe opcje manipulowania:
  - udostępnione przestrzenie nazw
  - obszary nazw akcji
  - marginalne przestrzenie nazw
  - Funkcje kwadratowe
- Aktualizowanie opcji reguł
  - stawka szkoleniowa
  - Uregulowanie L1
  - wartość t

Szczegółowe wyjaśnienie powyższych argumentów można znaleźć w temacie [Vowpal Wabbit argumenty wiersza polecenia](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Wymagania wstępne
- Vowpal Wabbit: Zainstalowane i na ścieżce.
  - W systemie Windows: [Użyj Instalatora`.msi` ](https://github.com/eisber/vowpal_wabbit/releases).
  - Inne platformy: [Pobierz kod źródłowy](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Zainstalowane i na ścieżce.
- NumPy: Użyj wybranego Menedżera pakietów.
- Repozytorium *Microsoft/MWT-ds* : [Klonowanie repozytorium](https://github.com/Microsoft/mwt-ds).
- Plik dziennika JSON usługi decyzyjnej: Domyślnie polecenie podstawowe obejmuje `--dsjson`, co umożliwia analizę JSON usługi decyzyjnej pliku danych wejściowych. [Zapoznaj się z przykładem tego formatu](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Użycie
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

Dziennik wyników jest dołączany do pliku *CSV MWT-ds/datascience/eksperymenty.*

### <a name="parameters"></a>Parametry
| Dane wejściowe | Opis | Domyślny |
| --- | --- | --- |
| `-h`, `--help` | Pokaż komunikat pomocy i Zakończ. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Ścieżka pliku danych (`.json` lub `.json.gz` format — każdy wiersz to a `dsjson`). | Wymagane |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Podstawowe polecenie Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Liczba procesów równoległych do użycia. | Procesory logiczne |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Przestrzenie nazw funkcji udostępnionych (na `abc` przykład, `a`oznacza przestrzenie `c`nazw, `b`i).  | Automatyczne wykrywanie z pliku danych |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Przestrzenie nazw funkcji akcji. | Automatyczne wykrywanie z pliku danych |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Obszary nazw funkcji krańcowej. | Automatyczne wykrywanie z pliku danych |  
| `--auto_lines AUTO_LINES` | Liczba wierszy plików danych do skanowania w przestrzeni nazw funkcji automatycznego wykrywania. | `100` |  
| `--only_hp` | Odchylenia tylko za pośrednictwem parametrów`learning rate`funkcji `L1 regularization`Hyper- `power_t`Parameters (, i). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Zakres uczenia jako wartości `min,max,steps`dodatnie. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Zakres uregulowania L1 jako wartości `min,max,steps`dodatnie. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Power_t zakres jako wartości `min,max,step`dodatnie. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Liczba par kwadratów do przetestowania w fazie w ramach siły częściowej. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Zaokrągla bez ulepszeń, po zatrzymaniu fazy wyszukiwania zachłanne kwadratów. | `3` |  

### <a name="examples"></a>Przykłady
Aby użyć wstępnie ustawionych wartości domyślnych:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Podobnie, Vowpal Wabbit może również `.json.gz` przyjmować pliki:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Aby wyrównać tylko parametry funkcji Hyper`learning rate`- `L1 regularization`Parameters ( `power_t`,, i, zatrzymywanie po kroku 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```

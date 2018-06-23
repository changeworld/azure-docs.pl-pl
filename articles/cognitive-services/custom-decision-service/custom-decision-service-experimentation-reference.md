---
title: Eksperymenty - kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule jest przewodnikiem podczas eksperymenty Azure niestandardowe decyzji usługi.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b0ac0bc049d556423493f0c48dd9a548929bcd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349253"
---
# <a name="experimentation"></a>Eksperymentowanie

Następujące teorii [bandits kontekstowych (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), niestandardowe decyzji usługi wielokrotnie przestrzega kontekst, podejmuje działania i przestrzega wynagrodzenia dla wybranej akcji. Przykładem jest personalizacji zawartości: kontekst opisuje użytkownika, akcje są wątki kandydata i nagrody mierzy, ile użytkownik zbędne zalecane wątku.

Usługa niestandardowa decyzji tworzy zasady, ponieważ mapuje on z kontekstami do akcji. Za pomocą zasad określony element docelowy zapoznać się jego oczekiwanego osób trzecich. Jednym ze sposobów oszacować nagrody jest używanie zasad do trybu online i pozwól mu wybierz akcje (na przykład zalecamy wątków dla użytkowników). Jednak takie oceny online może być kosztowne dwóch powodów:

* Prezentuje ona użytkownikom zastosowaniem, eksperymentalny zasad.
* Nie go skalować do obliczenia wiele zasad docelowych.

Ocena wyłączanie zasad jest alternatywnych modelu. Jeśli masz dzienniki z istniejącego systemu online, które należy wykonać zasad rejestrowania oceny wyłączanie zasad można oszacować oczekiwane korzyści wynikające z nowych zasad docelowych.

Przy użyciu pliku dziennika, próbuje odnaleźć zasad o najwyższym nagrody szacowany, oczekiwano eksperymenty. Zasady docelowego mają zdefiniowane przez [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumentów. W domyślnym trybie skrypt testów przez dołączenie do szerokiej gamy argumenty Vowpal Wabbit `--base_command`. Skrypt wykonuje następujące czynności:

* Wykrywa automatycznie funkcji przestrzeni nazw z pierwszego `--auto_lines` wiersze pliku wejściowego.
* Wykonuje pierwszy odchylenia za pośrednictwem parametrów funkcji hyper (`learning rate`, `L1 regularization`, i `power_t`).
* Testy oceny zasad `--cb_type` (wynik odwrotny większego ukierunkowania (`ips`) lub podwójnie niezawodne (`dr`). Aby uzyskać więcej informacji, zobacz [przykład kontekstowe Bandit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testy notatek.
* Funkcje interakcji kwadratową testów:
   * **Faza siłowych**: testów wszystkich kombinacji z `--q_bruteforce_terms` pary lub mniej.
   * **Faza zachłanne**: Dodaje parę najlepsze, dopóki nie istnieje żadne poprawy w przypadku `--q_greedy_stop` zaokrągla.
* Wykonuje drugi odchylenia za pośrednictwem funkcji hyper parametry (`learning rate`, `L1 regularization`, i `power_t`).

Parametry, które kontrolują następujące kroki obejmują niektóre argumenty Vowpal Wabbit:
- Przykład manipulowania opcje:
  - współużytkowanych obszarów nazw
  - przestrzenie nazw akcji
  - brzegowego przestrzenie nazw
  - Funkcje kwadratową
- Opcje regułę aktualizacji
  - tempo uczenia
  - Uregulowania L1
  - wartość t zasilania

Aby uzyskać szczegółowe wyjaśnienie powyższych argumentów, zobacz [argumenty wiersza polecenia Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Wymagania wstępne
- Vowpal Wabbit: Zainstalowane i ścieżki.
  - System Windows: [użyj `.msi` Instalatora](https://github.com/eisber/vowpal_wabbit/releases).
  - Innych platform: [pobrać kodu źródłowego](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Zainstalowane i ścieżki.
- NumPy: Użyj Menedżera pakietów wybranych przez użytkownika.
- *O Microsoft-ds* repozytorium: [sklonować repozytorium](https://github.com/Microsoft/mwt-ds).
- Plik dziennika usługi JSON decyzji: domyślnie zawiera podstawowe polecenia `--dsjson`, które umożliwia analizowanie pliku danych wejściowych JSON usługi decyzji. [Pobierz przykład tego formatu](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Sposób użycia
Przejdź do `mwt-ds/DataScience` i uruchom `Experimentation.py` z odpowiednich argumentów, zgodnie z opisem w poniższym kodzie:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Dziennik wyników jest dołączany do *mwt-ds/DataScience/experiments.csv* pliku.

### <a name="parameters"></a>Parametry
| Dane wejściowe | Opis | Domyślne |
| --- | --- | --- |
| `-h`, `--help` | Pokaż komunikat pomocy i Zakończ. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Ścieżka pliku danych (`.json` lub `.json.gz` format — każdy wiersz jest `dsjson`). | Wymagane |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Podstawowe polecenie Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Liczba równoległych procesów do użycia. | Procesory logiczne |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Udostępnione przestrzenie nazw funkcji (na przykład `abc` oznacza przestrzenie nazw `a`, `b`, i `c`).  | Autowykrywanie z pliku danych |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Przestrzenie nazw funkcji akcji. | Autowykrywanie z pliku danych |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Przestrzenie nazw funkcji brzegowych. | Autowykrywanie z pliku danych |  
| `--auto_lines AUTO_LINES` | Liczba wierszy pliku danych do skanowania automatycznie wykrywa funkcje przestrzenie nazw. | `100` |  
| `--only_hp` | Odchylenia tylko za pośrednictwem funkcji hyper parametry (`learning rate`, `L1 regularization`, i `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Learning zakresu szybkość jako wartości dodatnie `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Zakres uregulowania L1 jako wartości dodatnie `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Zakres Power_t jako wartości dodatnie `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Liczba par kwadratową do testowania w fazie metodą siłową. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Zaokrągla liczbę bez ulepszenia, po upływie którego jest zatrzymywane fazy kwadratową intensywnie wyszukiwania. | `3` |  

### <a name="examples"></a>Przykłady
Aby użyć wartości domyślnych wstępnie zdefiniowane:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Ekwiwalentnie Vowpal Wabbit można również pozyskiwania `.json.gz` plików:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Aby odchylenia tylko za pośrednictwem funkcji hyper parametry (`learning rate`, `L1 regularization`, i `power_t`, zatrzymywanie po wykonaniu kroku 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```

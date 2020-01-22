---
title: Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Debuguj i rozwiązywanie problemów z potokami uczenia maszynowego w zestawie Azure Machine Learning SDK dla języka Python. Poznaj typowe pułapeky dla tworzenia potoków oraz porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: 991f7ebf51be5f805a8b12fa0af0fefeff0ef582
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309561"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z [potokami uczenia maszynowego](concept-ml-pipelines.md) w [zestawach SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) i [Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer).


## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Debugowanie i rozwiązywanie problemów w zestawie Azure Machine Learning SDK
Poniższe sekcje zawierają omówienie typowych pułapek podczas kompilowania potoków oraz różnych strategii debugowania kodu działającego w potoku. Jeśli masz problemy z uruchamianiem potoku zgodnie z oczekiwaniami, Skorzystaj z poniższych wskazówek. 
### <a name="testing-scripts-locally"></a>Lokalne testowanie skryptów

Jeden z najczęstszych błędów w potoku polega na tym, że dołączony skrypt (skrypt czyszczący dane, skrypt oceniania itp.) nie jest uruchomiony zgodnie z oczekiwaniami lub zawiera błędy środowiska uruchomieniowego w zdalnym kontekście obliczeniowym, które są trudne do debugowania w obszarze roboczym na maszynie na platformie Azure Learning Studio. 

Potoki same nie mogą być uruchamiane lokalnie, ale uruchamianie skryptów w izolacji na komputerze lokalnym pozwala na szybsze debugowanie, ponieważ nie trzeba czekać na proces tworzenia i kompilowania środowiska. Aby to zrobić, należy wykonać następujące czynności:

* Jeśli dane są w magazynie danych w chmurze, musisz pobrać dane i udostępnić je dla skryptu. Użycie małego przykładu danych jest dobrym sposobem na wycinanie w czasie wykonywania i szybkie uzyskanie opinii na temat zachowania skryptu
* Jeśli podjęto próbę symulowania pośredniego etapu potoku, może być konieczne ręczne skompilowanie typów obiektów, których konkretny skrypt oczekuje od poprzedniego kroku
* Należy również zdefiniować własne środowisko i zreplikować zależności zdefiniowane w zdalnym środowisku obliczeniowym

Po skonfigurowaniu skryptu do uruchomienia w środowisku lokalnym można znacznie łatwiej wykonywać zadania debugowania, takie jak:

* Dołączanie konfiguracji debugowania niestandardowego
* Wstrzymywanie wykonywania i sprawdzania stanu obiektu
* Typ przechwytywania lub błędy logiczne, które nie zostaną ujawnione do czasu wykonania

> [!TIP] 
> Po sprawdzeniu, czy skrypt działa zgodnie z oczekiwaniami, dobrym następnym krokiem jest uruchomienie skryptu w potoku jednoetapowym przed podjęciem próby uruchomienia go w potoku z wieloma krokami.

### <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Testowanie skryptów lokalnie to doskonały sposób na Debugowanie głównych fragmentów kodu i złożonej logiki przed rozpoczęciem tworzenia potoku, ale w pewnym momencie prawdopodobnie trzeba będzie debugować skrypty podczas rzeczywistego uruchomienia potoku, szczególnie podczas diagnozowania zachowania podczas interakcji między krokami potoku. Zalecamy korzystanie z niezliberalizowanych instrukcji `print()` w skryptach kroków, dzięki czemu można zobaczyć stan obiektu i oczekiwane wartości podczas wykonywania zdalnego, podobnie jak w przypadku debugowania kodu JavaScript.

Plik dziennika `70_driver_log.txt` zawiera: 

* Wszystkie wydrukowane instrukcje podczas wykonywania skryptu
* Ślad stosu dla skryptu 

Aby znaleźć te i inne pliki dziennika w portalu, najpierw kliknij potok uruchomiony w obszarze roboczym.

![Strona listy uruchomień potoku](./media/how-to-debug-pipelines/pipelinerun-01.png)

Przejdź do strony szczegółów uruchomienia potoku.

![Strona szczegółów uruchomienia potoku](./media/how-to-debug-pipelines/pipelinerun-02.png)

Kliknij moduł dla określonego kroku. Przejdź do karty **dzienniki** . Inne dzienniki zawierają informacje o procesie kompilacji obrazu środowiska i skryptach przygotowania krokowego.

![Karta dziennika strony szczegółów uruchomienia potoku](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Przebiegi *opublikowanych potoków* można znaleźć na karcie **punkty końcowe** w obszarze roboczym. Przebiegi dla *nieopublikowanych potoków* można znaleźć w **eksperymentach** lub **potokach**.

### <a name="troubleshooting-tips"></a>Porady dotyczące rozwiązywania problemów

Poniższa tabela zawiera typowe problemy podczas tworzenia potoku z potencjalnymi rozwiązaniami.

| Problem | Możliwe rozwiązanie |
|--|--|
| Nie można przekazać danych do katalogu `PipelineData` | Upewnij się, że utworzono katalog w skrypcie, który odnosi się do miejsca, w którym potok oczekuje danych wyjściowych kroku. W większości przypadków argument wejściowy określi katalog wyjściowy, a następnie utworzysz katalog jawnie. Użyj `os.makedirs(args.output_dir, exist_ok=True)`, aby utworzyć katalog wyjściowy. Zapoznaj się z [samouczkiem](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) przykładowego skryptu oceniania, który pokazuje ten Wzorzec projektowy. |
| Błędy zależności | Jeśli lokalnie opracowano i przetestowano skrypty, ale występują problemy z zależnościami podczas uruchamiania na zdalnym obliczeniu w potoku, upewnij się, że zależności środowiska obliczeniowego i wersje są zgodne ze środowiskiem testowym. |
| Niejednoznaczne błędy z obiektami docelowymi obliczeń | Usunięcie i ponowne utworzenie obiektów docelowych obliczeń może rozwiązać pewne problemy związane z obiektami docelowymi obliczeń. |
| Potok nie wykorzystał się z kroków | Ponowne użycie kroku jest włączone domyślnie, ale upewnij się, że nie zostało wyłączone w kroku potoku. Jeśli ponowne użycie jest wyłączone, parametr `allow_reuse` w kroku zostanie ustawiony na `False`. |
| Potok jest niepotrzebny. | Aby zapewnić, że kroki mają zostać uruchomione ponownie tylko wtedy, gdy ich dane podstawowe lub skrypty zmienią się, należy rozdzielić katalogi dla każdego kroku. Jeśli używasz tego samego katalogu źródłowego dla wielu kroków, może wystąpić niepotrzebne wykonanie ponownie. Użyj parametru `source_directory` w obiekcie kroku potoku, aby wskazać odizolowany katalog dla tego kroku i upewnić się, że nie używasz tej samej ścieżki `source_directory` dla wielu kroków. |

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer"></a>Debugowanie i rozwiązywanie problemów w programie Azure Machine Learning Designer

Ta sekcja zawiera omówienie sposobu rozwiązywania problemów z potokami w projektancie.
W przypadku potoków utworzonych w projektancie można znaleźć **pliki dziennika** na stronie Tworzenie lub na stronie szczegółów uruchomienia potoku.

### <a name="access-logs-from-the-authoring-page"></a>Dostęp do dzienników ze strony tworzenia

Po przesłaniu uruchomienia potoku i pozostawania na stronie Tworzenie można znaleźć pliki dziennika wygenerowane dla każdego modułu.

1. Wybierz dowolny moduł na kanwie tworzenia.
1. W okienku właściwości przejdź do karty **dzienniki** .
1. Wybierz plik dziennika `70_driver_log.txt`

    ![Tworzenie dzienników modułu strony](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Dostęp do dzienników z przebiegów potoku

Pliki dziennika określonych przebiegów można również znaleźć na stronie szczegółów uruchomienia potoku w sekcjach **potoków** lub **eksperymentów** .

1. Wybierz uruchomienie potoku utworzone w projektancie.
    ![strony uruchomienia potoku](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Wybierz dowolny moduł w okienku podglądu.
1. W okienku właściwości przejdź do karty **dzienniki** .
1. Wybierz plik dziennika `70_driver_log.txt`

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) oraz pakietem [kroków dla potoków usługi Azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Zapoznaj się z listą [wyjątków projektanta i kodów błędów](algorithm-module-reference/designer-error-codes.md).

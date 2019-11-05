---
title: Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Debuguj i rozwiązywanie problemów z potokami uczenia maszynowego w zestawie Azure Machine Learning SDK dla języka Python. Poznaj typowe pułapeky dla tworzenia potoków oraz porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: fc19e864f00489d3ebc0162705af864785af0811
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497064"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera informacje na temat debugowania i rozwiązywania problemów z [potokami uczenia maszynowego](concept-ml-pipelines.md) w [zestawie Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Poniższe sekcje zawierają omówienie typowych pułapek podczas kompilowania potoków oraz różnych strategii debugowania kodu działającego w potoku. Jeśli masz problemy z uruchamianiem potoku zgodnie z oczekiwaniami, Skorzystaj z poniższych wskazówek. 

## <a name="testing-scripts-locally"></a>Lokalne testowanie skryptów

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

## <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Testowanie skryptów lokalnie to doskonały sposób na Debugowanie głównych fragmentów kodu i złożonej logiki przed rozpoczęciem tworzenia potoku, ale w pewnym momencie prawdopodobnie trzeba będzie debugować skrypty podczas rzeczywistego uruchomienia potoku, szczególnie podczas diagnozowania zachowania podczas interakcji między krokami potoku. Zalecamy korzystanie z niezliberalizowanych instrukcji `print()` w skryptach kroków, dzięki czemu można zobaczyć stan obiektu i oczekiwane wartości podczas wykonywania zdalnego, podobnie jak w przypadku debugowania kodu JavaScript.

Plik dziennika `70_driver_log.txt` zawiera: 

* Wszystkie wydrukowane instrukcje podczas wykonywania skryptu
* Ślad stosu dla skryptu 

Aby znaleźć te i inne pliki dziennika w portalu, najpierw kliknij potok w obszarze roboczym.

![Strona potoki w portalu](./media/how-to-debug-pipelines/pipeline-1.png)

Przejdź do przebiegu nadrzędnego potoku.

![Przebiegi nadrzędne potoki](./media/how-to-debug-pipelines/pipeline-2.png)

Kliknij identyfikator przebiegu dla określonego kroku.

![Strona potoki w portalu](./media/how-to-debug-pipelines/pipeline-3.png)

Przejdź do karty **dzienniki** . Inne dzienniki zawierają informacje o procesie kompilacji obrazu środowiska i skryptach przygotowania krokowego.

![Strona potoki w portalu](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Przebiegi *opublikowanych potoków* można znaleźć na karcie **potoki** w obszarze roboczym w portalu. Przebiegi dla *nieopublikowanych potoków* można znaleźć w **eksperymentach**.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Poniższa tabela zawiera typowe problemy podczas tworzenia potoku z potencjalnymi rozwiązaniami.

| Problem | Możliwe rozwiązanie |
|--|--|
| Nie można przekazać danych do katalogu `PipelineData` | Upewnij się, że utworzono katalog w skrypcie, który odnosi się do miejsca, w którym potok oczekuje danych wyjściowych kroku. W większości przypadków argument wejściowy określi katalog wyjściowy, a następnie utworzysz katalog jawnie. Użyj `os.makedirs(args.output_dir, exist_ok=True)`, aby utworzyć katalog wyjściowy. Zapoznaj się z [samouczkiem](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) przykładowego skryptu oceniania, który pokazuje ten Wzorzec projektowy. |
| Błędy zależności | Jeśli lokalnie opracowano i przetestowano skrypty, ale występują problemy z zależnościami podczas uruchamiania na zdalnym obliczeniu w potoku, upewnij się, że zależności środowiska obliczeniowego i wersje są zgodne ze środowiskiem testowym. |
| Niejednoznaczne błędy z obiektami docelowymi obliczeń | Usunięcie i ponowne utworzenie obiektów docelowych obliczeń może rozwiązać pewne problemy związane z obiektami docelowymi obliczeń. |
| Potok nie wykorzystał się z kroków | Ponowne użycie kroku jest włączone domyślnie, ale upewnij się, że nie zostało wyłączone w kroku potoku. Jeśli ponowne użycie jest wyłączone, parametr `allow_reuse` w kroku zostanie ustawiony na `False`. |
| Potok jest niepotrzebny. | Aby zapewnić, że kroki mają zostać uruchomione ponownie tylko wtedy, gdy ich dane podstawowe lub skrypty zmienią się, należy rozdzielić katalogi dla każdego kroku. Jeśli używasz tego samego katalogu źródłowego dla wielu kroków, może wystąpić niepotrzebne wykonanie ponownie. Użyj parametru `source_directory` w obiekcie kroku potoku, aby wskazać odizolowany katalog dla tego kroku i upewnić się, że nie używasz tej samej ścieżki `source_directory` dla wielu kroków. |

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z dokumentacją zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) oraz pakietem [kroków dla potoków usługi Azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Postępuj zgodnie z [zaawansowanym samouczkiem](tutorial-pipeline-batch-scoring-classification.md) dotyczącym tworzenia wsadowego oceniania przy użyciu potoków.

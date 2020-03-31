---
title: Gdzie zapisać & zapis plików eksperymentu
titleSuffix: Azure Machine Learning
description: Dowiedz się, gdzie zapisać pliki wejściowe eksperymentu i gdzie zapisywać pliki wyjściowe, aby zapobiec błędom ograniczenia magazynu i opóźnieniom eksperymentu.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078417"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Gdzie zapisywać i zapisywać pliki do eksperymentów usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, gdzie zapisać pliki wejściowe i gdzie zapisać pliki wyjściowe z eksperymentów, aby zapobiec błędom limitu magazynu i opóźnienia eksperymentu.

Podczas uruchamiania szkolenia działa na [cel obliczeniowy,](how-to-set-up-training-targets.md)są one izolowane od środowisk zewnętrznych. Celem tego projektu jest zapewnienie odtwarzalności i przenośności eksperymentu. Jeśli uruchomisz ten sam skrypt dwa razy, na tym samym lub innym celu obliczeniowym, otrzymasz te same wyniki. Za pomocą tego projektu można traktować obiekty docelowe obliczeń jako bezstanowe zasoby obliczeniowe, z których każdy nie ma koligacji z zadaniami, które są uruchomione po ich zakończeniu.

## <a name="where-to-save-input-files"></a>Gdzie zapisać pliki wejściowe

Aby można było zainicjować eksperyment na komputerze docelowym obliczeń lub komputerze lokalnym, należy upewnić się, że niezbędne pliki są dostępne dla tego obiektu docelowego obliczeń, takie jak pliki zależności i pliki danych, które kod musi uruchomić.

Usługa Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały folder skryptu do docelowego kontekstu obliczeniowego, a następnie wykonuje migawkę. Limit magazynu migawek eksperymentu wynosi 300 MB i/lub 2000 plików.

Z tego powodu zalecamy:

* **Przechowywanie plików w [magazynie danych](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)usługi Azure Machine Learning .** Zapobiega to problemom z opóźnieniami eksperymentu i ma zalety uzyskiwania dostępu do danych ze zdalnego obiektu docelowego obliczeń, co oznacza, że uwierzytelnianie i montaż są zarządzane przez usługę Azure Machine Learning. Dowiedz się więcej o określaniu magazynu danych jako katalogu źródłowym i przekazywaniu plików do magazynu danych w [artykule Dostęp do danych z magazynów danych.](how-to-access-data.md)

* **Jeśli potrzebujesz tylko kilku plików danych i skryptów zależności i nie możesz używać magazynu danych,** umieść pliki w tym samym katalogu folderów, co skrypt szkoleniowy. Określ ten `source_directory` folder jako bezpośrednio w skrypcie szkolenia lub w kodzie, który wywołuje skrypt szkolenia.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limity magazynowania migawek eksperymentów

W przypadku eksperymentów usługa Azure Machine Learning automatycznie tworzy migawkę eksperymentu kodu na podstawie katalogu sugerowanego podczas konfigurowania przebiegu. Ma całkowity limit 300 MB i/lub 2000 plików. Jeśli przekroczysz ten limit, zobaczysz następujący błąd:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Aby rozwiązać ten błąd, należy przechowywać pliki eksperymentu w magazynie danych. Jeśli nie możesz korzystać z magazynu danych, poniższa tabela zawiera możliwe alternatywne rozwiązania.

Opis&nbsp;eksperymentu|Rozwiązanie limitu magazynowania
---|---
Mniej niż 2000 plików & nie może korzystać z magazynu danych| Zastąd wymień limit rozmiaru migawki za pomocą <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Może to potrwać kilka minut w zależności od liczby i rozmiaru plików.
Musi używać określonego katalogu skryptów| Należy `.amlignore` wprowadzić plik, aby wykluczyć pliki z migawki eksperymentu, które nie są częścią kodu źródłowego. Dodaj nazwy plików do `.amlignore` pliku i umieść je w tym samym katalogu co skrypt szkoleniowy. Plik `.amlignore` używa tej samej [składni i](https://git-scm.com/docs/gitignore) `.gitignore` wzorców co plik.
Potok|Użyj innego podkatalogu dla każdego kroku
Notesy programu Jupyter| Utwórz `.amlignore` plik lub przenieś notes do nowego, pustego podkatalogu i uruchom ponownie kod.

## <a name="where-to-write-files"></a>Gdzie zapisywać pliki

Ze względu na izolację eksperymentów szkoleniowych zmiany w plikach, które mają miejsce podczas przebiegów, niekoniecznie są zachowywane poza środowiskiem. Jeśli skrypt modyfikuje pliki lokalne do obliczenia, zmiany nie są zachowywane dla następnego uruchomienia eksperymentu i nie są one propagowane z powrotem do komputera klienckiego automatycznie. W związku z tym zmiany wprowadzone podczas pierwszego uruchomienia eksperymentu nie i nie powinny mieć wpływu na te w drugim.

Podczas pisania zmian zaleca się zapisywanie plików w magazynie danych usługi Azure Machine Learning. Zobacz [Dostęp do danych z magazynów danych](how-to-access-data.md).

Jeśli nie potrzebujesz magazynu danych, zapisz pliki `./outputs` w `./logs` folderze i/lub folderze.

>[!Important]
> Dwa foldery, *dane wyjściowe* i *dzienniki*, otrzymują specjalne traktowanie przez usługę Azure Machine Learning. Podczas szkolenia podczas pisania`./outputs` plików`./logs` i folderów pliki zostaną automatycznie przesłane do historii uruchamiania, dzięki czemu masz do nich dostęp po zakończeniu biegu.

* **W przypadku danych wyjściowych, takich jak komunikaty o stanie lub wyniki oceniania,** należy zapisać pliki w folderze, `./outputs` aby były zachowywane jako artefakty w historii uruchamiania. Należy pamiętać o liczbie i rozmiarze plików zapisanych w tym folderze, ponieważ opóźnienie może wystąpić, gdy zawartość jest przekazywane do uruchamiania historii. Jeśli opóźnienie jest problemem, zaleca się zapisywanie plików do magazynu danych.

* **Aby zapisać plik pisemny jako dzienniki w historii uruchamiania,** zapisz pliki w folderze. `./logs` Dzienniki są przekazywane w czasie rzeczywistym, więc ta metoda jest odpowiednia do przesyłania strumieniowego aktualizacji na żywo z zdalnego uruchomienia.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [uzyskiwaniu dostępu do danych z magazynów danych.](how-to-access-data.md)

* Dowiedz się więcej o [konfigurowaniu celów szkoleniowych](how-to-set-up-training-targets.md).

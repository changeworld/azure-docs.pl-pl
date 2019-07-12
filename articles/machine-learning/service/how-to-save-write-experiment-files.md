---
title: Zapobieganie ograniczenia magazynu i eksperymentować opóźnienia dzięki katalogi wejściowe i wyjściowe
description: W tym artykule dowiesz się, miejsca zapisu plików wejściowych eksperymentu, a miejsce do zapisania plików wyjściowych zapobiegać błędom ograniczenia magazynu i eksperymentowania opóźnienia.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 1f9199b5bae0c82cd46750d8ef5522a0d3579671
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595279"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>W przypadku, gdy do zapisywania i zapisywania plików dla usługi Azure Machine Learning eksperymentów

W tym artykule dowiesz się, miejsca zapisu plików wejściowych i gdzie należy zapisać pliki wyjściowe z eksperymentów w taki sposób, aby zapobiec magazynu ograniczyć błędy i eksperymentowania opóźnienia.

Po uruchomieniu szkolenia działa na [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md), są one odizolowana od zewnętrznego środowiska. Celem tego projektu jest zapewnienie odtwarzaniem przenośność i mnogość opcji doświadczenia. Jeśli uruchamiasz dwa razy ten sam skrypt, w tym samym lub innym obliczeniowego elementu docelowego, pojawi się te same wyniki. W tym projekcie można traktować jako zasobów obliczeniowych bezstanowych, każdy o bez koligacji do zadań, które są uruchomione po zakończeniu obliczeniowych elementów docelowych.

## <a name="where-to-save-input-files"></a>Miejsca zapisu plików wejściowych

Przed rozpoczęciem eksperymentów na cel obliczenia lub na komputerze lokalnym, upewnij się, że niezbędne pliki są dostępne dla obiektu docelowego obliczeniowych, takich jak pliki zależności i pliki danych, którą Twój kod musi zostać uruchomiony.

Usługa Azure Machine Learning uruchamia skrypty szkolenia przez skopiowanie folderu cały skrypt do kontekstu obliczeniowego docelowego, a następnie tworzy migawkę. Limit magazynu migawek eksperymentu to 300 MB i/lub pliki 2000.

Z tego powodu zaleca się:

* **Przechowywanie plików w usłudze Azure Machine Learning [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Zapobiega to eksperymentu opóźnień i ma zalety dostępu do danych z zdalnego obliczeniowego elementu docelowego, co oznacza, że uwierzytelnianie i instalowania są zarządzane przez usługę Azure Machine Learning. Więcej informacji na temat określania magazyn danych jako katalog źródłowy i przekazywanie plików z magazynem danych w [dostęp do danych pochodzących z magazynów danych](how-to-access-data.md) artykułu.

* **Jeśli potrzebujesz tylko kilka plików danych i zależności, skrypty i nie można użyć magazyn danych,** umieścić pliki w tym samym katalogu folderu jako skrypt szkolenia. Określ ten folder jako swojej `source_directory` bezpośrednio w skrypcie szkolenia lub w kodzie, który wywołuje skrypt szkolenia.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limity magazynowania migawek eksperymentu

Dla eksperymentów Azure Machine Learning automatycznie sprawia, że migawki eksperymentu, oparte na katalog, który zasugerować po skonfigurowaniu uruchomienia kodu. Ma to całkowity limit 300 MB i/lub pliki 2000. Jeśli przekroczysz ten limit, zostanie wyświetlony następujący błąd:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Aby rozwiązać ten problem, należy przechowywać plików eksperyment na magazyn danych. Jeśli magazyn danych, nie można użyć poniższej tabeli oferuje możliwe rozwiązania alternatywne.

Eksperyment&nbsp;opis|Limit pamięci masowej
---|---
Mniej niż 2000 plików i nie można użyć magazynu danych| Limit rozmiaru migawki za pomocą zastąpienia <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Może to potrwać kilka minut w zależności od liczby i rozmiaru plików.
Należy użyć określonego skryptu w katalogu| Wprowadź `.amlignore` plik, aby wykluczyć pliki z migawki usługi eksperymentu, które nie są częścią kodu źródłowego. Dodaj nazwy plików do `.amlignore` plik i umieść go w tym samym katalogu co skrypt szkolenia. `.amlignore` Plików używa tych samych [składni i wzorce](https://git-scm.com/docs/gitignore) jako `.gitignore` pliku.
Potok|Używać różnych podkatalogu dla każdego kroku
Notesy programu Jupyter| Utwórz `.amlignore` plików lub Przenieś notesu do podkatalogu nowy, pusty, i ponownie uruchom kod.

## <a name="where-to-write-files"></a>Gdzie można zapisywać pliki

Ze względu na izolację eksperymentów szkolenia zmiany do plików, które występują podczas przebiegów nie są zawsze zachowywane spoza środowiska. Jeśli skrypt modyfikuje pliki lokalne do obliczenia, zmiany nie są zachowywane do swojego eksperymentu następnego uruchomienia i mogą one nie propagowany z powrotem do komputera klienckiego automatycznie. W związku z tym zmiany wprowadzone podczas pierwszego eksperymentu wykonywania nie i nie powinien wpływać na do drugiego.

Podczas zapisywania zmian, zaleca się zapisywania plików do magazynu danych usługi Azure Machine Learning. Zobacz [dostęp do danych pochodzących z magazynów danych](how-to-access-data.md).

Jeśli magazyn danych nie jest wymagany, Zapisz pliki na `./outputs` i/lub `./logs` folderu.

>[!Important]
> Dwa foldery *generuje* i *dzienniki*, specjalne traktowane przez uczenie maszynowe Azure. Podczas szkolenia, podczas zapisywania plików`./outputs` i`./logs` folderów, pliki automatycznie przekaże do Twojej historii uruchamiania, aby mieli do nich dostęp, po zakończeniu przebieg.

* **Dla danych wyjściowych, takich jak komunikaty o stanie lub oceniania wyniki** Zapisz pliki na `./outputs` folderu, dzięki czemu zostaną utrwalone jako artefakty w historii uruchamiania. Być w trosce o liczbę i rozmiar plików zapisywane do tego folderu, ponieważ opóźnienie może wystąpić, gdy zawartość są przekazywane do historii uruchamiania. Jeśli czas oczekiwania jest istotna, zaleca się zapisywania plików do magazynu danych.

* **Aby zapisać plik napisane jako dzienniki w historii uruchamiania** Zapisz pliki na `./logs` folderu. Dzienniki są przekazywane w czasie rzeczywistym, dzięki czemu ta metoda jest przydatna do przesyłania strumieniowego na żywo aktualizacje z lokalizacji zdalnej, uruchom.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [dostępu do danych z usługi magazynów danych](how-to-access-data.md).

* Dowiedz się więcej o [jak ustawić cele szkoleniowe](how-to-set-up-training-targets.md).

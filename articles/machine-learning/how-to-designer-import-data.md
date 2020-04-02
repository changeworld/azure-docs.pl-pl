---
title: Importowanie danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zaimportować dane do projektanta usługi Azure Machine Learning z różnych źródeł danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 1ad7677607d625f673546a6ea29ea58b80a8d1b5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546750"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importowanie danych do projektanta usługi Azure Machine Learning (wersja zapoznawcza)

W tym artykule dowiesz się, jak zaimportować własne dane w projektancie, aby utworzyć rozwiązania niestandardowe. Istnieją dwa sposoby importowania danych do projektanta: 

* **Zestawy danych usługi Azure Machine Learning** — rejestrowanie [zestawów danych](concept-data.md#datasets) w usłudze Azure Machine Learning w celu włączenia zaawansowanych funkcji ułatwiające zarządzanie danymi.
* **Moduł Importuj dane** — użyj modułu [Importuj dane,](algorithm-module-reference/import-data.md) aby uzyskać bezpośredni dostęp do danych z internetowych źródeł danych.

## <a name="use-azure-machine-learning-datasets"></a>Korzystanie z zestawów danych usługi Azure Machine Learning

Zaleca się użycie [zestawów danych](concept-data.md#datasets) do importowania danych do projektanta. Podczas rejestrowania zestawu danych można w pełni korzystać z zaawansowanych funkcji danych, takich jak [przechowywanie wersji, śledzenie](how-to-version-track-datasets.md) i [monitorowanie danych.](how-to-monitor-datasets.md)

### <a name="register-a-dataset"></a>Rejestrowanie zestawu danych

Istniejące zestawy danych można zarejestrować [programowo za pomocą zestawu SDK](how-to-create-register-datasets.md#use-the-sdk) lub [wizualnie w studiu usługi Azure Machine Learning.](how-to-create-register-datasets.md#use-the-ui)

Można również zarejestrować dane wyjściowe dla dowolnego modułu projektanta jako zestaw danych.

1. Wybierz moduł, który wyprowadza dane, które chcesz zarejestrować.

1. W okienku właściwości wybierz polecenie **Dane rejestruj** > **zestaw danych**.

    ![Zrzut ekranu przedstawiający przechodzenie do opcji Zarejestruj zestaw danych](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Używanie zestawu danych

Zarejestrowane zestawy danych można znaleźć w palecie modułów w obszarze **Zestawy danych** > **Moje zestawy danych**. Aby użyć zestawu danych, przeciągnij go i upuść na kanwę potoku. Następnie podłącz port wyjściowy zestawu danych do innych modułów w palecie.

![Zrzut ekranu przedstawiający lokalizację zapisanych zestawów danych w palecie projektanta](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Projektant obecnie obsługuje tylko przetwarzanie [zestawów danych tabelarów.](how-to-create-register-datasets.md#dataset-types) Jeśli chcesz użyć [zestawów danych plików,](how-to-create-register-datasets.md#dataset-types)użyj zestawu SDK usługi Azure Machine Learning dostępnego dla języka Python i Języka R.

## <a name="import-data-using-the-import-data-module"></a>Importowanie danych przy użyciu modułu Importuj dane

Chociaż zaleca się używanie zestawów danych do importowania danych, można również użyć modułu [Importuj dane.](algorithm-module-reference/import-data.md) Moduł Importuj dane pomija rejestrowanie zestawu danych w usłudze Azure Machine Learning i importuje dane bezpośrednio z [magazynu danych](concept-data.md#datastores) lub adresu URL HTTP.

Szczegółowe informacje na temat używania modułu Importuj dane można znaleźć na [stronie odwołania importu](algorithm-module-reference/import-data.md)danych .

> [!NOTE]
> Jeśli zestaw danych ma zbyt wiele kolumn, może wystąpić następujący błąd: "Sprawdzanie poprawności nie powiodło się z powodu ograniczenia rozmiaru". Aby tego uniknąć, [zarejestruj zestaw danych w interfejsie zestawów danych](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Obsługiwane źródła

W tej sekcji wymieniono źródła danych obsługiwane przez projektanta. Dane są dochodzące do projektanta z magazynu danych lub z [tabelaryczny zestaw danych](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Źródła magazynu danych
Aby uzyskać listę obsługiwanych źródeł magazynu danych, zobacz [Dostęp do danych w usługach magazynu platformy Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Tabelaryczne źródła zestawów danych

Projektant obsługuje tabelaryczne zestawy danych utworzone z następujących źródeł:
 * Rozdzielane pliki
 * Pliki JSON
 * Pliki Parquet
 * Zapytania SQL

## <a name="data-types"></a>Typy danych

Projektant wewnętrznie rozpoznaje następujące typy danych:

* Ciąg
* Liczba całkowita
* Wartość dziesiętna
* Wartość logiczna
* Data

Projektant używa wewnętrznego typu danych do przekazywania danych między modułami. Dane można jawnie przekonwertować na format tabeli danych za pomocą modułu [Konwertuj na zestaw danych.](algorithm-module-reference/convert-to-dataset.md) Każdy moduł, który akceptuje formaty inne niż format wewnętrzny, przekonwertuje dane po cichu przed przekazaniem ich do następnego modułu.

## <a name="data-constraints"></a>Ograniczenia danych

Moduły w projektancie są ograniczone przez rozmiar obiektu docelowego obliczeń. W przypadku większych zestawów danych należy użyć większego zasobu obliczeniowego usługi Azure Machine Learning. Aby uzyskać więcej informacji na temat obliczeń usługi Azure Machine Learning, zobacz [Jakie są obiekty docelowe obliczeń w usłudze Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Następne kroki

Poznaj podstawy projektanta z [samouczka: Przewiduj cenę samochodu z projektantem.](tutorial-designer-automobile-price-train-score.md)

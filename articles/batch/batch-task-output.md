---
title: Utrwalanie danych wyjściowych zadania i zadania w magazynie danych — usługa Azure Batch
description: Dowiedz się więcej o różnych opcjach utrwalania danych wyjściowych z zadań i zadań usługi Batch. Można utrwalić dane do usługi Azure Storage lub do innego magazynu danych.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0809a838f1d34491eb4e276ce356eded9b98756e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022838"
---
# <a name="persist-job-and-task-output"></a>Utrwalanie danych wyjściowych zadań i zadań podrzędnych

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Oto kilka typowych przykładów danych wyjściowych zadań:

- Pliki utworzone podczas przetwarzania danych wejściowych przez zadanie.
- Pliki dziennika skojarzone z wykonywaniem zadań.

W tym artykule opisano różne opcje utrwalania danych wyjściowych zadań.

## <a name="options-for-persisting-output"></a>Opcje utrwalania danych wyjściowych

W zależności od scenariusza istnieje kilka różnych metod, które można podjąć, aby utrwalić dane wyjściowe zadania:

- [Użyj interfejsu API usługi wsadowej](batch-task-output-files.md).  
- [Użyj biblioteki Konwencje plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).  
- Zaimplementuj standard Konwencje plików wsadowych w aplikacji.
- Zaimplementuj niestandardowe rozwiązanie do przenoszenia plików.

W poniższych sekcjach pokrótce opisano każde podejście, a także ogólne zagadnienia dotyczące projektu dla utrwalania danych wyjściowych.

### <a name="use-the-batch-service-api"></a>Korzystanie z interfejsu API usługi wsadowej

Usługa Batch obsługuje określanie plików wyjściowych w usłudze Azure Storage dla danych zadań podczas [dodawania zadania do zadania](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) lub [dodawania kolekcji zadań do zadania](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Aby uzyskać więcej informacji na temat utrwalania danych wyjściowych zadań za pomocą interfejsu API usługi wsadowej, zobacz [Utrwalanie danych zadań w usłudze Azure Storage za pomocą interfejsu API usługi wsadowej.](batch-task-output-files.md)

### <a name="use-the-batch-file-conventions-library-for-net"></a>Użyj biblioteki Konwencje plików wsadowych dla platformy .NET

Usługa Batch definiuje opcjonalny zestaw konwencji nazewnictwa plików wyjściowych zadań w usłudze Azure Storage. [Standard Konwencje plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) opisuje te konwencje. Standard Konwencje plików określa nazwy docelowego kontenera i ścieżki obiektu blob w usłudze Azure Storage dla danego pliku wyjściowego na podstawie nazw zadania i zadania.

To od Ciebie zależy, czy zdecydujesz się użyć standardu Konwencje plików do nazywania plików danych wyjściowych. Można również nazwać kontenera docelowego i obiektu blob jednak chcesz. Jeśli używasz standardu Konwencje plików do nazewnictwa plików wyjściowych, pliki wyjściowe są dostępne do przeglądania w [witrynie Azure portal][portal].

Deweloperzy, którzy twórz rozwiązania wsadowe w językach C# i .NET, mogą używać [biblioteki Konwencje plików dla platformy .NET][nuget_package] do utrwalania danych zadań na koncie usługi Azure Storage, zgodnie [ze standardem Konwencje plików wsadowych.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) Biblioteka konwencji plików obsługuje przenoszenie plików wyjściowych do usługi Azure Storage i nazywanie kontenerów docelowych i obiektów blob w dobrze znany sposób.

Aby uzyskać więcej informacji na temat utrwalania danych wyjściowych zadań za pomocą biblioteki Konwencje plików dla platformy .NET, zobacz [Utrwalanie danych zadania i zadania w usłudze Azure Storage za pomocą biblioteki Konwencje plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementowanie standardu Konwencje plików wsadowych

Jeśli używasz języka innego niż .NET, można zaimplementować [standard Konwencje plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) w własnej aplikacji.

Można zaimplementować konwencje plików nazewnictwa standard samodzielnie, gdy chcesz sprawdzony schemat nazewnictwa lub gdy chcesz wyświetlić dane wyjściowe zadania w witrynie Azure portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementowanie niestandardowego rozwiązania do przenoszenia plików

Można również zaimplementować własne kompletne rozwiązanie do przenoszenia plików. Użyj tego podejścia, gdy:

- Chcesz utrwalić dane zadania do magazynu danych innych niż Usługa Azure Storage. Aby przekazać pliki do magazynu danych, takich jak Sql azure lub Azure DataLake, można utworzyć niestandardowy skrypt lub plik wykonywalny do przekazania do tej lokalizacji. Następnie można wywołać go w wierszu polecenia po uruchomieniu podstawowego pliku wykonywalnego. Na przykład w węźle systemu Windows można wywołać te dwa polecenia:`doMyWork.exe && uploadMyFilesToSql.exe`
- Chcesz wykonać zaznaczenie lub wczesne przesłanie wstępnych wyników.
- Chcesz zachować szczegółową kontrolę nad obsługą błędów. Na przykład można zaimplementować własne rozwiązanie, jeśli chcesz użyć akcji zależności zadania do podjęcia pewnych akcji przekazywania na podstawie określonych kodów zakończenia zadania. Aby uzyskać więcej informacji na temat akcji zależności zadań, zobacz [Tworzenie zależności zadań w celu uruchamiania zadań zależnych od innych zadań](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Zagadnienia dotyczące projektu dla utrwalania danych wyjściowych

Podczas projektowania rozwiązania batch, należy wziąć pod uwagę następujące czynniki związane z zadania i zadania dane wyjściowe.

- **Okres istnienia węzła obliczeniowego:** węzły obliczeniowe są często przejściowe, szczególnie w pulach z obsługą skalowania automatycznego. Dane wyjściowe z zadania uruchamianego w węźle są dostępne tylko wtedy, gdy węzeł istnieje i tylko w okresie przechowywania plików ustawionym dla zadania. Jeśli zadanie generuje dane wyjściowe, które mogą być potrzebne po zakończeniu zadania, zadanie musi przekazać jego pliki wyjściowe do magazynu trwałe, takie jak usługa Azure Storage.

- **Magazyn wyjściowy:** Usługa Azure Storage jest zalecana jako magazyn danych dla danych wyjściowych zadań, ale można użyć dowolnego magazynu trwałe. Zapisywanie danych wyjściowych zadań w usłudze Azure Storage jest zintegrowane z interfejsem API usługi batch. Jeśli używasz innej formy magazynu trwałe, należy napisać logikę aplikacji, aby samodzielnie utrwalić dane wyjściowe zadania.

- **Pobieranie danych wyjściowych:** Można pobrać dane wyjściowe zadań bezpośrednio z węzłów obliczeniowych w puli lub z usługi Azure Storage lub innego magazynu danych, jeśli utrwaliłeś dane wyjściowe zadania. Aby pobrać dane wyjściowe zadania bezpośrednio z węzła obliczeniowego, potrzebna jest nazwa pliku i jego lokalizacja wyjściowa w węźle. Jeśli utrwalisz dane wyjściowe zadania do usługi Azure Storage, potrzebujesz pełnej ścieżki do pliku w usłudze Azure Storage, aby pobrać pliki wyjściowe za pomocą narzędzia Azure Storage SDK.

- **Wyświetlanie danych wyjściowych:** Podczas przechodzenia do zadania batch w witrynie Azure portal i wybierz **pliki w węźle**, są prezentowane ze wszystkimi plikami skojarzonymi z zadaniem, a nie tylko pliki wyjściowe, które Cię interesują. Ponownie pliki w węzłach obliczeniowych są dostępne tylko wtedy, gdy węzeł istnieje i tylko w czasie przechowywania plików ustawionym dla zadania. Aby wyświetlić dane wyjściowe zadań, które zostały utrwalone w usłudze Azure Storage, można użyć witryny Azure Portal lub aplikacji klienckiej usługi Azure Storage, takiej jak [Eksplorator usługi Azure Storage.][storage_explorer] Aby wyświetlić dane wyjściowe w usłudze Azure Storage za pomocą portalu lub innego narzędzia, musisz znać lokalizację pliku i przejść do niego bezpośrednio.

## <a name="next-steps"></a>Następne kroki

- Eksploruj przy użyciu nowych funkcji w interfejsie API usługi wsadowej do utrwalania danych zadań w [persist danych zadań do usługi Azure Storage za pomocą interfejsu API usługi wsadowej.](batch-task-output-files.md)
- Dowiedz się więcej o używaniu biblioteki konwencje plików wsadowych dla platformy .NET w [utrwalenie danych zadania i zadania w usłudze Azure Storage za pomocą biblioteki Konwencje plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).
- Zobacz [PersistOutputs][github_persistoutputs] przykładowego projektu w usłudze GitHub, który pokazuje, jak używać zarówno biblioteki klienta usługi Batch dla platformy .NET i biblioteki konwencje plików dla platformy .NET, aby utrwalić dane wyjściowe zadania do trwałego magazynu.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 

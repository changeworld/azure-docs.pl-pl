---
title: Opracowywanie funkcji .NET Standard na potrzeby Azure Stream Analytics zadań brzegowych (wersja zapoznawcza)
description: Dowiedz się, jak opracowywać zdefiniowane przez użytkownika funkcje programu .NET dla Azure Stream Analytics zadań przy użyciu CodeBehind, istniejącego pakietu lub projektu lokalnego.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 287ee2d84ea3d5c6f1568edb1636191f509681e0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901631"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Opracowywanie .NET Standard funkcji zdefiniowanych przez użytkownika do Azure Stream Analytics zadań brzegowych (wersja zapoznawcza)

Azure Stream Analytics oferuje język zapytań przypominający SQL, służący do wykonywania transformacji i obliczeń na strumieniach danych zdarzeń. Istnieje wiele wbudowanych funkcji, ale niektóre złożone scenariusze wymagają dodatkowej elastyczności. Dzięki .NET standard funkcji zdefiniowanych przez użytkownika (UDF) można wywoływać własne funkcje, które są zapisywane w dowolnym języku .NET standardC#( F#, itp.), aby zwiększyć Stream Analytics języka zapytań. UDF umożliwiają wykonywanie złożonych obliczeń matematycznych, importowanie niestandardowych modeli ml przy użyciu ml.NET i używanie niestandardowych reguł przypisywania w przypadku brakujących danych. Funkcja zdefiniowana przez użytkownika dla zadań usługi Stream Analytics Edge jest obecnie dostępna w wersji zapoznawczej i nie powinna być używana w przypadku obciążeń produkcyjnych.

## <a name="overview"></a>Przegląd
Narzędzia Visual Studio Tools for Azure Stream Analytics ułatwiają pisanie UDF, testowanie zadań lokalnie (nawet w trybie offline) i publikowanie zadań Stream Analytics na platformie Azure. Po opublikowaniu na platformie Azure można wdrożyć zadanie na urządzeniach IoT przy użyciu IoT Hub.

Istnieją trzy sposoby implementowania funkcji zdefiniowanych przez użytkownika:

* CodeBehind pliki w projekcie ASA
* UDF z lokalnego projektu
* Istniejący pakiet z konta usługi Azure Storage

## <a name="package-path"></a>Ścieżka pakietu

Format dowolnego pakietu UDF ma `/UserCustomCode/CLR/*`Path. Biblioteki dołączane dynamicznie (dll) i zasoby są kopiowane w folderze `/UserCustomCode/CLR/*`, co ułatwia izolowanie bibliotek DLL użytkowników z systemowych i Azure Stream Analytics bibliotek DLL. Ta ścieżka pakietu jest używana dla wszystkich funkcji niezależnie od metody używanej do ich używania.

## <a name="supported-types-and-mapping"></a>Obsługiwane typy i mapowanie

|**Typ UDF (C#)**  |**Typ Azure Stream Analytics**  |
|---------|---------|
|Długo  |  bigint   |
|double  |  double   |
|string  |  nvarchar (max)   |
|Datę  |  Datę   |
|konstrukcja  |  IRecord   |
|obiekt  |  IRecord   |
|> Obiektu\<tablicy  |  IArray   |
|< słownika, ciąg >  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Funkcje zdefiniowane przez użytkownika można napisać w **skrypcie. ASQL** Codebehind. Narzędzia programu Visual Studio automatycznie kompilują plik CodeBehind do pliku zestawu. Zestawy są spakowane jako plik zip i przekazywane do konta magazynu podczas przesyłania zadania do platformy Azure. Możesz dowiedzieć się, jak napisać C# UDF przy użyciu CodeBehind, postępując zgodnie z [ C# samouczkiem UDF for Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) . 

## <a name="local-project"></a>Projekt lokalny
Funkcje zdefiniowane przez użytkownika mogą być zapisywane w zestawie, który jest później przywoływany w kwerendzie Azure Stream Analytics. Jest to zalecana opcja dla funkcji złożonych, które wymagają pełnej mocy języka .NET Standard poza językiem wyrażeń, na przykład logiki proceduralnej lub rekursji. UDF z projektu lokalnego może być również używany, gdy trzeba udostępnić logikę funkcji w kilku Azure Stream Analyticsych zapytaniach. Dodanie UDF do projektu lokalnego umożliwia debugowanie i testowanie funkcji lokalnie w programie Visual Studio.

Aby odwołać się do projektu lokalnego:

1. Utwórz nową bibliotekę klas w rozwiązaniu.
2. Napisz kod w klasie. Należy pamiętać, że klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*. 
3. Skompiluj projekt. Narzędzia będą pakować wszystkie artefakty w folderze bin do pliku zip i przekazać plik zip na konto magazynu. W przypadku odwołań zewnętrznych należy użyć odwołania do zestawu zamiast pakietu NuGet.
4. Odwołuje się do nowej klasy w projekcie Azure Stream Analytics.
5. Dodaj nową funkcję w projekcie Azure Stream Analytics.
6. Skonfiguruj ścieżkę zestawu w pliku konfiguracji zadania, `JobConfig.json`. Ustaw ścieżkę zestawu na **odwołanie do projektu lokalnego lub CodeBehind**.
7. Kompiluj zarówno projekt funkcji, jak i projekt Azure Stream Analytics.  

### <a name="example"></a>Przykład

W tym przykładzie **UDFTest** jest projektem biblioteki C# klas, a **ASAEdgeUDFDemo** jest projektem Azure Stream Analytics krawędzi, który będzie odwoływać się do **UDFTest**.

![Azure Stream Analytics IoT Edge projektu w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Skompiluj C# projekt, który umożliwi dodanie odwołania do C# formatu UDF z kwerendy Azure Stream Analytics.
    
   ![Tworzenie projektu IoT Edge Azure Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Dodaj odwołanie do C# projektu w projekcie ASA Edge. Kliknij prawym przyciskiem myszy węzeł odwołania i wybierz polecenie Dodaj odwołanie.

   ![Dodawanie odwołania do C# projektu w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Wybierz nazwę C# projektu z listy. 
    
   ![Wybierz nazwę C# projektu z listy referencyjnej](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. W obszarze **References** ( **Eksplorator rozwiązań**) powinna zostać wyświetlona pozycja **UDFTest** .

   ![Wyświetl odwołanie do funkcji zdefiniowanej przez użytkownika w Eksploratorze rozwiązań](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kliknij prawym przyciskiem myszy folder **Functions** i wybierz polecenie **nowy element**.

   ![Dodaj nowy element do funkcji w rozwiązaniu Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Dodaj C# funkcję **SquareFunction. json** do projektu Azure Stream Analytics.

   ![Wybierz funkcję CSharp z elementów krawędzi Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kliknij dwukrotnie funkcję w **Eksplorator rozwiązań** , aby otworzyć okno dialogowe konfiguracji.

   ![Konfiguracja funkcji Sharp języka C w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. W obszarze C# Konfiguracja funkcji wybierz pozycję **Załaduj z odwołania do projektu ASA** i pokrewnego zestawu, klasy i nazw metod z listy rozwijanej. Aby odwołać się do metod, typów i funkcji w kwerendzie Stream Analytics Edge, klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.

   ![Konfiguracja funkcji Sharp Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Istniejące pakiety

Możesz tworzyć .NET Standard UDF w dowolnym dowolnym środowisku IDE i wywoływać je z poziomu zapytania Azure Stream Analytics. Najpierw Skompiluj swój kod i Spakuj wszystkie biblioteki DLL. Format pakietu ma ścieżkę `/UserCustomCode/CLR/*`. Następnie Przekaż `UserCustomCode.zip` do katalogu głównego kontenera na koncie usługi Azure Storage.

Po przekazaniu pakietów zip zestawu do konta usługi Azure Storage można użyć funkcji w Azure Stream Analytics zapytaniach. Wszystko, co należy zrobić, obejmuje informacje o magazynie w konfiguracji zadania Stream Analytics Edge. Nie można przetestować funkcji lokalnie przy użyciu tej opcji, ponieważ narzędzia Visual Studio Tools nie pobierają pakietu. Ścieżka pakietu jest analizowana bezpośrednio w usłudze. 

Aby skonfigurować ścieżkę zestawu w pliku konfiguracji zadania, `JobConfig.json`:

Rozwiń sekcję **Konfiguracja kodu skonfigurowana przez użytkownika**, a następnie wypełnij konfigurację za pomocą następujących sugerowanych wartości:

 |**Ustawienie**  |**Sugerowana wartość**  |
 |---------|---------|
 |Źródło zestawu  | Istniejące pakiety zestawu z chmury    |
 |Zasób  |  Wybierz dane z bieżącego konta   |
 |Subskrypcja  |  Wybierz subskrypcję.   |
 |Konto magazynu  |  Wybierz konto magazynu.   |
 |Kontener  |  Wybierz kontener utworzony na koncie magazynu.   |

![Konfiguracja zadania usługi Azure Stream Analytics Edge w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>Ograniczenia
Wersja zapoznawcza UDF ma obecnie następujące ograniczenia:

* W przypadku Azure Stream Analytics na urządzeniach IoT Edge nie można używać języków .NET Standard. W przypadku zadań w chmurze można napisać funkcje języka JavaScript zdefiniowane przez użytkownika. Aby dowiedzieć się więcej, zapoznaj się z samouczkiem [UDF w Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-functions.md) .

* .NET Standard UDF można tworzyć tylko w programie Visual Studio i publikować je na platformie Azure. Wersje .NET Standard UDF w trybie tylko do odczytu mogą być wyświetlane w obszarze **funkcje** w Azure Portal. Tworzenie funkcji .NET Standard nie jest obsługiwane w Azure Portal.

* Edytor zapytań Azure Portal zawiera błąd podczas korzystania z .NET Standard UDF w portalu. 

* Ponieważ niestandardowy kod udostępnia kontekst z aparatem Azure Stream Analytics, kod niestandardowy nie może odwoływać się do wszystkich elementów, które mają sprzeczną przestrzeń nazw/DLL_name z kodem Azure Stream Analytics. Na przykład nie można odwołać się do *Newtonsoft JSON*.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: pisanie funkcji C# zdefiniowanej przez użytkownika dla zadania Azure Stream Analytics Edge (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Samouczek: Azure Stream Analytics funkcje języka JavaScript zdefiniowane przez użytkownika](stream-analytics-javascript-user-defined-functions.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)

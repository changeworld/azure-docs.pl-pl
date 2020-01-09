---
title: Opracowywanie funkcji .NET Standard dla zadań Azure Stream Analytics (wersja zapoznawcza)
description: Dowiedz się, jak napisać funkcje zdefiniowane przez użytkownika w języku c# dla Stream Analytics zadań.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426314"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Opracowywanie .NET Standard funkcji zdefiniowanych przez użytkownika dla zadań Azure Stream Analytics (wersja zapoznawcza)

Usługa Azure Stream Analytics oferuje język zapytań przypominający SQL umożliwiające wykonywanie przekształceń i obliczenia za pośrednictwem strumieni danych zdarzenia. Dostępnych jest wiele wbudowanych funkcji, ale niektóre złożone scenariusze wymagają zapewnia zwiększoną elastyczność. Za pomocą .NET Standard funkcje zdefiniowane przez użytkownika (UDF), można wywołać funkcje napisane w dowolnym języku standardowy .NET (C#, F#, itp.) aby rozszerzyć język zapytań usługi Stream Analytics. Funkcje zdefiniowane przez użytkownika umożliwiają wykonują obliczenia złożonych obliczeń, importowanie niestandardowych modeli uczenia Maszynowego przy użyciu strukturze ML.NET i logiki niestandardowej przypisywania na użytek brakujące dane. Funkcja UDF dla Stream Analytics zadań jest obecnie w wersji zapoznawczej i nie powinna być używana w obciążeniach produkcyjnych.

Funkcja zdefiniowana przez użytkownika platformy .NET dla zadań w chmurze jest dostępna w:
* Zachodnio-środkowe stany USA
* Europa Północna
* Wschodnie stany USA
* Zachodnie stany USA
* Wschodnie stany USA 2
* Europa Zachodnia

Jeśli interesuje Cię korzystanie z tej funkcji w innym regionie, możesz [poprosić o dostęp](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Przegląd
Program Visual Studio tools dla usługi Azure Stream Analytics ułatwiają pisanie funkcje zdefiniowane przez użytkownika, test zadań lokalnie (nawet w trybie offline) i opublikuj zadania usługi Stream Analytics na platformie Azure. Po opublikowaniu na platformie Azure, można wdrożyć zadanie na urządzeniach IoT przy użyciu usługi IoT Hub.

Istnieją trzy sposoby implementowania funkcji zdefiniowanych przez użytkownika:

* Pliki CodeBehind w projekcie ASA
* Funkcji zdefiniowanej przez użytkownika z lokalnej projektu
* Istniejący pakiet z konta usługi Azure storage

## <a name="package-path"></a>Ścieżka do pakietu

Format dowolny pakiet funkcji zdefiniowanej przez użytkownika ma ścieżkę `/UserCustomCode/CLR/*`. Biblioteka dołączana dynamicznie (dll) i zasoby, które są kopiowane w ramach `/UserCustomCode/CLR/*` folderu, co pomaga wykrywać dll użytkownika z systemu i Azure Stream Analytics z biblioteki dll. Ta ścieżka pakietu jest używana dla wszystkich funkcji, niezależnie od metody używanej do nich stosować.

## <a name="supported-types-and-mapping"></a>Obsługiwane typy i mapowanie

|**Typ funkcji zdefiniowanej przez użytkownika (C#)**  |**Usługa Azure Stream Analytics, wpisz**  |
|---------|---------|
|długi  |  bigint   |
|double  |  double   |
|string  |  nvarchar(max)   |
|Data i godzina  |  Data i godzina   |
|— Struktura  |  IRecord   |
|obiekt  |  IRecord   |
|> Obiektu\<tablicy  |  IArray   |
|Dictionary < string, object >  |  IRecord   |

## <a name="codebehind"></a>Plik CodeBehind
Funkcje zdefiniowane przez użytkownika można pisać w **Script.asql** CodeBehind. Narzędzia programu Visual Studio automatycznie zostanie skompilowany plik CodeBehind do pliku zestawu. Zestawy są spakowany jako plik zip i przekazać do swojego konta magazynu, po przesłaniu zadania na platformie Azure. Możesz dowiedzieć się, jak napisać UDF języka C# przy użyciu pliku CodeBehind, postępując zgodnie z [UDF języka C# dla zadań usługi Stream Analytics Edge](stream-analytics-edge-csharp-udf.md) samouczka. 

## <a name="local-project"></a>Lokalny projekt
Funkcje zdefiniowane przez użytkownika mogą być napisane w zestawie, do którego odwołuje się później w zapytaniu usługi Azure Stream Analytics. Jest to zalecana opcja dla złożone funkcje, które wymagają pełnych możliwości programu .NET Standard języka poza jego język wyrażeń, takich jak logika procedurach lub rekursji. Funkcje zdefiniowane przez użytkownika z projektu lokalnego również mogą być używane, gdy zachodzi potrzeba udostępniania logiki funkcji między kilka zapytań usługi Azure Stream Analytics. Dodawanie funkcji zdefiniowanych przez użytkownika do projektu lokalnego daje możliwość debugowanie i testowanie funkcji lokalnie w programie Visual Studio.

Aby odwołać się do lokalnego projektu:

1. Utwórz nową bibliotekę klas w rozwiązaniu.
2. Zapisz kod w klasie. Należy pamiętać, że klasy musi być zdefiniowany jako *publicznych* i obiekty muszą być zdefiniowane jako *statyczne publiczne*. 
3. Skompiluj projekt. Narzędzia będzie spakować wszystkich artefaktów na folder bin do pliku zip i przekaż plik zip do konta magazynu. Dla odwołania zewnętrzne należy użyć odwołania do zestawu, zamiast pakietu NuGet.
4. Odwołanie do nowej klasy w projekcie usługi Azure Stream Analytics.
5. Dodaj nową funkcję w projekcie usługi Azure Stream Analytics.
6. Skonfiguruj ścieżkę zestawu w pliku konfiguracyjnym zadania `JobConfig.json`. Ustaw ścieżkę zestawu **odwołanie do lokalnego projektu lub pliku CodeBehind**.
7. Ponownie skompiluj projekt funkcji i projekt usługi Azure Stream Analytics.  

### <a name="example"></a>Przykład

W tym przykładzie **UDFTest** jest projektem biblioteki C# klas, a **ASAUDFDemo** jest projektem Azure Stream Analytics, który będzie odwoływać się do **UDFTest**.

![Projekt platformy Azure Stream Analytics z usługi IoT Edge w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Tworzenie projektu C#, która umożliwi Dodaj odwołanie do UDF języka C# z kwerendy usługi Azure Stream Analytics.
    
   ![Tworzenie projektu usługi Azure Stream Analytics IoT Edge w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Dodaj odwołanie do C# projektu w projekcie ASA. Kliknij prawym przyciskiem myszy węzeł odwołania i wybierz polecenie Dodaj odwołanie.

   ![Dodaj odwołanie do projektu C# w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Wybierz nazwę projektu C# z listy. 
    
   ![Wybierz nazwę projektu C# z listy odwołania](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Powinien zostać wyświetlony **UDFTest** obszarze **odwołania** w **Eksploratora rozwiązań**.

   ![Wyświetl użytkownika określone odwołanie do funkcji w Eksploratorze rozwiązań](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kliknij prawym przyciskiem myszy **funkcje** folder i wybierz polecenie **nowy element**.

   ![Dodaj nowy element do funkcji w rozwiązaniu usługi Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Funkcja języka C# Dodaj **SquareFunction.json** do projektu usługi Azure Stream Analytics.

   ![Wybierz funkcję CSharp z usługi Stream Analytics Edge elementów w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kliknij dwukrotnie funkcję w **Eksploratora rozwiązań** aby otworzyć okno dialogowe konfiguracji.

   ![C sharp funkcja konfiguracji w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. W języku C# konfiguracji funkcji, wybierz **obciążenia z odwołania projektu ASA** i powiązane nazwy zestawu, klasy i metody, z listy rozwijanej. Aby odwołać się do metod, typów i funkcji w kwerendzie Stream Analytics, klasy muszą być zdefiniowane jako *publiczne* , a obiekty muszą być zdefiniowane jako *statyczne publiczne*.

   ![Stream Analytics w języku C sharp konfiguracji — funkcja](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Istniejące pakiety

Można tworzyć .NET Standard funkcje zdefiniowane przez użytkownika w dowolnym ulubionego środowiska IDE, a następnie wywołaj je z zapytania usługi Azure Stream Analytics. Najpierw należy ją skompilować kod i pakietu wszystkie biblioteki dll. Format pakietu ma ścieżkę `/UserCustomCode/CLR/*`. Następnie przekaż `UserCustomCode.zip` w katalogu głównym kontenera na koncie magazynu platformy Azure.

Gdy zestaw pakietów zip zostały przekazane do konta usługi Azure storage, można użyć funkcji w zapytaniach usługi Azure Stream Analytics. Wszystko, co należy zrobić, obejmuje informacje o magazynie w konfiguracji zadania Stream Analytics. Nie można przetestować tę funkcję lokalnie przy użyciu tej opcji, ponieważ narzędzia programu Visual Studio nie będą pobierane do pakietu. Ścieżka pakietu jest analizowany bezpośrednio do usługi. 

Aby skonfigurować ścieżkę zestawu w pliku konfiguracyjnym zadania `JobConfig.json`:

Rozwiń sekcję **Konfiguracja kodu skonfigurowana przez użytkownika**, a następnie wypełnij konfigurację za pomocą następujących sugerowanych wartości:

   |**Ustawienie**|**Sugerowana wartość**|
   |-------|---------------|
   |Zasób ustawień magazynu globalnego|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja ustawień magazynu globalnego| < subskrypcję >|
   |Konto magazynu ustawień globalnych magazynu| < konta magazynu >|
   |Zasób ustawień niestandardowego magazynu kodu|Wybierz źródło danych z bieżącego konta|
   |Konto magazynu ustawień niestandardowych magazynu kodu|< konta magazynu >|
   |Kontener ustawień niestandardowego magazynu kodu|< kontener magazynu >|
   |Źródło niestandardowego zestawu kodu|Istniejące pakiety zestawu z chmury|
   |Źródło niestandardowego zestawu kodu|UserCustomCode. zip|

## <a name="limitations"></a>Ograniczenia
Wersja zapoznawcza funkcji zdefiniowanej przez użytkownika aktualnie ma następujące ograniczenia:

* Można tylko utworzone w programie Visual Studio .NET standard UDF i opublikowane na platformie Azure. Tylko do odczytu wersje platformy .NET Standard funkcje zdefiniowane przez użytkownika mogą być wyświetlane w obszarze **funkcji** w witrynie Azure portal. Tworzenie funkcji .NET Standard nie jest obsługiwana w witrynie Azure portal.

* Edytor zapytań portalu platformy Azure pokazuje błąd, podczas korzystania z platformy .NET Standard funkcji zdefiniowanej przez użytkownika w portalu. 

* Ponieważ kod niestandardowy udostępni kontekst aparatu usługi Azure Stream Analytics, niestandardowy kod nie może odwoływać się wszystkie elementy, które ma powodujące konflikt przestrzeń nazw/NazwaDLL przy użyciu kodu usługi Azure Stream Analytics. Na przykład nie można odwoływać się *Newtonsoft Json*.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: pisanie funkcji C# zdefiniowanej przez użytkownika dla zadania Azure Stream Analytics (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Samouczek: Usługa Azure Stream Analytics JavaScript zdefiniowanych przez użytkownika funkcje](stream-analytics-javascript-user-defined-functions.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)

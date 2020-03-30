---
title: Tworzenie standardowych funkcji platformy .NET dla zadań usługi Azure Stream Analytics (wersja zapoznawcza)
description: Dowiedz się, jak napisać funkcje zdefiniowane przez użytkownika w języku c# dla zadań usługi Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426314"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Tworzenie standardowych funkcji zdefiniowanych przez użytkownika platformy .NET dla zadań usługi Azure Stream Analytics (wersja zapoznawcza)

Usługa Azure Stream Analytics oferuje język zapytań podobny do języka SQL do wykonywania przekształceń i obliczeń za pośrednictwem strumieni danych zdarzeń. Istnieje wiele wbudowanych funkcji, ale niektóre złożone scenariusze wymagają dodatkowej elastyczności. Za pomocą funkcji .NET Standard zdefiniowanych przez użytkownika (UDF) można wywoływać własne funkcje napisane w dowolnym standardowym języku platformy .NET (C#, F#itp.), aby rozszerzyć język zapytań usługi Stream Analytics. Pliki UDF umożliwiają wykonywanie złożonych obliczeń matematycznych, importowanie niestandardowych modeli uczenia maszynowego przy użyciu ML.NET i używanie niestandardowej logiki przypisywania brakujących danych. Funkcja UDF dla zadań usługi Stream Analytics jest obecnie w wersji zapoznawczej i nie powinna być używana w obciążeniach produkcyjnych.

Funkcja zdefiniowana przez użytkownika platformy .NET dla zadań w chmurze jest dostępna w:
* Zachodnio-środkowe stany USA
* Europa Północna
* Wschodnie stany USA
* Zachodnie stany USA
* Wschodnie stany USA 2
* Europa Zachodnia

Jeśli chcesz korzystać z tej funkcji w innym regionie, możesz [poprosić o dostęp](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Omówienie
Narzędzia programu Visual Studio dla usługi Azure Stream Analytics ułatwiają pisanie plików UDF, testowanie zadań lokalnie (nawet w trybie offline) i publikowanie zadania usługi Stream Analytics na platformie Azure. Po opublikowaniu na platformie Azure można wdrożyć zadanie na urządzeniach IoT przy użyciu usługi IoT Hub.

Istnieją trzy sposoby implementowania funkcji zdefiniowanych przez użytkownika:

* Pliki CodeBehind w projekcie ASA
* UDF z lokalnego projektu
* Istniejący pakiet z konta magazynu platformy Azure

## <a name="package-path"></a>Ścieżka pakietu

Format dowolnego pakietu UDF ma `/UserCustomCode/CLR/*`ścieżkę . Biblioteki łączy dynamicznych (DLL) i zasoby `/UserCustomCode/CLR/*` są kopiowane w folderze, co pomaga izolować biblioteki DLL użytkowników od bibliotek DLL systemu i usługi Azure Stream Analytics. Ta ścieżka pakietu jest używana dla wszystkich funkcji, niezależnie od metody używanej do ich wykorzystania.

## <a name="supported-types-and-mapping"></a>Obsługiwane typy i mapowanie

|**Typ UDF (C#)**  |**Typ usługi Azure Stream Analytics**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|ciąg  |  Nvarchar(max)   |
|Data i godzina  |  Data i godzina   |
|struktura   |  IRecord ( IRecord )   |
|obiekt  |  IRecord ( IRecord )   |
|>\<obiektu tablicy  |  IArray (IArray)   |
|<słownik,> obiektów  |  IRecord ( IRecord )   |

## <a name="codebehind"></a>KodZachodnie
Funkcje zdefiniowane przez użytkownika można zapisać w **pliku Script.asql** CodeBehind. Narzędzia programu Visual Studio automatycznie skompilują plik CodeBehind do pliku zestawu. Zestawy są pakowane jako plik zip i przekazywane do konta magazynu podczas przesyłania zadania do platformy Azure. Możesz dowiedzieć się, jak napisać C# UDF przy użyciu CodeBehind, wykonując [C# UDF dla usługi Stream Analytics Edge zadania](stream-analytics-edge-csharp-udf.md) samouczka. 

## <a name="local-project"></a>Projekt lokalny
Funkcje zdefiniowane przez użytkownika mogą być zapisywane w zestawie, do którego później odwołuje się zapytanie usługi Azure Stream Analytics. Jest to zalecana opcja dla złożonych funkcji, które wymagają pełnej mocy języka .NET Standard poza jego język wyrażenia, takich jak logiki proceduralnej lub rekursji. Pliki UDF z projektu lokalnego mogą być również używane, gdy trzeba udostępnić logikę funkcji w kilku zapytaniach usługi Azure Stream Analytics. Dodawanie plików UDF do projektu lokalnego umożliwia debugowanie i testowanie funkcji lokalnie z programu Visual Studio.

Aby odwołać się do projektu lokalnego:

1. Utwórz nową bibliotekę klas w rozwiązaniu.
2. Wpisz kod w swojej klasie. Należy pamiętać, że klasy muszą być zdefiniowane jako *publiczne,* a obiekty muszą być zdefiniowane jako *statyczne.* 
3. Skompilowanie projektu. Narzędzia będą pakować wszystkie artefakty w folderze bin do pliku zip i przekazywać plik zip do konta magazynu. W przypadku odwołań zewnętrznych należy użyć odwołania do zestawu zamiast pakietu NuGet.
4. Odwołaj się do nowej klasy w projekcie usługi Azure Stream Analytics.
5. Dodaj nową funkcję w projekcie usługi Azure Stream Analytics.
6. Skonfiguruj ścieżkę złożenia `JobConfig.json`w pliku konfiguracji zadania, . Ustaw ścieżkę zestawu na **Lokalne odwołanie do projektu lub CodeBehind**.
7. Odbuduj zarówno projekt funkcji, jak i projekt usługi Azure Stream Analytics.  

### <a name="example"></a>Przykład

W tym przykładzie **UDFTest** jest projekt biblioteki klasy C#, a **ASAUDFDemo** jest projektem usługi Azure Stream Analytics, który odwołuje się do **testu UDFTest.**

![Projekt usługi Azure Stream Analytics w usłudze IoT Edge w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Skompiluj swój projekt C#, który pozwoli Ci dodać odwołanie do konta UDF języka C# z kwerendy usługi Azure Stream Analytics.
    
   ![Tworzenie projektu usługi Azure Stream Analytics w usłudze Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Dodaj odwołanie do projektu C# w projekcie ASA. Kliknij prawym przyciskiem myszy węzeł Odwołania i wybierz polecenie Dodaj odwołanie.

   ![Dodawanie odwołania do projektu języka C# w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Wybierz nazwę projektu C# z listy. 
    
   ![Wybierz nazwę projektu w języku C# z listy odwołań](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. W Eksploratorze **rozwiązań** powinien zostać wyświetlony **test UDFTest** wymieniony w obszarze Odwołania w **Eksploratorze rozwiązań**.

   ![Wyświetlanie odwołania do funkcji zdefiniowanej przez użytkownika w Eksploratorze rozwiązań](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Kliknij prawym przyciskiem myszy folder **Funkcje** i wybierz polecenie **Nowy element**.

   ![Dodawanie nowego elementu do funkcji w rozwiązaniu usługi Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Dodaj funkcję C# **SquareFunction.json** do projektu usługi Azure Stream Analytics.

   ![Wybierz funkcję CSharp z elementów usługi Stream Analytics Edge w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Kliknij dwukrotnie funkcję w **Eksploratorze rozwiązań,** aby otworzyć okno dialogowe konfiguracji.

   ![Konfiguracja funkcji C sharp w programie Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. W konfiguracji funkcji Języka C# wybierz pozycję **Załaduj z odwołania do projektu ASA** i powiązane nazwy zestawu, klasy i metody z listy rozwijanej. Aby odwołać się do metod, typów i funkcji w zapytaniu usługi Stream Analytics, klasy muszą być zdefiniowane jako *publiczne,* a obiekty muszą być zdefiniowane jako *statyczne.*

   ![Konfiguracja funkcji ostrych usługi Usługi Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Istniejące pakiety

Można tworzyć pliki .NET Standard UDFs w dowolnym poju wybranym interfejsie IDE i wywoływać je z kwerendy usługi Azure Stream Analytics. Najpierw skompiluj kod i zapakuj wszystkie biblioteki DLL. Format pakietu ma ścieżkę `/UserCustomCode/CLR/*`. Następnie przekaż `UserCustomCode.zip` do katalogu głównego kontenera na koncie usługi Azure storage.

Po podaniu pakietów zip zestawu do konta usługi Azure storage, można użyć funkcji w kwerendach usługi Azure Stream Analytics. Wszystko, co musisz zrobić, to uwzględnić informacje o magazynie w konfiguracji zadania usługi Stream Analytics. Nie można przetestować funkcji lokalnie z tej opcji, ponieważ narzędzia programu Visual Studio nie pobierze pakietu. Ścieżka pakietu jest analizowana bezpośrednio do usługi. 

Aby skonfigurować ścieżkę złożenia w `JobConfig.json`pliku konfiguracji zadania, :

Rozwiń sekcję **Konfiguracja kodu skonfigurowana przez użytkownika**, a następnie wypełnij konfigurację za pomocą następujących sugerowanych wartości:

   |**Ustawienie**|**Sugerowana wartość**|
   |-------|---------------|
   |Zasób globalnych ustawień magazynu|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja globalnych ustawień magazynu| < > subskrypcji|
   |Konto magazynu ustawień magazynu globalnego| < > konta pamięci masowej|
   |Zasób niestandardowych ustawień magazynu kodu|Wybierz źródło danych z bieżącego konta|
   |Konto magazynu niestandardowych ustawień magazynu kodu|< > konta pamięci masowej|
   |Kontener niestandardowych ustawień przechowywania kodu|< > kontenera magazynu|
   |Źródło zestawu kodu niestandardowego|Istniejące pakiety montażowe z chmury|
   |Źródło zestawu kodu niestandardowego|UserCustomCode.zip|

## <a name="limitations"></a>Ograniczenia
Podgląd UDF ma obecnie następujące ograniczenia:

* .NET Standardowe pliki UDF można tworzyć tylko w programie Visual Studio i publikować na platformie Azure. Wersje tylko do odczytu .NET standardowe pliki UDF można wyświetlać w obszarze **Funkcje** w witrynie Azure portal. Tworzenie funkcji .NET Standard nie jest obsługiwane w witrynie Azure portal.

* Edytor zapytań portalu Azure pokazuje błąd podczas korzystania z platformy .NET Standard UDF w portalu. 

* Ponieważ kod niestandardowy udostępnia kontekst z aparatem usługi Azure Stream Analytics, kod niestandardowy nie może odwoływać się do niczego, co ma konfliktowy obszar nazw/dll_name z kodem usługi Azure Stream Analytics. Na przykład nie można odwoływać się do *Newtonsoft Json*.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Napisz funkcję zdefiniowaną przez użytkownika w języku C# dla zadania usługi Azure Stream Analytics (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Samouczek: funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)

---
title: Samouczek — niestandardowe deserializatory platformy .NET dla zadań w chmurze usługi Azure Stream Analytics
description: W tym samouczku pokazano, jak utworzyć niestandardowy deserializer .NET dla zadania chmury usługi Azure Stream Analytics przy użyciu programu Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75443696"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Samouczek: Niestandardowe deserializatory platformy .NET dla usługi Azure Stream Analytics

Usługa Azure Stream Analytics ma [wbudowaną obsługę trzech formatów danych:](stream-analytics-parsing-json.md)JSON, CSV i Avro. Za pomocą niestandardowych deserializatorów platformy .NET można odczytywać dane z innych formatów, takich jak [Bufor protokołu,](https://developers.google.com/protocol-buffers/) [Obligacje](https://github.com/Microsoft/bond) i inne formaty zdefiniowane przez użytkownika dla zadań chmury i krawędzi.

W tym samouczku pokazano, jak utworzyć niestandardowy deserializer .NET dla zadania chmury usługi Azure Stream Analytics przy użyciu programu Visual Studio. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie niestandardowego deserializer dla buforu protokołu.
> * Utwórz zadanie usługi Azure Stream Analytics w programie Visual Studio.
> * Skonfiguruj zadanie usługi Stream Analytics, aby używać niestandardowego deserializatora.
> * Uruchom zadanie usługi Stream Analytics lokalnie, aby przetestować niestandardowy deserializer.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Zainstaluj [program Visual Studio 2017](https://www.visualstudio.com/downloads/) lub Visual Studio [2015](https://www.visualstudio.com/vs/older-downloads/). Obsługiwane są wersje Enterprise (Ultimate/Premium), Professional i Community. Wersja ekspresowa nie jest obsługiwana.

* [Zainstaluj narzędzia analizy strumienia dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md) lub zaktualizuj do najnowszej wersji. Obsługiwane są następujące wersje programu Visual Studio:
   * Visual Studio 2015
   * Visual Studio 2017

* Otwórz **Cloud Explorer** w programie Visual Studio i zaloguj się do subskrypcji platformy Azure.

* Utwórz kontener na koncie usługi Azure Storage.
Utworzony kontener będzie używany do przechowywania zasobów związanych z twoim zadaniem usługi Stream Analytics. Jeśli masz już konto magazynu z istniejącymi kontenerami, możesz go użyć. Jeśli nie, musisz [utworzyć nowy kontener](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Tworzenie niestandardowego deserializatora

1. Otwórz program Visual Studio i wybierz **pozycję Plik > Nowy projekt >**. Wyszukaj **usługę Stream Analytics** i wybierz **niestandardowy projekt deserializera usługi Azure Stream Analytics (.NET).** Nadaj projektowi nazwę, taką jak **Protobuf Deserializer**.

   ![Tworzenie projektu biblioteki klas standardowych dotnet programu Visual Studio](./media/custom-deserializer/create-dotnet-library-project.png)

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **Protobuf Deserializer** i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. Następnie zainstaluj pakiety **Microsoft.Azure.StreamAnalytics** i **Google.Protobuf** NuGet.

3. Dodaj [MessageBodyProto klasy](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) i [MessageBodyDeserializer klasy](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) do projektu.

4. Zbuduj projekt **Protobuf Deserializer.**

## <a name="add-an-azure-stream-analytics-project"></a>Dodawanie projektu usługi Azure Stream Analytics

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie **Protobuf Deserializer** i wybierz polecenie **Dodaj > nowego projektu**. W obszarze **Usługa Azure Stream Analytics > stream analytics**wybierz pozycję Usługa Azure Stream Analytics **.** Nazwij go **ProtobufCloudDeserializer** i wybierz **OK**. 

2. Kliknij prawym przyciskiem myszy **odwołania** w ramach projektu **ProtobufCloudDeserializer** Azure Stream Analytics. W obszarze **Projekty**dodaj **Protobuf Deserializer**. Powinien być automatycznie wypełniany dla Ciebie.

## <a name="configure-a-stream-analytics-job"></a>Konfigurowanie zadania usługi Stream Analytics

1. Kliknij dwukrotnie **przycisk JobConfig.json**. Użyj konfiguracji domyślnych, z wyjątkiem następujących ustawień:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Zasób globalnych ustawień magazynu|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja globalnych ustawień magazynu| < > subskrypcji|
   |Konto magazynu ustawień magazynu globalnego| < > konta pamięci masowej|
   |Zasób niestandardowych ustawień magazynu kodu|Wybierz źródło danych z bieżącego konta|
   |Konto magazynu niestandardowych ustawień magazynu kodu|< > konta pamięci masowej|
   |Kontener niestandardowych ustawień przechowywania kodu|< > kontenera magazynu|

2. W obszarze **Wejścia**kliknij dwukrotnie pozycję **Input.json**. Użyj konfiguracji domyślnych, z wyjątkiem następujących ustawień:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Element źródłowy|Blob Storage|
   |Zasób|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja|< > subskrypcji|
   |Konto magazynu|< > konta pamięci masowej|
   |Kontener|< > kontenera magazynu|
   |Format serializacji zdarzeń|Inne (Protobuf, XML, zastrzeżone...)|
   |Zasób|Obciążenie z odwołania do projektu ASA lub CodeBehind|
   |Nazwa zestawu CSharp|ProtobufDeserializer.dll|
   |Nazwa klasy|MessageBodyProto.MessageBodyDeserializer|
   |Typ kompresji zdarzeń|Brak|

3. Dodaj następującą kwerendę do pliku **Script.asaql.**

   ```sql
   SELECT * FROM Input
   ```

4. Pobierz [przykładowy plik wejściowy protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). W folderze **Wejścia** kliknij prawym przyciskiem myszy **pozycję Input.json** i wybierz polecenie **Dodaj dane wejściowe lokalne**. Następnie kliknij dwukrotnie **local_Input.json** i skonfiguruj następujące ustawienia:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Alias danych wejściowych|Dane wejściowe|
   |Typ źródła|Strumień danych|
   |Format serializacji zdarzeń|Inne (Protobuf, XML, zastrzeżone...)|
   |Nazwa zestawu CSharp|ProtobufDeserializer.dll|
   |Nazwa klasy|MessageBodyProto.MessageBodyDeserializer|
   |Ścieżka lokalnego pliku wejściowego|< ścieżkę pliku pobranego przykładowego pliku wejściowego protobuf>|

## <a name="execute-the-stream-analytics-job"></a>Wykonywanie zadania usługi Stream Analytics

1. Otwórz **plik Script.asaql** i wybierz pozycję **Uruchom lokalnie**.

2. Obserwuj wyniki w **wynikach uruchamiania lokalnego analizy strumieniowej**.

Pomyślnie zaimplementowano niestandardowy deserializer dla zadania usługi Stream Analytics! W tym samouczku przetestowano niestandardowy deserializer lokalnie. Dla rzeczywistych danych należy poprawnie skonfigurować dane wejściowe i wyjściowe. Następnie prześlij zadanie do platformy Azure z programu Visual Studio, aby uruchomić zadanie w chmurze przy użyciu niestandardowego deserializatora, który właśnie zaimplementowałeś.

## <a name="debug-your-deserializer"></a>Debugowanie deserializatora

Deserializator .NET można debugować lokalnie w taki sam sposób, w jaki debugujesz standardowy kod .NET. 

1. Dodaj punkty przerwania w funkcji.

2. Naciśnij klawisz **F5**, aby uruchomić debugowanie. Zgodnie z oczekiwaniami program będzie zatrzymywać się w punktach przerwania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz je zatrzymać i uruchomić ponownie później, gdy będzie potrzebne. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz nazwę utworzonego zasobu.  

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak zaimplementować niestandardowy deserializer .NET dla serializacji wejściowej buforu protokołu. Aby dowiedzieć się więcej na temat tworzenia deserializatorów niestandardowych, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> [Tworzenie różnych deserializatorów platformy .NET dla zadań usługi Azure Stream Analytics](custom-deserializer-examples.md)

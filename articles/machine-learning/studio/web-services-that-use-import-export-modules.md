---
title: Importowanie/Eksportowanie danych w usługach sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Dowiedz się, jak korzystać z modułów danych importowanie i eksportowanie danych do wysyłania i odbierania danych z usługi sieci web.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 144a3bc0d9e0499a238e4033d37d5e4d3fa61e05
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204066"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Wdrażanie usług sieci Web Azure Machine Learning Studio (klasycznych), które używają modułów importowania danych i eksportu danych

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Podczas tworzenia eksperyment predykcyjny zazwyczaj dodajesz usługi sieci web w danych wejściowych i wyjściowych. Podczas wdrażania doświadczenia użytkowników umożliwia wysyłanie oraz odbieranie danych z usługi sieci web za pośrednictwem dane wejściowe i wyjściowe. W niektórych aplikacjach dane klienta mogą być dostępne ze strumieniowego źródła danych lub już znajdują się w zewnętrznym źródle danych takich jak usługi Azure Blob storage. W takiej sytuacji nie ma potrzeby odczytywanie i zapisywanie danych przy użyciu usługi sieci web w danych wejściowych i wyjściowych. Mogą, zamiast tego odczytywanie danych ze źródła danych za pomocą modułu importu danych przy użyciu usługi Batch Execution Service (BES) i zapisać wyniki oceny lokalizacji różnych danych, przy użyciu modułu Eksport danych.

Importowanie danych i moduły danych eksportu, może odczytywać i zapisywać różne dane podane przez lokalizacje, takie jak adres URL sieci Web za pośrednictwem protokołu HTTP, zapytanie Hive, bazy danych Azure SQL, Azure Table storage, usługi Azure Blob storage, strumieniowego źródła danych lub bazą danych SQL database w środowisku lokalnym.

W tym temacie używany "przykład 5: oceń szkolenia i testowania dla klasyfikacji binarnej: treści dla dorosłych zestawu danych" przykładowy i zakłada się, zestaw danych został już załadowany do tabeli Azure SQL o nazwie censusdata.

## <a name="create-the-training-experiment"></a>Tworzenie eksperymentu szkolenia
Po otwarciu "przykład 5: oceń szkolenia i testowania dla klasyfikacji binarnej: treści dla dorosłych zestawu danych" przykład używa przykładowego zestawu danych treści dla dorosłych klasyfikacji binarnej dochodu spisu. I doświadczenia w obszarze roboczym będzie wyglądać podobnie do poniższej ilustracji:

![Wstępna konfiguracja eksperymentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Do odczytywania danych z tabeli Azure SQL:

1. Usuń moduł zestawu danych.
2. W polu wyszukiwania składniki typu importu.
3. Z listy wyników Dodaj moduł *Importuj dane* do kanwy eksperymentu.
4. Połącz dane wyjściowe modułu *Importuj dane* dane wejściowe modułu *czyste brakujące dane* .
5. W okienku właściwości wybierz pozycję **Azure SQL Database** na liście rozwijanej **Źródło danych** .
6. W polach **Nazwa serwera bazy danych**, **Nazwa bazy danych**, **Nazwa użytkownika**i **hasło** wprowadź odpowiednie informacje dotyczące bazy danych.
7. W polu zapytania bazy danych wprowadź następujące zapytanie.

     Wybierz pozycję [Wiek]

        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     z dbo.censusdata;
8. W dolnej części kanwy eksperymentu kliknij pozycję **Uruchom**.

## <a name="create-the-predictive-experiment"></a>Utworzyć eksperyment predykcyjny
Następnie należy skonfigurować eksperyment predykcyjny, z której można wdrożyć usługi sieci web.

1. W dolnej części kanwy eksperymentu kliknij pozycję **Skonfiguruj usługę sieci Web** i wybierz opcję **predykcyjna usługa sieci Web [zalecane]** .
2. Usuń moduły *danych wejściowych* i *usług* internetowych usługi sieci Web z eksperymentu predykcyjnego.
3. W polu wyszukiwania składniki typu eksportu.
4. Z listy wyników Dodaj moduł *eksportu danych* do kanwy eksperymentu.
5. Połącz dane wyjściowe modułu *wynik model* dane wejściowe modułu *eksport danych* .
6. W okienku właściwości wybierz pozycję **Azure SQL Database** na liście rozwijanej miejsce docelowe danych.
7. W polu **Nazwa serwera bazy danych**, **Nazwa bazy danych**, **nazwa konta użytkownika serwera**i **hasło konta użytkownika serwera** wprowadź odpowiednie informacje dotyczące bazy danych.
8. Na **liście rozdzielanych przecinkami kolumn, które mają być zapisane** pole wpisz etykiety z wynikami.
9. W **polu Nazwa tabeli danych**wpisz dbo. ScoredLabels. Jeśli tabela nie istnieje, jest tworzony, po uruchomieniu eksperymentu lub nosi nazwę usługi sieci web.
10. Na **liście rozdzielanej przecinkami pól kolumny DataTable** wpisz ScoredLabels.

Podczas pisania aplikacji, która wywołuje usługę sieci web końcowy, można określić inne zapytanie wejściowe lub docelowej tabeli w czasie wykonywania. Aby skonfigurować te dane wejściowe i wyjściowe, użyj funkcji parametrów usługi sieci Web, aby ustawić właściwość *Źródło danych* modułu *Import* danych i Właściwość docelowa eksport danych trybu *dane* .  Aby uzyskać więcej informacji na temat parametrów usługi sieci Web, zobacz [wpis Azure Machine Learning Studio usługi sieci Web](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) w blogu Cortana Intelligence i Machine Learning.

Aby skonfigurować parametry usługi sieci Web dla zapytań import i tabela docelowa:

1. W okienku właściwości modułu *Importuj dane* kliknij ikonę w prawym górnym rogu pola **kwerendy bazy danych** i wybierz pozycję **Ustaw jako parametr usługi sieci Web**.
2. W okienku właściwości modułu *eksport danych* kliknij ikonę w prawym górnym rogu pola **Nazwa tabeli danych** i wybierz pozycję **Ustaw jako parametr usługi sieci Web**.
3. W dolnej części okienka właściwości modułu *eksport danych* w sekcji **Parametry usługi sieci Web** kliknij pozycję kwerenda bazy danych i Zmień nazwę zapytania.
4. Kliknij pozycję **Nazwa tabeli danych** i zmień jej nazwę na **tabelę**.

Gdy wszystko będzie gotowe, eksperyment powinien wyglądać podobnie do poniższej ilustracji:

![Końcowy wygląd eksperymentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Teraz można wdrożyć eksperymentu jako usługę sieci web.

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web
Można wdrożyć do klasycznego lub nowej usługi sieci web.

### <a name="deploy-a-classic-web-service"></a>Wdrażanie usługi sieci Web
Aby wdrożyć jako klasycznej usługi sieci Web i tworzenie aplikacji z jego:

1. W dolnej części obszaru roboczego eksperymentu kliknij przycisk Uruchom.
2. Po zakończeniu przebiegu kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [klasyczny]** .
3. Zlokalizuj swój klucz interfejsu API na pulpicie nawigacyjnym usługi sieci web. Skopiuj i zapisz go do późniejszego użycia.
4. W **domyślnej tabeli punktów końcowych** kliknij link **wykonywania wsadowego** , aby otworzyć stronę pomocy interfejsu API.
5. W programie Visual Studio Utwórz aplikację C# konsolową: **New** > **Project** > **Visual C#**  > **Windows Classic Desktop** > **aplikacji konsolowej (.NET Framework)** .
6. Na stronie Pomoc interfejsu API Znajdź **przykładową sekcję kod** w dolnej części strony.
7. Skopiuj i Wklej C# przykładowego kodu do pliku Program.cs i usunąć wszystkie odwołania do usługi blob storage.
8. Zaktualizuj wartość zmiennej *apiKey* z kluczem interfejsu API zapisanym wcześniej.
9. Znajdź deklarację żądania i zaktualizuj wartości parametrów usługi sieci Web, które są przesyłane do modułów *Importuj dane* i *Eksportuj dane* . W tym przypadku użyj oryginalne zapytanie, ale zdefiniowanie nowej nazwy tabeli.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Uruchom aplikację.

Po zakończeniu przebiegu zostanie dodana nowa tabela, z bazą danych zawierającą wyniki oceny.

### <a name="deploy-a-new-web-service"></a>Wdrażanie nowej usługi sieci Web

> [!NOTE]
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci web Azure Machine Learning](manage-new-webservice.md).

Aby wdrożyć jako nową usługę sieci Web i tworzenie aplikacji z jego:

1. W dolnej części kanwy eksperymentu kliknij pozycję **Uruchom**.
2. Po zakończeniu przebiegu kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [New]** .
3. Na stronie wdrażanie eksperymentu wprowadź nazwę usługi sieci Web i wybierz plan cenowy, a następnie kliknij przycisk **Wdróż**.
4. Na stronie **Szybki Start** **kliknij pozycję**Użyj.
5. W sekcji **przykładowy kod** kliknij pozycję **Batch**.
6. W programie Visual Studio Utwórz aplikację C# konsolową: **New** > **Project** > **Visual C#**  > **Windows Classic Desktop** > **aplikacji konsolowej (.NET Framework)** .
7. Skopiuj i Wklej C# przykładowego kodu do pliku Program.cs.
8. Zaktualizuj wartość zmiennej *apiKey* przy użyciu **klucza podstawowego** znajdującego się w sekcji **podstawowe informacje o zużyciu** .
9. Znajdź deklarację *scoreRequest* i zaktualizuj wartości parametrów usługi sieci Web, które są przesyłane do modułów *Importuj dane* i *Eksportuj dane* . W tym przypadku użyj oryginalne zapytanie, ale zdefiniowanie nowej nazwy tabeli.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Uruchom aplikację.


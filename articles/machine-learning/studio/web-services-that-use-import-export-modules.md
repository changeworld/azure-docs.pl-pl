---
title: Import/Eksport danych w sieci web services — Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z modułów danych importowanie i eksportowanie danych do wysyłania i odbierania danych z usługi sieci web.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 28d16bce6dbb5063c085e8c4393777ee9d152768
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345145"
---
# <a name="deploy-azure-machine-learning-studio-web-services-that-use-data-import-and-data-export-modules"></a>Wdrażaj usługi sieci web Azure Machine Learning Studio, korzystające z modułów importu i eksportu danych

Podczas tworzenia eksperyment predykcyjny zazwyczaj dodajesz usługi sieci web w danych wejściowych i wyjściowych. Podczas wdrażania doświadczenia użytkowników umożliwia wysyłanie oraz odbieranie danych z usługi sieci web za pośrednictwem dane wejściowe i wyjściowe. W niektórych aplikacjach dane klienta mogą być dostępne ze strumieniowego źródła danych lub już znajdują się w zewnętrznym źródle danych takich jak usługi Azure Blob storage. W takiej sytuacji nie ma potrzeby odczytywanie i zapisywanie danych przy użyciu usługi sieci web w danych wejściowych i wyjściowych. Mogą, zamiast tego odczytywanie danych ze źródła danych za pomocą modułu importu danych przy użyciu usługi Batch Execution Service (BES) i zapisać wyniki oceny lokalizacji różnych danych, przy użyciu modułu Eksport danych.

Importowanie danych i moduły danych eksportu, może odczytywać i zapisywać różne dane podane przez lokalizacje, takie jak adres URL sieci Web za pośrednictwem protokołu HTTP, zapytanie Hive, bazy danych Azure SQL, Azure Table storage, usługi Azure Blob storage, strumieniowego źródła danych lub bazą danych SQL database w środowisku lokalnym.

W tym temacie używany "przykład 5: Szkolenie, testowanie, oceny Klasyfikacja binarna: Przykładowy zestaw danych treści dla dorosłych"i zakłada się, zestaw danych został już załadowany do tabeli Azure SQL o nazwie censusdata.

## <a name="create-the-training-experiment"></a>Tworzenie eksperymentu szkolenia
Po otwarciu "przykład 5: Szkolenie, testowanie, oceny Klasyfikacja binarna: Przykład treści dla dorosłych zestaw danych"używa przykładowego zestawu danych treści dla dorosłych klasyfikacji binarnej dochodu spisu. I doświadczenia w obszarze roboczym będzie wyglądać podobnie do poniższej ilustracji:

![Wstępna konfiguracja eksperymentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Do odczytywania danych z tabeli Azure SQL:

1. Usuń moduł zestawu danych.
2. W polu wyszukiwania składniki typu importu.
3. Z listy wyników, należy dodać *importu danych* modułu do obszaru roboczego eksperymentu.
4. Połącz dane wyjściowe *importu danych* modułu danych wejściowych z *Clean Missing Data* modułu.
5. W okienku właściwości wybierz **usługi Azure SQL Database** w **źródła danych** listy rozwijanej.
6. W **nazwy serwera bazy danych**, **Nazwa bazy danych**, **nazwy użytkownika**, i **hasło** wprowadź odpowiednie informacje o sieci Baza danych.
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
8. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **Uruchom**.

## <a name="create-the-predictive-experiment"></a>Utworzyć eksperyment predykcyjny
Następnie należy skonfigurować eksperyment predykcyjny, z której można wdrożyć usługi sieci web.

1. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **predykcyjne usługi sieci Web [zalecane]** .
2. Usuń *dane wejściowe usługi sieci Web* i *moduły danych wyjściowych usługi sieci Web* z eksperyment predykcyjny.
3. W polu wyszukiwania składniki typu eksportu.
4. Z listy wyników, należy dodać *Eksport danych* modułu do obszaru roboczego eksperymentu.
5. Połącz dane wyjściowe *Score Model* modułu danych wejściowych z *Eksport danych* modułu.
6. W okienku właściwości wybierz **usługi Azure SQL Database** na liście rozwijanej miejsce docelowe danych.
7. W **nazwy serwera bazy danych**, **Nazwa bazy danych**, **nazwę konta użytkownika serwera**, i **hasło konta użytkownika serwera** wprowadź odpowiednie informacje dla bazy danych.
8. W **rozdzielaną przecinkami listę kolumn, które mają być zapisywane** wpisz sklasyfikowane etykiety.
9. W **pole Nazwa tabeli danych**, wpisz dbo. ScoredLabels. Jeśli tabela nie istnieje, jest tworzony, po uruchomieniu eksperymentu lub nosi nazwę usługi sieci web.
10. W **rozdzielaną przecinkami listę kolumn tabeli datatable** wpisz ScoredLabels.

Podczas pisania aplikacji, która wywołuje usługę sieci web końcowy, można określić inne zapytanie wejściowe lub docelowej tabeli w czasie wykonywania. Aby skonfigurować te dane wejściowe i wyjściowe, należy użyć funkcji parametry usługi sieci Web można ustawić *importu danych* modułu *źródła danych* właściwości i *Eksport danych* dane trybu Właściwość docelowego.  Aby uzyskać więcej informacji na temat parametrów usługi sieci Web, zobacz [usługi Azure Machine Learning studio parametry usługi sieci Web wpisu](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) pakietu Cortana Intelligence i blogu dotyczącym uczenia maszynowego.

Aby skonfigurować parametry usługi sieci Web dla zapytań import i tabela docelowa:

1. W okienku właściwości *importu danych* modułu, kliknij ikonę u góry po prawej stronie **zapytanie bazy danych** pola, a następnie wybierz **ustawiony jako parametr usługi sieci web**.
2. W okienku właściwości *Eksport danych* modułu, kliknij ikonę u góry po prawej stronie **Nazwa tabeli danych** pola, a następnie wybierz **ustawiony jako parametr usługi sieci web**.
3. W dolnej części *Eksport danych* okienka właściwości modułu w **parametry usługi sieci Web** sekcji kliknij zapytanie bazy danych i zmień jej nazwę zapytania.
4. Kliknij przycisk **Nazwa tabeli danych** i zmień jego nazwę **tabeli**.

Gdy wszystko będzie gotowe, eksperyment powinien wyglądać podobnie do poniższej ilustracji:

![Końcowy wygląd eksperymentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Teraz można wdrożyć eksperymentu jako usługę sieci web.

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web
Można wdrożyć do klasycznego lub nowej usługi sieci web.

### <a name="deploy-a-classic-web-service"></a>Wdrażanie usługi sieci Web
Aby wdrożyć jako klasycznej usługi sieci Web i tworzenie aplikacji z jego:

1. W dolnej części obszaru roboczego eksperymentu kliknij przycisk Uruchom.
2. Gdy działanie zostało zakończone, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** .
3. Zlokalizuj swój klucz interfejsu API na pulpicie nawigacyjnym usługi sieci web. Skopiuj i zapisz go do późniejszego użycia.
4. W **domyślny punkt końcowy** tabelę, kliknij przycisk **wykonywanie wsadowe** link, aby otworzyć stronę pomocy interfejsu API.
5. W programie Visual Studio, należy utworzyć C# konsoli aplikacji: **Nowe** > **projektu** > **Visual C#**   >  **Windows Classic Desktop**  >   **Aplikacja konsoli (.NET Framework)** .
6. Na stronie pomocy interfejsu API, należy znaleźć **przykładowy kod** sekcji w dolnej części strony.
7. Skopiuj i Wklej C# przykładowego kodu do pliku Program.cs i usunąć wszystkie odwołania do usługi blob storage.
8. Zaktualizuj wartość *apiKey* zmiennej za pomocą klucza interfejsu API, zapisany wcześniej.
9. Znajdź zgłoszenie żądania i zaktualizuj wartości parametrów usługi sieci Web, które są przekazywane do *importu danych* i *Eksport danych* modułów. W tym przypadku użyj oryginalne zapytanie, ale zdefiniowanie nowej nazwy tabeli.

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
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md).

Aby wdrożyć jako nową usługę sieci Web i tworzenie aplikacji z jego:

1. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **Uruchom**.
2. Gdy działanie zostało zakończone, kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [New]** .
3. Na stronie wdrażanie eksperymentu, wprowadź nazwę usługi sieci web i wybrać planu cenowego, a następnie kliknij przycisk **Wdróż**.
4. Na **Szybki Start** kliknij **zużywania**.
5. W **przykładowy kod** kliknij **partii**.
6. W programie Visual Studio, należy utworzyć C# konsoli aplikacji: **Nowe** > **projektu** > **Visual C#**   >  **Windows Classic Desktop**  >   **Aplikacja konsoli (.NET Framework)** .
7. Skopiuj i Wklej C# przykładowego kodu do pliku Program.cs.
8. Zaktualizuj wartość *apiKey* zmiennej za pomocą **klucza podstawowego** znajduje się w **informacje podstawowe użycie** sekcji.
9. Znajdź *scoreRequest* deklaracji i zaktualizuj wartości parametrów usługi sieci Web, które są przekazywane do *importu danych* i *Eksport danych* modułów. W tym przypadku użyj oryginalne zapytanie, ale zdefiniowanie nowej nazwy tabeli.

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


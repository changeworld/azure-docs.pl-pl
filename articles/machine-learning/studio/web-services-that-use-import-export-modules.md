---
title: Przy użyciu danych importu/eksportu w usługach sieci web Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać modułów danych importowanie i eksportowanie danych do wysyłania i odbierania danych z usługi sieci web.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 27873930ebef75923088f8bf2170c8e6a383cfa8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837416"
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Wdrażanie usług sieci Web Azure ML używających modułów importu i eksportu danych

Po utworzeniu eksperyment predykcyjny zazwyczaj są dodawane usługi sieci web w danych wejściowych i wyjściowych. Podczas wdrażania eksperyment konsumentów można wysyłać i odbierać dane z usługi sieci web za pośrednictwem danych wejściowych i wyjściowych. Dla pewnych aplikacji danych klientów mogą być dostępne ze strumieniowego źródła danych lub już znajdują się w źródle danych zewnętrznych, takie jak magazyn obiektów Blob platformy Azure. W takich przypadkach nie potrzebują oni odczytywanie i zapisywanie danych przy użyciu sieci web usługi wejścia i wyjścia. Mogą, zamiast tego użyć usługi wykonywania wsadowego (BES) można odczytać danych ze źródła danych za pomocą modułu importowanie danych i zapisać wyników oceniania do lokalizacji innych danych za pomocą modułu eksportu danych.

Importowanie danych i moduły danych eksportu, można z do odczytu i zapisu różne dane, na przykład adres URL sieci Web za pośrednictwem protokołu HTTP, zapytanie Hive, bazy danych Azure SQL, Magazyn tabel Azure, magazynu obiektów Blob platformy Azure, strumieniowego źródła danych zawierają lub lokalną bazą danych SQL.

Używa tego tematu "próbki 5: Evaluate pociągu, testu, klasyfikacji binarnej: dla dorosłych zestawu danych" Przykładowe przyjęto założenie, zestaw danych został już załadowany do tabeli Azure SQL o nazwie censusdata.

## <a name="create-the-training-experiment"></a>Tworzenie eksperymentu szkolenia
Po otwarciu "próbki 5: Evaluate pociągu, testu, klasyfikacji binarnej: dla dorosłych zestawu danych" próbki używa dla dorosłych klasyfikacji binarnej dochodu spisu przykładowego zestawu danych. I doświadczenia w obszarze roboczym będzie wyglądać podobnie do poniższej ilustracji:

![Wstępna konfiguracja eksperymentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Odczytywanie danych z tabeli Azure SQL:

1. Usuń moduł zestawu danych.
2. W polu wyszukiwania składników typu importu.
3. Z listy wyników, należy dodać *i zaimportuj dane* modułu do obszaru roboczego eksperymentu.
4. Połącz dane wyjściowe *i zaimportuj dane* modułu danych wejściowych z *Clean Missing Data* modułu.
5. W okienku właściwości, wybierz **bazy danych SQL Azure** w **źródła danych** listy rozwijanej.
6. W **nazwę serwera bazy danych**, **Nazwa bazy danych**, **nazwy użytkownika**, i **hasło** wprowadź odpowiednie informacje o sieci Baza danych.
7. W polu kwerendy bazy danych wpisz poniższe zapytanie.
   
     Wybierz [wieku]
   
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
Następnie możesz skonfigurować eksperyment predykcyjny, z którego można wdrożyć usługi sieci web.

1. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **predykcyjnej usługi sieci Web [zalecane]**.
2. Usuń *wprowadzania usługi sieci Web* i *modułów wyjście usługi sieci Web* z eksperyment predykcyjny. 
3. W polu wyszukiwania składników typu eksportu.
4. Z listy wyników, należy dodać *eksportowanie danych* modułu do obszaru roboczego eksperymentu.
5. Połącz dane wyjściowe *Score Model* modułu danych wejściowych z *eksportowanie danych* modułu. 
6. W okienku właściwości, wybierz **bazy danych SQL Azure** na liście rozwijanej docelowego danych.
7. W **nazwę serwera bazy danych**, **Nazwa bazy danych**, **nazwę konta użytkownika serwera**, i **hasło konta użytkownika serwera** wprowadź odpowiednie informacje o bazie danych.
8. W **rozdzielana przecinkami lista kolumn do zapisania** wpisz oceniane etykiety.
9. W **pole Nazwa tabeli danych**, wpisz dbo. ScoredLabels. Jeśli tabela nie istnieje, jest on utworzony po uruchomieniu eksperyment lub usługi sieci web jest wywoływana.
10. W **rozdzielana przecinkami lista kolumn datatable** wpisz ScoredLabels.

Podczas pisania aplikacji, która wywołuje usługę sieci web końcowego można określić inne zapytanie wejściowe lub tabeli docelowej w czasie wykonywania. Aby skonfigurować te wejściach i wyjściach, należy użyć funkcji parametry usługi sieci Web można ustawić *i zaimportuj dane* modułu *źródła danych* właściwości i *eksportowanie danych* dane trybu Właściwość docelowego.  Aby uzyskać więcej informacji o parametry usługi sieci Web, zobacz [wpis parametry usługi sieci Web uczenie maszynowe Azure](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) Cortana Intelligence i Machine Learning blogu.

Aby skonfigurować parametry usługi sieci Web dla zapytań import i tabeli docelowej:

1. W okienku właściwości dla *i zaimportuj dane* modułu, kliknij ikonę u góry po prawej **zapytanie bazy danych** i wybierz pozycję **ustawić jako parametr usługi sieci web**.
2. W okienku właściwości dla *eksportowanie danych* modułu, kliknij ikonę u góry po prawej **Nazwa tabeli danych** i wybierz pozycję **ustawić jako parametr usługi sieci web**.
3. W dolnej części *eksportowanie danych* okienka właściwości modułu w **parametry usługi sieci Web** sekcji, kliknij przycisk zapytanie bazy danych i zmień jego nazwę zapytania.
4. Kliknij przycisk **Nazwa tabeli danych** i zmień jego nazwę **tabeli**.

Gdy wszystko będzie gotowe, eksperymentu powinien wyglądać podobnie do poniższej ilustracji:

![Końcowe wygląd eksperymentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Teraz można wdrożyć eksperymentu jako usługę sieci web.

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web
Można wdrożyć do klasycznej lub nową usługę sieci web.

### <a name="deploy-a-classic-web-service"></a>Wdrażanie usługi sieci Web klasycznego
Aby wdrożyć jako usługę sieci Web klasycznego i utworzyć aplikację, aby pobrać go:

1. W dolnej części obszaru roboczego eksperymentu kliknij przycisk Uruchom.
2. Po zakończeniu uruchom kliknij **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]**.
3. Na pulpicie nawigacyjnym usługi sieci web zlokalizuj klucz interfejsu API. Skopiuj i zapisz go do użycia w przyszłości.
4. W **domyślny punkt końcowy** tabeli, kliknij przycisk **Batch Execution** łącze, aby otworzyć stronę pomocy interfejsu API.
5. W programie Visual Studio Utwórz aplikację konsoli języka C#: **nowy** > **projektu** > **Visual C#** > **systemu Windows Klasyczny pulpit** > **konsoli aplikacji (.NET Framework)**.
6. Strona pomocy interfejsu API, Znajdź **przykładowy kod** sekcji u dołu strony.
7. Skopiuj i Wklej kod przykładowy C# w pliku Program.cs i usunąć wszystkie odwołania do magazynu obiektów blob.
8. Zaktualizuj wartość *apiKey* przy użyciu klucza interfejsu API zapisany wcześniej zmiennej.
9. Znajdź deklaracji żądania i zaktualizuj wartości parametrów usługi sieci Web, które są przekazywane do *i zaimportuj dane* i *eksportowanie danych* modułów. W takim przypadku użyj oryginalnego zapytania, ale definiuje nowej nazwy tabeli.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Uruchom aplikację. 

Po zakończeniu uruchom nowej tabeli jest dodawany do bazy danych zawierającej wyników oceniania.

### <a name="deploy-a-new-web-service"></a>Wdrożenie nowej usługi sieci Web

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia do subskrypcji, do którego należy wdrożyć usługę sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md). 

Aby wdrożyć jako nową usługę sieci Web i utworzyć aplikację, aby pobrać go:

1. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **Uruchom**.
2. Po zakończeniu uruchom kliknij **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [New]**.
3. Na stronie eksperymentu wdrażania, wprowadź nazwę usługi sieci web i wybrać planu cenowego, a następnie kliknij przycisk **Wdróż**.
4. Na **szybkiego startu** kliknij przycisk **Consume**.
5. W **przykładowy kod** kliknij **partii**.
6. W programie Visual Studio Utwórz aplikację konsoli języka C#: **nowy** > **projektu** > **Visual C#** > **systemu Windows Klasyczny pulpit** > **konsoli aplikacji (.NET Framework)**.
7. Skopiuj i Wklej kod przykładowy C# w pliku Program.cs.
8. Zaktualizuj wartość *apiKey* zmiennej z **klucza podstawowego** znajduje się w **zużycie podstawowe informacje o** sekcji.
9. Zlokalizuj *scoreRequest* deklaracji i zaktualizuj wartości parametrów usługi sieci Web, które są przekazywane do *i zaimportuj dane* i *eksportowanie danych* modułów. W takim przypadku użyj oryginalnego zapytania, ale definiuje nowej nazwy tabeli.
   
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


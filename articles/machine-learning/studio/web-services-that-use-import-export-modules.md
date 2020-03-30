---
title: Importowanie/eksportowanie danych w usługach sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Dowiedz się, jak wysyłać i odbierać dane z usługi sieci web za pomocą modułów Importuj dane i Eksportuj dane.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204066"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Wdrażanie usług Azure Machine Learning Studio (klasycznych) usług sieci web, które używają modułów importu i eksportu danych

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Podczas tworzenia eksperymentu predykcyjnego zazwyczaj dodaje się dane wejściowe i wyjściowe usługi sieci web. Podczas wdrażania eksperymentu konsumenci mogą wysyłać i odbierać dane z usługi sieci web za pośrednictwem danych wejściowych i wyjściowych. W przypadku niektórych aplikacji dane konsumenta mogą być dostępne z zestawienia danych lub już znajdują się w zewnętrznym źródle danych, takim jak magazyn obiektów Blob platformy Azure. W takich przypadkach nie trzeba odczytywać i zapisywać dane przy użyciu danych wejściowych i wyjściowych usługi sieci web. Zamiast tego mogą używać usługi wykonywania wsadowego (BES) do odczytywania danych ze źródła danych przy użyciu modułu Importuj dane i zapisywania wyników oceniania w innej lokalizacji danych przy użyciu modułu Eksportuj dane.

Moduły danych importuj dane i eksportuj, można odczytywać i zapisywać w różnych lokalizacjach danych, takich jak adres URL sieci Web za pośrednictwem protokołu HTTP, kwerendy hive, bazy danych SQL platformy Azure, magazynu tabel platformy Azure, magazynu obiektów Blob platformy Azure, źródła danych zapewniają lub lokalnej bazy danych SQL.

W tym temacie użyto przykładu "Przykładowa 5: Pociąg, Test, Oceń klasyfikację binarną: Zestaw danych dorosłych" i zakłada, że zestaw danych został już załadowany do tabeli SQL platformy Azure o nazwie censusdata.

## <a name="create-the-training-experiment"></a>Tworzenie eksperymentu szkoleniowego
Po otwarciu próbki "Przykładowa 5: Pociąg, Test, Oceń dla klasyfikacji binarnej: Zestaw danych dorosłych" używa przykładowego zestawu danych klasyfikacji binarnej dochodu dla dorosłych. A eksperyment na płótnie będzie wyglądał podobnie do następującego obrazu:

![Wstępna konfiguracja eksperymentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Aby odczytać dane z tabeli SQL platformy Azure:

1. Usuń moduł zestawu danych.
2. W polu wyszukiwania składników wpisz import.
3. Z listy wyników dodaj moduł *Importuj dane* do kanwy eksperymentu.
4. Połącz dane wyjściowe modułu *Importuj dane* z danymi wejściowymi modułu *Clean Missing Data.*
5. W okienku właściwości wybierz **pozycję Azure SQL Database** w menu rozwijanym Źródło **danych.**
6. W polach **Nazwa serwera bazy danych**, Nazwa bazy **danych**, **Nazwa użytkownika**i **Hasło** wprowadź odpowiednie informacje dla bazy danych.
7. W polu Kwerenda bazy danych wprowadź następującą kwerendę.

     wybierz [wiek],

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
8. U dołu kanwy eksperymentu kliknij przycisk **Uruchom**.

## <a name="create-the-predictive-experiment"></a>Tworzenie eksperymentu predykcyjnego
Następnie skonfigurujesz eksperyment predykcyjny, z którego wdrażasz usługę sieci web.

1. U dołu obszaru roboczego eksperymentu kliknij pozycję **Konfiguruj usługę sieci Web** i wybierz pozycję **Predykcyjna usługa sieci Web [Zalecane]**.
2. Usuń *moduły wejścia usługi sieci Web* i wyjścia usługi sieci *Web* z eksperymentu predykcyjnego.
3. W polu wyszukiwania składników wpisz export.
4. Z listy wyników dodaj moduł *Eksportuj dane* do kanwy eksperymentu.
5. Połącz dane wyjściowe modułu *Score Model* z danymi wejściowymi modułu *Eksportuj dane.*
6. W okienku właściwości wybierz **pozycję Azure SQL Database** w menu rozwijanym miejsca docelowego danych.
7. W polach **Nazwa serwera bazy danych**, Nazwa bazy **danych**, Nazwa konta **użytkownika serwera**i Hasło konta **użytkownika serwera** wprowadź odpowiednie informacje dla bazy danych.
8. Na **liście oddzielonych przecinkami kolumn do zapisania** wpisz etykiety punktowane.
9. W **polu Nazwa tabeli Dane**wpisz dbo. ScoredLabels. Jeśli tabela nie istnieje, jest tworzona po uruchomieniu eksperymentu lub wywołaniu usługi sieci web.
10. W polu **Rozdzielona przecinek kolumnami datatable** wpisz ScoredLabels.

Podczas pisania aplikacji, która wywołuje ostateczną usługę sieci web, można określić inną kwerendę wejściową lub tabelę docelową w czasie wykonywania. Aby skonfigurować te dane wejściowe i wyjściowe, użyj funkcji Parametry usługi sieci Web, aby ustawić *właściwość* *Importuj* moduł źródła danych i właściwość miejsce docelowe danych *w trybie eksportu.*  Aby uzyskać więcej informacji na temat parametrów usługi sieci Web, zobacz [wpis Parametry usługi sieci Web usługi Azure Machine Learning Studio](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) w blogu analizy analizy cortany i uczenia maszynowego.

Aby skonfigurować parametry usługi sieci Web dla kwerendy importującej i tabeli docelowej:

1. W okienku właściwości modułu *Importuj dane* kliknij ikonę w prawym górnym rogu pola **Kwerenda bazy danych** i wybierz polecenie Ustaw jako parametr usługi sieci **web**.
2. W okienku właściwości modułu *Eksportuj dane* kliknij ikonę w prawym górnym rogu pola **Nazwa tabeli danych** i wybierz polecenie Ustaw jako parametr usługi sieci **web**.
3. U dołu okienka Właściwości *modułu Eksportuj dane* w sekcji **Parametry usługi sieci Web** kliknij pozycję Kwerenda bazy danych i zmień jej nazwę na Kwerenda.
4. Kliknij **pozycję Nazwa tabeli danych** i zmień jej nazwę **Tabela**.

Po zakończeniu eksperyment powinien wyglądać podobnie do następującego obrazu:

![Ostateczny wygląd eksperymentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Teraz można wdrożyć eksperyment jako usługę sieci web.

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web
Można wdrożyć w klasycznej lub nowej usłudze sieci web.

### <a name="deploy-a-classic-web-service"></a>Wdrażanie klasycznej usługi sieci Web
Aby wdrożyć jako klasyczną usługę sieci Web i utworzyć aplikację, aby ją korzystać:

1. U dołu kanwy eksperymentu kliknij pozycję Uruchom.
2. Po zakończeniu uruchamiania kliknij pozycję **Wdrażanie usługi sieci Web** i wybierz pozycję **Wdrażanie usługi sieci Web [Classic]**.
3. Na pulpicie nawigacyjnym usługi sieci Web znajdź klucz interfejsu API. Skopiuj i zapisz go do użycia później.
4. W tabeli **Domyślny punkt końcowy** kliknij łącze **Wykonywanie wsadowe,** aby otworzyć stronę pomocy interfejsu API.
5. W programie Visual Studio utwórz aplikację konsoli języka C#: **Nowa** > **klasyczna** > aplikacja konsoli klasycznej systemu Windows**programu Project** > **Visual C#** > **(.NET Framework)**.
6. Na stronie pomocy interfejsu API znajdź sekcję **Przykładowy kod** u dołu strony.
7. Skopiuj i wklej przykładowy kod języka C# do pliku Program.cs i usuń wszystkie odwołania do magazynu obiektów blob.
8. Zaktualizuj wartość zmiennej *apiKey* za pomocą klucza INTERFEJSU API zapisanego wcześniej.
9. Zlokalizuj deklarację żądania i zaktualizuj wartości parametrów usługi sieci Web, które są przekazywane do modułów *Importuj dane* i *Eksportuj dane.* W takim przypadku należy użyć oryginalnej kwerendy, ale zdefiniować nową nazwę tabeli.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Uruchom aplikację.

Po zakończeniu uruchomienia nowa tabela jest dodawana do bazy danych zawierającej wyniki oceniania.

### <a name="deploy-a-new-web-service"></a>Wdrażanie nowej usługi sieci Web

> [!NOTE]
> Aby wdrożyć nową usługę sieci web, musisz mieć wystarczające uprawnienia w subskrypcji, do której wdrażasz usługę sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług azure machine learning web .](manage-new-webservice.md)

Aby wdrożyć jako nową usługę sieci Web i utworzyć aplikację, aby ją korzystać:

1. U dołu kanwy eksperymentu kliknij przycisk **Uruchom**.
2. Po zakończeniu uruchomienia kliknij pozycję **Wdrażanie usługi sieci Web** i wybierz pozycję **Wdrażanie usługi sieci Web [Nowy]**.
3. Na stronie Wdrażanie eksperymentu wprowadź nazwę usługi sieci web i wybierz plan cenowy, a następnie kliknij przycisk **Wdrażanie**.
4. Na stronie **Szybki start** kliknij pozycję **Konsumuj**.
5. W sekcji **Przykładowy kod** kliknij pozycję **Partia**.
6. W programie Visual Studio utwórz aplikację konsoli języka C#: **Nowa** > **klasyczna** > aplikacja konsoli klasycznej systemu Windows**programu Project** > **Visual C#** > **(.NET Framework)**.
7. Skopiuj i wklej przykładowy kod języka C# do pliku Program.cs.
8. Zaktualizuj wartość zmiennej *apiKey* za pomocą **klucza podstawowego** znajdującego się w sekcji **Informacje o zużyciu podstawowym.**
9. Zlokalizuj deklarację *scoreRequest* i zaktualizuj wartości parametrów usługi sieci Web, które są przekazywane do modułów *Importuj dane* i *Eksportuj dane.* W takim przypadku należy użyć oryginalnej kwerendy, ale zdefiniować nową nazwę tabeli.

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


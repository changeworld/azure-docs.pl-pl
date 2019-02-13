---
title: Programowe ponowne trenowanie modeli usługi Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Dowiedz się, jak programowe ponowne trenowanie modelu przy użyciu C# i usługa Machine Learning Batch Execution.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 474355088a339741b8dd1917fb354d55df4990f6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189195"
---
# <a name="retrain-azure-machine-learning-studio-models-programmatically"></a>Programowe ponowne trenowanie modeli usługi Azure Machine Learning Studio
W tym instruktażu będą nauczysz się programowe ponowne trenowanie korzystając usługi sieci web Azure Machine Learning Studio C# i usługa Machine Learning Batch Execution.

Po mają retrained modelu, zapoznaj się z [Ponowne szkolenie nowej usługi sieci web przy użyciu poleceń cmdlet zarządzania w usłudze Machine Learning](retrain-new-web-service-using-powershell.md) aby zobaczyć, jak można zaktualizować modelu w swojej predykcyjną usługę sieci web:

Omówienie ponownego trenowania procesu, zobacz [Ponowne szkolenie modelu uczenia maszynowego](retrain-machine-learning-model.md).

Jeśli chcesz uruchomić przy użyciu usługi sieci web nowej usługi Azure Resource Manager na podstawie istniejących, zobacz [Ponowne szkolenie istniejącej usługi internetowej predykcyjne](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkolenia
Na przykład użyjesz "przykład 5: Szkolenie, testowanie, oceny Klasyfikacja binarna: Treści dla dorosłych zestawu danych"z przykładów usługi Microsoft Azure Machine Learning. 

Aby utworzyć eksperyment:

1. Zaloguj się do platformy Microsoft Azure Machine Learning Studio. 
2. W prawym dolnym rogu pulpitu nawigacyjnego, kliknij polecenie **New**.
3. Wybierz przykład 5 z Samples firmy Microsoft.
4. Aby zmienić nazwę eksperymentu w górnej części obszaru roboczego eksperymentu, wybierz nazwę eksperymentu "przykład 5: Szkolenie, testowanie, oceny Klasyfikacja binarna: Treści dla dorosłych zestaw danych".
5. Typ modelu spisu.
6. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **Uruchom**.
7. Kliknij przycisk **Konfiguruj usługę sieci web** i wybierz **ponownego trenowania usługi sieci web**. 

Poniżej przedstawiono początkowe doświadczenia.
   
   ![Początkowe doświadczenia.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Utworzyć eksperyment predykcyjny i Opublikuj jako usługę sieci web
Następnie należy utworzyć eksperyment Predicative.

1. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **predykcyjne usługi sieci Web**. Zapisuje modelu w postaci Trenowanego modelu i dodaje modułów usługi sieci web dane wejściowe i wyjściowe. 
2. Kliknij pozycję **Run** (Uruchom). 
3. Po zakończeniu eksperymentu, kliknij przycisk **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]**.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Wdrażanie eksperymentu szkolenia jako usługę sieci web szkolenia
Doskonalenie trenowanego modelu, należy wdrożyć eksperymentu szkolenia, który został utworzony jako Retraining usługi sieci web. Usługi sieci web musi *danych wyjściowych usługi sieci Web* podłączone do modułu *[Train Model] [ train-model]* modułu, aby można było tworzyć nowe skonfigurowanych pod kątem modele.

1. Aby powrócić do eksperymentu szkolenia, kliknij ikonę eksperymenty, w okienku po lewej stronie, a następnie kliknij eksperyment, o nazwie Model spisu.  
2. W polu wyszukiwania wyszukiwanie eksperymentu elementów typu usługi sieci web. 
3. Przeciągnij *dane wejściowe usługi sieci Web* modułów na eksperyment obszaru roboczego i połącz dane wyjściowe do *Clean Missing Data* modułu.  Daje to gwarancję, że ponownego trenowania danych jest przetwarzany taki sam sposób jak oryginalne dane szkoleniowe.
4. Przeciągnij dwa *danych wyjściowych usługi sieci web* modułów do obszaru roboczego eksperymentu. Połącz wyjście *Train Model* modułu do jednego i dane wyjściowe *Evaluate Model* modułu do drugiego. Dane wyjściowe usługi sieci web dla **Train Model** daje nam nowe trenowanego modelu. Dane wyjściowe są dołączone do **Evaluate Model** zwraca ten moduł danych wyjściowych, który jest wyników wydajności.
5. Kliknij pozycję **Run** (Uruchom). 

Następnie należy wdrożyć eksperymentu szkolenia jako usługę sieci web, który produkuje uczonego modelu i wyniki oceny modelu. Aby to osiągnąć, następnego zestawu działań są zależne od tego, czy pracujesz z klasycznej usługi sieci web lub nowej usługi sieci web.  

**Klasyczna usługa sieci web**

W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]**. Usługa sieci Web **pulpit nawigacyjny** jest wyświetlana przy użyciu klucza interfejsu API i strona pomocy interfejsu API dla wykonywania wsadowego. Tylko metody wykonywania wsadowego może służyć do tworzenia przeszkolone modele.

**Nowa usługa sieci web**

W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **wdrażanie usługi sieci Web [New]**. Portal usług sieci Web usługi Azure Machine Learning Web otwiera się do strony usługi sieci web Deploy. Wpisz nazwę usługi sieci web i wybierz plan płatności, a następnie kliknij przycisk **Wdróż**. Tylko metody wykonywania wsadowego można użyć do tworzenia przeszkolone modele

W obu przypadkach po eksperymentu zakończy działanie, wynikowy przepływ pracy powinien wyglądać następująco:

![Wynikowy przepływ pracy, po uruchomieniu.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Ponowne szkolenie modelu przy użyciu nowych danych przy użyciu usługi BES
Na przykład używasz C# do tworzenia aplikacji ponownego trenowania. Umożliwia także przykładowy kod języka Python lub R, aby wykonać to zadanie.

Do wywoływania interfejsów API do ponownego trenowania:

1. Tworzenie C# konsoli aplikacji w programie Visual Studio: **Nowe** > **projektu** > **Visual C#**   >  **Windows Classic Desktop**  >   **Aplikacja konsoli (.NET Framework)**.
2. Zaloguj się do portalu usługi internetowej Machine Learning.
3. Jeśli pracujesz z klasycznej usługi sieci web, kliknij przycisk **klasycznych usług sieci Web**.
   1. Kliknij usługę internetową, którą pracujesz.
   2. Kliknij domyślny punkt końcowy.
   3. Kliknij przycisk **używanie**.
   4. W dolnej części **zużywania** strony w **przykładowy kod** kliknij **partii**.
   5. Przejdź do kroku 5 tej procedury.
4. Jeśli pracujesz z nowej usługi sieci web, kliknij przycisk **usług sieci Web**.
   1. Kliknij usługę internetową, którą pracujesz.
   2. Kliknij przycisk **używanie**.
   3. W dolnej części wykorzystania strony w **przykładowy kod** kliknij **partii**.
5. Skopiuj przykład C# kod wykonywanie wsadowe i wklej go do pliku Program.cs, upewniając się, przestrzeń nazw pozostaną niezmienione.

Dodaj pakiet Nuget Microsoft.AspNet.WebApi.Client, jak określono w komentarzach. Aby dodać odwołanie do Microsoft.WindowsAzure.Storage.dll, może najpierw musisz zainstalować bibliotekę klienta usługi Magazyn Microsoft Azure. Aby uzyskać więcej informacji, zobacz [usług magazynu systemu Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Aktualizacja deklaracji apikey
Znajdź **apikey** deklaracji.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

W **informacje podstawowe użycie** części **zużywania** stronie Znajdź klucz podstawowy i skopiuj go do **apikey** deklaracji.

### <a name="update-the-azure-storage-information"></a>Zaktualizuj informacje o usłudze Azure Storage
Przykładowy kod BES służy do przekazywania pliku z dysku lokalnego (na przykład "C:\temp\CensusIpnput.csv") do usługi Azure Storage, przetwarza je i zapisuje wyniki z powrotem do usługi Azure Storage.  

Aby wykonać to zadanie, należy pobrać informacje nazwy, klucza i kontenera konta magazynu dla konta magazynu z klasycznego portalu Azure i odpowiadające im wartości aktualizacji w kodzie. 

1. Zaloguj się do klasycznego portalu Azure.
2. W kolumnie nawigacji po lewej stronie kliknij **magazynu**.
3. Z listy kont magazynu wybierz jeden do przechowywania retrained modelu.
4. W dolnej części strony kliknij **Zarządzaj kluczami dostępu**.
5. Skopiuj i Zapisz **podstawowy klucz dostępu** i zamknij okno dialogowe. 
6. W górnej części strony kliknij **kontenery**.
7. Wybierz istniejący kontener lub Utwórz nową i Zapisz nazwę.

Znajdź *StorageAccountName*, *StorageAccountKey*, i *StorageContainerName* deklaracje i zaktualizuj wartości zapisane w witrynie Azure portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Ponadto musisz zapewnić, że plik wejściowy jest dostępny w lokalizacji, które określisz w kodzie. 

### <a name="specify-the-output-location"></a>Określanie lokalizacji wyjściowej
Podczas określania lokalizacji danych wyjściowych w ładunku żądania, rozszerzenie nazwy pliku określonej w *RelativeLocation* muszą być określone jako ilearner. 

Zobacz poniższy przykład:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> Nazwy lokalizacji danych wyjściowych może się różnić od przedstawionych w tym przewodniku, w oparciu o kolejność, w której są dodawane modułów danych wyjściowych usługi sieci web. Ponieważ konfigurowania tego eksperymentu szkolenia z danych wyjściowych dwa wyniki obejmują informacje o lokalizacji magazynu dla obu z nich.  
> 
> 

![Ponowne szkolenie danych wyjściowych][6]

Diagram 4: Dane wyjściowe ponownego trenowania.

## <a name="evaluate-the-retraining-results"></a>Ocena ponownego trenowania wyników
Po uruchomieniu aplikacji, dane wyjściowe obejmują adres URL sygnatury dostępu Współdzielonego tokenu i niezbędnych do uzyskania dostępu wyniki oceny.

Wyniki wydajności retrained modelu można wyświetlić, łącząc *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników danych wyjściowych dla *output2* (pokazany na wcześniejszej ilustracji ponownego trenowania danych wyjściowych) i wklejanie pełny adres URL w pasku adresu przeglądarki.  

Sprawdź wyniki, aby ustalić, czy nowo uczonego modelu wykonuje wystarczająco dobrze, aby zamienić istniejący.

Kopiuj *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników danych wyjściowych, użyjesz ich w procesie ponownego trenowania.

## <a name="next-steps"></a>Kolejne kroki

 [Ponowne szkolenie nowej usługi sieci web przy użyciu poleceń cmdlet zarządzania w usłudze Machine Learning](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

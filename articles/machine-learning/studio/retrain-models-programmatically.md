---
title: Ponownie ucz modele uczenia maszynowego programowo | Dokumentacja firmy Microsoft
description: Dowiedz się, jak programowo retrain modelu i usługi sieci web, aby użyć nowo uczonego modelu w usłudze Azure Machine Learning aktualizacji.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: b2090b39991363ee2a5b2e12945d97dc0fa9f2b2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835508"
---
# <a name="retrain-machine-learning-models-programmatically"></a>Programowe ponowne trenowanie modeli usługi Machine Learning
W tym przewodniku dowiesz się, jak programowo ponownie ucz Azure Machine Learning usługi sieci Web przy użyciu języka C# i usługę wykonywania wsadowego usługi Machine Learning.

Po ma retrained modelu, następujące wskazówki pokazują, jak zaktualizować model w usłudze sieci web predykcyjnej:

* Jeśli wdrożono klasycznym usługi sieci web w portalu usługi sieci Web usługi Machine Learning, zobacz [Retrain usługi sieci web klasycznego](retrain-a-classic-web-service.md). 
* Jeśli wdrożono nową usługę sieci web, zobacz [Retrain nową usługę sieci web za pomocą poleceń cmdlet Machine Learning Management](retrain-new-web-service-using-powershell.md).

Omówienie procesu ponownego trenowania, zobacz [Retrain modelu uczenia maszynowego](retrain-machine-learning-model.md).

Jeśli chcesz uruchomić z usługą sieci web nowej usługi Azure Resource Manager na podstawie istniejących, zobacz [Retrain istniejącej usługi sieci web predykcyjnej](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Tworzenie eksperymentu szkolenia
Na przykład użyjesz "próbki 5: Evaluate pociągu, testu, klasyfikacji binarnej: dla dorosłych zestawu danych" z próbek Microsoft Azure Machine Learning. 

Aby utworzyć eksperyment:

1. Studio uczenia maszynowego za pomocą Zaloguj się do platformy Microsoft Azure. 
2. W prawym dolnym rogu pulpitu nawigacyjnego, kliknij polecenie **nowy**.
3. Wybierz przykład 5 z Samples firmy Microsoft.
4. Aby zmienić nazwę eksperymentu w górnej części obszaru roboczego eksperymentu, wybierz nazwę eksperymentu "próbki 5: Evaluate pociągu, testu, klasyfikacji binarnej: dla dorosłych zestawu danych".
5. Typ modelu spisu.
6. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **Uruchom**.
7. Kliknij przycisk **Konfiguruj usługę sieci web** i wybierz **ponownego trenowania usługi sieci web**. 

Poniżej przedstawiono początkowej eksperymentu.
   
   ![Początkowa eksperymentu.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Utworzyć eksperyment predykcyjny i Opublikuj jako usługi sieci web
Następnie należy utworzyć eksperyment Predicative.

1. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **predykcyjnej usługi sieci Web**. Ten model jest zapisywany jako Uczonego modelu i dodaje modułów wejścia i wyjścia usługi sieci web. 
2. Kliknij pozycję **Run** (Uruchom). 
3. Po zakończeniu pracy eksperyment, kliknij przycisk **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]**.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia do subskrypcji, do którego należy wdrożyć usługę sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Wdrażanie eksperyment uczenia jako usługę sieci web szkolenia
Aby ponownie ucz trenowanego modelu, należy wdrożyć eksperyment uczenia, utworzony jako usługę sieci web Retraining. Ta usługa sieci web musi *wyjście usługi sieci Web* modułu połączony *[Train Model] [ train-model]* modułu, aby można było tworzyć nowe modele przeszkolone.

1. Aby powrócić do eksperyment uczenia, kliknij ikonę eksperymenty w okienku po lewej stronie, a następnie kliknij eksperyment o nazwie modelu spisu.  
2. W polu wyszukiwania elementów eksperymentu wyszukiwania wpisz usługi sieci web. 
3. Przeciągnij *wprowadzania usługi sieci Web* modułu na eksperyment roboczego i połącz dane wyjściowe do *Clean Missing Data* modułu.  Dzięki temu, że ponownego trenowania danych jest przetwarzany taki sam sposób jak oryginalne dane szkolenia.
4. Przeciągnij dwa *wyjście usługi sieci web* modułów do kanwy eksperymentu. Połącz dane wyjściowe *Train Model* na jednym i dane wyjściowe z modułu *Evaluate Model* modułu do drugiego. Wyjście usługi sieci web dla **Train Model** daje nowe trenowanego modelu. Dane wyjściowe dołączony do **Evaluate Model** zwraca wyjściowy ten moduł, który jest wyniki wydajności.
5. Kliknij pozycję **Run** (Uruchom). 

Następnie należy wdrożyć eksperyment uczenia jako usługę sieci web, który spowoduje utworzenie uczonego modelu i wyniki oceny modelu. W tym celu dalej zestawu działań są zależne od tego, czy działają z usługą sieci web klasycznego lub nową usługę sieci web.  

**Usługa sieci web klasycznego**

W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]**. Usługa sieci Web **pulpitu nawigacyjnego** jest wyświetlana strona pomocy interfejsu API i klucz interfejsu API dla wykonywania wsadowego usługi. Metody wykonywania wsadowego usługi może służyć do tworzenia modeli przeszkolone.

**Nowa usługa sieci web**

W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web** i wybierz **wdrażanie usługi sieci Web [New]**. Portal usługi sieci Web sieci Web usługi Azure Machine Learning otwiera się do strony usługi sieci web Deploy. Wpisz nazwę usługi sieci web i wybierz plan płatności, a następnie kliknij przycisk **Wdróż**. Metody wykonywania wsadowego usługi może służyć do tworzenia modeli przeszkolone

W obu przypadkach po eksperymentu zakończy działanie, wynikowy przepływu pracy powinna wyglądać następująco:

![Wynikowa przepływu pracy, po uruchomieniu.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Ponownie ucz modelu nowymi danymi za pomocą usługi BES
Na przykład używasz C# do tworzenia aplikacji ponownego trenowania. Aby wykonać to zadanie umożliwia także przykładowy kod języka Python lub R.

Wywoływanie interfejsów API ponownego trenowania:

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio: **nowy** > **projektu** > **Visual C#** > **Windows Desktop klasycznego** > **aplikacji konsoli (.NET Framework)**.
2. Zaloguj się do portalu usługi sieci Web Machine Learning.
3. Podczas pracy z usługą sieci web klasycznego, kliknij przycisk **klasycznym usługi sieci Web**.
   1. Kliknij usługę sieci web, w którym pracujesz z.
   2. Kliknij domyślny punkt końcowy.
   3. Kliknij przycisk **korzystać**.
   4. W dolnej części **Consume** strony w **przykładowy kod** kliknij **partii**.
   5. Przejdź do kroku 5 tej procedury.
4. Jeśli pracujesz z nową usługę sieci web, kliknij przycisk **usług sieci Web**.
   1. Kliknij usługę sieci web, w którym pracujesz z.
   2. Kliknij przycisk **korzystać**.
   3. W dolnej części Consume strony, **przykładowy kod** kliknij **partii**.
5. Skopiuj przykładowy kod C# dla wykonywania wsadowego i wklej go do pliku Program.cs, upewniając się, że przestrzeń nazw pozostanie niezmieniona.

Dodaj pakiet Nuget Microsoft.AspNet.WebApi.Client określonych w komentarzach. Można dodać odwołania do Microsoft.WindowsAzure.Storage.dll, należy najpierw zainstalować bibliotekę klienta usługi magazynu Microsoft Azure. Aby uzyskać więcej informacji, zobacz [usługi magazynu systemu Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Deklaracja apikey aktualizacji
Zlokalizuj **apikey** deklaracji.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

W **zużycie podstawowe informacje o** sekcji **Consume** strony Znajdź klucz podstawowy i skopiuj go do **apikey** deklaracji.

### <a name="update-the-azure-storage-information"></a>Zaktualizuj informacje o usłudze Azure Storage
BES przykładowy kod operacji przekazywania plików z dysku lokalnego (na przykład "C:\temp\CensusIpnput.csv") do usługi Azure Storage, procesy i zapisuje wyniki wstecz do magazynu Azure.  

Aby wykonać to zadanie, należy pobrać nazwę, klucz oraz kontenera informacji o koncie magazynu dla konta magazynu z klasycznego portalu Azure i wartości odpowiednich aktualizacji w kodzie. 

1. Zaloguj się do klasycznego portalu Azure.
2. W kolumnie nawigacji po lewej stronie kliknij **magazynu**.
3. Z listy kont magazynu wybierz jeden do przechowywania retrained modelu.
4. W dolnej części strony kliknij **Zarządzaj kluczami dostępu**.
5. Skopiuj i Zapisz **podstawowy klucz dostępu** i zamknij okno dialogowe. 
6. W górnej części strony kliknij **kontenery**.
7. Wybierz kontener istniejącą lub Utwórz nową i Zapisz nazwę.

Zlokalizuj *StorageAccountName*, *StorageAccountKey*, i *StorageContainerName* deklaracje i zaktualizuj wartości zapisane w portalu Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Ponadto musisz zapewnić, że plik wejściowy jest dostępny w lokalizacji, które określisz w kodzie. 

### <a name="specify-the-output-location"></a>Określ lokalizację danych wyjściowych
Podczas określania lokalizacji danych wyjściowych w ładunku żądania, rozszerzenie pliku określona w *RelativeLocation* muszą być określone jako ilearner. 

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
> Nazwy lokalizacji danych wyjściowych może być inne niż w podane w tym przewodniku, na podstawie kolejności, w której są dodawane modułów wyjście usługi sieci web. Ponieważ skonfigurowaniu tego eksperymentu uczenia z danych wyjściowych dwóch wyniki obejmują informacje o lokalizacji magazynu dla obu z nich.  
> 
> 

![Ponownego trenowania danych wyjściowych][6]

Diagram 4: Ponownego trenowania danych wyjściowych.

## <a name="evaluate-the-retraining-results"></a>Ocena ponownego trenowania wyników
Po uruchomieniu aplikacji, danych wyjściowych zawiera token adresu URL i skojarzenia zabezpieczeń niezbędnych do uzyskania dostępu wyniki oceny.

Można wyświetlić wyniki wydajności retrained modelu, łącząc *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników danych wyjściowych dla *output2* (jak pokazano w poprzednim ponownego trenowania obrazu wyjściowego) i wklejanie pełny adres URL na pasku adresu przeglądarki.  

Przeanalizuj wyniki do ustalenia, czy nowo trenowanego modelu pełni wystarczająco również zastąpić istniejący.

Kopiuj *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników dane wyjściowe będą używały ich podczas procesu ponownego trenowania.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wdrożono usługę sieci web predykcyjnych, klikając **wdrażanie usługi sieci Web [klasyczny]**, zobacz [Retrain usługi sieci web klasycznego](retrain-a-classic-web-service.md).

Jeśli wdrożono usługę sieci web predykcyjnych, klikając **wdrażanie usługi sieci Web [New]**, zobacz [Retrain nową usługę sieci web za pomocą poleceń cmdlet Machine Learning Management](retrain-new-web-service-using-powershell.md).

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

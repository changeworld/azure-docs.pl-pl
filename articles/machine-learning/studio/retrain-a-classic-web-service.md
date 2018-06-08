---
title: Usługi sieci web klasycznego retrain | Dokumentacja firmy Microsoft
description: Dowiedz się, jak programowo retrain modelu i usługi sieci web, aby użyć nowo uczonego modelu w usłudze Azure Machine Learning aktualizacji.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 6fc03865185b97fb1f34028239f647f97d5bd315
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836878"
---
# <a name="retrain-a-classic-web-service"></a>Ponowne szkolenie klasycznej usługi internetowej
Usługa sieci Web predykcyjnych, wdrożone jest domyślnego punktu końcowego oceniania. Domyślne punkty końcowe są utrzymywane w synchronizacji z oryginalnego szkolenia i oceniania eksperymentów, a w związku z tym nie można zamienić trenowanego modelu dla domyślnego punktu końcowego. Aby ponownie ucz usługi sieci web, należy dodać nowy punkt końcowy usługi sieci web. 

## <a name="prerequisites"></a>Wymagania wstępne
Musi mieć skonfigurowaniu eksperyment uczenia i eksperyment predykcyjny jak pokazano w [Retrain Machine Learning programowo modele](retrain-models-programmatically.md). 

> [!IMPORTANT]
> Należy wdrożyć eksperyment predykcyjny jako usługę sieci web uczenia maszynowego klasycznego. 
> 
> 

Aby uzyskać dodatkowe informacje na temat usługi sieci web wdrażanie, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Dodaj nowy punkt końcowy
Predykcyjnej wdrożonej usługi sieci Web zawiera domyślne oceniania punktu końcowego, który jest synchronizowana z oryginalnym szkolenia i oceniania eksperymenty trenowanego modelu. Aby zaktualizować usługi sieci web do nowego trenowanego modelu, należy utworzyć nowy punkt końcowy oceniania. 

Aby utworzyć nowy punkt końcowy oceniania, od predykcyjnej usługi sieci Web, która może zostać zaktualizowana przy użyciu trenowanego modelu:

> [!NOTE]
> Upewnij się, że punkt końcowy jest dodawany do predykcyjnej usługi sieci Web nie usługę sieci Web szkolenia. Jeśli zostały poprawnie wdrożone zarówno szkolenia, jak i usługi sieci Web predykcyjnej, powinny pojawić się dwie usługi WWW, na liście. Predykcyjnej usługi sieci Web powinien kończyć się "[exp predykcyjnych.]".
> 
> 

Istnieją dwa sposoby, w których można dodać nowy punkt końcowy usługi sieci web:

1. Programistyczne
2. Za pomocą portalu usługi sieci Web platformy Microsoft Azure

### <a name="programmatically-add-an-endpoint"></a>Programowe Dodawanie punktu końcowego
Możesz dodać oceniania punktów końcowych przy użyciu przykładowy kod podany w tym [repozytorium github](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Dodawanie punktu końcowego za pomocą portalu usługi sieci Web platformy Microsoft Azure
1. W usłudze Machine Learning Studio w kolumnie nawigacji po lewej stronie kliknij usług sieci Web.
2. W dolnej części pulpitu nawigacyjnego usługi sieci web, kliknij przycisk **Zarządzaj punktami końcowymi Podgląd**.
3. Kliknij pozycję **Add** (Dodaj).
4. Wpisz nazwę i opis dla nowego punktu końcowego. Wybierz poziom rejestrowania i czy jest włączone przykładowych danych. Aby uzyskać więcej informacji, zobacz [należy włączyć rejestrowanie dla usługi sieci web uczenie maszynowe](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Zaktualizuj uczonego modelu dodano punkt końcowy
Aby ukończyć proces ponownego trenowania, należy zaktualizować trenowanego modelu nowy punkt końcowy, który został dodany.

Jeśli dodano punkt końcowy, za pomocą przykładowy kod, w tym lokalizacji identyfikowana na podstawie adresu URL pomocy *HelpLocationURL* wartość w danych wyjściowych.

Aby pobrać ścieżki adresu URL:

1. Skopiuj i wklej adres URL w przeglądarce.
2. Kliknij łącze aktualizacji zasobów.
3. Skopiuj adres URL żądania PATCH POST. Na przykład:
   
     ADRES URL POPRAWKI: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Można teraz używać trenowanego modelu można zaktualizować punktu końcowego oceniania utworzonego wcześniej.

Następujący przykładowy kod przedstawia sposób użycia *BaseLocation*, *RelativeLocation*, *SasBlobToken*i poprawka adres URL, aby zaktualizować punktu końcowego.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

*ApiKey* i *endpointUrl* dla wywołania można uzyskać z poziomu pulpitu nawigacyjnego punktu końcowego.

Wartość *nazwa* parametru w *zasobów* powinna odpowiadać nazwie zasobu zapisane Uczonego modelu w eksperyment predykcyjny. Aby uzyskać nazwę zasobu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij **uczenia maszynowego**.
3. W obszarze nazwy, kliknij obszar roboczy, a następnie kliknij **usług sieci Web**.
4. W obszarze nazwy, kliknij przycisk **modelu spisu [exp predykcyjnych.]** .
5. Kliknij nowy punkt końcowy, dodane.
6. Na pulpicie nawigacyjnym punktu końcowego kliknij **aktualizacji zasobów**.
7. Na stronie dokumentacji interfejsu API aktualizacji zasobów dla usługi sieci web można znaleźć **Nazwa zasobu** w obszarze **nadaje się do aktualizacji zasobów**.

Jeśli token sygnatury dostępu Współdzielonego wygasa przed zakończeniem aktualizowanie punktu końcowego, należy wykonać pobierania identyfikatorem zadania do uzyskania tokenu świeże.

Jeśli kod został uruchomiony pomyślnie, nowy punkt końcowy należy rozpocząć używanie retrained modelu w około 30 sekund.

## <a name="summary"></a>Podsumowanie
Interfejsy API ponownego trenowania można aktualizować trenowanego modelu predykcyjnego usługi sieci Web, włączanie scenariuszy, takich jak:

* Model okresowego ponownego trenowania nowymi danymi.
* Dystrybucja klientów przy użyciu modelu w celu umożliwienie je ponownie ucz modelu, używając własnych danych.

## <a name="next-steps"></a>Kolejne kroki
[Rozwiązywanie problemów z ponownego trenowania usługi sieci web klasycznego Azure Machine Learning](troubleshooting-retraining-models.md)


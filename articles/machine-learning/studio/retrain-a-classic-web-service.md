---
title: Ponowne szkolenie klasycznej usługi sieci web | Dokumentacja firmy Microsoft
description: Dowiedz się, jak programowe ponowne trenowanie modelu i zaktualizować usługę sieci web, aby używać nowo uczonego modelu w usłudze Azure Machine Learning.
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
ms.openlocfilehash: 3de17375670d7697a298023dc79ffc2418cb7e42
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963774"
---
# <a name="retrain-a-classic-web-service"></a>Ponowne szkolenie klasycznej usługi internetowej
Predykcyjne wdrożonej usługi sieci Web jest domyślnego punktu końcowego oceniania. Domyślne punkty końcowe są synchronizowane z oryginalnego szkolenia i oceniania eksperymentów, a w związku z tym nie można zastąpić uczonego modelu dla domyślnego punktu końcowego. Doskonalenie usługi sieci web, należy dodać nowy punkt końcowy usługi sieci web. 

## <a name="prerequisites"></a>Wymagania wstępne
Musi mieć skonfigurowaniu eksperymentu szkolenia i eksperyment predykcyjny pokazany na [Retrain Machine Learning models programowo](retrain-models-programmatically.md). 

> [!IMPORTANT]
> Eksperyment predykcyjny musi zostać wdrożony jako klasyczna usługi machine learning web service. 
> 
> 

Aby uzyskać dodatkowe informacje na temat usługi sieci web wdrażanie, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Dodaj nowy punkt końcowy
Predykcyjne usługi sieci Web, która została wdrożona zawiera domyślne oceniania punkt końcowy, który jest synchronizowana z oryginalnego szkolenie i ocenianie uczonego modelu eksperymentów. Aby zaktualizować usługę sieci web w taki sposób, aby nowe uczonego modelu, należy utworzyć nowy punkt końcowy oceniania. 

Aby utworzyć nowy punkt końcowy oceniania, na predykcyjne usługi sieci Web, które mogą być aktualizowane za pomocą uczonego modelu:

> [!NOTE]
> Upewnij się, że chcesz dodać punkt końcowy do predykcyjnej usługi sieci Web nie szkolenia usługi sieci Web. Jeśli zostały poprawnie wdrożone szkoleniowe i predykcyjne usługi sieci Web, powinny pojawić się dwie usługi sieci web w osobnych na liście. Predykcyjne usługi sieci Web powinien kończyć się znakiem "[exp predykcyjne.]".
> 
> 

Istnieją dwa sposoby, w których można dodać nowy punkt końcowy usługi sieci web:

1. Programistyczne
2. Korzystanie z portalu usług sieci Web systemu Microsoft Azure

### <a name="programmatically-add-an-endpoint"></a>Programowe Dodawanie punktu końcowego
Możesz dodać punktami końcowymi oceniania, korzystającą z przykładowego kodu, podany w tej [repozytorium github](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Dodawanie punktu końcowego przy użyciu portalu usług sieci Web systemu Microsoft Azure
1. W usłudze Machine Learning Studio w kolumnie nawigacji po lewej stronie kliknij usług sieci Web.
2. W dolnej części pulpitu nawigacyjnego usług sieci web kliknij **Zarządzaj punktami końcowymi w wersji zapoznawczej**.
3. Kliknij pozycję **Add** (Dodaj).
4. Wpisz nazwę i opis dla nowego punktu końcowego. Wybierz poziom rejestrowania i czy jest włączony przykładowych danych. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania usług sieci web Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizowanie modelu uczonego dodano punkt końcowy
Aby ukończyć proces ponownego trenowania, należy zaktualizować uczony model nowy punkt końcowy, który został dodany.

Jeśli dodano punkt końcowy, korzystającą z przykładowego kodu, w tym lokalizacji identyfikowane za pomocą adresu URL pomocy *HelpLocationURL* wartość w danych wyjściowych.

Aby pobrać adres URL ścieżki:

1. Skopiuj i wklej adres URL w przeglądarce.
2. Kliknij link do zasobu aktualizacji.
3. Skopiuj adres URL wpisu żądania PATCH. Na przykład:
   
     ADRES URL POPRAWKI: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Można teraz używać uczonego modelu, można zaktualizować punktu końcowego oceniania, który został utworzony wcześniej.

Następujący przykładowy kod przedstawia sposób użycia *BaseLocation*, *RelativeLocation*, *SasBlobToken*i stosowanie poprawek do adresu URL, aby zaktualizować punkt końcowy.

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

Wartość *nazwa* parametru w *zasobów* powinna odpowiadać nazwie zasobu w zapisany Uczonego modelu w eksperyment predykcyjny. Aby uzyskać nazwę zasobu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij **uczenia maszynowego**.
3. W obszarze nazwy, kliknij obszar roboczy, a następnie kliknij **usług sieci Web**.
4. Pod nazwą, kliknij pozycję **spisu modelu [exp predykcyjne.]** .
5. Kliknij nowy punkt końcowy, który został dodany.
6. Na pulpicie nawigacyjnym punktu końcowego kliknij **aktualizacja zasobów**.
7. Na stronie dokumentacji interfejsu API zasobu aktualizacji usługi sieci web można znaleźć **Nazwa zasobu** w obszarze **zasobów można aktualizować**.

Jeśli Twój token SAS upłynie przed zakończeniem aktualizowanie punktu końcowego, należy wykonać GET z identyfikatorem zadania do uzyskania tokenu od nowa.

Gdy kod zostało pomyślnie uruchomione, nowy punkt końcowy na początek przy użyciu modelu retrained w ciągu około 30 sekund.

## <a name="summary"></a>Podsumowanie
Za pomocą interfejsów API do ponownego trenowania, możesz zaktualizować uczonego modelu predykcyjnego usługi sieci Web, scenariusze, takie jak:

* Model okresowe ponowne szkolenie przy użyciu nowych danych.
* Dystrybucja modelu klientom celem jest umożliwienie im Ponowne szkolenie modelu przy użyciu własnych danych.

## <a name="next-steps"></a>Kolejne kroki
[Rozwiązywanie problemów z ponownym szkoleniem usługi sieci web Azure Machine Learning](troubleshooting-retraining-models.md)


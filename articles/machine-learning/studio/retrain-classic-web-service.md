---
title: Ponowne trenowanie i wdrażanie klasycznej usługi internetowej
titleSuffix: Azure Machine Learning Studio
description: Dowiedz się, jak ponowne szkolenie modelu i aktualizowanie klasyczna usługa sieci web do korzystania z nowo uczonego modelu w usłudze Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: b636883ee1f08fa0fb6d080b6980cd07553dde1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234047"
---
# <a name="retrain-and-deploy-a-classic-studio-web-service"></a>Ponowne szkolenie i wdrażania klasycznej usługi sieci web programu Studio

Ponowne trenowanie modeli uczenia maszynowego jest jednym ze sposobów, aby zapewnić pozostawanie ich dokładne i oparte na dostępnych danych najbardziej odpowiednie. Ten artykuł będzie pokazują, jak ponowne szkolenie klasycznej usługi sieci web Studio. Aby uzyskać wskazówki na temat nowej usługi sieci web Studio doskonalenie [wyświetlić w tym artykule.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już eksperyment predykcyjny i ponownego trenowania eksperymentu. Te kroki są wyjaśnione w [Wytrenujesz i wdrożyć model uczenia maszynowego.](/azure/machine-learning/studio/retrain-machine-learning-model) Jednak zamiast wdrażania modelu uczenia maszynowego jako nowej usługi sieci web, usługi eksperyment predykcyjny zostanie wdrożony jako klasyczna usługa sieci web.
     
## <a name="add-a-new-endpoint"></a>Dodaj nowy punkt końcowy

Usługę predykcyjne sieci web, która została wdrożona zawiera domyślne oceniania punkt końcowy, który jest synchronizowana z oryginalnego szkolenie i ocenianie uczonego modelu eksperymentów. Aby zaktualizować usługę sieci web w taki sposób, aby nowe uczonego modelu, należy utworzyć nowy punkt końcowy oceniania.

Istnieją dwa sposoby, w których można dodać nowy punkt końcowy usługi sieci web:

* Programistyczne
* Przy użyciu portalu usług sieci Web platformy Azure

> [!NOTE]
> Upewnij się, że chcesz dodać punkt końcowy do predykcyjnej usługi sieci Web nie szkolenia usługi sieci Web. Jeśli zostały poprawnie wdrożone szkoleniowe i predykcyjne usługi sieci Web, powinny pojawić się dwie usługi sieci web w osobnych na liście. Predykcyjne usługi sieci Web powinien kończyć się znakiem "[exp predykcyjne.]".
>

### <a name="programmatically-add-an-endpoint"></a>Programowe Dodawanie punktu końcowego

Możesz dodać punktami końcowymi oceniania, korzystającą z przykładowego kodu, podany w tej [repozytorium GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Dodawanie punktu końcowego przy użyciu portalu usług sieci Web platformy Azure

1. W usłudze Machine Learning Studio w kolumnie nawigacji po lewej stronie kliknij usług sieci Web.
1. W dolnej części pulpitu nawigacyjnego usług sieci web kliknij **Zarządzaj punktami końcowymi w wersji zapoznawczej**.
1. Kliknij pozycję **Add** (Dodaj).
1. Wpisz nazwę i opis dla nowego punktu końcowego. Wybierz poziom rejestrowania i czy jest włączony przykładowych danych. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania usług sieci web Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizowanie modelu uczonego dodano punkt końcowy

### <a name="retrieve-patch-url"></a>Pobieranie adresu URL poprawki

### <a name="option-1-programmatically"></a>Opcja 1: Programistyczne

Aby programowo uzyskać poprawny adres URL poprawki, wykonaj następujące kroki:

1. Uruchom [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) przykładowego kodu.
1. Z danych wyjściowych AddEndpoint znaleźć *HelpLocation* wartości i skopiuj adres URL.

   ![HelpLocation w danych wyjściowych przykładu addEndpoint.](./media/retrain-classic/addEndpoint-output.png)
1. Wklej adres URL do przeglądarki, aby przejść do strony, który zawiera linki pomocy dla usługi sieci web.
1. Kliknij przycisk **aktualizacja zasobów** link, aby otworzyć stronę pomocy poprawki.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Opcja 2: Korzystanie z portalu usług sieci Web Azure Machine Learning

Wykonaj następujące kroki, aby uzyskać poprawny URL poprawki przy użyciu portalu sieci web:

1. Zaloguj się do [usług sieci Web Azure Machine Learning](https://services.azureml.net/) portalu.
1. Kliknij przycisk **usług sieci Web** lub **klasycznych usług sieci Web** u góry.
1. Kliknij przycisk pracujemy z użyciem usługi internetowej przyznawania ocen (Jeśli nie zmodyfikujesz domyślną nazwę usługi sieci web, przestanie ona w "[Exp Punktacja.]").
1. Kliknij przycisk **+ nowy**.
1. Po dodaniu punktu końcowego kliknij nazwę punktu końcowego.
1. W obszarze **poprawki** adresu URL, kliknij przycisk **pomocy interfejsu API** o otwarcie strony pomocy stosowania poprawek.

> [!NOTE]
> Jeśli dodano punkt końcowy usługi sieci Web szkolenia zamiast predykcyjne usługi sieci Web zostanie wyświetlony następujący błąd, po kliknięciu **aktualizacja zasobów** łącza: "Niestety, ale ta funkcja nie jest obsługiwana lub dostępna w tym kontekście. Ta usługa sieci Web ma nie można zaktualizować zasobów. Firma Microsoft Przepraszamy za utrudnienia i działają na poprawę tego przepływu pracy".
>

Strona pomocy poprawki zawiera adres URL poprawki, należy użyć i zawiera przykładowy kod, który można użyć w celu wywołania go.

![Adres URL poprawki.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Zaktualizuj punkt końcowy

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
1. W menu po lewej stronie kliknij **uczenia maszynowego**.
1. W obszarze nazwy, kliknij obszar roboczy, a następnie kliknij **usług sieci Web**.
1. Pod nazwą, kliknij pozycję **spisu modelu [exp predykcyjne.]** .
1. Kliknij nowy punkt końcowy, który został dodany.
1. Na pulpicie nawigacyjnym punktu końcowego kliknij **aktualizacja zasobów**.
1. Na stronie dokumentacji interfejsu API zasobu aktualizacji usługi sieci web można znaleźć **Nazwa zasobu** w obszarze **zasobów można aktualizować**.

Jeśli Twój token SAS upłynie przed zakończeniem aktualizowanie punktu końcowego, należy wykonać GET z Identyfikatorem zadania do uzyskania tokenu od nowa.

Gdy kod zostało pomyślnie uruchomione, nowy punkt końcowy na początek przy użyciu modelu retrained w ciągu około 30 sekund.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zarządzania usługami sieci web lub śledzić wiele przebiegów eksperymentów, zobacz następujące artykuły:

* [Zapoznaj się z portalu usług sieci Web](manage-new-webservice.md)
* [Zarządzanie iteracjami eksperymentów](manage-experiment-iterations.md)
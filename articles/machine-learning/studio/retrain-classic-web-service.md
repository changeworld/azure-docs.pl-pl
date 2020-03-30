---
title: Przekwalifikowanie klasycznej usługi sieci web
titleSuffix: ML Studio (classic) - Azure
description: Dowiedz się, jak przeszkolić model i zaktualizować klasyczną usługę sieci web, aby używać nowo przeszkolonego modelu w usłudze Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 8094d64eab1a4b25a76554bf9eb6848c2e4d3493
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204243"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Przekwalifikowanie i wdrożenie klasycznej usługi sieci web Studio

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Przekwalifikowanie modeli uczenia maszynowego jest jednym ze sposobów zapewnienia ich dokładności i na podstawie najbardziej dostępnych danych. W tym artykule pokazano, jak przeszkolić klasyczną usługę sieci web Studio (klasyczną). Aby zapoznać się z przewodnikiem na temat ponownego przeszkolenia nowej usługi sieci web Studio (klasycznej), [zobacz ten artykuł in jakże.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już zarówno eksperyment przekwalifikowania, jak i eksperyment predykcyjny. Te kroki są wyjaśnione w [Retrain i wdrożyć model uczenia maszynowego.](/azure/machine-learning/studio/retrain-machine-learning-model) Jednak zamiast wdrażania modelu uczenia maszynowego jako nowej usługi sieci web, można wdrożyć eksperyment predykcyjny jako klasycznej usługi sieci web.
     
## <a name="add-a-new-endpoint"></a>Dodawanie nowego punktu końcowego

Wdrożona predykcyjna usługa sieci web zawiera domyślny punkt końcowy oceniania, który jest synchronizowany z oryginalnym modelem szkolenia i oceniania eksperymentów. Aby zaktualizować usługę sieci web do nowego modelu przeszkolonego, należy utworzyć nowy punkt końcowy oceniania.

Istnieją dwa sposoby dodawania nowego punktu końcowego do usługi sieci web:

* Programistyczne
* Korzystanie z portalu usług Azure Web Services

> [!NOTE]
> Upewnij się, że dodajesz punkt końcowy do predictive web service, a nie do usługi sieci Web szkolenia. Jeśli poprawnie wdrożono zarówno szkolenie, jak i usługę predykcyjną sieci Web, na liście powinny zostać wyświetlone dwie oddzielne usługi sieci web. Predykcyjna usługa sieci Web powinna zakończyć się na "[predictive exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Programowo dodawanie punktu końcowego

Punkty końcowe oceniania można dodawać przy użyciu przykładowego kodu podanego w tym [repozytorium GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Dodawanie punktu końcowego za pomocą portalu usług sieci Web platformy Azure

1. W programie Machine Learning Studio (klasycznym) w lewej kolumnie nawigacji kliknij pozycję Usługi sieci Web.
1. U dołu pulpitu nawigacyjnego usługi sieci Web kliknij pozycję **Zarządzaj podglądem punktów końcowych**.
1. Kliknij przycisk **Dodaj**.
1. Wpisz nazwę i opis nowego punktu końcowego. Wybierz poziom rejestrowania i czy dane przykładowe są włączone. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania dla usług sieci Web usługi uczenia maszynowego](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizowanie uczonego modelu dodanego punktu końcowego

### <a name="retrieve-patch-url"></a>Pobierz adres URL POPRAWKI

Wykonaj następujące kroki, aby uzyskać poprawny adres URL POPRAWKI za pomocą portalu internetowego:

1. Zaloguj się do portalu [usług azure machine learning web services.](https://services.azureml.net/)
1. Kliknij pozycję **Usługi sieci Web** lub **klasyczne usługi sieci Web** u góry.
1. Kliknij usługę sieci web oceniania, z którą pracujesz (jeśli nie zmodyfikowałeś domyślnej nazwy usługi sieci web, zakończy się na "[Scoring Exp.]").
1. Kliknij **przycisk +NOWOŚĆ**.
1. Po dodaniu punktu końcowego kliknij nazwę punktu końcowego.
1. W obszarze adresu URL **poprawki** kliknij pozycję **Pomoc interfejsu API,** aby otworzyć stronę pomocy dotyczącą poprawek.

> [!NOTE]
> Jeśli punkt końcowy został dodany do usługi sieci Web szkolenia zamiast predictive web service, po kliknięciu łącza **Aktualizuj zasobu** zostanie wyświetlony następujący błąd: "Przepraszamy, ale ta funkcja nie jest obsługiwana lub dostępna w tym kontekście. Ta usługa sieci Web nie ma zasobów, które można aktualizować. Przepraszamy za niedogodności i pracujemy nad poprawą tego przepływu pracy."
>

Strona pomocy PATCH zawiera adres URL patcha, którego musisz użyć, i zawiera przykładowy kod, którego można użyć, aby go wywołać.

![Adres URL poprawki.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Aktualizowanie punktu końcowego

Teraz można użyć modelu przeszkolonego, aby zaktualizować punkt końcowy oceniania, który został utworzony wcześniej.

Poniższy przykładowy kod pokazuje, jak używać *BaseLocation*, *RelativeLocation*, *SasBlobToken*i PATCH URL, aby zaktualizować punkt końcowy.

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
                        RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
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

*ApiKey* i *endpointUrl* dla wywołania można uzyskać z pulpitu nawigacyjnego punktu końcowego.

Wartość *Name* parametru w *zasobach* powinna być zgodna z nazwą zasobu zapisanego modelu przeszkolonego w eksperymencie predykcyjnym. Aby uzyskać nazwę zasobu:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu po lewej stronie kliknij pozycję **Uczenie maszynowe**.
1. W obszarze Nazwa kliknij obszar roboczy, a następnie kliknij pozycję **Usługi sieci Web**.
1. W obszarze Nazwa kliknij pozycję **Model spisu [predictive exp.]**.
1. Kliknij nowy dodany punkt końcowy.
1. Na pulpicie nawigacyjnym punktu końcowego kliknij pozycję **Aktualizuj zasób**.
1. Na stronie Aktualizowanie dokumentacji interfejsu API zasobów dla usługi sieci web można znaleźć **nazwę zasobu** w obszarze **Zasoby, które można aktualizować**.

Jeśli token sygnatury dostępu Współdzielonego wygasa przed zakończeniem aktualizacji punktu końcowego, należy wykonać GET z identyfikatorem zadania, aby uzyskać świeży token.

Po pomyślnym uruchomieniu kodu, nowy punkt końcowy należy rozpocząć przy użyciu modelu przeszkolony w około 30 sekund.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzaniu usługami sieci Web lub śledzeniu wielu przebiegów eksperymentów, zobacz następujące artykuły:

* [Poznaw portalu usług sieci Web](manage-new-webservice.md)
* [Zarządzanie iteracjami eksperymentów](manage-experiment-iterations.md)
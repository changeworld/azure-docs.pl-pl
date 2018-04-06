---
title: Korzystanie z punktów końcowych usługi Azure Machine Learning w Stream Analytics | Dokumentacja firmy Microsoft
description: Funkcje zdefiniowane przez użytkownika język maszyny w analiza strumienia
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 406b258f-b8c2-4e55-953c-b7f84e8e5354
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: a7d76d6015f8e9f08d3493b1c1e237858c341592
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Maszyny Learning integracji usługi analiza strumienia
Analiza strumienia obsługuje funkcje zdefiniowane przez użytkownika, które wyróżnienia do punktów końcowych usługi Azure Machine Learning. Obsługa interfejsu API REST dla tej funkcji została szczegółowo opisana w [biblioteki interfejsu API REST usługi analiza strumienia](https://msdn.microsoft.com/library/azure/dn835031.aspx). Ten artykuł zawiera dodatkowe informacje potrzebne do pomyślnego wykonania tej funkcji w Stream Analytics. Samouczek również została opublikowana i jest dostępny [tutaj](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Omówienie: Usługa Azure Machine Learning terminologia
Microsoft Azure Machine Learning zapewnia narzędzia współpracy, przeciągnij i upuść, używanego do tworzenia, testowania i wdrażania rozwiązań analizy predykcyjnej na podstawie danych. To narzędzie jest nazywany *Azure Machine Learning Studio*. Studio służy do interakcji z zasobami uczenia maszynowego i łatwy sposób tworzenia, testowania i iterację projektu. Poniżej są tych zasobów i ich definicje.

* **Obszar roboczy**: *obszaru roboczego* jest kontenerem, który zawiera inne zasoby usługi Machine Learning razem w kontenerze zarządzania i kontroli.
* **Eksperymentu**: *eksperymenty* są tworzone przez analityków danych, do korzystania z zestawów danych i uczenia modelu uczenia maszynowego.
* **Punkt końcowy**: *punkty końcowe* są Azure Machine Learning obiekt używany do wybranie funkcji jako dane wejściowe, modelu uczenia maszynowego określonego i zwraca scored danych wyjściowych.
* **Ocenianie usługi sieci Web**: A *oceniania webservice* jest kolekcją punktów końcowych, jak wspomniano powyżej.

Każdy punkt końcowy ma interfejsów API dla wykonywania wsadowego i wykonywania synchronicznego. Analiza strumienia używa synchronicznej. Nosi nazwę określonej usługi [żądania/odpowiedzi usługi](../machine-learning/studio/consume-web-services.md) w studio uczenie maszynowe Azure.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Zasobów niezbędnych do zadania usługi analiza strumienia uczenia maszynowego
Na potrzeby usługi analiza strumienia zadania przetwarzania punktu końcowego żądanie/odpowiedź [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), i definicji struktury swagger są wszystkie niezbędne do pomyślnego wykonania. Analiza strumienia ma dodatkowe punktu końcowego, który konstruuje adres url punktu końcowego struktury swagger, wyszukuje interfejsu i zwraca domyślną definicją funkcji zdefiniowanej przez użytkownika dla użytkownika.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurowanie usługi analiza strumienia i funkcji zdefiniowanej przez użytkownika za pomocą interfejsu API REST uczenia maszynowego
Za pomocą interfejsów API REST można tak skonfigurować zadanie do wywołania funkcji Azure maszyny języka. Dostępne są następujące kroki:

1. Tworzenie zadania usługi Stream Analytics
2. Zdefiniuj dane wejściowe
3. Definiowanie danych wyjściowych
4. Tworzenie funkcji zdefiniowanej przez użytkownika (UDF)
5. Zapis transformację Stream Analytics, która wywołuje funkcję zdefiniowaną przez użytkownika
6. Uruchom zadanie

## <a name="creating-a-udf-with-basic-properties"></a>Tworzenie funkcji zdefiniowanej przez użytkownika za pomocą właściwości podstawowe
Na przykład następujący przykładowy kod tworzy skalarne funkcji zdefiniowanej przez użytkownika o nazwie *newudf* wiąże punktu końcowego uczenia maszynowego Azure. Należy pamiętać, że *punktu końcowego* (identyfikator URI usługi) można znaleźć na stronie pomocy interfejsu API dla wybranych usług i *apiKey* można znaleźć na stronie głównej usług.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Przykład treści żądania:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Punkt końcowy RetrieveDefaultDefinition wywołanie domyślnego funkcji zdefiniowanej przez użytkownika
Utworzone szkielet UDF pełnej definicji funkcji zdefiniowanej przez użytkownika jest wymagana. Punkt końcowy RetreiveDefaultDefinition ułatwia rozpoczęcie definicji domyślnej dla funkcji skalarnej powiązanej z punktem końcowym usługi Azure Machine Learning. Ładunek poniżej wymaga pobrania domyślnej definicji funkcji zdefiniowanej przez użytkownika dla funkcji skalarnej powiązanej z punktem końcowym usługi Azure Machine Learning. Rzeczywisty punkt końcowy go nie określa, jak zostały już dostarczone podczas żądania PUT. Analiza strumienia wywołuje podany w żądaniu, jeśli podano jawnie punktu końcowego. W przeciwnym razie używa jednego oryginalnie odwołuje się do. W tym miejscu ma UDF ciągu pojedynczy parametr (zdania) i zwraca jeden wyjściowy typu ciąg, co oznacza etykietę "wskaźniki nastrojów klientów" zdanie to.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Przykład treści żądania:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Przykładowe dane wyjściowe tego poszukać coś chcieliby poniżej.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Poprawka UDF z odpowiedzią
Teraz UDF muszą poprawiono z poprzedniej odpowiedzi, jak pokazano poniżej.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Treść żądania (dane wyjściowe z RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Wdrożenie usługi analiza strumienia transformacji wywołać funkcję zdefiniowaną przez użytkownika
Teraz zapytanie UDF (w tym miejscu o nazwie scoreTweet) dla każdego zdarzenia wejściowe i zapisać dane wyjściowe odpowiedzi dla tego zdarzenia.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

---
title: Wyślij niestandardowe metryki dla zasobów platformy Azure, aby metryki usługi Azure Monitor przechowywania, przy użyciu interfejsu API REST
description: Wyślij niestandardowe metryki dla zasobów platformy Azure, aby metryki usługi Azure Monitor przechowywania, przy użyciu interfejsu API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d36697e6b5765ecf35ed9b3add45cff6c33823a5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958224"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>Wyślij niestandardowe metryki dla zasobów platformy Azure, aby metryki usługi Azure Monitor przechowywania, przy użyciu interfejsu API REST

W tym artykule pokazano, jak wysyłać metryki niestandardowe dla zasobów platformy Azure w magazynie usługi Azure Monitor metryk za pośrednictwem interfejsu API REST.  Po metryki znajdują się w usłudze Azure Monitor, można wykonywać wszystkie te rzeczy z nimi zrobić przy użyciu standardowych metryk, takich jak tworzenie wykresów, alerty, routing je do innych zewnętrznych narzędzi itp.  

>[!NOTE] 
>Interfejs API REST umożliwia jedynie wysyłanie metryki niestandardowe dla zasobów platformy Azure. Aby wysłać metryki dla zasobów w różnych środowiskach lub środowisku lokalnym, możesz użyć [usługi Application Insights](../application-insights/app-insights-api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Tworzenie i autoryzować nazwę główną usługi, aby emitować metryki 

Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory, korzystając z instrukcji w [utworzyć nazwę główną usługi](../active-directory/develop/howto-create-service-principal-portal.md). 

Należy pamiętać, że podczas przechodzenia przez ten proces: 

- Możesz umieścić dowolny adres URL dla adresu URL logowania jednokrotnego.  
- Utwórz nowy wpis tajny klienta dla tej aplikacji  
- Zapisz klucz i identyfikator klienta do użycia w kolejnych krokach.  

Nadaj aplikacja utworzona w ramach kroku 1 uprawnienia "Monitorowanie metryk Publisher" do zasobu, który chcesz emitują metryki względem. Jeśli planujesz korzystanie z aplikacji do emitowania metryki niestandardowe w odniesieniu do wielu zasobów, można przyznać te uprawnienia na poziomie grupy lub subskrypcji zasobów. 

## <a name="get-an-authorization-token"></a>Pobierz token autoryzacji
Otwórz wiersz polecenia i uruchom następujące polecenie
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Zapisywanie tokenu dostępu z odpowiedzi

![Token dostępu](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emituj metryki za pomocą interfejsu API REST 

1. Wklej następujący kod JSON do pliku i zapisz go jako custommetric.json na komputerze lokalnym. Zaktualizuj parametru czasu w pliku JSON. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. W oknie wiersza polecenia Opublikuj dane metryk 
    - Region platformy Azure — muszą być zgodne, regionu geograficznego, który emitujesz metryki dla zasobu. 
    - ResourceID — identyfikator zasobu platformy Azure, które są śledzone metryki względem zasobu.  
    - Token dostępu — Wklej token uzyskaną wcześniej

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Zmiana sygnatury czasowej i wartości w pliku JSON. 
1. Powtórz dwa poprzednie kroki kilka razy, dzięki czemu masz dane przez wiele minut.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Jeśli pojawi się błąd dotyczący część procesu, biorąc pod uwagę następujące

1. Metryki względem subskrypcji lub grupy zasobów nie mogą wystawiać jako swoich zasobów platformy Azure. 
1. Nie można umieścić metrykę do magazynu, który jest ponad 20 minut. Metryki magazynu jest zoptymalizowany pod kątem alertów oraz wykresów w czasie rzeczywistym. 
2. Liczba wymiarów nazw powinny odpowiadać wartości i odwrotnie. Sprawdź wartości. 
2. Może być emitowania metryki względem region, który nie obsługuje metryki niestandardowe. Zobacz [obsługiwane regiony metryk niestandardowych (wersja zapoznawcza)](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Wyświetlaj swoje metryki 

1. Logowanie do witryny Azure Portal 

1. W menu po lewej stronie kliknij **monitora** 

1. Na stronie monitora kliknij **metryki**. 

   ![Token dostępu](./media/metrics-store-custom-rest-api/metrics.png) 

1. Okres agregacji można zmienić **ciągu ostatnich 30 minut**.  

1. W *zasobów* listę rozwijaną, wybierz zasób emitowane metryki względem.  

1. W *przestrzenie nazw* listę rozwijaną, wybierz opcję **QueueProcessing** 

1. W *metryki* listę rozwijaną, wybierz **QueueDepth**.  

 
## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).
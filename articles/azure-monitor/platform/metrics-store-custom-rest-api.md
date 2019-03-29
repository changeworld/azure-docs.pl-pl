---
title: Wyślij metryki niestandardowe dla zasobów platformy Azure do usługi Azure Monitor metryki magazynu przy użyciu interfejsu API REST
description: Wyślij metryki niestandardowe dla zasobów platformy Azure do usługi Azure Monitor metryki magazynu przy użyciu interfejsu API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: aa842979bf86410e9dab97d6209f336eb6b02bd3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621910"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Wyślij metryki niestandardowe dla zasobów platformy Azure do usługi Azure Monitor metryki magazynu przy użyciu interfejsu API REST

W tym artykule pokazano, jak wysyłać metryki niestandardowe dla zasobów platformy Azure w magazynie usługi Azure Monitor metryk za pośrednictwem interfejsu API REST. Po metryki znajdują się w usłudze Azure Monitor, można wykonać wszystkie czynności, które z nich, które możesz wykonać przy użyciu standardowych metryk. Przykłady są wykresy, alerty i routing je do innych narzędzi zewnętrznych.  

>[!NOTE]  
>Interfejs API REST umożliwia jedynie wysyłanie metryki niestandardowe dla zasobów platformy Azure. Aby wysłać metryki dla zasobów w różnych środowiskach lub środowisku lokalnym, możesz użyć [usługi Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Tworzenie i autoryzować nazwę główną usługi, aby emitować metryki 

Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory, korzystając z instrukcjami przedstawionymi w temacie [utworzyć nazwę główną usługi](../../active-directory/develop/howto-create-service-principal-portal.md). 

Gdy przejdziesz przez ten proces, należy pamiętać o następujących: 

- Adres URL logowania, można wprowadzić dowolny adres URL.  
- Utwórz nowy wpis tajny klienta dla tej aplikacji.  
- Zapisz klucz i identyfikator klienta do użycia w kolejnych krokach.  

Nadaj aplikacja utworzona w ramach kroku 1, monitorowania metryk wydawcy, uprawnienia do zasobów, które chcesz emitują metryki względem. Jeśli planujesz korzystanie z aplikacji do emitowania metryki niestandardowe w odniesieniu do wielu zasobów, można przyznać te uprawnienia na poziomie grupy lub subskrypcji zasobów. 

## <a name="get-an-authorization-token"></a>Pobierz token autoryzacji
Otwórz wiersz polecenia i uruchom następujące polecenie:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Zapisywanie tokenu dostępu z odpowiedzi.

![Token dostępu](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emituj metryki za pomocą interfejsu API REST 

1. Wklej następujący kod JSON do pliku i zapisz go jako **custommetric.json** na komputerze lokalnym. Aktualizacja parametru czasu w pliku JSON: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
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

1. W oknie wiersza polecenia po dane metryk: 
   - **azureRegion**. Musi być zgodna regionu geograficznego, zasobów, które w przypadku emitowania metryki. 
   - **resourceID**.  Identyfikator zasobu zasobów platformy Azure, które należy prześledzić metryki względem.  
   - **AccessToken**. Wklej uzyskany wcześniej tokenu.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Zmiana sygnatury czasowej i wartości w pliku JSON. 
1. Powtórz dwa poprzednie kroki kilka razy, dzięki czemu masz dane przez kilka minut.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Jeśli zostanie wyświetlony komunikat o błędzie z niektórych części procesu, należy wziąć pod uwagę następujące informacje dotyczące rozwiązywania problemów:

1. Metryki względem subskrypcji lub grupy zasobów nie mogą wystawiać jako swoich zasobów platformy Azure. 
1. Nie można umieścić metrykę do magazynu, który jest ponad 20 minut. Metryki magazynu jest zoptymalizowany pod kątem alertów oraz wykresów w czasie rzeczywistym. 
2. Liczba wymiarów nazw powinny odpowiadać wartości i na odwrót. Sprawdź wartości. 
2. Może być emitowania metryki względem region, który nie obsługuje metryki niestandardowe. Zobacz [obsługiwane regiony](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Wyświetlaj swoje metryki 

1. Zaloguj się do Portalu Azure. 

1. W menu po lewej stronie wybierz **Monitor**. 

1. Na **Monitor** wybierz opcję **metryki**. 

   ![Wybierz metryki](./media/metrics-store-custom-rest-api/metrics.png) 

1. Okres agregacji można zmienić **ciągu ostatnich 30 minut**.  

1. W **zasobów** menu rozwijanego wybierz zasób emitowane metryki względem.  

1. W **przestrzenie nazw** menu rozwijanego wybierz opcję **QueueProcessing**. 

1. W **metryki** menu rozwijanego wybierz opcję **QueueDepth**.  

 
## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](../../azure-monitor/platform/metrics-custom-overview.md).


---
title: Wysyłanie metryk do bazy danych metryk Usługi Azure Monitor przy użyciu interfejsu API REST
description: Wysyłanie niestandardowych metryk zasobu platformy Azure do magazynu metryk Usługi Azure Monitor przy użyciu interfejsu API REST
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 84709c022631543101889f784231158ebb96b6f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662268"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Wysyłanie niestandardowych metryk zasobu platformy Azure do magazynu metryk Usługi Azure Monitor przy użyciu interfejsu API REST

W tym artykule pokazano, jak wysłać niestandardowe metryki zasobów platformy Azure do magazynu metryk usługi Azure Monitor za pośrednictwem interfejsu API REST. Po metryki są w usłudze Azure Monitor, można wykonać wszystkie rzeczy z nimi, które można zrobić ze standardowymi metrykami. Przykładami są wykresy, alerty i kierowanie ich do innych narzędzi zewnętrznych.  

>[!NOTE]  
>Interfejs API REST zezwala tylko na wysyłanie niestandardowych metryk dla zasobów platformy Azure. Aby wysłać metryki dotyczące zasobów w różnych środowiskach lub lokalnie, można użyć [usługi Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Tworzenie i autoryzowanie jednostki usługi do emitowania metryk 

Utwórz jednostkę usługi w dzierżawie usługi Azure Active Directory przy użyciu instrukcji znalezionych w [witrynie Utwórz jednostkę usługi.](../../active-directory/develop/howto-create-service-principal-portal.md) 

Podczas przechodzenia przez ten proces należy zwrócić uwagę na następujące kwestie: 

- Możesz wprowadzić dowolny adres URL adresu URL logowania.  
- Utwórz nowy klucz tajny klienta dla tej aplikacji.  
- Zapisz klucz i identyfikator klienta do użycia w późniejszych krokach.  

Nadaj aplikacji utworzonej w ramach kroku 1, Monitorowanie metryk Wydawca, uprawnienia do zasobu, który chcesz emitować metryki. Jeśli zamierzasz użyć aplikacji do emitowania niestandardowych metryk względem wielu zasobów, możesz udzielić tych uprawnień na poziomie grupy zasobów lub subskrypcji. 

## <a name="get-an-authorization-token"></a>Pobierz token autoryzacji
Otwórz wiersz polecenia i uruchom następujące polecenie:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Zapisz token dostępu z odpowiedzi.

![Token dostępu](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emituj metrykę za pośrednictwem interfejsu API REST 

1. Wklej następujący JSON do pliku i zapisz go jako **custommetric.json** na komputerze lokalnym. Zaktualizuj parametr czasu w pliku JSON: 
    
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

1. W oknie wiersza polecenia opublikuj dane metryki: 
   - **azureRegion**. Musi odpowiadać regionowi wdrażania zasobu, dla którego emitujesz metryki. 
   - **identyfikator zasobów**.  Identyfikator zasobu platformy Azure, przeciwko którego śledzeniu metryki.  
   - **AccessToken**. Wklej token, który został nabyty wcześniej.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Zmienianie sygnatury czasowej i wartości w pliku JSON. 
1. Powtórz poprzednie dwa kroki kilka razy, więc masz dane przez kilka minut.

## <a name="troubleshooting"></a>Rozwiązywanie problemów 
Jeśli zostanie wyświetlony komunikat o błędzie z jaką częścią procesu, należy wziąć pod uwagę następujące informacje dotyczące rozwiązywania problemów:

1. Nie można wystawiać metryk względem subskrypcji lub grupy zasobów jako zasobu platformy Azure. 
1. Nie można umieścić metryki w sklepie, który ma ponad 20 minut. Magazyn metryk jest zoptymalizowany pod kątem alertów i wykresów w czasie rzeczywistym. 
2. Liczba nazw wymiarów powinna być zgodna z wartościami i odwrotnie. Sprawdź wartości. 
2. Może być emitowanie metryk względem regionu, który nie obsługuje metryk niestandardowych. Zobacz [obsługiwane regiony](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Wyświetlanie danych 

1. Zaloguj się do Portalu Azure. 

1. W menu po lewej stronie wybierz pozycję **Monitor**. 

1. Na stronie **Monitor** wybierz pozycję **Metryki**. 

   ![Wybierz metryki](./media/metrics-store-custom-rest-api/metrics.png) 

1. Zmień okres agregacji na **Ostatnie 30 minut**.  

1. W menu rozwijanym **zasobu** wybierz zasób, dla którego emitowana jest metryka.  

1. W menu rozwijanym **Przestrzenie nazw** wybierz polecenie **KolejkaProcessing**. 

1. W menu rozwijanym **metryki** wybierz **opcję QueueDepth**.  

 
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](../../azure-monitor/platform/metrics-custom-overview.md).


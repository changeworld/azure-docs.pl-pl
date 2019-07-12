---
title: Monitoruj dzienniki diagnostyczne usługi Media Services za pomocą usługi Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób kierowania i przeglądaj dzienniki diagnostyki za pomocą usługi Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 233b043ffdc295fe94ed2e3ba837d4229848df22
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795837"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitoruj dzienniki diagnostyczne usługi Media Services

[Usługa Azure Monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które pomagają zrozumieć, jak działają aplikacje. Szczegółowy opis tej funkcji i aby zobaczyć, dlaczego chcesz przy użyciu dzienników metryki i diagnostyka usługi Azure Media Services, zobacz [usługi Media Services monitora, metryk i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md).

Ten artykuł pokazuje, jak kierować dane do konta magazynu, a następnie Wyświetl dane. 

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).
- Przegląd [usługi Media Services monitora, metryk i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Dane trasy do konta magazynu przy użyciu portalu

1. Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.
1. Przejdź do swojego konta usługi Media Services w, a następnie kliknij przycisk **ustawień diagnostycznych** w obszarze **Monitor**. Zostanie wyświetlona lista wszystkich zasobów w subskrypcji, które generują dane monitorowania za pośrednictwem usługi Azure Monitor. 

    ![Sekcja ustawień diagnostycznych](media/media-services-diagnostic-logs/logs01.png)

1. Kliknij przycisk **Dodaj ustawienie diagnostyczne**.

   Ustawienie diagnostyczne zasobu definiuje, *jakie* dane monitorowania powinny być przekierowywane z określonego zasobu i *gdzie* powinny one trafiać.

1. W wyświetlonej sekcji podaj **nazwę** ustawienia i zaznacz pole wyboru **Zarchiwizuj na koncie magazynu**.

    Wybierz konto magazynu, do którego chcesz wysłać dzienniki, a następnie naciśnij klawisz **OK**.
1. Zaznacz wszystkie pola w obszarach **Dziennik** i **Metryka**. W zależności od typu zasobu może być widoczna tylko jedna z tych opcji. Za pomocą tych pól wyboru można wybrać kategorie danych dzienników i metryk, dostępne dla tego typu zasobu, które mają być wysyłane do wybranego miejsca docelowego — w tym przypadku konta magazynu.

   ![Sekcja ustawień diagnostycznych](media/media-services-diagnostic-logs/logs02.png)
1. Ustaw suwak **Okres przechowywania (w dniach)** na wartość 30. Suwak umożliwia ustawienie liczby dni przechowywania danych monitorowania na koncie magazynu. Starsze dane są automatycznie usuwane przez usługę Azure Monitor. Wpisanie wartości zero powoduje, że dane są przechowywane przez nieograniczony czas.
1. Kliknij polecenie **Zapisz**.

Dane monitorowania z zasobu będą teraz przepływać do konta magazynu.

## <a name="route-data-to-the-storage-account-using-the-cli"></a>Dane trasy do konta magazynu przy użyciu interfejsu wiersza polecenia

Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, należy uruchomić następujące `az monitor diagnostic-settings` interfejsu wiersza polecenia: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Na przykład:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Wyświetlanie danych na koncie magazynu przy użyciu portalu

Jeśli wykonano powyższe kroki, dane zaczęły już przepływać do konta magazynu.

Może upłynąć nawet pięć minut, zanim zdarzenie pojawi się na koncie magazynu.

1. W portalu przejdź do sekcji **Konta magazynu** widocznej na pasku nawigacyjnym po lewej stronie.
1. Znajdź konto magazynu utworzone w poprzedniej sekcji i kliknij je.
1. Kliknij pozycję **obiektów blob**, kliknij kontener oznaczony **insights — dzienniki keydeliveryrequests**. Jest to kontener, który zawiera dzienniki. Dane monitorowania są grupowane w kontenerach według Identyfikatora zasobu, następnie według daty i godziny.
1. Przejdź do pliku PT1H.json, klikając identyfikator zasobu, datę i godzinę w obrębie kontenerów. Kliknij plik PT1H.json, a następnie kliknij przycisk **Pobierz**.

 Możesz teraz wyświetlić zdarzenie JSON przechowywane na koncie magazynu.

### <a name="examples-of-pt1hjson"></a>Przykłady PT1H.json

#### <a name="clear-key-delivery-log"></a>Dostarczania klucza czyszczenia dziennika

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Dziennik zaszyfrowanego klucza dostawy Widevine

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>Zobacz także

* [Metryki usługi Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Dzienniki diagnostyczne platformy Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Jak zbierać i wykorzystywać dane dzienników z zasobów platformy Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie metryk](media-services-metrics-howto.md)

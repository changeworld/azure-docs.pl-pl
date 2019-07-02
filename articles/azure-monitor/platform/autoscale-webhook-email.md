---
title: Korzystanie ze skalowania automatycznego do wysyłania wiadomości e-mail i elementy webhook powiadomień o alertach
description: 'Zobacz, jak używać akcji skalowania automatycznego do wywołania adresu URL sieci web lub wysyłania powiadomień e-mail w usłudze Azure Monitor. '
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 25ef2541dfa0b4cbd6e11d64381da645acfe653a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787315"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Użyj akcji skalowania automatycznego, aby wysyłać wiadomości e-mail i elementy webhook powiadomienia o alertach w usłudze Azure Monitor
Ten artykuł pokazuje, jak skonfigurować wyzwalaczy, aby było wywołanie konkretne adresy URL lub wysyłać wiadomości e-mail oparte na akcje skalowania automatycznego na platformie Azure.  

## <a name="webhooks"></a>Elementy webhook
Elementy Webhook pozwalają kierować powiadomienia o alertach platformy Azure z innymi systemami powiadomień przetwarzania końcowego lub niestandardowy. Na przykład routingu alertu do usługi, które może obsłużyć przychodzące żądania sieci web, aby wysyłać wiadomość SMS, dziennik błędów, powiadomienia zespołu za pomocą czatu lub obsługi komunikatów usługi itd. Element webhook identyfikator URI musi być prawidłowy punkt końcowy HTTP lub HTTPS.

## <a name="email"></a>Email
Wiadomości e-mail mogą być wysyłane do dowolnego adresu e-mail. Administratorów i współadministratorów subskrypcji, w której działa zasada również zostanie powiadomiony.

## <a name="cloud-services-and-web-apps"></a>Usługi w chmurze i aplikacji sieci Web
Użytkownik może uczestnictwo w witrynie Azure portal usług Cloud Services i farmy serwerów (aplikacje sieci Web).

* Wybierz **skalowanie przez** metryki.

![Skalowanie przez](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych
Aby uzyskać nowsze maszyny wirtualne utworzone przy użyciu usługi Resource Manager (zestawy skalowania maszyn wirtualnych) można skonfigurować to przy użyciu interfejsu API REST, szablonów usługi Resource Manager, programu PowerShell i interfejsu wiersza polecenia. Portal interfejsu nie jest jeszcze dostępna.
Korzystając z interfejsu API REST lub Menedżera zasobów szablonu, obejmują element powiadomienia przy użyciu następujących opcji.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Pole | Obowiązkowe? | Opis |
| --- | --- | --- |
| Operacja |tak |Wartość musi być "Skalowanie" |
| sendToSubscriptionAdministrator |tak |musi mieć wartość "true" lub "false" |
| sendToSubscriptionCoAdministrators |tak |musi mieć wartość "true" lub "false" |
| customEmails |tak |wartość może być null [] lub tablicę ciągów, wiadomości e-mail |
| webhooks |tak |wartość może być zerowy lub nieprawidłowy identyfikator Uri |
| serviceUri |tak |Nieprawidłowy identyfikator Uri protokołu https |
| properties |tak |Wartość musi być pusta {} lub może zawierać pary klucz wartość |

## <a name="authentication-in-webhooks"></a>Uwierzytelnianie w elementów webhook
Element webhook można uwierzytelnić przy użyciu uwierzytelniania opartego na tokenach, oszczędzić elementu webhook identyfikatora URI identyfikatorem token jako parametr zapytania. Na przykład https: \/ /mysamplealert/webcallback? tokenid = sometokenid & someparameter = wartość somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Schematu ładunek elementu webhook powiadomienia skalowania automatycznego
Po wygenerowaniu powiadomienia skalowania automatycznego, następujące metadane są objęte ładunek elementu webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Pole | Obowiązkowe? | Opis |
| --- | --- | --- |
| status |tak |Stan, który wskazuje, że zostało wygenerowane akcji skalowania automatycznego |
| Operacja |tak |Zwiększanie wystąpień będzie "Limit skalowania" i dla spadek wystąpień będzie "W skali" |
| context |tak |Kontekst akcji skalowania automatycznego |
| timestamp |tak |Sygnaturę czasową, gdy akcja skalowania automatycznego została wyzwolona |
| id |Tak |Identyfikator Menedżera zasobów Ustawienia skalowania automatycznego |
| name |Yes |Nazwa ustawienia automatycznego skalowania |
| details informacje |Yes |Opis akcji, która miała usługi skalowania automatycznego i zmianę liczby wystąpień |
| subscriptionId |Tak |Identyfikator zasobu docelowego, który wykonywane jest skalowanie subskrypcji |
| resourceGroupName |Yes |Nazwa grupy zasobów zasobu docelowego, który wykonywane jest skalowanie |
| resourceName |Tak |Nazwa zasobu docelowego, który wykonywane jest skalowanie |
| Typ zasobu |Yes |Trzy obsługiwane wartości: "microsoft.classiccompute/domainnames/slots/roles" - role usługi w chmurze, "microsoft.compute/virtualmachinescalesets" — Virtual Machine Scale Sets i "Microsoft.Web/serverfarms" - aplikacji sieci Web |
| resourceId |Tak |Identyfikator zasobu docelowego, który wykonywane jest skalowanie usługi Resource Manager |
| portalLink |Yes |Link do portalu Azure na stronie Podsumowanie zasób docelowy |
| oldCapacity |Tak |Bieżąca (stare) liczba wystąpień podczas automatycznego skalowania trwało akcji skalowania |
| newCapacity |Tak |Skalowanie automatyczne skalowanie zasobów do nowej liczba wystąpień |
| properties |Nie |Opcjonalny. Zestaw < klucz, wartość > pary (na przykład, Dictionary < String, String >). Pole właściwości jest opcjonalne. W przypadku niestandardowego interfejsu użytkownika lub przepływu pracy aplikacji logiki można wprowadzić klucze i wartości, które można przekazać za pomocą ładunku. Alternatywny sposób, aby przekazywać niestandardowe właściwości wychodzące wywołanie elementu webhook jest użycie elementu webhook identyfikator URI sam (jako parametry zapytania) |


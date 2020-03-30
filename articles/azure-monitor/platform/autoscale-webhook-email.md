---
title: Wysyłanie powiadomień o alertach e-mail i webhook za pomocą automatycznego skalowania
description: Dowiedz się, jak używać akcji skalowania automatycznego do wywoływania adresów URL sieci Web lub wysyłania powiadomień e-mail w usłudze Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120693"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Wysyłanie powiadomień o alertach poczty e-mail i programu Webhook w monitorze platformy Azure Za pomocą akcji skalowania automatycznego
W tym artykule pokazano, jak skonfigurować wyzwalacze, dzięki czemu można wywołać określone adresy URL sieci web lub wysyłać wiadomości e-mail na podstawie akcji skalowania automatycznego na platformie Azure.  

## <a name="webhooks"></a>Elementy webhook
Elementów Webhook umożliwiają kierowanie powiadomień alertów platformy Azure do innych systemów w celu przetwarzania końcowego lub powiadomień niestandardowych. Na przykład routing alertu do usług, które mogą obsługiwać przychodzące żądanie sieci web, aby wysłać SMS, błędy dziennika, powiadomić zespół za pomocą czatu lub usług obsługi wiadomości, itp. Identyfikator URI elementu webhook musi być prawidłowym punktem końcowym HTTP lub HTTPS.

## <a name="email"></a>Adres e-mail
Wiadomość e-mail może zostać wysłana na dowolny prawidłowy adres e-mail. Administratorzy i współadministratorzy subskrypcji, w której reguła jest uruchomiona, również zostaną powiadomieni.

## <a name="cloud-services-and-app-services"></a>Usługi w chmurze i usługi aplikacji
Możesz wyrazić zgodę z witryny Azure portal dla usług w chmurze i farm serwerów (usługi aplikacji).

* Wybierz **skalę według** metryki.

![skalę według](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych
W przypadku nowszych maszyn wirtualnych utworzonych za pomocą Menedżera zasobów (zestawy skalowania maszyny wirtualnej) można skonfigurować to przy użyciu interfejsu API REST, szablonów Menedżera zasobów, programu PowerShell i interfejsu wiersza polecenia. Interfejs portalu nie jest jeszcze dostępny.
W przypadku korzystania z interfejsu API REST lub szablonu Menedżera zasobów należy dołączyć element powiadomień w [ustawieniach skalowania automatycznego](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) z następującymi opcjami.

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
| Operacji |tak |wartość musi być "Skala" |
| sendToSubscriptionAdministrator |tak |wartość musi być "true" lub "false" |
| sendToSubscriptionCoAdministrators |tak |wartość musi być "true" lub "false" |
| customEmails |tak |wartość może mieć wartość null [] lub tablicę ciągów wiadomości e-mail |
| elementów webhook |tak |wartość może mieć wartość null lub prawidłową wartość Uri |
| identyfikator URI usługi |tak |prawidłowy https Uri |
| properties |tak |wartość musi {} być pusta lub może zawierać pary klucz-wartość |

## <a name="authentication-in-webhooks"></a>Uwierzytelnianie w elementach webhook
Element webhook można uwierzytelnić przy użyciu uwierzytelniania opartego na tokenie, gdzie zapisać identyfikator URI elementu webhook z identyfikatorem tokenu jako parametr kwerendy. Na przykład https:\//mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Schemat ładunku za pomocą elementu webhook powiadomień automatycznego
Po wygenerowaniu powiadomienia o skalowaniu automatycznym w ładunku elementu webhook znajdują się następujące metadane:

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
| status |tak |Stan wskazujący, że została wygenerowana akcja skalowania automatycznego |
| Operacji |tak |W przypadku zwiększenia liczby wystąpień będzie to "Skala w poziomie", a w przypadku zmniejszenia liczby wystąpień będzie to "Skaluj w" |
| kontekst |tak |Kontekst akcji skalowania automatycznego |
| sygnatura czasowa |tak |Sygnatura czasowa po wyzwoleniu akcji skalowania automatycznego |
| id |Tak |Identyfikator Menedżera zasobów ustawienia skalowania automatycznego |
| name |Tak |Nazwa ustawienia skalowania automatycznego |
| Szczegóły |Tak |Wyjaśnienie akcji, którą podjęła usługa skalowania automatycznego i zmiana liczby wystąpień |
| subscriptionId |Tak |Identyfikator subskrypcji zasobu docelowego, który jest skalowany |
| resourceGroupName |Tak |Nazwa grupy zasobów zasobu docelowego, który jest skalowany |
| resourceName |Tak |Nazwa zasobu docelowego, który jest skalowany |
| resourceType |Tak |Trzy obsługiwane wartości: "microsoft.classiccompute/domainnames/slots/roles" - Role usługi w chmurze, "microsoft.compute/virtualmachinescalesets" - Zestawy skalowania maszyny wirtualnej i "Microsoft.Web/serverfarms" - Web App |
| resourceId |Tak |Identyfikator Menedżera zasobów zasobu docelowego, który jest skalowany |
| portalLink (link) |Tak |Łącze portalu Azure do strony podsumowania zasobu docelowego |
| oldCapacity (zdolność produkcyjna) |Tak |Bieżąca (stara) liczba wystąpień, gdy skalowanie automatyczne miało akcję skalowania |
| newCapacity (NowaKażdość) |Tak |Liczba nowych wystąpień, zgodnie z którą skalowanie automatyczne przeskalował zasób do |
| properties |Nie |Element opcjonalny. Zestaw <, pary> wartości (na przykład Słownik <ciąg, Ciąg>). Pole właściwości jest opcjonalne. W niestandardowym interfejsie użytkownika lub przepływie pracy opartym na aplikacji logiki można wprowadzić klucze i wartości, które mogą być przekazywane przy użyciu ładunku. Alternatywnym sposobem przekazywania właściwości niestandardowych z powrotem do wychodzącego wywołania elementu webhook jest użycie samego identyfikatora URI elementu webhook (jako parametrów kwerendy) |


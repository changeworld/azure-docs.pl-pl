---
title: Zaawansowane automatyczne skalowanie przy użyciu usługi Azure Virtual Machines
description: Za pomocą usługi Resource Manager i usługi VM Scale Sets wiele zasad i profilów, których wysyłanie wiadomości e-mail i Wywołaj element webhook adresy URL akcji skalowania.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 6da653bc94c8b549282ab9124dba23b08771c5f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787810"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Konfiguracja zaawansowane automatyczne skalowanie usługi VM Scale Sets za pomocą szablonów usługi Resource Manager
Możesz skalować i skalowalnego w poziomie w zestawach skalowania maszyn wirtualnych na podstawie dotyczące progów metryk wydajności, według powtarzającego się harmonogramu lub według określonej daty. Można również skonfigurować powiadomienia e-mail i elementy webhook dla akcji skalowania. W tym instruktażu przedstawiono przykład konfigurowania tych obiektów w zestawie skalowania maszyn wirtualnych przy użyciu szablonu usługi Resource Manager.

> [!NOTE]
> W tym przewodniku opisano kroki zestawów skalowania maszyn wirtualnych, te same informacje ma zastosowanie do automatycznego skalowania [usług w chmurze](https://azure.microsoft.com/services/cloud-services/), [App Service — Web Apps](https://azure.microsoft.com/services/app-service/web/), i [usługi API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Prosty skalowania w pionie i poziomie ustawienie w zestawie skalowania maszyn wirtualnych na podstawie metryki wydajności prosty przykład procesora CPU, dotyczą [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) i [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) dokumentów
>
>

## <a name="walkthrough"></a>Przewodnik
W tym przewodniku używamy [Eksploratora zasobów Azure](https://resources.azure.com/) do konfigurowania i Aktualizuj ustawienie skalowania automatycznego dla zestawu skalowania. Eksplorator zasobów Azure to prosty sposób zarządzać zasobami platformy Azure za pomocą szablonów usługi Resource Manager. Jeśli jesteś nowym użytkownikiem narzędzia Eksploratora zasobów Azure, zapoznaj się z [wprowadzeniu](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Wdrażanie nowego zestawu z ustawieniem podstawowe skalowania automatycznego skalowania. W tym artykule wykorzystano jeden z galerii Szybki Start Azure, która ma Windows zestawu skalowania przy użyciu szablonu podstawowa funkcja automatycznego skalowania. Zestawy skalowania systemu Linux, działają tak samo.
2. Po utworzeniu zestawu skalowania, przejdź do zasobu zestawu skalowania z usługi Azure Resource Explorer. Zobaczysz następujący widok w węźle Microsoft.Insights.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Wykonywanie szablonu, który utworzył domyślne ustawienie automatycznego skalowania o nazwie **"autoscalewad"**. Po prawej stronie można wyświetlić pełna definicja to ustawienie automatycznego skalowania. W tym przypadku domyślne ustawienie skalowania automatycznego jest dostarczany z regułą skalowania w poziomie i skalowanie w % na podstawie procesora CPU.  

3. Teraz możesz dodać więcej profile i zasady na podstawie harmonogramu lub określonych wymagań. Ustawienia automatycznego skalowania możemy utworzyć przy użyciu trzech profilów. Aby poznać profile i regułami automatycznego skalowania, zobacz [najlepszych praktykach dotyczących skalowania](autoscale-best-practices.md).  

    | Profile & reguły | Opis |
    |--- | --- |
    | **Profil** |**Wydajność/metryki na podstawie** |
    | Reguła |Liczba komunikatów w kolejce usługi Service Bus > x |
    | Reguła |Liczba komunikatów w kolejce usługi Service Bus < y |
    | Reguła |Procent użycia procesora CPU > n |
    | Reguła |Procent użycia procesora CPU < p |
    | **Profil** |**Dzień tygodnia godziny rano (nie reguły)** |
    | **Profil** |**Dniu premiery produktu (nie reguły)** |

4. Poniżej przedstawiono hipotetyczny scenariusza skalowania, których będziemy używać na potrzeby tego przewodnika.

   * **Obciążenia na podstawie** — Chcę skalowanie w poziomie lub w oparciu o obciążenie w mojej aplikacji hostowanych na Moje set.* skalowania
   * **Rozmiar kolejki wiadomości** — czy mogę użyć kolejki usługi Service Bus dla komunikatów przychodzących do mojej aplikacji. Liczba komunikatów w kolejce i procent użycia procesora CPU i Konfigurowanie profilu domyślnego do wyzwalania akcji skalowania, jeśli liczba komunikatów lub Procesora osiąga wartość progową.\*
   * **Czas tydzień i dzień** — Chcę, aby co tydzień cyklicznego "godziny dnia", na podstawie profil o nazwie "Dzień roboczy rano Hours". Na podstawie danych historycznych, wiem, że warto mieć określonej liczby wystąpień maszyn wirtualnych do obsługi obciążenia mojej aplikacji, w tym czasie.\*
   * **Wybranych dat** — po dodaniu profilu produktu Uruchom dnia. Czy mogę Planuj z wyprzedzeniem dla określonej daty, Moja aplikacja jest gotowa do obsługi obciążenia powodu marketingu anonsów i testujemy nowego produktu w aplikacji.\*
   * *Ostatnie dwa profile mogą mieć również inne metryki na podstawie reguły wydajności w nich. W takim przypadku postanowiła, czy nie masz, i zamiast polegać na metryki wydajności domyślnej reguły na podstawie. Reguły są opcjonalne dla profilów cyklicznych i na podstawie daty.*

     Aparat skalowania automatycznego priorytetyzacji profile i reguł, również są przechwytywane w [najlepsze rozwiązania skalowania automatycznego](autoscale-best-practices.md) artykułu.
     Aby uzyskać listę typowe metryki automatycznego skalowania można znaleźć [typowe metryki automatycznego skalowania](autoscale-common-metrics.md)

5. Upewnij się, że korzystasz z **odczytu/zapisu** trybu w Eksploratorze zasobów

    ![Autoscalewad, ustawienie skalowania automatycznego domyślne](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Kliknij przycisk Edytuj. **Zastąp** elementu "profile" w ustawieniu skalowania automatycznego za pomocą następującej konfiguracji:

    ![Profile](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Obsługiwane pola i ich wartości, zobacz [dokumentację interfejsu API REST skalowania automatycznego](https://msdn.microsoft.com/library/azure/dn931928.aspx). Ustawienie skalowania automatycznego zawiera teraz trzy profile, które opisano wcześniej.

7. Na koniec Przyjrzyj się skalowania automatycznego **powiadomień** sekcji. Powiadomień dotyczących automatycznego skalowania pozwalają wykonać trzy czynności podczas skalowania w poziomie lub pomyślnie jest wyzwalany w działaniu.
   - Powiadom administratora i współadministratorów subskrypcji
   - Wyślij wiadomość e-mail w grupie użytkowników
   - Wyzwala wywołanie elementu webhook. Gdy wywoływane, ten element webhook wysyła metadane dotyczące warunek skalowania automatycznego i zasobów zestawu skalowania. Aby dowiedzieć się więcej na temat ładunek elementu webhook skalowania automatycznego, zobacz [Konfigurowanie elementu Webhook i powiadomienia E-mail dotyczące automatycznego skalowania](autoscale-webhook-email.md).

   Dodaj następujący kod do zastępowania ustawienie automatycznego skalowania usługi **powiadomień** elementu, którego wartość ma wartość null

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
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

   Trafienia **umieścić** przycisku w Eksploratorze zasobów, aby zaktualizować ustawienia skalowania automatycznego.

Zaktualizowano ustawienia automatycznego skalowania na zestawie obejmują wiele profilów skalowania i skalować powiadomienia skalowania maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki
Użyj poniższych linków, aby dowiedzieć się więcej na temat skalowania automatycznego.

[Rozwiązywanie problemów z funkcją automatycznego skalowania za pomocą zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Typowe metryki automatycznego skalowania](autoscale-common-metrics.md)

[Najlepsze rozwiązania dotyczące skalowania automatycznego platformy Azure](autoscale-best-practices.md)

[Zarządzanie funkcją automatycznego skalowania przy użyciu programu PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Zarządzanie Skalowanie automatyczne za pomocą interfejsu wiersza polecenia](cli-samples.md#autoscale)

[Konfigurowanie elementu Webhook i powiadomienia E-mail dotyczące automatycznego skalowania](autoscale-webhook-email.md)

[Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings) odwołanie do szablonu

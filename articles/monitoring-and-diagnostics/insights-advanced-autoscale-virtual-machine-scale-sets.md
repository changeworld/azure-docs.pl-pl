---
title: Funkcja automatycznego skalowania zaawansowanych za pomocą usługi Azure Virtual Machines
description: Używa usługi Resource Manager i zestawy skalowania maszyny Wirtualnej z wielu zasad i profilów, które wysyłania wiadomości e-mail i Wywołaj adresy URL elementu webhook z akcji skalowania.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 9ff8c28a139d9a16d31a61b560ef7f5759d0a3f5
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267734"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Zestawy skalowania maszyny Wirtualnej za pomocą szablonów usługi Resource Manager konfiguracji zaawansowanej skalowania automatycznego
Można w skali i skalowalnego w poziomie w zestawy skalowania maszyny wirtualnej w oparciu metryki progów wydajności, zgodnie z cyklicznym harmonogramem lub w określonym dniu. Można również skonfigurować powiadomienia e-mail i elementu webhook dla akcji skalowania. W tym przewodniku przedstawiono przykład konfigurowania tych obiektów przy użyciu szablonu usługi Resource Manager dla zestawu skalowania maszyny Wirtualnej.

> [!NOTE]
> Gdy w tym przewodniku opisano kroki dla zestawy skalowania maszyny Wirtualnej, te same informacje dotyczą Skalowanie automatyczne [usługi w chmurze](https://azure.microsoft.com/services/cloud-services/), i [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/).
> Dla prostego skali we/wy ustawienie dla zestawu skalowania maszyny Wirtualnej w oparciu o metryki wydajności prosty przykład procesora CPU, zapoznaj się [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) i [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) dokumentów
>
>

## <a name="walkthrough"></a>Przewodnik
W tym przewodniku używamy [Eksploratora zasobów Azure](https://resources.azure.com/) do konfigurowania i aktualizowania w ustawieniu skalowania automatycznego dla zestawu skalowania. Eksploratora zasobów Azure to prosty sposób zarządzania zasobami Azure za pomocą szablonów Resource Manager. Jeśli jesteś nowym użytkownikiem narzędzia Eksploratora zasobów Azure, przeczytaj [to wprowadzenie](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Wdrożenie nowej skali ustawić przy użyciu ustawienia skalowania automatycznego podstawowe. W tym artykule wykorzystano z galerii Szybki Start Azure, z systemu Windows zestawu przy użyciu szablonu podstawowego skalowania automatycznego skalowania. Zestawy skalowania Linux działają tak samo.
2. Po utworzeniu zestawu skalowania, przejdź do zasobu zestaw skali z Eksploratora zasobów Azure. Można znaleźć w następujących tematach w węźle elemencie Microsoft.Insights.

    ![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

    Wykonanie szablon został utworzony domyślnego ustawienia skalowania automatycznego o nazwie **"autoscalewad"**. Po prawej stronie można wyświetlić pełnej definicji tego ustawienia skalowania automatycznego. W takim przypadku domyślne ustawienie skalowania automatycznego zawiera reguły Procesora % na podstawie skalowalnego w poziomie i w skali.  

3. Teraz można dodać więcej profilów i reguły na podstawie harmonogramu lub określone wymagania. Utworzymy ustawieniu skalowania automatycznego przy użyciu trzech profilów. Aby zrozumieć, profile i zasady automatycznego skalowania, przejrzyj [najlepsze rozwiązania w zakresie skalowania automatycznego](insights-autoscale-best-practices.md).  

    | Profile & reguły | Opis |
    |--- | --- |
    | **Profil** |**Na podstawie wydajności/metryki** |
    | Reguła |Liczba wiadomości w kolejce magistrali usługi > x |
    | Reguła |Liczba wiadomości w kolejce magistrali usługi < y |
    | Reguła |Procent użycia procesora CPU > n |
    | Reguła |Procent użycia procesora CPU < p |
    | **Profil** |**Dzień tygodnia godziny rano (Brak reguł)** |
    | **Profil** |**Dzień uruchamiania produktu (Brak reguł)** |

4. Oto scenariusza skalowania hipotetycznego używamy na potrzeby tego przewodnika.

    * **Na podstawie obciążenia** — Chcę skalowania w poziomie lub w oparciu o obciążenie mojej aplikacji hostowanych na mój set.* skali
    * **Rozmiar kolejki wiadomości** -używam kolejką usługi Service Bus dla komunikatów przychodzących do mojej aplikacji. Użyj kolejki wiadomości i procent użycia procesora CPU i skonfigurować profil domyślny, aby wyzwolić akcji skalowania, jeśli liczba komunikatów lub Procesora trafienia threshold.*
    * **Czas tydzień i dzień** — Chcę co tydzień cyklicznego profilu "czasu dnia", na podstawie o nazwie "Godzin dnia tygodnia rano". Na podstawie danych historycznych, wiadomo, że warto mieć określoną liczbę wystąpień maszyn wirtualnych do obsługi obciążenia mojej aplikacji podczas tego time.*
    * **Wybranych dat** — po dodaniu profilu produktu uruchamianie dnia. I planowanie określonymi datami tak Moja aplikacja jest gotowa do obsługi obciążenia powodu anonsów marketing i testujemy nowego produktu w application.*
    * *Ostatnie dwa profile ma także inne metryki na podstawie zasad wydajności w nich. W takim przypadku I postanowiła nie istnieje, a zamiast tego polegać na domyślne metryki wydajności na podstawie reguł. Reguły są opcjonalne dla profilów cyklicznego i oparty na dacie.*

    Aparat skalowania automatycznego priorytetyzacji profilów i zasad jest również przechwycone w [najlepsze rozwiązania w zakresie Skalowanie automatyczne](insights-autoscale-best-practices.md) artykułu.
    Listę typowych metryki skalowania automatycznego zawiera [wspólnej metryki automatycznego skalowania](insights-autoscale-common-metrics.md)

5. Upewnij się, że jesteś na **odczytu/zapisu** trybu w Eksploratorze zasobów

    ![Autoscalewad, ustawienie skalowania automatycznego domyślne](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Kliknij przycisk Edytuj. **Zastąp** element "profilów" w ustawieniu skalowania automatycznego przy użyciu następującej konfiguracji:

    ![Profile](./media/insights-advanced-autoscale-vmss/profiles.png)

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
    Obsługiwane pól i ich wartości, zobacz [dokumentacja interfejsu API REST skalowania automatycznego](https://msdn.microsoft.com/library/azure/dn931928.aspx). Teraz ustawienia skalowania automatycznego zawiera trzy profile wyjaśniono wcześniej.

7. Na koniec przyjrzeć się skalowania automatycznego **powiadomień** sekcji. Powiadomienia skalowania automatycznego umożliwiają wykonać trzy czynności podczas skalowania w poziomie lub w akcji pomyślnie zostanie wywołany.
   - Powiadom administratora i współadministratorzy subskrypcji
   - Wyślij wiadomość e-mail zbioru użytkowników
   - Wyzwala wywołanie elementu webhook. Po, ten element webhook wysyła metadane dotyczące warunku Skalowanie automatyczne i zestawu skalowania zasobu. Aby dowiedzieć się więcej na temat ładunku webhook skalowania automatycznego, zobacz [skonfigurować elementu Webhook & powiadomienia E-mail dotyczące skalowania automatycznego](insights-autoscale-to-webhook-email.md).

   Dodaj następującą wartość do ustawienia skalowania automatycznego zastąpienia z **powiadomień** elementu, którego wartość ma wartość null

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

   Trafienia **Put** przycisk w Eksploratorze zasobów, aby zaktualizować ustawienia skalowania automatycznego.

Zaktualizowano Ustawienia skalowania automatycznego skali maszyny Wirtualnej obejmują wiele profilów skalowania i skalować powiadomienia.

## <a name="next-steps"></a>Następne kroki
Użyj poniższych linków, aby dowiedzieć się więcej na temat skalowania automatycznego.

[Rozwiązywanie problemów z automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Typowe metryki automatycznego skalowania](insights-autoscale-common-metrics.md)

[Najlepsze rozwiązania dotyczące usługi Azure skalowania automatycznego](insights-autoscale-best-practices.md)

[Zarządzaj skalowania automatycznego przy użyciu programu PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Zarządzaj skalowania automatycznego przy użyciu interfejsu wiersza polecenia](insights-cli-samples.md#autoscale)

[Konfigurowanie elementu Webhook & powiadomienia E-mail dotyczące skalowania automatycznego](insights-autoscale-to-webhook-email.md)

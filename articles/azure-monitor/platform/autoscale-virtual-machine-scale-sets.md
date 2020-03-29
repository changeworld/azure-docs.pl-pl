---
title: Zaawansowane skalowanie automatyczne przy użyciu maszyn wirtualnych platformy Azure
description: Używa Zestawów skalowania Menedżera zasobów i maszyn wirtualnych z wieloma regułami i profilami, które wysyłają adresy URL poczty e-mail i elementów webhook z akcjami skalowania.
ms.topic: conceptual
ms.date: 02/22/2016
ms.subservice: autoscale
ms.openlocfilehash: e22806ff94ce2eb830bb6918bfc7f80e5ad3ba0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364224"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Zaawansowana konfiguracja skalowania automatycznego przy użyciu szablonów Menedżera zasobów dla zestawów skalowania maszyn wirtualnych
Można skalować i skalować w poziomie w zestawach skalowania maszyny wirtualnej na podstawie progów metryk wydajności, harmonogramu cyklicznego lub określonej daty. Można również skonfigurować powiadomienia e-mail i webhook dla akcji skalowania. W tym przewodniku przedstawiono przykład konfigurowania wszystkich tych obiektów przy użyciu szablonu Menedżera zasobów w zestawie skalowania maszyny Wirtualnej.

> [!NOTE]
> W tym przewodniku wyjaśniono kroki dotyczące zestawów skalowania maszyn wirtualnych, te same informacje dotyczą skalowania automatycznego [usługi w chmurze,](https://azure.microsoft.com/services/cloud-services/) [usługi app service - aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/)i usługi [zarządzania interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Dla prostego ustawienia skalowania w/wymetrycznego w zestawie skalowania maszyny Wirtualnej na podstawie prostej metryki wydajności, takiej jak procesor, należy zapoznać się z dokumentami systemu [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) i [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Przewodnik
W tym instruktażu używamy [Eksploratora zasobów platformy Azure](https://resources.azure.com/) do konfigurowania i aktualizowania ustawienia skalowania automatycznego dla zestawu skalowania. Eksplorator zasobów platformy Azure to prosty sposób zarządzania zasobami platformy Azure za pomocą szablonów usługi Resource Manager. Jeśli jesteś nowym użytkownikem narzędzia Azure Resource Explorer, przeczytaj [to wprowadzenie](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Wdrażanie nowego zestawu skalowania z podstawowym ustawieniem skalowania automatycznego. W tym artykule użyto tego z Galerii Szybki start platformy Azure, która ma zestaw skalowania systemu Windows z podstawowym szablonem skalowania automatycznego. Zestawy skalowania Linuksa działają w ten sam sposób.
2. Po utworzeniu zestawu skalowania przejdź do zasobu zestawu skalowania z Eksploratora zasobów platformy Azure. W węźle Microsoft.Insights są widoczne następujące informacje.

    ![Eksplorator platformy Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Wykonanie szablonu utworzyło domyślne ustawienie skalowania automatycznego o nazwie **"autoscalewad"**. Po prawej stronie można wyświetlić pełną definicję tego ustawienia skalowania automatycznego. W takim przypadku domyślne ustawienie skalowania automatycznego jest zawiera regułę skalowania w poziomie i skalowania w poziomie opartą na procesorze CPU%.  

3. Teraz możesz dodać więcej profili i reguł na podstawie harmonogramu lub określonych wymagań. Tworzymy ustawienie skalowania automatycznego z trzema profilami. Aby zrozumieć profile i reguły w skalowaniu automatycznym, zapoznaj się [z najlepszymi praktykami skalowania automatycznego](autoscale-best-practices.md).  

    | Profile & Regulamin | Opis |
    |--- | --- |
    | **Profil** |**Oparte na wydajności/metryce** |
    | Reguła |Liczba komunikatów kolejki usługi Service Bus > x |
    | Reguła |Liczba komunikatów kolejki usługi Service Bus < y |
    | Reguła |CPU% > n |
    | Reguła |CPU% < p |
    | **Profil** |**Poranne godziny w dni powszednie (bez reguł)** |
    | **Profil** |**Dzień premiery produktu (bez reguł)** |

4. Oto hipotetyczny scenariusz skalowania, którego używamy do tego przejścia.

   * **Oparte na obciążeniu** — chcę skalować w poziomie lub w oparciu o obciążenie aplikacji hostowane w zestawie skalowania.*
   * **Rozmiar kolejki komunikatów** — używam kolejki magistrali usług dla wiadomości przychodzących do mojej aplikacji. Używam kolejki liczby wiadomości i CPU% i skonfigurować domyślny profil, aby wyzwolić akcję skalowania, jeśli jeden z liczby wiadomości lub cpu osiągnie próg.\*
   * **Poranek i dzień** - chcę cotygodniowy powtarzający się profil "poranek dnia" o nazwie "Godziny poranne w dni powszednie". Na podstawie danych historycznych wiem, że lepiej jest mieć określoną liczbę wystąpień maszyn wirtualnych do obsługi obciążenia mojej aplikacji w tym czasie.\*
   * **Daty specjalne** - dodałem profil "Dzień premiery produktu". Planuję z wyprzedzeniem konkretne daty, więc moja aplikacja jest gotowa do obsługi obciążenia ze względu ogłoszeń marketingowych i kiedy umieszczamy nowy produkt w aplikacji.\*
   * *Dwa ostatnie profile mogą również mieć inne reguły oparte na wydajności. W takim przypadku postanowiłem nie mieć jednego i zamiast polegać na domyślnych regułach opartych na wydajności. Reguły są opcjonalne dla profilów cyklicznych i opartych na dacie.*

     Priorytetyzacja profili i reguł aparatu w skali automatycznej jest również przechwytywany w [artykule najważniejsze wskazówki dotyczące skalowania automatycznego.](autoscale-best-practices.md)
     Aby uzyskać listę typowych metryk skalowania automatycznego, zapoznaj się [z typowymi metrykami skalowania automatycznego](autoscale-common-metrics.md)

5. Upewnij się, że jesteś w trybie **odczytu/zapisu** w Eksploratorze zasobów

    ![Skalowanie automatyczne, domyślne ustawienie skalowania automatycznego](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Kliknij pozycję Edytuj. **Zastąp** element "profile" w ustawieniu skalowania automatycznego następującą konfiguracją:

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
    Aby uzyskać obsługiwane pola i ich wartości, zobacz [Dokumentacja interfejsu API REST w automatycznym skalowaniu](https://msdn.microsoft.com/library/azure/dn931928.aspx). Teraz ustawienie skalowania automatycznego zawiera trzy profile wyjaśnione wcześniej.

7. Na koniec przyjrzyj się sekcji **powiadomień skalowania** automatycznego. Powiadomienia skalowania automatycznego umożliwiają zrobienie trzech czynności po pomyślnym wyzwoleniu skalowania w poziomie lub w akcji.
   - Powiadamianie administratora i współadministratorów subskrypcji
   - Wysyłanie wiadomości e-mail do zestawu użytkowników
   - Wyzwalanie połączenia elementu webhook. Po uruchomieniu ten element webhook wysyła metadane dotyczące warunku skalowania automatycznego i zasobu zestawu skalowania. Aby dowiedzieć się więcej o ładunku elementu webhook skalowania automatycznego, zobacz [Konfigurowanie elementu Webhook & powiadomienia e-mail dla skalowania automatycznego](autoscale-webhook-email.md).

   Dodaj następujące elementy do ustawienia Skalowanie automatyczne, zastępując element **powiadomienia,** którego wartość jest zerowa

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

   Naciśnij przycisk **Umieść** w Eksploratorze zasobów, aby zaktualizować ustawienie skalowania automatycznego.

Zaktualizowano ustawienie skalowania automatycznego w zestawie skalowania maszyny Wirtualnej, aby zawierało wiele profilów skalowania i powiadomień o skali.

## <a name="next-steps"></a>Następne kroki
Skorzystaj z tych łączy, aby dowiedzieć się więcej o skalowaniu automatycznym.

[Funkcja Rozwiązywanie problemów z zestawami skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Typowe metryki skalowania automatycznego](autoscale-common-metrics.md)

[Najważniejsze wskazówki dotyczące skalowania automatycznego platformy Azure](autoscale-best-practices.md)

[Zarządzanie skalowaniem automatycznym za pomocą programu PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Zarządzanie skalowaniem automatycznym przy użyciu interfejsu wiersza polecenia](cli-samples.md#autoscale)

[Konfigurowanie & powiadomień e-mail dla aplikacji Webhook dla skalowania automatycznego](autoscale-webhook-email.md)

Odwołanie do [szablonu Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)

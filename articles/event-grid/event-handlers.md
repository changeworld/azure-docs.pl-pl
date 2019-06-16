---
title: Programy obsługi zdarzeń Azure Event Grid
description: W tym artykule opisano procedury obsługi zdarzeń obsługiwanych dla usługi Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 6093e1017af2fb8c54eaf1c3192f937172567982
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080553"
---
# <a name="event-handlers-in-azure-event-grid"></a>Programy obsługi zdarzeń w usłudze Azure Event Grid

Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Program obsługi ma kilka dalszych działań do przetworzenia zdarzenia. Kilka usług platformy Azure są automatycznie konfigurowane do obsługi zdarzeń. Można również użyć dowolnego elementu WebHook do obsługi zdarzeń. Element WebHook nie musi być hostowana na platformie Azure do obsługi zdarzeń. Usługa Event Grid obsługuje tylko punkty końcowe HTTPS elementu WebHook.

Ten artykuł zawiera linki do zawartości dla każdego programu obsługi zdarzeń.

## <a name="azure-automation"></a>Azure Automation

Usługa Azure Automation umożliwia przetwarzanie zdarzeń za pomocą automatycznych elementów runbook.

|Stanowisko  |Opis  |
|---------|---------|
|[Samouczek: Usługa Azure Automation za pomocą usługi Event Grid i Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła zdarzenie. Zdarzenie wyzwala element runbook usługi Automation, tagi maszyny wirtualnej, która wyzwala komunikat, który jest wysyłany do kanału Microsoft Teams. |

## <a name="azure-functions"></a>Azure Functions

Użyj usługi Azure Functions bez użycia serwera odpowiedzi na zdarzenia.

W przypadku używania usługi Azure Functions jako procedury obsługi należy korzystać z wyzwalaczy usługi Event Grid, a nie ogólnych wyzwalaczy HTTP. Usługa Event Grid automatycznie weryfikuje wyzwalacze funkcji usługi Event Grid. W przypadku ogólnych wyzwalaczy HTTP trzeba zaimplementować [odpowiedź weryfikacji](security-authentication.md#webhook-event-delivery).

|Stanowisko  |Opis  |
|---------|---------|
| [Wyzwalacz siatki zdarzeń dla usługi Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Omówienie funkcji przy użyciu wyzwalacza usługi Event Grid. |
| [Samouczek: Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid](resize-images-on-storage-blob-upload-event.md) | Użytkownicy Przekaż obrazy za pomocą aplikacji sieci web do konta magazynu. Po utworzeniu magazynu obiektów blob usługi Event Grid wysyła zdarzenie do aplikacji funkcji, która zmienia rozmiar przekazanego obrazu. |
| [Samouczek: przesyłanie strumieniowe danych big data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy usługa Event Hubs tworzy plik przechwytywania, usługa Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |
| [Samouczek: Usługa Azure Service Bus przykłady integracji usługi Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Usługa Event Grid wysyła komunikaty z tematu usługi Service Bus, działanie aplikacji i aplikacji logiki. |

## <a name="event-hubs"></a>Event Hubs

Usługa Event Hubs, gdy rozwiązanie odbiera zdarzenia szybciej, niż może przetworzyć zdarzenia. Aplikacja przetwarza zdarzeń z centrów zdarzeń w jej własny harmonogram. Możesz skalować usługi do obsługi zdarzeń przychodzących do przetwarzania zdarzeń.

Usługa Event Hubs może działać jako źródło zdarzenia lub program obsługi zdarzeń. Następujący artykuł pokazuje, jak usługa Event Hubs jako program obsługi.

|Stanowisko  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń niestandardowych w usłudze Azure Event Hubs przy użyciu wiersza polecenia platformy Azure i usługi Event Grid](custom-event-to-eventhub.md) | Wysyła zdarzenie niestandardowe do Centrum zdarzeń dla przetwarzania przez aplikację. |
| [Szablon usługi Resource Manager: tematu niestandardowego i punktu końcowego usługi Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon usługi Resource Manager, która tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do usługi Azure Event Hubs. |

Aby zapoznać się z przykładami usługi Event hubs jako źródła, zobacz [źródła usługi Event Hubs](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Za pomocą połączeń hybrydowych usługi Azure Relay wysyłać zdarzenia aplikacji, które znajdują się w sieci przedsiębiorstwa i nie masz publicznie dostępnym punkcie końcowym.

|Stanowisko  |Opis  |
|---------|---------|
| [Samouczek: wysyłanie zdarzeń do połączenia hybrydowego](custom-event-to-hybrid-connection.md) | Wysyła zdarzenie niestandardowe istniejącego połączenia hybrydowego dla przetwarzania przez aplikacji odbiornika. |

## <a name="logic-apps"></a>Logic Apps

Użyj usługi Logic Apps można automatyzować procesy biznesowe reagowania na zdarzenia.

|Stanowisko  |Opis  |
|---------|---------|
| [Samouczek: monitorowanie zmian maszyn wirtualnych za pomocą usługi Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki monitoruje zmiany w maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. |
| [Samouczek: wysyłanie powiadomień e-mail dotyczących zdarzeń usługi Azure IoT Hub przy użyciu aplikacji logiki](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie zostanie dodane do Twojego Centrum IoT. |
| [Samouczek: Usługa Azure Service Bus przykłady integracji usługi Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Usługa Event Grid wysyła komunikaty z tematu usługi Service Bus, działanie aplikacji i aplikacji logiki. |

## <a name="service-bus-queue-preview"></a>Kolejki usługi Service Bus (wersja zapoznawcza)

Używa magistrali usługi jako program obsługi zdarzeń do routingu zdarzeń w usłudze Event Grid bezpośrednio do kolejki usługi Service Bus do użycia w scenariuszach buforowania lub Sterowanie i kontrola w aplikacjach dla przedsiębiorstw. Korzystania z wersji zapoznawczej nie działa z obsługą tematów usługi Service Bus i sesji, ale działa z wszystkich warstwach usługi Service Bus.

Pamiętaj, że podczas usługi Service Bus jako program obsługi jest w publicznej wersji zapoznawczej, należy zainstalować rozszerzenie interfejsu wiersza polecenia lub programu PowerShell podczas używania tych do utworzenia subskrypcji zdarzeń.

### <a name="install-extension-for-azure-cli"></a>Zainstaluj rozszerzenie wiersza polecenia platformy Azure

W przypadku wiersza polecenia platformy Azure, należy [rozszerzenia usługi Event Grid](/cli/azure/azure-cli-extensions-list).

W [CloudShell](/azure/cloud-shell/quickstart):

* Jeśli wcześniej zainstalowano rozszerzenie, zaktualizuj go przy użyciu `az extension update -n eventgrid`.
* Jeśli wcześniej nie zainstalowano rozszerzenie, zainstaluj go przy użyciu `az extension add -n eventgrid`.

W przypadku instalacji lokalnej:

1. [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Upewnij się, że masz najnowszą wersję, sprawdzając z `az --version`.
1. Odinstaluj rozszerzenie z poprzednich wersji `az extension remove -n eventgrid`.
1. Zainstaluj `eventgrid` rozszerzenie `az extension add -n eventgrid`.

### <a name="install-module-for-powershell"></a>Instalowanie modułu programu PowerShell

W przypadku programu PowerShell należy [modułu AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

W [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Zainstaluj moduł przy użyciu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

W przypadku instalacji lokalnej:

1. Otwórz konsolę programu PowerShell jako administrator.
1. Zainstaluj moduł przy użyciu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Jeśli `-AllowPrerelease` parametr nie jest dostępna, wykonaj następujące czynności:

1. Uruchom polecenie `Install-Module PowerShellGet -Force`.
1. Uruchom polecenie `Update-Module PowerShellGet`.
1. Zamknij konsolę programu PowerShell.
1. Ponownie uruchom program PowerShell jako administrator.
1. Zainstaluj moduł `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

### <a name="using-cli-to-add-a-service-bus-handler"></a>Dodawanie obsługi usługi Service Bus przy użyciu interfejsu wiersza polecenia

Wiersza polecenia platformy Azure w poniższym przykładzie subskrybuje i temat usługi Event Grid nawiązanie połączenia z kolejki usługi Service Bus:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Queue Storage

Usługa Queue storage umożliwia odbieranie zdarzeń, które muszą zostać pobrane. Usługa Queue storage można użyć w przypadku długotrwałych procesu, który trwa zbyt długo na odpowiedź. Przez wysyłanie zdarzeń do usługi Queue storage, można ściągnąć aplikację i przetwarzać zdarzenia w jego własnej harmonogramu.

|Stanowisko  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure Queue storage przy użyciu wiersza polecenia platformy Azure i usługi Event Grid](custom-event-to-queue-storage.md) | W tym artykule opisano, jak wysyłać zdarzenia niestandardowe do usługi Queue storage. |

## <a name="webhooks"></a>Elementy webhook

Używanie elementów webhook dla punktów końcowych można dostosowywać, reagujące na zdarzenia.

|Stanowisko  |Opis  |
|---------|---------|
| Szybki Start: tworzenie i kierowanie zdarzeń niestandardowych za pomocą - [wiersza polecenia platformy Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), i [portal](custom-event-quickstart-portal.md). | Pokazuje, jak wysyłać zdarzenia niestandardowe do elementu WebHook. |
| Szybki Start: kierowanie zdarzeń usługi Blob storage do niestandardowego internetowego punktu końcowego z - [wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), i [portal](blob-event-quickstart-portal.md). | Pokazuje, jak wysyłać zdarzenia usługi blob storage do elementu WebHook. |
| [Szybki Start: wysyłanie zdarzeń do rejestru kontenerów](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia rejestru kontenera za pomocą wiersza polecenia platformy Azure. |
| [Omówienie: odbieranie zdarzeń w punkcie końcowym HTTP](receive-events.md) | W tym artykule opisano sposób sprawdzania poprawności punktu końcowego HTTP na odbieranie zdarzeń z subskrypcji zdarzeń oraz odbierania i wykonać deserializacji zdarzenia. |

## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).

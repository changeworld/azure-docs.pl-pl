---
title: Źródła zdarzeń usługi Azure Event Grid
description: W tym artykule opisano źródła obsługiwanych zdarzeń usługi Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: f6c00f4ada0590df91fce0c36281d0c0cac67b77
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257950"
---
# <a name="event-sources-in-azure-event-grid"></a>Źródła zdarzeń w usłudze Azure Event Grid

Źródłem zdarzenia jest, gdy zdarzenie. Kilka usług platformy Azure są automatycznie konfigurowane tak, aby wysyłać zdarzenia. Można również utworzyć aplikacje niestandardowe, które wysyła zdarzenia. Niestandardowe aplikacje nie muszą być hostowane na platformie Azure do użycia dystrybucję zdarzeń usługi Event Grid.

Ten artykuł zawiera linki do zawartości dla każdego źródła zdarzeń.

## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

Subskrybowanie do zdarzeń subskrypcje platformy Azure w celu reagowanie na zmiany w zasoby w subskrypcji platformy Azure.

|Stanowisko |Opis  |
|---------|---------|
| [Samouczek: Usługa Azure Automation za pomocą usługi Event Grid i Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła zdarzenie. Zdarzenie wyzwala element runbook usługi Automation, tagi maszyny wirtualnej, która wyzwala komunikat, który jest wysyłany do kanału Microsoft Teams. |
| [Porady: subskrybowanie zdarzeń za pośrednictwem portalu](subscribe-through-portal.md) | Subskrybowanie do zdarzeń dla subskrypcji platformy Azure za pomocą portalu. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla subskrypcji platformy Azure](./scripts/event-grid-cli-azure-subscription.md) |Przykładowy skrypt tworzy subskrypcję usługi Event Grid z subskrypcją platformy Azure, która wysyła zdarzenia do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń dla subskrypcji platformy Azure](./scripts/event-grid-powershell-azure-subscription.md)| Przykładowy skrypt tworzy subskrypcję usługi Event Grid z subskrypcją platformy Azure, która wysyła zdarzenia do elementu WebHook. |
| [Schemat zdarzeń](event-schema-subscriptions.md) | Pokazuje pola w zdarzeniach subskrypcji platformy Azure. |

## <a name="container-registry"></a>Container Registry

Subskrybowanie do zdarzeń Container Registry do reagowania na zmiany w obrazach.

|Stanowisko |Opis  |
|---------|---------|
| [Szybki Start: wysyłanie zdarzeń do rejestru kontenerów](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia rejestru kontenera za pomocą wiersza polecenia platformy Azure. |
| [Schemat zdarzeń](event-schema-container-registry.md) | Pokazuje pola w zdarzeniach rejestru kontenerów. |

## <a name="custom-topics"></a>Tematy niestandardowe

Subskrybować tematy niestandardowe w celu reagowania na zdarzenia aplikacji.

|Stanowisko  |Opis  |
|---------|---------|
| [Szybki Start: tworzenie i kierowanie zdarzeń niestandardowych za pomocą wiersza polecenia platformy Azure](custom-event-quickstart.md) | Pokazuje, jak wysyłać zdarzenia niestandardowe za pomocą wiersza polecenia platformy Azure. |
| [Szybki Start: tworzenie i kierowanie zdarzeń niestandardowych za pomocą programu Azure PowerShell](custom-event-quickstart-powershell.md) | Pokazuje, jak wysyłać zdarzenia niestandardowe za pomocą programu Azure PowerShell. |
| [Szybki Start: tworzenie i kierowanie zdarzeń niestandardowych za pomocą witryny Azure portal](custom-event-quickstart-portal.md) | Pokazuje, jak korzystać z portalu, aby wysyłać zdarzenia niestandardowe. |
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure Queue storage](custom-event-to-queue-storage.md) | W tym artykule opisano, jak wysyłać zdarzenia niestandardowe do usługi Queue storage. |
| [Porady: publikowanie w temacie niestandardowym](post-to-custom-topic.md) | Pokazuje, jak można opublikować zdarzenia do tematu niestandardowego. |
| [Interfejs wiersza polecenia platformy Azure: tworzenie tematu niestandardowego usługi Event Grid](./scripts/event-grid-cli-create-custom-topic.md)|Przykładowy skrypt tworzy temat niestandardowy. Skrypt pobiera punktu końcowego i klucz.|
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-cli-subscribe-custom-topic.md)|Przykładowy skrypt tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do elementu WebHook.|
| [Program PowerShell: tworzenie tematu niestandardowego usługi Event Grid](./scripts/event-grid-powershell-create-custom-topic.md)|Przykładowy skrypt tworzy temat niestandardowy. Skrypt pobiera punktu końcowego i klucz.|
| [Program PowerShell: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Przykładowy skrypt tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do elementu WebHook.|
| [Szablon usługi Resource Manager: tematu niestandardowego i punktem końcowym elementu WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Szablon usługi Resource Manager, który tworzy niestandardowy temat i subskrypcję tego niestandardowego tematu. Wysyła zdarzenia do elementu WebHook. |
|
| [Szablon usługi Resource Manager: tematu niestandardowego i punktu końcowego usługi Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon usługi Resource Manager, która tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do usługi Azure Event Hubs. |
| [Schemat zdarzeń](event-schema.md) | Pokazuje pola w zdarzeniach niestandardowych. |

## <a name="event-hubs"></a>Event Hubs

Subskrybowanie zdarzeń usługi Event Hubs odpowiedzieć, aby przechwytywać zdarzenia w pliku. Usługa Event Hubs może działać jako źródło zdarzenia lub program obsługi zdarzeń. Następujące artykuły pokazują, jak używać usługi Event Hubs jako źródła.

|Stanowisko  |Opis  |
|---------|---------|
| [Samouczek: przesyłanie strumieniowe danych big data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy usługa Event Hubs tworzy plik przechwytywania, usługa Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |
| [Schemat zdarzeń](event-schema-event-hubs.md) | Pokazuje pola w zdarzeniach usługi Event Hubs. |

Aby zapoznać się z przykładami usługi Event hubs jako program obsługi, zobacz [obsługi usługi Event Hubs](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Usługa IoT Hub

Subskrybowanie zdarzeń usługi IoT Hub na utworzono usunięty, połączone, odłączonej urządzenie i zdarzenia telemetrii.

|Stanowisko  |Opis  |
|---------|---------|
| [Wysyłanie powiadomień e-mail dotyczących zdarzeń usługi Azure IoT Hub przy użyciu aplikacji logiki](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie zostanie dodane do Twojego Centrum IoT. |
| [Reagowanie na zdarzenia IoT Hub przy użyciu usługi Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md) | Omówienie integracji usługi IoT Hub z usługą Event Grid. |
| [Schemat zdarzeń](event-schema-iot-hub.md) | Pola wyświetlane w usłudze IoT Hub zdarzenia. |
| [Kolejność połączonych urządzeń i Urządzenie odłączone zdarzenia](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Pokazuje, jak kolejność zdarzeń stanu połączenia urządzenia. |

## <a name="media-services"></a>Media Services

Subskrybowanie zdarzeń usługi Media Services w celu reagowania na zdarzenia stanu zadania.

|Stanowisko  |Opis  |
|---------|---------|
| [Omówienie: reagowanie na zdarzenia usługi Media Services](../media-services/latest/reacting-to-media-services-events.md) | Omówienie integracji usług Media Services za pomocą usługi Event Grid. |
| [Samouczek: kierowanie zdarzeń usługi Azure Media Services do niestandardowego internetowego punktu końcowego przy użyciu interfejsu wiersza polecenia](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia z usługi Media Services. |
| [Schemat zdarzeń](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje pola w zdarzeniach usługi Media Services. |

## <a name="resource-groups"></a>Grupy zasobów

Subskrybowanie zdarzenia grupy zasobów, aby odpowiadanie na zmiany w zasoby w grupie zasobów.

|Stanowisko  |Opis  |
|---------|---------|
| [Samouczek: monitorowanie zmian maszyn wirtualnych za pomocą usługi Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki monitoruje zmiany w maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla grupy zasobów](./scripts/event-grid-cli-resource-group.md)| Przykładowy skrypt, który subskrybuje do zdarzeń dla grupy zasobów. Wysyła zdarzenia do elementu WebHook. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla grupy zasobów i filtr dla zasobu](./scripts/event-grid-cli-resource-group-filter.md) | Przykładowy skrypt, który subskrybuje do zdarzeń dla grupy zasobów i filtruje zdarzenia dla jednego zasobu. |
| [Program PowerShell: subskrybowanie zdarzeń dla grupy zasobów](./scripts/event-grid-powershell-resource-group.md) | Przykładowy skrypt, który subskrybuje do zdarzeń dla grupy zasobów. Wysyła zdarzenia do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń dla grupy zasobów i filtr dla zasobu](./scripts/event-grid-powershell-resource-group-filter.md) | Przykładowy skrypt, który subskrybuje do zdarzeń dla grupy zasobów i filtruje zdarzenia dla jednego zasobu. |
| [Szablon usługi Resource Manager: subskrypcji zasobu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Subskrybuje do zdarzeń dla platformy Azure subskrypcji lub grupy zasobów. Wysyła zdarzenia do elementu WebHook. |
| [Schemat zdarzeń](event-schema-resource-groups.md) | Pokazuje pola w zasobie pogrupować zdarzenia. |

## <a name="service-bus"></a>Service Bus

Subskrybowanie zdarzeń usługi Service Bus, aby odpowiadać na komunikaty bez aktywny odbiornik.

|Stanowisko  |Opis  |
|---------|---------|
| [Samouczek: Usługa Azure Service Bus przykłady integracji usługi Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Usługa Event Grid wysyła komunikaty z tematu usługi Service Bus, działanie aplikacji i aplikacji logiki. |
| [Omówienie: Usługa Azure Service Bus do integracji usługi Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Omówienie integracji usługi Service Bus z usługą Event Grid. |
| [Schemat zdarzeń](event-schema-service-bus.md) | Pokazuje pola w zdarzeniach usługi Service Bus. |

## <a name="storage"></a>Magazyn

Subskrybowanie zdarzeń usługi Blob Storage do odpowiadanie na zdarzenia utworzone i usunięto usługi blob.

|Stanowisko  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń usługi Blob storage do niestandardowego internetowego punktu końcowego przy użyciu wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia usługi blob storage do elementu WebHook przy użyciu wiersza polecenia platformy Azure. |
| [Szybki Start: kierowanie zdarzeń usługi Blob storage do niestandardowego internetowego punktu końcowego przy użyciu programu PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia usługi blob storage do elementu WebHook przy użyciu programu Azure PowerShell. |
| [Szybki Start: tworzenie i kierowanie zdarzeń usługi Blob storage za pomocą witryny Azure portal](blob-event-quickstart-portal.md) | Pokazuje, jak korzystać z portalu do wysyłania zdarzeń usługi blob storage do elementu WebHook. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla konta usługi Blob storage](./scripts/event-grid-cli-blob.md) | Przykładowy skrypt, która ją subskrybuje zdarzenia dla konta usługi Blob storage. Wysyła zdarzenie do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń dla konta usługi Blob storage](./scripts/event-grid-powershell-blob.md) | Przykładowy skrypt, która ją subskrybuje zdarzenia dla konta usługi Blob storage. Wysyła zdarzenie do elementu WebHook. |
| [Szablon usługi Resource Manager: Tworzenie magazynu obiektów Blob i subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. Wysyła zdarzenia do elementu WebHook. |
| [Omówienie: reagowanie na zdarzenia usługi Blob storage](../storage/blobs/storage-blob-event-overview.md) | Omówienie integracji usługi Blob storage z usługą Event Grid. |
| [Schemat zdarzeń](event-schema-blob-storage.md) | Pokazuje pola w zdarzeniach usługi Blob Storage. |

## <a name="maps"></a>Maps
Subskrybowanie zdarzeń usługi Azure Maps w celu reagowania na zdarzenia w wirtualnym ogrodzeniu. Na przykład aplikacji wyśle wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenia wprowadza lub kończy działanie w wirtualnym ogrodzeniu.

|Stanowisko  |Opis  |
|---------|---------|
| [Reagowanie na zdarzenia usługi Azure Maps za pomocą usługi Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Przegląd integrowania usługi Azure Maps z usługi Event Grid. |
| [Samouczek: Konfigurowanie wirtualnego ogrodzenia](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania geofencingu przy użyciu usługi Azure Maps. Azure Event Grid umożliwia przesyłanie strumieniowe wyników wirtualnego ogrodzenia i skonfigurować powiadomienia na podstawie wyników w wirtualnym ogrodzeniu. |
| [Schemat zdarzeń](event-schema-azure-maps.md) | Pokazuje pola w zdarzeniach usługi Azure Maps. |


## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).

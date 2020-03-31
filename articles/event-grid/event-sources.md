---
title: Źródła zdarzeń usługi Azure Event Grid
description: Subskrypcje platformy Azure, rejestr kontenerów, tematy niestandardowe, centra zdarzeń, centrum IoT Hub, magazyn kluczy, usługi multimedialne, grupy zasobów, usługa Service Bus, magazyn, mapy, konfiguracja aplikacji, signal R, uczenie maszynowe.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265001"
---
# <a name="event-sources-in-azure-event-grid"></a>Źródła zdarzeń w siatce zdarzeń platformy Azure

Źródłem zdarzenia jest miejsce, w którym zdarzenie się dzieje. Kilka usług platformy Azure są automatycznie skonfigurowane do wysyłania zdarzeń. Można również tworzyć aplikacje niestandardowe, które wysyłają zdarzenia. Aplikacje niestandardowe nie muszą być hostowane na platformie Azure, aby używać usługi Event Grid do dystrybucji zdarzeń.

Ten artykuł zawiera łącza do zawartości dla każdego źródła zdarzeń.

## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

Subskrybuj zdarzenia subskrypcji platformy Azure, aby reagować na zmiany w zasobach w ramach subskrypcji platformy Azure.

|Tytuł |Opis  |
|---------|---------|
| [Samouczek: Automatyzacja platformy Azure z siatką zdarzeń i zespołami Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła zdarzenie. Zdarzenie wyzwala element runbook automatyzacji, który oznacza maszynę wirtualną i wyzwala komunikat, który jest wysyłany do kanału usługi Microsoft Teams. |
| [Jak: subskrybować wydarzenia za pośrednictwem portalu](subscribe-through-portal.md) | Użyj portalu, aby subskrybować zdarzenia dla subskrypcji platformy Azure. |
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń w ramach subskrypcji platformy Azure](./scripts/event-grid-cli-azure-subscription.md) |Przykładowy skrypt, który tworzy subskrypcję usługi Event Grid do subskrypcji platformy Azure i wysyła zdarzenia do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń w ramach subskrypcji platformy Azure](./scripts/event-grid-powershell-azure-subscription.md)| Przykładowy skrypt, który tworzy subskrypcję usługi Event Grid do subskrypcji platformy Azure i wysyła zdarzenia do elementu WebHook. |
| [Schemat zdarzeń](event-schema-subscriptions.md) | Pokazuje pola w zdarzeniach subskrypcji platformy Azure. |

## <a name="container-registry"></a>Container Registry

Subskrybuj zdarzenia rejestru kontenerów, aby reagować na zmiany w obrazach.

|Tytuł |Opis  |
|---------|---------|
| [Szybki start: wysyłanie zdarzeń rejestru kontenerów](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń rejestru kontenerów. |
| [Schemat zdarzeń](event-schema-container-registry.md) | Pokazuje pola w zdarzeniach rejestru kontenerów. |

## <a name="custom-topics"></a>Tematy niestandardowe

Subskrybuj tematy niestandardowe, aby odpowiadać na zdarzenia aplikacji.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą interfejsu wiersza polecenia platformy Azure](custom-event-quickstart.md) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń niestandardowych. |
| [Szybki start: tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą programu Azure PowerShell](custom-event-quickstart-powershell.md) | Pokazuje, jak używać programu Azure PowerShell do wysyłania zdarzeń niestandardowych. |
| [Szybki start: tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą witryny Azure Portal](custom-event-quickstart-portal.md) | Pokazuje, jak używać portalu do wysyłania zdarzeń niestandardowych. |
| [Szybki start: kierowanie zdarzeń niestandardowych do magazynu kolejki platformy Azure](custom-event-to-queue-storage.md) | W tym artykule opisano sposób wysyłania zdarzeń niestandardowych do magazynu kolejki. |
| [Jak: publikować w temacie niestandardowym](post-to-custom-topic.md) | Pokazuje, jak opublikować zdarzenie w temacie niestandardowym. |
| [Narzędzie CLI platformy Azure: tworzenie tematu niestandardowego siatki zdarzeń](./scripts/event-grid-cli-create-custom-topic.md)|Przykładowy skrypt, który tworzy temat niestandardowy. Skrypt pobiera punkt końcowy i klucz.|
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń w temacie niestandardowym](./scripts/event-grid-cli-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję dla tematu niestandardowego. Wysyła zdarzenia do elementu WebHook.|
| [PowerShell: tworzenie tematu niestandardowego usługi Event Grid](./scripts/event-grid-powershell-create-custom-topic.md)|Przykładowy skrypt, który tworzy temat niestandardowy. Skrypt pobiera punkt końcowy i klucz.|
| [Program PowerShell: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję dla tematu niestandardowego. Wysyła zdarzenia do elementu WebHook.|
| [Szablon Menedżera zasobów: temat niestandardowy i punkt końcowy elementu WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Szablon Menedżera zasobów, który tworzy temat niestandardowy i subskrypcję dla tego tematu niestandardowego. Wysyła zdarzenia do elementu WebHook. |
|
| [Szablon Menedżera zasobów: temat niestandardowy i punkt końcowy centrów zdarzeń](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon Menedżera zasobów, który tworzy subskrypcję dla tematu niestandardowego. Wysyła zdarzenia do usługi Azure Event Hubs. |
| [Schemat zdarzeń](event-schema.md) | Pokazuje pola w zdarzeniach niestandardowych. |

## <a name="event-hubs"></a>Usługa Event Hubs

Subskrybuj zdarzenia usługi Event Hubs, aby odpowiedzieć na zdarzenia pliku przechwytywania. Centra zdarzeń może działać jako źródło zdarzeń lub program obsługi zdarzeń. W poniższych artykułach pokazano, jak używać centrów zdarzeń jako źródła.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: przesyłanie strumieniowe dużych zbiorów danych do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Centra zdarzeń tworzy plik przechwytywania, usługa Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik Przechwytywania i migruje dane do magazynu danych. |
| [Schemat zdarzeń](event-schema-event-hubs.md) | Pokazuje pola w zdarzeniach Centrum zdarzeń. |

Aby zapoznać się z przykładami centrów zdarzeń jako programu obsługi, zobacz [Obsługa centrów zdarzeń](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Usługa IoT Hub

Subskrybuj zdarzenia usługi IoT Hub, aby odpowiadać na zdarzenia utworzone, usunięte, połączone, rozłączone i telemetryczne.

|Tytuł  |Opis  |
|---------|---------|
| [Wysyłanie powiadomień e-mail o zdarzeniach usługi Azure IoT Hub przy użyciu usługi Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie jest dodawane do centrum IoT Hub. |
| [Reagowanie na zdarzenia usługi IoT Hub za pomocą siatki zdarzeń do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md) | Omówienie integracji Usługi IoT Hub z siatką zdarzeń. |
| [Schemat zdarzeń](event-schema-iot-hub.md) | Pokazuje pola w zdarzeniach Usługi IoT Hub. |
| [Zamów podłączone urządzenie i zdarzenia odłączone od urządzenia](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Pokazuje, jak zamówić zdarzenia stanu połączenia urządzenia. |

## <a name="key-vault-preview"></a>Przechowalnia kluczy (wersja zapoznawcza)

Integracja magazynu kluczy z siatką zdarzeń jest obecnie w wersji zapoznawczej. 

Subskrybuj zdarzenia usługi Key Vault, aby otrzymywać powiadomienia o wygaśnięciu klucza tajnego, wygaśnięciu klucza tajnego lub udostępnieniu klucza tajnego w nowej wersji. 

|Tytuł  |Opis  |
|---------|---------|
| [Monitorowanie zdarzeń magazynu kluczy za pomocą usługi Azure Event Grid](../key-vault/event-grid-overview.md) | Omówienie integracji magazynu kluczy z siatką zdarzeń. |
| [Samouczek: Tworzenie i monitorowanie zdarzeń magazynu kluczy za pomocą siatki zdarzeń](../key-vault/event-grid-tutorial.md) | Dowiedz się, jak skonfigurować powiadomienia o siatce zdarzeń dla usługi Key Vault. |
| [Schemat zdarzeń](event-schema-key-vault.md) | Pokazuje pola w zdarzeniach Przechowalni kluczy. |

## <a name="media-services"></a>Media Services

Subskrybuj zdarzenia usługi Media Services, aby odpowiadać na zdarzenia stanu zadania.

|Tytuł  |Opis  |
|---------|---------|
| [Omówienie: reagowanie na zdarzenia usługi Media Services](../media-services/latest/reacting-to-media-services-events.md) | Omówienie integrowania usługi Media Services z siatką zdarzeń. |
| [Samouczek: kierowanie zdarzeń usługi Azure Media Services do niestandardowego punktu końcowego sieci Web przy użyciu interfejsu wiersza polecenia](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia z usługi Media Services. |
| [Schemat zdarzeń](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje pola w zdarzeniach usługi Media Services. |

## <a name="resource-groups"></a>Grupy zasobów

Subskrybuj zdarzenia grupy zasobów, aby reagować na zmiany w zasobach w grupie zasobów.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: monitorowanie zmian na maszynach wirtualnych za pomocą usługi Azure Event Grid i aplikacji logiki](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki monitoruje zmiany na maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. |
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla grupy zasobów](./scripts/event-grid-cli-resource-group.md)| Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów. Wysyła zdarzenia do elementu WebHook. |
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla grupy zasobów i filtrowanie zasobu](./scripts/event-grid-cli-resource-group-filter.md) | Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów i filtruje zdarzenia dla jednego zasobu. |
| [Program PowerShell: subskrybowanie zdarzeń dla grupy zasobów](./scripts/event-grid-powershell-resource-group.md) | Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów. Wysyła zdarzenia do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń dla grupy zasobów i filtrowanie zasobu](./scripts/event-grid-powershell-resource-group-filter.md) | Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów i filtruje zdarzenia dla jednego zasobu. |
| [Szablon Menedżera zasobów: subskrypcja zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Subskrybuje zdarzenia dla subskrypcji platformy Azure lub grupy zasobów. Wysyła zdarzenia do elementu WebHook. |
| [Schemat zdarzeń](event-schema-resource-groups.md) | Pokazuje pola w zdarzeniach grupy zasobów. |

## <a name="service-bus"></a>Service Bus

Subskrybuj zdarzenia usługi Service Bus, aby odpowiadać na wiadomości bez aktywnego odbiornika.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: przykłady integracji usługi Azure Service Bus to Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Usługa Event Grid wysyła wiadomości z tematu usługi Service Bus do aplikacji funkcji i aplikacji logiki. |
| [Omówienie: integracja usługi Azure Service Bus to Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Omówienie integrowania usługi Service Bus z siatką zdarzeń. |
| [Schemat zdarzeń](event-schema-service-bus.md) | Pokazuje pola w zdarzeniach usługi Service Bus. |

## <a name="storage"></a>Magazyn

Subskrybuj zdarzenia magazynu obiektów Blob, aby odpowiedzieć na zdarzenia utworzone i usunięte w obiekcie blob.

>[!NOTE]
> Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia w wersji 2)** i integracji zdarzeń obsługi **BlobStorage.** **Magazyn (ogólnego przeznaczenia w wersji 1)** *nie* obsługuje integracji z siatką zdarzeń.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: kierowanie zdarzeń magazynu obiektów Blob do niestandardowego punktu końcowego sieci Web za pomocą interfejsu wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń magazynu obiektów blob do elementu WebHook. |
| [Szybki start: kierowanie zdarzeń magazynu obiektów Blob do niestandardowego punktu końcowego sieci Web za pomocą programu PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać programu Azure PowerShell do wysyłania zdarzeń magazynu obiektów blob do elementu WebHook. |
| [Szybki start: tworzenie i rozsyłanie zdarzeń magazynu obiektów Blob za pomocą witryny Azure portal](blob-event-quickstart-portal.md) | Pokazuje, jak używać portalu do wysyłania zdarzeń magazynu obiektów blob do elementu WebHook. |
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla konta magazynu obiektów Blob](./scripts/event-grid-cli-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta magazynu obiektów Blob. Wysyła zdarzenie do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń dla konta magazynu obiektów Blob](./scripts/event-grid-powershell-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta magazynu obiektów Blob. Wysyła zdarzenie do elementu WebHook. |
| [Szablon Menedżera zasobów: tworzenie magazynu i subskrypcji obiektów Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. Wysyła zdarzenia do elementu WebHook. |
| [Omówienie: reagowanie na zdarzenia magazynu obiektów Blob](../storage/blobs/storage-blob-event-overview.md) | Omówienie integrowania magazynu obiektów Blob z siatką zdarzeń. |
| [Schemat zdarzeń](event-schema-blob-storage.md) | Pokazuje pola w zdarzeniach magazynu obiektów blob. |

## <a name="maps"></a>Maps
Subskrybuj zdarzenia usługi Azure Maps, aby reagować na zdarzenia geofence. Na przykład aplikacja może dostarczać powiadomienie e-mail za każdym razem, gdy urządzenie wchodzi lub kończy działanie geograficzne.

|Tytuł  |Opis  |
|---------|---------|
| [Reagowanie na zdarzenia usługi Azure Maps przy użyciu siatki zdarzeń](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Omówienie integracji usługi Azure Maps z siatką zdarzeń. |
| [Samouczek: Konfigurowanie geofence](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania geofencingu przy użyciu usługi Azure Maps. Usługa Azure Event Grid służy do przesyłania strumieniowego wyników geofence i konfigurowania powiadomienia na podstawie wyników geofence. |
| [Schemat zdarzeń](event-schema-azure-maps.md) | Pokazuje pola w zdarzeniach usługi Azure Maps. |

## <a name="app-configuration"></a>Konfiguracja aplikacji
Subskrybuj zdarzenia konfiguracji aplikacji platformy Azure, aby odpowiadać na zdarzenia modyfikacji wartości klucza.

|Tytuł | Opis |
|---------|---------|
| [Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure przy użyciu siatki zdarzeń](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Omówienie integrowania konfiguracji aplikacji platformy Azure z siatką zdarzeń. |
| [Szybki start: kierowanie zdarzeń konfiguracji aplikacji platformy Azure do niestandardowego punktu końcowego sieci Web za pomocą interfejsu wiersza polecenia platformy Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń konfiguracji aplikacji platformy Azure do elementu WebHook. |
| [Schemat zdarzeń](event-schema-app-configuration.md) | Pokazuje pola w zdarzeniach konfiguracji aplikacji platformy Azure. |

## <a name="azure-signalr"></a>Azure SignalR
Subskrybuj zdarzenia usługi Azure SignalR, aby reagować na zdarzenia połączenia klienta.

|Tytuł | Opis |
|---------|---------|
| [Reagowanie na zdarzenia usługi Azure SignalR przy użyciu siatki zdarzeń](../azure-signalr/signalr-concept-event-grid-integration.md) | Omówienie integracji usługi Azure SignalR z usługą Event Grid. |
| [Jak wysyłać zdarzenia usługi Azure SignalR do siatki zdarzeń](../azure-signalr/signalr-howto-event-grid-integration.md) | Pokazuje, jak wysyłać zdarzenia usługi Azure SignalR do aplikacji za pośrednictwem usługi Event Grid. |
| [Schemat zdarzeń](event-schema-azure-signalr.md) | Pokazuje pola w zdarzeniach usługi Azure SignalR. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Subskrybuj zdarzenia obszaru roboczego usługi Azure Machine Learning, aby odpowiedzieć na rejestrację modelu.

| Tytuł | Opis |
| ----- | ----- |
| [Korzystanie ze zdarzeń usługi Azure Machine Learning](../machine-learning/concept-event-grid-integration.md) | Omówienie integracji usługi Azure Machine Learning z usługą Event Grid. |
| [Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Machine Learning](event-schema-machine-learning.md) | Pokazuje pola w zdarzeniach usługi Azure Machine Learning. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).

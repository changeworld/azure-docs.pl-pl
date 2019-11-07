---
title: Azure Event Grid źródła zdarzeń
description: Opisuje obsługiwane źródła zdarzeń dla Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 8c9258f099587ba4dbdfe3a7e46f72cd7a825024
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603836"
---
# <a name="event-sources-in-azure-event-grid"></a>Źródła zdarzeń w Azure Event Grid

Źródło zdarzenia to miejsce, w którym występuje zdarzenie. Kilka usług platformy Azure jest automatycznie konfigurowanych do wysyłania zdarzeń. Można również tworzyć niestandardowe aplikacje, które wysyłają zdarzenia. Aplikacje niestandardowe nie muszą być hostowane na platformie Azure, aby można było używać Event Grid do dystrybucji zdarzeń.

Ten artykuł zawiera linki do zawartości dla każdego źródła zdarzeń.

## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

Subskrybowanie zdarzeń subskrypcji platformy Azure w celu reagowania na zmiany zasobów w ramach subskrypcji platformy Azure.

|Tytuł |Opis  |
|---------|---------|
| [Samouczek: Azure Automation z Event Grid i Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła zdarzenie. Zdarzenie wyzwala element Runbook usługi Automation, który oznacza maszynę wirtualną, i wyzwala komunikat wysyłany do kanału Microsoft Teams. |
| [Instrukcje: subskrybowanie zdarzeń za poorednictwem portalu](subscribe-through-portal.md) | Użyj portalu, aby subskrybować zdarzenia dla subskrypcji platformy Azure. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie do zdarzeń dla subskrypcji platformy Azure](./scripts/event-grid-cli-azure-subscription.md) |Przykładowy skrypt, który tworzy subskrypcję Event Grid w ramach subskrypcji platformy Azure i wysyła zdarzenia do elementu webhook. |
| [PowerShell: subskrybowanie do zdarzeń dla subskrypcji platformy Azure](./scripts/event-grid-powershell-azure-subscription.md)| Przykładowy skrypt, który tworzy subskrypcję Event Grid w ramach subskrypcji platformy Azure i wysyła zdarzenia do elementu webhook. |
| [Schemat zdarzenia](event-schema-subscriptions.md) | Wyświetla pola w zdarzeniach subskrypcji platformy Azure. |

## <a name="container-registry"></a>Container Registry

Subskrybuj zdarzenia Container Registry, aby reagować na zmiany w obrazach.

|Tytuł |Opis  |
|---------|---------|
| [Szybki Start: wysyłanie zdarzeń rejestru kontenera](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, w jaki sposób używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń Container Registry. |
| [Schemat zdarzenia](event-schema-container-registry.md) | Wyświetla pola w zdarzeniach Container Registry. |

## <a name="custom-topics"></a>Tematy niestandardowe

Subskrybowanie tematów niestandardowych w celu reagowania na zdarzenia aplikacji.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: Tworzenie i kierowanie zdarzeń niestandardowych za pomocą interfejsu wiersza polecenia platformy Azure](custom-event-quickstart.md) | Pokazuje, jak wysyłać zdarzenia niestandardowe przy użyciu interfejsu wiersza polecenia platformy Azure. |
| [Szybki Start: Tworzenie i kierowanie zdarzeń niestandardowych za pomocą Azure PowerShell](custom-event-quickstart-powershell.md) | Pokazuje, w jaki sposób używać Azure PowerShell do wysyłania zdarzeń niestandardowych. |
| [Szybki Start: Tworzenie i kierowanie zdarzeń niestandardowych za pomocą Azure Portal](custom-event-quickstart-portal.md) | Pokazuje, w jaki sposób używać portalu do wysyłania zdarzeń niestandardowych. |
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure queue storage](custom-event-to-queue-storage.md) | Opisuje sposób wysyłania zdarzeń niestandardowych do magazynu kolejki. |
| [Instrukcje: publikowanie w temacie niestandardowym](post-to-custom-topic.md) | Pokazuje, jak ogłosić zdarzenie w temacie niestandardowym. |
| [Interfejs wiersza polecenia platformy Azure: Tworzenie Event Grid tematu niestandardowego](./scripts/event-grid-cli-create-custom-topic.md)|Przykładowy skrypt służący do tworzenia tematu niestandardowego. Skrypt Pobiera punkt końcowy i klucz.|
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-cli-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do elementu webhook.|
| [PowerShell: Tworzenie niestandardowego tematu Event Grid](./scripts/event-grid-powershell-create-custom-topic.md)|Przykładowy skrypt służący do tworzenia tematu niestandardowego. Skrypt Pobiera punkt końcowy i klucz.|
| [PowerShell: subskrybowanie zdarzeń dla tematu niestandardowego](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Przykładowy skrypt, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do elementu webhook.|
| [Szablon Menedżer zasobów: temat niestandardowy i punkt końcowy elementu webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Szablon Menedżer zasobów, który tworzy niestandardowy temat i subskrypcję dla tego tematu niestandardowego. Wysyła zdarzenia do elementu webhook. |
|
| [Szablon Menedżer zasobów: temat niestandardowy i Event Hubs punkt końcowy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon Menedżer zasobów, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do Event Hubs platformy Azure. |
| [Schemat zdarzenia](event-schema.md) | Pokazuje pola w zdarzeniach niestandardowych. |

## <a name="event-hubs"></a>Usługa Event Hubs

Zasubskrybuj zdarzenia Event Hubs, aby odpowiedzieć na zdarzenia plików przechwytywania. Event Hubs może działać jako źródło zdarzenia lub procedura obsługi zdarzeń. W poniższych artykułach pokazano, jak używać Event Hubs jako źródła.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy Event Hubs tworzy plik przechwytywania, Event Grid wysyła zdarzenie do aplikacji funkcji. Aplikacja pobiera plik przechwytywania i migruje dane do magazynu danych. |
| [Schemat zdarzenia](event-schema-event-hubs.md) | Wyświetla pola w zdarzeniach Event Hubs. |

Przykłady Event Hubs jako programu obsługi znajdują się w temacie [obsługa Event Hubs](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Usługa IoT Hub

Zasubskrybuj zdarzenia IoT Hub, aby odpowiedzieć na zdarzenia utworzone, usunięte, połączone, rozłączone i telemetrii.

|Tytuł  |Opis  |
|---------|---------|
| [Wysyłanie powiadomień e-mail dotyczących zdarzeń usługi Azure IoT Hub przy użyciu Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie zostanie dodane do IoT Hub. |
| [Reagowanie na zdarzenia IoT Hub przy użyciu Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md) | Omówienie integrowania IoT Hub z Event Grid. |
| [Schemat zdarzenia](event-schema-iot-hub.md) | Wyświetla pola w zdarzeniach IoT Hub. |
| [Zamawianie połączonych urządzeń i zdarzeń odłączonych do urządzenia](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Pokazuje, jak zamówić zdarzenia stanu połączenia urządzenia. |

## <a name="key-vault-preview"></a>Key Vault (wersja zapoznawcza)

Integracja Key Vault z usługą Event Grid jest obecnie dostępna w wersji zapoznawczej. 

Subskrybuj zdarzenia Key Vault, aby otrzymywać powiadomienia o wygaśnięciu wpisu tajnego, klucz tajny wygasa lub wpis tajny ma dostępną nową wersję. 

|Tytuł  |Opis  |
|---------|---------|
| [Monitorowanie zdarzeń Key Vault z Azure Event Grid](../key-vault/event-grid-overview.md) | Omówienie integrowania Key Vault z Event Grid. |
| [Samouczek: Tworzenie i monitorowanie zdarzeń Key Vault przy użyciu Event Grid](../key-vault/event-grid-tutorial.md) | Dowiedz się, jak skonfigurować Event Grid powiadomienia dla Key Vault. |
| [Schemat zdarzenia](event-schema-key-vault.md) | Wyświetla pola w zdarzeniach Key Vault. |

## <a name="media-services"></a>Media Services

Zasubskrybuj zdarzenia Media Services, aby odpowiedzieć na zdarzenia stanu zadania.

|Tytuł  |Opis  |
|---------|---------|
| [Przegląd: oddziałanie do Media Services zdarzeń](../media-services/latest/reacting-to-media-services-events.md) | Omówienie integrowania Media Services z Event Grid. |
| [Samouczek: kierowanie zdarzeń Azure Media Services do niestandardowego punktu końcowego sieci Web przy użyciu interfejsu wiersza polecenia](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia z Media Services. |
| [Schemat zdarzenia](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Wyświetla pola w zdarzeniach Media Services. |

## <a name="resource-groups"></a>Grupy zasobów

Zasubskrybuj zdarzenia grupy zasobów, aby odpowiedzieć na zmiany zasobów w grupie zasobów.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: monitorowanie zmian maszyny wirtualnej przy użyciu Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki monitoruje zmiany w maszynie wirtualnej i wysyła wiadomości e-mail o tych zmianach. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla grupy zasobów](./scripts/event-grid-cli-resource-group.md)| Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów. Wysyła zdarzenia do elementu webhook. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla grupy zasobów i filtrowanie zasobów](./scripts/event-grid-cli-resource-group-filter.md) | Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów i filtruje zdarzenia dla jednego zasobu. |
| [PowerShell: subskrybowanie zdarzeń dla grupy zasobów](./scripts/event-grid-powershell-resource-group.md) | Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów. Wysyła zdarzenia do elementu webhook. |
| [PowerShell: subskrybowanie zdarzeń dla grupy zasobów i filtrowanie zasobów](./scripts/event-grid-powershell-resource-group-filter.md) | Przykładowy skrypt, który subskrybuje zdarzenia dla grupy zasobów i filtruje zdarzenia dla jednego zasobu. |
| [Szablon Menedżer zasobów: subskrypcja zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Subskrybuje zdarzenia dla subskrypcji lub grupy zasobów platformy Azure. Wysyła zdarzenia do elementu webhook. |
| [Schemat zdarzeń](event-schema-resource-groups.md) | Pokazuje pola w zdarzeniach grupy zasobów. |

## <a name="service-bus"></a>Service Bus

Subskrybuj zdarzenia Service Bus, aby odpowiedzieć na komunikaty bez aktywnego odbiornika.

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: przykłady dotyczące Azure Event Grid integracji Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid wysyła wiadomości z tematu Service Bus do aplikacji funkcji i aplikacji logiki. |
| [Przegląd: Azure Service Bus do integracji Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Omówienie integrowania Service Bus z Event Grid. |
| [Schemat zdarzenia](event-schema-service-bus.md) | Wyświetla pola w zdarzeniach Service Bus. |

## <a name="storage"></a>Magazyn

Zasubskrybuj zdarzenia Blob Storage, aby odpowiedzieć na zdarzenia utworzone i usunięte przez obiekt BLOB.

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń magazynu obiektów BLOB do niestandardowego internetowego punktu końcowego za pomocą interfejsu wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak za pomocą interfejsu wiersza polecenia platformy Azure wysyłać zdarzenia usługi BLOB Storage do elementu webhook. |
| [Szybki Start: kierowanie zdarzeń magazynu obiektów BLOB do niestandardowego punktu końcowego sieci Web przy użyciu programu PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, w jaki sposób używać Azure PowerShell do wysyłania zdarzeń magazynu obiektów BLOB do elementu webhook. |
| [Szybki Start: Tworzenie i kierowanie zdarzeń magazynu obiektów BLOB za pomocą Azure Portal](blob-event-quickstart-portal.md) | Pokazuje, jak za pomocą portalu wysyłać zdarzenia magazynu obiektów BLOB do elementu webhook. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla konta usługi BLOB Storage](./scripts/event-grid-cli-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta usługi BLOB Storage. Wysyła zdarzenie do elementu webhook. |
| [PowerShell: subskrybowanie zdarzeń dla konta usługi BLOB Storage](./scripts/event-grid-powershell-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta usługi BLOB Storage. Wysyła zdarzenie do elementu webhook. |
| [Szablon Menedżer zasobów: Tworzenie magazynu obiektów blob i subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. Wysyła zdarzenia do elementu webhook. |
| [Przegląd: reagowanie na zdarzenia usługi BLOB Storage](../storage/blobs/storage-blob-event-overview.md) | Omówienie integrowania usługi BLOB Storage z Event Grid. |
| [Schemat zdarzenia](event-schema-blob-storage.md) | Wyświetla pola w zdarzeniach Blob Storage. |

## <a name="maps"></a>Maps
Zasubskrybuj zdarzenia Azure Maps, aby odpowiedzieć na zdarzenia geofencingu. Aplikacja może na przykład dostarczyć powiadomienie e-mail za każdym razem, gdy urządzenie przejdzie lub wyjdzie z geoogrodzenia.

|Tytuł  |Opis  |
|---------|---------|
| [Reagowanie na zdarzenia Azure Maps przy użyciu Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Omówienie integrowania Azure Maps z Event Grid. |
| [Samouczek: Konfigurowanie geoogrodzenia](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania geofencingu przy użyciu usługi Azure Maps. Używasz Azure Event Grid do przesyłania strumieniowego wyników geoogrodzenia i konfigurowania powiadomienia na podstawie wyników. |
| [Schemat zdarzenia](event-schema-azure-maps.md) | Wyświetla pola w zdarzeniach Azure Maps. |

## <a name="app-configuration"></a>Konfiguracja aplikacji
Zasubskrybuj zdarzenia konfiguracji aplikacji platformy Azure w celu reagowania na zdarzenia modyfikacji klucza i wartości.

|Tytuł | Opis |
|---------|---------|
| [Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure za pomocą Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Omówienie integracji usługi Azure App Configuration z Event Grid. |
| [Szybki Start: kierowanie zdarzeń konfiguracji aplikacji platformy Azure do niestandardowego internetowego punktu końcowego za pomocą interfejsu wiersza polecenia platformy Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, w jaki sposób używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń konfiguracji aplikacji platformy Azure do elementu webhook. |
| [Schemat zdarzenia](event-schema-app-configuration.md) | Wyświetla pola w zdarzeniach konfiguracji aplikacji platformy Azure. |

## <a name="azure-signalr"></a>Azure SignalR
Zasubskrybuj zdarzenia usługi Azure Signal Service, aby odpowiedzieć na zdarzenia połączenia klienta.

|Tytuł | Opis |
|---------|---------|
| [Reagowanie na zdarzenia usługi Azure sygnalizujące przy użyciu Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Omówienie integrowania usługi Azure Signal z Event Grid. |
| [Jak wysyłać zdarzenia usługi Azure sygnalizacyjne do Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Pokazuje, w jaki sposób wysyłać zdarzenia usługi Azure Signal do aplikacji za pomocą Event Grid. |
| [Schemat zdarzenia](event-schema-azure-signalr.md) | Wyświetla pola w zdarzeniach usługi Azure Signal Service. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Zasubskrybuj Azure Machine Learning zdarzenia obszaru roboczego, aby odpowiedzieć na rejestrację modelu.

| Tytuł | Opis |
| ----- | ----- |
| [Korzystanie z zdarzeń Azure Machine Learning](../machine-learning/service/concept-event-grid-integration.md) | Omówienie integrowania Azure Machine Learning z Event Grid. |
| [Schemat zdarzeń Azure Event Grid dla Azure Machine Learning](event-schema-machine-learning.md) | Wyświetla pola w zdarzeniach Azure Machine Learning. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).

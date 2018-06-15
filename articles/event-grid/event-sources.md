---
title: Źródła zdarzeń siatki zdarzeń platformy Azure
description: Zawiera opis źródła zdarzeń obsługiwanych Azure zdarzeń siatki
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34302130"
---
# <a name="event-sources-in-azure-event-grid"></a>Źródła zdarzeń w siatce zdarzeń platformy Azure

Źródłem zdarzenia jest, gdy zdarzenie. Wiele usług platformy Azure są automatycznie konfigurowane do wysyłania zdarzeń. Istnieje również możliwość utworzenia niestandardowych aplikacji, które wysyłają zdarzenia. Niestandardowe aplikacje zbędna, nie ma być obsługiwana na platformie Azure na potrzeby dystrybucji zdarzeń siatki zdarzeń.

Ten artykuł zawiera linki do zawartości dla każdego źródła zdarzenia.

## <a name="azure-subscriptions"></a>Subskrypcje platformy Azure

Subskrybowanie zdarzeń subskrypcji platformy Azure do reagowania na zmiany w zasobach między subskrypcją platformy Azure.

|Stanowisko |Opis  |
|---------|---------|
| [Integracja usługi Automatyzacja Azure z siatki zdarzeń i zespoły firmy Microsoft](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła odpowiednie zdarzenie. Zdarzenie wyzwalane element runbook usługi Automatyzacja, znaczniki maszyny wirtualnej, która wyzwala komunikat, który jest wysyłany do kanału Teams firmy Microsoft. |
| [Schematu zdarzeń](event-schema-subscriptions.md) | Pokazuje pola w zdarzeniach subskrypcji platformy Azure. |

## <a name="custom-topics"></a>Niestandardowe — tematy

Subskrybowanie tematów niestandardowe na odpowiadanie na zdarzenia aplikacji.

|Stanowisko  |Opis  |
|---------|---------|
| [Tworzenie i trasy niestandardowe zdarzenia z wiersza polecenia platformy Azure](custom-event-quickstart.md) | Przedstawia sposób użycia interfejsu wiersza polecenia Azure do wysyłania zdarzeń niestandardowych. |
| [Tworzenie i trasy zdarzeń niestandardowych przy użyciu programu Azure PowerShell](custom-event-quickstart-powershell.md) | Przedstawia sposób użycia programu Azure PowerShell do wysyłania zdarzeń niestandardowych. |
| [Tworzenie i trasy zdarzeń niestandardowych w portalu Azure](custom-event-quickstart-portal.md) | Pokazuje, jak korzystać z portalu do wysyłania zdarzeń niestandardowych. |
| [Wysyłanie do niestandardowego tematu](post-to-custom-topic.md) | Pokazuje, jak można wysłać zdarzenia do niestandardowego tematu. |
| [Trasy zdarzeń niestandardowych w celu magazynu kolejek Azure](custom-event-to-queue-storage.md) | Opisuje sposób wysyłania zdarzeń niestandardowych magazynu kolejek. |
| [Schematu zdarzeń](event-schema.md) | Pokazuje pola w zdarzeniach niestandardowych. |

## <a name="event-hubs"></a>Event Hubs

Subskrybowanie zdarzeń usługi Event Hubs na odpowiadanie na zdarzenia pliku przechwytywania.

|Stanowisko  |Opis  |
|---------|---------|
| [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy usługa Event Hubs tworzy plik przechwytywania, siatki zdarzeń wysyła odpowiednie zdarzenie w aplikacji funkcji. Aplikacja pobiera plik przechwytywania i przeprowadzanie migracji danych do magazynu danych. |
| [Schematu zdarzeń](event-schema-event-hubs.md) | Pokazuje pola w zdarzeniach usługi Event Hubs. |

## <a name="iot-hub"></a>Usługa IoT Hub

Subskrypcja do Centrum IoT zdarzenia odpowiedzieć na urządzeniu tworzona i usunąć zdarzenia.

|Stanowisko  |Opis  |
|---------|---------|
| [Wysyłanie powiadomień e-mail o zdarzeniach Centrum IoT Azure za pomocą aplikacji logiki](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie dodane do Centrum IoT. |
| [Reagowanie na zdarzenia Centrum IoT przy użyciu siatki zdarzeń do akcje wyzwalacza](../iot-hub/iot-hub-event-grid.md) | Omówienie integracji centra Iot z siatki zdarzeń. |
| [Schematu zdarzeń](event-schema-iot-hub.md) | Pokazuje pola w Centrum IoT zdarzenia. |

## <a name="media-services"></a>Media Services

Subskrybowanie zdarzeń usługi Media Services na odpowiadanie na zdarzenia stanu zadania.

|Stanowisko  |Opis  |
|---------|---------|
| [Reaguje na zdarzenia usługi Media Services](../media-services/latest/reacting-to-media-services-events.md) | Przegląd integrowania usługi Media Services z siatki zdarzeń. |
| [Zdarzenia usługi Azure Media Services trasy do punktu końcowego niestandardowe sieci web przy użyciu interfejsu wiersza polecenia](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Przedstawia sposób wysłania zdarzeń z usługi Media Services. |
| [Schematu zdarzeń](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje pola w zdarzeniach usługi Media Services. |

## <a name="resource-groups"></a>Grupy zasobów

Subskrybowanie zdarzeń grupy zasobów do reagowania na zmiany w zasoby w grupie zasobów.

|Stanowisko  |Opis  |
|---------|---------|
| [Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki śledzi zmiany w maszynie wirtualnej i wysyła powiadomienia dotyczące tych zmian. |
| [Schemat zdarzeń](event-schema-resource-groups.md) | Grupowanie zdarzeń zawiera pola zasobów. |

## <a name="service-bus"></a>Service Bus

Subskrybowanie zdarzeń usługi Service Bus, aby odpowiadać na komunikaty bez aktywnego odbiornika.

|Stanowisko  |Opis  |
|---------|---------|
| [Usługa Azure Service Bus do siatki zdarzeń Azure integracji przykładów](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Siatka zdarzeń wysyła komunikaty z tematu usługi Service Bus działanie aplikacji i aplikacji logiki. |
| [Usługa Azure Service Bus do siatki zdarzeń omówienie integracji](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Przegląd integrowania usługi Service Bus zdarzeń siatki. |
| [Schematu zdarzeń](event-schema-service-bus.md) | Pokazuje pola w zdarzeniach usługi Service Bus. |

## <a name="storage"></a>Magazyn

Subskrybowanie zdarzeń magazynu obiektów Blob na odpowiadanie na zdarzenia utworzone lub usunięte obiektu blob.

|Stanowisko  |Opis  |
|---------|---------|
| [Zdarzenia magazynu obiektów Blob trasy do punktu końcowego niestandardowe sieci web z wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak wysyłać zdarzenia magazynu obiektów blob przy użyciu wiersza polecenia platformy Azure. |
| [Zdarzenia magazynu obiektów Blob trasy do punktu końcowego niestandardowe sieci web przy użyciu programu PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazano, jak wysyłać zdarzenia magazynu obiektów blob przy użyciu programu Azure PowerShell. |
| [Reagowanie na zdarzenia usługi Blob Storage](../storage/blobs/storage-blob-event-overview.md) | Omówienie integracji magazynu obiektów Blob z siatki zdarzeń. |
| [Schematu zdarzeń](event-schema-blob-storage.md) | Pokazuje pola w zdarzeniach magazynu obiektów Blob. |

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [o siatki zdarzeń](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).

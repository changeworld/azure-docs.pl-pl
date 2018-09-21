---
title: Programy obsługi zdarzeń Azure Event Grid
description: W tym artykule opisano procedury obsługi zdarzeń obsługiwanych dla usługi Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: tomfitz
ms.openlocfilehash: 12cc5a918044e3bc74e34f2e05adc9773dee5421
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498661"
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

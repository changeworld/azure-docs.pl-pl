---
title: Programy obsługi zdarzeń siatki zdarzeń platformy Azure
description: W tym artykule opisano procedury obsługi zdarzeń obsługiwanych Azure zdarzeń siatki
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 01a49ec47b406b7b916ecad5ef9ccc3af295fac1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="event-handlers-in-azure-event-grid"></a>Programy obsługi zdarzeń w siatce zdarzeń platformy Azure

Program obsługi zdarzeń jest miejscem, w której zdarzenia są wysyłane. Program obsługi ma niektórych dalszych kroków w celu przetworzenia zdarzenia. Wiele usług platformy Azure są automatycznie konfigurowane do obsługi zdarzeń. Umożliwia także dowolnego elementu webhook na potrzeby obsługi zdarzeń. Elementu webhook nie musi być hostowana na platformie Azure do obsługi zdarzeń.

Ten artykuł zawiera linki do zawartości dla każdego programu obsługi zdarzeń.

## <a name="azure-automation"></a>Azure Automation

Używanie automatyzacji Azure do zdarzenia procesu z automatycznych elementów runbook.

|Stanowisko  |Opis  |
|---------|---------|
|[Integracja usługi Automatyzacja Azure z siatki zdarzeń i zespoły firmy Microsoft](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła odpowiednie zdarzenie. Zdarzenie wyzwalane element runbook usługi Automatyzacja, znaczniki maszyny wirtualnej, która wyzwala komunikat, który jest wysyłany do kanału Teams firmy Microsoft. |

## <a name="azure-functions"></a>Azure Functions

Użyj usługi Azure Functions dla niekorzystającą odpowiedzi na zdarzenia.

|Stanowisko  |Opis  |
|---------|---------|
| [Wyzwalacz siatki zdarzeń dla usługi Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Omówienie funkcji za pomocą wyzwalacza zdarzenia siatki. |
| [Automatyzowanie zmiany rozmiaru obrazów przekazanych obrazów za pomocą usługi Event Grid](resize-images-on-storage-blob-upload-event.md) | Użytkownicy przekazywanie obrazów za pośrednictwem aplikacji sieci web do konta magazynu. Po utworzeniu obiektu blob magazynu siatki zdarzeń wysyła zdarzenia do aplikacji funkcji, która zmienia rozmiar załadowanego obrazu. |
| [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md) | Gdy usługa Event Hubs tworzy plik przechwytywania, siatki zdarzeń wysyła odpowiednie zdarzenie w aplikacji funkcji. Aplikacja pobiera plik przechwytywania i przeprowadzanie migracji danych do magazynu danych. |
| [Usługa Azure Service Bus do siatki zdarzeń Azure integracji przykładów](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Siatka zdarzeń wysyła komunikaty z tematu usługi Service Bus działanie aplikacji i aplikacji logiki. |

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Za pomocą połączeń hybrydowych przekazywania Azure do wysyłania zdarzeń do aplikacji, które znajdują się w sieci przedsiębiorstwa i nie masz publicznie dostępnym punkcie końcowym.

|Stanowisko  |Opis  |
|---------|---------|
| [Wysyłanie zdarzeń połączenia hybrydowego](custom-event-to-hybrid-connection.md) | Wysyła zdarzenie niestandardowe do istniejącego połączenia hybrydowego dla przetwarzania przez aplikację do odbiornika. |

## <a name="logic-apps"></a>Logic Apps

Korzystanie z aplikacji logiki można automatyzować procesy biznesowe reagowania na zdarzenia.

|Stanowisko  |Opis  |
|---------|---------|
| [Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikacja logiki śledzi zmiany w maszynie wirtualnej i wysyła powiadomienia dotyczące tych zmian. |
| [Wysyłanie powiadomień e-mail o zdarzeniach Centrum IoT Azure za pomocą aplikacji logiki](publish-iot-hub-events-to-logic-apps.md) | Aplikacja logiki wysyła wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie dodane do Centrum IoT. |
| [Usługa Azure Service Bus do siatki zdarzeń Azure integracji przykładów](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Siatka zdarzeń wysyła komunikaty z tematu usługi Service Bus działanie aplikacji i aplikacji logiki. |

## <a name="queue-storage"></a>Queue Storage

Aby odbierać zdarzenia, które muszą być pobierane, należy używać magazynu kolejek.

|Stanowisko  |Opis  |
|---------|---------|
| [Trasy zdarzeń niestandardowych w celu magazynu kolejek Azure z wiersza polecenia platformy Azure i siatki zdarzeń](custom-event-to-queue-storage.md) | Opisuje sposób wysyłania zdarzeń niestandardowych magazynu kolejek. |

## <a name="webhooks"></a>Elementy webhook

Użyj elementów webhook dla dostosowania punktów końcowych, które odpowiadanie na zdarzenia.

|Stanowisko  |Opis  |
|---------|---------|
| [Odbieranie zdarzeń do punktu końcowego HTTP](receive-events.md) | Opisuje sposób sprawdzania poprawności punktu końcowego HTTP odbieranie zdarzeń z subskrypcji zdarzeń i otrzymywanie i deserializować zdarzenia. |

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [o siatki zdarzeń](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).

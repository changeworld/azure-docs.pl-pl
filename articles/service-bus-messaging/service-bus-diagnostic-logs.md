---
title: Azure Service Bus dzienników diagnostycznych | Microsoft Docs
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne dla Service Bus na platformie Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592470"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Włączanie dzienników diagnostycznych dla Service Bus

Po rozpoczęciu korzystania z przestrzeni nazw Azure Service Bus można monitorować sposób i czas tworzenia, usuwania lub uzyskiwania dostępu do przestrzeni nazw. Ten artykuł zawiera omówienie wszystkich dostępnych dzienników operacyjnych/diagnostycznych.

Azure Service Bus obecnie obsługuje dzienniki aktywności/działania, które przechwytują **operacje zarządzania** wykonane na Azure Service Bus przestrzeni nazw. W szczególności te dzienniki przechwytują typ operacji, w tym Tworzenie kolejki, używane zasoby i stan operacji.

## <a name="operational-logs-schema"></a>Schemat dzienników operacyjnych

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON) w poniższych dwóch lokalizacjach.

- **Azure** — wyświetla dzienniki z operacji/akcji wykonywanych względem przestrzeni nazw w portalu lub za pomocą wdrożeń szablonów Azure Resource Manager.
- **AzureDiagnostics** — wyświetla dzienniki z operacji/akcji wykonywanych w odniesieniu do przestrzeni nazw za pomocą interfejsu API lub przez klientów zarządzania w zestawie SDK języka.

Ciągi JSON dziennika operacyjnego zawierają elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ------- | ------- |
| ActivityId | Wewnętrzny identyfikator używany do identyfikowania określonego działania |
| eventName | Nazwa operacji |
| ResourceId | Identyfikator zasobu Azure Resource Manager |
| SubscriptionId | Identyfikator subskrypcji |
| EventTimeString | Czas operacji |
| EventProperties | Właściwości operacji |
| Stan | Stan operacji |
| Obiekt wywołujący | Obiekt wywołujący operacji (Azure Portal lub klient zarządzania) |
| Kategoria | OperationalLogs |

Oto przykład ciągu JSON dziennika operacyjnego:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Jakie zdarzenia/operacje są przechwytywane w dziennikach operacyjnych?

Dzienniki operacji przechwytują wszystkie operacje zarządzania wykonane na Azure Service Bus przestrzeni nazw. Operacje na danych nie są przechwytywane z powodu dużej ilości operacji na danych, które są wykonywane w Azure Service Bus.

> [!NOTE]
> Aby lepiej śledzić operacje na danych, zalecamy korzystanie z funkcji śledzenia po stronie klienta.

Poniższe operacje zarządzania są przechwytywane w dziennikach operacyjnych — 

| Zakres | Operacja|
|-------| -------- |
| Przestrzeń nazw | <ul> <li> Utwórz przestrzeń nazw</li> <li> Aktualizowanie przestrzeni nazw </li> <li> Usuń przestrzeń nazw </li>  </ul> | 
| Kolejka | <ul> <li> Utwórz kolejkę</li> <li> Aktualizuj kolejkę</li> <li> Usuń kolejkę </li> </ul> | 
| Temat | <ul> <li> Utwórz temat </li> <li> Aktualizowanie tematu </li> <li> Usuń temat </li> </ul> |
| Subskrypcja | <ul> <li> Tworzenie subskrypcji </li> <li> Aktualizowanie subskrypcji </li> <li> Usuń subskrypcję </li> </ul> |

> [!NOTE]
> Obecnie operacje **odczytu** nie są śledzone w dziennikach operacyjnych.

## <a name="how-to-enable-operational-logs"></a>Jak włączyć dzienniki operacyjne?

Dzienniki operacyjne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do przestrzeni nazw Azure Service Bus i w obszarze **monitorowanie**kliknij pozycję **Ustawienia diagnostyki**.

   ![Nawigacja w bloku do dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image1.png)

2. Kliknij pozycję **Dodaj ustawienie diagnostyczne** , aby skonfigurować ustawienia diagnostyczne.  

   ![Włączanie dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image2.png)

3. Konfigurowanie ustawień diagnostycznych
   1. Wpisz **nazwę** , aby zidentyfikować ustawienia diagnostyczne.
   2. Wybierz lokalizację docelową dla diagnostyki.
      - W przypadku wybrania **konta magazynu**należy skonfigurować konto magazynu, na którym będzie przechowywana Diagnostyka.
      - W przypadku wybrania **centrów zdarzeń**należy skonfigurować odpowiednie centrum zdarzeń, do którego zostaną przesłane strumieniowo ustawienia diagnostyki.
      - W przypadku wybrania **log Analytics**należy określić wystąpienie log Analytics do wysłania diagnostyki.
    3. Sprawdź **OperationalLogs**.

       ![Zmień stan dzienników diagnostycznych](./media/service-bus-diagnostic-logs/image3.png)

4. Kliknij przycisk **Save** (Zapisz).


Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym archiwizowania w bloku **dzienniki diagnostyczne** .

Więcej informacji o konfigurowaniu diagnostyki znajduje się w temacie [Omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus, zobacz następujące linki:

* [Wprowadzenie do Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do Service Bus](service-bus-dotnet-get-started-with-queues.md)

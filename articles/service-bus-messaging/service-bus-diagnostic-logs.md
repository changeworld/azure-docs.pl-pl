---
title: Dzienniki diagnostyczne usługi Azure Service Bus | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie wszystkich dzienników operacyjnych i diagnostycznych, które są dostępne dla usługi Azure Service Bus.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c8eba538a7015648611e6054ce85b381dcfc9105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761002"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Włączanie dzienników diagnostyki dla magistrali usługOwej

Po rozpoczęciu korzystania z obszaru nazw usługi Azure Service Bus, można monitorować, jak i kiedy obszar nazw jest tworzony, usuwany lub uzyskiwać dostęp. Ten artykuł zawiera omówienie wszystkich dzienników operacyjnych i diagnostycznych, które są dostępne.

Usługa Azure Service Bus obsługuje obecnie dzienniki działań i operacji, które przechwytują *operacje zarządzania* wykonywane w obszarze nazw usługi Azure Service Bus. W szczególności te dzienniki przechwytują typ operacji, w tym tworzenie kolejki, używane zasoby i stan operacji.

## <a name="operational-logs-schema"></a>Schemat dzienników operacyjnych

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON) w następujących dwóch lokalizacjach:

- **AzureActivity:** Wyświetla dzienniki z operacji i akcji, które są prowadzone dla obszaru nazw w witrynie Azure portal lub za pośrednictwem wdrożenia szablonów usługi Azure Resource Manager.
- **AzureDiagnostics:** Wyświetla dzienniki z operacji i akcji, które są prowadzone dla obszaru nazw przy użyciu interfejsu API lub za pośrednictwem klientów zarządzania w języku SDK.

Parametry JSON dziennika operacyjnego obejmują elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ------- | ------- |
| Activityid | Identyfikator wewnętrzny, używany do identyfikacji określonego działania |
| EventName | Nazwa operacji |
| ResourceId | Identyfikator zasobu usługi Azure Resource Manager |
| SubscriptionId | Identyfikator subskrypcji |
| EventTimeString | Czas pracy |
| EventProperties | Właściwości operacji |
| Stan | Stan operacji |
| Obiekt wywołujący | Obiekt wywołujący operację (portal Azure lub klient zarządzania) |
| Kategoria | Dzienniki operacyjne |

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

## <a name="events-and-operations-captured-in-operational-logs"></a>Zdarzenia i operacje przechwycone w dziennikach operacyjnych

Dzienniki operacyjne przechwytują wszystkie operacje zarządzania wykonywane w obszarze nazw usługi Azure Service Bus. Operacje na danych nie są przechwytywane ze względu na dużą liczbę operacji danych, które są prowadzone w usłudze Azure Service Bus.

> [!NOTE]
> Aby ułatwić lepsze śledzenie operacji danych, zalecamy korzystanie z śledzenia po stronie klienta.

Następujące operacje zarządzania są przechwytywane w dziennikach operacyjnych: 

| Zakres | Operacja|
|-------| -------- |
| Przestrzeń nazw | <ul> <li> Tworzenie obszaru nazw</li> <li> Aktualizowanie obszaru nazw </li> <li> Usuń obszar nazw </li>  </ul> | 
| Kolejka | <ul> <li> Utwórz kolejkę</li> <li> Kolejka aktualizacji</li> <li> Usuń kolejkę </li> </ul> | 
| Temat | <ul> <li> Utwórz temat </li> <li> Temat aktualizacji </li> <li> Usuń temat </li> </ul> |
| Subskrypcja | <ul> <li> Tworzenie subskrypcji </li> <li> Aktualizuj subskrypcję </li> <li> Usuń subskrypcję </li> </ul> |

> [!NOTE]
> Obecnie operacje *odczytu* nie są śledzone w dziennikach operacyjnych.

## <a name="enable-operational-logs"></a>Włączanie dzienników operacyjnych

Dzienniki operacyjne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do obszaru nazw usługi Azure Service Bus, a następnie w obszarze **Monitorowanie**wybierz pozycję **Ustawienia diagnostyczne**.

   ![Łącze "Ustawienia diagnostyczne"](./media/service-bus-diagnostic-logs/image1.png)

1. W okienku **Ustawienia diagnostyki** wybierz pozycję **Dodaj ustawienie diagnostyczne**.  

   ![Łącze "Dodaj ustawienie diagnostyczne"](./media/service-bus-diagnostic-logs/image2.png)

1. Skonfiguruj ustawienia diagnostyki, wykonując następujące czynności:

   a. W polu **Nazwa** wprowadź nazwę ustawień diagnostyki.  

   b. Wybierz jedno z następujących trzech miejsc docelowych dla dzienników diagnostycznych:  
   - Jeśli wybierzesz **Archiwum do konta magazynu,** musisz skonfigurować konto magazynu, na którym będą przechowywane dzienniki diagnostyczne.  
   - Jeśli wybierzesz **Strumień do centrum zdarzeń,** należy skonfigurować centrum zdarzeń, do którego chcesz przesyłać strumieniowo dzienniki diagnostyczne.
   - Jeśli wybierzesz **Wyślij do usługi Log Analytics,** musisz określić, do którego wystąpienia usługi Log Analytics zostanie wysłana diagnostyka.  

   d. Zaznacz pole wyboru **OperationalLogs.**

    ![Okienko "Ustawienia diagnostyki"](./media/service-bus-diagnostic-logs/image3.png)

1. Wybierz **pozycję Zapisz**.

Nowe ustawienia zajmą się efektem około 10 minut. Dzienniki są wyświetlane w skonfigurowanym celu archiwizacji w okienku **Dzienniki diagnostyki.**

Aby uzyskać więcej informacji na temat konfigurowania ustawień diagnostycznych, zobacz [omówienie dzienników diagnostyki platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Service Bus, zobacz:

* [Wprowadzenie do usługi Service Bus](service-bus-messaging-overview.md)
* [Rozpoczynanie pracy z usługą Service Bus](service-bus-dotnet-get-started-with-queues.md)

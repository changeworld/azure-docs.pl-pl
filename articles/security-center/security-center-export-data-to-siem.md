---
title: Eksportowanie danych zabezpieczeń platformy Azure do SIEM potoku konfiguracji [Podgląd] | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera dokumentację produktu pobierania Azure security center dzienniki, aby używany system SIEM
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: 7a0a72a25010952f13eb190f0e0a1a65cc6d42d3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124837"
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Eksportowanie danych zabezpieczeń platformy Azure do SIEM potoku konfiguracji [Podgląd]

Ten dokument zawiera szczegóły dotyczące procedury, aby wyeksportować dane zabezpieczeń w Centrum zabezpieczeń Azure do rozwiązania SIEM.

Przetworzone zdarzenia generowane przez Centrum zabezpieczeń Azure są publikowane do platformy Azure [dziennik aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), jeden dziennika typów dostępne za pośrednictwem Monitora Azure. Azure Monitor oferuje skonsolidowanych potoku dla routingu żadnych danych monitorowania do narzędzia SIEM. Jest to realizowane przez strumieniowe przesyłanie danych do Centrum zdarzeń, w którym go może następnie być pobierane do narzędzia partnera.

Używa tego potoku [monitorowania Azure pojedynczy potok](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) do uzyskiwania dostępu do danych monitorowania z Twoim środowiskiem platformy Azure. Dzięki temu można łatwo skonfigurować rozwiązań Siem i narzędzi do monitorowania danych.

Kolejne sekcje opisują, jak można skonfigurować dane przesyłane strumieniowo do Centrum zdarzeń. W krokach założono, że masz już skonfigurowane w ramach subskrypcji platformy Azure Centrum zabezpieczeń Azure.

Ogólne omówienie

![Ogólne omówienie](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co to jest narażony na SIEM danych zabezpieczeń platformy Azure?

W tej wersji zapoznawczej uwidaczniamy [alertów zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md) W kolejnych wersjach firma Microsoft będzie wzbogacić zestaw danych o zalecenia dotyczące zabezpieczeń.

## <a name="how-to-setup-the-pipeline"></a>Jak skonfigurować potoku? 

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń 

Przed rozpoczęciem należy [tworzenie przestrzeni nazw usługi Event Hubs](../event-hubs/event-hubs-create.md). Ten obszar nazw i Centrum zdarzeń jest miejscem docelowym dla monitorowania danych.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Strumień dziennika aktywności platformy Azure do usługi Event Hubs

Zapoznaj się z następującym artykułem [dziennik aktywności strumienia do usługi Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Instalowanie łącznika SIEM partnera 

Routing monitorowanych danych do Centrum zdarzeń z monitorem Azure pozwala łatwo zintegrować z partnerem SIEM i narzędzi do monitorowania.

Zobacz następujące łącze, aby wyświetlić listę [obsługiwane rozwiązań Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Przykład wykonywanie zapytania na danych 

Oto kilka zapytań Splunk, które służy do pobierania danych alertu:

| **Opis elementu zapytania**                                | **Zapytanie**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Wszystkie alerty                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Podsumuj liczba operacji według nazwy             | Indeks = głównego źródłowa = "amal: zabezpieczeń" \| operationName tabeli \| Statystyka liczba przez operationName                                |
| Pobierz informacje dotyczące alertów: czas, nazwę stanu, identyfikator i subskrypcji | Indeks = głównego Microsoft.Security/locations/alerts \| tabeli \_czasu, properties.eventName, stan, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Kolejne kroki

- [Obsługiwane rozwiązań Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Strumieniowe przesyłanie dzienników aktywności do usługi Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Alerty zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md)
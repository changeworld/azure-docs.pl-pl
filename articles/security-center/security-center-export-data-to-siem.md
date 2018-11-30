---
title: Danych zabezpieczeń platformy Azure, eksportowanie do rozwiązania SIEM — Konfiguracja potoku | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera dokumentację produktu pobierania zabezpieczeń platformy Azure, dzienniki Centrum do rozwiązania SIEM
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: barclayn
ms.openlocfilehash: 4cc9478197b39198a94ace1cbefab81de3cdb32b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52306730"
---
# <a name="azure-security-data-export-to-siem-pipeline-configuration"></a>Usługa Azure Eksport danych zabezpieczeń do konfiguracji rozwiązania SIEM potoku

W tym dokumencie przedstawiono procedurę eksportowania danych zabezpieczeń usługi Azure Security Center do rozwiązania SIEM.

Przetworzone zdarzenia generowane przez usługę Azure Security Center są publikowane na platformie Azure [dziennika aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), jeden z dziennika typów dostępnych za pośrednictwem usługi Azure Monitor. Usługa Azure Monitor udostępnia skonsolidowany potoku na potrzeby routingu jakichkolwiek danych monitorowania do narzędzia SIEM. Polega to na przesyłanie strumieniowe danych do Centrum zdarzeń, gdzie go może następnie zostać pobrane do narzędzia partnera.

Ten potok używa [monitorowania platformy Azure w jeden potok](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) w celu uzyskania dostępu do danych monitorowania ze środowiska platformy Azure. Dzięki temu można łatwo skonfigurować rozwiązań Siem i narzędzi do monitorowania, korzystającą z danych.

W kolejnych sekcjach opisano, jak można skonfigurować dane przesyłane strumieniowo do Centrum zdarzeń. W krokach założono, że masz już usługę Azure Security Center skonfigurowane w ramach subskrypcji platformy Azure.

Ogólne omówienie

![Ogólne omówienie](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co to jest udostępniane do rozwiązania SIEM dane zabezpieczeń platformy Azure?

W tej wersji uwidaczniamy [alertów zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md) W następnych wersjach firma Microsoft będzie wzbogacić zestawu danych z zaleceniami dotyczącymi zabezpieczeń.

## <a name="how-to-setup-the-pipeline"></a>Jak skonfigurować potok

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Przed rozpoczęciem należy [tworzenie przestrzeni nazw usługi Event Hubs](../event-hubs/event-hubs-create.md). Ten obszar nazw i Centrum zdarzeń jest miejsce docelowe dla wszystkich danych monitorowania.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream dziennika aktywności platformy Azure do usługi Event Hubs

Zapoznaj się z następującym artykułem [strumieniowe przesyłanie dzienników aktywności do usługi Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>Instalowanie łącznika SIEM partnera 

Routing danych monitorowania do Centrum zdarzeń za pomocą usługi Azure Monitor pozwala łatwo zintegrować ją z partnerem rozwiązania SIEM i narzędzi do monitorowania.

Zapoznaj się z następującego linku, aby wyświetlić listę [obsługiwanych rozwiązań Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Przykład wykonywanie zapytań o dane 

Poniżej przedstawiono kilka zapytań Splunk, które służy do pobierania danych alertu:

| **Opis kwerendy**                                | **Zapytanie**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Wszystkie alerty                                              | Indeks = Microsoft.Security/locations/alerts głównego                                                                                         |
| Podsumowanie łącznej liczby operacji według nazwy             | Indeks = głównego sourcetype = "amal: zabezpieczeń" \| operationName tabeli \| statystyki, liczba przez operationName                                |
| Pobierz informacje dotyczące alertów: czas, nazwa, stan, identyfikator i subskrypcji | Indeks = głównego Microsoft.Security/locations/alerts \| tabeli \_czasu, properties.eventName, stan, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Kolejne kroki

- [Obsługiwanych rozwiązań Siem](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Strumieniowe przesyłanie dzienników aktywności do usługi Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Alerty zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md)
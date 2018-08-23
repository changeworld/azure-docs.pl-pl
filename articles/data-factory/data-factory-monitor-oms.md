---
title: Monitorowanie usługi Azure Data Factory za pomocą pakietu OMS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować usługę Azure Data Factory, routingu danych do usługi Operations Management Suite (OMS) w celu analizy.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42062094"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Monitorowanie usługi Azure Data Factory przy użyciu pakietu Operations Management Suite (OMS)

Za pomocą integracji usługi Azure Data Factory i Azure Monitor do kierowania danych do usługi Operations Management Suite (OMS). Jest to przydatne w następujących scenariuszach:

1.  Chcesz napisać złożonych zapytań w bogaty zestaw metryk, która zostanie opublikowana przez usługę Data Factory do pakietu OMS. Można również utworzyć niestandardowe alerty na te zapytania za pomocą pakietu OMS.

2.  Chcesz monitorować całej fabryki danych. Za przekazywanie danych z różnych fabryk danych z jednym obszarem roboczym OMS.

7 minutowym wprowadzenie i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Konfigurowanie ustawień diagnostycznych i obszar roboczy pakietu OMS

Włączanie ustawień diagnostycznych dla fabryki danych.

1.  Wybierz **usługi Azure Monitor** -> **ustawień diagnostycznych** -> Wybierz usługi data factory -> Włącz diagnostykę.

    ![Monitor — oms — image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Podaj ustawienia diagnostyczne, w tym konfiguracją obszar roboczy pakietu OMS.

    ![Monitor — oms — image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Zainstaluj pakiet Azure Data Factory Analytics OMS w witrynie Azure Marketplace

![Monitor — oms — image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Monitor — oms — image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Kliknij przycisk **Utwórz** i wybierz pozycję Ustawienia obszaru roboczego pakietu OMS i obszar roboczy pakietu OMS.

![Monitor — oms — image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Monitoruj metryki fabryki danych Azure przy użyciu pakietu OMS

Instalowanie **usługi Azure Data Factory Analytics** pakietu OMS tworzy domyślny zestaw widoków umożliwiająca następujące metryki:

- Uruchomienia potoku usługi ADF przebiegów-1) przez usługę Data Factory

- Uruchomienia działania usługi ADF przebiegów-2) przez usługę Data Factory

- Przebiegi ADF-3) uruchomienia wyzwalacza przez usługę Data Factory

- Błędy usługi ADF-1) 10 najważniejszych potoku błędów przez usługę Data Factory

- Uruchomienia działania pierwszych 10 błędy ADF-2) przez usługę Data Factory

- Błędy usługi ADF-3) 10 najważniejszych wyzwalacza błędów przez usługę Data Factory

- Uruchomienia działania usługi ADF statystyki-1) według typu

- Uruchomienia wyzwalacza statystyki ADF-2) według typu

- Czas trwania uruchomienia potoku Max statystyki ADF-3)

![Monitor — oms — image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![Monitor — oms — image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Można wizualizować metryki powyżej, Przyjrzyj się zapytania za te metryki, edytowanie zapytań, tworzenie alertów i tak dalej.

![Monitor — oms — image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [monitorowanie potoków i zarządzanie nimi programistycznie](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) informacje na temat monitorowania oraz zarządzanie potokami dzięki uruchamianiu skryptów.

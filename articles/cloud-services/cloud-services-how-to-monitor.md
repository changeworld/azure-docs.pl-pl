---
title: Monitorowanie usługi w chmurze platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje jakie monitorowania usługi w chmurze platformy Azure obejmuje i jakie niektóre opcje są.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: jeconnoc
ms.openlocfilehash: 844fef9a87c1db06c6415c59d4be26caf928382b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432917"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Wprowadzenie do monitorowania usługi w chmurze

Możesz monitorować kluczowe metryki wydajności dla dowolnej usługi w chmurze. Rola usługi w chmurze co zbiera dane minimalne: Użycie procesora CPU, użycia sieci i wykorzystanie dysku. Jeśli usługa w chmurze ma `Microsoft.Azure.Diagnostics` rozszerzenia stosowane do roli tej roli może zbierać dodatkowe punkty danych. Ten artykuł zawiera wprowadzenie do usługi Azure Diagnostics dla usług w chmurze.

Podstawowe monitorowanie danych licznika wydajności z wystąpień roli jest próbkowany i zebrane w odstępach 3-minutowy. Te podstawowe dane monitorowania nie są przechowywane na koncie magazynu i ma bez dodatkowych kosztów skojarzonych z nim.

Za pomocą zaawansowanego monitorowania dodatkowe metryki są próbkowane tak i zebrane w odstępach czasu wynoszącą 5 minut, godzinę i 12 godzin. Zagregowane dane są przechowywane na koncie magazynu w tabelach i są przeczyszczane po 10 dni. Skonfigurowano konto magazynu używane przez rolę; dla różnych ról, można użyć różnych kont magazynu. To jest skonfigurowany przy użyciu parametrów połączenia w [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) i [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) plików.


## <a name="basic-monitoring"></a>Podstawowe monitorowanie

Jak wspomniano we wprowadzeniu, usługi w chmurze automatycznie zbiera podstawowe dane monitorowania z hosta maszyny wirtualnej. Te dane obejmują procent procesora CPU, wchodzącym/wychodzącym sieci i odczytu/zapisu na dysku. Zebrane dane monitorowania jest automatycznie wyświetlana na stronach przeglądu i metryki usługi w chmurze, w witrynie Azure portal. 

Podstawowe monitorowanie nie wymaga konta magazynu. 

![Usługa w chmurze podstawowe monitorowanie kafelków](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Zaawansowane monitorowanie polega na użyciu **diagnostyki Azure** rozszerzenia (i opcjonalnie zestawu SDK usługi Application Insights) w roli, którą chcesz monitorować. Rozszerzenie diagnostyki korzysta z pliku konfiguracji (poszczególnych ról) o nazwie **diagnostics.wadcfgx** skonfigurować monitorowane metryki diagnostyki. Rozszerzenie diagnostyki platformy Azure zbiera i przechowuje dane na koncie usługi Azure Storage. Te ustawienia są konfigurowane w **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), i [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) plików. Oznacza to, że istnieje dodatkowy koszt związany z zaawansowane monitorowanie.

Podczas tworzenia poszczególnych ról programu Visual Studio dodaje rozszerzenia diagnostyki Azure do niego. To rozszerzenie diagnostyki zebrać następujące typy informacji:

* Niestandardowe liczniki wydajności
* Dzienniki aplikacji
* Dzienniki zdarzeń systemu Windows
* Źródło zdarzenia platformy .NET
* Dzienniki usług IIS
* Śledzenie zdarzeń systemu Windows w oparciu o manifest
* Zrzuty awaryjne
* Dzienniki błędów klienta

> [!IMPORTANT]
> Gdy te dane są agregowane na konto magazynu, portalu jest **nie** umożliwiają natywnych danych wykresu. Zdecydowanie zaleca się integracji innej usługi, takie jak usługi Application Insights do aplikacji.

## <a name="setup-diagnostics-extension"></a>Konfigurowanie rozszerzenia diagnostyki

Pierwsze, jeśli nie masz **klasycznego** konta magazynu, [utworzyć](../storage/common/storage-quickstart-create-account.md). Upewnij się, że konto magazynu jest tworzone za pomocą **klasycznego modelu wdrażania** określony.

Następnie przejdź do **konto magazynu (klasyczne)** zasobów. Wybierz **ustawienia** > **klucze dostępu** i skopiuj **podstawowe parametry połączenia** wartość. Ta wartość jest potrzebna dla usługi w chmurze. 

Istnieją dwa pliki konfiguracji dla zaawansowanej diagnostyki do włączenia, należy zmienić **ServiceDefinition.csdef** i **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

W **ServiceDefinition.csdef** Dodaj nowe ustawienie o nazwie `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` dla każdej roli, który używa zaawansowanej diagnostyki. Visual Studio dodaje tę wartość do pliku, podczas tworzenia nowego projektu. W przypadku, gdy go brakuje, można dodać go teraz. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Definiuje to nowe ustawienie, które muszą zostać dodane do każdego **ServiceConfiguration.cscfg** pliku. 

Prawdopodobnie masz dwa **.cscfg** pliki jedną o nazwie **ServiceConfiguration.cloud.cscfg** dotyczące wdrażania na platformie Azure i jedną o nazwie **ServiceConfiguration.local.cscfg** który jest używany w przypadku wdrożeń lokalnych w środowisku emulowane. Otwórz i zmień każdego **.cscfg** pliku. Dodaj ustawienie o nazwie `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Ustaw wartość **podstawowe parametry połączenia** konta klasycznego magazynu. Używany Magazyn lokalny na komputerze deweloperskim, należy użyć `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Korzystanie z usługi Application Insights

Podczas publikowania usługi w chmurze w programie Visual Studio, możesz skorzystać z opcji do wysyłania danych diagnostycznych do usługi Application Insights. Można utworzyć Application Insights w usłudze Azure resource, w tym czasie, lub wysłać dane do istniejącego zasobu platformy Azure. Usługi w chmurze mogą być monitorowane przez usługę Application Insights dla dostępności, wydajności, błędów i użycia. Niestandardowe wykresy można dodać do usługi Application Insights tak, aby zobaczyć dane, mają największe znaczenie. Dane wystąpienia roli mogą być zbierane przy użyciu zestawu SDK usługi Application Insights w projekcie usługi w chmurze. Aby uzyskać więcej informacji na temat integracji usługi Application Insights, zobacz [usługi Application Insights z usługami w chmurze](../azure-monitor/app/cloudservices.md).

Należy pamiętać, że podczas korzystania z usługi Application Insights, aby wyświetlić liczniki wydajności (i inne ustawienia) został określony za pomocą rozszerzenia diagnostyki Azure Windows, możesz jedynie Uzyskaj więcej możliwości dzięki integracji zestawu SDK usługi Application Insights do usługi role sieci web i proces roboczy.


## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o usłudze Application Insights z usługami w chmurze](../azure-monitor/app/cloudservices.md)
- [Skonfiguruj liczniki wydajności](diagnostics-performance-counters.md)


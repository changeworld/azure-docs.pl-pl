---
title: Monitorowanie usługi w chmurze platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano obejmuje co monitorowanie usługi w chmurze platformy Azure i jakie niektóre opcje są.
services: cloud-services
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: adegeo
ms.openlocfilehash: f3a3a1beb8540ee8ab0502379396c06ea505fb44
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2018
ms.locfileid: "29149910"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Wprowadzenie do monitorowania usługi w chmurze

Można monitorować kluczowe metryki wydajności dla usługi w chmurze. Minimalna ilość danych zbiera co roli usługi w chmurze: użycia Procesora, wykorzystanie sieci i wykorzystania dysku. Jeśli usługa w chmurze ma `Microsoft.Azure.Diagnostics` rozszerzenia stosowane do roli, tej roli mogą zbierać dodatkowe punktów danych. Ten artykuł zawiera wprowadzenie do diagnostyki Azure dla usługi w chmurze.

Podstawowe monitorowanie danych licznika wydajności z wystąpień roli jest próbkowany i zbierane co 3 minut. To podstawowe dane monitorowania nie są przechowywane na koncie magazynu i bez dodatkowych kosztów skojarzone z nim.

Zaawansowane monitorowanie dodatkowe metryki są pobierane i pobierane częstotliwością wynoszącą 5 minut, godzinę i 12 godzin. Zagregowane dane są przechowywane na koncie magazynu, w tabelach i są przeczyszczane po 10 dni. Skonfigurowano konto magazynu używane przez rolę; można użyć różnych kont magazynu dla różnych ról. Te ustawienia zostaną skonfigurowane z użyciem parametrów połączenia w [csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) i [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) plików.


## <a name="basic-monitoring"></a>Podstawowe monitorowanie

Zgodnie z wprowadzeniem, usługa w chmurze automatycznie zbiera podstawowe dane monitorowania z hosta maszyny wirtualnej. Te dane obejmują: procent procesora CPU, we/wy sieci i dysku odczytu/zapisu. Zebrane dane monitorowania jest automatycznie wyświetlana strony Przegląd i metryki usługi w chmurze, w portalu Azure. 

Podstawowe monitorowanie nie wymagają konta magazynu. 

![usługi w chmurze podstawowe monitorowanie kafelków](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Zaawansowanego monitorowania polega na użyciu **diagnostyki Azure** rozszerzenia (i opcjonalnie zestaw SDK usługi Application Insights) dla danej roli, który chcesz monitorować. Rozszerzenie diagnostyki używa pliku konfiguracyjnego (dla każdej roli) o nazwie **diagnostics.wadcfgx** konfigurowania metryki diagnostyki monitorowane. Rozszerzenia Azure diagnostycznych zbiera i przechowuje dane na koncie magazynu Azure. Te ustawienia są konfigurowane w **.wadcfgx**, [csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), i [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) plików. Oznacza to, że istnieje dodatkowy koszt związany z zaawansowanego monitorowania.

Przy tworzeniu każdej roli programu Visual Studio dodaje rozszerzenia diagnostyki Azure do niej. To rozszerzenie diagnostyki zebrać następujące informacje:

* Niestandardowe liczniki wydajności
* Dzienniki aplikacji
* Dzienniki zdarzeń systemu Windows
* Źródło zdarzenia platformy .NET
* Dzienniki usług IIS
* Manifest na podstawie ETW
* Zrzuty awaryjne
* Dzienniki błędów klienta

> [!IMPORTANT]
> Podczas tych danych jest agregowana do konta magazynu, portalu jest **nie** umożliwiają natywnych danych wykresu. Zdecydowanie zaleca się integrowanie innej usługi, takie jak usługi Application Insights do aplikacji.

## <a name="setup-diagnostics-extension"></a>Ustawienia diagnostyki rozszerzenia

Pierwsza strona, jeśli nie masz **klasycznego** konta magazynu [utworzyć](../storage/common/storage-create-storage-account.md#create-a-storage-account). Upewnij się, że utworzono konto magazynu z **klasycznego modelu wdrożenia** określony.

Następnie przejdź do **konta magazynu (klasyczne)** zasobów. Wybierz **ustawienia** > **klucze dostępu** i skopiuj **parametry połączenia podstawowej** wartość. Ta wartość jest wymagane dla usługi w chmurze. 

Istnieją dwa pliki konfiguracji, należy zmienić zaawansowane Diagnostics uaktywnioną, **ServiceDefinition.csdef** i **pliku ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

W **ServiceDefinition.csdef** plików, Dodaj nowe ustawienie o nazwie `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` dla każdej roli, która używa zaawansowane diagnostyki. Podczas tworzenia nowego projektu programu Visual Studio dodaje do tej wartości do pliku. W przypadku, gdy jest on niedostępny, można dodać go teraz. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Definiuje to nowe ustawienie, które muszą zostać dodane do każdej **pliku ServiceConfiguration.cscfg** pliku. 

Prawdopodobnie użytkownik ma dwa **.cscfg** pliki jedną o nazwie **ServiceConfiguration.cloud.cscfg** wdrażania platformy Azure i jedną o nazwie **ServiceConfiguration.local.cscfg** używany w przypadku wdrożeń lokalnych w środowisku emulowanej. Otwórz i zmień każdego **.cscfg** pliku. Dodaj ustawienie o nazwie `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Ustaw wartość na **parametry połączenia podstawowej** konta magazynu klasycznego. Jeśli chcesz użyć lokalnego magazynu na komputerze deweloperskim, należy użyć `UseDevelopmentStorage=true`.

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

## <a name="use-application-insights"></a>Użyj usługi Application Insights

Po opublikowaniu usługi w chmurze w programie Visual Studio podano opcję Wyślij dane diagnostyczne do usługi Application Insights. Można utworzyć zasobów aplikacji Azure szczegółowych informacji w tym czasie lub wysyłać dane do istniejącego zasobu platformy Azure. Usługi w chmurze mogą być monitorowane przez usługę Application Insights dostępność, wydajność, błędy i użycia. Niestandardowe wykresy mogą być dodawane do usługi Application Insights, dzięki czemu dane można wyświetlić który ma najbardziej znaczenie. Mogą być zbierane dane wystąpienia roli przy użyciu zestawu SDK usługi Application Insights w projekcie usługi w chmurze. Aby uzyskać więcej informacji na temat integracji usługi Application Insights, zobacz [usługi Application Insights z usługami w chmurze](../application-insights/app-insights-cloudservices.md).

Należy pamiętać, że podczas korzystania z usługi Application Insights, aby wyświetlić liczniki wydajności (i inne ustawienia) został określony za pomocą rozszerzenia diagnostyki Windows Azure, możesz tylko Pobierz więcej możliwości dzięki integracji zestawu SDK usługi Application Insights do użytkownika role sieci web i proces roboczy.


## <a name="next-steps"></a>Kolejne kroki

- [Więcej informacji na temat usługi Application Insights z usługami w chmurze](../application-insights/app-insights-cloudservices.md)
- [Ustawianie liczników wydajności](diagnostics-performance-counters.md)


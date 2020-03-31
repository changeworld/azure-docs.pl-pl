---
title: Monitorowanie usługi w chmurze platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, co obejmuje monitorowanie usługi Azure Cloud Service i jakie są niektóre opcje.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: tagore
ms.openlocfilehash: 61c794ba03934ae1828ba310f3f776bfb61b652b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273100"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Wprowadzenie do monitorowania usług w chmurze

Możesz monitorować kluczowe metryki wydajności dla dowolnej usługi w chmurze. Każda rola usługi w chmurze zbiera minimalne dane: użycie procesora CPU, użycie sieci i wykorzystanie dysku. Jeśli usługa w `Microsoft.Azure.Diagnostics` chmurze ma rozszerzenie zastosowane do roli, ta rola może zbierać dodatkowe punkty danych. Ten artykuł zawiera wprowadzenie do diagnostyki platformy Azure dla usług w chmurze.

Dzięki podstawowemu monitorowaniu dane licznika wydajności z wystąpień roli są próbkowanie i zbierane w odstępach 3-minutowych. Te podstawowe dane monitorowania nie są przechowywane na koncie magazynu i nie wiążą się z nimi żadnych dodatkowych kosztów.

Dzięki zaawansowanemu monitorowaniu dodatkowe dane są pobierane i zbierane w odstępach 5 minut, 1 godziny i 12 godzin. Zagregowane dane są przechowywane na koncie magazynu, w tabelach i są czyszczone po 10 dniach. Konto magazynu używane jest skonfigurowane według roli; można użyć różnych kont magazynu dla różnych ról. Jest to skonfigurowane z ciągiem połączenia w plikach [csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) i [cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)


## <a name="basic-monitoring"></a>Monitorowanie podstawowe

Jak podano we wstępie, usługa w chmurze automatycznie zbiera podstawowe dane monitorowania z maszyny wirtualnej hosta. Dane te obejmują procent procesora CPU, wyjm/wyjście sieci oraz odczyt/zapis dysku. Zebrane dane monitorowania są automatycznie wyświetlane na stronach przeglądu i metryki usługi w chmurze w witrynie Azure portal. 

Podstawowe monitorowanie nie wymaga konta magazynu. 

![kafelki monitorowania podstawowych usług w chmurze](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Zaawansowane monitorowanie obejmuje przy użyciu rozszerzenia **diagnostyki platformy Azure** (i opcjonalnie zestaw SDK usługi Application Insights) na roli, którą chcesz monitorować. Rozszerzenie diagnostyki używa pliku konfiguracyjnego (na rolę) o nazwie **diagnostics.wadcfgx** do skonfigurowania monitorowanych metryk diagnostyki. Rozszerzenie diagnostyki platformy Azure zbiera i przechowuje dane na koncie usługi Azure Storage. Te ustawienia są konfigurowane w plikach **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)i [cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg) Oznacza to, że istnieje dodatkowy koszt związany z zaawansowanym monitorowaniem.

Jak każda rola jest tworzona, Visual Studio dodaje rozszerzenie diagnostyki platformy Azure do niego. To rozszerzenie diagnostyki może zbierać następujące typy informacji:

* Niestandardowe liczniki wydajności
* Dzienniki aplikacji
* Dzienniki zdarzeń systemu Windows
* Źródło zdarzenia .NET
* Dzienniki usług IIS
* Śledzenie zdarzeń systemu Windows w oparciu o manifest
* Zrzuty awaryjne
* Dzienniki błędów klienta

> [!IMPORTANT]
> Podczas gdy wszystkie te dane są agregowane do konta magazynu, portal **nie** zapewnia natywnego sposobu wykresu danych. Zdecydowanie zaleca się zintegrowanie innej usługi, takiej jak usługa Application Insights, z aplikacją.

## <a name="setup-diagnostics-extension"></a>Rozszerzenie diagnostyki konfiguracji

Po pierwsze, jeśli nie masz **klasycznego** konta magazynu, [utwórz jedno](../storage/common/storage-account-create.md). Upewnij się, że konto magazynu jest tworzone przy określonym **modelu wdrażania klasycznego.**

Następnie przejdź do zasobu **konta magazynu (klasycznego).** Wybierz **pozycję Ustawienia** > **klawiszy dostępu** i skopiuj wartość **podstawowego ciągu połączenia.** Ta wartość jest potrzebna dla usługi w chmurze. 

Istnieją dwa pliki konfiguracyjne, które należy zmienić, aby można było włączyć zaawansowaną diagnostykę, **ServiceDefinition.csdef** i **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>UsługaDefinicja.csdef

W pliku **ServiceDefinition.csdef** dodaj nowe `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` ustawienie o nazwie dla każdej roli, która używa zaawansowanej diagnostyki. Visual Studio dodaje tę wartość do pliku podczas tworzenia nowego projektu. W przypadku, gdy go brakuje, możesz dodać go teraz. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Definiuje to nowe ustawienie, które musi zostać dodane do każdego pliku **ServiceConfiguration.cscfg.** 

Najprawdopodobniej masz dwa pliki **cscfg,** jeden o nazwie **ServiceConfiguration.cloud.cscfg** do wdrożenia na platformie Azure i jeden o nazwie **ServiceConfiguration.local.cscfg,** który jest używany do wdrożeń lokalnych w środowisku emulowanym. Otwórz i zmień każdy plik **cscfg.** Dodaj ustawienie `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`o nazwie . Ustaw wartość ciągu **połączenia podstawowego** klasycznego konta magazynu. Jeśli chcesz użyć magazynu lokalnego na komputerze `UseDevelopmentStorage=true`deweloperskim, użyj programu .

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

Podczas publikowania usługi w chmurze z programu Visual Studio, masz możliwość wysyłania danych diagnostycznych do usługi Application Insights. Można utworzyć zasób platformy Azure usługi Application Insights w tym czasie lub wysłać dane do istniejącego zasobu platformy Azure. Usługa w chmurze może być monitorowana przez usługę Application Insights pod kątem dostępności, wydajności, awarii i użycia. Wykresy niestandardowe można dodać do usługi Application Insights, dzięki czemu można zobaczyć dane, które mają największe znaczenie. Dane wystąpienia roli mogą być zbierane przy użyciu SDK usługi Application Insights w projekcie usługi w chmurze. Aby uzyskać więcej informacji na temat integrowania usługi Application Insights, zobacz [Usługa Application Insights z usługami w chmurze.](../azure-monitor/app/cloudservices.md)

Należy zauważyć, że chociaż można używać usługi Application Insights do wyświetlania liczników wydajności (i innych ustawień) określonych za pośrednictwem rozszerzenia Diagnostyka systemu Windows Azure, otrzymasz tylko bogatsze środowisko, integrując zestaw SDK usługi Application Insights z pracowników i stron internetowych.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o usłudze Application Insights za pomocą usług w chmurze](../azure-monitor/app/cloudservices.md)
- [Konfigurowanie liczników wydajności](diagnostics-performance-counters.md)





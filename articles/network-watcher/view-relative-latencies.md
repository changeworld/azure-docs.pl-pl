---
title: Wyświetl względną opóźnienia, aby regiony platformy Azure z określonych lokalizacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak widok względną opóźnienia między internetowi do regiony platformy Azure z określonych lokalizacji.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2018
ms.locfileid: "27600957"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Widok względny czas opóźnienia regiony platformy Azure z określonych lokalizacji

W tym samouczku, jak przy użyciu platformy Azure [obserwatora sieciowego](network-watcher-monitoring-overview.md) usługi, aby określić, jakie region platformy Azure w przypadku wdrażania aplikacji lub usług w, na podstawie z informacjami demograficznymi użytkownika. Ponadto można go użyć służą do oceniania połączenia dostawcy usług Azure.  
        
## <a name="create-a-network-watcher"></a>Utwórz obserwatora sieciowego

Jeśli masz już obserwatora sieciowego w systemie Azure co najmniej jeden [region](https://azure.microsoft.com/regions), możesz pominąć zadania w tej sekcji. Utwórz grupę zasobów dla obserwatora sieciowego. W tym przykładzie grupa zasobów nie zostanie utworzona w regionie wschodnie stany USA, ale można utworzyć grupy zasobów w dowolnym regionie Azure.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Utwórz obserwatora sieciowego. Musi mieć obserwatora sieciowego, utworzony w co najmniej jeden region platformy Azure. W tym przykładzie obserwatora sieciowego jest tworzony w regionie wschodnie nam Azure.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porównanie opóźnienia sieciowe względną do jednego regionu Azure z określonej lokalizacji

Ocenę dostawcy usług lub Rozwiązywanie problemów z użytkownikiem raportowania problemu, takie jak "lokacji jest powolne," z określonej lokalizacji do regionu azure, w którym zostanie wdrożona usługa. Na przykład poniższe polecenie zwraca opóźnienia średni dostawcy usługi względna Internet między stanu Waszyngton w Stanach Zjednoczonych i regionu zachodnie nam 2 Azure od grudnia 13-15, 2017:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Region, które określisz w poprzednim poleceniu nie musi być taka sama jak regionie, w którym określono po pobraniu obserwatora sieciowego. Poprzednie polecenie po prostu wymaga określenia istniejących obserwatora sieciowego. Obserwatora sieciowego można w dowolnym regionie. Jeśli można określić wartości `-Country` i `-State`, musi być prawidłowy. Wartości jest rozróżniana wielkość liter. Dane są dostępne dla ograniczonej liczby krajów, Stany i miast. Uruchom polecenia w [wyświetlić dostępne krajach, Stany miejscowości i dostawców](#view-available) Aby wyświetlić listę dostępnych krajów, miast i stanów do użycia przy użyciu poprzedniego polecenia. 

> [!WARNING]
> Należy określić datę wypadającą po 14 listopada 2017 dla `-StartTime` i `-EndTime`. Określenie daty przed 14 listopada 2017 zwraca żadnych danych. 

Następujące dane wyjściowe z poprzedniego polecenia:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

W wynikach zwrócona wartość **wynik** jest względny czas oczekiwania w różnych regionach i dostawców. Wynik 1 jest najgorszy opóźnienie (najwyższy), 100 jest najniższym opóźnieniu. Względne opóźnienia są uśredniane na dzień. W poprzednim przykładzie gdy ma on Wyczyść, który opóźnienia są takie same zarówno dni i że istnieje niewielkie różnice między czasem oczekiwania wynoszącym dwóch dostawców, ma także wyczyścić który opóźnienia zarówno dostawców brakuje skali 1-100. Gdy jest to zachowanie normalne, ponieważ stanu Waszyngton w Stanach Zjednoczonych fizycznie znajduje się w pobliżu regionu zachodnie nam 2 Azure, czasami wyników nie są zgodnie z oczekiwaniami. Określ większy zakres dat, tym bardziej Średni czas oczekiwania w czasie.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porównanie opóźnienia sieciowe względne w różnych regionach platformy Azure z określonej lokalizacji

Jeśli zamiast określania względnych opóźnienia między określonej lokalizacji i przy użyciu określonego regionu Azure `-Location`, aby określić względną opóźnienia wszystkie regiony platformy Azure z określonej lokalizacji fizycznej, możesz to zrobić za. Na przykład następujące polecenie pomoże Ci ocenić, jakie region platformy azure, aby wdrożyć usługę w, jeśli użytkownicy głównej Comcast użytkowników znajdujących się w stanie Waszyngton:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
W odróżnieniu od po określeniu jednej lokalizacji, jeśli nie Określ lokalizację, lub określ wiele lokalizacji, takie jak "US2 zachodnie", "Zachodnie nas", należy określić usługodawcy internetowego podczas uruchamiania polecenia. 

## <a name="view-available"></a>Wyświetl dostępne krajach, Stany miejscowości i dostawców

Dane są dostępne dla określonego dostawcy usług internetowych, krajach stanów i miast. Aby wyświetlić listę wszystkich dostępnych usługodawców internetowych, krajach, Stany i miasta, które mogą wyświetlać dane, wprowadź następujące polecenie:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Danych jest dostępna tylko dla innych krajów, Stany i miasta zwrócony przez poprzednie polecenie. Poprzednie polecenie należy określić istniejącą obserwatora sieciowego. Przykład określony *NetworkWatcher_eastus* obserwatora sieciowego w grupie zasobów o nazwie *NetworkWatcherRG*, ale można określić żadnych istniejących obserwatora sieciowego. Jeśli nie masz istniejących obserwatora sieciowego, utwórz ją za wykonywanie zadań w [utworzyć obserwatora sieciowego](#create-a-network-watcher). 

Po uruchomieniu poprzedniego polecenia, można filtrować dane wyjściowe zwracane przez określenie prawidłowymi wartościami dla **kraju**, **stanu**, i **miasta**, jeśli to konieczne.  Na przykład aby wyświetlić listę usługodawców internetowych dostępne w Seattle, Washington, na terenie Stanów Zjednoczonych, wprowadź następujące polecenie:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Wartość określona dla **kraju** musi być wielkich i małych. Wartości podane **stanu** i **miasta** muszą być małymi literami. Wartości muszą znajdować się dane wyjściowe zwracane po uruchomieniu polecenia bez wartości dla **kraju**, **stanu**, i **miasta**. Jeśli Określ nieprawidłowej wielkości liter, lub określ wartość **kraju**, **stanu**, lub **miasta** nie jest w dane wyjściowe zwracane po uruchomieniu polecenia bez wartości dla nich właściwości, dane wyjściowe jest pusta.

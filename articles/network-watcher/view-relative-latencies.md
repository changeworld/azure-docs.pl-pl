---
title: Wyświetlanie względnej opóźnienia do regionów platformy Azure z określonych lokalizacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić względne wartości opóźnienia między dostawców Internetu w regionach platformy Azure z określonych lokalizacji.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: b4a50657442422786f49c931aa6c2610d49846b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939874"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Wyświetl względnego opóźnienia do regionów platformy Azure z określonych lokalizacji

W tym samouczku, Dowiedz się, jak używać platformy Azure [usługi Network Watcher](network-watcher-monitoring-overview.md) service, aby pomóc zdecydować, jakie regionie platformy Azure do wdrażania aplikacji lub usługi, oparte na użytkownika demograficznych. Ponadto służy do oceniania połączenia dostawcy usług z platformą Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Tworzenie usługi network watcher

Jeśli masz już usługę network watcher na platformie Azure co najmniej jeden [region](https://azure.microsoft.com/regions), można pominąć zadania przedstawione w tej sekcji. Utwórz grupę zasobów dla usługi network watcher. W tym przykładzie grupa zasobów zostanie utworzona w regionie wschodnie stany USA, ale można utworzyć grupę zasobów, w dowolnym regionie systemu Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Tworzenie usługi network watcher. Konieczne jest posiadanie usługi network watcher utworzone w co najmniej jednego regionu platformy Azure. W tym przykładzie usługi network watcher zostało utworzone w regionie wschodnie stany USA Azure.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porównaj opóźnienia sieciowe względnej z pojedynczym regionem platformy Azure z określonej lokalizacji

Ocenę dostawców usług lub Rozwiązywanie problemów z użytkownikiem zgłoszenie problemu, takie jak "witryna była powoli," z określonej lokalizacji do regionu platformy azure, w którym wdrożono usługę. Na przykład poniższe polecenie zwraca średnie opóźnienia dostawcy względne usługi Internet między stanu Waszyngton w Stanach Zjednoczonych i regionu zachodnie stany USA 2 platformy Azure między 13 15 grudnia 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Region, który określisz w poprzednim poleceniu nie musi być taki sam jak region, który określiłeś, podczas pobierania usługi network watcher. Poprzednie polecenie wymaga po prostu, że podajesz istniejącej usługi network watcher. Usługa network watcher można w dowolnym regionie. W przypadku określenia wartości `-Country` i `-State`, musi być prawidłowy. Wartości jest rozróżniana wielkość liter. Dane są dostępne dla ograniczonej liczby krajów/regionów, regionów i miast. Uruchom polecenia w [wyświetlić dostępne krajów/regionów, Stany, miasta i dostawców](#view-available) Aby wyświetlić listę dostępnych krajów/regionów, miast i stanów do użycia z poprzedniego polecenia. 

> [!WARNING]
> Należy określić datę w ciągu ostatnich 30 dni dla `-StartTime` i `-EndTime`. Określanie w ubiegłym spowoduje żadne dane nie zostały zwrócone.

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

W wynikach zwrócona wartość **wynik** jest względnego opóźnienia w różnych regionach i dostawców. Wynik 1 jest najgorszy opóźnienia (najwyższy), 100 jest najmniejszego opóźnienia. Względne wartości opóźnienia są uśredniane dla danego dnia. W poprzednim przykładzie podczas gdy ma ona jasno wynika, opóźnienia są takie same dni, a czy istnieje niewielkie różnice między czasem oczekiwania wynoszącym dwóch dostawców, ma także wyczyścić, opóźnienia dla obu dostawców niskie na skali 1-100. Gdy jest to oczekiwane, ponieważ stanu Waszyngton w Stanach Zjednoczonych fizycznie zbliża się w regionie zachodnie stany USA 2 platformy Azure, czasami wyniki są zgodnie z oczekiwaniami. Im większy zakres dat, które można wybrać, tym bardziej Średni czas oczekiwania, wraz z upływem czasu.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porównaj opóźnienia sieciowe względne regionami platformy Azure z określonej lokalizacji

Jeśli zamiast określania względnych opóźnienia między określonej lokalizacji i określonego regionu platformy Azure przy użyciu `-Location`chcesz wyznaczyć opóźnienia względną do wszystkich regionów platformy Azure z określonej lokalizacji fizycznej, możesz to zrobić za. Na przykład następujące polecenie pomoże Ci ocenić jakie regionie platformy azure, aby wdrożyć usługę w, jeśli użytkownicy głównej znajdują się użytkownicy Comcast, położony w stanie Waszyngton:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> W odróżnieniu od po określeniu jednej lokalizacji, jeśli nie Określ lokalizację lub określ wiele lokalizacji, na przykład "Zachodnie stany USA 2", "Zachodnie stany USA", należy określić usługodawcy internetowego przy uruchamianiu polecenia. 

## <a name="view-available"></a>Wyświetl dostępne krajów/regionów, Stany, miasta i dostawców

Dane są dostępne dla określonego dostawcy usług internetowych, kraje/regiony, regionów i miast. Aby wyświetlić listę wszystkich internetowych dostępnych dostawców usług, kraje/regiony, stanów i miasta, które można wyświetlać dane, wprowadź następujące polecenie:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data jest dostępna tylko dla krajów/regionów, stanów i zwrócony przez poprzednie polecenie miast. Poprzednie polecenie wymaga określenia istniejącej usługi network watcher. Przykład określony *NetworkWatcher_eastus* usługi network watcher w grupie zasobów o nazwie *NetworkWatcherRG*, ale można określić wszelkie istniejące usługi network watcher. Jeśli nie masz istniejącej usługi network watcher, utwórz ją, wykonywania zadań w [utworzyć usługę network watcher](#create-a-network-watcher). 

Po uruchomieniu w poprzednim poleceniu, można filtrować dane wyjściowe zwracane, określając prawidłowe wartości dla **kraju**, **stanu**, i **Miasto**, jeśli to konieczne.  Na przykład aby wyświetlić listę usługodawców internetowych dostępnych w Seattle, Waszyngton w Stanach Zjednoczonych, wprowadź następujące polecenie:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Wartość określona dla **kraju** musi być wielkich i małych. Wartości określone dla **stanu** i **Miasto** muszą być małymi literami. Wartości muszą znajdować się dane wyjściowe zwracane po uruchomieniu polecenia bez wartości dla **kraju**, **stanu**, i **Miasto**. Jeśli Określanie nieprawidłowej wielkości liter lub określić wartość dla **kraju**, **stanu**, lub **Miasto** który nie znajduje się w dane wyjściowe zwracane po uruchomieniu polecenia bez wartości dla tych właściwości, dane wyjściowe jest pusty.

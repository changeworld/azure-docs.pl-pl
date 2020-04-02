---
title: Wyświetlanie względnych opóźnień w regionach platformy Azure z określonych lokalizacji
description: Dowiedz się, jak wyświetlać względne opóźnienia między dostawcami internetu w regionach platformy Azure z określonych lokalizacji.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 39f81731f20566d1a39f3f0931ff52c4e8b43ec0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521392"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Wyświetlanie względnego opóźnienia do regionów platformy Azure z określonych lokalizacji

> [!WARNING]
> Ta funkcja jest obecnie w wersji zapoznawczej i nadal jest testowana pod kątem stabilności.

W tym samouczku dowiesz się, jak korzystać z usługi Azure [Network Watcher,](network-watcher-monitoring-overview.md) aby ułatwić ci podjęcie decyzji o regionie platformy Azure, w jakim można wdrożyć aplikację lub usługę na podstawie danych demograficznych użytkowników. Ponadto można go użyć, aby ułatwić ocenę połączeń dostawców usług z platformą Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Tworzenie obserwatora sieciowego

Jeśli masz już obserwatora sieci w co najmniej jednym [regionie](https://azure.microsoft.com/regions)platformy Azure, możesz pominąć zadania w tej sekcji. Utwórz grupę zasobów dla obserwatora sieci. W tym przykładzie grupa zasobów jest tworzona w regionie Wschodnie stany USA, ale można utworzyć grupę zasobów w dowolnym regionie platformy Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Utwórz obserwatora sieciowego. Musisz mieć obserwatora sieci utworzonego w co najmniej jednym regionie platformy Azure. W tym przykładzie obserwator sieci jest tworzony w regionie Platformy Azure wschodnich stanów USA.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Porównywanie względnych opóźnień sieciowych z pojedynczym regionem platformy Azure z określonej lokalizacji

Oceń dostawców usług lub rozwiąż problem z użytkownikiem zgłaszającym problem, taki jak "witryna była powolna", z określonej lokalizacji do regionu platformy Azure, w którym wdrożono usługę. Na przykład następujące polecenie zwraca średnie względne opóźnienia dostawcy usług internetowych między stanem Waszyngton w Stanach Zjednoczonych i regionem Platformy Azure zachodnie stany USA 2 w okresie od 13 do 15 grudnia 2017 r.:

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
> Region określony w poprzednim poleceniu nie musi być taki sam jak region określony podczas pobierania obserwatora sieci. Poprzednie polecenie wymaga po prostu określenia istniejącego obserwatora sieci. Obserwator sieci może znajdować się w dowolnym regionie. Jeśli określisz `-Country` wartości `-State`dla i , muszą być prawidłowe. W wartościach rozróżniana jest wielkość liter. Dane są dostępne dla ograniczonej liczby krajów/regionów, stanów i miast. Uruchom polecenia w [widoku dostępnych krajów/regionów, stanów, miast i dostawców,](#view-available) aby wyświetlić listę dostępnych krajów/regionów, miast i stanów do użycia z poprzednim poleceniem. 

> [!WARNING]
> Należy określić datę w ciągu ostatnich `-StartTime` 30 dni dla i `-EndTime`. Określenie wcześniejszej daty spowoduje, że żadne dane nie zostaną zwrócone.

Dane wyjściowe z poprzedniego polecenia są następujące:

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

W zwróconych danych wyjściowych wartość **Score** jest względne opóźnienie między regionami i dostawcami. Wynik 1 jest najgorszym (najwyższym) opóźnieniem, podczas gdy 100 jest najniższym opóźnieniem. Względne opóźnienia są uśrednione dla dnia. W poprzednim przykładzie, podczas gdy jest jasne, że opóźnienia były takie same w obu dniach i że istnieje niewielka różnica między opóźnieniem dwóch dostawców, jest również jasne, że opóźnienia dla obu dostawców są niskie w skali 1-100. Chociaż jest to oczekiwane, ponieważ stan Waszyngton w Stanach Zjednoczonych jest fizycznie blisko regionu Platformy Azure w zachodnie stany USA 2, czasami wyniki nie są zgodne z oczekiwaniami. Im większy zakres dat określisz, tym więcej można średniego opóźnienia w czasie.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Porównywanie względnych opóźnień sieci w regionach platformy Azure z określonej lokalizacji

Jeśli zamiast określania względnych opóźnień między określonej lokalizacji i `-Location`określonego regionu platformy Azure przy użyciu , chcesz określić względne opóźnienia dla wszystkich regionów platformy Azure z określonej lokalizacji fizycznej, można to zrobić zbyt. Na przykład następujące polecenie pomaga ocenić, w jakim regionie platformy Azure wdrożyć usługę, jeśli użytkownicy podstawowi są użytkownikami comcast znajdującym się w stanie Waszyngton:

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
> W przeciwieństwie do określania pojedynczej lokalizacji, jeśli nie określisz lokalizacji lub określisz wiele lokalizacji, takich jak "Zachodnie stany USA2", "Zachodnie stany USA", należy określić dostawcę usług internetowych podczas uruchamiania polecenia. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Wyświetlanie dostępnych krajów/regionów, stanów, miast i dostawców

Dane są dostępne dla określonych dostawców usług internetowych, krajów/regionów, stanów i miast. Aby wyświetlić listę wszystkich dostępnych dostawców usług internetowych, krajów/regionów, stanów i miast, dla których można wyświetlać dane, wprowadź następujące polecenie:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Dane są dostępne tylko dla krajów/regionów, stanów i miast zwróconych przez poprzednie polecenie. Poprzednie polecenie wymaga określenia istniejącego obserwatora sieci. W przykładzie określono *NetworkWatcher_eastus* obserwatora sieci w grupie zasobów o nazwie *NetworkWatcherRG*, ale można określić istniejącego obserwatora sieci. Jeśli nie masz już obserwatora sieci, utwórz go, wykonując zadania w [obszarze Tworzenie obserwatora sieci .](#create-a-network-watcher) 

Po uruchomieniu poprzedniego polecenia można filtrować zwrócone dane wyjściowe, określając w razie potrzeby prawidłowe wartości dla **kraju,** **stanu**i **miasta.**  Na przykład, aby wyświetlić listę dostawców usług internetowych dostępnych w Seattle w stanie Waszyngton w Stanach Zjednoczonych, wprowadź następujące polecenie:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Wartość określona dla **kraju** musi być wielka i mała litera. Wartości określone dla **stanu** i **miasta** muszą być małe litery. Wartości muszą być wymienione w danych wyjściowych zwróconych po uruchomieniu polecenia bez wartości dla **Kraju,** **Stanu**i **Miasta**. Jeśli określisz niepoprawną sprawę lub określisz wartość **kraju,** **stanu**lub **miasta,** które nie znajduje się w danych wyjściowych zwróconych po uruchomieniu polecenia bez wartości dla tych właściwości, zwrócone dane wyjściowe są puste.

---
title: Rozwiązywanie problemów z systemem Azure Blockchain Workbench
description: Jak rozwiązać problem z aplikacją Azure Blockchain Workbench Preview.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324296"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Rozwiązywanie problemów z systemem Azure Blockchain Workbench Preview

Skrypt programu PowerShell jest dostępny, aby pomóc w debugowaniu deweloperów lub obsłudze. Skrypt generuje podsumowanie i zbiera szczegółowe dzienniki do rozwiązywania problemów. Zebrane dzienniki obejmują:

* Sieć blockchain, taka jak Ethereum
* Mikrousługi blockchain workbench
* Application Insights
* Monitorowanie platformy Azure (dzienniki usługi Azure Monitor)

Za pomocą tych informacji można określić kolejne kroki i określić główną przyczynę problemów.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Rozwiązywanie skryptów

Skrypt rozwiązywania problemów programu PowerShell jest dostępny w usłudze GitHub. [Pobierz plik ZIP](https://github.com/Azure-Samples/blockchain/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Uruchamianie skryptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Uruchom `collectBlockchainWorkbenchTroubleshooting.ps1` skrypt, aby zebrać dzienniki i utworzyć plik ZIP zawierający folder informacji o rozwiązywaniu problemów. Przykład:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skrypt akceptuje następujące parametry:

| Parametr  | Opis | Wymagany |
|---------|---------|----|
| Subscriptionid | Identyfikator subskrypcji, aby utworzyć lub zlokalizować wszystkie zasoby. | Tak |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, w której wdrożono program Blockchain Workbench. | Tak |
| Historia wyjściowa | Ścieżka do utworzenia danych wyjściowych . zip. Jeśli nie zostanie określony, domyślnie bieżący katalog. | Nie |
| LookbackHours | Liczba godzin do użycia podczas ściągania danych telemetrycznych. Wartość domyślna to 24 godziny. Maksymalna wartość to 90 godzin | Nie |
| OmsSubscriptionId | Identyfikator subskrypcji, w którym są wdrażane dzienniki usługi Azure Monitor. Przekaż ten parametr tylko wtedy, gdy dzienniki usługi Azure Monitor dla sieci blockchain są wdrażane poza grupą zasobów Blockchain Workbench.| Nie |
| Grupa OmsResource |Grupa zasobów, w której wdrożono dzienniki usługi Azure Monitor. Przekaż ten parametr tylko wtedy, gdy dzienniki usługi Azure Monitor dla sieci blockchain są wdrażane poza grupą zasobów Blockchain Workbench.| Nie |
| Nazwa obszaru OmsWorkSpace | Nazwa obszaru roboczego usługi Log Analytics. Przekaż ten parametr tylko wtedy, gdy dzienniki usługi Azure Monitor dla sieci blockchain są wdrażane poza grupą zasobów Blockchain Workbench | Nie |

## <a name="what-is-collected"></a>Co jest zbierane?

Wyjściowy plik ZIP zawiera następującą strukturę folderów:

| Folder lub plik | Opis  |
|---------|---------|
| \Podsumowanie.txt | Podsumowanie systemu |
| \Metryki\blockchain | Metryki dotyczące łańcucha bloków |
| \Metryki\Stół roboczy | Metryki dotyczące warsztatu |
| \Szczegóły\Blockchain | Szczegółowe dzienniki dotyczące łańcucha bloków |
| \Szczegóły\Stół roboczy | Szczegółowe dzienniki dotyczące warsztatu |

Plik podsumowania zawiera migawkę ogólnego stanu aplikacji i kondycji aplikacji. Podsumowanie zawiera zalecane akcje, wyróżnia najważniejsze błędy i metadane dotyczące uruchamiania usług.

Folder **Metryki** zawiera metryki różnych składników systemu w czasie. Na przykład plik `\Details\Workbench\apiMetrics.txt` wyjściowy zawiera podsumowanie różnych kodów odpowiedzi i czasy odpowiedzi w całym okresie zbierania. Folder **Szczegóły** zawiera szczegółowe dzienniki rozwiązywania określonych problemów z workbench lub podstawową siecią łańcucha bloków. Na przykład `\Details\Workbench\Exceptions.csv` zawiera listę najnowszych wyjątków, które wystąpiły w systemie, co jest przydatne do rozwiązywania problemów z błędami z inteligentnych kontraktów lub interakcji z łańcucha bloków. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przewodnik po rozwiązywaniu problemów z usługą Azure Blockchain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)

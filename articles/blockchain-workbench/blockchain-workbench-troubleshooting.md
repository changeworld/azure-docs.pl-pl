---
title: Azure Blockchain Workbench Rozwiązywanie problemów
description: Jak rozwiązywać problemy z aplikacją Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 419ed6dc76101366e47ae94067f7b671a10c94e2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196338"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Azure Blockchain Workbench Rozwiązywanie problemów

Skrypt programu PowerShell jest dostępny do pomocniczą developer debugowania lub obsługuje. Skrypt generuje podsumowanie i zbiera szczegółowe dzienniki na potrzeby rozwiązywania problemów. Dzienniki zebranych obejmują:

* Blockchain sieci, takich jak Ethereum
* Mikrousług Blockchain Workbench
* Application Insights
* Azure monitorowania (OMS)

Aby określić następne czynności i ustalić główną przyczynę problemów, można użyć informacji. 

## <a name="troubleshooting-script"></a>Rozwiązywanie problemów z skryptu

PowerShell Rozwiązywanie problemów z skryptu jest dostępna w witrynie GitHub. [Pobierz plik ZIP](https://github.com/Azure-Samples/blockchain/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Uruchom skrypt
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Uruchom `collectBlockchainWorkbenchTroubleshooting.ps1` skryptu zbieranie dzienników i Utwórz plik ZIP zawierający folderu informacje dotyczące rozwiązywania problemów. Na przykład:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skrypt akceptuje następujące parametry:

| Parametr  | Opis | Wymagane |
|---------|---------|----|
| Identyfikator subskrypcji | Identyfikator subskrypcji do tworzenia i Znajdź wszystkie zasoby. | Yes |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, na którym wdrożono Blockchain Workbench. | Yes |
| OutputDirectory | Ścieżka do tworzenia danych wyjściowych. Plik ZIP. Jeśli nie zostanie określony, domyślnie przyjmowana jest do bieżącego katalogu. | Nie |
| LookbackHours | Liczba godzin do użycia podczas ściąganie danych telemetrycznych. Wartość domyślna to 24 godziny. Maksymalna wartość to 90 godzin | Nie |
| OmsSubscriptionId | Identyfikator subskrypcji, którym jest wdrażany OMS. Ten parametr należy przekazać tylko w przypadku wdrażania OMS sieci blockchain poza Blockchain Workbench grupy zasobów.| Nie |
| OmsResourceGroup |Grupy zasobów, w którym jest wdrażany OMS. Ten parametr należy przekazać tylko w przypadku wdrażania OMS sieci blockchain poza Blockchain Workbench grupy zasobów.| Nie |
| OmsWorkspaceName | Nazwa obszaru roboczego OMS. Ten parametr należy przekazać tylko, jeśli OMS sieci blockchain jest wdrożona poza Blockchain Workbench grupy zasobów | Nie |

## <a name="what-is-collected"></a>Jakie informacje są zbierane?

Wyjściowy plik ZIP zawiera następującą strukturę folderów:

| Folder lub plik | Opis  |
|---------|---------|
| \Summary.txt | Podsumowanie systemu |
| \Metrics\blockchain | Metryk dotyczących blockchain |
| \Metrics\Workbench | Metryk dotyczących workbench |
| \Details\Blockchain | Szczegółowe dzienniki o blockchain |
| \Details\Workbench | Szczegółowe dzienniki o workbench |

Plik podsumowania tworzy migawkę ogólny stan aplikacji i kondycji tej aplikacji. Podsumowanie proponują zalecane akcje, wyróżnia top błędów i metadane dotyczące uruchamiania usługi.

**Metryki** folder zawiera metryki różnych składników systemu wraz z upływem czasu. Na przykład plik wyjściowy `\Details\Workbench\apiMetrics.txt` zawiera podsumowanie kody odpowiedzi różnych i czasy odpowiedzi przez cały okres zbierania. **Szczegóły** folder zawiera szczegółowe dzienniki określonych problemów z podstawowej sieci blockchain lub Workbench. Na przykład `\Details\Workbench\Exceptions.csv` zawiera listę najnowszych wyjątki, które wystąpiły w systemie, w którym jest przydatne podczas rozwiązywania problemów błędy z kontraktami inteligentne lub interakcji z blockchain. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Architektura Blockchain Workbench Azure](blockchain-workbench-architecture.md)
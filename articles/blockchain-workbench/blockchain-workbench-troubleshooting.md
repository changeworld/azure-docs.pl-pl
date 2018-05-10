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
ms.openlocfilehash: df7fd71bee287a063e8d586de38522f3b71e14d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
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
| OutputDirectory | Ścieżka do tworzenia danych wyjściowych. Plik ZIP. Jeśli nie zostanie określony, domyślnie przyjmowana jest do bieżącego katalogu. | Nie
| OmsSubscriptionId | Identyfikator subskrypcji, którym jest wdrażany OMS. Ten parametr należy przekazać tylko w przypadku wdrażania OMS sieci blockchain poza Blockchain Workbench grupy zasobów.| Nie |
| OmsResourceGroup |Grupy zasobów, w którym jest wdrażany OMS. Ten parametr należy przekazać tylko w przypadku wdrażania OMS sieci blockchain poza Blockchain Workbench grupy zasobów.| Nie |
| OmsWorkspaceName | Nazwa obszaru roboczego OMS. Ten parametr należy przekazać tylko, jeśli OMS sieci blockchain jest wdrożona poza Blockchain Workbench grupy zasobów | Nie |

## <a name="what-is-collected"></a>Jakie informacje są zbierane?

Wyjściowy plik ZIP zawiera następującą strukturę folderów:

| Folder \ pliku | Opis  |
|---------|---------|
| \Summary.txt | Podsumowanie systemu |
| \metrics\blockchain | Metryk dotyczących blockchain |
| \metrics\workbench | Metryk dotyczących workbench |
| \details\blockchain | Szczegółowe dzienniki o blockchain |
| \details\workbench | Szczegółowe dzienniki o workbench |

Plik podsumowania tworzy migawkę ogólny stan aplikacji i kondycji tej aplikacji. Podsumowanie proponują zalecane akcje, wyróżnia top błędów i metadane dotyczące uruchamiania usługi.

## <a name="next-steps"></a>Kolejne kroki

* [Architektura Blockchain Workbench Azure](blockchain-workbench-architecture.md)
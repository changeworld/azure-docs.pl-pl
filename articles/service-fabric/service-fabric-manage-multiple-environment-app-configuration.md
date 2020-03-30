---
title: Zarządzanie aplikacjami w wielu środowiskach
description: Aplikacje sieci szkieletowej usług Azure mogą być uruchamiane w klastrach o rozmiarze od jednego komputera do tysięcy maszyn. W niektórych przypadkach należy skonfigurować aplikację inaczej dla tych różnych środowisk. W tym artykule opisano, jak zdefiniować różne parametry aplikacji dla środowiska.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196983"
---
# <a name="manage-applications-for-multiple-environments"></a>Zarządzanie aplikacjami dla wielu środowisk

Klastry sieci szkieletowej usług Azure umożliwiają tworzenie klastrów w dowolnym miejscu od jednego do wielu tysięcy komputerów. W większości przypadków należy wdrożyć aplikację w wielu konfiguracjach klastra: klastrze lokalnego rozwoju, klastrze deweloperów udostępnionych i klastrze produkcyjnym. Wszystkie te klastry są uważane za różne środowiska, w których kod musi zostać uruchomiony. Pliki binarne aplikacji można uruchomić bez modyfikacji w tym szerokim spektrum, ale często chcesz skonfigurować aplikację inaczej.

Rozważmy dwa proste przykłady:
  - usługa nasłuchuje na zdefiniowanym porcie, ale potrzebny jest, aby ten port był inny w różnych środowiskach
  - musisz podać różne poświadczenia powiązania dla bazy danych w środowiskach

## <a name="specifying-configuration"></a>Określanie konfiguracji

Konfigurację, którą udostępniasz, można podzielić na dwie kategorie:

- Konfiguracja, która ma zastosowanie do sposobu uruchamiania usług
  - Na przykład numer portu dla punktu końcowego lub liczba wystąpień usługi
  - Ta konfiguracja jest określona w pliku manifestu aplikacji lub usługi
- Konfiguracja, która ma zastosowanie do kodu aplikacji
  - Na przykład powiązanie informacji dla bazy danych
  - Ta konfiguracja może być dostarczana za pośrednictwem plików konfiguracyjnych lub zmiennych środowiskowych

> [!NOTE]
> Nie wszystkie atrybuty w parametrach obsługi pliku manifestu aplikacji i usługi.
> W takich przypadkach należy polegać na zastępowanie ciągów jako część przepływu pracy wdrażania. W usłudze Azure DevOps można użyć https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens rozszerzenia, takiego jak Zamień tokeny: lub w usłudze Jenkins można uruchomić zadanie skryptu, aby zastąpić wartości.
>

## <a name="specifying-parameters-during-application-creation"></a>Określanie parametrów podczas tworzenia aplikacji

Podczas tworzenia wystąpienia aplikacji o nazwie w sieci szkieletowej usług, masz możliwość przekazywania w parametrach. Sposób, w jaki to zrobić, zależy od sposobu tworzenia wystąpienia aplikacji.

  - W programie PowerShell polecenie [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet przyjmuje parametry aplikacji jako skrót.
  - Za pomocą sfctl, Polecenie [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) przyjmuje parametry jako ciąg JSON. Skrypt install.sh używa sfctl.
  - Program Visual Studio udostępnia zestaw plików parametrów w folderze Parametry w projekcie aplikacji. Te pliki parametrów są używane podczas publikowania z programu Visual Studio przy użyciu usług Azure DevOps services lub usługi Azure DevOps Server. W programie Visual Studio pliki parametrów są przekazywane do skryptu Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach pokazano, jak korzystać z niektórych pojęć opisanych tutaj:

- [Jak określić zmienne środowiskowe dla usług w sieci szkieletowej usług](service-fabric-how-to-specify-environment-variables.md)
- [Jak określić numer portu usługi przy użyciu parametrów w sieci szkieletowej usług](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Jak parametryzować pliki konfiguracyjne](service-fabric-how-to-parameterize-configuration-files.md)

- [Odwołanie do zmiennej środowiskowej](service-fabric-environment-variables-reference.md)

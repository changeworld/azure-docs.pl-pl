---
title: Zarządzanie aplikacjami w wielu środowiskach, w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Aplikacje usługi sieć szkieletowa usług Azure może działać w klastrach tego zakresu o rozmiarze z jednego komputera do maszyn. W niektórych przypadkach należy skonfigurować aplikację inaczej w przypadku tych środowisk zróżnicowane. W tym artykule opisano sposób definiowania parametry różnych aplikacji dla środowiska.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 15ad606578970290cef440ec4efdd967ca0c0b32
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205233"
---
# <a name="manage-applications-for-multiple-environments"></a>Zarządzanie aplikacjami dla wielu środowisk

Klastrów sieci szkieletowej usług Azure umożliwiają tworzenie klastrów za pomocą dowolnej lokalizacji z jednego do wielu tysięcy komputerów. W większości przypadków okaże się, że konieczności wdrażania aplikacji w konfiguracji klastra z wieloma: z lokalnego klastra projektowego klastra udostępnionego programowanie i klastra produkcyjnego. Wszystkie te klastry są traktowane jako kodu musi zostać uruchomione różnych środowisk. Pliki binarne aplikacji mogą działać bez żadnych modyfikacji w tym szerokie spektrum, ale często chcesz skonfigurować aplikację inaczej.

Należy wziąć pod uwagę dwa proste przykłady:
  - usługi nasłuchuje na porcie zdefiniowane, ale należy ten port różni się w środowiskach
  - Musisz podać poświadczenia inne powiązanie dla bazy danych w środowiskach

## <a name="specifying-configuration"></a>Określenie konfiguracji

Konfigurację, którą należy podać można podzielić na dwie kategorie:

- Konfigurację, która ma zastosowanie do działanie usług
  - Na przykład numer portu dla punktu końcowego lub liczbę wystąpień usługi
  - Ta konfiguracja jest określony w pliku manifestu usługi lub aplikacji
- Konfigurację, która ma zastosowanie do kodu aplikacji
  - Na przykład informacje o powiązaniu dla bazy danych
  - Tę konfigurację można podać przy użyciu plików konfiguracyjnych lub zmienne środowiskowe

> [!NOTE]
> Nie wszystkie atrybuty w aplikacji i usług manifestu parametry pliku pomocy technicznej.
> W takich przypadkach trzeba polegać na zastępowanie ciągi jako część wdrożenia przepływ pracy. W programie Visual Studio Team Services, można użyć rozszerzenia takich jak zastąpić tokenów: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens lub w Wpięć można uruchomić zadania skryptu, aby zastąpić wartości.
>

## <a name="specifying-parameters-during-application-creation"></a>Określanie parametrów podczas tworzenia aplikacji

Podczas tworzenia wystąpienia nazwanego aplikacji w sieci szkieletowej usług, istnieje możliwość do przekazania parametrów. Sposób odbywa się zależy od tego, jak utworzyć wystąpienie aplikacji.

  - W programie PowerShell [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) polecenia cmdlet przyjmuje parametry aplikacji jako tablica skrótów.
  - Przy użyciu sfctl, [ `sfctl application create` ](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) polecenie przyjmuje parametry jako ciągu JSON. Skrypt install.sh używa sfctl.
  - Visual Studio zapewnia zbiór parametrów pliki w folderze parametrów w projekcie aplikacji. Te pliki parametrów są używane podczas publikowania z programu Visual Studio za pomocą programu Visual Studio Team Service lub Team Foundation Server. W programie Visual Studio pliki parametrów są są przekazywane do skryptu Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Kolejne kroki
Następujące artykuły pokazują, jak korzystać z niektórych pojęcia opisane tutaj:

- [Sposób określania zmiennych środowiskowych dla usług w sieci szkieletowej usług](service-fabric-how-to-specify-environment-variables.md)
- [Jak określić numer portu usługi przy użyciu parametrów w sieci szkieletowej usług](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Jak parametryzacja pliki konfiguracji](service-fabric-how-to-parameterize-configuration-files.md)

- [Odwołanie do zmiennej środowiskowej](service-fabric-environment-variables-reference.md)

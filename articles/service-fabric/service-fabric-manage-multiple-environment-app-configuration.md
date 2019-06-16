---
title: Zarządzanie aplikacjami dla wielu środowisk w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Aplikacje usługi Azure Service Fabric może działać w klastrach tego zakresu rozmiarze z jednego komputera do tysięcy maszyn. W niektórych przypadkach należy skonfigurować aplikację w różny sposób dla tych środowisk zróżnicowane. W tym artykule opisano sposób definiowania parametrów inną aplikacją na środowisko.
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
ms.openlocfilehash: dac96ef6fce38a0557444e181fa6eccb649cfb9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719230"
---
# <a name="manage-applications-for-multiple-environments"></a>Zarządzanie aplikacjami dla wielu środowisk

Klastry usługi Service Fabric platformy Azure umożliwiają tworzenie klastrów za pomocą w dowolnym miejscu od jednej do tysięcy wiele maszyn. W większości przypadków okaże się konieczności wdrożenia aplikacji w konfiguracji klastra z wieloma: lokalnego klastra projektowego i klaster projektowy udostępnionego klastra produkcyjnego. Wszystkie klastry te są traktowane jako różnych środowiskach, w których Twój kod musi zostać uruchomione. Pliki binarne aplikacji można uruchomić bez modyfikacji w tym szerokie spektrum, ale często zachodzi potrzeba skonfigurować aplikację w różny sposób.

Należy wziąć pod uwagę dwa proste przykłady:
  - Usługa nasłuchuje na porcie zdefiniowanych, ale należy tego portu do różnić się między środowiskami
  - należy podać poświadczenia inne powiązanie dla bazy danych w środowiskach

## <a name="specifying-configuration"></a>Określanie konfiguracji

Konfiguracji, których udzielasz można podzielić na dwie kategorie:

- Konfigurację, która dotyczy działanie usługi
  - Na przykład numer portu dla punktu końcowego lub liczbę wystąpień usługi
  - Ta konfiguracja jest określona w pliku manifestu usługi lub aplikacji
- Konfiguracja, która ma zastosowanie do kodu aplikacji
  - Na przykład informacje o powiązaniu dla bazy danych
  - Tę konfigurację można podać za pomocą plików konfiguracji lub zmienne środowiskowe

> [!NOTE]
> Nie wszystkie atrybuty w aplikacji i usług manifestu parametry obsługi plików.
> W takich przypadkach trzeba polegać na zastępując ciągi jako część przepływu pracy wdrażania. W metodyce DevOps platformy Azure można użyć rozszerzeń, takich jak zamienić tokeny: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens lub w usłudze Jenkins można uruchomić zadania skryptu, aby zastąpić wartości.
>

## <a name="specifying-parameters-during-application-creation"></a>Określanie parametrów podczas tworzenia aplikacji

Podczas tworzenia wystąpienia nazwanego aplikacji w usłudze Service Fabric, masz możliwość przekazanie parametrów. Sposób to zrobić, zależy od tego, jak utworzyć wystąpienie aplikacji.

  - W programie PowerShell [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) polecenie cmdlet przyjmuje parametry aplikacji jako tablica skrótów.
  - Za pomocą interfejsu sfctl, [ `sfctl application create` ](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) polecenie pobiera parametry jako ciąg JSON. Skrypt install.sh używa interfejsu sfctl.
  - Visual Studio udostępnia zestaw pliki parametrów w folderze parametrów w projekcie aplikacji. Te pliki parametrów są używane podczas publikowania z programu Visual Studio przy użyciu usługi Azure DevOps Services lub serwera Team Foundation Server. W programie Visual Studio pliki parametrów są jest przekazywane do skryptu wdrażania FabricApplication.ps1.

## <a name="next-steps"></a>Kolejne kroki
Następujące artykuły pokazują, jak korzystać z niektórych pojęcia opisane w tym miejscu:

- [Sposobu określania zmiennych środowiskowych dla usług w usłudze Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Jak określić numer portu usługi Service Fabric przy użyciu parametrów](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Jak zdefiniować parametry plików konfiguracji](service-fabric-how-to-parameterize-configuration-files.md)

- [Odnośnik zmiennej środowiskowej](service-fabric-environment-variables-reference.md)

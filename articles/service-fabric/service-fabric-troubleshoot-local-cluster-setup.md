---
title: Rozwiązywanie problemów z lokalnych ustawień klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano zestaw sugestie dotyczące rozwiązywania problemów z lokalnego klastra projektowego
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 8bb32b2bded061bd19bcd7cfda4ef259a75b0626
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60864443"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Rozwiązywanie problemów z konfigurację klastra programowania lokalnego
Jeśli napotkasz problem podczas interakcji z Twojej lokalny klaster projektowy usługi Azure Service Fabric, należy przejrzeć poniższe sugestie dotyczące potencjalnych rozwiązań.

## <a name="cluster-setup-failures"></a>Błędów konfiguracji klastra
### <a name="cannot-clean-up-service-fabric-logs"></a>Nie można wyczyścić dzienniki usługi Service Fabric
#### <a name="problem"></a>Problem
Po uruchomieniu skryptu DevClusterSetup, zostanie wyświetlony następujący błąd:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Rozwiązanie
Zamknij bieżące okno programu PowerShell i Otwórz nowe okno programu PowerShell jako administrator. Teraz możesz pomyślnie uruchomić skrypt.

## <a name="cluster-connection-failures"></a>Błędy połączenia klastra

### <a name="type-initialization-exception"></a>Wyjątek inicjalizacji typu
#### <a name="problem"></a>Problem
Po nawiązaniu połączenia z klastrem w programie PowerShell zostanie wyświetlony błąd typeinitializationexception — dla System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Rozwiązanie
Zmiennej path nie została poprawnie ustawiona podczas instalacji. Wyloguj się z Windows, a następnie zaloguj się ponownie. Spowoduje to odświeżenie ścieżki.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Połączenie klastra kończy się niepowodzeniem z "Obiekt jest zamknięty"
#### <a name="problem"></a>Problem
Wywołanie Connect-ServiceFabricCluster kończy się niepowodzeniem z powodu błędu następująco:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Rozwiązanie
Zamknij bieżące okno programu PowerShell i Otwórz nowe okno programu PowerShell jako administrator.

### <a name="fabric-connection-denied-exception"></a>Wyjątek odmowa połączenia sieci szkieletowej
#### <a name="problem"></a>Problem
Podczas debugowania w programie Visual Studio, otrzymasz błąd FabricConnectionDeniedException.

#### <a name="solution"></a>Rozwiązanie
Ten błąd występuje przeważnie podczas próby uruchomienia procesu hosta usługi ręcznie.

Upewnij się, że nie masz żadnych projektów usługi ustawiony jako projekty startowe w rozwiązaniu. Tylko projekty aplikacji usługi Service Fabric, należy ustawić jako projekty startowe.

> [!TIP]
> Jeśli po zakończeniu instalacji, klaster lokalny rozpoczyna zachowywać się nieprawidłowo, można zresetować go przy użyciu aplikacji na pasku zadań Menedżera klastra lokalnego. Spowoduje to usunięcie istniejącego klastra i skonfigurować nowy. Należy pamiętać, że wszystkie wdrożone aplikacje i skojarzone dane zostaną usunięte.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie i rozwiązywanie problemów z klastrem przy użyciu raportów kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)


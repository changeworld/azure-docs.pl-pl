---
title: Rozwiązywanie problemów z lokalną konfiguracją klastra sieci szkieletowej usług Azure
description: W tym artykule omówiono zestaw sugestii dotyczących rozwiązywania problemów z lokalnym klastrem programistycznym
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465505"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Troubleshoot your local development cluster setup (Rozwiązywanie problemów z lokalną konfiguracją klastra programowania)
Jeśli napotkasz problem podczas interakcji z lokalnym klastrem deweloperów usługi Azure Service Fabric, przejrzyj następujące sugestie dotyczące potencjalnych rozwiązań.

## <a name="cluster-setup-failures"></a>Błędy konfiguracji klastra
### <a name="cannot-clean-up-service-fabric-logs"></a>Nie można wyczyścić dzienników sieci szkieletowej usług
#### <a name="problem"></a>Problem
Podczas uruchamiania skryptu DevClusterSetup pojawia się następujący błąd:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Rozwiązanie
Zamknij bieżące okno programu PowerShell i otwórz nowe okno programu PowerShell jako administrator. Teraz można pomyślnie uruchomić skrypt.

## <a name="cluster-connection-failures"></a>Błędy połączenia klastra

### <a name="type-initialization-exception"></a>Wpisz wyjątek inicjowania
#### <a name="problem"></a>Problem
Podczas nawiązywania połączenia z klastrem w programie PowerShell zostanie wyświetlony błąd TypeInitializationException dla system.fabric.Common.AppTrace.

#### <a name="solution"></a>Rozwiązanie
Zmienna ścieżki nie została poprawnie ustawiona podczas instalacji. Wyloguj się z systemu Windows i zaloguj się z powrotem. To odświeża ścieżkę.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Połączenie klastra kończy się niepowodzeniem z "Obiekt jest zamknięty"
#### <a name="problem"></a>Problem
Wywołanie Connect-ServiceFabricCluster kończy się niepowodzeniem z błędem w ten sposób:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Rozwiązanie
Zamknij bieżące okno programu PowerShell i otwórz nowe okno programu PowerShell jako administrator.

### <a name="fabric-connection-denied-exception"></a>Odmowa połączenia sieci szkieletowej wyjątek
#### <a name="problem"></a>Problem
Podczas debugowania z programu Visual Studio, otrzymasz FabricConnectionDeniedException błąd.

#### <a name="solution"></a>Rozwiązanie
Ten błąd zwykle występuje podczas próby ręcznego uruchomienia procesu hosta usługi.

Upewnij się, że nie masz żadnych projektów usług ustawionych jako projekty uruchamiania w rozwiązaniu. Tylko projekty aplikacji sieci szkieletowej usług powinny być ustawione jako projekty uruchamiania.

> [!TIP]
> Jeśli po zakończeniu instalacji klaster lokalny zacznie zachowywać się nieprawidłowo, można go zresetować za pomocą aplikacji zasobnika systemu lokalnego menedżera klastrów. Spowoduje to usunięcie istniejącego klastra i skonfigurowanie nowego. Należy zauważyć, że wszystkie wdrożone aplikacje i skojarzone dane są usuwane.
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Opis klastra i rozwiązywanie problemów z raportami kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)


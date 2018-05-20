---
title: Debugowanie aplikacji sieci szkieletowej usług Azure w programie Eclipse | Dokumentacja firmy Microsoft
description: Zwiększyć niezawodność i wydajność usługi poprzez opracowywania i debugowania ich w środowisku Eclipse na lokalny klaster projektowy.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 0e9e816fa84816b1b5d12f066dc65aee7b4930f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Debugowanie aplikacji Java sieci szkieletowej usług za pomocą programu Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Uruchom lokalny klaster projektowy, wykonując kroki opisane w [konfigurowania środowiska deweloperskiego sieci szkieletowej usług](service-fabric-get-started-linux.md).

2. Zaktualizuj entryPoint.sh usługi, którą chcesz debugować, tak aby był uruchamiany z parametrami zdalnego debugowania procesu java. Ten plik znajduje się w następującej lokalizacji: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. W tym przykładzie na potrzeby debugowania ustawiono port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Zaktualizuj Manifest aplikacji, ustawiając liczbę wystąpień lub liczby replik dla usługi, która jest debugowany 1. To ustawienie pozwala uniknąć konfliktów z portem używanym podczas debugowania. Na przykład w przypadku usług bezstanowych ustaw ``InstanceCount="1"``, a dla usług stanowych ustaw docelowy i minimalny rozmiar zestawu replik na 1 w następujący sposób: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Wdrażanie aplikacji.

5. W środowisku IDE programu Eclipse wybierz **Uruchom -> debugowanie konfiguracji -> zdalnego aplikacji Java i wprowadź właściwości połączenia** i ustaw właściwości w następujący sposób:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ustaw punkty przerwania w punktach żądaną i debugowania aplikacji.

Jeśli awarii aplikacji można również włączyć coredumps. Wykonanie ``ulimit -c`` w powłoce i zwraca 0, a następnie coredumps nie są włączone. Aby włączyć nieograniczone coredumps, uruchom następujące polecenie: ``ulimit -c unlimited``. Można również sprawdzić stan za pomocą polecenia ``ulimit -a``.  Jeśli chcesz zaktualizować ścieżkę generowania coredump wykonania ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Kolejne kroki

* [Zbieranie dzienników przy użyciu diagnostyki Azure Linux](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorowanie i diagnozowania usług lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

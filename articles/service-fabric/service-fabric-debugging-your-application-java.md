---
title: Debugowanie aplikacji usługi Azure Service Fabric w środowisku Eclipse | Dokumentacja firmy Microsoft
description: Zwiększ niezawodność i wydajność usługi, opracowywania i debugowania ich w środowisku Eclipse na lokalny klaster projektowy.
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
ms.openlocfilehash: 78483a5a5d78b539415aeeb0e28c1dbaf3680173
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38619344"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Debugowanie aplikacji Java usługi Service Fabric przy użyciu programu Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Java/w środowisku Eclipse](service-fabric-debugging-your-application-java.md)
> 

1. Uruchom lokalny klaster projektowy, wykonując kroki opisane w [Konfigurowanie środowiska deweloperskiego usługi Service Fabric](service-fabric-get-started-linux.md).

2. Zaktualizuj entryPoint.sh usługi, które chcesz debugować, tak aby był uruchamiany proces języka java z parametrami debugowania zdalnego. Ten plik znajduje się w następującej lokalizacji: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. W tym przykładzie na potrzeby debugowania ustawiono port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Zaktualizuj Manifest aplikacji, ustawiając liczbę wystąpień lub liczbę replik dla debugowanej usługi na 1. To ustawienie pozwala uniknąć konfliktów z portem używanym podczas debugowania. Na przykład w przypadku usług bezstanowych ustaw ``InstanceCount="1"``, a dla usług stanowych ustaw docelowy i minimalny rozmiar zestawu replik na 1 w następujący sposób: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Wdróż aplikację.

5. W środowisku IDE programu Eclipse wybierz **Uruchom -> Debuguj konfiguracje -> zdalna aplikacja Java i wprowadź właściwości połączenia** i ustaw właściwości w następujący sposób:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ustawianie punktów przerwania w punktach żądaną i debugowania aplikacji.

Jeśli aplikacja uległa awarii, mogą również chcesz włączyć coredumps. Wykonaj ``ulimit -c`` w powłoce i zwraca 0, a następnie coredumps nie są włączone. Aby włączyć coredumps bez ograniczeń, wykonaj następujące polecenie: ``ulimit -c unlimited``. Można również sprawdzić stan za pomocą polecenia ``ulimit -a``.  Jeśli chcesz zaktualizować ścieżkę generowania coredump wykonania ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Kolejne kroki

* [Zbieranie dzienników przy użyciu diagnostyki Azure Linux](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorować i diagnozować usługi lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

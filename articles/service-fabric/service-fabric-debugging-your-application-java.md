---
title: Debugowanie aplikacji usługi Azure Service Fabric w środowisku Eclipse | Dokumentacja firmy Microsoft
description: Zwiększ niezawodność i wydajność usługi, opracowywania i debugowania ich w środowisku Eclipse na lokalny klaster projektowy.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 2f00636da2b29e7815569a683fdf51c6a4e3b0e0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393592"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Debugowanie aplikacji Java usługi Service Fabric przy użyciu programu Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Uruchom lokalny klaster projektowy, wykonując kroki opisane w [Konfigurowanie środowiska deweloperskiego usługi Service Fabric](service-fabric-get-started-linux.md).

2. Zaktualizuj entryPoint.sh usługi, które chcesz debugować, tak aby był uruchamiany proces języka java z parametrami debugowania zdalnego. Ten plik znajduje się w następującej lokalizacji: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`. W tym przykładzie na potrzeby debugowania ustawiono port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Zaktualizuj Manifest aplikacji, ustawiając liczbę wystąpień lub liczbę replik dla debugowanej usługi na 1. To ustawienie pozwala uniknąć konfliktów z portem używanym podczas debugowania. Na przykład w przypadku usług bezstanowych ustaw `InstanceCount="1"`, a dla usług stanowych ustaw docelowy i minimalny rozmiar zestawu replik na 1 w następujący sposób: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Wdrażanie aplikacji.

5. W środowisku IDE programu Eclipse wybierz **Uruchom -> Debuguj konfiguracje -> zdalna aplikacja Java i wprowadź właściwości połączenia** i ustaw właściwości w następujący sposób:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ustawianie punktów przerwania w punktach żądaną i debugowania aplikacji.

Jeśli aplikacja uległa awarii, mogą również chcesz włączyć coredumps. Wykonaj `ulimit -c` w powłoce i zwraca 0, a następnie coredumps nie są włączone. Aby włączyć coredumps bez ograniczeń, wykonaj następujące polecenie: `ulimit -c unlimited`. Można również sprawdzić stan za pomocą polecenia `ulimit -a`.  Jeśli chcesz zaktualizować ścieżkę generowania coredump wykonania `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`. 

### <a name="next-steps"></a>Kolejne kroki

* [Zbieranie dzienników przy użyciu diagnostyki Azure Linux](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorować i diagnozować usługi lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

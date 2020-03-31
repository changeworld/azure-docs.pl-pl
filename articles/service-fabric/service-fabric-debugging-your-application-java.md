---
title: Debugowanie aplikacji w programie Eclipse
description: Zwiększ niezawodność i wydajność usług, opracowując i debugując je w programie Eclipse w lokalnym klastrze deweloperów.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614489"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Debugowanie aplikacji sieci szkieletowej usługi Java przy użyciu programu Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zaćmienie/Jawa](service-fabric-debugging-your-application-java.md)
> 

1. Uruchom klaster lokalnego rozwoju, wykonując kroki opisane w [ustawieniach konfigurowania środowiska programistycznego sieci szkieletowej usług](service-fabric-get-started-linux.md).

2. Zaktualizuj entryPoint.sh usługi, którą chcesz debugować, tak aby rozpoczynała proces java z parametrami zdalnego debugowania. Ten plik można znaleźć w `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`następującej lokalizacji: . W tym przykładzie na potrzeby debugowania ustawiono port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Zaktualizuj manifest aplikacji, ustawiając liczbę wystąpień lub liczbę replik dla usługi, która jest debugowana na 1. To ustawienie pozwala uniknąć konfliktów z portem używanym podczas debugowania. Na przykład w przypadku usług bezstanowych ustaw `InstanceCount="1"`, a dla usług stanowych ustaw docelowy i minimalny rozmiar zestawu replik na 1 w następujący sposób: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Wdrażanie aplikacji.

5. W środowisku Eclipse IDE wybierz opcję **Uruchom -> debugowanie -> zdalnej aplikacji Java i właściwości połączenia wejściowego** i ustaw właściwości w następujący sposób:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ustaw punkty przerwania w żądanych punktach i debugować aplikację.

Jeśli aplikacja ulega awarii, można również włączyć coredumps. Wykonaj `ulimit -c` w powłoce i jeśli zwraca 0, następnie coredumps nie są włączone. Aby włączyć nieograniczoną liczbę coredumps, wykonaj następujące polecenie: `ulimit -c unlimited`. Stan można również sprawdzić za `ulimit -a`pomocą polecenia .  Jeśli chcesz zaktualizować ścieżkę generowania coredump, wykonaj . `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern` 

### <a name="next-steps"></a>Następne kroki

* [Zbieraj dzienniki przy użyciu systemu Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitoruj i diagnozuj usługi lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

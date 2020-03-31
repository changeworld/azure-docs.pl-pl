---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67671199"
---
1. Zainstaluj dapl, rdmacm, ibverbs i mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. W /etc/waagent.conf włącz rdma, odkomentowywając następujące wiersze konfiguracji. Aby edytować ten plik, potrzebujesz dostępu administratora.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Dodaj lub zmień następujące ustawienia pamięci w KB w pliku /etc/security/limits.conf. Aby edytować ten plik, potrzebujesz dostępu administratora. Do celów testowych można ustawić memlock na nieograniczony. Na przykład: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Zainstaluj bibliotekę Intel MPI. Albo [zakup i pobrać](https://software.intel.com/intel-mpi-library/) bibliotekę z Intel lub pobrać [darmową wersję ewaluacjową](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Obsługiwane są tylko środowiska wykonawcze Intel MPI 5.x.
 
   Aby zapoznać się z instrukcjami instalacji, zobacz [Podręcznik instalacji biblioteki mpi firmy Intel](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Włącz ptrace dla procesów innych niż główny nie debuger (potrzebne dla najnowszych wersji intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```

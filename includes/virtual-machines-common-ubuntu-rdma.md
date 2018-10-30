---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 7f5b829399004a58ba84dc9abc34b2c9dd544fbf
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227305"
---
1. Zainstaluj dapl, rdmacm, ibverbs i mlx4

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. W ścieżce/etc/waagent.conf należy włączyć funkcję RDMA, uncommenting następujące wiersze konfiguracji. Potrzebny jest dostęp głównego, aby edytować ten plik.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Dodaj lub Zmień następujące ustawienia pamięci w KB w pliku /etc/security/limits.conf. Potrzebny jest dostęp głównego, aby edytować ten plik. Do celów testowych można ustawić memlock bez ograniczeń. Na przykład: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Zainstaluj Intel MPI biblioteki. Albo [kupić i pobrać](https://software.intel.com/intel-mpi-library/) biblioteki Intel lub pobierania [wersję bezpłatną wersją ewaluacyjną](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
  wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
  ```
 
 Obsługiwane są tylko runtimes 5.x Intel MPI.
 
 Aby uzyskać instrukcje instalacji, zobacz [Intel MPI biblioteki Installation Guide](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Włącz ptrace dla użytkowników innych niż root debugger innych procesów (wymagane dla najnowszej wersji Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```

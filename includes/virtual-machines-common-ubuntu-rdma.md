1. Zainstaluj dapl, rdmacm ibverbs i mlx4

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. W /etc/waagent.conf należy włączyć funkcję RDMA przez usunięcie komentarza z poniższych wierszy konfiguracji. Należy głównego dostępu do edytowania tego pliku.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Dodaj lub Zmień następujące ustawienia pamięci w artykule bazy wiedzy w pliku /etc/security/limits.conf. Należy głównego dostępu do edytowania tego pliku. Do celów testowych można ustawić memlock na nieograniczony. Na przykład: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Zainstaluj biblioteki MPI Intel. Albo [kupić i pobrać](https://software.intel.com/intel-mpi-library/) biblioteki z Intel lub pobierania [wersji bezpłatnej wersji ewaluacyjnej](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
  wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
  ```
 
 Obsługiwane są tylko runtimes 5.x Intel MPI.
 
 Aby uzyskać instrukcje instalacji, zobacz [Przewodnik instalacji biblioteki MPI Intel](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Włącz ptrace dla procesów z systemem innym niż debugera inny niż główny (wymagane dla najnowsze wersje Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```

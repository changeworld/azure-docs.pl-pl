---
title: Regionalnego odzyskiwania po awarii dla usługi Azure Databricks
description: W tym artykule opisano sposób wykonywania odzyskiwania po awarii w usłudze Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: bd91d9201e81c884b48b41de27146c186eeb9598
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000848"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionalnego odzyskiwania po awarii w przypadku klastrów usługi Azure Databricks

W tym artykule opisano architekturę odzyskiwania po awarii przydatny w przypadku klastrów usługi Azure Databricks i kroki do wykonania tego projektu.

## <a name="azure-databricks-architecture"></a>Architektura usługi Azure Databricks

Na wysokim poziomie, gdy utworzysz obszar roboczy usługi Azure Databricks w witrynie Azure portal [urządzenia zarządzanego](../managed-applications/overview.md) jest wdrażany jako zasób platformy Azure w ramach subskrypcji, w wybranym regionie platformy Azure (na przykład zachodnie stany USA). To urządzenie zostało wdrożone w [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) z [sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md) i konto usługi Azure Storage, dostępne w Twojej subskrypcji. Sieć wirtualna zapewnia obwodu zabezpieczeń na poziomie do obszaru roboczego usługi Databricks i jest chroniony za pomocą sieciowej grupy zabezpieczeń. W obszarze roboczym można utworzyć klastry usługi Databricks, podając procesu roboczego i sterownika typu maszyny Wirtualnej i wersji środowiska uruchomieniowego usługi Databricks. Trwałe dane są dostępne w ramach konta magazynu, który może być usługi Azure Blob Storage lub Azure Data Lake Store. Po utworzeniu klastra można uruchamiać zadania za pośrednictwem punktów końcowych ODBC/JDBC notesów, interfejsy API REST, dołączając do określonego klastra.

Na płaszczyźnie kontroli usługi Databricks zarządza i monitoruje środowisko obszaru roboczego usługi Databricks. Żadnych operacji zarządzania, które takie jak utworzyć klaster zostanie zainicjowane z na płaszczyźnie kontroli. Wszystkie metadane, takie jak zaplanowanych zadań, znajduje się w bazie danych Azure przy użyciu replikacji geograficznej dla odporności na uszkodzenia.

![Architektura usługi Databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Jedną z zalet tej architektury jest to, czy użytkownicy mogą łączyć z usługi Azure Databricks z dowolnymi zasobami magazynu na jego koncie. Zaletą jest zarówno obliczeniowe (Azure Databricks) i magazynu mogą być skalowane niezależnie od siebie nawzajem.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Jak utworzyć topologię odzyskiwania po awarii regionalnej

Jak widać w powyższy opis architektury, istnieje kilka składników stosowanych do potoku danych Big Data za pomocą usługi Azure Databricks:  Usługa Azure Storage, Azure Database i innymi źródłami danych. Usługa Azure Databricks to *obliczenia* dla danych Big Data potoku. Jest *efemeryczne* pochylone, co oznacza, że podczas gdy dane są nadal dostępne w usłudze Azure Storage *obliczenia* (klastra usługi Azure Databricks) może zostać zakończone, dzięki czemu nie trzeba płacić za obliczenia, gdy zostanie nie są potrzebne. *Obliczenia* (usługa Azure Databricks) i źródłach magazynowania musi być w tym samym regionie, tak aby zadania nie występuje duże opóźnienie.  

Aby utworzyć własny topologii odzyskiwania regionalnej awarii, wykonaj te wymagania:

   1. Aprowizuj wielu Azure obszarów roboczych usługi Databricks w oddzielnych regionach platformy Azure. Na przykład utworzyć podstawowy obszar roboczy usługi Azure Databricks w wschodnie stany USA 2. Utwórz obszar roboczy usługi Azure Databricks dodatkowej odzyskiwania po awarii w osobnym regionie, takich jak zachodnie stany USA.

   2. Użyj [magazyn geograficznie nadmiarowy](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Dane, które skojarzone usługi Azure Databricks jest domyślnie przechowywane w usłudze Azure Storage. Wyniki z zadań usługi Databricks są także przechowywane w usłudze Azure Blob Storage, dzięki czemu przetworzone dane są trwałe i pozostają wysoce dostępne po klastra zostanie zakończony. Zgodnie z magazynu i Databricks klastra mają wspólną lokalizację, należy użyć magazyn geograficznie nadmiarowy, dzięki czemu dane są dostępne w regionie pomocniczym Jeśli region podstawowy nie jest już dostępny.

   3. Po utworzeniu regionu pomocniczego należy migracji użytkowników, foldery użytkowników, notesów, konfigurację klastra, zadania konfiguracji, bibliotek, magazynu, skrypty init i skonfigurować kontrolę dostępu. Dodatkowe szczegóły są opisane w poniższej sekcji.

## <a name="detailed-migration-steps"></a>Kroki migracji szczegółowe

1. **Konfigurowanie interfejsu wiersza polecenia Databricks na komputerze**

   Ten artykuł zawiera szereg przykładów kodu, które przy użyciu interfejsu wiersza polecenia dla większości automatyczne czynności, ponieważ jest otoką łatwy do użytkownika za pośrednictwem interfejsu API REST usługi Azure Databricks.

   Przed wykonaniem kroków migracji, należy zainstalować usługi databricks — interfejs wiersza polecenia na komputerze stacjonarnym lub maszynę wirtualną, w których planuje się wykonują pracę. Aby uzyskać więcej informacji, zobacz [zainstalować interfejs wiersza polecenia w usłudze Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Oczekiwano wszystkie skrypty języka python, zawarte w tym artykule do pracy z językiem Python 2.7 + < 3.x.

2. **Skonfiguruj dwa profile.**

   Skonfiguruj jeden dla podstawowy obszar roboczy, a innym dodatkowej obszaru roboczego:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Blokuje kod w tym przełączniku artykułu między profilami w każdy kolejny krok za pomocą odpowiedniego polecenia obszaru roboczego. Pamiętaj, że nazwy profili, które tworzysz są zastępowane do każdego bloku kodu.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Możesz ręcznie przełączać w wierszu polecenia w razie potrzeby:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrowanie użytkowników usługi Azure Active Directory**

   Ręcznie dodać tych samych użytkowników usługi Azure Active Directory do dodatkowej obszar roboczy, który istnieje w podstawowym obszarem roboczym.

4. **Migrowanie folderów użytkownika, a notesów**

   Użyj poniższego kodu języka python do migracji środowiska użytkownika w trybie piaskownicy, obejmujących strukturę folderów zagnieżdżonych i notesy dla poszczególnych użytkowników.

   > [!NOTE]
   > Biblioteki nie są kopiowane na w tym kroku jako podstawowy interfejs API nie obsługuje tych.

   Skopiuj i Zapisz poniższy skrypt języka python do pliku i uruchom go w wierszu polecenia usługi Databricks. Na przykład `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Migrowanie konfiguracji klastra**

   Po przeprowadzeniu migracji notesów Opcjonalnie można migrować konfiguracji klastra do nowego obszaru roboczego. Jest prawie w pełni zautomatyzowane kroku przy użyciu interfejsu wiersza polecenia databricks, chyba że chcesz przeprowadzić migrację konfiguracji selektywnej klastra, a nie dla wszystkich.

   > [!NOTE]
   > Niestety nie ma żadnych Utwórz punkt końcowy konfiguracji klastra, a ten skrypt próbuje następnie od razu tworzenie każdego klastra. Jeśli nie ma wystarczającej liczby rdzeni dostępnych w ramach subskrypcji, tworzenie klastra może zakończyć się niepowodzeniem. Błąd można zignorować, tak długo, jak konfiguracja jest przenoszona pomyślnie.

   Poniższy skrypt, pod warunkiem drukuje mapowanie ze starego do nowego klastra identyfikatory, które mogłyby zostać użyte do zadania migracji (dla zadań, które są skonfigurowane do używania istniejących klastrów).

   Skopiuj i Zapisz poniższy skrypt języka python do pliku i uruchom go w wierszu polecenia usługi Databricks. Na przykład `python scriptname.py`.

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **Migrowanie konfiguracji zadania**

   Jeśli wykonano migrację konfiguracji klastra w poprzednim kroku, możesz zdecydować się na migrację konfiguracji zadania do nowego obszaru roboczego. Jest to krok pełni zautomatyzowanego, przy użyciu interfejsu wiersza polecenia databricks, chyba że chcesz przeprowadzić migrację konfiguracji selektywnej zadania, a nie działa dla wszystkich zadań.

   > [!NOTE]
   > Konfiguracja dla zaplanowanego zadania zawiera "" informacje o harmonogramie, więc domyślnie, rozpocznie się pracę dla każdego skonfigurowanego chronometrażu tak szybko, jak została ona zmigrowana. W związku z tym poniższy blok kodu usuwa wszystkie informacje o harmonogramie podczas migracji (Aby uniknąć zduplikowanych przebiegów w starych i nowych obszarach roboczych). Skonfiguruj harmonogramy tych zadań, gdy wszystko będzie gotowe do uruchomienia produkcyjnego.

   Konfiguracja zadania wymaga ustawienia dla nowego lub istniejącego klastra. Jeśli używasz istniejącego klastra, poniżej/Code skryptu będzie próbował zastąpić stary identyfikator klastra za pomocą nowego klastra identyfikatora.

   Skopiuj i Zapisz python poniższy skrypt do pliku. Zastąp wartość `old_cluster_id` i `new_cluster_id`, z danymi wyjściowymi migracji klastra w poprzednim kroku. Uruchom go w wierszu polecenia interfejsu wiersza polecenia databricks, na przykład `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Migruj biblioteki**

   Obecnie to prosty sposób Migruj biblioteki z jednego obszaru roboczego do innego. Zamiast tego należy ręcznie ponownie tych bibliotek do nowego obszaru roboczego. Można zautomatyzować przy użyciu kombinacji [DBFS interfejsu wiersza polecenia](https://github.com/databricks/databricks-cli#dbfs-cli-examples) można przekazać niestandardowe biblioteki do obszaru roboczego i [bibliotek interfejsu wiersza polecenia](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrowanie magazynu obiektów blob platformy Azure i instaluje usługi Azure Data Lake Store**

   Ręcznie zainstaluj wszystkie [usługi Azure Blob storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) i [usługi Azure Data Lake Store (Gen 2)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) przy użyciu rozwiązania na podstawie notesu punkty instalacji. Zasobów usługi storage będą instalowane w podstawowym obszarem roboczym i który ma być powtarzany w dodatkowej obszaru roboczego. Nie ma żadnych zewnętrznych interfejsów API dla instalacji.

9. **Migrowanie klastra init skryptów**

   Wszystkie skrypty inicjowania klastra można poddać migracji ze starego do nowego obszaru roboczego przy użyciu [DBFS interfejsu wiersza polecenia](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Najpierw skopiuj wymagane skrypty z `dbfs:/dat abricks/init/..` Twojego pulpitu lokalnego lub na maszynie wirtualnej. Następnie skopiuj te skrypty do nowego obszaru roboczego w tej samej ścieżce.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Ręcznie zmień konfigurację i ponowne zastosowanie kontroli dostępu.**

    Jeśli istniejący podstawowy obszar roboczy jest skonfigurowany do używania w warstwie Premium (SKU), prawdopodobnie też używasz [funkcji kontroli dostępu](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control).

    Korzystając z funkcji kontroli dostępu, należy ręcznie ponownie kontroli dostępu do zasobów (notesów, klastrów, zadań, tabel).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Odzyskiwanie po awarii dla Twojego ekosystem platformy Azure

Jeśli używasz innych usług platformy Azure Pamiętaj zbyt implementacji najlepszych rozwiązań odzyskiwania po awarii dla tych usług. Na przykład, jeśli zdecydujesz się używać wystąpienia zewnętrznego magazynu metadanych Hive, należy rozważyć odzyskiwanie po awarii dla [serwera SQL Azure](../sql-database/sql-database-disaster-recovery.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md), i/lub [— Azure Database for MySQL ](../mysql/concepts-business-continuity.md). Aby uzyskać ogólne informacje na temat odzyskiwania po awarii, zobacz [odzyskiwania po awarii dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Databricks](https://docs.azuredatabricks.net/user-guide/index.html).

---
title: Regionalne odzyskiwanie po awarii dla usługi Azure Databricks
description: W tym artykule opisano podejście do odzyskiwania po awarii w usłudze Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 2604d5b357feacce3493b4a4ded971144262611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161940"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionalne odzyskiwanie po awarii dla klastrów usługi Azure Databricks

W tym artykule opisano architekturę odzyskiwania po awarii przydatne dla klastrów usługi Azure Databricks i kroki, aby wykonać ten projekt.

## <a name="azure-databricks-architecture"></a>Architektura usługi Azure Databricks

Na wysokim poziomie podczas tworzenia obszaru roboczego usługi Azure Databricks z witryny Azure portal, [zarządzane urządzenie](../azure-resource-manager/managed-applications/overview.md) jest wdrażany jako zasób platformy Azure w ramach subskrypcji, w wybranym regionie platformy Azure (na przykład zachodnie stany USA). To urządzenie jest wdrażane w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) z [sieciową grupą zabezpieczeń](../virtual-network/manage-network-security-group.md) i kontem usługi Azure Storage, dostępnym w ramach subskrypcji. Sieć wirtualna zapewnia bezpieczeństwo na poziomie obwodowym obszaru roboczego Databricks i jest chroniona za pośrednictwem sieciowej grupy zabezpieczeń. W obszarze roboczym można utworzyć klastry Databricks, podając typ maszyny Wirtualnej procesu roboczego i sterownika oraz wersję środowiska wykonawczego Databricks. Utrwalone dane są dostępne na koncie magazynu, które może być usługi Azure Blob Storage lub Usługi Azure Data Lake Storage. Po utworzeniu klastra można uruchamiać zadania za pośrednictwem notesów, interfejsów API REST, punktów końcowych ODBC/JDBC, dołączając je do określonego klastra.

Płaszczyzna sterowania Databricks zarządza i monitoruje środowisko obszaru roboczego Databricks. Każda operacja zarządzania, taka jak tworzenie klastra, zostanie zainicjowana z płaszczyzny sterowania. Wszystkie metadane, takie jak zaplanowane zadania, są przechowywane w bazie danych platformy Azure z replikacją geograficzną w celu usunięcia z powodu odporności na uszkodzenia.

![Architektura Databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Jedną z zalet tej architektury jest to, że użytkownicy mogą łączyć usługi Azure Databricks z dowolnym zasobem magazynu na swoim koncie. Kluczową zaletą jest to, że zarówno obliczenia (Azure Databricks) i magazynu mogą być skalowane niezależnie od siebie.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Jak utworzyć regionalną topologię odzyskiwania po awarii

Jak można zauważyć w poprzednim opisie architektury, istnieje wiele składników używanych do potoku dużych zbiorów danych z usługi Azure Databricks: Usługa Azure Storage, usługa Azure Database i innych źródeł danych. Usługa Azure Databricks to *obliczeń* dla potoku Big Data. Ma charakter *tymczasowy,* co oznacza, że gdy dane są nadal dostępne w usłudze Azure Storage, *obliczenia* (klaster Azure Databricks) można zakończyć, dzięki czemu nie trzeba płacić za obliczenia, gdy nie jest to potrzebne. Dane *obliczeniowe* (Azure Databricks) i źródła magazynu muszą znajdować się w tym samym regionie, aby zadania nie były wyzywają dużych opóźnień.  

Aby utworzyć własną regionalną topologię odzyskiwania po awarii, wykonaj następujące wymagania:

   1. Aprowizuj wiele obszarów roboczych usługi Azure Databricks w oddzielnych regionach platformy Azure. Na przykład utwórz podstawowy obszar roboczy usługi Azure Databricks we wschodniej części US2. Tworzenie pomocniczego obszaru roboczego usługi Azure Databricks odzyskiwania po awarii w oddzielnym regionie, takim jak zachodnie stany USA.

   2. Użyj [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy.md). Dane skojarzone z usługą Azure Databricks są domyślnie przechowywane w usłudze Azure Storage. Wyniki z zadań Databricks są również przechowywane w usłudze Azure Blob Storage, dzięki czemu przetwarzane dane są trwałe i pozostają wysoce dostępne po zakończeniu klastra. Ponieważ magazyn i Databricks klastra są zlokalizowane współlokowane, należy użyć magazynu geograficznie nadmiarowe, dzięki czemu można uzyskać dostęp do danych w regionie pomocniczym, jeśli region podstawowy nie jest już dostępny.

   3. Po utworzeniu regionu pomocniczego należy przeprowadzić migrację użytkowników, folderów użytkowników, notesów, konfiguracji klastra, konfiguracji zadań, bibliotek, magazynu, skryptów init i ponownie skonfigurować kontrolę dostępu. Dodatkowe szczegóły przedstawiono w poniższej sekcji.

## <a name="detailed-migration-steps"></a>Szczegółowe kroki migracji

1. **Konfigurowanie interfejsu wiersza polecenia Databricks na komputerze**

   W tym artykule przedstawiono kilka przykładów kodu, które używają interfejsu wiersza polecenia dla większości zautomatyzowanych kroków, ponieważ jest to łatwe dla użytkownika otoki za pomocą interfejsu API REST usługi Azure Databricks.

   Przed wykonaniem jakichkolwiek kroków migracji należy zainstalować databricks-cli na komputerze stacjonarnym lub na maszynie wirtualnej, na której planujesz wykonać pracę. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia databricks](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Wszystkie skrypty języka Python zawarte w tym artykule powinny współpracować z python 2.7+ < 3.x.

2. **Skonfiguruj dwa profile.**

   Skonfiguruj jeden dla podstawowego obszaru roboczego, a drugi dla pomocniczego obszaru roboczego:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Bloki kodu w tym artykule przełączają się między profilami w każdym kolejnym kroku przy użyciu odpowiedniego polecenia obszaru roboczego. Upewnij się, że nazwy utworzonych profili są podstawione do każdego bloku kodu.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   W razie potrzeby można ręcznie przełączyć się w wierszu polecenia:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrowanie użytkowników usługi Azure Active Directory**

   Ręcznie dodaj tych samych użytkowników usługi Azure Active Directory do pomocniczego obszaru roboczego, który istnieje w podstawowym obszarze roboczym.

4. **Migrowanie folderów i notesów użytkowników**

   Użyj następującego kodu języka Python, aby przeprowadzić migrację środowisk użytkownika w trybie piaskownicy, które obejmują strukturę zagnieżdżonych folderów i notesy na użytkownika.

   > [!NOTE]
   > Biblioteki nie są kopiowane w tym kroku, ponieważ podstawowy interfejs API nie obsługuje tych.

   Skopiuj i zapisz następujący skrypt języka Python do pliku i uruchom go w wierszu polecenia Databricks. Na przykład `python scriptname.py`.

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

   Po migracji notesów można opcjonalnie przeprowadzić migrację konfiguracji klastra do nowego obszaru roboczego. Jest to prawie w pełni zautomatyzowany krok przy użyciu databricks-cli, chyba że chcesz zrobić selektywną migrację konfiguracji klastra, a nie dla wszystkich.

   > [!NOTE]
   > Niestety nie istnieje żaden punkt końcowy konfiguracji klastra, a ten skrypt próbuje utworzyć każdy klaster od razu. Jeśli w subskrypcji nie ma wystarczającej liczby rdzeni, tworzenie klastra może zakończyć się niepowodzeniem. Błąd można zignorować, tak długo, jak konfiguracja jest przenoszona pomyślnie.

   Poniższy skrypt pod warunkiem drukowania mapowania ze starych do nowych identyfikatorów klastra, które mogą być używane do migracji zadań później (dla zadań, które są skonfigurowane do korzystania z istniejących klastrów).

   Skopiuj i zapisz następujący skrypt języka Python do pliku i uruchom go w wierszu polecenia Databricks. Na przykład `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json, os

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE])
   clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append(cluster_info.split(None, 1)[0])

   for cluster_info in clusters_info_list: 
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]

   print(str(len(clusters_list)) + " clusters found in the primary site" )

   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " + cluster)
      cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
      print ("Got cluster config from old workspace")

       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' : 
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

      for key in cluster_json_keys:
         if key not in cluster_req_elems:
            cluster_req_json.pop(key, None)

      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json" 

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()

      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```

6. **Migrowanie konfiguracji zadań**

   Jeśli konfiguracje klastra zostały zmigrowane w poprzednim kroku, można zdecydować się na migrację konfiguracji zadań do nowego obszaru roboczego. Jest to w pełni zautomatyzowany krok przy użyciu databricks-cli, chyba że chcesz zrobić selektywną migrację konfiguracji zadań, a nie robić to dla wszystkich zadań.

   > [!NOTE]
   > Konfiguracja zaplanowanego zadania zawiera również informacje o harmonogramie, więc domyślnie rozpocznie pracę zgodnie ze skonfigurowanym czasem, gdy tylko zostanie zmigrowany. W związku z tym następujący blok kodu usuwa wszelkie informacje o harmonogramie podczas migracji (aby uniknąć duplikatów przebiegów w starych i nowych obszarach roboczych). Skonfiguruj harmonogramy dla takich zadań, gdy będziesz gotowy do cutover.

   Konfiguracja zadania wymaga ustawień dla nowego lub istniejącego klastra. Jeśli używasz istniejącego klastra, skrypt /code poniżej spróbuje zastąpić stary identyfikator klastra nowym identyfikatorem klastra.

   Skopiuj i zapisz następujący skrypt języka Python do pliku. Zastąp `old_cluster_id` `new_cluster_id`wartość dla i , z wyjściem z migracji klastra wykonane w poprzednim kroku. Uruchom go w wierszu polecenia databricks-cli, na przykład `python scriptname.py`.

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

7. **Migrowanie bibliotek**

   Obecnie nie ma prostego sposobu migracji bibliotek z jednego obszaru roboczego do drugiego. Zamiast tego należy ręcznie zainstalować te biblioteki w nowym obszarze roboczym. Możliwe jest zautomatyzowanie za pomocą kombinacji [interfejsu wiersza polecenia DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples) do przekazywania niestandardowych bibliotek do obszaru roboczego i [bibliotek cli](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrowanie magazynu obiektów blob platformy Azure i instalacji usługi Azure Data Lake Storage**

   Ręcznie ponownie zainstaluj wszystkie punkty [instalacji usługi Azure Blob storage](/azure/databricks/data/data-sources/azure/azure-storage) i [usługi Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) przy użyciu rozwiązania opartego na notesie. Zasoby magazynu zostałyby zainstalowane w podstawowym obszarze roboczym i które muszą być powtarzane w pomocniczym obszarze roboczym. Nie ma zewnętrznego interfejsu API dla instalacji.

9. **Migrowanie skryptów init klastra**

   Wszystkie skrypty inicjowania klastra można migrować ze starego do nowego obszaru roboczego przy użyciu [interfejsu wiersza polecenia DBFS.](https://github.com/databricks/databricks-cli#dbfs-cli-examples) Najpierw skopiuj `dbfs:/dat abricks/init/..` potrzebne skrypty z pulpitu lokalnego lub maszyny wirtualnej. Następnie skopiuj te skrypty do nowego obszaru roboczego w tej samej ścieżce.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Ręcznie ponownie skonfiguruj i ponownie naprzymiedź kontrolę dostępu.**

    Jeśli istniejący podstawowy obszar roboczy jest skonfigurowany do używania warstwy Premium (SKU), prawdopodobnie używasz również [funkcji Kontrola dostępu.](/azure/databricks/administration-guide/access-control/index)

    Jeśli korzystasz z funkcji Kontroli dostępu, ręcznie ponownie naprzymień kontrolę dostępu do zasobów (notesy, klastry, zadania, tabele).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Odzyskiwanie po awarii dla ekosystemu platformy Azure

Jeśli używasz innych usług platformy Azure, należy zaimplementować najlepsze rozwiązania odzyskiwania po awarii dla tych usług, zbyt. Na przykład, jeśli zdecydujesz się użyć zewnętrznego wystąpienia metasklepu hive, należy rozważyć odzyskiwanie po awarii dla [usługi Azure SQL Server,](../sql-database/sql-database-disaster-recovery.md) [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)i/lub [usługi Azure Database for MySQL](../mysql/concepts-business-continuity.md). Aby uzyskać ogólne informacje na temat odzyskiwania po awarii, zobacz [Odzyskiwanie po awarii dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Databricks](index.yml).
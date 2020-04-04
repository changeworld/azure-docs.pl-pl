---
title: Często zadawane pytania dotyczące usługi Azure HDInsight
description: Często zadawane pytania dotyczące hdinsight
keywords: często zadawane pytania, często zadawane pytania
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a37e1b9bc4a0b953dc727dbab2813dd938ed576
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652214"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Usługa Azure HDInsight: często zadawane pytania

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [usługi Azure HDInsight.](https://azure.microsoft.com/services/hdinsight/)

## <a name="creating-or-deleting-hdinsight-clusters"></a>Tworzenie lub usuwanie klastrów usługi HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Jak aprowizować klaster HDInsight?

Aby przejrzeć typy dostępnych klastrów HDInsight i metody inicjowania obsługi administracyjnej, zobacz [Konfigurowanie klastrów w udziale HDInsight za pomocą apache Hadoop, Apache Spark, Apache Kafka i innych](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Jak usunąć istniejący klaster HDInsight?

Aby dowiedzieć się więcej o usuwaniu klastra, gdy nie jest już używany, zobacz [Usuwanie klastra HDInsight](hdinsight-delete-cluster.md).

Pozostaw co najmniej 30 do 60 minut między operacjami tworzenia i usuwania. W przeciwnym razie operacja może zakończyć się niepowodzeniem z następującym komunikatem o błędzie:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Jak wybrać prawidłową liczbę rdzeni lub węzłów dla mojego obciążenia?

Odpowiednia liczba rdzeni i innych opcji konfiguracji zależy od różnych czynników.

Aby uzyskać więcej informacji, zobacz [Planowanie pojemności klastrów HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Co mogę zrobić, gdy aprowizacja klastra kończy się niepowodzeniem z powodu problemu z pojemnością?

Typowe błędy problemu pojemności i techniki ograniczania emisji znajdują się w tej sekcji.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Błąd: Wdrożenie przekroczyłoby limit "800"

Platforma Azure ma limit przydziału, który wynosi 800 wdrożeń na grupę zasobów. Różne przydziały są stosowane dla grupy zasobów, subskrypcji, konta lub innych zakresów. Na przykład subskrypcja może być skonfigurowana w taki sposób, aby ograniczyć liczbę rdzeni dla regionu. Jeśli spróbujesz wdrożyć maszynę wirtualną, która ma więcej rdzeni niż dozwolona kwota, zostanie wyświetlony komunikat o błędzie informujący, że przydział został przekroczony.

Aby rozwiązać ten problem, usuń wdrożenia, które nie są już potrzebne przy użyciu witryny Azure portal, interfejsu wiersza polecenia lub programu PowerShell.

Aby uzyskać więcej informacji, zobacz [Resolve errors for resource quotas (Rozwiązywanie błędów z limitami przydziałów zasobów)](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Błąd: maksymalny węzeł przekroczył dostępne rdzenie w tym regionie

Twoja subskrypcja może być skonfigurowana w taki sposób, aby ograniczyć liczbę rdzeni dla regionu. Jeśli spróbujesz wdrożyć zasób, który ma więcej rdzeni niż dozwolona kwota, zostanie wyświetlony komunikat o błędzie informujący, że przydział został przekroczony.

Aby zażądać zwiększenia limitu przydziału, wykonaj następujące kroki:

1. Przejdź do [witryny Azure portal](https://portal.azure.com)i wybierz pozycję Pomoc + pomoc **techniczna**.
   
1. Wybierz **pozycję Nowe żądanie pomocy technicznej**.
   
1. Na karcie **Podstawy** na stronie **Nowe żądanie pomocy technicznej** podaj następujące informacje:
   
   - **Typ problemu:** Wybierz **pozycję Limity usług i subskrypcji (przydziały).**
   - **Subskrypcja:** Wybierz subskrypcję, którą chcesz zmodyfikować.
   - **Typ przydziału:** Wybierz **HDInsight**.

Aby uzyskać więcej informacji, zobacz [Tworzenie biletu pomocy technicznej w celu zwiększenia liczby rdzeni](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Jakie są różne typy węzłów w klastrze HDInsight?

Klastry usługi Azure HDInsight mają różne typy maszyn wirtualnych lub węzłów. Każdy typ węzła odgrywa rolę w działaniu systemu.

Aby uzyskać więcej informacji, zobacz [Typy zasobów w klastrach usługi Azure HDInsight](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Pojedyncze składniki

### <a name="can-i-install-additional-components-on-my-cluster"></a>Czy mogę zainstalować dodatkowe składniki w klastrze?

Tak. Aby zainstalować dodatkowe składniki lub dostosować konfigurację klastra, należy użyć:

- Skrypty podczas lub po utworzeniu. Skrypty są wywoływane za pomocą [akcji skryptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), która jest opcją konfiguracji, której można użyć z portalu Azure Portal, poleceń cmdlet programu HDInsight systemu Windows PowerShell lub zestawu SDK .NET usługi HDInsight. Tej opcji konfiguracji można używać z witryny Azure portal, poleceń cmdlet programu Windows Programu Windows hdinsight lub zestawu SDK .NET usługi HDInsight.

- [Platforma aplikacji HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) do instalowania aplikacji ekosystemowych.

Aby uzyskać listę obsługiwanych składników, zobacz [Jakie są składniki i wersje Apache Hadoop dostępne z hdinsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Czy można uaktualnić poszczególne składniki, które są wstępnie zainstalowane w klastrze?

W przypadku uaktualnienia wbudowanych składników lub aplikacji, które są wstępnie zainstalowane w klastrze, wynikowa konfiguracja nie będzie obsługiwana przez firmę Microsoft. Te konfiguracje systemu nie zostały przetestowane przez firmę Microsoft. Spróbuj użyć innej wersji klastra HDInsight, która może mieć już uaktualnioną wersję składnika wstępnie zainstalowaną.

Na przykład uaktualnianie gałęzi jako pojedynczego składnika nie jest obsługiwane. HDInsight jest usługą zarządzaną, a wiele usług jest zintegrowanych z serwerem Ambari i przetestowanych. Uaktualnianie gałęzi na własną rękę powoduje zindeksowane pliki binarne innych składników do zmiany i spowoduje problemy z integracją składników w klastrze.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Czy Spark i Kafka mogą działać w tym samym klastrze HDInsight?

Nie, nie można uruchomić Apache Kafka i Apache Spark w tym samym klastrze HDInsight. Tworzenie oddzielnych klastrów dla platformy Kafka i Spark, aby uniknąć problemów z rywalizacją o zasoby.

### <a name="how-do-i-change-timezone-in-ambari"></a>Jak zmienić strefę czasową w Ambari?

1. Otwórz interfejs użytkownika sieci Web `https://CLUSTERNAME.azurehdinsight.net`Ambari w witrynie , gdzie nazwa klastra jest nazwą klastra.
2. W prawym górnym rogu wybierz admina | Ustawienia. 

   ![Ustawienia Ambari](media/hdinsight-faq/ambari-settings.png)

3. W oknie Ustawienia użytkownika wybierz nową strefę czasową z listy rozwijanej Strefa czasowa, a następnie kliknij przycisk Zapisz.

   ![Ustawienia użytkownika Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Magazyn metadanych

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Jak przeprowadzić migrację z istniejącego magazynu meta do programu Azure SQL Server? 

Aby przeprowadzić migrację z programu SQL Server do programu Azure SQL Server, zobacz [Samouczek: Migrowanie programu SQL Server do pojedynczej bazy danych lub puli bazy danych w bazie danych SQL w trybie offline usługi Azure przy użyciu usługi DMS.](../dms/tutorial-sql-server-to-azure-sql.md)

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Czy magazyn meteula jest usuwany po usunięciu klastra?

To zależy od typu magazynu meta, który jest skonfigurowany do użycia klastra.

W przypadku domyślnego magazynu metastore: domyślny magazyn meta jest częścią cyklu życia klastra. Po usunięciu klastra odpowiedni magazyn meta i metadane są również usuwane.

W przypadku niestandardowego magazynu metastore: cykl życia magazynu met nie jest powiązany z cyklem życia klastra. W związku z tym można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy gałęzi, utrzymują się nawet po usunięciu i ponownym utworzeniu klastra HDInsight.

Aby uzyskać więcej informacji, zobacz [Używanie zewnętrznych magazynów metadanych w usłudze Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Czy migracja metasklepu hive również migracji domyślnych zasad bazy danych Ranger?

Nie, definicja zasad znajduje się w bazie danych Ranger, więc migracja bazy danych Ranger spowoduje migrację jej zasad.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Czy można przeprowadzić migrację magazynu meteoru hive z klastra pakietu zabezpieczeń przedsiębiorstwa (ESP) do klastra innych niż ESP i odwrotnie?

Tak, można migrować magazyn metepułk z usługi ESP do klastra innych niż ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Jak oszacować rozmiar bazy danych sieci metestore hive?

A Hive metastore służy do przechowywania metadanych dla źródeł danych, które są używane przez serwer hive. Wymagania dotyczące rozmiaru zależą częściowo od liczby i złożoności źródeł danych hive i nie można ich oszacować z góry. Zgodnie z [wytycznymi dotyczącymi magazynu metastore hive](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)można rozpocząć od warstwy S2, która zapewnia 50 identyfikatorów DTU i 250 GB pamięci masowej, a jeśli zobaczysz wąskie gardło, możesz skalować bazę danych.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Czy obsługujesz inną bazę danych inną niż Azure SQL Database jako zewnętrzny magazyn metastore?

Nie, firma Microsoft obsługuje tylko usługę Azure SQL Database jako zewnętrzny niestandardowy magazyn meteoza.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Czy mogę udostępnić magazyn meta w wielu klastrach?

Tak, można udostępniać niestandardowy magazyn meta w wielu klastrach, o ile są one przy użyciu tej samej wersji usługi HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Łączność i sieci wirtualne  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Jakie są konsekwencje blokowania portów 22 i 23 w mojej sieci?

Jeśli zablokujesz porty 22 i port 23, nie będziesz mieć dostępu SSH do klastra. Te porty nie są używane przez usługę HDInsight.

Więcej informacji można znaleźć w następujących dokumentach:

- [Kontrolowanie ruchu sieciowego](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Zabezpieczanie przychodzącego ruchu do klastrów USŁUGI HDInsight w sieci wirtualnej z prywatnym punktem końcowym](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Adresy IP zarządzania hdinsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Czy można wdrożyć dodatkową maszynę wirtualną w tej samej podsieci co klaster USŁUGI HDInsight?

Tak, można wdrożyć dodatkową maszynę wirtualną w tej samej podsieci co klaster HDInsight. Możliwe są następujące konfiguracje:

- Węzły krawędzi: Do klastra można dodać kolejny węzeł krawędziowy, zgodnie [z opisem w pola Użyj pustych węzłów krawędzi w klastrach Apache Hadoop w programie HDInsight](hdinsight-apps-use-edge-node.md).

- Węzły autonomiczne: Do tej samej podsieci można dodać autonomiczną maszynę wirtualną i `https://<CLUSTERNAME>-int.azurehdinsight.net`uzyskać dostęp do klastra z tej maszyny wirtualnej przy użyciu prywatnego punktu końcowego . Aby uzyskać więcej informacji, zobacz [Kontrolowanie ruchu sieciowego](hdinsight-plan-virtual-network-deployment.md#networktraffic).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Czy należy przechowywać dane na dysku lokalnym węzła brzegowego?

Nie, przechowywanie danych na dysku lokalnym nie jest dobrym pomysłem. Jeśli węzeł ulegnie awarii, wszystkie dane przechowywane lokalnie zostaną utracone. Zaleca się przechowywanie danych w usłudze Azure Data Lake Storage Gen2 lub magazynie obiektów Blob platformy Azure lub przez zamontowanie udziału usługi Azure Files do przechowywania danych.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Czy można dodać istniejący klaster HDInsight do innej sieci wirtualnej?

Nie, nie można. Sieć wirtualna powinna być określona w czasie inicjowania obsługi administracyjnej. Jeśli podczas inicjowania obsługi administracyjnej nie określono żadnej sieci wirtualnej, wdrożenie tworzy sieć wewnętrzną, która nie jest dostępna z zewnątrz. Aby uzyskać więcej informacji, zobacz [Dodawanie funkcji HDInsight do istniejącej sieci wirtualnej](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Zabezpieczenia i certyfikaty

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Jakie są zalecenia dotyczące ochrony przed złośliwym oprogramowaniem w klastrach usługi Azure HDInsight?

Aby uzyskać informacje na temat ochrony przed złośliwym oprogramowaniem, zobacz [Ochrona przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Jak utworzyć klucz dla klastra ESP usługi HDInsight?

Utwórz klucza Kerberos dla nazwy użytkownika domeny. Można później użyć tej klucza do uwierzytelniania do zdalnych klastrów przyłączonych do domeny bez wprowadzania hasła. Nazwa domeny jest wielka litera:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Czy można użyć istniejącej dzierżawy usługi Azure Active Directory do utworzenia klastra usługi HDInsight, który ma esp?

Przed utworzeniem klastra usługi HDInsight za pomocą usługi ESP należy włączyć usługi domenowe usługi Azure Active Directory (Usługi azure AD DS). Hadoop open-source opiera się na Kerberos uwierzytelniania (w przeciwieństwie do OAuth).

Aby dołączyć maszyny wirtualne do domeny, musisz mieć kontroler domeny. Usługi Azure AD DS jest zarządzanym kontrolerem domeny i jest uważany za rozszerzenie usługi Azure Active Directory, który zapewnia wszystkie wymagania protokołu Kerberos do tworzenia bezpiecznego klastra Hadoop w sposób zarządzany. Usługa HDInsight jako usługa zarządzana integruje się z usługą Azure AD DS w celu zapewnienia kompleksowych zabezpieczeń.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Czy mogę używać certyfikatu z podpisem własnym w bezpiecznej konfiguracji ldap ldap aAD-DS i aprowizować klaster ESP?

Zaleca się korzystanie z certyfikatu wystawionego przez urząd certyfikacji, ale przy użyciu certyfikatu z podpisem własnym jest również obsługiwane w esp. Aby uzyskać więcej informacji, zobacz:

- [Włączanie usług Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Samouczek: Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej usług domenowych usługi active directory platformy Azure](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Jak mogę wyciągnąć aktywność logowania pokazaną w Ranger?

W przypadku wymagań dotyczących inspekcji firma Microsoft zaleca włączenie dzienników usługi Azure Monitor zgodnie z opisem w [obszarze Korzystanie z dzienników usługi Azure Monitor w celu monitorowania klastrów usługi HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Czy mogę wyłączyć Clamscan w klastrze?

Clamscan to oprogramowanie antywirusowe, które działa w klastrze HDInsight i jest używane przez zabezpieczenia platformy Azure (azsecd) do ochrony klastrów przed atakami wirusów. Firma Microsoft zdecydowanie zaleca użytkownikom powstrzymanie się od wprowadzania jakichkolwiek zmian w domyślnej konfiguracji Clamscan.

Proces ten nie koliduje z lub wziąć żadnych cykli od innych procesów. Zawsze podda się innemu procesowi. Skoki procesora z Clamscan powinny być widoczne tylko wtedy, gdy system jest bezczynny.  

W scenariuszach, w których należy kontrolować harmonogram, można wykonać następujące kroki:

1. Wyłącz automatyczne wykonywanie za pomocą następującego polecenia:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Dodaj zadanie Cron, które uruchamia następujące polecenie jako główne:
   
   `/usr/local/bin/azsecd manual -s clamav`

Aby uzyskać więcej informacji na temat konfigurowania i uruchamiania zadania cron, zobacz [Jak skonfigurować zadanie cron?](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Dlaczego usługa LLAP jest dostępna w klastrach Spark ESP?
W klastrach ESP Spark protokół LLAP jest włączona ze względów bezpieczeństwa (tj. Maszyny wirtualne większego węzła należy używać do obsługi wykorzystania zasobów llap (np. minimum D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Jak dodać dodatkowe grupy AAD po utworzeniu klastra ESP?
Istnieją dwa sposoby osiągnięcia tego celu: 1- Można odtworzyć klastra i dodać dodatkową grupę w momencie tworzenia klastra. Jeśli używasz synchronizacji o określonym zakresie w AAD-DS, upewnij się, że grupa B jest uwzględniona w synchronizacji o określonym zakresie.
2- Dodaj grupę jako zagnieżdżoną podgrupę poprzedniej grupy, która została użyta do utworzenia klastra ESP. Na przykład, jeśli utworzono klaster ESP z grupą, `A`można później dodać grupę `B` jako zagnieżdżoną podgrupę `A` i po około jednej godzinie zostanie ona automatycznie zsynchronizowana i udostępniona w klastrze. 

## <a name="storage"></a>Magazyn

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Czy mogę dodać usługę Azure Data Lake Storage Gen2 do istniejącego klastra USŁUGI HDInsight jako dodatkowe konto magazynu?

Nie, obecnie nie jest możliwe dodanie konta magazynu usługi Azure Data Lake Storage Gen2 do klastra, który ma magazyn obiektów blob jako podstawowy magazyn. Aby uzyskać więcej informacji, zobacz [Porównanie opcji magazynowania](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Jak znaleźć aktualnie połączonego podmiotu obsługującego konto magazynu usługi Data Lake?

Ustawienia można znaleźć w **usłudze Dostęp do magazynu usługi Data Lake Gen1** w obszarze właściwości klastra w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Weryfikowanie konfiguracji klastra](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Jak obliczyć użycie kont magazynu i kontenerów obiektów blob dla moich klastrów HDInsight?

Wykonaj jedną z następujących czynności:

- [Korzystanie z programu PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Znajdź rozmiar */user/hive/. Kosz/* folder w klastrze HDInsight, używając następującego wiersza polecenia:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Jak skonfigurować inspekcję konta magazynu obiektów blob?

Aby przeprowadzić inspekcję kont magazynu obiektów blob, skonfiguruj monitorowanie przy użyciu procedury [w obszarze Monitoruj konto magazynu w witrynie Azure portal](../storage/common/storage-monitor-storage-account.md). Dziennik inspekcji HDFS zawiera tylko informacje inspekcji dla lokalnego systemu plików HDFS (hdfs://mycluster).  Nie obejmuje operacji, które są wykonywane w magazynie zdalnym.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Jak przenieść pliki między kontenerem obiektu blob a węzłem głównym HDInsight?

Uruchom skrypt podobny do następującego skryptu powłoki w węźle głównym:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> *Filenames.txt* plik będzie miał ścieżkę bezwzględną plików w kontenerach obiektów blob.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Czy są jakieś wtyczki Ranger do przechowywania?

Obecnie nie istnieje żadna wtyczka Ranger dla magazynu obiektów blob i usługi Azure Data Lake Storage Gen1 lub Gen2. W przypadku klastrów ESP należy użyć usługi Azure Data Lake Storage, ponieważ można co najmniej ręcznie ustawić uprawnienia szczegółowe na poziomie systemu plików przy użyciu narzędzi HDFS. Ponadto podczas korzystania z usługi Azure Data Lake Storage klastry ESP wykonają niektóre kontroli dostępu do systemu plików przy użyciu usługi Azure Active Directory na poziomie klastra. 

Zasady dostępu do danych można przypisać do grup zabezpieczeń użytkowników przy użyciu Eksploratora usługi Azure Storage. Aby uzyskać więcej informacji, zobacz:

- [Jak ustawić uprawnienia użytkowników usługi Azure AD do wykonywania zapytań o dane w usłudze Data Lake Storage Gen2 przy użyciu gałęzi lub innych usług?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Ustawianie uprawnień na poziomie plików i katalogów przy użyciu Eksploratora usługi Azure Storage w usłudze Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Czy można zwiększyć magazyn HDFS w klastrze bez zwiększania rozmiaru dysku węzłów procesu roboczego?

Nie, nie można zwiększyć rozmiaru dysku dowolnego węzła procesu roboczego, więc jedynym sposobem zwiększenia rozmiaru dysku jest upuszczenie klastra i ponownetworzenie go przy większych maszynach wirtualnych procesu roboczego. Nie używaj systemu PLIKÓW HDFS do przechowywania danych usługi HDInsight, ponieważ dane są usuwane po usunięciu klastra. Zamiast tego należy przechowywać dane na platformie Azure. Skalowanie w górę klastra można również dodać dodatkową pojemność do klastra HDInsight.

## <a name="edge-nodes"></a>Węzły brzegowe

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Czy mogę dodać węzeł krawędzi po utworzeniu klastra?

klastra HDInsight lub do nowego klastra podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [Use empty edge nodes on Apache Hadoop clusters in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów brzegowych w klastrach Apache Hadoop w usłudze HDInsight).

### <a name="how-can-i-connect-to-an-edge-node"></a>Jak mogę połączyć się z węzłem brzegowym?

Po utworzeniu węzła krawędzi można się z nim połączyć za pomocą funkcji SSH na porcie 22. Nazwę węzła krawędzi można znaleźć w portalu klastra. Nazwy zwykle kończą się *na -ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Dlaczego utrwalone skrypty nie są uruchamiane automatycznie w nowo utworzonych węzłach brzegowych?

Skrypty utrwalone służy do dostosowywania nowych węzłów procesu roboczego dodane do klastra za pomocą operacji skalowania. Utrwalone skrypty nie mają zastosowania do węzłów brzegowych.

## <a name="rest-api"></a>Interfejs API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Jakie są wywołania interfejsu API REST, aby wyciągnąć widok kwerendy Tez z klastra?

Za pomocą następujących punktów końcowych REST można wyciągnąć niezbędne informacje w formacie JSON. Użyj podstawowych nagłówków uwierzytelniania, aby składać żądania.

- Widok kwerendy Tez: *https:\//\<nazwa klastra>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Tez Dag View: *https:\//\<nazwa klastra>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Jak pobrać szczegóły konfiguracji z klastra HDI przy użyciu użytkownika usługi Azure Active Directory?

Aby wynegocjować odpowiednie tokeny uwierzytelniania z użytkownikiem usługi AAD, przejdź przez bramę przy użyciu następującego formatu:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Jak używać Ambari Restful API do monitorowania wydajności YARN?

Jeśli wywołasz polecenie Curl w tej samej sieci wirtualnej lub w sieci wirtualnej równorzędnej, polecenie brzmi:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Jeśli wywołasz polecenie spoza sieci wirtualnej lub z sieci wirtualnej niewymrionowej, format polecenia jest:

- W przypadku klastra innych niż ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Dla klastra ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl wyświetli monit o podanie hasła. Należy wprowadzić prawidłowe hasło dla nazwy użytkownika logowania klastra.

## <a name="billing"></a>Rozliczenia

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Ile kosztuje wdrożenie klastra HDInsight?

Aby uzyskać więcej informacji na temat cen i często zadawanych pytań związanych z rozliczeniami, zobacz stronę [cennika usługi Azure HDInsight.](https://azure.microsoft.com/pricing/details/hdinsight/)

### <a name="when-does-hdinsight-billing-start--stop"></a>Kiedy rozpoczyna się & fakturowanie HDInsight?

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Rozliczenia są proporcjonalnie do na minutę.

### <a name="how-do-i-cancel-my-subscription"></a>Jak anulować subskrypcję?

Aby uzyskać informacje dotyczące anulowania subskrypcji, zobacz [Anulowanie subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Co się stanie po anulowaniu subskrypcji w przypadku subskrypcji zgodnie z rzeczywistym wynagrodzeniem?

Aby uzyskać informacje o subskrypcji po jej anulowaniu, zobacz [Co się stanie po anulowaniu subskrypcji?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Dlaczego wersja hive jest wyświetlana jako 1.2.1000 zamiast 2.1 w interfejsie użytkownika Ambari, mimo że używam klastra HDInsight 3.6?

Chociaż tylko 1.2 pojawia się w interfejsie użytkownika Ambari, HDInsight 3.6 zawiera zarówno Hive 1.2 i Hive 2.1.

## <a name="other-faq"></a>Inne często zadawane pytania

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Co oferuje usługa HDInsight pod względem możliwości przetwarzania strumienia w czasie rzeczywistym?

Aby uzyskać informacje na temat możliwości integracji przetwarzania strumienia w usłudze Azure HDInsight, zobacz [Wybieranie technologii przetwarzania strumienia na platformie Azure.](/azure/architecture/data-guide/technology-choices/stream-processing)

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Czy istnieje sposób dynamicznego zakończenia węzła głównego klastra, gdy klaster jest bezczynny przez określony okres?

Nie można tego zrobić za pomocą klastrów HDInsight. W tych scenariuszach można użyć usługi Azure Data Factory.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Jakie oferty zgodności oferuje hdinsight?

Aby uzyskać informacje o zgodności, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trust-center) i [omówienie zgodności platformy Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

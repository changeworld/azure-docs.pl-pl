---
title: Zarządzanie klastrami Hadoop w HDInsight przy użyciu witryny Azure portal
description: Dowiedz się, jak utworzyć i zarządzać klastrami HDInsight przy użyciu witryny Azure portal.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jasonh
ms.openlocfilehash: 20a48dcd4a9c3dd4c89390c1048ec4fd5f5783ae
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597212"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie klastrami Hadoop w HDInsight przy użyciu witryny Azure portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Za pomocą [witryny Azure portal][azure-portal], można zarządzać klastrami Hadoop w usłudze Azure HDInsight. Selektor karty powyżej, aby uzyskać informacje na temat zarządzania klastrami Hadoop w HDInsight przy użyciu innych narzędzi.

**Wymagania wstępne**

Aby wykonać kroki opisane w tym artykule, konieczne będzie **subskrypcji platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Otwórz witrynę Azure portal
1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Po otwarciu portalu, możesz wykonywać następujące czynności:

   * Kliknij przycisk **Utwórz zasób** menu po lewej stronie, aby utworzyć nowy klaster:

       ![przycisk Nowy klaster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Wprowadź **HDInsight** w **Przeszukaj witrynę Marketplace**, kliknij przycisk **HDInsight**, a następnie kliknij przycisk **Utwórz**.

   * Kliknij przycisk **klastry HDInsight** menu po lewej stronie, aby wyświetlić listę istniejących klastrów:

       ![Usługa Azure HDInsight klastra przycisku w portalu](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Jeśli nie widzisz **klastry HDInsight** przycisk, a następnie kliknij przycisk **klastry HDInsight** w obszarze **rozwiązania inteligentne + analiza** sekcji.


## <a name="create-clusters"></a>Tworzenie klastrów
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight działa dzięki składnikom szeroki zakres usługi Hadoop. Aby uzyskać listę składników, które są zweryfikowane i obsługiwane, zobacz [jakiej wersji usługi Hadoop jest w usłudze Azure HDInsight?](hdinsight-component-versioning.md) Aby uzyskać informacje o tworzeniu klastra ogólne, zobacz [Tworzenie klastrów usługi Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Wymagania dotyczące kontroli dostępu

Podczas tworzenia klastra usługi HDInsight, należy określić subskrypcji platformy Azure. W nowej grupie zasobów platformy Azure lub w istniejącej grupie zasobów można utworzyć klastra. Sprawdź swoje uprawnienia do tworzenia klastrów HDInsight umożliwia następujące czynności:

- Aby utworzyć nową grupę zasobów:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Kliknij przycisk **subskrypcji** menu po lewej stronie. Posiada żółtą ikonę klucza. Zobaczysz listę subskrypcji.
    3. Kliknij subskrypcję, która umożliwia tworzenie klastrów. 
    4. Kliknij przycisk **Moje uprawnienia**.  Prezentuje swoje [roli](../role-based-access-control/built-in-roles.md) w ramach subskrypcji. Potrzebujesz przynajmniej prawa dostępu współautora do utworzenia klastra HDInsight.

- Aby użyć istniejącej grupy zasobów:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Kliknij przycisk **grup zasobów** menu po lewej stronie, aby wyświetlić listę grup zasobów.
    3. Kliknij grupę zasobów, których chcesz użyć do tworzenia klastra usługi HDInsight.
    4. Kliknij przycisk **kontrola dostępu (IAM)** i upewnij się, że użytkownik (lub należeć do grupy) ma co najmniej dostęp współautora do grupy zasobów.

Jeśli wystąpi błąd NoRegisteredProviderFound lub błąd MissingSubscriptionRegistration, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Listy i wyświetlaniu klastrów
1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Kliknij przycisk **klastry HDInsight** menu po lewej stronie, aby wyświetlić listę istniejących klastrów. Jeśli nie widzisz **klastry HDInsight**, kliknij przycisk **wszystkich usług** pierwszy.
3. Kliknij nazwę klastra. Jeśli lista klastra jest długa, możesz użyć filtrów w górnej części strony.
4. Kliknij klaster z listy, aby znaleźć na stronie przeglądu:

    ![Azure portal essentials klastra HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **menu Przegląd:**
    * **Pulpit nawigacyjny**: Otwiera interfejs webowy Ambari klastra.
    * **Bezpieczna powłoka**: zawiera instrukcje łączenia z klastrem przy użyciu połączenia protokołu Secure Shell (SSH).
    * **Skalowanie klastra**: umożliwia zmianę liczby węzłów procesu roboczego dla tego klastra.
    * **Przenieś**: Przenosi klastra do innej grupy zasobów lub do innej subskrypcji.
    * **Usuń**: usuwa klaster.

    **Menu po lewej stronie:**
    * **Dzienniki aktywności**: Pokaż i zapytań dzienników aktywności.
    * **Kontrola dostępu (IAM)**: za pomocą przypisań ról.  Zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md).
    * **Tagi**: umożliwia ustawienie pary klucz/wartość, aby zdefiniować taksonomię niestandardowych usług w chmurze. Na przykład, może utworzyć klucz o nazwie **projektu**, a następnie użyj wspólnej wartości dla wszystkich usług skojarzone z określonego projektu.
    * **Diagnozowanie i rozwiązywanie problemów**: wyświetlić informacje dotyczące rozwiązywania problemów.
    * **Blokuje**: Dodaj blokadę, aby zapobiec, przy czym klastra, zmodyfikowane lub usunięte.
    * **Skrypt automatyzacji**: wyświetlanie i eksportowanie szablonu usługi Azure Resource Manager dla klastra. Obecnie można wyeksportować tylko konta magazynu Azure zależnego. Zobacz [opartych na systemie Linux z Tworzenie klastrów usługi Hadoop w HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Przewodnik Szybki Start**: Wyświetla informacje, które ułatwia rozpoczęcie korzystania z HDInsight.
    * **Narzędzia Tools for HDInsight**: narzędzia pokrewne informacje pomocnicze o HDInsight.
    * **Użycie rdzeni subskrypcji**: wyświetlić rdzeni używane i dostępne dla Twojej subskrypcji.
    * **Skalowanie klastra**: zwiększyć i zmniejszyć liczbę węzłów procesu roboczego klastra. Zobacz[Skaluj klastry](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **Logowania do klastra i protokołu SSH**: zawiera instrukcje łączenia z klastrem przy użyciu połączenia protokołu Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **HDInsight Partner**: Dodaj/Usuń bieżącego partnera HDInsight.
    * **Zewnętrzne magazyny metadanych**: Wyświetl magazyny metadanych Hive i Oozie. Magazyny metadanych można skonfigurować tylko podczas procesu tworzenia klastra. Zobacz [Użyj Magazyn metadanych Hive/programu Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Akcji skryptu**: uruchamianie programu Bash skryptów w klastrze. Zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).
    * **Aplikacje**: HDInsight dodawania/usuwania aplikacji.  Zobacz [instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md).
    * **Monitorowanie**: monitorowanie klastra w usłudze Azure Log Analytics.
    * **Właściwości**: wyświetlanie właściwości klastra.
    * **Konta magazynu**: wyświetlić klucze i kont magazynu. Konta magazynu są skonfigurowane w trakcie procesu tworzenia klastra.
    * **Data Lake Store dostępu**: Konfigurowanie dostępu są przechowywane w usłudze Data Lake.  Zobacz [Szybki Start: konfigurowanie klastrów w HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
    * **Usługa Resource health**: zobacz [Przegląd kondycji zasobów platformy Azure](../service-health/resource-health-overview.md).
    * **Nowe żądanie pomocy technicznej**: pozwala na tworzenie biletu pomocy technicznej za pomocą techniczną firmy Microsoft.
    
6. Kliknij przycisk **właściwości**:

    Właściwości są następujące:

   * **Nazwa hosta**: Nazwa klastra.
   * **Adres URL klastra**: adres URL dla interfejsu sieci web Ambari.
   * **Bezpieczna powłoka (SSH)**: nazwa użytkownika i hosta do użycia podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH.
   * **Stan**: jeden z: przerwany, akceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacyjne, uruchomione, błąd, usuwanie, usunięte, przekroczenie limitu czasu, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued lub ClusterCustomization.
   * **Region**: Lokalizacja platformy Azure. Aby uzyskać listę obsługiwanych lokalizacji platformy Azure, zobacz **Region** lista rozwijana na [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data utworzenia**: data została wdrożona w klastrze.
   * **System operacyjny**: albo **Windows** lub **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Wersja**. Zobacz [wersji HDInsight](hdinsight-component-versioning.md).
   * **Subskrypcja**: Nazwa subskrypcji.
   * **Domyślne źródło danych**: domyślnego systemu plików klastra.
   * **Rozmiar węzłów procesu roboczego**: wybrany rozmiar maszyny Wirtualnej węzłów procesu roboczego.
   * **Rozmiar węzła w elemencie head**: wybrany rozmiar maszyny Wirtualnej węzłów głównych.
   * **Sieć wirtualna**: Nazwa sieci wirtualnej, która jest wdrażany klaster, jeśli wybrano jeden w czasie wdrażania.

## <a name="delete-clusters"></a>Usuwanie klastrów
Usunięcie klastrów nie powoduje usunięcia domyślne konto magazynu, ani wszystkie połączone konta magazynu. Można ponownie utworzyć klaster przy użyciu tego samego konta magazynu i samej magazyny metadanych. Zalecamy używanie nowego domyślnego kontenera obiektów Blob podczas ponownego tworzenia klastra.

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **klastry HDInsight** menu po lewej stronie. Jeśli nie widzisz **klastry HDInsight**, kliknij przycisk **wszystkich usług** pierwszy.
3. Kliknij klaster, który chcesz usunąć.
4. Kliknij przycisk **Usuń** z górnego menu, a następnie postępuj zgodnie z instrukcjami.

Zobacz też [Pause/zamykania klastrów](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Po utworzeniu klastra możesz dodać dodatkowe konta usługi Azure Storage i kont usługi Azure Data Lake Store. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używane przez klaster usługi HDInsight na platformie Azure bez konieczności ponownego tworzenia klastra.

> [!NOTE]
> Tylko klastry HDInsight w wersji 3.1.3 lub nowszej są obsługiwane. Jeśli masz pewności, jaka wersja klastra, możesz sprawdzić na stronie właściwości.  Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).
>
>

Wpływ zmiany liczby węzłów danych różni się dla każdego typu klastra obsługiwane przez HDInsight:

* Hadoop

    Możesz bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze usługi Hadoop, w którym jest uruchomiony bez wywierania wpływu na wszystkie oczekujące lub uruchomione zadania. Nowe zadania należy dostarczyć również w przypadku, gdy operacja jest w toku. Błędy trwaniem skalowania bez problemu zmieniała są obsługiwane, dzięki czemu klaster zawsze pozostanie w stanie działać.

    Gdy klaster Hadoop jest skalowane w dół dzięki zmniejszeniu liczby węzłów danych, zostaną ponownie uruchomione niektóre z tych usług w klastrze. To zachowanie powoduje, że wszystkie uruchomione i oczekujące zadania na zakończenie operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* HBase

    Możesz bezproblemowo Dodawanie lub usuwanie węzłów do klastra HBase jest uruchomiona. Serwery regionalne automatycznie są równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie równoważyć serwerów regionalnych, logując się do węzła głównego klastra i uruchamiając następujące polecenia z okna wiersza polecenia:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Aby uzyskać więcej informacji na temat korzystania z powłoki HBase, zobacz [Rozpoczynanie pracy z przykładem bazy danych Apache HBase w HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Bezproblemowo można dodać lub usunąć węzły danych z klastrem Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.

    Ponowne równoważenie może się odbywać na dwa sposoby:

  * Interfejs użytkownika sieci web systemu STORM
  * Narzędzia interfejsu wiersza polecenia (CLI)

    Zapoznaj się [dokumentacji platformy Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) Aby uzyskać więcej informacji.

    Interfejs użytkownika sieci web systemu Storm jest dostępny w klastrze HDInsight:

    ![Ponowne równoważenie skalowania HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Oto przykład polecenia interfejsu wiersza polecenia, aby ponowne zrównoważenie topologii systemu Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Skalowanie klastrów**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **klastry HDInsight** menu po lewej stronie.
3. Kliknij klaster, który chcesz skalować.
3. Kliknij przycisk **skalowanie klastra**.
4. Wprowadź **węzłów liczba procesów roboczych**. Limit liczby węzłów klastra waha się między subskrypcjami platformy Azure. Można skontaktuj się z działem pomocy technicznej dotyczącej rozliczeń w celu zwiększenia limitu.  Informacje o kosztach odzwierciedla zmiany wprowadzone do liczby węzłów.

    ![HDInsight hadoop hbase storm spark skalowania](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Wstrzymywanie/zamykania klastrów

Większość zadań usługi Hadoop to zadania wsadowe, które działają tylko od czasu do czasu. Większość klastrów platformy Hadoop istnieją duże okresy czasu, przez jaki klastra nie jest używany do przetwarzania. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany.
Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Istnieje wiele sposobów, można programować procesu:

* Użytkownik usługi Azure Data Factory. Zobacz [tworzenie na żądanie opartą na systemie Linux platformą Hadoop clusters w HDInsight przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) tworzenia HDInsight na żądanie połączonych usług.
* Używanie programu Azure PowerShell.  Zobacz [analizowanie danych dotyczących opóźnień lotów](hdinsight-analyze-flight-delay-data.md).
* Użyj wiersza polecenia platformy Azure. Zobacz [HDInsight Zarządzanie klastrami przy użyciu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md).
* Użyj zestawu .NET SDK HDInsight. Zobacz [zadań Hadoop przesłać](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z poziomu portalu, zobacz [usuwać klastry](#delete-clusters)

## <a name="move-cluster"></a>Przenieś klastra

Klaster usługi HDInsight można przenieść do innej grupy zasobów platformy Azure lub innej subskrypcji.  Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Zobacz [klastra HDInsight uaktualnienia do nowszej wersji](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci web systemu Ambari

Ambari udostępnia intuicyjne, łatwe w użyciu Hadoop zarządzania interfejsu użytkownika sieci web obsługiwane przez jego interfejsów API RESTful. Ambari umożliwia administratorom systemu monitorowania klastrów platformy Hadoop oraz zarządzania nimi.

1. Otwórz klaster usługi HDInsight w witrynie Azure portal.  Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).
2. Kliknij przycisk **klastra pulpit nawigacyjny**.

    ![Menu klastra usługi HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Wprowadź nazwę użytkownika klastra i hasło.  Domyślna nazwa użytkownika klastra _administratora_. Ambari web UI wygląda następująco:

    ![Interfejs użytkownika sieci Web Ambari HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Aby uzyskać więcej informacji, zobacz [HDInsight Zarządzanie klastrami przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Zmienianie haseł
Klaster usługi HDInsight może mieć dwóch kont użytkowników. HDInsight cluster (zwany również konto użytkownika Konto użytkownika HTTP) i konto użytkownika SSH są tworzone podczas procesu tworzenia. Interfejs webowy Ambari służy do zmiany nazwy użytkownika konta użytkownika klastra i hasło i akcji skryptu, aby zmienić konto użytkownika SSH

### <a name="change-the-cluster-user-password"></a>Zmień hasło użytkownika klastra
Interfejs użytkownika sieci Web Ambari służy do zmiany hasła użytkownika klastra. Aby zalogować się do systemu Ambari, należy użyć istniejącego klastra nazwy użytkownika i hasło.

> [!NOTE]
> Zmiana hasła użytkownika (administratora) klastra może spowodować akcji skryptu, uruchamiać ten klaster nie powiedzie się. Jeśli masz wszystkie akcje utrwalonego skryptu tej węzłów procesu roboczego w docelowej tych skryptów może zakończyć się niepowodzeniem Kiedy dodajesz węzły do klastra za pośrednictwem rozmiar operacji. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Zaloguj się do Interfejsu sieci Web Ambari, przy użyciu poświadczeń użytkownika klastra HDInsight. Domyślna nazwa użytkownika to **admin**. Adres URL jest **https://&lt;nazwy klastra HDInsight > azurehdinsight.net**.
2. Kliknij przycisk **administratora** z górnego menu, a następnie kliknij pozycję "Zarządzaj Ambari".
3. W menu po lewej stronie kliknij **użytkowników**.
4. Kliknij przycisk **administratora**.
5. Kliknij przycisk **zmiany hasła**.

Ambari następnie zmienia hasło we wszystkich węzłach w klastrze.

### <a name="change-the-ssh-user-password"></a>Zmień hasło użytkownika SSH
1. Za pomocą edytora tekstów, Zapisz poniższy tekst w pliku o nazwie **changepassword.sh**.

    > [!IMPORTANT]
    > Należy użyć edytora, który używa LF, jako koniec wiersza. Jeśli Edytor używa CRLF, następnie skrypt nie działa.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Przekaż plik do lokalizacji magazynu, który jest możliwy z HDInsight przy użyciu adresu protokołu HTTP lub HTTPS. Na przykład plik publicznego przechowywanie takich jak OneDrive lub Azure Blob storage. Zapisz identyfikator URI (adres HTTP lub HTTPS) do pliku, ponieważ ten identyfikator URI jest potrzebna w następnym kroku.
3. W witrynie Azure portal, kliknij przycisk **klastry HDInsight**.
4. Kliknij klaster usługi HDInsight.
4. Kliknij przycisk **akcji skryptu**.
4. Z **akcji skryptu** bloku wybierz **Prześlij nową**. Gdy **Prześlij akcję skryptu** zostanie wyświetlony blok, wprowadź następujące informacje:

   | Pole | Wartość |
   | --- | --- |
   | Name (Nazwa) |Zmienianie ssh hasła |
   | Identyfikator URI skryptu powłoki systemowej |Identyfikator URI pliku changepassword.sh |
   | Węzły (Head procesu roboczego, Nimbus, nadzorca, Zookeeper, itp.) |✓ dla wszystkich typów węzła na liście |
   | Parametry |Wprowadź nazwę użytkownika SSH, a następnie nowe hasło. Powinna być jedną spację między nazwę użytkownika i hasło. |
   | Utrwal tę akcję skryptu... |Nie zaznaczaj tego pola wyboru. |
5. Wybierz **Utwórz** zastosować skrypt. Po zakończeniu działania skryptu jesteś w stanie połączyć się z klastrem przy użyciu protokołu SSH przy użyciu nowego hasła.

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight mają następujące usługi sieci web HTTP (wszystkie te usługi mają punktów końcowych RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/Udziel dostępu za pomocą [wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) i [programu Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Znajdź identyfikator subskrypcji

**Aby znaleźć identyfikatory subskrypcji platformy Azure**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **subskrypcje**. Każda subskrypcja ma nazwę i identyfikator.

Każdy klaster jest powiązany z subskrypcją platformy Azure. Subskrypcji, identyfikator jest wyświetlany w klastrze **Essential** kafelka. Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Znajdź grupę zasobów
W trybie usługi Azure Resource Manager każdy klaster HDInsight jest tworzony z grupy usługi Azure Resource Manager. Grupy usługi Resource Manager, do której należy klaster jest wyświetlany w:

* Lista klastra ma **grupy zasobów** kolumny.
* Klaster **Essential** kafelka.  

Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Znajdowanie konta magazynu

Klastry HDInsight Użyj konta usługi Azure Storage lub Azure Data Lake Store do przechowywania danych. Każdy klaster HDInsight może mieć jeden domyślne konto magazynu i liczba połączonych kontach magazynu. Aby wyświetlić listę kont magazynu, najpierw otwórz klaster z poziomu portalu, a następnie kliknij **kont magazynu**:

![Konta magazynu w klastrze HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

Na poprzednim zrzucie ekranu jest __domyślne__ kolumny, wskazując, czy konto jest domyślne konto magazynu.

Aby wyświetlić listę kont Data Lake Store, kliknij przycisk **dostępu Data Lake Store** na poprzednim zrzucie ekranu.

## <a name="run-hive-queries"></a>Uruchamianie zapytań Hive
Nie można uruchomić zadania Hive bezpośrednio w witrynie Azure portal, ale można użyć programu Hive widoku w interfejsie użytkownika sieci Web Ambari.

**Uruchamianie zapytań Hive przy użyciu widoku Hive narzędzia Ambari**

1. Zaloguj się do Interfejsu sieci Web Ambari, przy użyciu poświadczeń użytkownika klastra HDInsight. Domyślna nazwa użytkownika to **admin**. Adres URL jest **https://&lt;nazwy klastra HDInsight > azurehdinsight.net**.
2. Otwórz widok programu Hive, jak pokazano na poniższym zrzucie ekranu:  

    ![HDInsight hive widoku](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Kliknij przycisk **zapytania** z górnego menu.
4. Wprowadź zapytanie programu Hive w **edytora zapytań**, a następnie kliknij przycisk **Execute**.

## <a name="monitor-jobs"></a>Monitorowanie zadań
Zobacz [HDInsight Zarządzanie klastrami przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Przeglądaj pliki
W witrynie Azure portal, możesz przeglądać zawartość domyślny kontener.

1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Kliknij przycisk **klastry HDInsight** menu po lewej stronie, aby wyświetlić listę istniejących klastrów.
3. Kliknij nazwę klastra. Jeśli lista klastra jest długa, możesz użyć filtrów w górnej części strony.
4. Kliknij przycisk **kont magazynu** menu po lewej stronie w klastrze.
5. Kliknij konto magazynu.
7. Kliknij przycisk **obiektów blob** kafelka.
8. Kliknij domyślną nazwę kontenera.

## <a name="monitor-cluster-usage"></a>Monitorowanie użycia klastra
**Użycia** części bloku klastra HDInsight Wyświetla informacje o liczba rdzeni dostępnych do Twojej subskrypcji do użycia z usługą HDInsight, a także liczby rdzeni przydzielonych do tego klastra i jak są przydzielane dla węzłów w klastrze. Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

> [!IMPORTANT]
> Aby monitorować usługi świadczone przez klaster usługi HDInsight, należy użyć sieci Ambari Web lub interfejs API REST Ambari. Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Łączenie z klastrem

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule mają przedstawiono niektóre podstawowe funkcje administracyjne. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie przy użyciu programu PowerShell usługi Azure HDInsight](hdinsight-administer-use-powershell.md)
* [Administrowanie przy użyciu wiersza polecenia platformy Azure HDInsight](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Dowiedz się więcej o korzystaniu z interfejsu użytkownika sieci Web systemu Ambari](hdinsight-hadoop-manage-ambari.md)
* [Szczegółowe informacje na temat korzystania z interfejsu API REST systemu Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Use Hive in HDInsight używanie](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig HDInsight](hadoop/hdinsight-use-pig.md)
* [W HDInsight przy użyciu narzędzia Sqoop](hadoop/hdinsight-use-sqoop.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jakiej wersji usługi Hadoop znajduje się w usłudze Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Wiersza polecenia usługi Hadoop"

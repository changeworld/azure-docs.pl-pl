---
title: Zarządzanie klastrami Apache Hadoop oparte na Windows w HDInsight przy użyciu witryny Azure portal
description: Dowiedz się, jak do administrowania usługą HDInsight. Tworzenie klastra usługi HDInsight, otwórz interakcyjnej konsoli języka JavaScript i Otwórz konsolę polecenia Apache Hadoop.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 4210528003cdb1f584bec3dea80c1aa1db2f86df
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632029"
---
# <a name="manage-windows-based-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie klastrami Apache Hadoop oparte na Windows w HDInsight przy użyciu witryny Azure portal

Za pomocą [witryny Azure portal][azure-portal], można tworzyć oparte na Windows [Apache Hadoop](https://hadoop.apache.org/) klastrów w usłudze Azure HDInsight, Zmień hasło użytkownika usługi Hadoop i Włącz protokół Remote Desktop Protocol ( Protokół RDP) umożliwi dostęp z konsoli poleceń platformy Hadoop w klastrze.

Informacje przedstawione w tym artykule dotyczy tylko klastry HDInsight oparty na oknach. Aby uzyskać informacji dotyczących zarządzania opartego na systemie Linux klastrów, zobacz [Zarządzanie Apache Hadoop clusters w HDInsight przy użyciu witryny Azure portal](hdinsight-administer-use-portal-linux.md).

[!INCLUDE [windows-retirement-notice](../../includes/windows-retirement-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Konto usługi Azure Storage** — klaster HDInsight używa kontenera usługi Azure Blob storage jako domyślnego systemu plików. Aby uzyskać więcej informacji na temat sposobu usługi Azure Blob storage zapewnia bezproblemową obsługę z klastrami HDInsight, zobacz [użycia usługi Azure Blob Storage za pomocą HDInsight](hdinsight-hadoop-use-blob-storage.md). Aby uzyskać więcej informacji na temat tworzenia konta usługi Azure Storage, zobacz [sposób tworzenia konta magazynu](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Otwórz Portal
1. Zaloguj się do witryny [https://portal.azure.com](https://portal.azure.com).
2. Po otwarciu portalu, możesz wykonywać następujące czynności:

   * Kliknij przycisk **Utwórz zasób** menu po lewej stronie, aby utworzyć nowy klaster:

       ![przycisk Nowy klaster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Kliknij przycisk **klastry HDInsight** menu po lewej stronie.

       ![Usługa Azure HDInsight klastra przycisku w portalu](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Jeśli **HDInsight** nie są wyświetlane w menu po lewej stronie kliknij pozycję **Przeglądaj**.

     ![Azure portal przycisk przeglądania klastra](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Tworzenie klastrów
Aby uzyskać instrukcje tworzenia przy użyciu portalu, zobacz [klastrów HDInsight tworzenie](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight działa ze składnikami szerokiego zakresu technologii Apache Hadoop. Aby uzyskać listę składników, które zostały zweryfikowane i obsługiwane, zobacz [jakiej wersji programu Apache Hadoop jest w usłudze Azure HDInsight](hdinsight-component-versioning.md). HDInsight można dostosować przy użyciu jednej z następujących opcji:

* Użyj akcji skryptu, aby uruchomić skrypty niestandardowe, które można dostosować klaster, aby zmienić konfigurację klastra lub instalowanie składników niestandardowych, takich jak system Giraph i Solr. Aby uzyskać więcej informacji, zobacz [klastra HDInsight dostosować za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md).
* Podczas tworzenia klastra, należy użyć parametrów dostosowania klastra HDInsight .NET SDK lub programu Azure PowerShell. Te zmiany w konfiguracji są następnie zachowywane czas życia klastra i nie dotyczy klastra węzeł odtwarza z obrazu, które platforma Azure przeprowadza okresowo konserwacji. Aby uzyskać więcej informacji na temat korzystania z parametrów dostosowywania klastra, zobacz [klastrów HDInsight tworzenie](hdinsight-hadoop-provision-linux-clusters.md).
* Niektóre składnikami macierzystymi Java, takich jak [Apache Mahout](https://mahout.apache.org/) i [usuwania kaskadowego](https://www.cascading.org/), można uruchomić w klastrze jako pliki JAR. Te pliki JAR można przekazany do usługi Azure Blob storage i przesłane do klastrów HDInsight za pomocą mechanizmów przesyłanie zadań Hadoop. Aby uzyskać więcej informacji, zobacz [przesłać Apache Hadoop zadań programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]  
  > Jeśli masz problemy dotyczące wdrażania plików JAR, korzystając z klastrami HDInsight lub wywoływania pliki JAR w klastrach HDInsight, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).
  >
  > Kaskadowe nie jest obsługiwana przez HDInsight, a nie kwalifikuje się do firmy Microsoft Support. Aby uzyskać listę obsługiwanych składników, zobacz [nowości w wersjach klastra, dostarczone przez HDInsight](hdinsight-component-versioning.md).

Instalacja oprogramowania niestandardowego w klastrze za pomocą połączenia pulpitu zdalnego nie jest obsługiwana. Należy unikać przechowywanie wszystkich plików na dyskach węzła głównego, ponieważ zostaną one utracone jeśli trzeba będzie ponownie utworzyć klastry. Zalecane jest przechowywanie plików w usłudze Azure Blob storage. Magazyn obiektów blob jest trwały.

## <a name="list-and-show-clusters"></a>Listy i wyświetlaniu klastrów
1. Zaloguj się do witryny [https://portal.azure.com](https://portal.azure.com).
2. Kliknij przycisk **klastry HDInsight** menu po lewej stronie.
3. Kliknij nazwę klastra. Jeśli lista klastra jest długa, możesz użyć filtrów w górnej części strony.
4. Kliknij dwukrotnie klastra z listy, aby wyświetlić jego szczegóły.

    **Menu i essentials**:

    ![Podstawy klastra HDInsight portalu Azure](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Aby dostosować menu, kliknij prawym przyciskiem myszy w dowolnym miejscu w menu, a następnie kliknij przycisk **Dostosuj**.
   * **Ustawienia** i **wszystkie ustawienia**: Wyświetla **ustawienia** blok dla klastra, który umożliwia dostęp do szczegółowych informacji dla klastra.
   * **Pulpit nawigacyjny**, **klastra pulpit nawigacyjny** i **adresu URL**: Są to wszystkie sposoby dostępu pulpit nawigacyjny klastra, który jest sieci Ambari Web w przypadku klastrów opartych na systemie Linux.
   * **Bezpieczna powłoka**: Zawiera instrukcje łączenia z klastrem przy użyciu połączenia protokołu Secure Shell (SSH).
   * **Skalowanie klastra**: Umożliwia zmianę liczby węzłów procesu roboczego dla tego klastra.
   * **Usuń**: Usuwa klaster.
   * **Przewodnik Szybki Start**: Wyświetla informacje, które pomogą Ci rozpocząć pracę, przy użyciu HDInsight.
   * **Użytkownicy**: Można ustawić uprawnienia dla *portalu zarządzania* klastra dla innych użytkowników w Twojej subskrypcji platformy Azure.

     > [!IMPORTANT]  
     > To *tylko* dotyczy dostępu i uprawnień do tego klastra w witrynie Azure portal i nie ma wpływu na kto może nawiązać połączenia lub przesyłania zadań do klastra HDInsight.

   * **Tagi**: Tagi umożliwiają ustawienie pary klucz/wartość, aby zdefiniować taksonomię niestandardowych usług w chmurze. Na przykład, może utworzyć klucz o nazwie **projektu**, a następnie użyj wspólnej wartości dla wszystkich usług skojarzone z określonego projektu.
   * **Widoki Ambari**: Zawiera łącza do sieci Web Ambari.

     > [!IMPORTANT]  
     > Aby zarządzać usługi świadczone przez klaster usługi HDInsight, należy użyć sieci Ambari Web lub interfejs API REST Ambari. Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md).

     **Użycie**:

     ![Użycie klastra HDInsight portalu platformy Azure](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Kliknij pozycję **Ustawienia**.

    ![Użycie klastra HDInsight portalu platformy Azure](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Właściwości**: Wyświetl właściwości klastra.
   * **Klaster tożsamości usługi AAD**:
   * **Klucze usługi Azure Storage**: Wyświetl domyślne konto magazynu i klucza. Konto magazynu jest konfiguracji podczas procesu tworzenia klastra.
   * **Klaster logowania**: Zmień nazwę użytkownika klastra HTTP i hasło.
   * **Zewnętrzne magazyny metadanych**: Widok [Apache Hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/) magazyny metadanych. Magazyny metadanych można skonfigurować tylko podczas procesu tworzenia klastra.
   * **Skalowanie klastra**: Zwiększać i zmniejszać liczbę węzłów procesu roboczego klastra.
   * **Pulpit zdalny**: Włączanie i wyłączanie dostępu zdalnego (RDP) pulpitu i konfigurowanie nazwy użytkownika protokołu RDP.  Nazwa użytkownika protokołu RDP musi być inna niż nazwa użytkownika protokołu HTTP.
   * **Partner of Record**:

     > [!NOTE]  
     > Jest to ogólny listą dostępnych ustawień; nie wszystkie z nich będzie stosowany w przypadku wszystkich typów klastra.

6. Kliknij przycisk **właściwości**:

    Sekcja właściwości zawiera następujące elementy:

   * **Nazwa hosta**: Nazwa klastra.
   * **Adres URL klastra**.
   * **Stan**: Obejmują zostało przerwane, akceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacyjne, uruchomione, błąd, usuwanie, usunięte, przekroczenie limitu czasu, DeleteQueued, DeleteTimedout, DeleteError PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Region**: Lokalizacja platformy Azure. Aby uzyskać listę obsługiwanych lokalizacji platformy Azure, zobacz **Region** lista rozwijana na [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Dane utworzone**.
   * **System operacyjny**: Albo **Windows** lub **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Wersja**. Zobacz [wersji HDInsight](hdinsight-component-versioning.md)
   * **Subskrypcja**: Nazwa subskrypcji.
   * **Identyfikator subskrypcji**.
   * **Podstawowe źródło danych**. Konto usługi Azure Blob storage używany jako domyślny system plików Hadoop.
   * **Węzły procesu roboczego warstwy cenowej**.
   * **Węzeł główny warstwy cenowej**.

## <a name="delete-clusters"></a>Usuwanie klastrów
Usuwanie klastra nie spowoduje usunięcia, domyślne konto magazynu lub wszystkie połączone konta magazynu. Można ponownie utworzyć klaster przy użyciu tego samego konta magazynu i samej magazyny metadanych.

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystkie** w menu po lewej stronie kliknij **klastry HDInsight**, kliknij swoją nazwę użytkownika klastra.
3. Kliknij przycisk **Usuń** z górnego menu, a następnie postępuj zgodnie z instrukcjami.

Zobacz też [Pause/zamykania klastrów](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używany przez klaster, który jest uruchamiany w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]  
> Tylko klastry HDInsight w wersji 3.1.3 lub nowszej są obsługiwane. Jeśli masz pewności, jaka wersja klastra, możesz sprawdzić na stronie właściwości.  Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

Wpływ zmianę liczby węzłów danych dla każdego typu klastra obsługiwane przez HDInsight:

* Apache Hadoop

    Możesz bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze usługi Hadoop, w którym jest uruchomiony bez wywierania wpływu na wszystkie oczekujące lub uruchomione zadania. Nowe zadania należy dostarczyć również w przypadku, gdy operacja jest w toku. Błędy trwaniem skalowania bez problemu zmieniała są obsługiwane, dzięki czemu klaster zawsze pozostanie w stanie działać.

    Gdy klaster Hadoop jest skalowane w dół dzięki zmniejszeniu liczby węzłów danych, zostaną ponownie uruchomione niektóre z tych usług w klastrze. To powoduje, że wszystkie uruchomione i oczekujące zadania na zakończenie operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* Apache HBase

    Możesz bezproblemowo Dodawanie lub usuwanie węzłów do klastra HBase jest uruchomiona. Serwery regionalne automatycznie są równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie równoważyć serwerów regionalnych, logując się do węzła głównego klastra i uruchamiając następujące polecenia z okna wiersza polecenia:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Aby uzyskać więcej informacji na temat korzystania z powłoki HBase Zobacz]
* Apache Storm

    Bezproblemowo można dodać lub usunąć węzły danych z klastrem Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.

    Ponowne równoważenie może się odbywać na dwa sposoby:

  * Interfejs użytkownika sieci web Apache Storm
  * Narzędzia interfejsu wiersza polecenia (CLI)

    Zapoznaj się [dokumentacji platformy Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) Aby uzyskać więcej informacji.

    Interfejs użytkownika sieci web systemu Storm jest dostępny w klastrze HDInsight:

    ![HDInsight storm skalowania w ponownego równoważenia](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Oto przykład jak ponowne zrównoważenie topologii Storm za pomocą polecenia interfejsu wiersza polecenia:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Skalowanie klastrów**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystkie** w menu po lewej stronie kliknij **klastry HDInsight**, kliknij swoją nazwę użytkownika klastra.
3. Kliknij przycisk **ustawienia** z górnego menu, a następnie kliknij **Skaluj klaster**.
4. Wprowadź **węzłów liczba procesów roboczych**. Limit liczby węzłów klastra waha się między subskrypcjami platformy Azure. Można skontaktuj się z działem pomocy technicznej dotyczącej rozliczeń w celu zwiększenia limitu.  Informacje o kosztach odzwierciedlają zmiany wprowadzone do liczby węzłów.

    ![Skala HDInsight Hadoop HBase Storm Spark](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Wstrzymywanie/zamykania klastrów
Większość zadań usługi Hadoop to zadania wsadowe, które są tylko od czasu do czasu do uruchomienia. Większość klastrów platformy Hadoop istnieją duże okresy czasu, przez jaki klastra nie jest używany do przetwarzania. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany.
Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Istnieje wiele sposobów, można programować procesu:

* Użytkownik usługi Azure Data Factory. Zobacz [połączona usługa HDInsight Azure](../data-factory/compute-linked-services.md) i [transformacji i analizy przy użyciu usługi Azure Data Factory](../data-factory/transform-data.md) HDInsight na żądanie i samodzielnie określonych połączonej usługi.
* Używanie programu Azure PowerShell.  Zobacz [analizowanie danych dotyczących opóźnień lotów](hdinsight-analyze-flight-delay-data.md).
* Użyj klasycznego wiersza polecenia platformy Azure. Zobacz [HDInsight Zarządzanie klastrami przy użyciu klasyczny interfejs wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md).
* Użyj zestawu .NET SDK HDInsight. Zobacz [zadania przesyłania Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z poziomu portalu, zobacz [usuwać klastry](#delete-clusters)

## <a name="change-cluster-username"></a>Zmień nazwę użytkownika z klastra
Klaster usługi HDInsight może mieć dwóch kont użytkowników. Konto użytkownika klastra HDInsight jest tworzony podczas procesu tworzenia. Można również utworzyć konto użytkownika protokołu RDP do uzyskiwania dostępu do klastra za pośrednictwem protokołu RDP. Zobacz [Włącz pulpit zdalny](#connect-to-hdinsight-clusters-by-using-rdp).

**Aby zmienić nazwę użytkownika klastra HDInsight i hasło**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystkie** w menu po lewej stronie kliknij **klastry HDInsight**, kliknij swoją nazwę użytkownika klastra.
3. Kliknij przycisk **ustawienia** z górnego menu, a następnie kliknij **logowania do klastra**.
4. Jeśli **logowania do klastra** została włączona, należy kliknąć opcję **wyłączyć**, a następnie kliknij przycisk **włączyć** przed zmianą nazwy użytkownika i hasła.
5. Zmiana **nazwa logowania klastra** i/lub **hasło logowania klastra**, a następnie kliknij przycisk **Zapisz**.

    ![HDInsight zmiany użytkownika http hasło username użytkownika klastra](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight mają następujące usługi sieci web HTTP (wszystkie te usługi mają punktów końcowych RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/Udziel dostępu w witrynie Azure portal.

> [!NOTE]  
> Przez przyznanie/odbieranie prawa dostępu, możesz zresetować klastra, nazwę użytkownika i hasło.

**Do przydzielenia/odwołania dostępu do usług sieci web HTTP**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystkie** w menu po lewej stronie kliknij **klastry HDInsight**, kliknij swoją nazwę użytkownika klastra.
3. Kliknij przycisk **ustawienia** z górnego menu, a następnie kliknij **logowania do klastra**.
4. Jeśli **logowania do klastra** została włączona, należy kliknąć opcję **wyłączyć**, a następnie kliknij przycisk **włączyć** przed zmianą nazwy użytkownika i hasła.
5. Aby uzyskać **nazwa użytkownika logowania klastra** i **hasło logowania klastra**, wprowadź nową nazwę użytkownika i hasło (odpowiednio) dla klastra.
6. Kliknij przycisk **SAVE** (Zapisz).

    ![HDInsight sumy usunąć dostęp do usługi sieci web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Znajdź domyślne konto magazynu
Każdy klaster HDInsight ma domyślne konto magazynu. Domyślne konto magazynu i jego klucze dla klastra jest wyświetlana w obszarze **ustawienia**/**właściwości**/**klucze magazynu Azure**. Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Znajdź grupę zasobów
W trybie usługi Azure Resource Manager każdy klaster HDInsight jest tworzony z grupy zasobów platformy Azure. Grupy zasobów platformy Azure, do której należy klaster jest wyświetlany w:

* Lista klastra ma **grupy zasobów** kolumny.
* Klaster **Essential** kafelka.  

Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Otwórz konsolę zapytania HDInsight
Z konsoli zapytań HDInsight obejmuje następujące funkcje:

* **Edytor hive**: Graficzny interfejs użytkownika sieci web interfejs przesyłania zadań Hive.  Zobacz [uruchamianie Apache zapytań Hive przy użyciu konsoli zapytań](hadoop/apache-hadoop-use-hive-query-console.md).

    ![Edytor portalu hive HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Historia zadania**: Monitorowanie zadań usługi Hadoop.  

    ![Historia zadań portalu HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Kliknij przycisk **Nazwa_zapytania** Aby wyświetlić szczegółowe informacje, łącznie z właściwości zadania **zapytanie dotyczące zadań**, i ** dane wyjściowe zadania. Zapytania i dane wyjściowe można również pobrać stację roboczą.
* **Plik przeglądarki**: Przeglądaj domyślne konto magazynu i połączone konta magazynu.

    ![Przeglądaj przeglądarki pliku portalu HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Na zrzucie ekranu **<Account>** typ wskazuje element jest konto magazynu platformy Azure.  Kliknij nazwę konta, aby przeglądać pliki.
* **Interfejs użytkownika usługi Hadoop**.

    ![Portal HDInsight Hadoop interfejsu użytkownika](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Z **interfejsu użytkownika usługi Hadoop*, możesz przeglądać pliki i sprawdź dzienniki.
* **Interfejs użytkownika usługi yarn**.

    ![HDInsight portalu w interfejsie użytkownika YARN](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Uruchamianie zapytań Hive
Aby uruchamiać zadania Hive z poziomu portalu, kliknij przycisk **Edytor Hive** w konsoli zapytań HDInsight. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Monitorowanie zadań
Aby monitorować zadania z poziomu portalu, kliknij **historii zadań** w konsoli zapytań HDInsight. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="browse-files"></a>Przeglądaj pliki
Aby przeglądać plików przechowywanych na domyślne konto magazynu i połączone konta magazynu, kliknij przycisk **przeglądarka plików** w konsoli zapytań HDInsight. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

Można również użyć **Przeglądaj system plików** narzędzia z **interfejsu użytkownika usługi Hadoop** w konsoli usługi HDInsight.  Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Monitorowanie użycia klastra
**Użycia** części bloku klastra HDInsight Wyświetla informacje o liczba rdzeni dostępnych do Twojej subskrypcji do użycia z usługą HDInsight, a także liczby rdzeni przydzielonych do tego klastra i jak są przydzielane dla węzłów w klastrze. Zobacz [listy i wyświetlaniu klastrów](#list-and-show-clusters).

> [!IMPORTANT]  
> Aby monitorować usługi świadczone przez klaster usługi HDInsight, należy użyć sieci Ambari Web lub interfejs API REST Ambari. Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="open-hadoop-ui"></a>Otwórz interfejs użytkownika usługi Hadoop
Do monitorowania klastra, Przeglądaj system plików i sprawdź dzienniki, kliknij przycisk **interfejsu użytkownika usługi Hadoop** w konsoli zapytań HDInsight. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Otwórz interfejs użytkownika usługi Yarn
Aby użyć interfejsu użytkownika usługi Yarn, kliknij **interfejsie użytkownika Yarn** w konsoli zapytań HDInsight. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Łączenie się z klastrami przy użyciu protokołu RDP
Poświadczenia podane podczas jego tworzenia klastra dają dostęp do usługi w klastrze, ale nie do klastra za pośrednictwem pulpitu zdalnego. Dostęp do usług pulpitu zdalnego można włączyć podczas aprowizowania klastra lub po zainicjowaniu obsługi klastra. Aby uzyskać instrukcje dotyczące włączania usług pulpitu zdalnego, podczas tworzenia, zobacz [klastra HDInsight tworzenie](hdinsight-hadoop-provision-linux-clusters.md).

**Aby włączyć Pulpit zdalny**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystkie** w menu po lewej stronie kliknij **klastry HDInsight**, kliknij swoją nazwę użytkownika klastra.
3. Kliknij przycisk **ustawienia** z górnego menu, a następnie kliknij **pulpitu zdalnego**.
4. Wprowadź **wygasa**, **nazwa użytkownika pulpitu zdalnego** i **hasła pulpitu zdalnego**, a następnie kliknij przycisk **Włącz**.

    ![HDInsight Włącz Wyłącz Konfigurowanie pulpitu zdalnego](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Wartości domyślne dla wygasa jest tygodnia.

   > [!NOTE]  
   > Można również użyć zestawu .NET SDK HDInsight można włączyć pulpitu zdalnego w klastrze. Użyj **EnableRdp** metody na obiekt klienta HDInsight w następujący sposób: **klienta. EnableRdp (nazwa_klastra, lokalizację, "rdpuser", "rdppassword" DateTime.Now.AddDays(6))**. Podobnie, aby wyłączyć pulpitu zdalnego w klastrze, należy użyć **klienta. DisableRdp (clustername, lokalizacja)**. Aby uzyskać więcej informacji na temat tych metod, zobacz [dokumentacja zestawu SDK .NET usługi HDInsight](https://go.microsoft.com/fwlink/?LinkId=529017). Ma to zastosowanie tylko w przypadku klastrów HDInsight w systemie Windows.

**Nawiązać połączenia z klastrem przy użyciu protokołu RDP**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystkie** w menu po lewej stronie kliknij **klastry HDInsight**, kliknij swoją nazwę użytkownika klastra.
3. Kliknij przycisk **ustawienia** z górnego menu, a następnie kliknij **pulpitu zdalnego**.
4. Kliknij przycisk **Connect** i postępuj zgodnie z instrukcjami. Jeśli połączenie jest wyłączona, należy ją najpierw włączyć. Upewnij się, przy użyciu nazwy użytkownika pulpitu zdalnego i hasła użytkownika.  Nie można użyć poświadczeń użytkownika klastra.

## <a name="open-hadoop-command-line"></a>Otwórz wiersz polecenia usługi Hadoop
Do łączenia z klastrem przy użyciu pulpitu zdalnego za pomocą wiersza polecenia usługi Hadoop, musisz najpierw włączyć Pulpit zdalny dostęp do klastra zgodnie z opisem w poprzedniej sekcji.

**Aby otworzyć wiersz polecenia usługi Hadoop**

1. Połącz się z klastrem przy użyciu pulpitu zdalnego.
2. Na pulpicie kliknij dwukrotnie **wiersza polecenia usługi Hadoop**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Aby uzyskać więcej informacji na temat poleceń platformy Hadoop, zobacz [Apache Hadoop polecenia odwołanie](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Na poprzednim zrzucie ekranu Nazwa folderu ma numer wersji usługi Hadoop, które są osadzone. Numer wersji można zmienić w zależności od wersji składniki platformy Hadoop, zainstalowane w klastrze. Aby odwołać się do tych folderów, można użyć zmiennych środowiskowych platformy Hadoop. Na przykład:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Kolejne kroki
W tym artykule wiesz, jak utworzyć klaster usługi HDInsight przy użyciu portalu i jak otworzyć narzędzia wiersza polecenia platformy Apache Hadoop. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie przy użyciu programu PowerShell usługi Azure HDInsight](hdinsight-administer-use-powershell.md)
* [Administrowanie przy użyciu klasycznego wiersza polecenia platformy Azure HDInsight](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Przesyłanie zadań Apache Hadoop programowe](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jakiej wersji programu Apache Hadoop znajduje się w usłudze Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Wiersza polecenia usługi Hadoop"

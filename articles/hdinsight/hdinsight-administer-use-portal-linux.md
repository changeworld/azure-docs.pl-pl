---
title: Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal
description: Dowiedz się, jak utworzyć i zarządzać klastrami HDInsight przy użyciu witryny Azure portal.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 003aeadba1f4683af40f390d40dd3bbe32e02a83
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096365"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Za pomocą [witryny Azure portal][azure-portal], można zarządzać [Apache Hadoop](https://hadoop.apache.org/) klastrów w usłudze Azure HDInsight. Selektor karty powyżej, aby uzyskać informacje na temat zarządzania klastrami Hadoop w HDInsight przy użyciu innych narzędzi.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Istniejący klaster Apache Hadoop w HDInsight.  Zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Wprowadzenie
Zaloguj się do witryny [https://portal.azure.com](https://portal.azure.com).

## <a name="showClusters"></a> Listy i wyświetlaniu klastrów
**Klastry HDInsight** stronie zostanie wyświetlona lista istniejących klastrów.  Z poziomu portalu:
1. Wybierz **wszystkich usług** menu po lewej stronie.
2. Wybierz **klastry HDInsight** w obszarze **analizy**.

## <a name="homePage"></a> Strona główna klastra 
Wybierz nazwę klastra z [ **klastry HDInsight** ](#showClusters) strony.  Spowoduje to otwarcie **Przegląd** widoku, który wygląda podobnie do poniższej ilustracji:

![Podstawy klastra HDInsight portalu Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu u góry:**  

| Element| Opis |
|---|---|
|Move|Przenosi klastra do innej grupy zasobów lub do innej subskrypcji.|
|Usuwanie|Usuwa klaster. |
|Odświeżanie|Odświeża widoku.|

**Menu po lewej stronie:**  
  - **Menu w lewym górnym**

    | Element| Opis |
    |---|---|
    |Omówienie|Zawiera ogólne informacje dla klastra.|
    |Dziennik aktywności|Pokaż i wyszukiwać w dziennikach aktywności.|
    |Kontrola dostępu (IAM)|Za pomocą przypisań ról.  Zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md).|
    |Tagi|Umożliwia ustawienie pary klucz/wartość, aby zdefiniować taksonomię niestandardowych usług w chmurze. Na przykład, może utworzyć klucz o nazwie **projektu**, a następnie użyj wspólnej wartości dla wszystkich usług skojarzone z określonego projektu.|
    |Diagnozowanie i rozwiązywanie problemów|Wyświetl informacje dotyczące rozwiązywania problemów.|
    |Szybki start|Wyświetla informacje, które ułatwia rozpoczęcie korzystania z HDInsight.|
    |Narzędzia|Pomoc dotycząca HDInsight narzędzia pokrewne.|

  - **Menu Ustawienia**  

    | Element| Opis |
    |---|---|
    |Rozmiar klastra|Sprawdź, zwiększyć i zmniejszyć liczbę węzłów procesu roboczego klastra. Zobacz [Skaluj klastry](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limity przydziału|Wyświetl rdzeni używane i dostępne dla Twojej subskrypcji.|
    |SSH + dane logowania klastra|Zawiera instrukcje łączenia z klastrem przy użyciu połączenia protokołu Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Usługa Data Lake Storage 1. generacji|Konfigurowanie dostępu Data Lake Storage Gen1.  Zobacz [Szybki start: Konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Konta magazynu|Wyświetl klucze i kont magazynu. Konta magazynu są skonfigurowane w trakcie procesu tworzenia klastra.|
    |Aplikacje|Dodawać i usuwać aplikacje HDInsight.  Zobacz [instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Działania skryptu|Uruchamiać skrypty powłoki systemowej w klastrze. Zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).|
    |Zewnętrzne magazyny metadanych|Widok [Apache Hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/) magazyny metadanych. Magazyny metadanych można skonfigurować tylko podczas procesu tworzenia klastra.|
    |Partner usługi HDInsight|Dodaj/Usuń bieżącego partnera HDInsight.|
    |Właściwości|Widok [właściwości klastra](#properties).|
    |Blokady|Dodaj blokadę, aby zapobiec klastra trwa zmodyfikowane lub usunięte.|
    |Skrypt automatyzacji|Wyświetlanie i eksportowanie szablonu usługi Azure Resource Manager dla klastra. Obecnie można wyeksportować tylko konta magazynu Azure zależnego. Zobacz [klastrów opartych na systemie Linux z tworzenia Apache Hadoop w HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Monitorowanie menu**

    | Element| Opis |
    |---|---|
    |Alerty|Zarządzanie alertami i akcje.|
    |Metryki|Monitoruj metryki klastra w dziennikach w usłudze Azure Monitor.|
    |Ustawienia diagnozy|Ustawienia, na którym będą przechowywane metryki diagnostyki.|
    |Operations Management Suite|Monitorowanie klastra w dziennikach Azure Operations Management Suite (OMS) i usługi Azure Monitor.|

  - **Pomoc techniczna i rozwiązywanie problemów z menu**

    | Element| Opis |
    |---|---|
    |Kondycja zasobów|Zobacz [Przegląd kondycji zasobów platformy Azure](../service-health/resource-health-overview.md).|
    |Nowe żądanie pomocy technicznej|Umożliwia tworzenie biletu pomocy technicznej za pomocą techniczną firmy Microsoft.|

## <a name="properties"></a> Właściwości klastra

Z [klastra, strona główna](#homePage)w obszarze **ustawienia** wybierz **właściwości**.

|Element | Opis |
|---|---|
|Nazwa hosta|Nazwa klastra.|
|Adres URL klastra|Adres URL dla interfejsu sieci web Ambari.|
|Bezpieczna powłoka (SSH)|Nazwa użytkownika i hosta do użycia podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH.|
|Stan|Jeden z: Przerwane i zaakceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacyjne, uruchomione, błąd, usuwanie, usunięte, przekroczenie limitu czasu, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, lub ClusterCustomization.|
|Region|Lokalizacja platformy Azure. Aby uzyskać listę obsługiwanych lokalizacji platformy Azure, zobacz **Region** pole listy rozwijanej na [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|Data utworzenia|Data została wdrożona w klastrze.|
|System operacyjny|Albo **Windows** lub **Linux**.|
|Type|Hadoop, HBase, Storm, Spark.|
|Wersja|Zobacz [wersji HDInsight](hdinsight-component-versioning.md).|
|Subskrypcja|Nazwa subskrypcji.|
|Domyślne źródło danych|Domyślny system plików klastra.|
|Rozmiar węzłów procesu roboczego|Wybrany rozmiar maszyny Wirtualnej węzłów procesu roboczego.|
|Rozmiar węzła HEAD|Wybrany rozmiar maszyny Wirtualnej węzłów głównych.|
|Sieć wirtualna|Nazwa sieci wirtualnej, który jest wdrażany klaster, jeśli wybrano jeden w czasie wdrażania.|

## <a name="move-clusters"></a>Przenieś klastrów

Klaster usługi HDInsight można przenieść do innej grupy zasobów platformy Azure lub innej subskrypcji.

Z [klastra, strona główna](#homePage):

1. Wybierz **przenieść** z górnego menu.
2. Wybierz **przenieść do innej grupy zasobów** lub **przenieść do innej subskrypcji**.
3. Postępuj zgodnie z instrukcjami na nowej stronie.

## <a name="delete-clusters"></a>Usuwanie klastrów
Usunięcie klastrów nie powoduje usunięcia domyślne konto magazynu, ani wszystkie połączone konta magazynu. Można ponownie utworzyć klaster przy użyciu tego samego konta magazynu i samej magazyny metadanych. Zalecamy używanie nowego domyślnego kontenera obiektów Blob podczas ponownego tworzenia klastra.

Z [klastra, strona główna](#homePage):

1. Wybierz **Usuń** z górnego menu.
2. Postępuj zgodnie z instrukcjami na nowej stronie.

Zobacz też [Pause/zamykania klastrów](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Po utworzeniu klastra możesz dodać dodatkowe konta usługi Azure Storage i kont magazynu usługi Azure Data Lake. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używane przez klaster usługi HDInsight na platformie Azure bez konieczności ponownego tworzenia klastra.

> [!NOTE]  
> Tylko klastry HDInsight w wersji 3.1.3 lub nowszej są obsługiwane. Jeśli masz pewności, jaka wersja klastra, możesz sprawdzić na stronie właściwości.  Zobacz listę i Pokaż klastrów.

Z [klastra, strona główna](#homePage):

1. W obszarze **ustawienia**, wybierz opcję **rozmiar klastra**.
2. Wprowadź **węzłów liczba procesów roboczych** w polu tekstowym liczbowych. Limit liczby węzłów klastra waha się między subskrypcjami platformy Azure. Można skontaktuj się z działem pomocy technicznej dotyczącej rozliczeń w celu zwiększenia limitu.  Informacje o kosztach odzwierciedla zmiany wprowadzone do liczby węzłów.
3. Wybierz pozycję **Zapisz**.

    ![HDInsight hadoop hbase storm spark skalowania](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

Wpływ zmiany liczby węzłów danych różni się dla każdego typu klastra obsługiwane przez HDInsight:

* Apache Hadoop

    Możesz bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze usługi Hadoop, w którym jest uruchomiony bez wywierania wpływu na wszystkie oczekujące lub uruchomione zadania. Nowe zadania należy dostarczyć również w przypadku, gdy operacja jest w toku. Błędy trwaniem skalowania bez problemu zmieniała są obsługiwane, dzięki czemu klaster zawsze pozostanie w stanie działać.

    Gdy klaster Hadoop jest skalowane w dół dzięki zmniejszeniu liczby węzłów danych, zostaną ponownie uruchomione niektóre z tych usług w klastrze. To zachowanie powoduje, że wszystkie uruchomione i oczekujące zadania na zakończenie operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* Apache HBase

    Możesz bezproblemowo Dodawanie lub usuwanie węzłów do klastra HBase jest uruchomiona. Serwery regionalne automatycznie są równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie równoważyć serwerów regionalnych, logując się do węzła głównego klastra i uruchamiając następujące polecenia z okna wiersza polecenia:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Aby uzyskać więcej informacji na temat korzystania z powłoki HBase, zobacz [Rozpoczynanie pracy z przykładem bazy danych Apache HBase w HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Bezproblemowo można dodać lub usunąć węzły danych z klastrem Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.

    Ponowne równoważenie może się odbywać na dwa sposoby:

  * Interfejs użytkownika sieci web systemu STORM
  * Narzędzia interfejsu wiersza polecenia (CLI)

    Zapoznaj się [dokumentacji platformy Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) Aby uzyskać więcej informacji.

    Interfejs użytkownika sieci web systemu Storm jest dostępny w klastrze HDInsight:

    ![Ponowne równoważenie skalowania HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Oto przykład polecenia interfejsu wiersza polecenia, aby ponowne zrównoważenie topologii systemu Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Wstrzymywanie/zamykania klastrów

Większość zadań usługi Hadoop to zadania wsadowe, które działają tylko od czasu do czasu. Większość klastrów platformy Hadoop istnieją duże okresy czasu, przez jaki klastra nie jest używany do przetwarzania. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany.
Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Istnieje wiele sposobów, można programować procesu:

* Użytkownik usługi Azure Data Factory. Zobacz [tworzenie na żądanie Apache Hadoop oparte na systemie Linux klastrów w HDInsight przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) tworzenia HDInsight na żądanie połączonych usług.
* Używanie programu Azure PowerShell.  Zobacz [analizowanie danych dotyczących opóźnień lotów](hdinsight-analyze-flight-delay-data-linux.md).
* Użyj klasycznego wiersza polecenia platformy Azure. Zobacz [HDInsight Zarządzanie klastrami przy użyciu klasyczny interfejs wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md).
* Użyj zestawu .NET SDK HDInsight. Zobacz [zadania przesyłania Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z poziomu portalu, zobacz [usuwać klastry](#delete-clusters)



## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Zobacz [klastra HDInsight uaktualnienia do nowszej wersji](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci web Apache Ambari

Ambari udostępnia intuicyjne, łatwe w użyciu Hadoop zarządzania interfejsu użytkownika sieci web obsługiwane przez jego interfejsów API RESTful. Ambari umożliwia administratorom systemu monitorowania klastrów platformy Hadoop oraz zarządzania nimi.

Z [klastra, strona główna](#homePage):

1. Wybierz **pulpity nawigacyjne klastra**.

    ![Menu klastra usługi HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Wybierz **Ambari macierzystego** na nowej stronie.
2. Wprowadź nazwę użytkownika klastra i hasło.  Domyślna nazwa użytkownika klastra _administratora_. Ambari web UI wygląda następująco:

Aby uzyskać więcej informacji, zobacz [HDInsight Zarządzanie klastrami za pomocą Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Zmienianie haseł
Klaster usługi HDInsight może mieć dwóch kont użytkowników. Konto użytkownika klastra HDInsight (konto użytkownika HTTP) i konto użytkownika SSH są tworzone podczas procesu tworzenia. Można użyć portalu, aby zmienić hasło konta użytkownika klastra i akcji skryptu, aby zmienić konto użytkownika SSH.

### <a name="change-the-cluster-user-password"></a>Zmień hasło użytkownika klastra

> [!NOTE]  
> Zmiana hasła użytkownika (administratora) klastra może spowodować akcji skryptu, uruchamiać ten klaster nie powiedzie się. Jeśli masz wszystkie akcje utrwalonego skryptu tej węzłów procesu roboczego w docelowej tych skryptów może zakończyć się niepowodzeniem Kiedy dodajesz węzły do klastra za pośrednictwem rozmiar operacji. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Z [klastra, strona główna](#homePage):
1. Wybierz **klastra i protokołu SSH logowania** w obszarze **ustawienia**.
2. Wybierz **Zresetuj poświadczenia**.
3. Wprowadź i Potwierdź nowe hasło w polach tekstowych.
4. Kliknij przycisk **OK**.

Hasło jest zmieniane na wszystkich węzłach w klastrze.

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
3. Z [klastra, strona główna](#homePage), wybierz opcję **akcji skryptu** w obszarze **ustawienia**.
4. Z **akcji skryptu** bloku wybierz **Prześlij nową**. 
5. Z **Prześlij akcję skryptu** bloku, wprowadź następujące informacje:

   | Pole | Wartość |
   | --- | --- |
   | Typ skryptu | Wybierz **- niestandardowa** z listy rozwijanej.|
   | Name (Nazwa) |"Zmień ssh hasła" |
   | Identyfikator URI skryptu powłoki systemowej |Identyfikator URI pliku changepassword.sh |
   | Typy węzłów: (Head procesu roboczego, Nimbus, nadzorca, Zookeeper, itp.) |✓ dla wszystkich typów węzła na liście |
   | Parametry |Wprowadź nazwę użytkownika SSH, a następnie nowe hasło. Powinna być jedną spację między nazwę użytkownika i hasło. |
   | Utrwal tę akcję skryptu... |Nie zaznaczaj tego pola wyboru. |

6. Wybierz **Utwórz** zastosować skrypt. Po zakończeniu działania skryptu jesteś w stanie połączyć się z klastrem przy użyciu protokołu SSH przy użyciu nowego hasła.

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight mają następujące usługi sieci web HTTP (wszystkie te usługi mają punktów końcowych RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/Udziel dostępu za pomocą [klasyczny interfejs wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) i [programu Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Znajdź identyfikator subskrypcji
Każdy klaster jest powiązany z subskrypcją platformy Azure.  Subskrypcja platformy Azure, identyfikator nie jest widoczny [klastra, strona główna](#homePage).

## <a name="find-the-resource-group"></a>Znajdź grupę zasobów
W trybie usługi Azure Resource Manager każdy klaster HDInsight jest tworzony z grupy usługi Azure Resource Manager. Grupa usługi Resource Manager nie jest widoczny [klastra, strona główna](#homePage).

## <a name="find-the-storage-accounts"></a>Znajdowanie konta magazynu
Klastry HDInsight Użyj konta usługi Azure Storage lub Azure Data Lake Storage do przechowywania danych. Każdy klaster HDInsight może mieć jeden domyślne konto magazynu i liczba połączonych kontach magazynu. Aby wyświetlić listę kont magazynu z [klastra, strona główna](#homePage) w obszarze **ustawienia**, wybierz opcję **kont magazynu**.


## <a name="monitor-jobs"></a>Monitorowanie zadań
Zobacz [HDInsight Zarządzanie klastrami za pomocą Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Monitorowanie użycia klastra
**Użycia** części bloku klastra HDInsight Wyświetla informacje o liczba rdzeni dostępnych do Twojej subskrypcji do użycia z usługą HDInsight, a także liczby rdzeni przydzielonych do tego klastra i jak są przydzielane dla węzłów w klastrze. Zobacz listę i Pokaż klastrów.

> [!IMPORTANT]  
> Aby monitorować usługi świadczone przez klaster usługi HDInsight, należy użyć sieci Ambari Web lub interfejs API REST Ambari. Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Łączenie z klastrem

* [Use Apache Hive z HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule mają przedstawiono niektóre podstawowe funkcje administracyjne. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie przy użyciu programu PowerShell usługi Azure HDInsight](hdinsight-administer-use-powershell.md)
* [Administrowanie przy użyciu klasycznego wiersza polecenia platformy Azure HDInsight](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Dowiedz się więcej o korzystaniu z Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Szczegółowe informacje na temat korzystania z interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Use Apache Hive w HDInsight](hadoop/hdinsight-use-hive.md)
* [Use Apache Pig w HDInsight](hadoop/hdinsight-use-pig.md)
* [W HDInsight przy użyciu narzędzia Apache Sqoop](hadoop/hdinsight-use-sqoop.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jakiej wersji programu Apache Hadoop znajduje się w usłudze Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Wiersza polecenia usługi Hadoop"

---
title: Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu witryny Azure portal
description: Dowiedz się, jak tworzyć klastry usługi Azure HDInsight i zarządzać nimi za pomocą witryny Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/12/2020
ms.openlocfilehash: b9d923b3272f9d8b3da39d7cdb771a766eee4eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272736"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu portalu Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Za pomocą [portalu Azure](https://portal.azure.com)można zarządzać [klastrami Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight. Użyj selektora kart powyżej, aby uzyskać informacje na temat zarządzania klastrami Hadoop w programie HDInsight przy użyciu innych narzędzi.

## <a name="prerequisites"></a>Wymagania wstępne

Istniejący klaster Apache Hadoop w hdinsight.  Zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Wprowadzenie

Zaloguj się [https://portal.azure.com](https://portal.azure.com)do .

## <a name="list-and-show-clusters"></a><a name="showClusters"></a>Wyświetlanie i pokazywale klastrów

Strona **klastrów usługi HDInsight** wyświetli listę istniejących klastrów.  Z portalu:
1. Z menu po lewej stronie wybierz **pozycję Wszystkie usługi.**
2. Wybierz **klastry HDInsight** w obszarze **ANALYTICS**.

## <a name="cluster-home-page"></a><a name="homePage"></a>Strona główna klastra

Wybierz nazwę klastra ze strony [**klastry HDInsight.**](#showClusters)  Spowoduje to otwarcie **widoku Przegląd,** który wygląda podobnie do następującego obrazu:

![Podstawowe informacje o klastrze usługi Azure portal HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu u góry:**  

| Element| Opis |
|---|---|
|Move|Przenosi klaster do innej grupy zasobów lub do innej subskrypcji.|
|Usuń|Usuwa klaster. |
|Odświeżanie|Odświeża widok.|

**Lewe menu:**  

  - **Menu lewy górny**

    | Element| Opis |
    |---|---|
    |Omówienie|Zawiera ogólne informacje dotyczące klastra.|
    |Dziennik aktywności|Pokazyj i wysyłaj zapytania dzienniki aktywności.|
    |Kontrola dostępu (IAM)|Użyj przypisań ról.  Zobacz [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań](../role-based-access-control/role-assignments-portal.md)ról.|
    |Tagi|Umożliwia ustawienie par klucz/wartość w celu zdefiniowania niestandardowej taksonomii usług w chmurze. Na przykład można utworzyć klucz o nazwie **projektu**, a następnie użyć wspólnej wartości dla wszystkich usług skojarzonych z określonym projektem.|
    |Diagnozowanie i rozwiązywanie problemów|Wyświetlanie informacji o rozwiązywaniu problemów.|
    |Szybki start|Wyświetla informacje ułatwiace rozpoczęcie korzystania z usługi HDInsight.|
    |Narzędzia|Informacje pomocy dotyczące narzędzi związanych z programem HDInsight.|

  - **Menu ustawień**  

    | Element| Opis |
    |---|---|
    |Rozmiar klastra|Sprawdź, zwiększ i zmniejsz liczbę węzłów procesu roboczego klastra. Zobacz [Skalowanie klastrów](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limity przydziałów|Wyświetl używane i dostępne rdzenie dla subskrypcji.|
    |SSH + Logowanie klastra|Pokazuje instrukcje łączenia się z klastrem przy użyciu połączenia Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Usługa Data Lake Storage 1. generacji|Skonfiguruj dostęp do magazynu danych Lake Gen1.  Zobacz [Szybki start: Konfigurowanie klastrów w ujrzyszy.](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)|
    |Konta magazynu|Wyświetlanie kont magazynu i kluczy. Konta magazynu są konfigurowane podczas procesu tworzenia klastra.|
    |Aplikacje|Dodaj/usuń aplikacje HDInsight.  Zobacz [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Akcje skryptu|Uruchom skrypty Bash w klastrze. Zobacz [Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).|
    |Zewnętrzne metasklepy|Zobacz [apache hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/) metastores. Magazyny metasklepów można skonfigurować tylko podczas procesu tworzenia klastra.|
    |Partner HDInsight|Dodaj/usuń bieżącego partnera HDInsight.|
    |Właściwości|Wyświetlanie [właściwości klastra](#properties).|
    |Blokady|Dodaj blokadę, aby zapobiec modyfikowaniu lub usuwaniu klastra.|
    |Eksportowanie szablonu|Wyświetlanie i eksportowanie szablonu usługi Azure Resource Manager dla klastra. Obecnie można wyeksportować tylko zależne konto magazynu platformy Azure. Zobacz [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menu monitorowania**

    | Element| Opis |
    |---|---|
    |Alerty|Zarządzanie alertami i akcjami.|
    |Metryki|Monitorowanie metryk klastra w dziennikach usługi Azure Monitor.|
    |Ustawienia diagnozy|Ustawienia dotyczące miejsca przechowywania danych diagnostycznych.|
    |Azure Monitor|Monitoruj swój klaster w usłudze Azure Monitor.|

  - **Menu pomocy technicznej i rozwiązywania problemów**

    | Element| Opis |
    |---|---|
    |Kondycja zasobów|Zobacz [omówienie kondycji zasobów platformy Azure](../service-health/resource-health-overview.md).|
    |Nowe żądanie pomocy technicznej|Umożliwia utworzenie biletu pomocy technicznej z pomocą techniczną firmy Microsoft.|

## <a name="cluster-properties"></a><a name="properties"></a>Właściwości klastra

Na [stronie głównej klastra](#homePage)w obszarze **Ustawienia** wybierz pozycję **Właściwości**.

|Element | Opis |
|---|---|
|Nazwa hosta|Nazwa klastra.|
|ADRES URL KLASTRA|Adres URL interfejsu internetowego Ambari.|
|Prywatny punkt końcowy|Prywatny punkt końcowy dla klastra.|
|Bezpieczna powłoka (SSH)|Nazwa użytkownika i nazwa hosta do użycia podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH.|
|STAN|Jeden z: Przerwane, Zaakceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operacyjne, Uruchomione, Błąd, Usuwanie, Usunięte, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, Zmienić rozmiaruuuskony lub ClusterCustomization.|
|REGION|Lokalizacja platformy Azure. Aby uzyskać listę obsługiwanych lokalizacji platformy Azure, zobacz pole listy rozwijanej **Region** w [cennikach USŁUGI HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA UTWORZENIA|Data wdrożenia klastra.|
|System operacyjny|**Windows** lub **Linux**.|
|TYP|Hadoop, HBase, Burza, Iskra.|
|Wersja|Zobacz [wersje HDInsight](hdinsight-component-versioning.md).|
|Minimalna wersja TLS|Wersja TLS.|
|SUBSKRYPCJA|Nazwa subskrypcji.|
|DOMYŚLNE ŹRÓDŁO DANYCH|Domyślny system plików klastra.|
|Rozmiary węzłów procesu roboczego|Wybrany rozmiar maszyny Wirtualnej węzłów procesu roboczego.|
|Rozmiar węzła głównego|Wybrany rozmiar maszyny Wirtualnej węzłów głównego.|
|Sieć wirtualna|Nazwa sieci wirtualnej, która jest wdrażana, jeśli została wybrana w czasie wdrażania.|

## <a name="move-clusters"></a>Przenoszenie klastrów

Klaster USŁUGI HDInsight można przenieść do innej grupy zasobów platformy Azure lub innej subskrypcji.

Ze [strony głównej klastra](#homePage):

1. Wybierz **pozycję Przenieś** z górnego menu.
2. Wybierz **pozycję Przenieś do innej grupy zasobów** lub Przenieś do innej **subskrypcji**.
3. Postępuj zgodnie z instrukcjami z nowej strony.

## <a name="delete-clusters"></a>Usuwanie klastrów

Usunięcie klastra nie powoduje usunięcia domyślnego konta magazynu ani żadnych połączonych kont magazynu. Klaster można ponownie utworzyć przy użyciu tych samych kont magazynu i tych samych magazynów. Zaleca się użycie nowego domyślnego kontenera obiektów blob podczas ponownego tworzenia klastra.

Ze [strony głównej klastra](#homePage):

1. Z górnego menu wybierz **polecenie Usuń.**
2. Postępuj zgodnie z instrukcjami z nowej strony.

Zobacz też [Wstrzymanie/zamykanie klastrów](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Po utworzeniu klastra można dodać dodatkowe konta usługi Azure Storage i konta usługi Azure Data Lake Storage. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalowanie klastrów

Funkcja skalowania klastra umożliwia zmianę liczby węzłów procesu roboczego używanych przez klaster usługi Azure HDInsight bez konieczności ponownego tworzenia klastra.

Aby uzyskać pełne informacje, zobacz [Skalowanie klastrów HDInsight.](./hdinsight-scaling-best-practices.md)

## <a name="pauseshut-down-clusters"></a>Wstrzymywanie/zamykanie klastrów

Większość zadań Hadoop to zadania wsadowe, które są uruchamiane tylko sporadycznie. W przypadku większości klastrów Hadoop istnieją duże okresy czasu, przez które klaster nie jest używany do przetwarzania. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany.
Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

Proces ten można zaprogramować na wiele sposobów:

- Fabryka danych platformy Azure użytkownika. Zobacz [Tworzenie klastrów Apache Hadoop opartych na systemie Linux na żądanie w programie HDInsight przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) do tworzenia usług połączonych HDInsight na żądanie.
- Użyj programu Azure PowerShell.  Zobacz [Analizowanie danych opóźnienia lotu](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Użyj interfejsu wiersza polecenia platformy Azure. Zobacz [Zarządzanie klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md).
- Użyj pliku HDInsight .NET SDK. Zobacz [Prześlij zadania Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [HDInsight cennik](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z portalu, zobacz [Usuwanie klastrów](#delete-clusters)

## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Zobacz [Uaktualnianie klastra HDInsight do nowszej wersji](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci Web Apache Ambari

Ambari zapewnia intuicyjny, łatwy w użyciu interfejs internetowy do zarządzania Hadoop wspierany przez jego interfejsy API RESTful. Ambari umożliwia administratorom systemu zarządzanie klastrami Hadoop i monitorowanie ich.

Ze [strony głównej klastra](#homePage):

1. Wybierz **pozycję Pulpity nawigacyjne klastra**.

    ![Menu klastra HDInsight Apache Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Wybierz **Ambari home** z nowej strony.
1. Wprowadź nazwę użytkownika i hasło klastra.  Domyślną nazwą użytkownika klastra jest _admin_.

Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Zmienianie haseł

Klaster HDInsight może mieć dwa konta użytkowników. Podczas procesu tworzenia tworzone jest konto użytkownika klastra HDInsight (konto użytkownika HTTP) i konto użytkownika SSH. Za pomocą portalu można zmienić hasło konta użytkownika klastra, a akcje skryptu w celu zmiany konta użytkownika SSH.

### <a name="change-the-cluster-user-password"></a>Zmienianie hasła użytkownika klastra

> [!NOTE]  
> Zmiana hasła użytkownika (administratora klastra) może spowodować niepowodzenie akcji skryptu uruchamiane w tym klastrze. Jeśli masz żadnych utrwalonych akcji skryptu, które docelowe węzłów procesu roboczego, skrypty te mogą zakończyć się niepowodzeniem po dodaniu węzłów do klastra za pomocą operacji zmiany rozmiaru. Aby uzyskać więcej informacji na temat akcji skryptów, zobacz [Dostosowywanie klastrów HDInsight przy użyciu akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).

Ze [strony głównej klastra](#homePage):
1. Wybierz **SSH + Logowanie do klastra** w **obszarze Ustawienia**.
2. Wybierz **pozycję Resetuj poświadczenia**.
3. Wprowadź i potwierdź nowe hasło w polach tekstowych.
4. Kliknij przycisk **OK**.

Hasło zostanie zmienione we wszystkich węzłach w klastrze.

### <a name="change-the-ssh-user-password"></a>Zmienianie hasła użytkownika SSH

1. Za pomocą edytora tekstu zapisz następujący tekst jako plik o nazwie **changepassword.sh**.

    > [!IMPORTANT]  
    > Należy użyć edytora, który używa LF jako zakończenia wiersza. Jeśli edytor używa CRLF, skrypt nie działa.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Przekaż plik do lokalizacji magazynu, do których można uzyskać dostęp z usługi HDInsight przy użyciu adresu HTTP lub HTTPS. Na przykład publiczny magazyn plików, takich jak OneDrive lub Azure Blob storage. Zapisz identyfikator URI (adres HTTP lub HTTPS) w pliku, ponieważ ten identyfikator URI jest potrzebny w następnym kroku.
3. Na [stronie głównej klastra](#homePage)wybierz pozycję **Akcje skryptu** w obszarze **Ustawienia**.
4. Na stronie **Akcje skryptu** wybierz pozycję **Prześlij nowy**.
5. Na stronie **Akcji Prześlij skrypt** wprowadź następujące informacje:

   | Pole | Wartość |
   | --- | --- |
   | Typ skryptu | Wybierz **— niestandardowe** z listy rozwijanej.|
   | Nazwa |"Zmień hasło ssh" |
   | Identyfikator URI skryptu bash |Identyfikator URI do pliku changepassword.sh |
   | Typ węzła:(head, worker, Nimbus, Supervisor, or Zookeeper). |✓ dla wszystkich typów węzłów wymienionych |
   | Parametry |Wprowadź nazwę użytkownika SSH, a następnie nowe hasło. Między nazwą użytkownika a hasłem powinna istnieć jedno odstępy. |
   | Utrwalić tę akcję skryptu ... |Nie zaznaczono tego pola. |

6. Wybierz **pozycję Utwórz,** aby zastosować skrypt. Po zakończeniu skryptu możesz połączyć się z klastrem przy użyciu funkcji SSH przy użyciu nowego hasła.

## <a name="find-the-subscription-id"></a>Znajdowanie identyfikatora subskrypcji

Każdy klaster jest powiązany z subskrypcją platformy Azure.  Identyfikator subskrypcji platformy Azure jest widoczny ze [strony głównej klastra](#homePage).

## <a name="find-the-resource-group"></a>Znajdowanie grupy zasobów

W trybie usługi Azure Resource Manager każdy klaster usługi HDInsight jest tworzony przy za pomocą grupy usługi Azure Resource Manager. Grupa Menedżer zasobów jest widoczna ze [strony głównej klastra](#homePage).

## <a name="find-the-storage-accounts"></a>Znajdowanie kont magazynu

Klastry usługi HDInsight używają konta usługi Azure Storage lub usługi Azure Data Lake Storage do przechowywania danych. Każdy klaster USŁUGI HDInsight może mieć jedno domyślne konto magazynu i kilka połączonych kont magazynu. Aby wyświetlić listę kont magazynu, [ze strony głównej klastra](#homePage) w obszarze **Ustawienia**wybierz pozycję **Konta magazynu**.

## <a name="monitor-jobs"></a>Monitorowanie zadań

Zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Rozmiar klastra

Kafelek **rozmiar klastra** ze [strony głównej klastra](#homePage) wyświetla liczbę rdzeni przydzielonych do tego klastra i sposób ich przydzielania dla węzłów w tym klastrze.

> [!IMPORTANT]  
> Aby monitorować usługi świadczone przez klaster HDInsight, należy użyć ambari web lub interfejsu API Ambari REST. Aby uzyskać więcej informacji na temat korzystania z programu Ambari, zobacz [Zarządzanie klastrami HDInsight przy użyciu apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Łączenie z klastrem

- [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Następne kroki

W tym artykule poznaliście kilka podstawowych funkcji administracyjnych. Aby dowiedzieć się więcej, zobacz następujące artykuły:

- [Administrowanie programem HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md)
- [Administrowanie usługą HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)
- [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Szczegółowe informacje na temat korzystania z api Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Użyj ula Apache w funkcji HDInsight](hadoop/hdinsight-use-hive.md)
- [Użyj Apache Sqoop w HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Korzystanie z funkcji zdefiniowanych przez użytkownika języka Python (UDF) z gałęzią Apache i świnią Apache w formacie HDInsight](hadoop/python-udf-hdinsight.md)
- [Jaka wersja apache Hadoop jest w usłudze Azure HDInsight?](hdinsight-component-versioning.md)

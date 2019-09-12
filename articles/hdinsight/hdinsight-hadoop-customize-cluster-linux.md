---
title: Dostosowywanie klastrów usługi Azure HDInsight za pomocą akcji skryptu
description: Dodawanie niestandardowych składników do klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu. Akcje skryptu są skryptami bash, które mogą służyć do dostosowywania konfiguracji klastra lub dodawania dodatkowych usług i narzędzi, takich jak odcień, Solr lub R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: dca329ce598a9ecbc7da71cc1c24bf7731a4c994
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885436"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Dostosowywanie klastrów usługi Azure HDInsight za pomocą akcji skryptu

Usługa Azure HDInsight udostępnia metodę konfiguracji o nazwie **Akcje skryptu** , które wywołuje niestandardowe skrypty w celu dostosowania klastra. Skrypty te służą do instalowania dodatkowych składników i zmieniania ustawień konfiguracji. Akcji skryptu można używać podczas tworzenia klastra lub po nim.

Akcje skryptu można również publikować w portalu Azure Marketplace jako aplikację usługi HDInsight. Aby uzyskać więcej informacji na temat aplikacji HDInsight, zobacz [publikowanie aplikacji usługi HDInsight w portalu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Uprawnienia

W przypadku klastra usługi HDInsight przyłączonego do domeny istnieją dwa uprawnienia Apache Ambari, które są wymagane w przypadku korzystania z akcji skryptów w klastrze:

* **AMBARI. URUCHOM\_POLECENIENIESTANDARDOWE\_** . To uprawnienie jest domyślnie objęte rolą administratora Ambari.
* **KLASTER. URUCHOM\_POLECENIENIESTANDARDOWE\_** . Zarówno administrator klastra usługi HDInsight, jak i administrator Ambari domyślnie mają to uprawnienie.

Aby uzyskać więcej informacji na temat pracy z uprawnieniami w usłudze HDInsight przyłączonych do domeny, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu pakiet Enterprise Security](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Kontrola dostępu

Jeśli nie jesteś administratorem ani właścicielem subskrypcji platformy Azure, Twoje konto musi mieć co najmniej dostęp współautora do grupy zasobów zawierającej klaster usługi HDInsight.

Jeśli utworzysz klaster usługi HDInsight, osoba mająca co najmniej dostęp współautora do subskrypcji platformy Azure musi mieć wcześniej zarejestrowaną dostawcę usług HDInsight. Rejestracja zasobu ma miejsce, gdy użytkownik o roli Współautor z dostępem do subskrypcji tworzy zasób po raz pierwszy w subskrypcji. Można to zrobić również bez tworzenia zasobu, jeśli [dostawca jest rejestrowany przy użyciu REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Uzyskaj więcej informacji na temat pracy z zarządzaniem dostępem:

* [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
* [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Opis akcji skryptu

Akcja skryptu to skrypt bash, który działa w węzłach klastra usługi HDInsight. Właściwości i funkcje skryptu są następujące:

* Musi być przechowywany w identyfikatorze URI dostępnym z klastra usługi HDInsight. Możliwe są następujące lokalizacje magazynu:
    
    * W przypadku regularnych klastrów:
    
      * ADLS Gen1: Główna Usługa HDInsight używa do uzyskiwania dostępu do Data Lake Storage musi mieć dostęp do odczytu do skryptu. Format identyfikatora URI dla skryptów przechowywanych w Data Lake Storage Gen1 to `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.
      
      * Obiekt BLOB na koncie magazynu platformy Azure, który jest podstawowym lub dodatkowym kontem magazynu dla klastra usługi HDInsight. Usługa HDInsight ma dostęp do obu typów kont magazynu podczas tworzenia klastra.

        > [!IMPORTANT]  
        > Nie obracaj klucza magazynu na tym koncie usługi Azure Storage, ponieważ spowoduje to, że kolejne akcje skryptu z zapisanymi skryptami nie powiodą się.

      * Publiczna usługa udostępniania plików dostępna za pomocą ścieżek http://. Przykłady to Azure Blob, GitHub, OneDrive.

        Przykładowo identyfikatory URI, zobacz [przykładowe skrypty akcji skryptu](#example-script-action-scripts).

     * W przypadku klastrów z partycją ESP:
         
         * Obsługiwane są wasb://lub wasbs://lub http [s]://URI.
            
* Mogą być ograniczone do uruchamiania tylko dla określonych typów węzłów. Przykłady to węzły główne lub węzły procesu roboczego.

* Mogą być utrwalone lub ad hoc.

    Utrwalone skrypty są używane do dostosowywania nowych węzłów procesu roboczego dodanych do klastra za pomocą operacji skalowania. Utrwalony skrypt może również zastosować zmiany do innego typu węzła w przypadku wystąpienia operacji skalowania. Przykładem jest węzeł główny.

  > [!IMPORTANT]  
  > Akcje utrwalonego skryptu muszą mieć unikatową nazwę.

    Skrypty ad hoc nie są utrwalane. Nie są one stosowane do węzłów procesu roboczego dodanych do klastra po uruchomieniu skryptu. Następnie można podwyższyć poziom skryptu ad hoc do utrwalonego skryptu lub obniżyć utrwalony skrypt do skryptu ad hoc.

  > [!IMPORTANT]  
  > Akcje skryptu używane podczas tworzenia klastra są automatycznie utrwalane.
  >
  > Skrypty, które nie są utrwalane, nawet jeśli wskazują, że powinny być.

* Może akceptować parametry, które są używane przez skrypt podczas wykonywania.

* Uruchom z uprawnieniami poziomu głównego w węzłach klastra.

* Może być używany w ramach Azure Portal, Azure PowerShell, klasycznego interfejsu wiersza polecenia platformy Azure lub zestawu .NET SDK usługi HDInsight.

Klaster przechowuje historię wszystkich uruchomionych skryptów. Historia jest pomocna w przypadku konieczności znalezienia identyfikatora skryptu dla operacji podwyższania lub obniżania poziomu.

> [!IMPORTANT]  
> Nie ma automatycznej metody cofania zmian dokonanych przez akcję skryptu. Ręcznie odwrócić zmiany lub podaj skrypt, który go odwraca.

### <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w procesie tworzenia klastra

Akcje skryptu używane podczas tworzenia klastra są nieco inne niż akcje skryptu uruchamiane w istniejącym klastrze:

* Skrypt jest automatycznie utrwalany.

* Awaria skryptu może spowodować niepowodzenie procesu tworzenia klastra.

Na poniższym diagramie przedstawiono, kiedy Akcja skryptu jest uruchamiana podczas procesu tworzenia:

![Dostosowywanie i etapy klastra usługi HDInsight podczas tworzenia klastra][img-hdi-cluster-states]

Skrypt jest uruchamiany podczas konfigurowania usługi HDInsight. Skrypt jest uruchamiany równolegle na wszystkich określonych węzłach w klastrze. Jest on uruchamiany z uprawnieniami głównymi w węzłach.

> [!NOTE]  
> Można wykonywać operacje, takie jak zatrzymywanie i uruchamianie usług, w tym usługi związane z Apache Hadoop. Jeśli zatrzymasz usługi, upewnij się, że usługa Ambari i inne usługi związane z usługą Hadoop są uruchomione przed zakończeniem działania skryptu. Te usługi są wymagane, aby pomyślnie określić kondycję i stan klastra podczas jego tworzenia.


Podczas tworzenia klastra można używać wielu akcji skryptów jednocześnie. Te skrypty są wywoływane w kolejności, w jakiej zostały określone.

> [!IMPORTANT]  
> Akcje skryptu muszą zakończyć się w ciągu 60 minut lub przekroczyć limit czasu. Podczas aprowizacji klastra skrypt jest uruchamiany współbieżnie z innymi procesami instalacji i konfiguracji. Konkurencja zasobów, takich jak czas procesora CPU lub przepustowość sieci, może spowodować, że wykonanie skryptu trwa dłużej niż w środowisku deweloperskim.
>
> Aby zminimalizować czas potrzebny na uruchomienie skryptu, należy unikać zadań, takich jak pobieranie i kompilowanie aplikacji ze źródła. Wstępne Kompilowanie aplikacji i przechowywanie plików binarnych w usłudze Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Akcja skryptu w uruchomionym klastrze

Błąd w skrypcie uruchomionym na już uruchomionym klastrze nie powoduje automatycznego zmiany stanu klastra na uszkodzony. Po zakończeniu działania skryptu klaster powinien powrócić do stanu uruchomienia.

> [!IMPORTANT]  
> Nawet jeśli klaster ma stan uruchomiony, uszkodzony skrypt może być uszkodzony. Na przykład skrypt może usunąć pliki potrzebne przez klaster.
>
> Akcje skryptów są uruchamiane z uprawnieniami głównymi. Upewnij się, że rozumiesz, co skrypt wykonuje przed zastosowaniem go do klastra.

Po zastosowaniu skryptu do klastra stan klastra zmienia się z **uruchomione** na **zaakceptowane**. Następnie zmieni się on na **Konfiguracja usługi HDInsight** , a wreszcie ponownie, aby **uruchomić** skrypty. Stan skryptu jest rejestrowany w historii akcji skryptu. Te informacje informują o tym, czy skrypt zakończył się powodzeniem, czy niepowodzeniem. Na przykład `Get-AzHDInsightScriptActionHistory` polecenie cmdlet programu PowerShell pokazuje stan skryptu. Zwraca informacje podobne do następującego tekstu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> W przypadku zmiany użytkownika klastra, administratora, hasła po utworzeniu klastra akcje skryptu uruchamiane względem tego klastra mogą zakończyć się niepowodzeniem. Jeśli masz jakiekolwiek akcje utrwalonego skryptu, które są przeznaczone dla węzłów procesu roboczego, te skrypty mogą kończyć się niepowodzeniem podczas skalowania klastra.

## <a name="example-script-action-scripts"></a>Przykładowe skrypty akcji skryptu

Skrypty akcji skryptu mogą być używane w następujących narzędziach:

* Witryna Azure Portal
* Azure PowerShell
* Klasyczny interfejs wiersza polecenia platformy Azure
* Zestaw SDK HDInsight An .NET

Usługa HDInsight udostępnia skrypty umożliwiające zainstalowanie następujących składników w klastrach usługi HDInsight:

| Name | Skrypt |
| --- | --- |
| Dodaj konto usługi Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](hdinsight-hadoop-add-storage.md). |
| Zainstaluj program Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zobacz [Instalowanie i używanie odcienia w klastrach usługi HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Zainstaluj program Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Zobacz [Instalowanie oprogramowania Apache Giraph w klastrach usługi HDInsight Hadoop](hdinsight-hadoop-giraph-install-linux.md). |
| Wstępne ładowanie bibliotek Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zobacz [Dodawanie niestandardowych bibliotek Apache Hive podczas tworzenia klastra usługi HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Używanie akcji skryptu podczas tworzenia klastra

W tej sekcji opisano różne sposoby używania akcji skryptu podczas tworzenia klastra usługi HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Użyj akcji skryptu podczas tworzenia klastra z Azure Portal

1. Rozpocznij tworzenie klastra zgodnie z opisem w temacie [Konfigurowanie klastrów w usłudze HDInsight z Apache Hadoop, Apache Spark, Apache Kafka i nie tylko](hdinsight-hadoop-provision-linux-clusters.md). Podczas tworzenia klastra docierasz do strony __podsumowania klastra__ . Na stronie __Podsumowanie klastra__ wybierz łącze __Edytuj__ __Ustawienia zaawansowane__.

    ![Link ustawień zaawansowanych](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. W sekcji __Ustawienia zaawansowane__ wybierz pozycję __Akcje skryptu__. W sekcji __Akcje skryptu__ wybierz pozycję __+ Prześlij nowe__.

    ![Prześlij nową akcję skryptu](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Użyj wpisu __skryptu__ , aby wybrać utworzony skrypt. Aby użyć niestandardowego skryptu, wybierz opcję __niestandardowy__. Podaj __nazwę__ i __Identyfikator URI skryptu bash__ dla skryptu.

    ![Dodawanie skryptu w formularzu wybierania skryptu](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy w formularzu:

    | Właściwość | Value |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz opcję __niestandardowy__. W przeciwnym razie wybierz jeden z podanych skryptów. |
    | Name |Określ nazwę akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Head/Worker/Zookeeper |Określ węzły, na których jest uruchamiany skrypt: **Head**, **Worker**, lub **ZooKeeper**. |
    | Parametry |Określ parametry, jeśli są wymagane przez skrypt. |

    Aby upewnić się, że skrypt jest stosowany podczas operacji skalowania, należy użyć wpisu __akcji Utrwalaj ten skrypt__ .

5. Wybierz pozycję __Utwórz__ , aby zapisać skrypt. Następnie możesz użyć __+ Prześlij nowy__ , aby dodać kolejny skrypt.

    ![Wiele akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Po zakończeniu dodawania skryptów wybierz przycisk __Wybierz__ , a następnie przycisk __dalej__ , aby powrócić do sekcji __Podsumowanie klastra__ .

3. Aby utworzyć klaster, wybierz pozycję __Utwórz__ na podstawie __podsumowania klastra__ .

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Używanie akcji skryptu z szablonów Azure Resource Manager

Akcji skryptu można używać z szablonami Azure Resource Manager. Aby zapoznać się z przykładem, zobacz [Tworzenie klastra usługi HDInsight w systemie Linux i uruchamianie akcji skryptu](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

W tym przykładzie akcja skryptu jest dodawana przy użyciu następującego kodu:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Uzyskaj więcej informacji na temat wdrażania szablonu:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Użyj akcji skryptu podczas tworzenia klastra z Azure PowerShell

W tej sekcji należy użyć polecenia cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) w celu wywołania skryptów w celu dostosowania klastra. Przed rozpoczęciem upewnij się, że zainstalowano i skonfigurowano Azure PowerShell. Aby można było używać tych poleceń programu PowerShell, należy użyć polecenia [AZ module](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższy skrypt pokazuje, jak zastosować akcję skryptu podczas tworzenia klastra przy użyciu programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Utworzenie klastra może potrwać kilka minut.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Używanie akcji skryptu podczas tworzenia klastra z poziomu zestawu .NET SDK usługi HDInsight

Zestaw SDK platformy .NET dla usługi HDInsight zawiera biblioteki klienckie, które ułatwiają pracę z usługą HDInsight z poziomu aplikacji .NET. Aby uzyskać przykład kodu, zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Zastosuj akcję skryptu do działającego klastra

W tej sekcji wyjaśniono, jak zastosować akcje skryptu do działającego klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Zastosuj akcję skryptu do działającego klastra z Azure Portal

Przejdź do [Azure Portal](https://portal.azure.com):

1. Z menu po lewej stronie wybierz pozycję **wszystkie usługi**.

1. W obszarze **Analiza**wybierz pozycję **Klastry usługi HDInsight**.

1. Wybierz z listy klaster, który otworzy widok domyślny.

1. W widoku domyślnym w obszarze **Ustawienia**wybierz pozycję **Akcje skryptu**.

1. W górnej części strony **Akcje skryptu** wybierz pozycję **+ Prześlij nowy**.

    ![Dodawanie skryptu do działającego klastra](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Użyj wpisu __skryptu__ , aby wybrać utworzony skrypt. Aby użyć niestandardowego skryptu, wybierz opcję __niestandardowy__. Podaj __nazwę__ i __Identyfikator URI skryptu bash__ dla skryptu.

    ![Dodawanie skryptu w formularzu wybierania skryptu](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy w formularzu:

    | Właściwość | Value |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz opcję __niestandardowy__. W przeciwnym razie wybierz podany skrypt. |
    | Name |Określ nazwę akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Head/Worker/Zookeeper |Określ węzły, na których jest uruchamiany skrypt: **Head**, **Worker**, lub **ZooKeeper**. |
    | Parametry |Określ parametry, jeśli są wymagane przez skrypt. |

    Użyj wpisu __Akcja Utrwalaj ten skrypt__ , aby upewnić się, że skrypt jest stosowany podczas operacji skalowania.

5. Na koniec wybierz przycisk **Utwórz** , aby zastosować skrypt do klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Zastosuj akcję skryptu do działającego klastra z Azure PowerShell

Aby można było używać tych poleceń programu PowerShell, należy użyć polecenia [AZ module](https://docs.microsoft.com/powershell/azure/overview).

Poniższy przykład pokazuje, jak zastosować akcję skryptu do działającego klastra:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po zakończeniu operacji otrzymujesz informacje podobne do następującego tekstu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Stosowanie akcji skryptu do działającego klastra z poziomu interfejsu wiersza polecenia platformy Azure

Przed rozpoczęciem upewnij się, że zainstalowano i skonfigurowano interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie klasycznego interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Przełącz do trybu Azure Resource Manager:

    ```bash
    azure config mode arm
    ```

2. Uwierzytelnianie w ramach subskrypcji platformy Azure:

    ```bash
    azure login
    ```

3. Zastosuj akcję skryptu do działającego klastra:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    W przypadku pominięcia parametrów dla tego polecenia zostanie wyświetlony monit o ich podanie. Jeśli skrypt określony przy użyciu `-u` przyjmuje parametry, można je określić za `-p` pomocą parametru.

    Prawidłowymi typami węzłów `headnode`są `workernode`,, `zookeeper`i. Jeśli skrypt ma być stosowany do kilku typów węzłów, określ typy rozdzielone średnikami `;`. Na przykład `-n headnode;workernode`.

    Aby zachować skrypt, Dodaj `--persistOnSuccess`. Możesz również utrzymać skrypt później za pomocą polecenia `azure hdinsight script-action persisted set`.

    Po zakończeniu zadania otrzymujesz dane wyjściowe podobne do następującego tekstu:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Stosowanie akcji skryptu do działającego klastra przy użyciu interfejsu API REST

Zobacz [interfejs API REST klastra w usłudze Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Stosowanie akcji skryptu do działającego klastra z zestawu .NET SDK usługi HDInsight

Aby zapoznać się z przykładem użycia zestawu SDK platformy .NET do zastosowania skryptów do klastra, zobacz temat [stosowanie akcji skryptu względem działającego klastra usługi HDInsight opartego na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Wyświetlanie historii i promowanie i obniżanie poziomu akcji skryptu

### <a name="the-azure-portal"></a>Witryna Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Z menu po lewej stronie wybierz pozycję **wszystkie usługi**.

1. W obszarze **Analiza**wybierz pozycję **Klastry usługi HDInsight**.

1. Wybierz z listy klaster, który otworzy widok domyślny.

1. W widoku domyślnym w obszarze **Ustawienia**wybierz pozycję **Akcje skryptu**.

4. Historia skryptów dla tego klastra jest wyświetlana w sekcji Akcje skryptu. Te informacje obejmują listę utrwalonych skryptów. Poniższy zrzut ekranu pokazuje, że skrypt Solr został uruchomiony w tym klastrze. Zrzut ekranu nie pokazuje żadnych utrwalonych skryptów.

    ![Działania skryptu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Wybierz skrypt z historii, aby wyświetlić sekcję **Właściwości** tego skryptu. W górnej części ekranu możesz ponownie uruchomić skrypt lub go podwyższyć.

    ![Akcje skryptu, właściwości](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Możesz również wybrać wielokropek, **...** , na prawo od wpisów w sekcji Akcje skryptu, aby wykonać akcje.

    ![Akcje skryptu, wielokropek](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Parametr | Funkcja |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Pobierz informacje o akcjach utrwalonego skryptu. |
| `Get-AzHDInsightScriptActionHistory` |Pobierz historię akcji skryptu zastosowanych do klastra lub szczegółowych informacji dotyczących określonego skryptu. |
| `Set-AzHDInsightPersistedScriptAction` |Podnieś akcję skryptu ad hoc do utrwalonej akcji skryptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Obniż poziom utrwalonej akcji skryptu do akcji ad hoc. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction`nie cofa akcji wykonywanych przez skrypt. To polecenie cmdlet usuwa tylko flagę PERSISTED.

Poniższy przykładowy skrypt demonstruje użycie poleceń cmdlet do podniesienia poziomu i obniżenia poziomu skryptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

| Parametr | Funkcja |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Pobierz listę utrwalonych akcji skryptów. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Pobierz informacje o określonej akcji utrwalonego skryptu. |
| `azure hdinsight script-action history list <clustername>` |Pobierz historię akcji skryptu zastosowanych do klastra. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Pobierz informacje dotyczące konkretnej akcji skryptu. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Podnieś akcję skryptu ad hoc do utrwalonej akcji skryptu. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Obniż poziom utrwalonej akcji skryptu do akcji ad hoc. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete`nie cofa akcji wykonywanych przez skrypt. To polecenie cmdlet usuwa tylko flagę PERSISTED.

### <a name="the-hdinsight-net-sdk"></a>Zestaw SDK usługi HDInsight dla platformy .NET

Aby zapoznać się z przykładem użycia zestawu SDK platformy .NET do pobierania historii skryptów z klastra, podniesienia lub obniżenia poziomu skryptów, zobacz temat [stosowanie akcji skryptu względem uruchomionego klastra usługi HDInsight opartego na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> W tym przykładzie pokazano również, jak zainstalować aplikację usługi HDInsight przy użyciu zestawu .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Obsługa oprogramowania Open Source używanego w klastrach usługi HDInsight

Usługa Microsoft Azure HDInsight używa ekosystemu technologii typu "open source" utworzonych wokół Apache Hadoop. Microsoft Azure zapewnia ogólny poziom wsparcia dla technologii typu open source. Aby uzyskać więcej informacji, zapoznaj się z sekcją dotyczącej **zakresu pomocy** [technicznej platformy Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom wsparcia dla wbudowanych składników programu.

W usłudze HDInsight są dostępne dwa typy składników typu "open source":

* **Składniki wbudowane**. Te składniki są wstępnie instalowane w klastrach usługi HDInsight i zapewniają podstawowe funkcje klastra. Następujące składniki należą do tej kategorii:

  * [Przędza Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)języka zapytań programu Hive.
  * [Apache Mahout](https://mahout.apache.org/). 
    
    Pełna lista składników klastra jest dostępna w temacie [co to są składniki Apache Hadoop i wersje dostępne w usłudze HDInsight?](hdinsight-component-versioning.md)

* **Składniki niestandardowe**. Jako użytkownik klastra można zainstalować lub użyć w obciążeniu dowolny składnik dostępny w społeczności lub utworzony przez użytkownika.

> [!WARNING]  
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, aby ułatwić dalsze Rozwiązywanie problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać ten problem. Mogą też zadawać pytania o zaangażowanie dostępnych kanałów dla technologii open source, w przypadku których zostanie znaleziona Szczegółowa wiedza dla tej technologii. Można użyć wielu witryn społeczności. Przykłady to [forum MSDN dotyczące usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) i [Stack Overflow](https://stackoverflow.com). 
>
> Projekty Apache zawierają również witryny projektu w [witrynie Apache](https://apache.org). Przykładem jest usługa [Hadoop](https://hadoop.apache.org/).

Usługa HDInsight oferuje kilka sposobów korzystania ze składników niestandardowych. Ten sam poziom wsparcia ma zastosowanie, niezależnie od tego, w jaki sposób składnik jest używany lub instalowany w klastrze. Na poniższej liście opisano najczęstsze sposoby używania składników niestandardowych w klastrach usługi HDInsight:

1. **Przesyłanie zadania**. Usługa Hadoop lub inne typy zadań, które wykonują lub używają niestandardowych składników można przesłać do klastra.

2. **Dostosowywanie klastra**. Podczas tworzenia klastra można określić dodatkowe ustawienia i składniki niestandardowe, które są zainstalowane w węzłach klastra.

3. **Przykłady**. W przypadku popularnych składników niestandardowych firma Microsoft i inne mogą przedstawić Przykłady sposobu używania tych składników w klastrach usługi HDInsight. Te przykłady są udostępniane bez pomocy technicznej.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Za pomocą interfejsu użytkownika sieci Web Ambari można wyświetlać informacje zarejestrowane przez akcje skryptu. Jeśli skrypt zakończy się niepowodzeniem podczas tworzenia klastra, dzienniki są również dostępne w domyślnym koncie magazynu skojarzonym z klastrem. Ta sekcja zawiera informacje dotyczące sposobu pobierania dzienników przy użyciu obu tych opcji.

### <a name="the-apache-ambari-web-ui"></a>Interfejs użytkownika sieci Web Apache Ambari

1. W przeglądarce przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

    Po wyświetleniu monitu wprowadź nazwę konta administratora, **administratora**i hasła dla klastra. Może zajść konieczność wprowadzenia ponownie poświadczeń administratora w formularzu sieci Web.

2. Na pasku w górnej części strony wybierz wpis **Ops** . Na liście są wyświetlane bieżące i poprzednie operacje wykonywane w klastrze za pomocą Ambari.

    ![Pasek interfejsu użytkownika sieci Web Ambari z wybranym elementem Ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Znajdź w kolumnie **operacje** wpisy **z\_uruchomioną customscriptactioną** . Te wpisy są tworzone po uruchomieniu akcji skryptu.

    ![Zrzut ekranu operacji](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Aby wyświetlić dane wyjściowe **stdout** i **stderr** , wybierz wpis **run\customscriptaction** i przejdź do szczegółów przez linki. Ta wartość wyjściowa jest generowana, gdy skrypt jest uruchamiany i może mieć przydatne informacje.

### <a name="access-logs-from-the-default-storage-account"></a>Dostęp do dzienników z domyślnego konta magazynu

Jeśli utworzenie klastra zakończy się niepowodzeniem z powodu błędu skryptu, dzienniki są przechowywane na koncie magazynu klastra.

* Dzienniki magazynu są dostępne pod adresem `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Dzienniki akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    W tym katalogu dzienniki są zorganizowane osobno dla węzła **węzła głównego**, **Node-Worker**i **dozorcy**. Zobacz poniższe przykłady:

    * **Węzła głównego**:`<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Węzeł procesu roboczego**:`<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Węzeł dozorcy**:`<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Wszystkie **stdout** i **stderr** odpowiedniego hosta są przekazywane do konta magazynu. Istnieje jedno **wyjście-\*. txt** i **Błędy-\*. txt** dla każdej akcji skryptu. Plik **Output-*. txt** zawiera informacje o identyfikatorze URI skryptu, który został uruchomiony na hoście. Poniżej przedstawiono przykład tych informacji:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Istnieje możliwość wielokrotnego utworzenia klastra akcji skryptu o tej samej nazwie. W takim przypadku można odróżnić odpowiednie dzienniki na podstawie nazwy folderu **daty** . Na przykład struktura folderów dla klastra, obiekt **webcluster**, utworzony w różnych datach, wygląda podobnie jak w przypadku następujących wpisów dziennika:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04``\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Jeśli utworzysz klaster akcji skryptu o tej samej nazwie w tym samym dniu, możesz użyć unikatowego prefiksu, aby zidentyfikować odpowiednie pliki dziennika.

* Jeśli utworzysz klaster o 12:00 AM i północy, istnieje możliwość, że pliki dziennika obejmują dwa dni. W takim przypadku zobaczysz dwa różne foldery dat dla tego samego klastra.

* Przekazywanie plików dziennika do kontenera domyślnego może potrwać do 5 minut, szczególnie w przypadku dużych klastrów. Dlatego jeśli chcesz uzyskać dostęp do dzienników, nie musisz natychmiast usunąć klastra, jeśli akcja skryptu zakończy się niepowodzeniem.

### <a name="ambari-watchdog"></a>Ambari = licznik

> [!WARNING]  
> Nie zmieniaj hasła dla Ambari licznika alarmowego hdinsightwatchdog, w klastrze usługi HDInsight opartej na systemie Linux. Zmiana hasła dla tego konta powoduje przerwanie możliwości uruchamiania nowych akcji skryptów w klastrze usługi HDInsight.

### <a name="cant-import-name-blobservice"></a>Nie można zaimportować nazwy BlobService

__Objawy__. Akcja skryptu kończy się niepowodzeniem. Podczas wyświetlania operacji w Ambari jest wyświetlany tekst podobny do następującego błędu:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Przyczyna__. Ten błąd występuje w przypadku uaktualniania klienta usługi Azure Storage w języku Python, który jest dołączony do klastra HDInsight. Usługa HDInsight oczekuje 0.20.0 klienta usługi Azure Storage.

__Rozwiązanie__. Aby rozwiązać ten problem, należy ręcznie połączyć się z każdym węzłem `ssh`klastra przy użyciu polecenia. Uruchom następujące polecenie, aby ponownie zainstalować poprawną wersję klienta magazynu:

```bash
sudo pip install azure-storage==0.20.0
```

Aby uzyskać informacje na temat nawiązywania połączenia z klastrem przy użyciu protokołu SSH, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) za pośrednictwem protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>W historii nie są wyświetlane skrypty używane podczas tworzenia klastra

Jeśli klaster został utworzony przed 15 marca 2016, w historii akcji skryptu może nie być wyświetlany wpis. Zmiany rozmiarów klastra powodują, że skrypty pojawiają się w historii akcji skryptu.

Istnieją jednak dwa wyjątki:

* Klaster został utworzony przed 1 września 2015. Ta data jest w przypadku wprowadzenia akcji skryptu. Każdy klaster utworzony przed tą datą nie mógł używać akcji skryptu do tworzenia klastra.

* Podczas tworzenia klastra użyto wielu akcji skryptu. Lub użyto tej samej nazwy dla wielu skryptów lub tej samej nazwy, tego samego identyfikatora URI, ale różnych parametrów dla wielu skryptów. W takich przypadkach zostanie wyświetlony następujący błąd:

    Żadne nowe akcje skryptu nie mogą być uruchamiane w tym klastrze ze względu na konflikt nazw skryptów w istniejących skryptach. Nazwy skryptów podane podczas tworzenia klastra muszą być unikatowe. Istniejące skrypty są uruchamiane przy zmianie rozmiaru.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalowanie i używanie oprogramowania Apache Giraph w klastrach usługi HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Dodawanie dodatkowego magazynu do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"

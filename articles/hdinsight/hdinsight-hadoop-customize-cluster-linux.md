---
title: Dostosowywanie klastrów usługi Azure HDInsight przy użyciu akcji skryptu
description: Dodaj niestandardowe składniki w klastrach HDInsight opartych na systemie Linux przy użyciu akcji skryptu. Akcje skryptu to skrypty powłoki Bash, które mogą służyć do dostosowywania konfiguracji klastra lub dodać dodatkowych usług i narzędzi, takich jak Hue, Solr lub języka R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 7885b03e9f92fc8e8c5b2c78049760cbed8d4dc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703961"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Dostosowywanie klastrów usługi Azure HDInsight przy użyciu akcji skryptu

Usługa Azure HDInsight udostępnia metodę konfiguracji o nazwie **akcji skryptu** wywołującej skryptów niestandardowych, aby dostosować klastra. Skrypty te służą do instalowania dodatkowych składników i zmianę ustawień konfiguracji. Akcje skryptu może służyć w trakcie lub po utworzeniu klastra.

Akcje skryptu można także publikować w portalu Azure Marketplace jako aplikacji HDInsight. Aby uzyskać więcej informacji na temat aplikacji HDInsight, zobacz [publikowania aplikacji HDInsight w portalu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Uprawnienia

Dla klastra HDInsight przyłączone do domeny istnieją dwa uprawnienia Apache Ambari, które są wymagane w przypadku użyj akcji skryptu za pomocą klastra:

* **AMBARI. URUCHOM\_NIESTANDARDOWE\_POLECENIA**. Domyślnie rola administratora Ambari ma to uprawnienie.
* **KLASTER. URUCHOM\_NIESTANDARDOWE\_POLECENIA**. Administrator klastra HDInsight i administratora Ambari domyślnie mają to uprawnienie.

Aby uzyskać więcej informacji na temat pracy z uprawnień za pomocą HDInsight przyłączone do domeny, zobacz [Zarządzanie HDInsight clusters z pakietem Enterprise Security](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Kontrola dostępu

Jeśli nie jesteś administratorem ani właścicielem subskrypcji platformy Azure, Twoje konto musi mieć co najmniej dostęp współautora do grupy zasobów, która zawiera klaster HDInsight.

Jeśli utworzysz klaster usługi HDInsight, inna osoba z co najmniej dostęp współautora do subskrypcji platformy Azure musi zostać wcześniej zarejestrowany dostawca HDInsight. Rejestracja zasobu ma miejsce, gdy użytkownik o roli Współautor z dostępem do subskrypcji tworzy zasób po raz pierwszy w subskrypcji. Można także przeprowadzić bez tworzenia zasobu, jeśli użytkownik [zarejestrować dostawcę za pomocą interfejsu REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Uzyskaj więcej informacji na temat pracy z zarządzania dostępem:

* [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
* [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Informacje o akcjach skryptu

Akcja skryptu jest skrypt powłoki Bash, działającego w węzłach w klastrze usługi HDInsight. Cechy i funkcje akcje skryptu są następujące:

* Muszą być przechowywane w identyfikatorze URI, który jest dostępny z klastra HDInsight. Lokalizacje przechowywania możliwe są następujące:
    
    * W przypadku klastrów regularne:
    
      * Azure Data Lake Store Gen1: Nazwa główna usługi HDInsight używane do dostępu do usługi Data Lake Storage przez musi mieć dostęp do odczytu do skryptu. Format identyfikatora URI dla skryptów przechowywanych w Data Lake Storage Gen1 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.
      
      * Obiektu blob na koncie Azure Storage, która jest konto magazynu podstawowej lub dodatkowej w przypadku klastra HDInsight. HDInsight udzielany jest dostęp do obu tych rodzajów kont magazynu, podczas tworzenia klastra.

        > [!IMPORTANT]  
        > Nie wymienić główny klucz magazynu dla tego konta usługi Azure Storage, ponieważ spowoduje to, że akcje kolejnych skryptu za pomocą skryptów tam przechowywane nie powiedzie się.

      * Publiczne udostępniania plików usługa za pośrednictwem ścieżki http://. Przykładami są usługi Azure Blob, GitHub, usłudze OneDrive.

        Na przykład zobacz identyfikatorów URI [przykładowe skrypty akcji skryptu](#example-script-action-scripts).

     * W przypadku klastrów z ESP:
         
         * Wasb: / / lub wasbs: / / lub http [s] :// identyfikatory URI są obsługiwane.
            
* Można ograniczyć do uruchamiania w ramach określonych typów węzłów. Przykładami są węzłów głównych lub węzłów procesu roboczego.

* Może być utrwalona lub ad-hoc.

    Utrwalonych skryptów są używane do dostosowywania nowych węzłach procesów roboczych dodane do klastra za pośrednictwem operacji skalowania. Utrwalonego skryptu może także Zastosuj zmiany do innego typu węzła, po wystąpieniu operacji skalowania. Przykładem jest węzła głównego.

  > [!IMPORTANT]  
  > Akcje utrwalonego skryptu musi mieć unikatową nazwę.

    Skryptów ad hoc nie są zachowywane. Nie są one stosowane do węzłów procesu roboczego dodane do klastra, po uruchomieniu skryptu. Następnie można promować skryptu ad hoc do utrwalonego skryptu lub obniżyć poziom utrwalonego skryptu do skryptu ad-hoc.

  > [!IMPORTANT]  
  > Akcje skryptu używany podczas tworzenia klastra, automatycznie są zachowywane.
  >
  > Skrypty, które nie są nie są utrwalane, nawet jeśli specjalnie wskazujesz, że powinny one być.

* Może akceptować parametry, które są używane przez skrypt podczas wykonywania.

* Uruchom z uprawnieniami na poziomie głównym w węzłach klastra.

* Może służyć za pośrednictwem witryny Azure portal, programu Azure PowerShell, Azure klasyczny interfejs wiersza polecenia lub zestawu .NET SDK HDInsight.

Klaster przechowuje historię wszystkie skrypty, które zostały uruchomione. Historia pomaga, gdy trzeba znaleźć Identyfikatora skryptu dla operacji podwyższenia poziomu lub obniżania poziomu.

> [!IMPORTANT]  
> Nie ma żadnych automatyczny sposób, aby cofnąć zmiany wprowadzone przez akcję skryptu. Ręcznie wycofania zmian lub napisać skrypt, który odwraca je.

### <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w procesie tworzenia klastra

Akcje skryptu używany podczas tworzenia klastra są nieco inne niż akcji skryptu, uruchom w istniejącym klastrze:

* Skrypt jest automatycznie zachowywane.

* Błąd w skrypcie może spowodować niepowodzenie procesu tworzenia klastra.

Na poniższym diagramie przedstawiono uruchomienie w trakcie procesu tworzenia akcji skryptu:

![Dostosowywanie klastra HDInsight i etapy podczas tworzenia klastra][img-hdi-cluster-states]

Skrypt jest uruchamiany, gdy skonfigurowano HDInsight. Skrypt jest uruchamiany równolegle w określonych węzłów w klastrze. Działa z uprawnień użytkownika root w węzłach.

> [!NOTE]  
> Można wykonywać operacje, takie jak zatrzymanie i uruchomienie usług, w tym usług związanych z platformy Apache Hadoop. Zatrzymanie usług, upewnij się, że usługa Ambari i innych usług związanych z usługi Hadoop są uruchomione przed zakończeniem skryptu. Te usługi są wymagane do określenia pomyślnie kondycję i stan klastra podczas jego tworzenia.


Podczas tworzenia klastra można użyć wielu akcji skryptu na raz. Te skrypty są wywoływane w kolejności, w którym zostały określone.

> [!IMPORTANT]  
> Akcje skryptu musi się zakończyć w ciągu 60 minut lub przekraczają limit czasu. Podczas inicjowania obsługi klastra, skrypt jest uruchamiany równolegle innych procesów instalacji i konfiguracji. Konkurencję w odniesieniu do zasobów, takich jak czas lub sieci przepustowości Procesora może powodować skryptu, aby zająć dłużej niż w środowisku programistycznym.
>
> Aby zminimalizować czas potrzebny do uruchomienia skryptu, należy unikać zadania, takie jak pobieranie i kompilowanie aplikacji ze źródła. Wstępna kompilacja aplikacji i przechowywanie danych binarnych w usłudze Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Akcja skryptu na działającego klastra

Wystąpił błąd podczas uruchamiania w klastrze już uruchomionego skryptu nie automatycznie powoduje klastra zmienić stan nie powiodło się. Po zakończeniu działania skryptu klastra powinien zwrócić do stanu roboczego.

> [!IMPORTANT]  
> Nawet wtedy, gdy klaster ma stan uruchomione, skryptu nie powiodło się może być uszkodzone elementy. Na przykład skrypt może usunąć pliki wymagane przez klaster.
>
> Akcje skrypty uruchamiane przy użyciu uprawnień głównych. Upewnij się, że rozumiesz, co to jest skryptem przed zastosowaniem do klastra.

Jeśli zastosujesz skrypt w klastrze, stan klastra zmieni się z **systemem** do **zaakceptowano**. A następnie zmienia się na **konfiguracji HDInsight** a na koniec z powrotem do **systemem** pomyślne skryptów. Stan skryptu są rejestrowane w historii akcji skryptu. Te informacje informujący o tym, czy skrypt zakończonych powodzeniem lub niepowodzeniem. Na przykład `Get-AzHDInsightScriptActionHistory` polecenia cmdlet programu PowerShell pokazuje stan skryptu. Zwraca informacje podobne do następującego tekstu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Jeśli zmienisz użytkownika klastra, administrator haseł po utworzeniu klastra akcji skryptu, uruchamiać dla tego klastra może zakończyć się niepowodzeniem. Jeśli masz wszystkie akcje utrwalonego skryptu tej węzłów procesu roboczego w docelowej tych skryptów może zakończyć się niepowodzeniem podczas skalowania klastra.

## <a name="example-script-action-scripts"></a>Przykładowe skrypty akcji skryptu

Można używać skryptów akcji skryptu za pomocą następujących narzędzi:

* Witryna Azure Portal
* Azure PowerShell
* Klasyczny interfejs wiersza polecenia Azure
* Zestaw SDK HDInsight platformy .NET

HDInsight zapewnia skrypty dopasowane do zainstalowania następujących składników w klastrach HDInsight:

| Name (Nazwa) | Skrypt |
| --- | --- |
| Dodaj konto usługi Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zobacz [dodawanie kolejnych kont magazynu do HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalowanie rozwiązania Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zobacz [instalacji i używania aplikacji Hue w usłudze HDInsight Hadoop clusters](hdinsight-hadoop-hue-linux.md). |
| Zainstalować system Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Zobacz [zainstalować system Giraph Apache na HDInsight Hadoop clusters](hdinsight-hadoop-giraph-install-linux.md). |
| Wstępne ładowanie bibliotek technologii Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zobacz [Dodawanie niestandardowych bibliotek technologii Hive, podczas tworzenia klastra usługi HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Za pomocą akcji skryptu, podczas tworzenia klastra

W tej sekcji opisano różne sposoby, można użyć akcji skryptu, podczas tworzenia klastra usługi HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Za pomocą akcji skryptu, podczas tworzenia klastra w witrynie Azure portal

1. Uruchom, aby utworzyć klaster, zgodnie z opisem w [konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i](hdinsight-hadoop-provision-linux-clusters.md). Podczas tworzenia klastra zostanie wyświetlony __klastra Podsumowanie__ strony. Z __klastra Podsumowanie__ wybierz opcję __Edytuj__ łączy dla __Zaawansowane ustawienia__.

    ![Link do ustawień zaawansowanych](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Z __Zaawansowane ustawienia__ zaznacz __akcji skryptu__. Z __akcji skryptu__ zaznacz __+ Prześlij nowe__.

    ![Przesłać nową akcję skryptu](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Użyj __wybierz skrypt__ wejścia Wykorzystaj skrypt. Aby użyć niestandardowego skryptu, wybierz __niestandardowe__. Następnie podaj __nazwa__ i __identyfikator URI skryptu powłoki systemowej__ skryptu.

    ![Dodawanie skryptu w formularzu Wybierz skrypt](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy na formularzu:

    | Właściwość | Value |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz __niestandardowe__. W przeciwnym razie wybierz dostarczone skrypty. |
    | Name (Nazwa) |Określ nazwę dla akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Head/Worker/Zookeeper |Określ węzły, na których jest uruchamiany skrypt: **Head**, **Worker**, lub **ZooKeeper**. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __Utrwal tę akcję skryptu__ wpis, aby upewnić się, że skrypt jest stosowana podczas operacji skalowania.

5. Wybierz __Utwórz__ można zapisać skryptu. Następnie można użyć __+ Prześlij nowe__ można dodać inny skrypt.

    ![Wiele akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Po zakończeniu dodawania skryptów wybierz __wybierz__ przycisk i następnie __dalej__ przycisk, aby powrócić do __podsumowanie klastra__ sekcji.

3. Aby utworzyć klaster, wybierz __Utwórz__ z __podsumowanie klastra__ zaznaczenia.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Użyj akcji skryptu za pomocą szablonów usługi Azure Resource Manager

Akcje skryptu może służyć za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać przykład, zobacz [Tworzenie klastra systemu Linux HDInsight i wykonywania akcji skryptu](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

W tym przykładzie akcji skryptu zostanie dodany przy użyciu następującego kodu:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Uzyskaj więcej informacji na temat sposobu wdrażania szablonu:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Za pomocą akcji skryptu, podczas tworzenia klastra za pomocą programu Azure PowerShell

W tej sekcji użyjesz [AzHDInsightScriptAction Dodaj](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) polecenia cmdlet, wywoływanie skryptów, aby dostosować klastra. Przed rozpoczęciem upewnij się, instalowanie i konfigurowanie programu Azure PowerShell. Aby użyć tych poleceń programu PowerShell, musisz mieć [modułu AZ](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższy skrypt pokazuje, jak zastosować akcji skryptu, podczas tworzenia klastra przy użyciu programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Może upłynąć kilka minut, zanim został utworzony klaster.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Za pomocą akcji skryptu, podczas tworzenia klastra z zestawu SDK HDInsight platformy .NET

Zestaw .NET SDK HDInsight zawiera biblioteki klienckie, które ułatwiają pracować HDInsight z poziomu aplikacji .NET. Przykładowy kod, zobacz [Tworzenie klastrów z systemem Linux w HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Stosowanie akcji skryptu do działającego klastra

W tej sekcji wyjaśniono, jak stosować akcje skryptu do działającego klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Dotyczą akcji skryptu działającego klastra w witrynie Azure portal

Przejdź do [witryny Azure portal](https://portal.azure.com):

1. Z menu po lewej stronie wybierz **wszystkich usług**.

1. W obszarze **ANALYTICS**, wybierz opcję **klastry HDInsight**.

1. Wybierz klaster, z listy, co spowoduje otwarcie widoku domyślnego.

1. W widoku domyślnego w obszarze **ustawienia**, wybierz opcję **akcji skryptu**.

1. W górnej części **akcji skryptu** wybierz opcję **+ Prześlij nowe**.

    ![Dodawanie skryptu do działającego klastra](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Użyj __wybierz skrypt__ wejścia Wykorzystaj skrypt. Aby użyć niestandardowego skryptu, wybierz __niestandardowe__. Następnie podaj __nazwa__ i __identyfikator URI skryptu powłoki systemowej__ skryptu.

    ![Dodawanie skryptu w formularzu Wybierz skrypt](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy na formularzu:

    | Właściwość | Wartość |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz __niestandardowe__. W przeciwnym razie wybierz dostarczonego skryptu. |
    | Name (Nazwa) |Określ nazwę dla akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Head/Worker/Zookeeper |Określ węzły, na których jest uruchamiany skrypt: **Head**, **Worker**, lub **ZooKeeper**. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __Utrwal tę akcję skryptu__ wpis, aby upewnić się, czy skrypt jest stosowane podczas operacji skalowania.

5. Na koniec wybierz pozycję **Utwórz** przycisk, aby zastosować skrypt do klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Dotyczą akcji skryptu działającego klastra za pomocą programu Azure PowerShell

Aby użyć tych poleceń programu PowerShell, musisz mieć [modułu AZ](https://docs.microsoft.com/powershell/azure/overview).

Poniższy przykład przedstawia sposób stosowania akcji skryptu do działającego klastra:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po zakończeniu operacji otrzymasz informacje podobne do następującego tekstu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Zastosuj akcję skryptu do działającego klastra z wiersza polecenia platformy Azure

Przed rozpoczęciem upewnij się, instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [zainstalować Azure klasyczny interfejs wiersza polecenia](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Przełącz do trybu usługi Azure Resource Manager:

    ```bash
    azure config mode arm
    ```

2. Uwierzytelnianie do swojej subskrypcji platformy Azure:

    ```bash
    azure login
    ```

3. Zastosuj akcję skryptu do działającego klastra:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Jeżeli pominięto parametrów dla tego polecenia zostanie wyświetlony monit o ich. Jeśli skrypt zostanie określona z `-u` akceptuje parametry, określ je przy użyciu `-p` parametru.

    Nieprawidłowy węzeł typy są `headnode`, `workernode`, i `zookeeper`. Jeśli skrypt powinien być stosowany do kilku typów węzłów, należy określić typy, rozdzielając je średnikiem `;`. Na przykład `-n headnode;workernode`.

    Aby zachować skrypt, należy dodać `--persistOnSuccess`. Można również utrwalić skrypt później za pomocą `azure hdinsight script-action persisted set`.

    Po zakończeniu zadania, otrzymasz dane wyjściowe podobne do następującego tekstu:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Dotyczą akcji skryptu działającego klastra przy użyciu interfejsu API REST

Zobacz [klastra interfejsu API REST w usłudze Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Dotyczą akcji skryptu działającego klastra z zestawu SDK HDInsight platformy .NET

Na przykład zastosować skryptów do klastra przy użyciu zestawu .NET SDK, zobacz [zastosowania akcji skryptu względem klastra HDInsight pracę opartą na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Wyświetl historię i promowania i obniżenie poziomu akcji skryptu

### <a name="the-azure-portal"></a>Witryna Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Z menu po lewej stronie wybierz **wszystkich usług**.

1. W obszarze **ANALYTICS**, wybierz opcję **klastry HDInsight**.

1. Wybierz klaster, z listy, co spowoduje otwarcie widoku domyślnego.

1. W widoku domyślnego w obszarze **ustawienia**, wybierz opcję **akcji skryptu**.

4. Wyświetla historię skrypty dla tego klastra w sekcji działania skryptu. Informacje te obejmują listę utrwalonych skryptów. Poniższy zrzut ekranu pokazuje, że skrypt Solr została uruchomiona na tym klastrze. Zrzut ekranu nie pokazuje żadnych utrwalonych skryptów.

    ![Akcje skryptu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Wybierz skrypt z historii, aby wyświetlić **właściwości** sekcji tego skryptu. W górnej części ekranu można ponownie uruchomić skrypt lub promowania go.

    ![Akcje skryptu, właściwości](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Możesz również wybrać przycisk wielokropka, **...** , po prawej stronie wpisy w sekcji Akcje skryptu do wykonania akcji.

    ![Akcje skryptu, wielokropka](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funkcja |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Pobieranie informacji na temat akcji utrwalonego skryptu. |
| `Get-AzHDInsightScriptActionHistory` |Pobieranie historii akcji skryptu, stosowane do klastra lub szczegóły dotyczące określonego skryptu. |
| `Set-AzHDInsightPersistedScriptAction` |Przyczyniają się do akcji skryptów ad hoc Akcja utrwalonego skryptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Obniż poziom akcji utrwalonego skryptu ad-hoc akcję. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` nie cofnięcie akcji wykonywanych przez skrypt. To polecenie cmdlet usuwa tylko Flaga utrwalonych.

Następujący skrypt przykładowy pokazuje promowania i następnie obniżenie poziomu skryptu za pomocą polecenia cmdlet.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia Azure

| cmdlet | Funkcja |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Pobierz listę akcje utrwalonego skryptu. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Pobieranie informacji na temat akcji określonych utrwalonego skryptu. |
| `azure hdinsight script-action history list <clustername>` |Pobieranie historii akcji skryptu, stosowane do klastra. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Pobieranie informacji na temat działania określonego skryptu. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Przyczyniają się do akcji skryptów ad hoc Akcja utrwalonego skryptu. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Obniż poziom akcji utrwalonego skryptu ad-hoc akcję. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` nie cofnięcie akcji wykonywanych przez skrypt. To polecenie cmdlet usuwa tylko Flaga utrwalonych.

### <a name="the-hdinsight-net-sdk"></a>HDInsight SDK platformy .NET

Na przykład pobrać skrypt historii z klastra przy użyciu zestawu .NET SDK, podwyższanie poziomu lub obniżenie poziomu skryptów, zobacz [ zastosowania akcji skryptu względem klastra HDInsight pracę opartą na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Również w tym przykładzie pokazano, jak instalowanie aplikacji usługi HDInsight przy użyciu zestawu .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Pomoc techniczna dotycząca oprogramowania typu open-source używane w klastrach HDInsight

Usługa Microsoft Azure HDInsight korzysta z ekosystemu technologii open source utworzona wokół Apache Hadoop. Microsoft Azure oferuje ogólnego poziomu pomocy technicznej dla technologii open source. Aby uzyskać więcej informacji, zobacz **zakres pomocy technicznej** części [— często zadawane pytania dla pomocy technicznej Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom pomocy technicznej dla wbudowanych składników.

Dwa typy składników typu open source są dostępne w usłudze HDInsight:

* **Wbudowane składniki**. Składniki te są wstępnie zainstalowane w klastrach HDInsight i zapewnia podstawowe funkcje klastra. Poniższe składniki należą do tej kategorii:

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Menedżera zasobów.
  * Język zapytań programu Hive [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/). 
    
    Pełną listę składniki klastra jest dostępna w [jakie są składniki platformy Apache Hadoop i wersje dostępne z HDInsight?](hdinsight-component-versioning.md)

* **Składniki niestandardowe**. Jako użytkownik klastra można zainstalować, lub użyj w obciążenia dowolny składnik, dostępne w społeczności lub utworzonej przez użytkownika.

> [!WARNING]  
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane. Microsoft Support pomaga wyizolować i rozwiązać problemy związane z tych składników.
>
> Składniki niestandardowe otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc Ci rozwiązać problem. Microsoft Support może być w stanie rozwiązać ten problem. Lub pracownik może poprosić o nawiązanie kanałów dostępnych dla technologii open source, w którym znajduje się specjalistyczna dla tej technologii. Wiele witryn społeczności, należy używać. Należą do nich [forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) i [Stack Overflow](https://stackoverflow.com). 
>
> Projektów Apache również mają projektu na witryn [witryny sieci Web Apache](https://apache.org). Na przykład [Hadoop](https://hadoop.apache.org/).

Usługa HDInsight oferuje kilka sposobów używania niestandardowych składników. Stosuje ten sam poziom pomocy technicznej niezależnie od tego, jak używać składnika lub zainstalowane w klastrze. Poniższa lista zawiera opis najbardziej typowych sposobów, że niestandardowe składniki są używane w klastrach HDInsight:

1. **Zadanie przesyłania**. Usługi Hadoop lub innych typów zadań, które wykonania lub użyć niestandardowych składników można przesyłać do klastra.

2. **Dostosowywanie klastra**. Podczas tworzenia klastra można określić dodatkowe ustawienia i niestandardowe składniki, które są instalowane w węzłach klastra.

3. **Przykłady**. Dla popularnych niestandardowych składników firma Microsoft i innych użytkowników może zawierać przykładów, jak te składniki mogą być używane w klastrach HDInsight. Te przykłady są udostępniane bez pomocy technicznej.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Interfejs użytkownika sieci web Ambari służy do wyświetlania informacji rejestrowanych przez akcji skryptu. Jeśli skrypt zakończy się niepowodzeniem podczas tworzenia klastra, dzienniki są również dostępne w skojarzonym z klastrem domyślne konto magazynu. Ta sekcja zawiera informacje dotyczące sposobu pobierania dzienników za pomocą obu tych opcji.

### <a name="the-apache-ambari-web-ui"></a>Apache Ambari web UI

1. W przeglądarce przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

    Po wyświetleniu monitu wprowadź nazwę konta administratora **administratora**i hasło dla klastra. Może być konieczne ponowne wprowadzenie poświadczeń administratora w formularzu sieci web.

2. Na pasku w górnej części strony wybierz **ops** wpisu. Na liście wyświetlane są aktualne i poprzednie operacje wykonywane w klastrze za pomocą systemu Ambari.

    ![Pasek interfejsu użytkownika sieci web Ambari, wraz z operacjami wybrane](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Znajdź pozycje, które mają **Uruchom\_customscriptaction** w **operacji** kolumny. Te wpisy są tworzone po uruchomieniu akcji skryptu.

    ![Zrzut ekranu przedstawiający operacje](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Aby wyświetlić **STDOUT** i **STDERR** danych wyjściowych, wybierz opcję **run\customscriptaction** wejścia i przechodzenie do szczegółów za pośrednictwem łącza. Tych danych wyjściowych jest generowany, gdy skrypt zostanie uruchomiony i może być przydatne informacje.

### <a name="access-logs-from-the-default-storage-account"></a>Uzyskiwanie dostępu do dzienników z domyślnego konta magazynu

Jeśli tworzenie klastra kończy się niepowodzeniem z powodu błędu skryptu, dzienniki są przechowywane na koncie magazynu klastra.

* Dzienniki magazynu są dostępne pod adresem `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Zrzut ekranu przedstawiający operacje](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    W tym katalogu, dzienniki są zorganizowane oddzielnie dla **węzeł główny**, **węzła procesu roboczego**, i **węzeł usługi zookeeper**. Zobacz poniższe przykłady:

    * **Węzeł główny**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Węzeł procesu roboczego**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Węzeł usługi zookeeper**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Wszystkie **stdout** i **stderr** odpowiedniego hosta jest przekazywany do konta magazynu. Istnieje **dane wyjściowe —\*.txt** i **błędy -\*.txt** dla każdej akcji skryptu. ***.Txt dane wyjściowe** plik zawiera informacje o identyfikatorze URI skryptu, która została uruchomiona na hoście. Przykładem tych informacji jest następujący tekst:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Istnieje możliwość, wielokrotne Tworzenie klastra akcji skryptu o takiej samej nazwie. W takim przypadku można odróżnić odpowiednie dzienniki na podstawie **data** nazwę folderu. Na przykład struktura folderów dla klastra, **mycluster**utworzony w różnych terminach wygląda podobnie do następujących wpisy dziennika:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04``\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Jeśli tworzysz klaster akcji skryptu o takiej samej nazwie w tym samym dniu umożliwia unikatowy prefiks zidentyfikować odpowiednich plików dziennika.

* Jeśli tworzysz klaster w pobliżu 12:00 AM, północy, jest możliwe, że pliki dziennika rozciągać się na dwa dni. W takim przypadku zobaczysz dwa foldery inną datę dla tego samego klastra.

* Przekazywanie plików dziennika na domyślny kontener może potrwać maksymalnie pięć minut, szczególnie w przypadku dużych klastrach. Dlatego jeśli chcesz uzyskać dostęp w dziennikach, nie należy natychmiast usunąć klaster w przypadku niepowodzenia akcji skryptu.

### <a name="ambari-watchdog"></a>Strażnika systemu Ambari

> [!WARNING]  
> Nie zmieniaj hasła dla strażnika Ambari, hdinsightwatchdog w klastrze usługi HDInsight opartych na systemie Linux. Zmienianie hasła dla tego konta spowoduje przerwanie możliwość uruchamiania nowe akcje skryptów w klastrze HDInsight.

### <a name="cant-import-name-blobservice"></a>Nie można zaimportować nazwa BlobService

__Objawy__. Akcja skryptu zakończy się niepowodzeniem. Tekst podobny do poniższego błędu wyświetlana podczas przeglądania operacji w Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Przyczyna__. Ten błąd występuje podczas uaktualniania klienta usługi Azure Storage dla języka Python, znajdującej się w klastrze HDInsight. HDInsight oczekuje, że klient usługi Azure Storage 0.20.0.

__Rozpoznawanie__. Aby rozwiązać ten problem, ręcznie połączyć się z każdego węzła klastra za pomocą `ssh`. Uruchom następujące polecenie, aby ponownie zainstalować magazynu poprawną wersję klienta:

```bash
sudo pip install azure-storage==0.20.0
```

Aby uzyskać informacji na temat łączenia się z klastrem przy użyciu protokołu SSH, zobacz [nawiązywanie połączenia z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historia nie pokazuje skrypty używane podczas tworzenia klastra

Jeśli klaster został utworzony przed 15 marca 2016 r., może nie być wyświetlana wpis w historii akcji skryptu. Zmiana rozmiaru klastra powoduje, że skrypty, które pojawią się w historii akcji skryptu.

Istnieją jednak dwa wyjątki:

* Klaster został utworzony przed 1 września 2015 r. Ta data jest datą akcji skryptu zostały wprowadzone. Każdy klaster utworzone przed tą datą nie były używane akcje skryptu do tworzenia klastra.

* Wiele akcji skryptu jest używany podczas tworzenia klastra. Lub taką samą nazwę dla wielu skryptów lub takiej samej nazwie, tego samego identyfikatora URI, ale różne parametry są używane dla wielu skryptów. W takich przypadkach zostanie wyświetlony następujący błąd:

    Nie nowe akcje skryptów można uruchomić w tym klastrze z powodu konfliktu nazw skryptu w istniejących skryptów. Nazwy skryptu, podane podczas tworzenia klastra muszą być unikatowe. Istniejące skrypty są uruchamiane przy zmianie rozmiaru.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie skryptów Akcja skryptu dla HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalowanie i używanie Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Dodaj dodatkowy magazyn do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"

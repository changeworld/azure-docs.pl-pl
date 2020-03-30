---
title: Dostosowywanie klastrów usługi Azure HDInsight przy użyciu akcji skryptów
description: Dodawanie składników niestandardowych do klastrów usługi HDInsight przy użyciu akcji skryptów. Akcje skryptów to skrypty Bash, których można użyć do dostosowania konfiguracji klastra lub dodania dodatkowych usług i narzędzi, takich jak Hue, Solr lub R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272502"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Dostosowywanie klastrów usługi Azure HDInsight przy użyciu akcji skryptów

Usługa Azure HDInsight udostępnia metodę konfiguracji o nazwie **akcje skryptu,** które wywołują skrypty niestandardowe w celu dostosowania klastra. Skrypty te są używane do instalowania dodatkowych składników i zmiany ustawień konfiguracji. Akcje skryptu mogą być używane podczas lub po utworzeniu klastra.

Akcje skryptu mogą być również publikowane w portalu Azure Marketplace jako aplikacja HDInsight. Aby uzyskać więcej informacji na temat aplikacji HDInsight, zobacz [Publikowanie aplikacji HDInsight w portalu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Uprawnienia

W przypadku klastra HDInsight przyłączony do domeny istnieją dwa uprawnienia Apache Ambari, które są wymagane podczas używania akcji skryptu z klastrem:

* **AMBARI. URUCHOM\_\_POLECENIE NIESTANDARDOWE**. Rola Administrator Ambari ma to uprawnienie domyślnie.
* **KLASTRA. URUCHOM\_\_POLECENIE NIESTANDARDOWE**. Zarówno administrator klastra HDInsight, jak i administrator ambari mają domyślnie to uprawnienie.

Aby uzyskać więcej informacji na temat pracy z uprawnieniami z dostępem hdinsight przyłączony do domeny, zobacz [Zarządzanie klastrami HDInsight za pomocą pakietu zabezpieczeń przedsiębiorstwa](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Kontrola dostępu

Jeśli nie jesteś administratorem lub właścicielem subskrypcji platformy Azure, twoje konto musi mieć co najmniej dostęp współautora do grupy zasobów, która zawiera klaster HDInsight.

Jeśli tworzysz klaster HDInsight, osoba z co najmniej współautorem dostępu do subskrypcji platformy Azure musi wcześniej zarejestrować dostawcę dla usługi HDInsight. Rejestracja zasobu ma miejsce, gdy użytkownik o roli Współautor z dostępem do subskrypcji tworzy zasób po raz pierwszy w subskrypcji. Można to również zrobić bez tworzenia zasobu, jeśli [zarejestrujesz dostawcę przy użyciu REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Uzyskaj więcej informacji na temat pracy z zarządzaniem dostępem:

* [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
* [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Opis akcji skryptu

Akcja skryptu to skrypt Bash, który działa w węzłach w klastrze HDInsight. Cechy i cechy akcji skryptu są następujące:

* Muszą być przechowywane w identyfikatorze URI, który jest dostępny z klastra HDInsight. Poniżej przedstawiono możliwe lokalizacje przechowywania:

    * W przypadku regularnych klastrów:

      * ADLS Gen1: Usługa, której usługa HDInsight używa do uzyskiwania dostępu do magazynu usługi Data Lake, musi mieć dostęp do odczytu do skryptu. Format identyfikatora URI dla skryptów przechowywanych `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`w pamięci masowej danych Data Lake Gen1 to .

      * Obiekt blob na koncie usługi Azure Storage, które jest podstawowym lub dodatkowym kontem magazynu dla klastra HDInsight. Usługa HDInsight ma dostęp do obu tych typów kont magazynu podczas tworzenia klastra.

        > [!IMPORTANT]  
        > Nie należy obracać klucz magazynu na tym koncie usługi Azure Storage, ponieważ spowoduje to, że kolejne akcje skryptu ze skryptami przechowywanymi tam zakończy się niepowodzeniem.

      * Publiczna usługa udostępniania plików dostępna za pośrednictwem ścieżek http://. Przykładami są azure blob, GitHub, OneDrive. Na przykład identyfikatory URI, zobacz [Przykładowe skrypty akcji skryptów](#example-script-action-scripts).

     * W przypadku klastrów z esp, wasb:// lub wasbs:// lub http[s]:// identyfikatory URI są obsługiwane.

* Można ograniczyć do uruchamiania tylko na niektórych typach węzłów. Przykładami są węzły głównego lub węzły procesu roboczego.

* Może być utrzymywany lub ad hoc.

    Akcje skryptu utrwalone muszą mieć unikatową nazwę. Utrwalone skrypty są używane do dostosowywania nowych węzłów procesu roboczego dodanych do klastra za pomocą operacji skalowania. Utrwalony skrypt może również zastosować zmiany do innego typu węzła podczas skalowania operacji. Przykładem jest węzeł główny.

    Skrypty ad hoc nie są utrwalone. Akcje skryptów używane podczas tworzenia klastra są automatycznie utrwalane. Nie są one stosowane do węzłów procesu roboczego dodanych do klastra po uruchomieniu skryptu. Następnie można podwyższyć poziomu skryptu ad hoc do utrwalonych skryptów lub obniżyć utrwalony skrypt do skryptu ad hoc. Skrypty, które nie powiodą się, nie są zachowywane, nawet jeśli wyraźnie wskazujesz, że powinny być.

* Można zaakceptować parametry, które są używane przez skrypt podczas wykonywania.

* Uruchom z uprawnieniami na poziomie głównym w węzłach klastra.

* Może być używany za pośrednictwem witryny Azure portal, Azure PowerShell, Interfejsu wiersza polecenia platformy Azure lub interfejsu SDK .NET.

Klaster przechowuje historię wszystkich skryptów, które zostały uruchomione. Historia pomaga, gdy trzeba znaleźć identyfikator skryptu dla operacji promocji lub degradacji.

> [!IMPORTANT]  
> Nie ma automatycznego sposobu, aby cofnąć zmiany wprowadzone przez akcję skryptu. Ręcznie odwrócić zmiany lub podać skrypt, który odwraca je.

### <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w procesie tworzenia klastra

Akcje skryptu używane podczas tworzenia klastra różnią się nieznacznie od akcji skryptów uruchamianych w istniejącym klastrze:

* Skrypt jest automatycznie utrwalony.

* Błąd w skrypcie może spowodować niepowodzenie procesu tworzenia klastra.

Na poniższym diagramie przedstawiono, kiedy akcja skryptu jest uruchamiana podczas procesu tworzenia:

![Dostosowywanie klastra HDInsight i etapy podczas tworzenia klastra][img-hdi-cluster-states]

Skrypt jest uruchamiany podczas konfigurowania programu HDInsight. Skrypt działa równolegle na wszystkich określonych węzłach w klastrze. Działa z uprawnieniami głównymi w węzłach.

Można wykonywać operacje, takie jak zatrzymywanie i uruchamianie usług, w tym usługi związane z Apache Hadoop. Jeśli zatrzymasz usługi, upewnij się, że usługa Ambari i inne usługi związane z usługami Hadoop są uruchomione przed zakończeniem skryptu. Te usługi są wymagane do pomyślnego określenia kondycji i stanu klastra podczas jego tworzenia.

Podczas tworzenia klastra można używać wielu akcji skryptu jednocześnie. Skrypty te są wywoływane w kolejności, w jakiej zostały określone.

> [!IMPORTANT]  
> Akcje skryptu muszą zakończyć się w ciągu 60 minut lub limit czasu. Podczas inicjowania obsługi administracyjnej klastra skrypt jest uruchamiany jednocześnie z innymi procesami konfiguracji i konfiguracji. Konkurencja o zasoby, takie jak czas procesora LUB przepustowość sieci, może spowodować, że skrypt zakończy się dłużej niż w środowisku programistycznym.
>
> Aby zminimalizować czas potrzebny do uruchomienia skryptu, należy unikać zadań, takich jak pobieranie i kompilowanie aplikacji ze źródła. Wstępnie kompiluj aplikacje i przechowuj plik binarny w usłudze Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Akcja skryptu w uruchomionym klastrze

Błąd w skrypcie uruchomionym w już uruchomionym klastrze nie powoduje automatycznie zmiany klastra na stan awarii. Po zakończeniu skryptu klaster powinien powrócić do stanu uruchomionego. Nawet jeśli klaster ma stan uruchomiony, skrypt nie powiodło się może mieć uszkodzone rzeczy. Na przykład skrypt może usunąć pliki potrzebne przez klaster.

Akcje skryptów są uruchamiane z uprawnieniami głównymi. Upewnij się, że rozumiesz, co skrypt robi przed zastosowaniem go do klastra.

Po zastosowaniu skryptu do klastra stan klastra zmienia się z **Uruchomiony** na **Zaakceptowany**. Następnie zmienia się w **konfigurację HDInsight** i, wreszcie, z powrotem do **uruchamiania** dla udanych skryptów. Stan skryptu jest rejestrowany w historii akcji skryptu. Te informacje informuje, czy skrypt zakończył się powodzeniem lub niepowodzeniem. Na przykład `Get-AzHDInsightScriptActionHistory` polecenie cmdlet programu PowerShell pokazuje stan skryptu. Zwraca informacje podobne do następującego tekstu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Jeśli zmienisz użytkownika klastra, administratora, hasło po utworzeniu klastra, akcje skryptu uruchamiane dla tego klastra mogą zakończyć się niepowodzeniem. Jeśli masz żadnych utrwalonych akcji skryptu, które docelowe węzłów procesu roboczego, skrypty te mogą zakończyć się niepowodzeniem podczas skalowania klastra.

## <a name="example-script-action-scripts"></a>Przykładowe skrypty akcji

Skryptów akcji skryptów można używać za pośrednictwem następujących narzędzi:

* Portal Azure
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure
* HDInsight .NET SDK

Usługa HDInsight udostępnia skrypty do instalowania następujących składników w klastrach usługi HDInsight:

| Nazwa | Skrypt |
| --- | --- |
| Dodawanie konta usługi Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](hdinsight-hadoop-add-storage.md). |
| Zainstaluj barwę |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zobacz [Instalowanie i używanie funkcji Hue w klastrach programu HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Wstępne ładowanie bibliotek gałęzi |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zobacz [Dodawanie niestandardowych bibliotek gałęzi Apache podczas tworzenia klastra USŁUGI HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Akcja skryptu podczas tworzenia klastra

W tej sekcji opisano różne sposoby używania akcji skryptów podczas tworzenia klastra HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Używanie akcji skryptu podczas tworzenia klastra z witryny Azure Portal

1. Rozpocznij tworzenie klastra zgodnie z opisem w [obszarze Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu portalu Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Na karcie **Konfiguracja + cennik** wybierz pozycję **+ Dodaj akcję skryptu**.

    ![Akcja skryptu klastra portalu Azure](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Użyj __wpisu Wybierz skrypt,__ aby wybrać wstępnie wykonany skrypt. Aby użyć skryptu niestandardowego, wybierz opcję __Niestandardowy__. Następnie podaj identyfikator __URI skryptu__ __Nazwa__ i Bash dla skryptu.

    ![Dodawanie skryptu w formularzu wyboru skryptu](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    W poniższej tabeli opisano elementy formularza:

    | Właściwość | Wartość |
    | --- | --- |
    | Wybieranie skryptu | Aby użyć własnego skryptu, wybierz opcję __Niestandardowy__. W przeciwnym razie wybierz jeden z podanych skryptów. |
    | Nazwa |Określ nazwę akcji skryptu. |
    | Identyfikator URI skryptu bash |Określ identyfikator URI skryptu. |
    | Głowa/Pracownik/ZooKeeper |Określ węzły, w których jest uruchamiany skrypt: **Głowa,** **Pracownik**lub **ZooKeeper**. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __utrwalania tego wpisu akcji skryptu,__ aby upewnić się, że skrypt jest stosowany podczas operacji skalowania.

1. Wybierz __pozycję Utwórz,__ aby zapisać skrypt. Następnie możesz użyć __+ Prześlij nowy,__ aby dodać kolejny skrypt.

    ![HDInsight wiele akcji skryptów](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Po zakończeniu dodawania skryptów powracasz do karty **Konfiguracja + cennik.**

1. Wykonaj pozostałe kroki tworzenia klastra w zwykły sposób.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Używanie akcji skryptu z szablonów usługi Azure Resource Manager

Akcje skryptu mogą być używane z szablonami usługi Azure Resource Manager. Na przykład zobacz [Tworzenie klastra systemu Linux HDInsight i uruchom akcję skryptu](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

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

* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Używanie akcji skryptu podczas tworzenia klastra z programu Azure PowerShell

W tej sekcji należy użyć polecenia cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) do wywoływania skryptów w celu dostosowania klastra. Przed rozpoczęciem upewnij się, że zainstalowano i skonfigurować program Azure PowerShell. Aby użyć tych poleceń programu PowerShell, potrzebny jest [moduł AZ](https://docs.microsoft.com/powershell/azure/overview).

Poniższy skrypt pokazuje, jak zastosować akcję skryptu podczas tworzenia klastra przy użyciu programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Może upłynąć kilka minut, zanim zostanie utworzony klaster.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Użyj akcji skryptu podczas tworzenia klastra z zestawu HDInsight .NET SDK

Interfejs SDK .NET usługi HDInsight udostępnia biblioteki klienckie, które ułatwiają pracę z programem HDInsight z aplikacji .NET. Aby zapoznać się z przykładem kodu, zobacz [Akcje skryptów](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Akcja skryptu do uruchomionego klastra

W tej sekcji wyjaśniono, jak zastosować akcje skryptu do uruchomionego klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Stosowanie akcji skryptu do uruchomionego klastra z witryny Azure portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i znajdź swój klaster.

1. W widoku domyślnym w obszarze **Ustawienia**wybierz pozycję **Akcje skryptu**.

1. U góry strony **Akcje skryptu** wybierz pozycję **+ Prześlij nowy**.

    ![Dodawanie skryptu do uruchomionego klastra](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Użyj __wpisu Wybierz skrypt,__ aby wybrać wstępnie wykonany skrypt. Aby użyć skryptu niestandardowego, wybierz opcję __Niestandardowy__. Następnie podaj identyfikator __URI skryptu__ __Nazwa__ i Bash dla skryptu.

    ![Dodawanie skryptu w formularzu wyboru skryptu](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    W poniższej tabeli opisano elementy formularza:

    | Właściwość | Wartość |
    | --- | --- |
    | Wybieranie skryptu | Aby użyć własnego skryptu, wybierz __niestandardowy__plik . W przeciwnym razie wybierz dostarczony skrypt. |
    | Nazwa |Określ nazwę akcji skryptu. |
    | Identyfikator URI skryptu bash |Określ identyfikator URI skryptu. |
    | Głowa/Pracownik/Zookeeper |Określ węzły, w których jest uruchamiany skrypt: **Głowa,** **Pracownik**lub **ZooKeeper**. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __utrwalania tego wpisu akcji skryptu,__ aby upewnić się, że skrypt jest stosowany podczas operacji skalowania.

1. Na koniec wybierz przycisk **Utwórz,** aby zastosować skrypt do klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Stosowanie akcji skryptu do uruchomionego klastra z programu Azure PowerShell

Aby użyć tych poleceń programu PowerShell, potrzebny jest [moduł AZ](https://docs.microsoft.com/powershell/azure/overview). W poniższym przykładzie pokazano, jak zastosować akcję skryptu do uruchomionego klastra:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po zakończeniu operacji otrzymasz informacje podobne do następującego tekstu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Stosowanie akcji skryptu do uruchomionego klastra z interfejsu wiersza polecenia platformy Azure

Przed rozpoczęciem upewnij się, że instalujesz i konfigurujesz interfejsu wiersza polecenia platformy Azure. Upewnij się, że masz najnowszą wersję. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Uwierzytelnij się w subskrypcji platformy Azure:

    ```azurecli
    az login
    ```

1. Stosowanie akcji skryptu do uruchomionego klastra:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Prawidłowe `headnode`role `workernode` `zookeepernode`to `edgenode`, , , . Jeśli skrypt powinien być stosowany do kilku typów węzłów, oddziel role spacją. Na przykład `--roles headnode workernode`.

    Aby utrwalić `--persist-on-success`skrypt, dodaj . Skrypt można również utrwalić później za pomocą programu `az hdinsight script-action promote`.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Stosowanie akcji skryptu do uruchomionego klastra przy użyciu interfejsu REST API

Zobacz [interfejs API odpoczynku klastra w usłudze Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Stosowanie akcji skryptu do uruchomionego klastra z zestawu HDInsight .NET SDK

Na przykład za pomocą zestawu SDK .NET do stosowania skryptów do klastra zobacz [Stosowanie akcji skryptu względem działającego klastra HDInsight opartego na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Wyświetlanie historii oraz promowanie i obniżanie poziomu akcji skryptów

### <a name="the-azure-portal"></a>Witryna Azure Portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i znajdź swój klaster.

1. W widoku domyślnym w obszarze **Ustawienia**wybierz pozycję **Akcje skryptu**.

1. Historia skryptów dla tego klastra jest wyświetlana w sekcji akcje skryptu. Informacje te obejmują listę utrwalonych skryptów. Poniższy zrzut ekranu pokazuje, że skrypt Solr został uruchomiony w tym klastrze. Zrzut ekranu nie pokazuje żadnych utrwalonych skryptów.

    ![Historia przesyłania skryptów portalu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Wybierz skrypt z historii, aby wyświetlić sekcję **Właściwości** dla tego skryptu. W górnej części ekranu można ponownie uruchomić skrypt lub promować go.

    ![Właściwości akcji skryptu promują](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Można również wybrać wielokropek, **...**, po prawej stronie wpisów w sekcji akcje skryptu do wykonywania akcji.

    ![Utrwalone akcje skryptu są usuwane](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funkcja |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Pobieranie informacji o akcjach skryptu utrwalone. To polecenie cmdlet nie cofa akcji wykonywanych przez skrypt, usuwa tylko utrwaloną flagę.|
| `Get-AzHDInsightScriptActionHistory` |Pobierz historię akcji skryptu zastosowanych do klastra lub szczegóły dla określonego skryptu. |
| `Set-AzHDInsightPersistedScriptAction` |Promuj akcję skryptu ad hoc do akcji skryptu utrwalone. |
| `Remove-AzHDInsightPersistedScriptAction` |Zdegraduj akcję utrwalonych skryptów do akcji ad hoc. |

Poniższy przykładowy skrypt pokazuje, za pomocą poleceń cmdlet do promowania, a następnie obniżyć skrypt.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

| Polecenie | Opis |
| --- | --- |
| [az hdinsight skrypt akcji usunąć](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Usuwa określoną akcję skryptu utrwalone klastra. To polecenie nie cofa akcji wykonywanych przez skrypt, usuwa tylko utrwaloną flagę.|
|[az hdinsight skrypt-akcja wykonać](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Wykonywanie akcji skryptu w określonym klastrze HDInsight.|
| [az hdinsight script-action list az hdinsight script-action list az hdinsight script-action list az](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Wyświetla listę wszystkich akcji skryptu utrwalone dla określonego klastra. |
|[az hdinsight script-action list-execution-history](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Wyświetla listę historii wykonywania wszystkich skryptów dla określonego klastra.|
|[az hdinsight skrypt-działania promować](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Promuje wykonanie określonego skryptu ad hoc do utrwalonych skryptów.|
|[az hdinsight skrypt-akcja show-wykonanie-szczegóły](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Pobiera szczegóły wykonywania skryptu dla danego identyfikatora wykonania skryptu.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Na przykład za pomocą zestawu SDK platformy .NET do pobierania historii skryptów z klastra, podwyższania lub obniżania poziomu skryptów, zobacz [Stosowanie akcji skryptu względem działającego klastra HDInsight opartego na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> W tym przykładzie pokazano również, jak zainstalować aplikację HDInsight przy użyciu pakietu SDK .NET.

## <a name="support-for-open-source-software"></a>Obsługa oprogramowania typu open source

Usługa Microsoft Azure HDInsight korzysta z ekosystemu technologii open source utworzonych wokół Apache Hadoop. Platforma Microsoft Azure zapewnia ogólny poziom obsługi technologii typu open source. Aby uzyskać więcej informacji, zobacz sekcję **Zakres pomocy technicznej** w często [zadawanych pytaniach dotyczących pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom obsługi wbudowanych składników.

W usłudze HDInsight dostępne są dwa typy składników typu open source:

* **Wbudowane komponenty**. Te składniki są preinstalowane w klastrach HDInsight i zapewniają podstawowe funkcje klastra. Do tej kategorii należą następujące składniki:

  * [Apache Hadoop Przędza](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resourcemanager.
  * Język zapytania [hiveQl](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/).

    Pełna lista składników klastra jest dostępna w [co są apache Hadoop składników i wersji dostępnych z HDInsight?](hdinsight-component-versioning.md)

* **Składniki niestandardowe**. Jako użytkownik klastra można zainstalować lub użyć w obciążeniu dowolnego składnika dostępnego w społeczności lub utworzonego przez użytkownika.

> [!WARNING]  
> Komponenty dostarczane z klastrem HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, aby pomóc w dalszym rozwiązywaniu problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać ten problem. Mogą też poprosić cię o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się głęboka wiedza specjalistyczna w zakresie tej technologii. Można korzystać z wielu witryn społecznościowych. Przykładami są [forum MSDN dla HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) i [Stack Overflow](https://stackoverflow.com).
>
> Projekty Apache mają również witryny projektu na [stronie internetowej Apache](https://apache.org). Przykładem jest [Hadoop](https://hadoop.apache.org/).

Usługa HDInsight oferuje kilka sposobów używania składników niestandardowych. Ten sam poziom pomocy technicznej ma zastosowanie, niezależnie od tego, jak składnik jest używany lub zainstalowany w klastrze. Na poniższej liście opisano najczęstsze sposoby używania składników niestandardowych w klastrach usługi HDInsight:

1. **Składanie ofert pracy**. Hadoop lub inne typy zadań, które wykonują lub używają składników niestandardowych mogą być przesyłane do klastra.

2. **Dostosowywanie klastra**. Podczas tworzenia klastra można określić dodatkowe ustawienia i składniki niestandardowe, które są instalowane w węzłach klastra.

3. **Przykłady**. W przypadku popularnych składników niestandardowych firma Microsoft i inne firmy mogą dostarczyć przykłady sposobu, w jaki te składniki mogą być używane w klastrach HDInsight. Te próbki są dostarczane bez pomocy technicznej.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Za pomocą interfejsu użytkownika sieci Web Ambari można wyświetlać informacje rejestrowane przez akcje skryptów. Jeśli skrypt nie powiedzie się podczas tworzenia klastra, dzienniki są również dostępne na domyślnym koncie magazynu skojarzonym z klastrem. Ta sekcja zawiera informacje na temat sposobu pobierania dzienników przy użyciu obu tych opcji.

### <a name="the-apache-ambari-web-ui"></a>Interfejs użytkownika sieci Apache Ambari

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Na pasku w górnej części strony wybierz pozycję **ops.** Lista wyświetla bieżące i poprzednie operacje wykonywane w klastrze za pośrednictwem ambari.

    ![Pasek interfejsu użytkownika w sieci Ambari z wybraną pozy](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. Znajdź wpisy, które **zostały uruchomione\_w** kolumnie **Operacje.** Te wpisy są tworzone po uruchomieniu akcji skryptu.

    ![Operacje akcji skryptu Apache Ambari](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Aby wyświetlić dane wyjściowe **STDOUT** i **STDERR,** wybierz wpis **run\customscriptaction** i przejdź do szczegółów łącza. To dane wyjściowe są generowane, gdy skrypt jest uruchamiany i może mieć przydatne informacje.

### <a name="access-logs-from-the-default-storage-account"></a>Dostęp do dzienników z domyślnego konta magazynu

Jeśli utworzenie klastra nie powiedzie się z powodu błędu skryptu, dzienniki są przechowywane na koncie magazynu klastra.

* Dzienniki pamięci masowej `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`są dostępne pod adresem .

    ![Dzienniki akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    W tym katalogu dzienniki są zorganizowane oddzielnie dla **węzła headnode,** **węzła roboczego**i **węzła zookeeper**. Zobacz poniższe przykłady:

    * **Główka**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Węzeł pracownika**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Węzeł zookeeper**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Wszystkie **stdout** i **stderr** odpowiedniego hosta są przesyłane na konto magazynu. Istnieje jeden **wynik\*wyjściowy- .txt** i **błędy-\*.txt** dla każdej akcji skryptu. Plik **output*.txt** zawiera informacje o identyfikatorze URI skryptu, który został uruchomiony na hoście. Przykładem tych informacji jest następujący tekst:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Możliwe, że wielokrotnie tworzysz klaster akcji skryptu o tej samej nazwie. W takim przypadku można odróżnić odpowiednie dzienniki na podstawie nazwy folderu **DATE.** Na przykład struktura folderów dla **klastra, mycluster**, utworzony w różnych dniach, jest podobna do następujących wpisów dziennika:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Jeśli tego samego dnia utworzysz klaster akcji skryptu o tej samej nazwie, możesz użyć unikatowego prefiksu do zidentyfikowania odpowiednich plików dziennika.

* Jeśli utworzysz klaster w pobliżu 12:00, północy, możliwe, że pliki dziennika obejmują dwa dni. W takim przypadku zobaczysz dwa różne foldery daty dla tego samego klastra.

* Przekazywanie plików dziennika do kontenera domyślnego może potrwać do pięciu minut, szczególnie w przypadku dużych klastrów. Więc jeśli chcesz uzyskać dostęp do dzienników, nie należy natychmiast usunąć klastra, jeśli akcja skryptu nie powiedzie się.

### <a name="ambari-watchdog"></a>Ambari watchdog

> [!WARNING]  
> Nie zmieniaj hasła do strażnika Ambari, hdinsightwatchdog, w klastrze HDInsight opartym na systemie Linux. Zmiana hasła dla tego konta przerywa możliwość uruchamiania nowych akcji skryptu w klastrze HDInsight.

### <a name="cant-import-name-blobservice"></a>Nie można zaimportować nazwy BlobService

__Objawy__. Akcja skryptu kończy się niepowodzeniem. Tekst podobny do następującego błędu jest wyświetlany podczas wyświetlania operacji w Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Przyczyna__. Ten błąd występuje w przypadku uaktualnienia klienta usługi Azure Storage języka Python dołączonego do klastra HDInsight. Usługa HDInsight oczekuje klienta usługi Azure Storage 0.20.0.

__Rozdzielczość__. Aby rozwiązać ten problem, należy ręcznie połączyć się z każdym węzłem klastra za pomocą programu `ssh`. Uruchom następujące polecenie, aby ponownie zainstalować poprawną wersję klienta magazynu:

```bash
sudo pip install azure-storage==0.20.0
```

Aby uzyskać informacje na temat łączenia się z klastrem za pomocą funkcji SSH, zobacz Łączenie się z [programem HDInsight (Apache Hadoop) przy użyciu funkcji SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historia nie pokazuje skryptów używanych podczas tworzenia klastra

Jeśli klaster został utworzony przed 15 marca 2016 r., może nie być widoczny wpis w historii akcji skryptu. Zmiana rozmiaru klastra powoduje, że skrypty pojawiają się w historii akcji skryptu.

Istnieją dwa wyjątki:

* Klaster został utworzony przed 1 września 2015 r. Ta data jest, gdy akcje skryptu zostały wprowadzone. Żaden klaster utworzony przed tą datą nie mógł używać akcji skryptu do tworzenia klastra.

* Podczas tworzenia klastra użyto wielu akcji skryptu. Lub użyto tej samej nazwy dla wielu skryptów lub tej samej nazwy, tego samego identyfikatora URI, ale różne parametry dla wielu skryptów. W takich przypadkach pojawia się następujący błąd:

    Żadne nowe akcje skryptu nie mogą być uruchamiane w tym klastrze z powodu sprzecznych nazw skryptów w istniejących skryptach. Nazwy skryptów podane podczas tworzenia klastra muszą być unikatowe. Istniejące skrypty są uruchamiane po zmienić rozmiar.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie skryptów akcji skryptów dla hdinsight](hdinsight-hadoop-script-actions-linux.md)
* [Dodawanie dodatkowego magazynu do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Etapy podczas tworzenia klastra"

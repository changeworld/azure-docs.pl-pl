---
title: Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu - Azure | Dokumentacja firmy Microsoft
description: Dodawanie niestandardowych składników do opartych na systemie Linux klastrów usługi HDInsight za pomocą akcji skryptu. Akcje skryptu to skrypty Bash, które mogą służyć do dostosowywania konfiguracji klastra lub dodawania dodatkowych usług i narzędzi, takich jak Hue, Solr lub R.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: ff8b8ffc66ea4c90ca2f11edc36aac670f24eac2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>Dostosowywanie klastrów usługi HDInsight opartej na systemie Linux za pomocą akcji skryptu

HDInsight zapewnia opcję konfiguracji o nazwie **akcji skryptu** który wywołuje niestandardowe skrypty umożliwiające dostosowanie klastra. Skrypty te służą do instalowania dodatkowych składników i zmienić ustawienia konfiguracji. Akcje skryptu można podczas lub po utworzeniu klastra.

> [!IMPORTANT]
> Możliwość używania akcji skryptu w klastrze już uruchomiona jest dostępna tylko w przypadku klastrów usługi HDInsight opartej na systemie Linux.
>
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Akcje skryptu mogą być publikowane w portalu Azure Marketplace jako aplikacji usługi HDInsight. Przykłady w tym dokumencie pokazują, jak zainstalować aplikację usługi HDInsight przy użyciu polecenia akcji skryptu środowiska PowerShell i zestawu .NET SDK. Aby uzyskać więcej informacji na temat aplikacji w usłudze HDInsight, zobacz [publikować aplikacje usługi HDInsight w portalu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Uprawnienia

Jeśli korzystasz z klastra usługi HDInsight przyłączonych do domeny, istnieją dwa uprawnienia Ambari, które są wymagane w przypadku klastra za pomocą akcji skryptu:

* **NARZĘDZIA AMBARI. Uruchom\_niestandardowy\_polecenia**: rola administratora Ambari domyślnie ma to uprawnienie.
* **KLASTER. Uruchom\_niestandardowy\_polecenia**: zarówno administratora klastra usługi HDInsight i administratora Ambari to uprawnienie mają domyślnie.

Aby uzyskać więcej informacji na temat pracy z uprawnieniami przyłączonych do domeny usługi HDInsight, zobacz [zarządzania klastrami HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Kontrola dostępu

Jeśli nie jesteś administratorem/właścicieli subskrypcji platformy Azure, jego konto musi mieć co najmniej **współautora** dostęp do grupy zasobów zawierającej klaster usługi HDInsight.

Ponadto podczas tworzenia klastra usługi HDInsight, ktoś z co najmniej **współautora** dostęp do subskrypcji platformy Azure musi zostały wcześniej zarejestrowane dostawcy dla usługi HDInsight. Rejestracja zasobu ma miejsce, gdy użytkownik o roli Współautor z dostępem do subskrypcji tworzy zasób po raz pierwszy w subskrypcji. Można to osiągnąć również bez tworzenia zasobu, [rejestrując dostawcę za pomocą interfejsu REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Więcej informacji o zarządzaniu dostępem można znaleźć w następujących dokumentach:

* [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
* [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)

## <a name="understanding-script-actions"></a>Opis akcji skryptu

Akcja skryptu to skrypty Bash uruchamiane w węzłach klastra usługi HDInsight. Poniżej przedstawiono cechy i funkcje akcji skryptu.

* Muszą być przechowywane na identyfikator URI, który jest dostępny z klastra usługi HDInsight. Lokalizacje magazynu możliwe są następujące:

    * **Azure Data Lake Store** konta, który jest dostępny dla klastra usługi HDInsight. Aby uzyskać informacje na temat używania usługi Azure Data Lake Store z usługą HDInsight, zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Podczas używania skryptu przechowywane w usłudze Data Lake Store, format identyfikatora URI jest `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Nazwy głównej usługi, które HDInsight używa do uzyskiwania dostępu usługi Data Lake Store musi mieć dostęp do odczytu do skryptu.

    * Obiekt blob w **konta magazynu Azure** czyli albo podstawowej lub dodatkowej konta magazynu dla klastra usługi HDInsight. HDInsight uzyskuje dostęp do obu typów kont magazynu podczas tworzenia klastra.

    * Plik publicznego udostępniania usługi takie jak obiektów Blob platformy Azure, GitHub, usługi OneDrive, Dropbox itp.

        Na przykład identyfikatory URI, zobacz [przykładowe skrypty akcji skryptu](#example-script-action-scripts) sekcji.

        > [!WARNING]
        > HDInsight obsługuje tylko __ogólnego przeznaczenia__ konta usługi Azure Storage. Aktualnie nie obsługuje __magazynu obiektów Blob__ typ konta.

* Można ograniczyć do **uruchomienia dla niektórych typów węzła**, na przykład węzłów głównych lub węzłów procesu roboczego.

* Może być **utrwalone** lub **ad hoc**.

    **Utrwalone** skrypty są używane do dostosowywania nowych węzłów procesu roboczego dodane do klastra za pomocą operacji skalowania. Utrwalony skrypt może również zastosować zmiany do innego typu węzła, na przykład węzła głównego, po operacji skalowania.

  > [!IMPORTANT]
  > Akcji utrwalonego skryptu musi mieć unikatową nazwę.

    **Ad hoc** skryptów nie są zachowywane. Nie są stosowane do węzłów procesu roboczego dodane do klastra, po ma uruchomiono skrypt. Można następnie podwyższania poziomu skryptów ad hoc do utrwalonego skryptu lub obniżyć poziom utrwalony skrypt do skryptu ad hoc.

  > [!IMPORTANT]
  > Akcje skryptu używane podczas tworzenia klastra automatycznie są zachowywane.
  >
  > Skrypty, które nie są niepowodzenie utrwalone, nawet jeśli w szczególności wskazują one powinna być.

* Można zaakceptować **parametry** używane przez skrypt podczas wykonywania.

* Uruchom z **głównego poziomu uprawnień** w węzłach klastra.

* Mogą być używane za pośrednictwem **portalu Azure**, **programu Azure PowerShell**, **wiersza polecenia platformy Azure w wersji 1.0**, lub **zestawu .NET SDK usługi HDInsight**

Klaster zawiera historię wszystkie skrypty, które zostały uruchomione. Historia jest przydatne, gdy trzeba znaleźć Identyfikatora skryptu dla operacji podwyższenia poziomu lub obniżania poziomu.

> [!IMPORTANT]
> Nie istnieje sposób automatycznej cofnąć zmiany wprowadzone przez akcji skryptu. Ręcznie cofnąć te zmiany lub Podaj skrypt, który odwraca je.

### <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w ramach procesu tworzenia klastra

Akcje skryptu używane podczas tworzenia klastra są nieco inne niż skryptu, który akcje został uruchomiony w istniejącym klastrze:

* Skrypt jest **automatycznie utrwalonego**.

* A **błąd** w skrypcie może spowodować niepowodzenie procesu tworzenia klastra.

Na poniższym diagramie przedstawiono podczas wykonywania akcji skryptu w trakcie procesu tworzenia:

![Dostosowywanie klastrów usługi HDInsight i etapy podczas tworzenia klastra][img-hdi-cluster-states]

Skrypt jest uruchamiany, gdy skonfigurowano usługi HDInsight. Skrypt uruchamia się równolegle na określonych węzłów w klastrze i uruchamiane z uprawnieniami głównego w węzłach.

> [!NOTE]
> Można wykonywać operacje, takie jak zatrzymanie i uruchomienie usług, w tym usług związanych z usługi Hadoop. Zatrzymanie usługi, należy się upewnić, zakończeniu usługi Ambari i innych usług związanych z Hadoop przed skryptu. Te usługi są wymagane do określenia pomyślnie kondycji i stan klastra podczas jego tworzenia.


Podczas tworzenia klastra można użyć jednocześnie wiele akcji skryptu. Skrypty te są wywoływane w kolejności, w jakiej zostały określone.

> [!IMPORTANT]
> Akcje skryptu należy wykonać w ciągu 60 minut lub limitu czasu. Podczas inicjowania obsługi klastra, skrypt zostanie uruchomiony równocześnie z innymi procesami instalacji i konfiguracji. Konkurowanie o zasoby, takie jak czas lub sieci przepustowości Procesora może powodować skrypt potrwać dłużej niż w środowisku projektowania.
>
> Aby zminimalizować czas wymagany do uruchomienia skryptu, należy unikać zadań, takich jak pobieranie i kompilowanie aplikacji ze źródła. Kompilacja wstępna aplikacji i przechowywać dane binarne w usłudze Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Akcja skryptu w klastrze uruchomione

Błąd skryptu uruchomionego na uruchomiono klastra nie powoduje automatycznego klaster, aby zmienić w stan niepowodzenia. Po ukończeniu działania skryptu klastra powinien zwrócić do stanu "uruchomiona".

> [!IMPORTANT]
> Nawet jeśli klaster ma stan "uruchomiona", skryptu nie powiodło się mogą zawierać uszkodzone elementy. Na przykład skrypt można usunąć plików wymaganych dla klastra.
>
> Akcje skrypty uruchamiane z uprawnieniami głównego. Sprawdź dokładnie skryptu jest przed jej zastosowaniem do klastra.

Podczas stosowania skrypt do klastra, klaster stan zmieni się z **systemem** do **zaakceptowane**, następnie **konfiguracji HDInsight**, a na koniec z powrotem do  **Uruchomiona** skryptów powiodło się. Stan skryptu jest rejestrowana w historii akcji skryptu, i te informacje można użyć do określenia, czy skrypt zakończyło się powodzeniem, czy nie powiodło się. Na przykład `Get-AzureRmHDInsightScriptActionHistory` polecenia cmdlet programu PowerShell można wyświetlić stan skryptu. Zwraca informacje podobne do następującego tekstu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]
> Jeśli zmienisz hasło użytkownika (Administrator) klastra po utworzeniu klastra, który akcje przeprowadzony na ten klaster może zakończyć się niepowodzeniem. Jeśli masz żadnych akcji utrwalonego skryptu węzłów procesu roboczego tego docelowego te skrypty może zakończyć się niepowodzeniem podczas skalowania klastra.

## <a name="example-script-action-scripts"></a>Przykładowe skrypty akcji skryptu

Można używać skryptów akcji skryptu za pomocą następujących narzędzi:

* Azure Portal
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure w wersji 1.0
* Zestaw SDK dla platformy .NET usługi HDInsight

Usługa HDInsight zapewnia skryptów do zainstalowania następujących składników w klastrach HDInsight:

| Name (Nazwa) | Skrypt |
| --- | --- |
| **Dodaj konto usługi Azure Storage** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Zobacz [dodać dodatkowy magazyn do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md). |
| **Instalowanie aplikacji Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Zobacz [instalacji i używania aplikacji Hue w usłudze HDInsight clusters](hdinsight-hadoop-hue-linux.md). |
| **Zainstaluj Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Zobacz [instalacji i używania Presto w usłudze HDInsight clusters](hdinsight-hadoop-install-presto.md). |
| **Zainstaluj Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Zobacz [instalacji i używania Solr w usłudze HDInsight clusters](hdinsight-hadoop-solr-install-linux.md). |
| **Zainstaluj Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Zobacz [instalacji i używania Giraph w usłudze HDInsight clusters](hdinsight-hadoop-giraph-install-linux.md). |
| **Wstępne ładowanie bibliotek technologii Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Zobacz [dodać Hive bibliotek w klastrach HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| **Instalowanie i aktualizowanie środowiska Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Zobacz [instalacji lub aktualizacji Mono w usłudze HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Za pomocą akcji skryptu, podczas tworzenia klastra

Ta sekcja zawiera przykłady na różne sposoby, można użyć akcji skryptu, podczas tworzenia klastra usługi HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Za pomocą akcji skryptu, podczas tworzenia klastra w portalu Azure

1. Rozpocznij tworzenie klastra, zgodnie z opisem w [klastrów utworzyć Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Zatrzymać po osiągnięciu __klastra Podsumowanie__ sekcji.

2. Z __klastra Podsumowanie__ zaznacz __Edytuj__ łączy dla __Zaawansowane ustawienia__.

    ![Łącze Ustawienia zaawansowane](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Z __Zaawansowane ustawienia__ zaznacz __skryptu akcji__. Z __skryptu akcje__ zaznacz __+ nowy przesyłania__

    ![Przedstawia nowa akcja skryptu](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Użyj __wybierz skrypt__ wpis wstępnie przygotowanych skrypt. Aby użyć niestandardowego skryptu, wybierz opcję __niestandardowych__ , a następnie wprowadź __nazwa__ i __Bash skryptu URI__ skryptu.

    ![Dodawanie skryptu w formie wybierz skryptu](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy na formularzu:

    | Właściwość | Wartość |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz opcję __niestandardowy__. W przeciwnym razie wybierz jeden z dostarczonego skryptów. |
    | Name (Nazwa) |Określ nazwę akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Dozorcy HEAD/procesu roboczego |Określ węzły (**Head**, **procesu roboczego**, lub **dozorcy**), na którym skrypt jest uruchamiany. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __Utrwal tę akcję skryptu__ wpis, aby upewnić się, że skrypt jest stosowane podczas operacji skalowania.

5. Wybierz __Utwórz__ można zapisać skryptu. Następnie można użyć __+ przesyłania nowych__ można dodać inny skrypt.

    ![Wiele akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Po zakończeniu dodawania skryptów, użyj __wybierz__ przycisk, a następnie __dalej__ przycisk, aby powrócić do __klastra Podsumowanie__ sekcji.

3. Aby utworzyć klaster, wybierz __Utwórz__ z __klastra Podsumowanie__ zaznaczenia.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Użyć akcji skryptu z szablonów usługi Azure Resource Manager

Akcje skryptu mogą być używane z szablonów usługi Azure Resource Manager. Na przykład zobacz [ https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/ ](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

W tym przykładzie akcji skryptu został dodany, używając następującego kodu:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Aby uzyskać informacje na temat sposobu wdrażania szablonu można znaleźć w następujących dokumentach:

* [Wdrażanie zasobów przy użyciu szablonów i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Wdrażanie zasobów przy użyciu szablonów i wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Za pomocą akcji skryptu, podczas tworzenia klastra z programu Azure PowerShell

W tej sekcji użyjesz [AzureRmHDInsightScriptAction Dodaj](https://msdn.microsoft.com/library/mt603527.aspx) polecenia cmdlet do wywołania, skrypty, aby dostosować klastra. Przed kontynuowaniem upewnij się, zainstalowaniu i skonfigurowaniu programu Azure PowerShell. Aby uzyskać informacji na temat konfigurowania stacji roboczej do uruchamiania poleceń cmdlet programu PowerShell w usłudze HDInsight, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Poniższy skrypt pokazuje, jak zastosować akcję skryptu, podczas tworzenia klastra przy użyciu programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Może upłynąć kilka minut przed utworzeniem klastra.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Za pomocą akcji skryptu, podczas tworzenia klastra z zestawu SDK .NET usługi HDInsight

Zestaw .NET SDK usługi HDInsight zapewnia bibliotek klienckich, które ułatwia do pracy z usługą HDInsight z poziomu aplikacji .NET. Przykładowy kod [opartych na systemie Linux z tworzenia klastrów w usłudze HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Zastosowanie akcji skryptu do uruchomionej klastra

W tej sekcji informacje o sposobie dotyczą akcji skryptu uruchomionego klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Akcja skryptu dotyczą klastra uruchomione w portalu Azure

1. Z [portalu Azure](https://portal.azure.com), wybierz z klastrem usługi HDInsight.

2. Omówienie klastrów usługi HDInsight, zaznacz **akcji skryptu** kafelka.

    ![Kafelek akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Możesz też wybrać **wszystkie ustawienia** , a następnie wybierz **akcji skryptu** w sekcji Ustawienia.

3. W górnej części sekcji działania skryptu, wybierz **przesyłania nowych**.

    ![Skrypt do klastra uruchomione](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Użyj __wybierz skrypt__ wpis wstępnie przygotowanych skrypt. Aby użyć niestandardowego skryptu, wybierz opcję __niestandardowych__ , a następnie wprowadź __nazwa__ i __Bash skryptu URI__ skryptu.

    ![Dodawanie skryptu w formie wybierz skryptu](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy na formularzu:

    | Właściwość | Wartość |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz opcję __niestandardowych__. W przeciwnym razie wybierz udostępnionego skryptu. |
    | Name (Nazwa) |Określ nazwę akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Dozorcy HEAD/procesu roboczego |Określ węzły (**Head**, **procesu roboczego**, lub **dozorcy**), na którym skrypt jest uruchamiany. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __Utrwal tę akcję skryptu__ wpis, aby się upewnić, że skrypt jest stosowany podczas operacji skalowania.

5. Na koniec użyj **Utwórz** przycisk, aby zastosować skrypt do klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Zastosuj Akcja skryptu do klastra uruchomione z programu Azure PowerShell

Przed kontynuowaniem upewnij się, zainstalowaniu i skonfigurowaniu programu Azure PowerShell. Aby uzyskać informacji na temat konfigurowania stacji roboczej do uruchamiania poleceń cmdlet programu PowerShell w usłudze HDInsight, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

W poniższym przykładzie pokazano sposób stosowania akcji skryptu do uruchomionej klastra:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po zakończeniu operacji, pojawi się informacje podobne do następującego tekstu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Zastosuj akcji skryptu do klastra uruchomione z wiersza polecenia platformy Azure

Przed kontynuowaniem upewnij się, zostanie zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [zainstalować 1.0 interfejsu wiersza polecenia Azure](../cli-install-nodejs.md).

> [!IMPORTANT]
> HDInsight wymaga interfejsu wiersza polecenia platformy Azure w wersji 1.0. Obecnie Azure CLI 2.0 nie zawiera polecenia do pracy z usługą HDInsight.

1. Aby włączyć tryb usługi Azure Resource Manager, wpisz następujące polecenie w wierszu polecenia:

    ```bash
    azure config mode arm
    ```

2. Użyj następującego polecenia do uwierzytelniania do subskrypcji platformy Azure.

    ```bash
    azure login
    ```

3. Użyj następującego polecenia do zastosowania akcji skryptu do uruchomionej klastra

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    W przypadku pominięcia parametrów dla tego polecenia, zostanie wyświetlony monit o ich. Jeśli skrypt zostanie określona z `-u` akceptuje parametry, można określić za pomocą `-p` parametru.

    Nieprawidłowy węzeł typy `headnode`, `workernode`, i `zookeeper`. Jeśli skrypt powinien być zastosowany wiele typów węzła, określić typy oddzielone ";". Na przykład `-n headnode;workernode`.

    Aby zachować skryptu, Dodaj `--persistOnSuccess`. Można również utrwalić skrypt później za pomocą `azure hdinsight script-action persisted set`.

    Po zakończeniu zadania, pojawi się dane wyjściowe podobne do następującego tekstu:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Dotyczą akcji skryptu uruchomionego klastra przy użyciu interfejsu API REST

Zobacz [wykonanie akcji skryptu w klastrze uruchomione](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Zastosuj akcji skryptu do klastra uruchomione z zestawu .NET SDK usługi HDInsight

Na przykład zastosować skryptów do klastra przy użyciu zestawu .NET SDK, zobacz [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Wyświetlanie historii, wspierania i obniżyć poziom akcji skryptu

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Z [portalu Azure](https://portal.azure.com), wybierz z klastrem usługi HDInsight.

2. Omówienie klastrów usługi HDInsight, zaznacz **akcji skryptu** kafelka.

    ![Kafelek akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Możesz też wybrać **wszystkie ustawienia** , a następnie wybierz **akcji skryptu** w sekcji Ustawienia.

4. Historia skryptów dla tego klastra jest wyświetlana w sekcji działania skryptu. Informacje te obejmują listę utrwalonych skryptów. Na poniższym zrzucie ekranu widać, że Solr skryptu zostało uruchomione w tym klastrze. Zrzut ekranu nie są wyświetlane wszystkie skrypty utrwalonych.

    ![Sekcji działania skryptu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Wybranie skryptu z historii powoduje wyświetlenie tego skryptu można znaleźć w sekcji właściwości. W górnej części ekranu można ponownie uruchomić skrypt lub Podwyższ go.

    ![Właściwości działania skryptu](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Można również użyć **...**  po prawej stronie wpisy w sekcji działania skryptu do wykonania akcji.

    ![Skrypt akcje... użycia](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

| Użyj następującego polecenia... | Aby... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Pobieranie informacji na temat akcji utrwalonego skryptu |
| Get-AzureRmHDInsightScriptActionHistory |Pobieranie historii akcji skryptu zastosowane do klastra lub szczegóły dotyczące określonego skryptu |
| Set-AzureRmHDInsightPersistedScriptAction |Zamienia akcji skryptów ad hoc do akcji utrwalonego skryptu |
| Remove-AzureRmHDInsightPersistedScriptAction |Przenosi akcji utrwalonego skryptu do akcji ad hoc |

> [!IMPORTANT]
> Przy użyciu `Remove-AzureRmHDInsightPersistedScriptAction` nie cofa akcje wykonywane przez skrypt. To polecenie cmdlet usuwa tylko utrwalonego flagę.

Następujący skrypt pokazuje, za pomocą poleceń cmdlet do promowania, a następnie obniżenia poziomu skryptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

| Użyj następującego polecenia... | Aby... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Pobierz listę akcji utrwalonego skryptu |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Pobieranie informacji na temat akcji określonych utrwalonego skryptu |
| `azure hdinsight script-action history list <clustername>` |Pobieranie historii akcji skryptu stosowane do klastra |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Pobieranie informacji na temat działań określonego skryptu |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Zamienia akcji skryptów ad hoc do akcji utrwalonego skryptu |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Przenosi akcji utrwalonego skryptu do akcji ad hoc |

> [!IMPORTANT]
> Przy użyciu `azure hdinsight script-action persisted delete` nie cofa akcje wykonywane przez skrypt. To polecenie cmdlet usuwa tylko utrwalonego flagę.

### <a name="using-the-hdinsight-net-sdk"></a>Przy użyciu zestawu SDK .NET usługi HDInsight

Na przykład przy użyciu zestawu .NET SDK można pobrać historii skryptu z klastra, podwyższyć poziom lub obniżyć poziom skryptów, zobacz [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Również w tym przykładzie pokazano, jak instalowanie aplikacji usługi HDInsight przy użyciu zestawu .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Obsługa oprogramowania typu open source używane w klastrach HDInsight

Usługa Microsoft Azure HDInsight używa ekosystem technologii open source utworzona wokół Hadoop. Microsoft Azure udostępnia ogólnego poziomu wsparcia dla technologii open source. Aby uzyskać więcej informacji, zobacz **zakres obsługi** sekcji [witryny sieci Web pomocy technicznej platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom obsługi dla wbudowanych składników.

Istnieją dwa typy składników open source, które są dostępne w usłudze HDInsight:

* **Wbudowanych składników** — te składniki są wstępnie zainstalowane w klastrach HDInsight i podaj podstawowe funkcje klastra. Na przykład YARN ResourceManager, język zapytań Hive (HiveQL) i biblioteki Mahout należą do tej kategorii. Pełna lista składniki klastra jest dostępna w [nowości w wersjach klastra Hadoop dostarczanych z usługą HDInsight](hdinsight-component-versioning.md).
* **Niestandardowe składniki** -, jako użytkownik klastra, można zainstalować lub użyć w obciążenie żadnych składników dostępnych w społeczności lub utworzonych przez użytkownika.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane. Microsoft Support pomaga wyizolować i rozwiązać problemy związane z tych składników.
>
> Niestandardowe składniki otrzymywanie pomocy uzasadnione ekonomicznie ułatwiające aby dalej rozwiązywać ten problem. Pomocy technicznej firmy Microsoft może być możliwe do rozwiązania problemu lub ich może poprosić o Uwzględnij dostępnych kanałów dla technologiach typu open source wykryto głębokie doświadczenia z tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projekty Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/).

Usługa HDInsight zapewnia kilka sposobów użycia niestandardowych składników. Taki sam poziom obsługi ma zastosowanie, niezależnie od tego, jak składnik jest używany lub zainstalować w klastrze. Na poniższej liście opisano najbardziej typowe sposoby, że niestandardowe składniki mogą być używane w klastrach HDInsight:

1. Przesyłanie zadań — usługi Hadoop i innych typów zadań, które wykonać albo użyć niestandardowych składników może zostać przesłane do klastra.

2. Dostosowywanie klastra — podczas tworzenia klastra, można określić dodatkowe ustawienia i niestandardowych składników, które są zainstalowane na węzłach klastra.

3. Próbek - dla popularnych niestandardowych składników, firma Microsoft i inne osoby mogą powodować przykłady sposobu użycia tych składników na klastry usługi HDInsight. Te przykłady są udostępniane bez obsługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Interfejs użytkownika sieci web Ambari służy do wyświetlania informacji zarejestrowanych przez akcji skryptu. Jeśli skrypt zakończy się niepowodzeniem podczas tworzenia klastra, dzienniki są również dostępne w domyślnego konta magazynu skojarzone z klastrem. Ta sekcja zawiera informacje dotyczące pobierania dzienników za pomocą obu tych opcji.

### <a name="using-the-ambari-web-ui"></a>Przy użyciu interfejsu użytkownika sieci Web Ambari

1. W przeglądarce przejdź do https://CLUSTERNAME.azurehdinsight.net. Zamień na nazwę klastra usługi HDInsight CLUSTERNAME.

    Po wyświetleniu monitu wprowadź nazwę konta administratora (Administrator) i hasło dla klastra. Może być konieczne ponowne wprowadzenie poświadczeń administratora w formularzu sieci web.

2. Na pasku w górnej części strony, wybierz **ops** wpisu. Zostanie wyświetlona lista o bieżącym i poprzednich operacji w klastrze za pomocą narzędzia Ambari.

    ![Pasek interfejsu użytkownika sieci web Ambari z ops wybrane](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Znajdź pozycje, które mają **Uruchom\_customscriptaction** w **operacji** kolumny. Te wpisy są tworzone po uruchomieniu akcji skryptu.

    ![Zrzut ekranu przedstawiający operacji](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Aby wyświetlić dane wyjściowe STDOUT i STDERR, wybierz pozycję run\customscriptaction, a następnie Drąż w dół za pośrednictwem łącza. Te dane wyjściowe jest generowany po uruchomieniu skryptu i mogą zawierać przydatne informacje.

### <a name="access-logs-from-the-default-storage-account"></a>Dzienniki dostępu z domyślnego konta magazynu

W przypadku tworzenia klastra zakończy się niepowodzeniem z powodu błędu skryptu, dzienniki będą przechowywane na koncie magazynu klastra.

* Dzienniki magazynu są dostępne pod adresem `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Zrzut ekranu przedstawiający operacji](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    W tym katalogu dzienniki są zorganizowane oddzielnie headnode, workernode i węzły dozorcy. Przykłady to:

    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Węzła procesu roboczego** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Węzeł dozorcy** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Wszystkie stdout i stderr odpowiedniego hosta jest przekazywane do konta magazynu. Istnieje **dane wyjściowe -\*.txt** i **błędy -\*.txt** dla każdej akcji skryptu. Plik wyjściowy *.txt zawiera informacje o identyfikatorze URI skryptu, który został uruchomiony na hoście. Przykładem tych informacji jest następujący tekst:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Możliwe jest wielokrotnie utworzyć klaster akcji skryptu o takiej samej nazwie. W takim przypadku można odróżnić odpowiednich dzienniki na podstawie nazwy folderu daty. Na przykład struktury folderów dla klastra (mycluster) utworzone w różnych terminach jest podobny do następujące wpisy dziennika:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Po utworzeniu klastra akcji skryptu z taką samą nazwę tego samego dnia umożliwia zidentyfikowanie odpowiednich plików dziennika przez osoby unikatowy prefiks.

* Po utworzeniu klastra w pobliżu 00:00:00 (północ), jest to możliwe, że pliki dziennika obejmują przez dwa dni. W takich przypadkach można zobaczyć dwa foldery inną datę dla tego samego klastra.

* Przekazywanie plików dziennika na domyślny kontener może potrwać maksymalnie 5 minutach, zwłaszcza w przypadku dużych klastrów. Tak Jeśli chcesz uzyskać dostęp w dziennikach, nie należy natychmiast usuwać klastra przypadku niepowodzenia akcji skryptu.

### <a name="ambari-watchdog"></a>Ambari programu alarmowego

> [!WARNING]
> Nie należy zmieniać hasła strażnika Ambari (hdinsightwatchdog) w klastrze usługi HDInsight opartej na systemie Linux. Zmiana hasła dla tego konta dzieli możliwość uruchamiania nowych akcji skryptu w klastrze usługi HDInsight.

### <a name="cant-import-name-blobservice"></a>Nie można zaimportować nazwa BlobService

__Objawy__: działanie skryptu zakończy się niepowodzeniem. Tekst podobny do następującego błędu jest wyświetlany podczas przeglądania operacji w Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Przyczyna__: ten błąd występuje podczas uaktualniania klienta Python usługi Azure Storage, która jest zawarta w klastrze usługi HDInsight. HDInsight oczekuje klienta usługi Azure Storage 0.20.0.

__Rozdzielczość__: Aby rozwiązać ten problem, ręcznie połączyć każdy węzeł klastra używa `ssh` i użyj następującego polecenia, aby ponownie zainstalować wersję klienta poprawne magazynu:

```bash
sudo pip install azure-storage==0.20.0
```

Aby uzyskać informacje na łączenie się z klastrem przy użyciu protokołu SSH, zobacz [używanie SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Historia nie wyświetla skrypty używane podczas tworzenia klastra

Jeśli klaster został utworzony przed 15 marca 2016 r nie miało wpis w historii akcji skryptu. Zmiana rozmiaru klastra powoduje, że skrypty, które mają być widoczne w historii akcji skryptu.

Istnieją jednak dwa wyjątki:

* Jeśli klaster został utworzony przed 1 września 2015 r. Ta data jest w przypadku akcji skryptu zostały wprowadzone. Dowolnego klastra utworzone przed tą datą nie ma użyć akcji skryptu do tworzenia klastra.

* Jeśli używane wiele akcji skryptu podczas tworzenia klastra, a następnie używać tej samej nazwy dla wielu skryptów lub tej samej nazwy, tym samym identyfikatorze URI, ale różne parametry dla wielu skryptów. W takich przypadkach zostanie wyświetlony następujący błąd:

    Skryptu nowych akcji może być uruchomione w tym klastrze z powodu konfliktu nazw skryptu w istniejących skryptów. Nazwy skrypt podanych w klastrze utworzyć musi być unikatowe. Istniejące skrypty są uruchomione na zmiany rozmiaru.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Zainstalować i używać Solr w klastrach HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Zainstalować i używać Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Dodaj dodatkowy magazyn do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"

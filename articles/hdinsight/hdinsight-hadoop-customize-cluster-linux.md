---
title: Dostosowywanie klastrów HDInsight za pomocą akcji skryptu, Azure
description: Dodaj niestandardowe składniki w klastrach HDInsight opartych na systemie Linux za pomocą akcji skryptu. Akcje skryptu to skrypty powłoki Bash, które mogą służyć do dostosowywania konfiguracji klastra lub dodać dodatkowych usług i narzędzi, takich jak Hue, Solr lub języka R.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: jasonh
ms.openlocfilehash: 2e5cddc03b522471cf7eea1f1e45b15c6f73f81d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595702"
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>Dostosowywanie klastrów HDInsight opartych na systemie Linux za pomocą akcji skryptu

HDInsight udostępnia metodę konfiguracji o nazwie **akcji skryptu** wywołującej skryptów niestandardowych, aby dostosować klastra. Skrypty te służą do instalowania dodatkowych składników i zmianę ustawień konfiguracji. Akcje skryptu może służyć w trakcie lub po utworzeniu klastra.

> [!IMPORTANT]
> Możliwość korzystania z akcji skryptów w klastrze usługi już uruchomionego jest dostępna tylko w przypadku klastrów HDInsight opartych na systemie Linux.
>
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Akcje skryptu można także publikować w portalu Azure Marketplace jako aplikacji HDInsight. Aby uzyskać więcej informacji na temat aplikacji HDInsight, zobacz [aplikacji HDInsight publikowania w portalu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Uprawnienia

Jeśli używasz klastra HDInsight przyłączone do domeny, istnieją dwa uprawnienia Ambari, które są wymagane, gdy z klastrem za pomocą akcji skryptu:

* **AMBARI. Uruchom\_niestandardowe\_polecenia**: rola administratora Ambari domyślnie ma to uprawnienie.
* **KLASTER. Uruchom\_niestandardowe\_polecenia**: zarówno Administrator klastra HDInsight i administratora Ambari domyślnie mają to uprawnienie.

Aby uzyskać więcej informacji na temat pracy z uprawnień za pomocą HDInsight przyłączone do domeny, zobacz [Zarządzanie klastrami HDInsight przyłączone do domeny](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Kontrola dostępu

Jeśli nie jesteś administratorem/właścicielem subskrypcji platformy Azure, Twoje konto musi mieć co najmniej **Współautor** dostęp do grupy zasobów, która zawiera klaster HDInsight.

Ponadto podczas tworzenia klastra usługi HDInsight, inna osoba z co najmniej **Współautor** dostęp do subskrypcji platformy Azure musi zostać wcześniej zarejestrowany dostawca HDInsight. Rejestracja zasobu ma miejsce, gdy użytkownik o roli Współautor z dostępem do subskrypcji tworzy zasób po raz pierwszy w subskrypcji. Można to osiągnąć również bez tworzenia zasobu, [rejestrując dostawcę za pomocą interfejsu REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Więcej informacji o zarządzaniu dostępem można znaleźć w następujących dokumentach:

* [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
* [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)

## <a name="understanding-script-actions"></a>Opis akcji skryptu

Akcja skryptu jest skrypt powłoki Bash, działającego w węzłach w klastrze usługi HDInsight. Oto cechy i funkcje akcji skryptu.

* Muszą być przechowywane w identyfikatorze URI, który jest dostępny z klastra HDInsight. Lokalizacje przechowywania możliwe są następujące:

    * **Usługi Azure Data Lake Store** konta, który jest dostępny dla klastra HDInsight. Aby uzyskać informacji dotyczących korzystania z usługi Azure Data Lake Store z usługą HDInsight, zobacz [Szybki Start: konfigurowanie klastrów w HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

        Podczas używania skryptu z przechowywanych w Data Lake Store, format identyfikatora URI jest `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Nazwa główna usługi, których HDInsight używa do dostępu Data Lake Store musi mieć dostęp do odczytu do skryptu.

    * Obiekt blob w **konta usługi Azure Storage** czyli albo z podstawowej lub dodatkowej konta magazynu dla klastra HDInsight. HDInsight udzielany jest dostęp do obu tych rodzajów kont magazynu, podczas tworzenia klastra.

    * Plik publicznego do udostępniania usługi, takiej jak usługi Azure Blob, GitHub, usłudze OneDrive, Dropbox itp.

        Na przykład zobacz identyfikatorów URI [przykładowe skrypty akcji skryptu](#example-script-action-scripts) sekcji.

        > [!WARNING]
        > HDInsight obsługuje tylko __ogólnego przeznaczenia__ kont usługi Azure Storage. Obecnie nie obsługuje __magazynu obiektów Blob__ typ konta.

* Można ograniczyć do **uruchomienia w ramach określonych typów węzłów**, na przykład węzłów głównych lub węzłów procesu roboczego.

* Może być **utrwalone** lub **ad hoc**.

    **Trwały** skrypty są używane do dostosowywania nowych węzłach procesów roboczych dodane do klastra za pośrednictwem operacji skalowania. Utrwalonego skryptu może mieć zastosowanie także zmiany na inny typ węzła, takich jak węzła głównego, po wystąpieniu operacji skalowania.

  > [!IMPORTANT]
  > Akcje utrwalonego skryptu musi mieć unikatową nazwę.

    **Ad hoc** skryptów nie są zachowywane. Nie są stosowane do węzłów procesu roboczego po ma uruchomiono skrypt, dodać do klastra. Można następnie wspierać ad-hoc skrypt do utrwalonego skryptu lub obniżyć poziom utrwalonego skryptu do skryptu ad-hoc.

  > [!IMPORTANT]
  > Akcje skryptu używany podczas tworzenia klastra, automatycznie są zachowywane.
  >
  > Skrypty, które nie są kończyć się niepowodzeniem utrwalone, nawet jeśli specjalnie wskazujesz, że powinny one być.

* Można zaakceptować **parametry** które są używane przez skrypt podczas wykonywania.

* Uruchom z **głównego poziomu uprawnień** w węzłach klastra.

* Mogą być używane za pośrednictwem **witryny Azure portal**, **programu Azure PowerShell**, **wiersza polecenia platformy Azure w wersji 1.0**, lub **HDInsight .NET SDK**

Klaster przechowuje historię wszystkie skrypty, które zostały uruchomione. Historia jest przydatne, gdy trzeba znaleźć Identyfikatora skryptu dla operacji podwyższenia poziomu lub obniżania poziomu.

> [!IMPORTANT]
> Nie ma żadnych automatyczny sposób, aby cofnąć zmiany wprowadzone przez akcję skryptu. Ręcznie wycofania zmian lub napisać skrypt, który odwraca je.

### <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w procesie tworzenia klastra

Akcje skryptu używany podczas tworzenia klastra są nieco inne niż skryptu, który akcji wykonywanych w istniejącym klastrze:

* Skrypt jest **automatycznie utrwalonych**.

* A **błąd** w skrypcie może spowodować niepowodzenie procesu tworzenia klastra.

Na poniższym diagramie przedstawiono podczas wykonywania w trakcie procesu tworzenia akcji skryptu:

![Dostosowywanie klastra HDInsight i etapy podczas tworzenia klastra][img-hdi-cluster-states]

Skrypt jest uruchamiany, gdy skonfigurowano HDInsight. Skrypt jest uruchamiany równolegle w określonych węzłów w klastrze i uruchamiane za pomocą uprawnień użytkownika root w węzłach.

> [!NOTE]
> Można wykonywać operacje, takie jak zatrzymanie i uruchomienie usług, w tym usług związanych z usługi Hadoop. Zatrzymanie usług, upewnij się, kończy usługa Ambari i innych usług związanych z usługi Hadoop, uruchomione przed skryptu. Te usługi są wymagane do określenia pomyślnie kondycję i stan klastra podczas jego tworzenia.


Podczas tworzenia klastra można użyć wielu akcji skryptu na raz. Te skrypty są wywoływane w kolejności, w którym zostały określone.

> [!IMPORTANT]
> Akcje skryptu musi zostać zakończone w ciągu 60 minut lub przekroczenie limitu czasu. Podczas inicjowania obsługi klastra, skrypt jest uruchamiany równolegle innych procesów instalacji i konfiguracji. Konkurencję w odniesieniu do zasobów, takich jak czas lub sieci przepustowości Procesora może powodować skryptu, aby zająć dłużej niż w środowisku programistycznym.
>
> Aby zminimalizować czas potrzebny do uruchomienia skryptu, należy unikać zadania, takie jak pobieranie i kompilowanie aplikacji ze źródła. Przed kompilacją aplikacji i przechowywanie danych binarnych w usłudze Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Akcja skryptu na działającego klastra

Błąd w skrypcie uruchomiliśmy już uruchomione klastrów nie powoduje automatycznego klastra zmienić stan nie powiodło się. Po ukończeniu działania skryptu klastra należy powrócić do stanu "uruchomiony".

> [!IMPORTANT]
> Nawet wtedy, gdy klaster ma stan "uruchomiona", skryptu nie powiodło się może być uszkodzone elementy. Na przykład skrypt można usunąć pliki wymagane przez klaster.
>
> Akcje skrypty uruchamiane przy użyciu uprawnień głównych. Upewnij się, zrozumieć, zanim zostaną one zastosowane do klastra, działanie skryptu.

Podczas stosowania skrypt w klastrze, stan klastra zmieni się z **systemem** do **zaakceptowano**, następnie **konfiguracji HDInsight**, a na koniec z powrotem do  **Uruchamianie** pomyślne skryptów. Stan skryptu są rejestrowane w historii akcji skryptu, a te informacje można użyć, aby określić, czy skrypt zakończonych powodzeniem lub niepowodzeniem. Na przykład `Get-AzureRmHDInsightScriptActionHistory` polecenia cmdlet programu PowerShell służy do wyświetlania stanu skryptu. Zwraca informacje podobne do następującego tekstu:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]
> Jeśli zostały zmienione hasło użytkownika (administratora) klastra, po utworzeniu klastra, skryptu, który akcji wykonywanych względem tego klastra może zakończyć się niepowodzeniem. Jeśli masz wszystkie akcje utrwalonego skryptu tej węzłów procesu roboczego w docelowej tych skryptów może zakończyć się niepowodzeniem podczas skalowania klastra.

## <a name="example-script-action-scripts"></a>Przykładowe skrypty akcji skryptu

Można używać skryptów akcji skryptu za pomocą następujących narzędzi:

* Azure Portal
* Azure PowerShell
* Wiersza polecenia platformy Azure w wersji 1.0
* Zestaw SDK dla platformy .NET usługi HDInsight

HDInsight zapewnia skrypty dopasowane do zainstalowania następujących składników w klastrach HDInsight:

| Name (Nazwa) | Skrypt |
| --- | --- |
| **Dodaj konto usługi Azure Storage** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Zobacz [Dodaj dodatkowy magazyn do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md). |
| **Instalowanie aplikacji Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Zobacz [instalacji i używania aplikacji Hue w HDInsight clusters](hdinsight-hadoop-hue-linux.md). |
| **Zainstaluj skrypt Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Zobacz [instalacji i używania środowiska Presto na HDInsight clusters](hdinsight-hadoop-install-presto.md). |
| **Zainstalować platformę Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Zobacz [instalacji i używania platformy Solr w HDInsight clusters](hdinsight-hadoop-solr-install-linux.md). |
| **Zainstalować system Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Zobacz [instalacji i używania system Giraph w HDInsight clusters](hdinsight-hadoop-giraph-install-linux.md). |
| **Wstępne ładowanie bibliotek technologii Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Zobacz [Dodaj Hive bibliotek w klastrach HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| **Instalowanie i aktualizowanie środowiska Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Zobacz [Instalowanie lub aktualizowanie środowiska Mono na HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Za pomocą akcji skryptu, podczas tworzenia klastra

Ta sekcja zawiera przykłady na różne sposoby, można użyć akcji skryptu, podczas tworzenia klastra usługi HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Za pomocą akcji skryptu, podczas tworzenia klastra w witrynie Azure portal

1. Rozpocznij tworzenie klastra zgodnie z opisem w [Tworzenie klastrów usługi Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Podczas tworzenia klastra zostanie wyświetlony __klastra Podsumowanie__ strony. Z __klastra Podsumowanie__ wybierz opcję __Edytuj__ łączy dla __Zaawansowane ustawienia__.

    ![Link do ustawień zaawansowanych](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Z __Zaawansowane ustawienia__ zaznacz __akcji skryptu__. Z __akcji skryptu__ zaznacz __+ Prześlij nowe__

    ![Przesłać nową akcję skryptu](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Użyj __wybierz skrypt__ wpis, aby wybrać wstępnie przygotowanych skryptów. Aby użyć niestandardowego skryptu, wybierz __niestandardowe__ , a następnie podaj __nazwa__ i __identyfikator URI skryptu powłoki systemowej__ skryptu.

    ![Dodawanie skryptu w formularzu Wybierz skrypt](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy na formularzu:

    | Właściwość | Wartość |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz __niestandardowe__. W przeciwnym razie wybierz dostarczone skrypty. |
    | Name (Nazwa) |Określ nazwę dla akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Proces roboczy/główne/dozorcy |Określ węzły (**Head**, **procesu roboczego**, lub **dozorcy**), na którym skrypt jest uruchamiany. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __Utrwal tę akcję skryptu__ wpis, aby upewnić się, że skrypt jest stosowana podczas operacji skalowania.

5. Wybierz __Utwórz__ można zapisać skryptu. Następnie można użyć __+ Prześlij nowe__ można dodać inny skrypt.

    ![Wiele akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Po zakończeniu dodawania skryptów, użyj __wybierz__ przycisku, a następnie __dalej__ przycisk, aby powrócić do __klastra Podsumowanie__ sekcji.

3. Aby utworzyć klaster, wybierz __Utwórz__ z __podsumowanie klastra__ zaznaczenia.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Użyj akcji skryptu za pomocą szablonów usługi Azure Resource Manager

Akcje skryptu może służyć za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać przykład, zobacz [ https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/ ](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

W tym przykładzie zostanie dodany akcji skryptu, używając następującego kodu:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Aby uzyskać informacje na temat sposobu wdrażania szablonu zobacz następujące dokumenty:

* [Wdrażanie zasobów za pomocą szablonów i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Wdrażanie zasobów za pomocą szablonów i interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Za pomocą akcji skryptu, podczas tworzenia klastra za pomocą programu Azure PowerShell

W tej sekcji użyjesz [AzureRmHDInsightScriptAction Dodaj](https://msdn.microsoft.com/library/mt603527.aspx) polecenia cmdlet, wywoływanie skryptów, aby dostosować klastra. Przed kontynuowaniem upewnij się, zainstalowaniu i skonfigurowaniu programu Azure PowerShell. Aby uzyskać informacji na temat konfigurowania stacji roboczej do uruchamiania poleceń cmdlet programu HDInsight PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Poniższy skrypt pokazuje, jak zastosować akcji skryptu, podczas tworzenia klastra przy użyciu programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Może upłynąć kilka minut, zanim został utworzony klaster.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Za pomocą akcji skryptu, podczas tworzenia klastra z zestawu SDK HDInsight platformy .NET

Zestaw .NET SDK HDInsight zawiera biblioteki klienckie, które sprawia, że łatwiej pracować z HDInsight z poziomu aplikacji .NET. Przykładowy kod, zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Stosowanie akcji skryptu do działającego klastra

W tej sekcji Dowiedz się, jak stosować akcje skryptu do działającego klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Dotyczą akcji skryptu działającego klastra w witrynie Azure portal

1. Z [witryny Azure portal](https://portal.azure.com), wybierz klaster usługi HDInsight.

2. Omówienie klastrów HDInsight, zaznacz **akcji skryptu** kafelka.

    ![Kafelek akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Możesz również wybrać **wszystkie ustawienia** , a następnie wybierz **akcji skryptu** w sekcji Ustawienia.

3. W górnej części akcji skryptu, wybierz **Prześlij nowe**.

    ![Dodawanie skryptu do działającego klastra](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Użyj __wybierz skrypt__ wpis, aby wybrać wstępnie przygotowanych skryptów. Aby użyć niestandardowego skryptu, wybierz __niestandardowe__ , a następnie podaj __nazwa__ i __identyfikator URI skryptu powłoki systemowej__ skryptu.

    ![Dodawanie skryptu w formularzu Wybierz skrypt](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    W poniższej tabeli opisano elementy na formularzu:

    | Właściwość | Wartość |
    | --- | --- |
    | Wybierz skrypt | Aby użyć własnego skryptu, wybierz __niestandardowe__. W przeciwnym razie wybierz dostarczonego skryptu. |
    | Name (Nazwa) |Określ nazwę dla akcji skryptu. |
    | Identyfikator URI skryptu powłoki systemowej |Określ identyfikator URI skryptu. |
    | Proces roboczy/główne/dozorcy |Określ węzły (**Head**, **procesu roboczego**, lub **dozorcy**), na którym skrypt jest uruchamiany. |
    | Parametry |Określ parametry, jeśli jest to wymagane przez skrypt. |

    Użyj __Utrwal tę akcję skryptu__ wpis, aby upewnić się, czy skrypt jest stosowane podczas operacji skalowania.

5. Na koniec użyj **Utwórz** przycisk, aby zastosować skrypt do klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Dotyczą akcji skryptu działającego klastra za pomocą programu Azure PowerShell

Przed kontynuowaniem upewnij się, zainstalowaniu i skonfigurowaniu programu Azure PowerShell. Aby uzyskać informacji na temat konfigurowania stacji roboczej do uruchamiania poleceń cmdlet programu HDInsight PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

Poniższy przykład pokazuje sposób stosowania akcji skryptu do działającego klastra:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po zakończeniu operacji otrzymasz informacje podobne do następującego tekstu:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Zastosuj akcję skryptu do działającego klastra z wiersza polecenia platformy Azure

Przed kontynuowaniem upewnij się, że masz zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [zainstalować interfejs wiersza polecenia platformy Azure w wersji 1.0](../cli-install-nodejs.md).

> [!IMPORTANT]
> HDInsight wymaga interfejsu wiersza polecenia platformy Azure w wersji 1.0. Obecnie interfejs wiersza polecenia platformy Azure w wersji 2.0 nie zapewnia polecenia do pracy z HDInsight.

1. Aby włączyć tryb usługi Azure Resource Manager, wpisz następujące polecenie w wierszu polecenia:

    ```bash
    azure config mode arm
    ```

2. Poniższa tabela uwierzytelniania do subskrypcji platformy Azure.

    ```bash
    azure login
    ```

3. Użyj następującego polecenia, aby zastosować akcję skryptu do działającego klastra

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Jeżeli pominięto parametrów dla tego polecenia zostanie wyświetlony monit o ich. Jeśli skrypt zostanie określona z `-u` akceptuje parametry, określ je przy użyciu `-p` parametru.

    Nieprawidłowy węzeł typy są `headnode`, `workernode`, i `zookeeper`. Jeśli skrypt powinien być stosowany do wielu typów węzłów, należy określić typy, oddzielone ";". Na przykład `-n headnode;workernode`.

    Aby zachować skrypt, należy dodać `--persistOnSuccess`. Można również utrwalić skrypt później za pomocą `azure hdinsight script-action persisted set`.

    Po zakończeniu zadania pojawić się dane wyjściowe podobne do następującego tekstu:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Stosowanie akcji skryptu do działającego klastra przy użyciu interfejsu API REST

Zobacz [systemem akcji skryptu działającego klastra](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Dotyczą akcji skryptu działającego klastra z zestawu SDK HDInsight platformy .NET

Na przykład zastosować skryptów do klastra przy użyciu zestawu .NET SDK, zobacz [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Wyświetlanie historii, wspierania i obniżenie poziomu akcji skryptu

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Z [witryny Azure portal](https://portal.azure.com), wybierz klaster usługi HDInsight.

2. Omówienie klastrów HDInsight, zaznacz **akcji skryptu** kafelka.

    ![Kafelek akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Możesz również wybrać **wszystkie ustawienia** , a następnie wybierz **akcji skryptu** w sekcji Ustawienia.

4. Historia skryptów dla tego klastra jest wyświetlany w sekcji Akcje skryptu. Informacje te obejmują listę utrwalonych skryptów. Na poniższym zrzucie ekranu zobaczysz, że Solr skryptu zostało uruchomione w tym klastrze. Zrzut ekranu nie są wyświetlane wszystkie skrypty utrwalonych.

    ![Sekcja akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Wybieranie skryptu z historii wyświetla ten skrypt można znaleźć w sekcji właściwości. W górnej części ekranu można ponownie uruchomić skrypt lub promowania go.

    ![Właściwości akcji skryptu](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Można również użyć **...**  po prawej stronie wpisy w sekcji Akcje skryptu do wykonania akcji.

    ![Akcji skryptu... użycia](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

| Użyj następującego polecenia... | Aby... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Pobieranie informacji na temat akcji utrwalonego skryptu |
| Get-AzureRmHDInsightScriptActionHistory |Pobieranie historii akcji skryptu zastosowane do klastra lub szczegóły dotyczące określonego skryptu |
| Set-AzureRmHDInsightPersistedScriptAction |Promuje akcję skryptu ad-hoc Akcja utrwalonego skryptu |
| Remove-AzureRmHDInsightPersistedScriptAction |Przenosi do akcji utrwalonego skryptu akcji ad-hoc |

> [!IMPORTANT]
> Za pomocą `Remove-AzureRmHDInsightPersistedScriptAction` cofa akcje wykonywane przez skrypt. To polecenie cmdlet usuwa tylko Flaga utrwalonych.

Następujący skrypt przykładowy pokazuje, podwyższanie poziomu, a następnie obniżenie poziomu skryptu za pomocą polecenia cmdlet.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

| Użyj następującego polecenia... | Aby... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Pobierz listę akcje utrwalonego skryptu |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Pobieranie informacji na temat akcji określonych utrwalonego skryptu |
| `azure hdinsight script-action history list <clustername>` |Pobieranie historii akcji skryptu, stosowane do klastra |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Pobieranie informacji na temat działania określonego skryptu |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promuje akcję skryptu ad-hoc Akcja utrwalonego skryptu |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Przenosi do akcji utrwalonego skryptu akcji ad-hoc |

> [!IMPORTANT]
> Za pomocą `azure hdinsight script-action persisted delete` cofa akcje wykonywane przez skrypt. To polecenie cmdlet usuwa tylko Flaga utrwalonych.

### <a name="using-the-hdinsight-net-sdk"></a>Przy użyciu zestawu .NET SDK HDInsight

Na przykład pobrać skrypt historii z klastra przy użyciu zestawu .NET SDK, podwyższanie poziomu lub obniżenie poziomu skryptów, zobacz [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Również w tym przykładzie pokazano, jak instalowanie aplikacji usługi HDInsight przy użyciu zestawu .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Pomoc techniczna dotycząca oprogramowania typu open-source używane w klastrach HDInsight

Usługa Microsoft Azure HDInsight korzysta z ekosystemu technologii open source utworzonych na platformie Hadoop. Microsoft Azure oferuje ogólnego poziomu pomocy technicznej dla technologii open source. Aby uzyskać więcej informacji, zobacz **zakres pomocy technicznej** części [witryny sieci Web — często zadawane pytania dla pomocy technicznej Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom pomocy technicznej dla wbudowanych składników.

Istnieją dwa typy składników typu open source, które są dostępne w usłudze HDInsight:

* **Wbudowane składniki** — te składniki są wstępnie zainstalowane w klastrach HDInsight i zapewnia podstawowe funkcje klastra. Na przykład Menedżer zasobów usługi YARN, język zapytań programu Hive (HiveQL) i biblioteki mahout dostępnych należą do tej kategorii. Pełną listę składniki klastra jest dostępna w [nowości w wersjach klastra Hadoop dostarczanych przez HDInsight](hdinsight-component-versioning.md).
* **Składniki niestandardowe** —, jako użytkownik klastra, można zainstalować lub użyj w obciążenia dowolny składnik, dostępne w społeczności lub utworzonej przez użytkownika.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane. Microsoft Support pomaga wyizolować i rozwiązać problemy związane z tych składników.
>
> Składniki niestandardowe otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Pomoc techniczna firmy Microsoft może rozwiązać ten problem, lub ich może poprosić o angażowanie dostępne kanały dla technologii "open source", gdzie znajduje się specjalistyczna dla tej technologii. Na przykład, istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projektów Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/).

Usługa HDInsight oferuje kilka sposobów używania niestandardowych składników. Stosuje ten sam poziom pomocy technicznej niezależnie od tego, jak używać składnika lub zainstalowane w klastrze. Poniższa lista zawiera opis najbardziej typowych sposobów, że niestandardowe składniki mogą być używane w klastrach HDInsight:

1. Przesyłanie zadań — do Hadoop lub innych typów zadań, które wykonania lub użyć niestandardowych składników można przesyłać do klastra.

2. Dostosowywanie klastra — podczas tworzenia klastra, można określić dodatkowe ustawienia i niestandardowe składniki, które są instalowane w węzłach klastra.

3. Przykłady — dla popularnych składnikami niestandardowymi, firma Microsoft i inne osoby mogą zawierać przykładów, jak te składniki mogą być używane w klastrach HDInsight. Te przykłady są udostępniane bez pomocy technicznej.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Interfejs webowy Ambari służy do wyświetlania informacji rejestrowanych przez akcji skryptu. Jeśli skrypt zakończy się niepowodzeniem podczas tworzenia klastra, dzienniki są również dostępne w skojarzonym z klastrem domyślne konto magazynu. Ta sekcja zawiera informacje na temat sposobu pobierania dzienników za pomocą obu tych opcji.

### <a name="using-the-ambari-web-ui"></a>Za pomocą interfejsu użytkownika sieci Web systemu Ambari

1. W przeglądarce przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp CLUSTERNAME nazwą klastra usługi HDInsight.

    Po wyświetleniu monitu wprowadź nazwę konta administratora (Administrator) i hasło dla klastra. Może być konieczne ponowne wprowadzenie poświadczeń administratora w formularzu sieci web.

2. Na pasku w górnej części strony wybierz **ops** wpisu. Zostanie wyświetlona lista bieżących i wcześniejszych czynności wykonywane w klastrze za pomocą systemu Ambari.

    ![Pasek interfejsu użytkownika sieci web Ambari, wraz z operacjami wybrane](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Znajdź pozycje, które mają **Uruchom\_customscriptaction** w **operacji** kolumny. Te wpisy są tworzone po uruchomieniu akcji skryptu.

    ![Zrzut ekranu przedstawiający operacje](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Aby wyświetlić dane wyjściowe STDOUT i STDERR, wybierz wpis run\customscriptaction, a następnie przejść do szczegółów za pośrednictwem łącza. Tych danych wyjściowych jest generowany, gdy skrypt zostanie uruchomiony i mogą zawierać przydatne informacje.

### <a name="access-logs-from-the-default-storage-account"></a>Uzyskiwanie dostępu do dzienników z domyślnego konta magazynu

Jeśli tworzenie klastra kończy się niepowodzeniem z powodu błędu skryptu, dzienniki są przechowywane na koncie magazynu klastra.

* Dzienniki magazynu są dostępne pod adresem `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Zrzut ekranu przedstawiający operacje](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    W tym katalogu dzienniki są zorganizowane oddzielnie dla węzła głównego, workernode i węzły dozorcy. Przykłady to:

    * **Węzeł główny** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Węzeł procesu roboczego** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Węzeł usługi zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Wszystkie stdout i stderr odpowiadający mu host jest przekazywany do konta magazynu. Istnieje **dane wyjściowe —\*.txt** i **błędy -\*.txt** dla każdej akcji skryptu. Plik wyjściowy *.txt zawiera informacje o identyfikatorze URI skryptu, który uzyskano uruchomić na hoście. Przykładem tych informacji jest następujący tekst:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Istnieje możliwość, wielokrotne Tworzenie klastra akcji skryptu o takiej samej nazwie. W takim przypadku można odróżnić odpowiednie dzienniki na podstawie daty nazwy folderu. Na przykład struktura folderów dla klastra (mycluster) utworzone w różnych terminach pojawia się podobne do następujących wpisy dziennika:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Jeśli tworzysz klaster akcji skryptu o takiej samej nazwie w tym samym dniu umożliwia unikatowy prefiks zidentyfikować odpowiednich plików dziennika.

* Jeśli tworzysz klaster w pobliżu 0:00:00 (północ), jest to możliwe, że pliki dziennika rozciągać się na dwa dni. W takiej sytuacji zobaczysz dwa foldery inną datę dla tego samego klastra.

* Przekazywanie plików dziennika na domyślny kontener może potrwać maksymalnie 5 minut, szczególnie w przypadku dużych klastrach. Tak Jeśli chcesz uzyskać dostęp w dziennikach, nie należy natychmiast usuwać klastra w przypadku niepowodzenia akcji skryptu.

### <a name="ambari-watchdog"></a>Strażnika systemu Ambari

> [!WARNING]
> Nie należy zmieniać hasła strażnika Ambari (hdinsightwatchdog) w klastrze usługi HDInsight opartych na systemie Linux. Zmienianie hasła dla tego konta spowoduje przerwanie możliwość uruchamiania nowe akcje skryptów w klastrze HDInsight.

### <a name="cant-import-name-blobservice"></a>Nie można zaimportować nazwa BlobService

__Objawy__: niepowodzenie akcji skryptu. Tekst podobny do poniższego błędu jest wyświetlany podczas przeglądania operacji w Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Przyczyna__: ten błąd występuje podczas uaktualniania klienta usługi Azure Storage dla języka Python, dostępnej z klastrem HDInsight. HDInsight oczekuje, że klient usługi Azure Storage 0.20.0.

__Rozpoznawanie__: Aby rozwiązać ten problem, ręcznie połączyć się z każdym klastra węzła przy użyciu `ssh` i ponowne zainstalowanie magazynu poprawną wersję klienta należy użyć następującego polecenia:

```bash
sudo pip install azure-storage==0.20.0
```

Aby uzyskać informacji na temat łączenia się z klastrem przy użyciu protokołu SSH, zobacz [użycia protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Historia nie pokazuje skrypty używane podczas tworzenia klastra

Jeśli klaster został utworzony przed 15 marca 2016 r. nie widać wpis w historii akcji skryptu. Zmiana rozmiaru klastra powoduje, że skrypty, które pojawią się w historii akcji skryptu.

Istnieją jednak dwa wyjątki:

* Jeśli klaster został utworzony przed 1 września 2015 r. Ta data jest datą akcji skryptu zostały wprowadzone. Każdy klaster utworzone przed tą datą nie ma użyć akcji skryptu do tworzenia klastra.

* Jeśli używane wielu akcji skryptów podczas tworzenia klastra, a używany dla tej samej nazwy wielu skryptów lub takiej samej nazwie, tego samego identyfikatora URI, ale różnych parametrów do wielu skryptów. W takich przypadkach jest wyświetlany następujący błąd:

    Nie nowy skrypt, działania mogą być uruchomione w tym klastrze z powodu konfliktu nazw skryptu w istniejących skryptów. Nazwy skryptu podane w klastrze tworzenie muszą być unikatowe. Istniejące skrypty uruchamiane są na zmiany rozmiaru.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie skryptów Akcja skryptu dla HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalowanie i korzystanie z platformy Solr w klastrach HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalowanie i używanie systemu Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Dodaj dodatkowy magazyn do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"

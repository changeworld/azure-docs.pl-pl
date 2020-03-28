---
title: 'Samouczek: Tworzenie klastrów — Azure HDInsight za pomocą łańszeń celi usługi Azure Automation'
description: Dowiedz się, jak tworzyć i usuwać klastry usługi Azure HDInsight za pomocą skryptów uruchamianych w chmurze przy użyciu śmięty uruchomieniu usługi Azure Automation.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75553513"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Samouczek: Tworzenie klastrów usługi Azure HDInsight za pomocą usługi Azure Automation

Usługa Azure Automation umożliwia tworzenie skryptów, które są uruchamiane w chmurze i zarządzanie zasobami platformy Azure na żądanie lub zgodnie z harmonogramem. W tym artykule opisano sposób tworzenia śmięty programu PowerShell w celu tworzenia i usuwania klastrów usługi Azure HDInsight.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj moduły niezbędne do interakcji z hdinsight.
> * Tworzenie i przechowywanie poświadczeń potrzebnych podczas tworzenia klastra.
> * Utwórz nowy element runbook usługi Azure Automation, aby utworzyć klaster USŁUGI HDInsight.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Istniejące [konto usługi Azure Automation](../automation/automation-quickstart-create-account.md).
* Istniejące [konto usługi Azure Storage](../storage/common/storage-account-create.md), które będzie używane jako magazyn klastra.

## <a name="install-hdinsight-modules"></a>Instalowanie modułów HDInsight

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Wybierz swoje konta usługi Azure Automation.
1. Wybierz **galerię Moduły** w obszarze **Zasoby udostępnione**.
1. Wpisz **AzureRM.Profile** w polu i naciśnij enter do wyszukiwania. Wybierz dostępny wynik wyszukiwania.
1. Na ekranie **AzureRM.profile** wybierz pozycję **Importuj**. Zaznacz to pole, aby zaktualizować moduły platformy Azure, a następnie wybierz **przycisk OK**.

    ![importowanie modułu AzureRM.profile](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Wróć do galerii modułów, wybierając **galerię modułów** w obszarze **Zasoby udostępnione**.
1. Wpisz **HDInsight**. Wybierz **pozycję AzureRM.HDInsight**.

    ![przeglądanie modułów HDInsight](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. W panelu **AzureRM.HDInsight** wybierz pozycję **Importuj** i **OK**.

    ![importowanie modułu AzureRM.HDInsight](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Tworzenie poświadczeń

1. W obszarze **Zasoby udostępnione**wybierz pozycję **Poświadczenia**.
1. Wybierz **pozycję Dodaj poświadczenie**.
1. Wprowadź wymagane informacje w panelu **Nowe poświadczenia.** To poświadczenie jest do przechowywania hasła klastra, który umożliwia zalogowanie się do Ambari.

    | Właściwość | Wartość |
    | --- | --- |
    | Nazwa | `cluster-password` |
    | Nazwa użytkownika | `admin` |
    | Hasło | `SECURE_PASSWORD` |
    | Potwierdź hasło | `SECURE_PASSWORD` |

1. Wybierz **pozycję Utwórz**.
1. Powtórz ten sam proces `ssh-password` dla `sshuser` nowego poświadczenia z nazwą użytkownika i wybranym hasłem. Wybierz **pozycję Utwórz**. To poświadczenie jest do przechowywania hasła SSH dla klastra.

    ![tworzenie poświadczenia](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Tworzenie śmiętu w celu utworzenia klastra

1. Wybierz **elementy runbook w** obszarze **Automatyzacja procesów**.
1. Wybierz **pozycję Utwórz fragment .**
1. W panelu **Tworzenie przewodniczącego** wprowadź nazwę systemu runbook, `hdinsight-cluster-create`na przykład . Wybierz **pozycję Powershell** z listy rozwijanej **Typu eks-owego.**
1. Wybierz **pozycję Utwórz**.

    ![tworzenie śmiętu](./media/manage-clusters-runbooks/create-runbook.png)

1. Wprowadź następujący kod na ekranie Edytuj program Runbook programu **PowerShell** i wybierz pozycję **Publikuj:**

    ![publikowanie eks-u](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Tworzenie kreślić w celu usunięcia klastra

1. Wybierz **elementy runbook w** obszarze **Automatyzacja procesów**.
1. Wybierz **pozycję Utwórz fragment .**
1. W panelu **Tworzenie przewodniczącego** wprowadź nazwę systemu runbook, `hdinsight-cluster-delete`na przykład . Wybierz **pozycję Powershell** z listy rozwijanej **Typu eks-owego.**
1. Wybierz **pozycję Utwórz**.
1. Wprowadź następujący kod na ekranie Edytuj program Runbook programu **PowerShell** i wybierz pozycję **Publikuj:**

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Wykonywanie obrażeń eks-owych

### <a name="create-a-cluster"></a>Tworzenie klastra

1. Wyświetl listę uruchomieniu dla swojego konta automatyzacji, wybierając elementy **runbook w** obszarze **Automatyzacja procesów**.
1. Wybierz `hdinsight-cluster-create`lub nazwę używaną podczas tworzenia elementu runbook tworzenia klastra.
1. Wybierz **przycisk Start,** aby natychmiast wykonać projekt runbook. Można również zaplanować okresowe uruchamianie śmięty. Zobacz [Planowanie uruchomieniu w usłudze Azure Automation](../automation/shared-resources/schedules.md)
1. Wprowadź wymagane parametry skryptu i wybierz **przycisk OK**. Spowoduje to utworzenie nowego klastra HDInsight o nazwie określonej w parametrze **CLUSTERNAME.**

    ![wykonywanie tworzenia runbooka klastra](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Usuwanie klastra

Usuń klaster, `hdinsight-cluster-delete` wybierając utworzony element runbook. Wybierz **start**, wprowadź parametr **CLUSTERNAME** i wybierz przycisk **OK**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, usuń konto usługi Azure Automation, które zostało utworzone w celu uniknięcia niezamierzonych opłat. Aby to zrobić, przejdź do witryny Azure Portal, wybierz grupę zasobów, w której utworzono konto usługi Azure Automation, wybierz konto automatyzacji, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie klastrami Apache Hadoop w programie HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md)
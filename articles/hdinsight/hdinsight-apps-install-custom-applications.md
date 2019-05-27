---
title: Instalowanie własnych niestandardowych aplikacji Apache Hadoop w usłudze Azure HDInsight
description: Informacje o sposobie instalowania aplikacji usługi HDInsight w usłudze HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 0acac29ee49bc94c195d0e13e55fff3a735ad36b
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859806"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie niestandardowych aplikacji platformy Apache Hadoop w usłudze Azure HDInsight

W tym artykule zostanie dowiesz się, jak zainstalować [Apache Hadoop](https://hadoop.apache.org/) aplikacji w usłudze Azure HDInsight, który nie został opublikowany w witrynie Azure portal. W tym artykule zostanie zainstalowana aplikacja [Hue](https://gethue.com/).

Aplikacja usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight opartym na systemie Linux.  Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

Inne pokrewne artykuły:

* [Instalowanie aplikacji HDInsight](hdinsight-apps-install-applications.md): Dowiedz się, jak instalowanie aplikacji usługi HDInsight do klastrów.
* [Publikowanie aplikacji HDInsight](hdinsight-apps-publish-applications.md): Dowiedz się, jak opublikować niestandardowe aplikacje HDInsight w portalu Azure Marketplace.
* [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Dowiedz się, jak zdefiniować aplikacje HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-hdinsight-applications"></a>Instalowanie aplikacji usługi HDInsight
Aplikacje usługi HDInsight można instalować podczas tworzenia klastra lub w istniejącym klastrze usługi HDInsight. Definiowanie szablonów usługi Azure Resource Manager, zobacz [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).

Pliki potrzebne do wdrożenia tej aplikacji (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): Szablon usługi Resource Manager do instalowania aplikacji HDInsight. Zobacz [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) do tworzenia szablonu usługi Resource Manager.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Akcja skryptu wywoływana przez szablon usługi Resource Manager w celu skonfigurowania węzła krawędzi.
* [HUE-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Plik binarny aplikacji hue wywoływany ze skryptu hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): Plik binarny aplikacji hue wywoływany ze skryptu hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): Przykład aplikacji sieci web (Tomcat) wywoływana ze skryptu hui-install_v0.sh.

**Instalowanie aplikacji Hue w istniejącym klastrze usługi HDInsight**

1. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon usługi Resource Manager w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Ten przycisk otwiera szablon usługi Resource Manager w witrynie Azure Portal.  Szablon usługi Resource Manager znajduje się w [ https://github.com/hdinsight/Iaas-Applications/tree/master/Hue ](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  Aby dowiedzieć się, jak napisać ten szablon usługi Resource Manager, zobacz [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
2. W bloku **Parametry** wprowadź następujące informacje:

   * **ClusterName**: Wprowadź nazwę klastra, w którym chcesz zainstalować aplikację. Musi to być istniejący klaster.
3. Kliknij przycisk **OK**, aby zapisać parametry.
4. W bloku **Wdrożenie niestandardowe** wprowadź wartość opcji **Grupa zasobów**.  Grupa zasobów jest kontenerem, który grupuje klaster, zależne konto magazynu oraz inne zasoby. Wymagane jest użycie tej samej grupy zasobów, która jest używana przez klaster.
5. Kliknij opcję **Postanowienia prawne**, a następnie kliknij przycisk **Utwórz**.
6. Sprawdź, czy zaznaczone jest pole wyboru **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij przycisk **Utwórz**. Możesz sprawdzić stan instalacji na kafelku przypiętym do pulpitu nawigacyjnego portalu i w powiadomieniu portalu (kliknij ikonę dzwonka w górnej części portalu).  Instalowanie aplikacji trwa około 10 minut.

**Instalowanie aplikacji Hue podczas tworzenia klastra**

1. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon usługi Resource Manager w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Ten przycisk otwiera szablon usługi Resource Manager w witrynie Azure Portal.  Szablon usługi Resource Manager znajduje się w [ https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json ](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  Aby dowiedzieć się, jak napisać ten szablon usługi Resource Manager, zobacz [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx).
2. Postępuj zgodnie z instrukcjami, aby utworzyć klaster i zainstalować aplikację Hue. Aby uzyskać więcej informacji na temat tworzenia klastrów usługi HDInsight, zobacz temat [Tworzenie opartych na systemie Linux klastrów Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Oprócz witryny Azure portal można również użyć [programu Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) i [klasyczny interfejs wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) do wywołania szablonów usługi Resource Manager.

## <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji
Możesz zobaczyć stan aplikacji w witrynie Azure Portal, aby sprawdzić poprawność instalacji aplikacji. Ponadto możesz sprawdzić, czy wszystkie punkty końcowe HTTP zostały utworzone zgodnie z oczekiwaniami oraz sprawdzić stronę sieci Web, jeśli istnieje:

**Otwieranie portalu Hue**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Klastry usługi HDInsight** w lewym menu.  Jeśli jej nie widzisz, kliknij przycisk **Przeglądaj**, a następnie kliknij pozycję **Klastry usługi HDInsight**.
3. Kliknij klaster, w którym zainstalowano aplikację.
4. W bloku **Ustawienia** kliknij opcję **Aplikacje** w obszarze **Ogólne**. Aplikacja **hue** powinna być widoczna w bloku **Zainstalowane aplikacje**.
5. Kliknij pozycję **hue** na liście, aby wyświetlić właściwości.  
6. Kliknij łącze strony sieci Web, aby zweryfikować witrynę sieci Web; Otwórz punkt końcowy HTTP w przeglądarce, aby sprawdzić poprawność interfejsu użytkownika sieci web aplikacji Hue, otwórz punkt końcowy SSH przy użyciu protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="troubleshoot-the-installation"></a>Rozwiązywanie problemów z instalacją
Możesz sprawdzić stan instalacji aplikacji w powiadomieniu portalu (kliknij ikonę dzwonka w górnej części portalu).

Jeśli instalacja aplikacji nie powiodła się, możesz wyświetlić komunikaty o błędach i informacje debugowania w trzech miejscach:

* Aplikacje usługi HDInsight: ogólne informacje o błędach.

    Otwórz klaster w portalu, a następnie kliknij opcję Aplikacje w bloku Ustawienia:

    ![błąd instalacji aplikacji usługi hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* Akcja skryptu HDInsight: Jeśli komunikat o błędzie aplikacji HDInsight wskazuje niepowodzenie akcji skryptu, zostanie wyświetlone więcej szczegółów na temat błędu skryptu w okienku akcji skryptu.

    Kliknij akcję skryptu w bloku Ustawienia. Historia akcji skryptu zawiera komunikaty o błędach.

    ![błąd akcji skryptu aplikacji usługi hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Ambari Web UI: Jeśli przyczyną niepowodzenia był skrypt instalacji, w celu sprawdzenia pełnych dzienników dotyczących skryptów instalacji należy użyć interfejsu użytkownika sieci Web Ambari.

    Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>Usuwanie aplikacji usługi HDInsight
Istnieje kilka sposobów usuwania aplikacji usługi HDInsight.

### <a name="use-portal"></a>Korzystanie z portalu
**Usuwanie aplikacji przy użyciu portalu**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Klastry usługi HDInsight** w lewym menu.  Jeśli jej nie widzisz, kliknij przycisk **Przeglądaj**, a następnie kliknij pozycję **Klastry usługi HDInsight**.
3. Kliknij klaster, w którym zainstalowano aplikację.
4. W bloku **Ustawienia** kliknij opcję **Aplikacje** w obszarze **Ogólne**. Powinna zostać wyświetlona lista zainstalowanych aplikacji. W tym samouczku aplikacja **hue** jest widoczna w bloku **Zainstalowane aplikacje**.
5. Kliknij prawym przyciskiem myszy aplikację, którą chcesz usunąć, a następnie kliknij przycisk **Usuń**.
6. Kliknij przycisk **Tak**, aby potwierdzić.

Korzystając z portalu, możesz usunąć klaster lub usunąć grupę zasobów zawierającą aplikację.

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Przy użyciu programu Azure PowerShell możesz usunąć klaster lub grupę zasobów. Zobacz temat [Usuwanie klastrów przy użyciu programu PowerShell systemu Azure](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Przy użyciu interfejsu wiersza polecenia platformy Azure możesz usunąć klaster lub grupę zasobów. Zobacz temat [Usuwanie klastrów przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>Kolejne kroki
* [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Dowiedz się, jak opracowywać szablony usługi Resource Manager w celu wdrażania aplikacji HDInsight.
* [Instalowanie aplikacji HDInsight](hdinsight-apps-install-applications.md): Dowiedz się, jak instalowanie aplikacji usługi HDInsight do klastrów.
* [Publikowanie aplikacji HDInsight](hdinsight-apps-publish-applications.md): Dowiedz się, jak opublikować niestandardowe aplikacje HDInsight w portalu Azure Marketplace.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów opartych na systemie Linux Apache Hadoop w HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Dowiedz się, jak wywoływać szablony usługi Resource Manager w celu tworzenia klastrów HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.

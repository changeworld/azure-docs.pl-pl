---
title: Użyj węzłami pusty edge na klastrów platformy Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Jak dodać węzeł krawędzi pusty do klastra usługi HDInsight, który może być używany jako klient, a następnie/hosta testów aplikacji usługi HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jgao
ms.openlocfilehash: 95ffc033a442fcf6074998398104ccb01e7a01a7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Użyj węzłami pusty edge na klastrów platformy Hadoop w usłudze HDInsight

Dowiedz się, jak dodać krawędzi pustego węzła do klastra usługi HDInsight. Węzeł krawędzi pusty jest maszyny wirtualnej systemu Linux przy użyciu tego samego klienta narzędzi zainstalowane i skonfigurowane tak jak headnodes, ale bez żadnych usług Hadoop uruchomiona. Za pomocą węzła krawędzi dostęp do klastra, testowanie aplikacji klienckich i hosting aplikacji klienta. 

Można dodać krawędzi pustego węzła w istniejącym klastrze usługi HDInsight, do nowego klastra podczas tworzenia klastra. Dodawanie węzła krawędzi pusty jest wykonywane przy użyciu szablonu usługi Azure Resource Manager.  W poniższym przykładzie pokazano, jak jest wykonywane przy użyciu szablonu:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Jak pokazano w przykładzie, opcjonalnie można wywołać [akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md) można wykonać dodatkowe czynności konfiguracyjne, takich jak instalowanie [Apache Hue](hdinsight-hadoop-hue-linux.md) w węźle krawędzi. Skrypt akcji skryptu musi być dostępny publicznie w sieci web.  Na przykład jeśli skrypt jest przechowywany w magazynie Azure, użyj publiczne kontenery lub publiczne obiekty BLOB.

Rozmiar maszyny wirtualnej węzła krawędzi musi spełniać wymagania rozmiar maszyny wirtualnej węzła procesu roboczego HDInsight klastra. Zalecany proces roboczy rozmiarów maszyn wirtualnych węzła, zobacz [klastrów utworzyć Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Po utworzeniu węzeł krawędzi, można połączyć się z węzłem krawędzi przy użyciu protokołu SSH i uruchamianie narzędzi klienta do dostępu do klastra usługi Hadoop w usłudze HDInsight.

> [!WARNING] 
> Niestandardowe składniki, które są zainstalowane w węźle krawędzi otrzymywania uzasadnione ekonomicznie pomocy technicznej firmy Microsoft. Może to spowodować w rozwiązywaniu problemów występujących u użytkownika. Lub może być określonej zasoby społeczności, aby uzyskać dalszą pomoc. Poniżej przedstawiono niektóre najbardziej aktywne witryn uzyskiwanie pomocy od społeczności:
>
> * [Forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com).
>
> Jeśli korzystasz z technologii Apache, można znaleźć pomoc za pośrednictwem Apache witryny projektu na [ http://apache.org ](http://apache.org), takich jak [Hadoop](http://hadoop.apache.org/) lokacji.

> [!NOTE]
> Taki sam jak klastrów, krawędzi węzły są zarządzane poprawki.  Aby uzyskać więcej informacji, zobacz [poprawki systemu operacyjnego dla usługi HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Dodawanie węzła krawędzi do istniejącego klastra
W tej sekcji możesz Użyj szablonu usługi Resource Manager, aby dodać węzeł krawędzi w istniejącym klastrze usługi HDInsight.  Szablon usługi Resource Manager można znaleźć w [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Szablon usługi Resource Manager wywołuje akcję skryptu, znajdujący się w https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Skrypt nie wykonywać żadnych akcji.  Jest aby zademonstrować wywoływania akcji skryptu z szablonem usługi Resource Manager.

**Aby dodać węzeł krawędzi pusty do istniejącego klastra**

1. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otwórz szablon usługi Azure Resource Manager w portalu Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Należy skonfigurować następujące właściwości:
   
   * **Subskrypcja**: Wybierz subskrypcję platformy Azure używana do tworzenia klastra.
   * **Grupa zasobów**: Wybierz grupę zasobów, używany do istniejącego klastra usługi HDInsight.
   * **Lokalizacja**: Wybierz lokalizację, w istniejącym klastrze usługi HDInsight.
   * **Nazwa klastra**: Wprowadź nazwę istniejącego klastra usługi HDInsight.
   * **Rozmiar węzła krawędzi**: Wybierz jeden z rozmiarów maszyn wirtualnych. Rozmiar maszyny wirtualnej musi spełniać wymagania rozmiar maszyny wirtualnej węzła procesu roboczego. Zalecany proces roboczy rozmiarów maszyn wirtualnych węzła, zobacz [klastrów utworzyć Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Krawędzi prefiksu węzła**: wartość domyślna to **nowe**.  Przy użyciu wartości domyślnej, jest nazwa węzła krawędzi **nowe edgenode**.  Można dostosować prefiks z portalu. Można również dostosować pełną nazwę z szablonu.

4. Sprawdź **akceptuję warunki i postanowienia, o których wspomniano**, a następnie kliknij przycisk **zakupu** można utworzyć węzła krawędzi.

>[!IMPORTANT]
> Upewnij się wybrać grupę zasobów platformy Azure dla istniejącego klastra usługi HDInsight.  W przeciwnym razie otrzymasz komunikat o błędzie "nie można wykonać żądanej operacji dla zasobu zagnieżdżonego. Zasobu nadrzędnego "&lt;ClusterName >' nie znaleziono."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Dodaj węzeł krawędzi, podczas tworzenia klastra
W tej sekcji użyjesz szablonu usługi Resource Manager do tworzenia klastra usługi HDInsight z węzłem krawędzi.  Szablon usługi Resource Manager można znaleźć w [galerię szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Szablon usługi Resource Manager wywołuje akcję skryptu, znajdujący się w https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Skrypt nie wykonywać żadnych akcji.  Jest aby zademonstrować wywoływania akcji skryptu z szablonem usługi Resource Manager.

**Aby utworzyć klaster usługi HDInsight z węzłem krawędzi**

1. Tworzenie klastra usługi HDInsight, jeśli nie masz jeszcze.  Zobacz [rozpocząć korzystanie z platformy Hadoop w usłudze HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otwórz szablon usługi Azure Resource Manager w portalu Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Należy skonfigurować następujące właściwości:
   
   * **Subskrypcja**: Wybierz subskrypcję platformy Azure używana do tworzenia klastra.
   * **Grupa zasobów**: Utwórz nową grupę zasobów klastra.
   * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów.
   * **Nazwa klastra**: Wprowadź nazwę dla nowego klastra utworzyć.
   * **Nazwa użytkownika logowania klastra**: Wprowadź nazwę użytkownika Hadoop HTTP.  Nazwa domyślna to **admin**.
   * **Hasło logowania klastra**: Wprowadź hasło użytkownika Hadoop HTTP.
   * **SSH nazwy użytkownika**: Wprowadź nazwę użytkownika SSH. Nazwa domyślna to **sshuser**.
   * **SSH hasła**: Wprowadź hasło użytkownika SSH.
   * **Zainstaluj akcji skryptu**: należy zachować wartość domyślną dla pośrednictwa w tym samouczku.
     
     Niektóre właściwości zostały zapisane na stałe w szablonie: typ klastra, liczba węzłów procesu roboczego klastra rozmiaru węzła krawędzi i nazwa węzła krawędzi.
4. Sprawdź **akceptuję warunki i postanowienia, o których wspomniano**, a następnie kliknij przycisk **zakupu** do utworzenia klastra z węzłem krawędzi.

## <a name="add-multiple-edge-nodes"></a>Dodawanie wielu węzłów krawędzi

Można dodać wiele krawędzi węzłów w klastrze usługi HDInsight.  Konfiguracja wielu węzłów krawędzi jest możliwe tylko za pomocą usługi Azure Resource Manager szablonów.  Zobacz przykład szablonu na początku tego artykułu.  Musisz zaktualizować **targetInstanceCount** aby odzwierciedlić liczby węzłów krawędzi chcesz utworzyć.

## <a name="access-an-edge-node"></a>Dostęp do węzła krawędzi
Węzeł brzegowy ssh punkt końcowy jest &lt;EdgeNodeName >.&lt; ClusterName >-ssh.azurehdinsight.net:22.  Na przykład nowy edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Węzeł krawędzi jest wyświetlany jako aplikacji w portalu Azure.  Portal zawiera informacje dostępu do węzła krawędzi przy użyciu protokołu SSH.

**Aby sprawdzić punkt końcowy SSH węzła krawędzi**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster usługi HDInsight z węzłem krawędzi.
3. Kliknij przycisk **aplikacji**. Zostanie wyświetlona węzła krawędzi.  Nazwa domyślna to **nowe edgenode**.
4. Kliknij węzeł krawędzi. Zostanie wyświetlona punkt końcowy SSH.

**Aby korzystanie z programu Hive w węźle krawędzi**

1. Połącz się z węzłem brzegowym za pomocą protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po nawiązaniu połączenia z węzłem krawędzi przy użyciu protokołu SSH, użyj następującego polecenia, aby otworzyć konsolę gałęzi:
   
        hive
3. Uruchom następujące polecenie, aby wyświetlić tabele programu Hive w klastrze:
   
        show tables;

## <a name="delete-an-edge-node"></a>Usuń węzeł krawędzi
W portalu Azure, można usunąć węzła krawędzi.

**Dostępu węzła krawędzi**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster usługi HDInsight z węzłem krawędzi.
3. Kliknij przycisk **aplikacji**. Zostanie wyświetlona lista węzłów krawędzi.  
4. Kliknij prawym przyciskiem myszy węzeł krawędzi chcesz usunąć, a następnie kliknij przycisk **usunąć**.
5. Kliknij przycisk **Tak**, aby potwierdzić.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule ma przedstawiono sposób dodawania węzła krawędzi oraz sposób dostępu do węzła krawędzi. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md): dowiedz się, jak instalować aplikacje usługi HDInsight w klastrach.
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć aplikację usługi HDInsight nieopublikowane do usługi HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Tworzenie klastrów Hadoop w usłudze HDInsight opartych na systemie Linux przy użyciu szablonów usługi Resource Manager): dowiedz się, jak wywoływać szablony usługi Resource Manager w celu tworzenia klastrów usługi HDInsight.


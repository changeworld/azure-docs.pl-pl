---
title: Używanie pustych węzłów brzegowych w klastrach usługi Apache Hadoop w HDInsight — Azure
description: Jak dodać pustego węzła krawędzi do klastra usługi HDInsight, który może służyć jako klienta, a następnie/hosta testów aplikacji HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: 41313c7d484df5568fbf729b683a0e9df5325abc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433737"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Używanie pustych węzłów brzegowych w klastrach usługi Apache Hadoop w HDInsight

Dowiedz się, jak dodać pustego węzła krawędzi do klastra usługi HDInsight. Pustego węzła krawędzi jest maszyną wirtualną systemu Linux przy użyciu tych samych narzędzi klienckich, które są zainstalowane i skonfigurowane tak jak w węzłach głównych, ale bez [Apache Hadoop](https://hadoop.apache.org/) usług. W węźle brzegowym służy do uzyskiwania dostępu do klastra, testowania aplikację kliencką i hosting aplikacji klienckich. 

Pustego węzła krawędzi można dodać do istniejącego klastra HDInsight, do nowego klastra podczas tworzenia klastra. Dodawanie pustego węzła krawędzi jest wykonywane przy użyciu szablonu usługi Azure Resource Manager.  W poniższym przykładzie pokazano, jak jest wykonywane przy użyciu szablonu:

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

Jak pokazano w przykładzie, można opcjonalnie wywołać [skryptu akcji](hdinsight-hadoop-customize-cluster-linux.md) przeprowadzenie dodatkowej konfiguracji, takich jak instalowanie [Apache Hue](hdinsight-hadoop-hue-linux.md) w węźle krawędzi. Skrypt akcji skryptu musi być dostępny publicznie w Internecie.  Na przykład jeśli skrypt jest przechowywany w usłudze Azure storage, użyj publiczne kontenery lub publiczne obiekty BLOB.

Rozmiar maszyny wirtualnej węzła krawędzi musi spełniać wymagania rozmiar maszyny wirtualnej węzła procesu roboczego HDInsight klastra. Rozmiary maszyn wirtualnych węzła zalecane procesu roboczego, zobacz [klastrów utworzyć Apache Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Po utworzeniu węzeł krawędzi, możesz nawiązać połączenie z węzłem krawędzi za pomocą protokołu SSH i uruchom narzędzia klienta, aby uzyskać dostęp do klastra usługi Hadoop w HDInsight.

> [!WARNING]   
> Niestandardowe składniki, które są instalowane w węzłach brzegowych otrzymywać uzasadnioną komercyjnie pomoc techniczną od firmy Microsoft. Może to spowodować Rozwiązywanie problemów, które wystąpią. Lub użytkownik może odwołać się do zasobów społeczności, aby uzyskać dalszą pomoc. Poniżej przedstawiono niektóre z najbardziej aktywnych lokacji w celu uzyskania pomocy od społeczności:
>
> * [Forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Jeśli używasz technologii Apache można znaleźć pomoc za pośrednictwem Apache witryny projektu na [ https://apache.org ](https://apache.org), takich jak [Apache Hadoop](https://hadoop.apache.org/) lokacji.

> [!IMPORTANT]
> Obrazy Ubuntu stają się dostępne dla nowego Tworzenie klastra HDInsight w ciągu 3 miesięcy opublikować. Począwszy od stycznia 2019 r, są uruchomione klastrów (łącznie z węzłów krawędzi) **nie** poprawiono automatycznie. Klienci, należy użyć akcji skryptu lub innych mechanizmów zastosowania poprawki względem działającego klastra.  Aby uzyskać więcej informacji, zobacz [stosowania poprawek systemu operacyjnego dla HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Dodaj węzeł krawędzi do istniejącego klastra
W tej sekcji używasz szablonu usługi Resource Manager można dodać węzła brzegowego do istniejącego klastra HDInsight.  Szablon usługi Resource Manager można znaleźć w [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Szablon usługi Resource Manager wymaga akcji skryptu, znajdujący się w https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Skrypt nie wykonuje żadnych akcji.  Jest aby zademonstrować wywoływania akcji skryptu za pomocą szablonu usługi Resource Manager.

**Aby dodać pustego węzła krawędzi do istniejącego klastra**

1. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon usługi Azure Resource Manager w witrynie Azure portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Skonfiguruj następujące właściwości:
   
   * **Subskrypcja**: Wybierz subskrypcję platformy Azure używane do tworzenia klastra.
   * **Grupa zasobów**: Wybierz grupę zasobów używaną do istniejącego klastra HDInsight.
   * **Lokalizacja**: Wybierz lokalizację, w istniejącym klastrze HDInsight.
   * **Nazwa klastra**: Wprowadź nazwę istniejącego klastra HDInsight.
   * **Rozmiar węzła krawędzi**: Wybierz jeden z rozmiarów maszyn wirtualnych. Rozmiar maszyny wirtualnej musi spełniać wymagania rozmiar maszyny wirtualnej węzła procesu roboczego. Rozmiary maszyn wirtualnych węzła zalecane procesu roboczego, zobacz [klastrów utworzyć Apache Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Prefiks węzła krawędzi**: Wartość domyślna to **nowe**.  Nazwa węzła krawędzi przy użyciu wartości domyślnej, jest **nowe edgenode**.  Można dostosować prefiks z portalu. Można również dostosować pełną nazwę z szablonu.

4. Sprawdź **zgodę na warunki i postanowienia, o których wspomniano**, a następnie kliknij przycisk **zakupu** do utworzenia węzła krawędzi.

>[!IMPORTANT]  
> Upewnij się wybrać grupę zasobów platformy Azure do istniejącego klastra HDInsight.  W przeciwnym razie zostanie wyświetlony komunikat o błędzie "nie można wykonać żądanej operacji dla zasobu zagnieżdżonego. Zasób nadrzędny "&lt;Nazwa_klastra >' nie znaleziono."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Dodaj węzeł krawędzi, podczas tworzenia klastra
W tej sekcji użyjesz szablon usługi Resource Manager do utworzenia klastra HDInsight z węzłem krawędzi.  Szablon usługi Resource Manager można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Szablon usługi Resource Manager wymaga akcji skryptu, znajdujący się w https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Skrypt nie wykonuje żadnych akcji.  Jest aby zademonstrować wywoływania akcji skryptu za pomocą szablonu usługi Resource Manager.

**Aby utworzyć klaster usługi HDInsight z węzłem krawędzi**

1. Jeśli nie masz jeszcze, należy utworzyć klaster usługi HDInsight.  Zobacz [rozpoczęcie korzystania z usługi Hadoop w HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon usługi Azure Resource Manager w witrynie Azure portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Skonfiguruj następujące właściwości:
   
   * **Subskrypcja**: Wybierz subskrypcję platformy Azure używane do tworzenia klastra.
   * **Grupa zasobów**: Utwórz nową grupę zasobów klastra.
   * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów.
   * **Nazwa klastra**: Wprowadź nazwę dla nowego klastra utworzyć.
   * **Nazwa użytkownika logowania klastra**: Wprowadź nazwę użytkownika Hadoop HTTP.  Nazwa domyślna to **admin**.
   * **Hasło logowania klastra**: Wprowadź hasło użytkownika HTTP usługi Hadoop.
   * **Nazwa użytkownika SSH**: Wprowadź nazwę użytkownika SSH. Nazwa domyślna to **sshuser**.
   * **SSH hasła**: Wprowadź hasło użytkownika SSH.
   * **Zainstaluj akcji skryptu**: Zachowaj wartość domyślną dla pośrednictwa w tym artykule.
     
     Niektóre właściwości zostały zapisane na stałe w szablonie: Typ klastra, liczby węzłów procesu roboczego klastra, rozmiar węzła krawędzi i nazwa węzła krawędzi.
4. Sprawdź **zgodę na warunki i postanowienia, o których wspomniano**, a następnie kliknij przycisk **zakupu** do utworzenia klastra z węzłem krawędzi.

## <a name="add-multiple-edge-nodes"></a>Dodawanie wielu węzłów brzegowych

Wiele węzłów krawędzi można dodać do klastra usługi HDInsight.  Konfiguracja wielu węzłów krawędzi jest możliwe tylko przy użyciu szablonów usługi Azure Resource Manager.  Zobacz przykładowy szablon na początku tego artykułu.  Należy zaktualizować **targetInstanceCount** do odzwierciedlają liczbę węzłów brzegowych, które chcesz utworzyć.

## <a name="access-an-edge-node"></a>Dostęp do węzła krawędzi
W węźle brzegowym ssh punkt końcowy jest &lt;EdgeNodeName >.&lt; Nazwa_klastra >-ssh.azurehdinsight.net:22.  Na przykład nowy edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

W węźle brzegowym pojawia się jako aplikację w witrynie Azure portal.  Portal zapewnia informacje, które mają dostęp do węzła krawędzi przy użyciu protokołu SSH.

**Aby sprawdzić, punkt końcowy SSH dla węzła krawędzi**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz węzeł krawędzi klastra HDInsight.
3. Kliknij przycisk **aplikacji**. Zostanie wyświetlona w węźle brzegowym.  Nazwa domyślna to **nowe edgenode**.
4. Kliknij węzeł krawędzi. Zostanie wyświetlona punkt końcowy SSH.

**Aby korzystanie z programu Hive w węźle krawędzi**

1. Połącz się z węzłem brzegowym za pomocą protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po nawiązaniu połączenia z węzłem krawędzi za pomocą protokołu SSH, użyj następującego polecenia, aby otworzyć konsolę Hive:
   
        hive
3. Uruchom następujące polecenie, aby wyświetlić tabele programu Hive w klastrze:
   
        show tables;

## <a name="delete-an-edge-node"></a>Usuń węzeł krawędzi
W witrynie Azure portal, można usunąć węzła krawędzi.

**Aby dostęp do węzła krawędzi**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz węzeł krawędzi klastra HDInsight.
3. Kliknij przycisk **aplikacji**. Zobaczysz listę węzłów krawędzi.  
4. Kliknij prawym przyciskiem myszy węzła krawędzi, które chcesz usunąć, a następnie kliknij przycisk **Usuń**.
5. Kliknij przycisk **Tak**, aby potwierdzić.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule wiesz, jak dodać węzeł krawędzi oraz sposób dostępu do węzła krawędzi. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Instalowanie aplikacji HDInsight](hdinsight-apps-install-applications.md): Dowiedz się, jak instalowanie aplikacji usługi HDInsight do klastrów.
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowane aplikację HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji HDInsight](hdinsight-apps-publish-applications.md): Dowiedz się, jak opublikować niestandardowe aplikacje HDInsight w portalu Azure Marketplace.
* [MSDN: Instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Dowiedz się, jak zdefiniować aplikacje HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów opartych na systemie Linux Apache Hadoop w HDInsight przy użyciu szablonów usługi Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Dowiedz się, jak wywoływać szablony usługi Resource Manager w celu tworzenia klastrów HDInsight.


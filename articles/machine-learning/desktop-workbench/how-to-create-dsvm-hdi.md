---
title: Jak utworzyć maszyny wirtualnej DSVM i HDI jako celów obliczeń dla usługi Azure ML
description: Tworzenie klastra DSVM i HDI Spark jako celów obliczeń dla eksperymentów usługi uczenie Maszynowe Azure.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 60abe46670353121ad308f8926a7ee178c76e74e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951665"
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Tworzenie klastra DSVM i HDI Spark jako celów obliczeń

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Możesz łatwo skalować w górę lub skalowania w poziomie eksperymentu usługi machine learning, dodając cele dodatkowe zasoby obliczeniowe, takie jak DSVM oparta na systemie Ubuntu (maszyna wirtualna do nauki o danych) i Apache Spark dla klastra usługi Azure HDInsight. Przeszukania tego artykułu Cię przez kroki tworzenia tych celów na platformie Azure obliczeń. Aby uzyskać więcej informacji na temat usługi Azure ML obliczeniowych elementów docelowych, zobacz [Przegląd usługi eksperymentowanie w usłudze Azure Machine Learning](experimentation-service-configuration.md).

>[!NOTE]
>Należy upewnić się, masz odpowiednie uprawnienia do tworzenia zasobów, takich jak klastry usługi HDI i maszyny Wirtualnej na platformie Azure, przed kontynuowaniem. Ponadto oba te zasoby mogą wykorzystywać wiele rdzeni obliczeniowych, w zależności od konfiguracji. Upewnij się, że Twoja subskrypcja ma za małą pojemność wirtualnego rdzeni procesora CPU. Użytkownik może zawsze skontaktuj się z pomocy technicznej platformy Azure zwiększyć maksymalną liczbę rdzeni dozwoloną w ramach subskrypcji.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Tworzenie maszyny wirtualnej DSVM z systemem Ubuntu w witrynie Azure portal

Można utworzyć maszyny wirtualnej DSVM z witryny Azure portal. 

1. Zaloguj się do witryny Azure portal z https://portal.azure.com
2. Kliknij pozycję **+ nowy** łącze i wyszukaj "maszyny wirtualnej analizy danych dla systemu Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Wybierz **maszyna wirtualna do nauki o danych dla systemu Linux (Ubuntu)** na liście, a następnie postępuj zgodnie z wyświetlanymi na ekranie instrukcjami w celu utworzenia maszyny DSVM.

>[!IMPORTANT]
>Upewnij się, że wybrano **hasło** jako _typ uwierzytelniania_.

![Użyj pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Tworzenie maszyny wirtualnej DSVM Ubuntu przy użyciu wiersza polecenia platformy azure

Można również użyć szablonu usługi Azure resource management do wdrożenia maszyny wirtualnej DSVM.

>[!NOTE]
>Przyjęto założenie, że wszystkie poniższe polecenia są wydawane w folderze głównym projektu usługi uczenie Maszynowe Azure.

Najpierw utwórz `mydsvm.json` plików za pomocą ulubionego edytora tekstu w `docs` folderu. (Jeśli nie masz `docs` utworzyć folder w folderze głównym projektu.) Używamy ten plik do skonfigurowania niektóre podstawowe parametry szablonu usługi Azure resource management. 

Skopiuj i wklej poniższy fragment kodu JSON do `mydsvm.json` pliku, a następnie wypełnij odpowiednie wartości:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Dla _vmSize_ pola, można użyć dowolnego rozmiaru maszyny Wirtualnej suppported na liście [szablonu administracyjnego zasobów Ubuntu DSVM Azure](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Firma Microsoft zaleca, użyj jednej z poniższych rozmiarach, zgodnie z celów obliczeń dla usługi Azure ML. 


>[!TIP]
> Dla [głębokiego uczenia obciążeń](how-to-use-gpu.md) można wdrożyć na GPU obsługiwane maszyny wirtualne.

- [Maszyny wirtualne ogólnego przeznaczenia](../../virtual-machines/linux/sizes-general.md)
  - Standardowa_DS2_v2 
  - Standardowa_DS3_v2 
  - Standardowa_DS4_v2 
  - Standardowa_DS12_v2 
  - Standardowa_DS13_v2 
  - Standardowa_DS14_v2 
- [Procesor GPU obsługiwane maszyny wirtualne](../../virtual-machines/linux/sizes-gpu.md)
  - Standardowa_NC6 
  - Standardowa_NC12 
  - Standardowa_NC24 
 

Dowiedz się więcej o tych [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../../virtual-machines/linux/sizes.md) i ich [informacje o cenach](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Uruchom okno interfejsu wiersza polecenia z aplikacji Azure ML Workbench, klikając **pliku** --> **Otwórz wiersz polecenia**, lub **Otwórz program PowerShell** elementu menu. 

>[!NOTE]
>Można to również zrobić w dowolnym środowisku wiersza polecenia, w którym masz az-zainstalować interfejs wiersza polecenia.

W oknie wiersza polecenia, wpisz poniższe polecenia:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Dołączanie maszyny wirtualnej DSVM obliczeniowego elementu docelowego
Po utworzeniu maszyny DSVM można teraz dołączać je do projektu usługi uczenie Maszynowe Azure.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Teraz powinno być gotowe do uruchamiania eksperymentów na tej maszyny wirtualnej DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Cofanie przydziału maszyny wirtualnej DSVM i uruchomić ponownie później
Po ukończeniu zadań obliczeniowych, przy użyciu usługi Azure ML można cofnąć alokacji maszyny DSVM. Ta akcja wyłącza maszynę Wirtualną, zwalnia zasoby obliczeniowe, ale zachowanie dysków wirtualnych. Opłaty nie są naliczane za koszt obliczeń, gdy jest cofnięty.

Aby cofnąć przydział maszyny Wirtualnej:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Aby przywrócić maszynę Wirtualną do użytkowania, należy użyć `az ml start` polecenia:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Rozszerzanie dysku systemu operacyjnego maszyny wirtualnej DSVM
Ubuntu maszyny wirtualnej DSVM jest dostarczany z dysku systemu operacyjnego 50GB i 100GB danych. Docker obrazy są przechowywane na dysku danych, więcej miejsca jest dostępna. Gdy jest używana jako cel obliczenia dla usługi Azure ML, ten dysk można zużyte przez aparat platformy Docker, przeciągnij w dół obrazów platformy Docker i tworzenia warstwy conda na ich podstawie. Może być konieczne rozszerzanie dysku na większy rozmiar (na przykład 200 GB), aby uniknąć tego błędu "dysk zapełniony:", gdy jesteś w trakcie wykonywania. Odwołanie [sposobu rozszerzania wirtualnych dysków twardych na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](../../virtual-machines/linux/expand-disks.md) dowiesz się, jak można to łatwo zrobić z wiersza polecenia platformy azure. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Utwórz Apache Spark dla klastra usługi Azure HDInsight w witrynie Azure portal

Uruchamianie zadań Spark skalowalnego w poziomie, musisz utworzyć Apache Spark dla klastra usługi Azure HDInsight w witrynie Azure portal.

1. Zaloguj się do witryny Azure portal z https://portal.azure.com
2. Kliknij pozycję **+ nowy** łącze i wyszukaj "HDInsight".

    ![Znajdowanie usługi hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Wybierz **HDInsight** w listy, a następnie kliknąć **Utwórz** przycisku.
4. W **podstawy** ekran konfiguracji **typ klastra** ustawienia, upewnij się, możesz wybrać **Spark** jako _typ klastra_, **Linux** jako _systemu operacyjnego_, i **Spark 2.1.0 (HDI 3.6)** jako _wersji.

    ![Konfigurowanie usługi hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Zwróć uwagę na ekranie powyżej klaster ma _nazwa użytkownika logowania klastra_ pola i _Secure Shell (SSH), username_ pola. Są to dwa różne tożsamości użytkownika, nawet jeśli dla wygody możesz określić to samo hasło do logowania zarówno do. _Nazwa użytkownika logowania klastra_ jest używana do logowania się w usłudze zarządzania interfejsu użytkownika sieci web klastra usługi HDI. _Nazwa użytkownika logowania SSH_ jest używana do logowania się z węzłem głównym klastra, i jest to, co jest potrzebne do usługi Azure ML do wysyłania zadań platformy Spark.

5. Wybierz rozmiar klastra i rozmiar węzłów, wymagane i Zakończ pracę Kreatora tworzenia. Może potrwać do 30 minut, aby klaster mógł zakończyć aprowizację. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Dołączanie usługi HDI Spark klastra obliczeniowego elementu docelowego

Po utworzeniu klastra usługi HDI Spark można teraz dołączać je do projektu usługi uczenie Maszynowe Azure.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Teraz powinno być gotowe do uruchamiania eksperymentów w tym klastrze Spark.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:
- [Omówienie usługi eksperymentowanie w usłudze Azure Machine Learning](experimentation-service-configuration.md)
- [Pliki konfiguracji usługi eksperymentowanie w usłudze Azure Machine Learning Workbench](experimentation-service-configuration-reference.md)
- [Platforma Apache Spark dla klastra usługi Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Maszyna wirtualna do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

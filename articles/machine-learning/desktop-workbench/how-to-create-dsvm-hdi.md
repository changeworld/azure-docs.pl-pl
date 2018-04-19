---
title: "Jak utworzyć DSVM i HDI jako obliczeniowe elementy docelowe dla uczenie Maszynowe Azure"
description: Tworzenie klastra DSVM i HDI Spark jako obliczeniowe elementy docelowe eksperymenty uczenie Maszynowe Azure.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 15cdee0fb3994874c88b16bebec35f5eae9f8de2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Tworzenie klastra DSVM i HDI Spark jako obliczeniowe elementy docelowe

Można łatwo skalować lub skalowanie eksperymentu uczenia maszynowego, dodając celów dodatkowe zasoby obliczeniowe takich jak systemem Ubuntu DSVM (maszyna wirtualna nauki danych) i platforma Apache Spark dla klastra usługi HDInsight Azure. Ten artykuł przeszukiwań, obliczeniowe użytkownika przez kroki tworzenia te cele na platformie Azure. Aby uzyskać więcej informacji na obiekty docelowe obliczeń uczenie Maszynowe Azure, zapoznaj się [Omówienie usługi Azure Machine Learning eksperymenty usługi](experimentation-service-configuration.md).

>[!NOTE]
>Należy się upewnić, masz odpowiednie uprawnienia do tworzenia zasobów, takich jak klastry maszyny Wirtualnej i HDI na platformie Azure, przed kontynuowaniem. Oba te zasoby również używać wielu rdzeni obliczeń, w zależności od konfiguracji. Upewnij się, że subskrypcja ma za mało pojemność wirtualnego rdzeni procesora CPU. Można zawsze skontaktuj się ze pomocy technicznej platformy Azure, aby zwiększyć maksymalną liczbę rdzeni dozwolona w Twojej subskrypcji.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Utwórz DSVM Ubuntu w portalu Azure

Można utworzyć DSVM z portalu Azure. 

1. Zaloguj się do portalu Azure z https://portal.azure.com
2. Polecenie **+ nowy** łącza i wyszukaj "data nauki maszyny wirtualnej dla systemu Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Wybierz **danych maszyny wirtualnej nauki dla systemu Linux (Ubuntu)** na liście i wykonaj wyświetlane na ekranie instrukcjami, aby utworzyć DSVM.

>[!IMPORTANT]
>Upewnij się, że możesz wybrać **hasło** jako _typ uwierzytelniania_.

![Użyj pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Utwórz DSVM Ubuntu przy użyciu wiersza polecenia platformy azure

Szablon administracyjny zasobów platformy Azure można także użyć do wdrażania DSVM.

>[!NOTE]
>Wszystkie poniższe polecenia są uznawane za wydaje się w folderze głównym projektu uczenie Maszynowe Azure.

Najpierw utwórz `mydsvm.json` plik za pomocą edytora tekstu w `docs` folderu. (Jeśli nie masz `docs` utworzyć folder w folderze głównym projektu.) Używamy ten plik do skonfigurowania niektórych podstawowych parametrów szablonu administracyjnego zasobów platformy Azure. 

Skopiuj i wklej poniższy fragment kodu JSON do `mydsvm.json` pliku, a następnie wprowadź odpowiednie wartości:

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

Dla _vmSize_ pola, można użyć dowolnego suppported rozmiaru maszyny Wirtualnej na liście [szablonu administracyjnego zasobów Ubuntu DSVM Azure](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Firma Microsoft zaleca, użyj jednej z poniższych rozmiary jako obliczeniowe elementy docelowe dla uczenie Maszynowe Azure. 


>[!TIP]
> Dla [głębokie uczenia obciążeń](how-to-use-gpu.md) można wdrożyć do procesora GPU zasilane maszyn wirtualnych.

- [Maszyny wirtualne ogólnego przeznaczenia](/virtual-machines/linux/sizes-general.md)
  - Standardowa_DS2_v2 
  - Standardowa_DS3_v2 
  - Standardowa_DS4_v2 
  - Standardowa_DS12_v2 
  - Standardowa_DS13_v2 
  - Standardowa_DS14_v2 
- [Procesor GPU obsługiwane maszyny wirtualne](/virtual-machines/linux/sizes-gpu.md)
  - Standardowa_NC6 
  - Standardowa_NC12 
  - Standardowa_NC24 
 

Dowiedz się więcej o tych [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../../virtual-machines/linux/sizes.md) i ich [uzyskać informacje o cenach](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Uruchom okno interfejsu wiersza polecenia z aplikacji Azure ML Workbench, klikając **pliku** --> **Otwórz okno wiersza polecenia**, lub **Otwórz program PowerShell** elementu menu. 

>[!NOTE]
>Można również w tym w każdym środowisku wiersza polecenia, gdzie masz zainstalowany az-cli.

W oknie wiersza polecenia, wpisz poniżej polecenia:

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
## <a name="attach-a-dsvm-compute-target"></a>Dołącz docelowy obliczeń DSVM
Po utworzeniu DSVM, aby teraz podłączyć go do projektu uczenie Maszynowe Azure.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Teraz możesz przystąpić do uruchomienia eksperymenty w tym DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Cofnięcie przydziału DSVM i uruchomić ponownie później
Po zakończeniu zadania obliczeń z uczenie Maszynowe Azure można cofnąć alokacji DSVM. Zamknięcie maszyny Wirtualnej, zwalnia zasoby obliczeniowe, ale zachowanie dysków wirtualnych. Użytkownik nie są naliczane kosztów obliczeniowe po cofnięciu przydziału maszyny Wirtualnej.

Można cofnąć alokacji maszyny Wirtualnej:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Aby przywrócić maszynę Wirtualną do użytkowania, należy użyć `az ml start` polecenia:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Zwiększ rozmiar dysku systemu operacyjnego DSVM
Maszyny Wirtualnej systemu Linux na platformie Azure zazwyczaj pochodzą z dyskiem systemu operacyjnego 30 GB. Gdy jest używany jako obliczeniowe docelowy dla uczenie Maszynowe Azure, go może być spożywany szybko przez aparat Docker ściąganie dół obrazy usługi Docker i tworzenia warstwy conda na nim. Należy dobrze, aby rozszerzyć dysk systemu operacyjnego na większy rozmiar (na przykład 200 GB) w celu uniknięcia "Pełna" błędów podczas pracy w trakcie wykonywania. Odwołanie [sposobu rozszerzania wirtualne dyski twarde na maszynę Wirtualną systemu Linux za pomocą interfejsu wiersza polecenia Azure](../../virtual-machines/linux/expand-disks.md) Aby dowiedzieć się, jak to łatwo zrobić z wiersza polecenia platformy azure. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Utwórz Apache Spark dla klastra usługi HDInsight Azure w portalu Azure

Do uruchomienia zadań Spark skalowalnego w poziomie, musisz utworzyć Apache Spark dla klastra usługi HDInsight Azure w portalu Azure.

1. Zaloguj się do portalu Azure z https://portal.azure.com
2. Polecenie **+ nowy** łącza i poszukaj pozycji "HDInsight".

    ![Znajdź hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Wybierz **HDInsight** listy, a następnie kliknij na **Utwórz** przycisku.
4. W **podstawy** ekranie konfiguracji **typ klastra** ustawienia, upewnij się, że możesz wybrać **Spark** jako _typ klastra_, **Linux** jako _systemu operacyjnego_, i **Spark 2.1.0 (HDI 3,6)** jako _Version.

    ![Skonfiguruj hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Powiadomienia na ekranie powyżej klaster ma _nazwa użytkownika logowania klastra_ pola i _username Secure Shell (SSH)_ pola. Są to tożsamości dwóch różnych użytkowników, nawet jeśli dla wygody można określić to samo hasło do logowania zarówno do. _Nazwa użytkownika logowania klastra_ służy do logowania do interfejsu użytkownika klastra HDI sieci web zarządzania. _Nazwa użytkownika logowania SSH_ służy do logowania do węzła głównego klastra, a jest to, co jest potrzebne do uczenie Maszynowe Azure do wysyłania zadań Spark.

5. Wybierz rozmiar klastra i rozmiaru węzła muszą i Zakończ pracę Kreatora tworzenia. Może potrwać do 30 minut, zanim klaster, aby zakończyć inicjowanie obsługi. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Dołącz obiekt docelowy HDI Spark klastra obliczeniowego

Po utworzeniu klastra Spark HDI, aby teraz podłączyć go do projektu uczenie Maszynowe Azure.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Teraz możesz przystąpić do uruchomienia eksperymenty w tym klastrze Spark.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o:
- [Omówienie usługi Azure Machine Learning eksperymenty usługi](experimentation-service-configuration.md)
- [Pliki konfiguracji usługi Azure Machine Learning Workbench eksperymenty](experimentation-service-configuration-reference.md)
- [Platforma Apache Spark w usłudze Azure HDInsight klastra](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Maszyna wirtualna nauki danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

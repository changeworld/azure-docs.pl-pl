---
title: Instalowanie bazy danych MongoDB na maszynie Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować bazy danych MongoDB na iusing maszyny wirtualnej systemu Linux wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 5fadf23cc1fc2e1a6092c48033580d398fc689a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542803"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Jak zainstalować i skonfigurować bazy danych MongoDB na maszynie Wirtualnej systemu Linux

[Bazy danych MongoDB](https://www.mongodb.org) to popularne typu open source, wysokiej wydajności baza danych NoSQL. W tym artykule przedstawiono sposób instalowania i konfigurowania bazy danych MongoDB na maszynie Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure. Przykłady są wyświetlane szczegóły tego jak do:

* [Ręcznie zainstaluj i skonfiguruj podstawowe wystąpienie bazy danych MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Tworzenie podstawowego wystąpienia bazy danych MongoDB przy użyciu szablonu usługi Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Tworzenie złożonych bazy danych MongoDB podzielonej na fragmenty klastra przy użyciu repliki ustawia przy użyciu szablonu usługi Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ręcznie zainstaluj i skonfiguruj bazy danych MongoDB na maszynie Wirtualnej
Bazy danych MongoDB [zapewniają instrukcje dotyczące instalacji](https://docs.mongodb.com/manual/administration/install-on-linux/) dla dystrybucje systemu Linux, w tym Red Hat / CentOS, SUSE, Ubuntu i Debian. Poniższy przykład tworzy *CentOS* maszyny Wirtualnej. Do utworzenia tego środowiska, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* użytkownikowi o nazwie *azureuser* przy użyciu uwierzytelniania klucza publicznego SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną Wirtualną przy użyciu swoją nazwę użytkownika i `publicIpAddress` wymienionych w danych wyjściowych z poprzedniego kroku:

```bash
ssh azureuser@<publicIpAddress>
```

Aby dodać źródła instalacji dla bazy danych MongoDB, Utwórz **yum** plik repozytorium w następujący sposób:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otwórz plik repozytorium bazy danych MongoDB do edycji, takie jak za pomocą `vi` lub `nano`. Dodaj następujące wiersze:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Instalowanie bazy danych MongoDB przy użyciu **yum** w następujący sposób:

```bash
sudo yum install -y mongodb-org
```

Domyślnie SELinux są wymuszane na CentOS obrazy, które uniemożliwia dostęp do bazy danych MongoDB. Zainstaluj narzędzia do zarządzania zasadami i skonfiguruj SELinux, aby umożliwić bazy danych MongoDB do pracy na jej domyślny port TCP 27017 w następujący sposób:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Uruchom usługę bazy danych MongoDB w następujący sposób:

```bash
sudo service mongod start
```

Weryfikacja instalacji bazy danych MongoDB, nawiązując połączenie za pomocą lokalnego `mongo` klienta:

```bash
mongo
```

Teraz przetestuj wystąpienie bazy danych MongoDB, dodając pewne dane, a następnie użyć opcji wyszukiwania:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Jeśli to konieczne, należy skonfigurować bazy danych MongoDB do automatycznego uruchamiania podczas ponownego uruchomienia systemu:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Tworzenie podstawowego wystąpienia bazy danych MongoDB na CentOS przy użyciu szablonu
Podstawowe wystąpienie bazy danych MongoDB można tworzyć na jednej maszynie Wirtualnej CentOS przy użyciu następującego szablonu szybkiego startu platformy Azure z usługi GitHub. Ten szablon używa rozszerzenia niestandardowego skryptu dla systemu Linux, aby dodać **yum** repozytorium do nowo utworzonej maszyny Wirtualnej CentOS i zainstaluj bazy danych MongoDB.

* [Podstawowe wystąpienie bazy danych MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Do utworzenia tego środowiska, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index). Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie Wdróż szablon bazy danych MongoDB z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment). Po wyświetleniu monitu wprowadź swoje własne unikatowe wartości *newStorageAccountName*, *dnsNameForPublicIP*oraz nazwę i hasło administratora:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Zaloguj się do maszyny Wirtualnej przy użyciu publicznego adresu DNS maszyny wirtualnej. Można wyświetlić publiczny adres DNS z [az vm show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH z maszyną Wirtualną przy użyciu swoją nazwę użytkownika i publicznego adresu DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Weryfikacja instalacji bazy danych MongoDB, nawiązując połączenie za pomocą lokalnego `mongo` klienta w następujący sposób:

```bash
mongo
```

Teraz przetestuj wystąpienie przez dodanie niektórych danych i wyszukiwanie w następujący sposób:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Tworzenie złożonych klastra podzielonej na fragmenty bazy danych MongoDB na CentOS przy użyciu szablonu
Można utworzyć złożone klastra podzielonej na fragmenty bazy danych MongoDB przy użyciu następującego szablonu szybkiego startu platformy Azure z usługi GitHub. Ten szablon jest zgodna [najlepsze rozwiązania klastra podzielonej na fragmenty bazy danych MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) celu zapewnienia nadmiarowości i wysokiej dostępności. Ten szablon tworzy dwóch fragmentów, w przypadku użycia trzech węzłów w każdym zestawie repliki. Jedna replika serwera konfiguracji zestawu z trzema węzłami jest tworzona, oraz dwóch **mongos** routera serwerów w celu zapewnienia spójności aplikacji między fragmentami.

* [Klaster fragmentowanie bazy danych MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Wdrożenie tego złożonych klastra podzielonej na fragmenty bazy danych MongoDB wymaga więcej niż 20 rdzeni, który jest zazwyczaj domyślna liczba rdzeni na region na subskrypcję. Otwórz żądanie pomocy technicznej platformy Azure, aby zwiększyć swoje liczba rdzeni.

Do utworzenia tego środowiska, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index). Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie Wdróż szablon bazy danych MongoDB z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment). Definiowanie własnych zasobów nazw i rozmiarach, w razie potrzeby, takie jak w przypadku *mongoAdminUsername*, *sizeOfDataDiskInGB*, i *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

To wdrożenie może potrwać ponad godzinę można wdrożyć i skonfigurować wszystkie wystąpienia maszyn wirtualnych. `--no-wait` Flaga jest używana na końcu poprzedniego polecenia, aby powrócić do wiersza polecenia kontroli, po wdrożeniu szablonu zostało zaakceptowane przez platformę Azure. Można wyświetlić stan wdrożenia za pomocą [az grupy wdrożenia show](/cli/azure/group/deployment). Poniższy przykład wyświetla stan *myMongoDBCluster* wdrożenia w *myResourceGroup* grupy zasobów:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Kolejne kroki
W tych przykładach połączysz się z wystąpieniem bazy danych MongoDB lokalnie z maszyny Wirtualnej. Jeśli chcesz nawiązać połączenie z wystąpieniem bazy danych MongoDB z innej maszyny Wirtualnej lub sieci, upewnij się, odpowiednie [reguły sieciowej grupy zabezpieczeń są tworzone](nsg-quickstart.md).

Te przykłady wdrażania podstawowym środowiskiem bazy danych MongoDB do celów programistycznych. Zastosowanie opcji konfiguracji zabezpieczeń wymagane w danym środowisku. Aby uzyskać więcej informacji, zobacz [docs zabezpieczeń bazy danych MongoDB](https://docs.mongodb.com/manual/security/).

Aby uzyskać więcej informacji na temat tworzenia przy użyciu szablonów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Szablony usługi Azure Resource Manager użyj rozszerzenia niestandardowego skryptu, aby pobrać i uruchomić skrypty na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [za pomocą niestandardowego rozszerzenia skryptu platformy Azure przy użyciu maszyn wirtualnych systemu Linux](extensions-customscript.md).


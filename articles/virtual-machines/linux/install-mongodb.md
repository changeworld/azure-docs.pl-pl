---
title: Instalowanie bazy danych MongoDB na maszynę Wirtualną systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania i konfigurowania bazy danych MongoDB na iusing maszyny wirtualnej systemu Linux 2.0 interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: a47c0e2f655f51444dc586f696c26caa63ab6cac
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937586"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Jak zainstalować i skonfigurować bazy danych MongoDB na Maszynę wirtualną systemu Linux
[Bazy danych MongoDB](http://www.mongodb.org) jest popularnych open source, wysokiej wydajności bazę danych NoSQL. W tym artykule przedstawiono sposób instalowania i konfigurowania bazy danych MongoDB na Maszynę wirtualną systemu Linux 2.0 interfejsu wiersza polecenia platformy Azure. Przykłady są wyświetlane szczegóły tego jak do:

* [Ręcznie zainstaluj i skonfiguruj podstawowe wystąpienie bazy danych MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Utwórz podstawowe wystąpienie bazy danych MongoDB przy użyciu szablonu usługi Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Tworzenie złożonych bazy danych MongoDB ustawia podzielonej klaster z repliki przy użyciu szablonu usługi Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ręcznie zainstaluj i skonfiguruj bazy danych MongoDB na maszynie Wirtualnej
Bazy danych MongoDB [zawierają instrukcje instalacji](https://docs.mongodb.com/manual/administration/install-on-linux/) dla dystrybucjach systemu Linux, łącznie z Red Hat / CentOS, SUSE, Ubuntu i Debian. Poniższy przykład tworzy *CentOS* maszyny Wirtualnej. Do utworzenia tego środowiska, należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* z użytkownikiem o nazwie *azureuser* przy użyciu uwierzytelniania klucza publicznego SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną Wirtualną przy użyciu własnego nazwy użytkownika i `publicIpAddress` dane wyjściowe z poprzedniego kroku na liście:

```bash
ssh azureuser@<publicIpAddress>
```

Aby dodać źródła instalacji bazy danych mongodb, Utwórz **yum** plik repozytorium w następujący sposób:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otwórz plik repozytorium bazy danych MongoDB do edycji, takie jak z `vi` lub `nano`. Dodaj następujące wiersze:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Instalowanie przy użyciu bazy danych MongoDB **yum** w następujący sposób:

```bash
sudo yum install -y mongodb-org
```

Domyślnie SELinux są wymuszane na obrazy CentOS, które uniemożliwiają dostęp do bazy danych MongoDB. Zainstaluj narzędzia do zarządzania zasadami i skonfiguruj SELinux, aby umożliwić bazy danych MongoDB do działania na jego domyślny port TCP 27017 w następujący sposób:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Uruchom usługę bazy danych MongoDB w następujący sposób:

```bash
sudo service mongod start
```

Sprawdź instalacji bazy danych MongoDB, nawiązując połączenie przy użyciu lokalnej `mongo` klienta:

```bash
mongo
```

Teraz testu wystąpienie bazy danych MongoDB, dodając niektórych danych, a następnie wyszukiwania:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

W razie potrzeby można skonfigurować bazy danych MongoDB do automatycznego uruchamiania podczas ponownego uruchomienia systemu:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Utwórz podstawowe wystąpienie bazy danych MongoDB na CentOS przy użyciu szablonu
Podstawowe wystąpienie bazy danych MongoDB można tworzyć na jednej maszyny Wirtualnej CentOS przy użyciu następującego szablonu Szybki Start Azure z usługi GitHub. Ten szablon używa rozszerzenia niestandardowego skryptu dla systemu Linux, aby dodać **yum** repozytorium do nowo utworzonej maszyny Wirtualnej CentOS i zainstaluj bazy danych MongoDB.

* [Podstawowe wystąpienie bazy danych MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Do utworzenia tego środowiska, należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie należy wdrożyć szablon bazy danych MongoDB z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create). Po wyświetleniu monitu wprowadź unikatowe wartości dla *newStorageAccountName*, *dnsNameForPublicIP*, a nazwa użytkownika i hasło:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Zaloguj się do maszyny Wirtualnej przy użyciu publicznego adresu DNS w sieci maszyny wirtualnej. Można wyświetlić publicznego adresu DNS z [az maszyny wirtualnej pokazu](/cli/azure/vm#az_vm_show):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH do maszyny Wirtualnej przy użyciu własnej nazwy użytkownika i publicznego adresu DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Sprawdź instalacji bazy danych MongoDB, nawiązując połączenie przy użyciu lokalnej `mongo` klienta w następujący sposób:

```bash
mongo
```

Teraz testu wystąpienie, dodając niektóre dane i wyszukiwanie w następujący sposób:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Tworzenie złożonych bazy danych MongoDB klastra podzielonej na CentOS przy użyciu szablonu
Można utworzyć klastra złożonych podzielonej bazy danych MongoDB, korzystając z poniższego szablonu Szybki Start Azure z usługi GitHub. Ten szablon jest zgodny [MongoDB podzielonej klastra najlepszych rozwiązań](https://docs.mongodb.com/manual/core/sharded-cluster-components/) aby zapewnić nadmiarowość i wysokiej dostępności. Szablon tworzy dwa niezależne z trzech węzłów każdego zestawu replik. Jedna replika serwera konfiguracji ustawiony za pomocą trzech węzłów tworzona jest również, oraz dwa **mongos** serwerów w celu zapewnienia spójności aplikacji z całej fragmentów.

* [Klaster dzielenia na fragmenty bazy danych MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Wdrażanie tego złożonych klastra podzielonej bazy danych MongoDB wymaga więcej niż 20 rdzenie, co jest typowe domyślnej liczby rdzeni na region na subskrypcję. Otwórz żądanie pomocy technicznej platformy Azure do zwiększenia Twojej liczby rdzeni.

Do utworzenia tego środowiska, należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie należy wdrożyć szablon bazy danych MongoDB z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create). Definiowanie własnych zasobów nazwy i rozmiar w razie potrzeby, takie jak w przypadku *mongoAdminUsername*, *sizeOfDataDiskInGB*, i *configNodeVmSize*:

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

To wdrożenie może zająć ponad godzinę do wdrożenia i skonfigurowania wszystkich wystąpień maszyn wirtualnych. `--no-wait` Flaga jest wykorzystywana na końcu poprzedniego polecenia, aby zwrócić kontrolkę do wiersza polecenia, gdy wdrożenie szablonu została zaakceptowana przez platformę Azure. Można wyświetlić stan wdrożenia z [Pokaż wdrożenia grupy az](/cli/azure/group/deployment#az_group_deployment_show). Poniższy przykład wyświetla stan *myMongoDBCluster* wdrożenia w *myResourceGroup* grupy zasobów:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Kolejne kroki
W tych przykładach łączysz się z wystąpieniem bazy danych MongoDB lokalnie z maszyny Wirtualnej. Jeśli chcesz połączyć się z wystąpieniem bazy danych MongoDB z innej maszyny Wirtualnej lub sieci, upewnij się, odpowiedni [reguł sieciowej grupy zabezpieczeń są tworzone](nsg-quickstart.md).

Te przykłady wdrażania środowiska podstawowej bazy danych MongoDB do celów programistycznych. Zastosowanie opcji konfiguracji zabezpieczeń jest wymagana dla danego środowiska. Aby uzyskać więcej informacji, zobacz [docs zabezpieczeń bazy danych MongoDB](https://docs.mongodb.com/manual/security/).

Aby uzyskać więcej informacji na temat tworzenia za pomocą szablonów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Szablony usługi Azure Resource Manager Użyj niestandardowe rozszerzenie skryptu do pobierania i wykonywania skryptów na maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [z maszyn wirtualnych systemu Linux przy użyciu niestandardowe rozszerzenie skryptu Azure](extensions-customscript.md).


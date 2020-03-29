---
title: Instalowanie mongodb na maszynie wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak zainstalować i skonfigurować mongodb na maszynie wirtualnej systemu Linux ius a Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: e1bc7c8a6f97d6dc6bb1d6cb54825425244b2158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944892"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Jak zainstalować i skonfigurować MongoDB na maszynie Wirtualnej z systemem Linux

[MongoDB](https://www.mongodb.org) jest popularną bazą danych NoSQL typu open source o wysokiej wydajności. W tym artykule pokazano, jak zainstalować i skonfigurować mongodb na maszynie wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure. Pokazano przykłady, które szczegółowo opisują sposób:

* [Ręczne instalowanie i konfigurowanie podstawowego wystąpienia mongodb](#manually-install-and-configure-mongodb-on-a-vm)
* [Tworzenie podstawowego wystąpienia bazy danych MongoDB przy użyciu szablonu Menedżera zasobów](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Tworzenie złożonego klastra podzielonego na fragmenty usługi MongoDB z zestawami replik przy użyciu szablonu Menedżera zasobów](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ręczne instalowanie i konfigurowanie mongodb na maszynie wirtualnej
MongoDB [dostarcza instrukcje instalacji](https://docs.mongodb.com/manual/administration/install-on-linux/) dla dystrybucji Linuksa, w tym Red Hat / CentOS, SUSE, Ubuntu i Debian. Poniższy przykład tworzy maszynę wirtualną *CentOS.* Aby utworzyć to środowisko, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z użytkownikiem o nazwie *azureuser* przy użyciu uwierzytelniania klucza publicznego SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH do maszyny Wirtualnej przy `publicIpAddress` użyciu własnej nazwy użytkownika i wymienione w danych wyjściowych z poprzedniego kroku:

```bash
ssh azureuser@<publicIpAddress>
```

Aby dodać źródła instalacji dla MongoDB, utwórz plik repozytorium **yum** w następujący sposób:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Otwórz plik repozytorium MongoDB do `vi` edycji, na przykład z lub `nano`. Dodaj następujące wiersze:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Zainstaluj MongoDB za pomocą **mniam w** następujący sposób:

```bash
sudo yum install -y mongodb-org
```

Domyślnie SELinux jest wymuszany na obrazach CentOS, co uniemożliwia dostęp do MongoDB. Zainstaluj narzędzia do zarządzania zasadami i skonfiguruj SELinux, aby umożliwić MongoDB działanie na domyślnym porcie TCP 27017 w następujący sposób:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Uruchom usługę MongoDB w następujący sposób:

```bash
sudo service mongod start
```

Sprawdź instalację mongodb, łącząc `mongo` się przy użyciu klienta lokalnego:

```bash
mongo
```

Teraz przetestuj wystąpienie MongoDB, dodając kilka danych, a następnie wyszukując:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

W razie potrzeby skonfiguruj mongodb tak, aby uruchamiała się automatycznie podczas ponownego uruchamiania systemu:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Tworzenie podstawowego wystąpienia MongoDB w centos przy użyciu szablonu
Podstawowe wystąpienie mongodb można utworzyć na jednej maszynie Wirtualnej CentOS przy użyciu następującego szablonu szybkiego startu platformy Azure z usługi GitHub. Ten szablon używa rozszerzenia Skrypt niestandardowy dla Linuksa, aby dodać repozytorium **yum** do nowo utworzonej maszyny wirtualnej CentOS, a następnie zainstalować MongoDB.

* [Podstawowe wystąpienie MongoDB w centos](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Aby utworzyć to środowisko, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index) Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie wdrożyć szablon MongoDB z [az wdrożenia grupy utworzyć](/cli/azure/group/deployment). Po wyświetleniu monitu wprowadź własne unikatowe wartości dla *newStorageAccountName*, *dnsNameForPublicIP*i nazwy użytkownika i hasła administratora:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Zaloguj się do maszyny Wirtualnej przy użyciu publicznego adresu DNS maszyny Wirtualnej. Publiczny adres DNS można wyświetlić za pomocą [programu az vm show:](/cli/azure/vm)

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH do maszyny Wirtualnej przy użyciu własnej nazwy użytkownika i publicznego adresu DNS:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Sprawdź instalację mongodb, łącząc `mongo` się przy użyciu klienta lokalnego w następujący sposób:

```bash
mongo
```

Teraz przetestuj wystąpienie, dodając niektóre dane i wyszukując w następujący sposób:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Tworzenie złożonego klastra podzielonego mongodb na centos przy użyciu szablonu
Można utworzyć złożony klaster podzielony na fragmenty usługi MongoDB przy użyciu następującego szablonu szybki start platformy Azure z usługi GitHub. Ten szablon jest zgodny z [najlepszymi rozwiązaniami klastra podzielonego na fragmenty usługi MongoDB,](https://docs.mongodb.com/manual/core/sharded-cluster-components/) aby zapewnić nadmiarowość i wysoką dostępność. Szablon tworzy dwa fragmenty, z trzech węzłów w każdym zestawie replik. Tworzony jest również jeden zestaw replik serwera konfiguracji z trzema węzłami oraz dwa **serwery routera mongos,** aby zapewnić spójność aplikacji z różnych fragmentów.

* [Klaster dzielenia na fragmenty MongoDB w systemie CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Wdrażanie tego złożonego klastra podzielonego na dane i mongodb wymaga więcej niż 20 rdzeni, co jest zazwyczaj domyślną liczbą rdzeni dla regionu dla subskrypcji. Otwórz żądanie pomocy technicznej platformy Azure, aby zwiększyć liczbę podstawowych.

Aby utworzyć to środowisko, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index) Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie wdrożyć szablon MongoDB z [az wdrożenia grupy utworzyć](/cli/azure/group/deployment). Zdefiniuj własne nazwy i rozmiary zasobów w razie potrzeby, takie jak *mongoAdminUsername*, *sizeOfDataDiskInGB*i *configNodeVmSize*:

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

To wdrożenie może potrwać ponad godzinę, aby wdrożyć i skonfigurować wszystkie wystąpienia maszyny Wirtualnej. Flaga `--no-wait` jest używana na końcu poprzedniego polecenia, aby przywrócić kontrolę do wiersza polecenia po zaakceptowaniu wdrożenia szablonu przez platformę Azure. Następnie można wyświetlić stan wdrożenia z [pokazem wdrażania grupy AZ](/cli/azure/group/deployment). W poniższym przykładzie przedstawiono stan wdrożenia *myMongoDBCluster* w grupie zasobów *myResourceGroup:*

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Następne kroki
W tych przykładach można połączyć się z wystąpieniem MongoDB lokalnie z maszyny Wirtualnej. Jeśli chcesz połączyć się z wystąpieniem MongoDB z innej maszyny Wirtualnej lub sieci, upewnij się, że zostaną utworzone odpowiednie [reguły sieciowej grupy zabezpieczeń](nsg-quickstart.md).

Te przykłady wdrożyć podstawowe środowisko MongoDB do celów programisty. Zastosuj wymagane opcje konfiguracji zabezpieczeń dla swojego środowiska. Aby uzyskać więcej informacji, zobacz [dokumenty zabezpieczeń MongoDB](https://docs.mongodb.com/manual/security/).

Aby uzyskać więcej informacji na temat tworzenia przy użyciu szablonów, zobacz [omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Szablony usługi Azure Resource Manager używają rozszerzenia skryptów niestandardowych do pobierania i wykonywania skryptów na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [Korzystanie z rozszerzenia skryptu niestandardowego platformy Azure z maszynami wirtualnymi systemu Linux](extensions-customscript.md).


---
title: Instalowanie bazy danych MongoDB na maszynie Wirtualnej systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania i konfigurowania bazy danych MongoDB na maszynie wirtualnej systemu Linux na platformie Azure przy użyciu modelu wdrażania Menedżera zasobów.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a334a0b7b3b638229c61eef086b1919b4c303338
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Jak zainstalować i skonfigurować bazy danych MongoDB na Maszynę wirtualną systemu Linux przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure
[Bazy danych MongoDB](http://www.mongodb.org) jest popularnych open source, wysokiej wydajności bazę danych NoSQL. W tym artykule przedstawiono sposób instalowania i konfigurowania bazy danych MongoDB na Maszynę wirtualną systemu Linux na platformie Azure przy użyciu modelu wdrażania Menedżera zasobów. Przykłady są wyświetlane szczegóły tego jak do:

* [Ręcznie zainstaluj i skonfiguruj podstawowe wystąpienie bazy danych MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Utwórz podstawowe wystąpienie bazy danych MongoDB przy użyciu szablonu usługi Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Tworzenie złożonych bazy danych MongoDB ustawia podzielonej klaster z repliki przy użyciu szablonu usługi Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- Interfejs wiersza polecenia platformy Azure w wersji 1.0 — nasz interfejs wiersza polecenia dla klasycznego modelu wdrażania i modelu wdrażania na potrzeby zarządzania zasobami (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](create-cli-complete-nodejs.md) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Ręcznie zainstaluj i skonfiguruj bazy danych MongoDB na maszynie Wirtualnej
Bazy danych MongoDB [zawierają instrukcje instalacji](https://docs.mongodb.com/manual/administration/install-on-linux/) dla dystrybucjach systemu Linux, łącznie z Red Hat / CentOS, SUSE, Ubuntu i Debian. Poniższy przykład tworzy *CentOS* maszyny Wirtualnej przy użyciu klucza SSH przechowywane w *~/.ssh/id_rsa.pub*. Odpowiedz monity dla nazwy konta magazynu, nazwa DNS i poświadczenia administratora:

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Zaloguj się do maszyny Wirtualnej przy użyciu publicznego adresu IP wyświetlane na końcu poprzedniego kroku tworzenia maszyny Wirtualnej:

```bash
ssh azureuser@40.78.23.145
```

Aby dodać źródła instalacji bazy danych mongodb, Utwórz **yum** plik repozytorium w następujący sposób:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Otwórz plik bazy danych MongoDB repozytorium do edycji. Dodaj następujące wiersze:

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Instalowanie przy użyciu bazy danych MongoDB **yum** w następujący sposób:

```bash
sudo yum install -y mongodb-org
```

Domyślnie SELinux są wymuszane na obrazy CentOS, które uniemożliwiają dostęp do bazy danych MongoDB. Zainstaluj narzędzia do zarządzania zasadami i skonfiguruj SELinux, aby umożliwić bazy danych MongoDB do działania na jego domyślny port TCP 27017 w następujący sposób. 

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
Podstawowe wystąpienie bazy danych MongoDB można tworzyć na jednej maszyny Wirtualnej CentOS przy użyciu następującego szablonu Szybki Start Azure z usługi GitHub. Ten szablon używa rozszerzenia niestandardowego skryptu dla systemu Linux, aby dodać `yum` repozytorium do nowo utworzonej maszyny Wirtualnej CentOS i zainstaluj bazy danych MongoDB.

* [Podstawowe wystąpienie bazy danych MongoDB na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `eastus` regionu. Wprowadź własne wartości w następujący sposób:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI powrót do wiersza w ciągu kilku sekund tworzenia wdrożenia, ale instalacja i konfiguracja może zająć kilka minut. Sprawdź stan wdrożenia z `azure group deployment show myResourceGroup`, w związku z tym wprowadzania nazwy grupy zasobów. Poczekaj na **ProvisioningState** pokazuje *zakończyło się pomyślnie* przed ponowną próbą SSH z maszyną Wirtualną.

Po zakończeniu wdrażania, SSH z maszyną wirtualną. Uzyskaj adres IP za pomocą maszyny Wirtualnej `azure vm show` polecenia jak w poniższym przykładzie:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Dane wyjściowe pod koniec publiczny adres IP jest wyświetlany. SSH do maszyny Wirtualnej przy użyciu adresu IP maszyny wirtualnej:

```bash
ssh azureuser@138.91.149.74
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

* [Bazy danych MongoDB dzielenia na fragmenty klastra na CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Wdrażanie klastra podzielonej to złożony bazy danych MongoDB wymaga więcej niż 20 Vcpu, co jest typowe domyślnej liczby vCPU na region na subskrypcję. Otwórz żądanie pomocy technicznej platformy Azure, aby zwiększyć liczbę sieci vCPU.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *eastus* regionu. Wprowadź własne wartości w następujący sposób:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Azure CLI powrót do wiersza w ciągu kilku sekund tworzenia wdrożenia, ale instalacja i konfiguracja może potrwać ponad godzinę ukończenia. Sprawdź stan wdrożenia z `azure group deployment show myResourceGroup`, Dostosuj odpowiednio nazwę grupy zasobów. Poczekaj na **ProvisioningState** pokazuje *zakończyło się pomyślnie* przed nawiązaniem połączenia z maszynami wirtualnymi.


## <a name="next-steps"></a>Kolejne kroki
W tych przykładach łączysz się z wystąpieniem bazy danych MongoDB lokalnie z maszyny Wirtualnej. Jeśli chcesz połączyć się z wystąpieniem bazy danych MongoDB z innej maszyny Wirtualnej lub sieci, upewnij się, odpowiedni [reguł sieciowej grupy zabezpieczeń są tworzone](nsg-quickstart.md).

Aby uzyskać więcej informacji na temat tworzenia za pomocą szablonów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Szablony usługi Azure Resource Manager Użyj niestandardowe rozszerzenie skryptu do pobierania i wykonywania skryptów na maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [z maszyn wirtualnych systemu Linux przy użyciu niestandardowe rozszerzenie skryptu Azure](extensions-customscript.md).


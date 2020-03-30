---
title: 'Szybki start: tworzenie bazy danych platformy Azure dla mysql przy użyciu az mysql up'
description: Przewodnik Szybki start, aby utworzyć usługę Azure Database dla serwera MySQL przy użyciu polecenia interfejsu wiersza polecenia platformy Azure (interfejsu wiersza polecenia).
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: 7b81e88fe6f658fdf4c1857c6082100894c6f2f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067719"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Szybki start: tworzenie bazy danych platformy Azure dla mysql przy użyciu prostego polecenia interfejsu wiersza polecenia platformy Azure — az mysql up (wersja zapoznawcza)

> [!IMPORTANT]
> Polecenie [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI jest w wersji zapoznawczej.

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. Narzędzie cli platformy Azure służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia lub skryptów. Ten przewodnik Szybki start pokazuje, jak użyć polecenia [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) do utworzenia usługi Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia platformy Azure. Oprócz tworzenia serwera `az mysql up` polecenie tworzy przykładową bazę danych, użytkownika głównego w bazie danych, otwiera zaporę dla usług platformy Azure i tworzy domyślne reguły zapory dla komputera klienckiego. Pomaga to przyspieszyć proces rozwoju.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

Ten artykuł wymaga, aby ślić ten Umiejscowić się w ten umiejscowieć. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [logowania az.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp właściwość **identyfikatora subskrypcji** z danych **wyjściowych logowania az** dla subskrypcji na symbol zastępczy identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Aby użyć poleceń, zainstaluj rozszerzenie [db-up.](/cli/azure/ext/db-up) Jeśli zostanie zwrócony błąd, upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Utwórz usługę Azure Database dla serwera MySQL przy użyciu następującego polecenia:

```azurecli
az mysql up
```

Serwer jest tworzony z następującymi wartościami domyślnymi (chyba że zostaną one zastąpione ręcznie):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Wygenerowany system | Unikatowa nazwa, która identyfikuje serwer usługi Azure Database for MySQL.
resource-group | Wygenerowany system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Domyślnie jest to serwer Ogólnego przeznaczenia Gen5 z 2 vCorami. Więcej informacji na temat warstw można znaleźć na naszej [stronie](https://azure.microsoft.com/pricing/details/mysql/) z cennikiem.
backup-retention | 7 | Jak długo należy przechowywać kopię zapasową. Jednostka to dni.
geo-redundant-backup | Disabled (Wyłączony) | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | westus2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Disabled (Wyłączony) | Czy protokół SSL powinien być włączony, czy nie dla tego serwera.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
version | 5.7 | Wersja główna MySQL.
admin-user | Wygenerowany system | Nazwa użytkownika w przypadku logowania administratora.
admin-password | Wygenerowany system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej `az mysql up` informacji na temat polecenia i jego dodatkowych parametrów, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Po utworzeniu serwera są dostarczane następujące ustawienia:

- Tworzona jest reguła zapory o nazwie "devbox". Narzędzie wiersza polecenia platformy Azure próbuje `az mysql up` wykryć adres IP komputera, z na który jest uruchamiane polecenie, i umieszcza na białej liście ten adres IP.
- Opcja "Zezwalaj na dostęp do usług platformy Azure" jest ustawiona na ON. To ustawienie konfiguruje zaporę serwera do akceptowania połączeń ze wszystkich zasobów platformy Azure, w tym zasobów, które nie są dostępne w ramach subskrypcji.
- Parametr `wait_timeout` jest ustawiony na 8 godzin
- Tworzona jest pusta baza danych o nazwie "sampledb"
- Tworzony jest nowy użytkownik o nazwie "root" z uprawnieniami do "sampledb"

> [!NOTE]
> Usługa Azure Database for MySQL komunikuje się za pomocą portu 3306. Podczas łączenia się z sieci firmowej zaporę sieci może nie zezwalać na ruch wychodzący przez port 3306. Niech twój dział IT otworzy port 3306, aby połączyć się z serwerem.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po `az mysql up` zakończeniu polecenia zwracana jest lista ciągów połączeń dla popularnych języków programowania. Te parametry połączenia są wstępnie skonfigurowane z określonymi atrybutami nowo utworzonej bazy danych platformy Azure dla serwera MySQL.

Za pomocą polecenia [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) można ponownie wyświetlić listę tych ciągów połączeń.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Oczyść wszystkie zasoby utworzone w przewodniku Szybki start za pomocą następującego polecenia. To polecenie usuwa usługę Azure Database dla serwera MySQL i grupę zasobów.

```azurecli
az mysql down --delete-group
```

Jeśli chcesz tylko usunąć nowo utworzony serwer, możesz uruchomić polecenie [az mysql down.](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down)

```azurecli
az mysql down
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie bazy danych MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-design-database-using-cli.md)

---
title: Przewodnik Szybki Start — Tworzenie usługi Azure Database for MySQL przy użyciu prostego polecenia wiersza polecenia platformy Azure — az mysql w górę (wersja zapoznawcza)
description: Przewodnik Szybki start dotyczący tworzenia usługi Azure Database dla serwera MySQL przy użyciu wiersza polecenia platformy Azure (interfejs wiersza polecenia) się polecenia.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.custom: mvc
ms.openlocfilehash: aa0d2a9e990faa8d99355744824f34e26aeb519e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61231084"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Szybki start: Tworzenie usługi Azure Database for MySQL przy użyciu prostego polecenia wiersza polecenia platformy Azure — az mysql w górę (wersja zapoznawcza)

> [!IMPORTANT]
> [Az mysql w górę](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) polecenie wiersza polecenia platformy Azure jest dostępna w wersji zapoznawczej.

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki Start dowiesz się, jak używać [az mysql w górę](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) polecenie, aby utworzyć serwer Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure. Oprócz tworzenia serwera, `az mysql up` polecenie tworzy przykładową bazę danych, użytkownika root w bazie danych, otwiera zapory dla usług platformy Azure i tworzy domyślne reguły zapory na komputerze klienckim. Pomaga to przyspieszyć proces tworzenia aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga, że korzystasz z wiersza polecenia platformy Azure w wersji 2.0 lub nowszej lokalnie. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na Twoim koncie za pomocą [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) polecenia. Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp **identyfikator subskrypcji** właściwość **az login** danych wyjściowych dla Twojej subskrypcji w zastępczym identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Aby użyć poleceń, zainstaluj [bazy danych w górę](/cli/azure/ext/db-up) rozszerzenia. Jeśli zostanie zwrócony błąd, upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [zainstalować interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Tworzenie serwera Azure Database for MySQL za pomocą następującego polecenia:

```azurecli
az mysql up
```

Serwer jest tworzony z następujące wartości domyślne (chyba że ręcznie zastępują ich):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Generowane przez system | Unikatowa nazwa, która identyfikuje serwer usługi Azure Database for MySQL.
resource-group | Generowane przez system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Wartość domyślna to serwer ogólnego przeznaczenia 5. generacji z 2 rdzeniami wirtualnymi. Zobacz nasze [stronę z cennikiem](https://azure.microsoft.com/pricing/details/mysql/) Aby uzyskać więcej informacji na temat warstw.
backup-retention | 7 | Jak długo należy przechowywać kopię zapasową. Jednostka to dni.
geo-redundant-backup | Disabled (Wyłączony) | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | westus2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Disabled (Wyłączony) | Określa, czy dla serwera powinien być włączony protokół SSL.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
version | 5.7 | Wersja główna MySQL.
admin-user | Generowane przez system | Nazwa użytkownika w przypadku logowania administratora.
admin-password | Generowane przez system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej informacji na temat `az mysql up` polecenia i dodatkowych parametrów, zobacz [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Po utworzeniu serwera pochodzi z następującymi ustawieniami:

- Tworzona jest reguła zapory o nazwie "devbox". Interfejs wiersza polecenia platformy Azure próbuje wykryć adres IP maszyny `az mysql up` polecenia jest uruchamiana z i umieszczenie tego adresu IP.
- "Zezwalaj na dostęp do usług platformy Azure" jest ustawiona na wartość ON. To ustawienie określa zapory serwera do akceptowania połączeń z wszystkich zasobów platformy Azure, w tym zasobów w Twojej subskrypcji.
- `wait_timeout` Parametr jest ustawiony na 8 godzin
- Utworzono pustą bazę danych o nazwie "sampledb"
- Tworzenia nowego użytkownika o nazwie "root", z uprawnieniami do "sampledb"

> [!NOTE]
> Usługa Azure Database for MySQL komunikuje się przez port 3306. Podczas nawiązywania połączenia z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany przez zaporę sieciową. Ma dział IT, otwórz port 3306, aby połączyć się z serwerem.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po `az mysql up` polecenia zostanie zakończona, lista parametrów połączenia dla popularnych języków programowania zostanie zwrócone do użytkownika. Te parametry połączenia są wstępnie skonfigurowane przy użyciu określonych atrybutów swoje nowo utworzonego serwera Azure Database for MySQL.

Możesz użyć [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) polecenie, aby ponownie wyświetlić listę tych parametrów połączenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść wszystkie zasoby utworzone w przewodniku Szybki Start, używając następującego polecenia. To polecenie usuwa serwer Azure Database for MySQL i grupy zasobów.

```azurecli
az mysql down --delete-group
```

Jeśli po prostu chcesz usunąć nowo utworzony serwer, możesz uruchomić [az mysql w dół](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) polecenia.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Projektowanie bazy danych MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-design-database-using-cli.md)

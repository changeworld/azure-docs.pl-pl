---
title: 'Szybki start: Tworzenie usługi Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia polecenie az postgres w górę'
description: Przewodnik Szybki start dotyczący tworzenia usługi Azure Database for PostgreSQL — jeden serwer przy użyciu wiersza polecenia platformy Azure (interfejs wiersza polecenia) się polecenia.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 49f71c199a2832d763bb3c19d878fade47dfb8e4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069073"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Szybki start: Umożliwia tworzenie usługi Azure Database for PostgreSQL — pojedynczy serwer polecenie wiersza polecenia platformy Azure, az postgres zapasowych (wersja zapoznawcza)

> [!IMPORTANT]
> [Az postgres się](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) polecenie wiersza polecenia platformy Azure jest dostępna w wersji zapoznawczej.

Azure Database for PostgreSQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki Start dowiesz się, jak używać [az postgres się](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) polecenie, aby utworzyć usługi Azure Database for PostgreSQL przy użyciu wiersza polecenia platformy Azure. Oprócz tworzenia serwera, `az postgres up` polecenie tworzy przykładową bazę danych, użytkownika root w bazie danych, otwiera zapory dla usług platformy Azure i tworzy domyślne reguły zapory na komputerze klienckim. Te ustawienia domyślne ułatwiają przyspieszenie procesu opracowywania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga, że korzystasz z wiersza polecenia platformy Azure w wersji 2.0 lub nowszej lokalnie. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do sieci za pomocą konta [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) polecenia. Uwaga **identyfikator** właściwości z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp **identyfikator subskrypcji** właściwość **az login** danych wyjściowych dla Twojej subskrypcji w zastępczym Identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Aby użyć poleceń, zainstaluj [bazy danych w górę](/cli/azure/ext/db-up) rozszerzenia. Jeśli zostanie zwrócony błąd, upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [zainstalować interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Tworzenie usługi Azure Database for PostgreSQL przy użyciu następującego polecenia:

```azurecli
az postgres up
```

Serwer jest tworzony z następujące wartości domyślne (chyba że ręcznie zastępują ich):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Generowane przez system | Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL.
resource-group | Generowane przez system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Wartość domyślna to serwer ogólnego przeznaczenia 5. generacji z 2 rdzeniami wirtualnymi. Zobacz nasze [stronę z cennikiem](https://azure.microsoft.com/pricing/details/postgresql/) Aby uzyskać więcej informacji na temat warstw.
backup-retention | 7 | Jak długo kopii zapasowej są zachowywane. Jednostka to dni.
geo-redundant-backup | Disabled (Wyłączony) | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | westus2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Disabled (Wyłączony) | Określa, czy dla serwera powinien być włączony protokół SSL.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
version | 10 | Wersja główna PostgreSQL.
admin-user | Generowane przez system | Nazwa użytkownika administratora.
admin-password | Generowane przez system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej informacji na temat `az postgres up` polecenia i dodatkowych parametrów, zobacz [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Po utworzeniu serwera pochodzi z następującymi ustawieniami:

- Tworzona jest reguła zapory o nazwie "devbox". Interfejs wiersza polecenia platformy Azure próbuje wykryć adres IP maszyny `az postgres up` polecenia jest uruchamiana z i umieszczenie tego adresu IP.
- "Zezwalaj na dostęp do usług platformy Azure" jest ustawiona na wartość ON. To ustawienie określa zapory serwera do akceptowania połączeń z wszystkich zasobów platformy Azure, w tym zasobów w Twojej subskrypcji.
- Utworzono pustą bazę danych o nazwie "sampledb"
- Tworzenia nowego użytkownika o nazwie "root", z uprawnieniami do "sampledb"

> [!NOTE]
> Usługa Azure Database for PostgreSQL komunikuje się przez port 5432. Podczas nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 5432 może być blokowany przez zaporę sieciową. Ma dział IT, otwarcie portu 5432, aby połączyć się z serwerem.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po `az postgres up` polecenia zostanie zakończona, lista parametrów połączenia dla popularnych języków programowania zostanie zwrócone do użytkownika. Te parametry połączenia są wstępnie skonfigurowane przy użyciu określonych atrybutów w nowo utworzonej bazy danych Azure, aby serwer PostgreSQL.

Możesz użyć [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) polecenie, aby ponownie wyświetlić listę tych parametrów połączenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść wszystkie zasoby utworzone w przewodniku Szybki Start, używając następującego polecenia. To polecenie usuwa usługi Azure Database for postgresql w warstwie serwera i grupy zasobów.

```azurecli
az postgres down --delete-group
```

Jeśli po prostu chcesz usunąć nowo utworzony serwer, możesz uruchomić [az postgres dół](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) polecenia.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)

---
title: 'Szybki start: Tworzenie serwera — az postgres up - Usługa Azure Database for PostgreSQL — pojedynczy serwer'
description: Przewodnik Szybki start do tworzenia bazy danych Azure Database dla PostgreSQL — pojedynczy serwer przy użyciu polecenia interfejsu wiersza polecenia platformy Azure (interfejs wiersza polecenia).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74774836"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Szybki start: tworzenie bazy danych Azure Database dla postgreSQL — pojedynczy serwer — jeden serwer — za pomocą polecenia interfejsu wiersza polecenia platformy Azure, az postgres up (wersja zapoznawcza)

> [!IMPORTANT]
> [Polecenie az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI znajduje się w wersji zapoznawczej.

Azure Database for PostgreSQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki start pokazuje, jak użyć polecenia [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) do utworzenia usługi Azure Database dla serwera PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure. Oprócz tworzenia serwera `az postgres up` polecenie tworzy przykładową bazę danych, użytkownika głównego w bazie danych, otwiera zaporę dla usług platformy Azure i tworzy domyślne reguły zapory dla komputera klienckiego. Te wartości domyślne pomagają przyspieszyć proces programowania.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

Ten artykuł wymaga, aby ślić ten Umiejscowić się w ten umiejscowieć. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [logowania az.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Należy zwrócić uwagę na właściwość **ID** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp właściwość **identyfikatora subskrypcji** z danych **wyjściowych logowania az** dla subskrypcji na symbol zastępczy identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Aby użyć poleceń, zainstaluj rozszerzenie [db-up.](/cli/azure/ext/db-up) Jeśli zostanie zwrócony błąd, upewnij się, że zainstalowano najnowszą wersję interfejsu wiersza polecenia platformy Azure. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Utwórz usługę Azure Database dla serwera PostgreSQL przy użyciu następującego polecenia:

```azurecli
az postgres up
```

Serwer jest tworzony z następującymi wartościami domyślnymi (chyba że zostaną one zastąpione ręcznie):

**Ustawienie** | **Wartość domyślna** | **Opis**
---|---|---
nazwa-serwera | Wygenerowany system | Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL.
resource-group | Wygenerowany system | Nowa grupa zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Domyślnie jest to serwer Ogólnego przeznaczenia Gen5 z 2 vCorami. Więcej informacji na temat warstw można znaleźć na naszej [stronie](https://azure.microsoft.com/pricing/details/postgresql/) z cennikiem.
backup-retention | 7 | Jak długo kopia zapasowa jest zachowywana. Jednostka to dni.
geo-redundant-backup | Disabled (Wyłączony) | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe.
location | westus2 | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Disabled (Wyłączony) | Określa, czy dla serwera powinien być włączony protokół SSL.
storage-size | 5120 | Pojemność magazynu serwera (w megabajtach).
version | 10 | Wersja główna PostgreSQL.
admin-user | Wygenerowany system | Nazwa użytkownika administratora.
admin-password | Wygenerowany system | Hasło użytkownika administratora.

> [!NOTE]
> Aby uzyskać więcej `az postgres up` informacji na temat polecenia i jego dodatkowych parametrów, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Po utworzeniu serwera są dostarczane następujące ustawienia:

- Tworzona jest reguła zapory o nazwie "devbox". Narzędzie wiersza polecenia platformy Azure próbuje `az postgres up` wykryć adres IP komputera, z na który jest uruchamiane polecenie, i umieszcza na białej liście ten adres IP.
- Opcja "Zezwalaj na dostęp do usług platformy Azure" jest ustawiona na ON. To ustawienie konfiguruje zaporę serwera do akceptowania połączeń ze wszystkich zasobów platformy Azure, w tym zasobów, które nie są dostępne w ramach subskrypcji.
- Tworzona jest pusta baza danych o nazwie "sampledb"
- Tworzony jest nowy użytkownik o nazwie "root" z uprawnieniami do "sampledb"

> [!NOTE]
> Usługa Azure Database for PostgreSQL komunikuje się za pomocą portu 5432. Podczas nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 5432 może być blokowany przez zaporę sieciową. Niech twój dział IT otworzy port 5432, aby połączyć się z serwerem.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Po `az postgres up` zakończeniu polecenia zwracana jest lista ciągów połączeń dla popularnych języków programowania. Te parametry połączenia są wstępnie skonfigurowane z określonymi atrybutami nowo utworzonej bazy danych platformy Azure dla serwera PostgreSQL.

Za pomocą polecenia [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) można ponownie wyświetlić listę tych ciągów połączeń.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Oczyść wszystkie zasoby utworzone w przewodniku Szybki start za pomocą następującego polecenia. To polecenie usuwa usługę Azure Database dla serwera PostgreSQL i grupę zasobów.

```azurecli
az postgres down --delete-group
```

Jeśli chcesz tylko usunąć nowo utworzony serwer, możesz uruchomić [polecenie az postgres down.](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down)

```azurecli
az postgres down
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)

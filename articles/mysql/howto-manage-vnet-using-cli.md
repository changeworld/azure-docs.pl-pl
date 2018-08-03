---
title: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL i reguł za pomocą wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia i zarządzania usługi Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL i reguł za pomocą wiersza polecenia z wiersza polecenia platformy Azure.
services: mysql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 22cf09a799b9eff6311b75291665a4f2844291ee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442455"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL przy użyciu wiersza polecenia platformy Azure
Punkty końcowe usługi sieci wirtualnej i reguł rozszerzyć prywatnej przestrzeni adresowej sieci wirtualnej do usługi Azure Database for MySQL server. Za pomocą wygodne poleceń interfejsu wiersza polecenia platformy Azure (CLI) można utworzyć, aktualizowanie, usuwanie, listy i Pokaż punkty końcowe usługi sieci wirtualnej i zasad do zarządzania serwerem. Omówienie usługi Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL, łącznie z ograniczeniami, zobacz [— Azure Database for punkty końcowe usługi sieci wirtualnej serwera MySQL](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne w publicznej wersji zapoznawczej we wszystkich obsługiwanych regionach dla usługi Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- Zainstaluj [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-azure-cli) narzędzie wiersza polecenia lub użyj usługi Azure Cloud Shell w przeglądarce.
- [— Azure Database for MySQL serwera i bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla usługi Azure Database for MySQL
[Az sieci vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) polecenia są używane do konfigurowania sieci wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz zalogować się na swoje konto za pomocą polecenia [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.
```azurecli-interactive
az login
```

Zainstaluj rozszerzenie interfejsu wiersza polecenia dla usługi Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL przy użyciu `az extension add --name rdbms-vnet` polecenia. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Uruchom `az extension list` polecenie, aby sprawdzić poprawność instalacji rozszerzenie interfejsu wiersza polecenia.
```azurecli-interactive
az extension list
```
Dane wyjściowe polecenia wyświetla listę wszystkich zainstalowanych rozszerzeń. Azure Database for rozszerzenie interfejsu wiersza polecenia MySQL jest:

 {"extensionType": "whl", "name": "rdbms wirtualna-sieć wirtualna", "wersja": "10.0.0"}

> [!NOTE]
> Aby odinstalować rozszerzenie interfejsu wiersza polecenia, uruchom `az extension remove -n rdbms-vnet` polecenia. 

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az-account-set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć uprawnienia niezbędne do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi można konfigurować w sieciach wirtualnych niezależnie od siebie, przez użytkownika z dostępem do zapisu do sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia do elementu „Microsoft.Network/JoinServicetoaSubnet” dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby usługi sieci wirtualnej i na platformie Azure należą do różnych subskrypcji, zasoby powinny znajdować się w tej samej dzierżawie usługi Active Directory (AD) w czasie tej wersji zapoznawczej.

> [!IMPORTANT]
> Zdecydowanie zaleca się przeczytaj ten artykuł, konfiguracje punktu końcowego usługi i zagadnienia przed uruchomieniem poniższych przykładowy skrypt lub konfigurowania punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** A [punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi platformy Azure. Punkty końcowe usługi sieci wirtualnej, użyj nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi platformy Azure o nazwie bazy danych SQL. Ten tag usługi dotyczy także usługi Azure SQL Database, Azure Database for PostgreSQL i MySQL. Ważne jest, aby pamiętać podczas stosowania **Microsoft.Sql** tag usługi punktu końcowego usługi sieci wirtualnej umożliwia skonfigurowanie ruch w ramach punktu końcowego usługi dla wszystkich usług bazy danych Azure, w tym usługi Azure SQL Database, Azure Database for PostgreSQL i Usługa Azure Database for MySQL serwerów w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt, aby utworzyć bazę danych Azure for MySQL database, Utwórz sieć wirtualną, a punkt końcowy usługi sieci wirtualnej i zabezpieczanie serwera z podsiecią, reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp identyfikator subskrypcji używany w `az account set --subscription` polecenia przy użyciu własnego identyfikatora subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

---
title: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi postgresql w warstwie sieci wirtualnej i reguł za pomocą wiersza polecenia platformy Azure
description: W tym artykule opisano sposób tworzenia i zarządzania — Azure Database dla punktów końcowych usługi postgresql w warstwie sieci wirtualnej i reguł za pomocą wiersza polecenia z wiersza polecenia platformy Azure.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 7dc5a49e8b27d1a4e4126bef1cce638373e056fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420934"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Tworzenie i zarządzanie nimi — Azure Database dla postgresql w warstwie sieci wirtualnej punktów końcowych usługi za pomocą wiersza polecenia platformy Azure
Punkty końcowe usługi sieci wirtualnej i reguł rozszerzyć prywatnej przestrzeni adresowej sieci wirtualnej do usługi Azure Database for postgresql w warstwie serwera. Za pomocą wygodne poleceń interfejsu wiersza polecenia platformy Azure (CLI) można utworzyć, aktualizowanie, usuwanie, listy i Pokaż punkty końcowe usługi sieci wirtualnej i zasad do zarządzania serwerem. Omówienie usługi Azure Database dla punktów końcowych usługi postgresql w warstwie sieci wirtualnej, w tym informacje o ograniczeniach, zobacz [— Azure Database for punkty końcowe usługi sieci wirtualnej serwera PostgreSQL](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionów dla usługi Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyć usługi Azure Cloud Shell w przeglądarce.
- [— Azure Database for postgresql w warstwie serwera i bazy danych](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnych, jeśli ruch przepływają przez ze wspólnej bramy sieci wirtualnej z punktami końcowymi usługi i powinna przepływać do elementu równorzędnego, Utwórz regułę listy ACL/sieci wirtualnej, aby umożliwić maszynom wirtualnym platformy Azure w sieci wirtualnej bramy usługi Azure Database for postgresql w warstwie serwera dostępu do.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla usługi Azure Database for PostgreSQL
[Az sieci vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) polecenia są używane do konfigurowania sieci wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz zalogować się na swoje konto za pomocą polecenia [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.
```azurecli-interactive
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć uprawnienia niezbędne do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi można konfigurować w sieciach wirtualnych niezależnie od siebie, przez użytkownika z dostępem do zapisu do sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure z siecią wirtualną, użytkownik musi mieć uprawnienia do "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Zasoby usługi sieci wirtualnej i na platformie Azure należą do różnych subskrypcji, zasoby, powinny znajdować się w tej samej dzierżawie usługi Active Directory (AD).

> [!IMPORTANT]
> Zdecydowanie zaleca się przeczytaj ten artykuł, konfiguracje punktu końcowego usługi i zagadnienia przed uruchomieniem poniższych przykładowy skrypt lub konfigurowania punktów końcowych usługi. **Punkt końcowy usługi wirtualne sieci:** A [punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi platformy Azure. Punkty końcowe usługi sieci wirtualnej, użyj nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi platformy Azure o nazwie bazy danych SQL. Ten tag usługi dotyczy także usługi Azure SQL Database, Azure Database for PostgreSQL i MySQL. Ważne jest, aby pamiętać podczas stosowania **Microsoft.Sql** tag usługi punktu końcowego usługi sieci wirtualnej umożliwia skonfigurowanie ruch w ramach punktu końcowego usługi dla wszystkich usług bazy danych Azure, w tym usługi Azure SQL Database, Azure Database for PostgreSQL i Usługa Azure Database for MySQL serwerów w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt do tworzenia usługi Azure Database for postgresql w warstwie bazy danych, Utwórz sieć wirtualną, a punkt końcowy usługi sieci wirtualnej i zabezpieczanie serwera z podsiecią, reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp identyfikator subskrypcji używany w `az account set --subscription` polecenia przy użyciu własnego identyfikatora subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

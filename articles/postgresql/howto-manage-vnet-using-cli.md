---
title: Używanie reguł sieci wirtualnej — interfejs wiersza polecenia platformy Azure — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób tworzenia i zarządzania punktami końcowymi usługi sieci wirtualnej i regułami dla usługi Azure Database dla postgreSQL przy użyciu wiersza polecenia interfejsu wiersza polecenia interfejsu wiersza interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure CLI.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5e80b48e43e72bc3eda16a5dcc64f980065c923f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974124"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tworzenie punktów końcowych usługi sieci wirtualnej dla usługi Azure Database dla usługi PostgreSQL i zarządzanie nimi — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
Punkty końcowe i reguły usług sieci wirtualnej (VNet) rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for PostgreSQL. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure (CLI), można tworzyć, aktualizować, usuwać, listy i wyświetlać punkty końcowe usługi sieci wirtualnej i reguły do zarządzania serwerem. Aby uzyskać omówienie usługi Azure Database dla punktów końcowych usługi postgreSQL VNet, w tym ograniczenia, zobacz [Usługa Azure Database dla punktów końcowych usługi sieci wirtualnej serwera PostgreSQL](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach usługi Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj usługi Azure Cloud Shell w przeglądarce.
- [Usługa Azure Database dla serwera i bazy danych PostgreSQL](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnej, jeśli ruch przepływa przez wspólną bramę sieci wirtualnej z punktami końcowymi usługi i ma przepływ do elementu równorzędnego, utwórz regułę ACL/VNet, aby zezwolić maszynom wirtualnym platformy Azure w sieci wirtualnej bramy na dostęp do bazy danych Azure dla serwera PostgreSQL.


## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla bazy danych platformy Azure dla postgreSQL
Polecenia [sieci vnet az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) służą do konfigurowania sieci wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz zalogować się na swoje konto za pomocą polecenia [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.
```azurecli-interactive
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi mogą być konfigurowane w sieciach wirtualnych niezależnie przez użytkownika z dostępem do zapisu do sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure do sieci wirtualnej, użytkownik musi mieć uprawnienia do "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby usługi sieci wirtualnej i platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny znajdować się w tej samej dzierżawie usługi Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowanego dostawcy zasobów **Microsoft.Sql.** Aby uzyskać więcej informacji, zapoznaj się [z rejestracją menedżera zasobów][resource-manager-portal]

> [!IMPORTANT]
> Zdecydowanie zaleca się przeczytanie tego artykułu na temat konfiguracji punktu końcowego usługi i zagadnienia przed uruchomieniem przykładowego skryptu poniżej lub skonfigurowaniem punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** [Punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwy typu usługi Platformy Azure. Punkty końcowe usług sieci wirtualnej używają nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi Azure o nazwie baza danych SQL. Ten tag usługi ma również zastosowanie do usługi Azure SQL Database, Azure Database for PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania tagu usługi **Microsoft.Sql** do punktu końcowego usługi sieci wirtualnej konfiguruje ruch punktu końcowego usługi dla wszystkich usług usługi Azure Database, w tym usługi Azure SQL Database, azure database for PostgreSQL i Azure Database for MySQL serwerów w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt w celu utworzenia bazy danych usługi Azure Database dla bazy danych PostgreSQL, utworzenia punktu końcowego usługi sieci wirtualnej, sieci wirtualnej i zabezpieczenia serwera w podsieci za pomocą reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp identyfikator `az account set --subscription` subskrypcji używany w poleceniu własnym identyfikatorem subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
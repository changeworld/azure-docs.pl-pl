---
title: Zarządzanie punktami końcowymi sieci wirtualnej — platformę Azure CLI — usługa Azure Database for MySQL
description: W tym artykule opisano sposób tworzenia i zarządzania usługą Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL i reguł przy użyciu wiersza polecenia interfejsu wiersza polecenia interfejsu wiersza polecenia interfejsu wiersza interfejsu wiersza polecenia platformy Azure CLI.
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c01f92f8144c8ebfce2d475f8b13ab1d70bca118
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063583"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Tworzenie punktów końcowych usługi Azure Database dla sieci wirtualnej MySQL i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MySQL. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure (CLI), można tworzyć, aktualizować, usuwać, listy i wyświetlać punkty końcowe usługi sieci wirtualnej i reguły do zarządzania serwerem. Aby uzyskać omówienie usługi Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL, w tym ograniczenia, zobacz [Usługa Azure Database dla punktów końcowych usługi sieci wirtualnej serwera MySQL](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach usługi Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj usługi Azure Cloud Shell w przeglądarce.
- Usługa [Azure Database dla serwera i bazy danych MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnej, jeśli ruch przepływa przez wspólną bramę sieci wirtualnej z punktami końcowymi usługi i ma przepływ do elementu równorzędnego, utwórz regułę ACL/VNet, aby zezwolić maszynom wirtualnym platformy Azure w sieci wirtualnej bramy na dostęp do bazy danych platformy Azure dla serwera MySQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla usługi Azure Database dla mysql
Polecenia [sieci vnet az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) służą do konfigurowania sieci wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

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

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt w celu utworzenia bazy danych usługi Azure Database dla mysql, utworzenia punktu końcowego usługi sieci wirtualnej, sieci wirtualnej i zabezpieczenia serwera do podsieci za pomocą reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp identyfikator `az account set --subscription` subskrypcji używany w poleceniu własnym identyfikatorem subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md


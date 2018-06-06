---
title: Tworzenie i zarządzanie Azure bazy danych dla punktów końcowych usługi MySQL sieci wirtualnej i reguł za pomocą wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia i zarządzania Azure bazy danych dla punktów końcowych usługi MySQL sieci wirtualnej i reguł za pomocą wiersza polecenia z wiersza polecenia platformy Azure.
services: mysql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: fd8b21d1273b1bd02b0a949894be53cdc4a5c3c0
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736596"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Tworzenie i zarządzanie Azure bazy danych dla punktów końcowych usługi MySQL sieci wirtualnej przy użyciu wiersza polecenia platformy Azure
Wirtualny punktów końcowych usługi Network (VNet) i reguły rozszerzyć prywatnej przestrzeni adresowej sieci wirtualnej Azure bazy danych dla serwera MySQL. Za pomocą wygodny poleceń Azure interfejsu wiersza polecenia (CLI), można utworzyć, zaktualizować, usuwanie, listy i wyświetlić punktów końcowych usługi sieci wirtualnej i zasad do zarządzania serwerem. Omówienie bazy danych Azure dla punktów końcowych usługi MySQL sieci wirtualnej, w tym ograniczenia, zobacz [bazą danych Azure dla punktów końcowych usługi MySQL serwera w sieci wirtualnej](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne w publicznej wersji zapoznawczej w wszystkie obsługiwane regiony dla bazy danych Azure dla programu MySQL.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych ten przewodnik, potrzebne są:
- Zainstaluj [Azure CLI 2.0](/cli/azure/install-azure-cli) narzędzia wiersza polecenia lub użyj powłoki chmury Azure w przeglądarce.
- [Azure bazy danych MySQL serwera i bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla bazy danych platformy Azure dla programu MySQL
[Az sieci vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) polecenia są używane do konfigurowania sieci wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz zalogować się na swoje konto za pomocą polecenia [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.
```azurecli-interactive
az login
```

Zainstaluj rozszerzenie interfejsu wiersza polecenia Azure bazy danych dla punktów końcowych usługi MySQL sieci wirtualnej przy użyciu `az extension add --name rdbms-vnet` polecenia. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Uruchom `az extension list` polecenie, aby sprawdzić, czy instalacja rozszerzenia interfejsu wiersza polecenia.
```azurecli-interactive
az extension list
```
Dane wyjściowe polecenia zawiera listę wszystkich zainstalowanych rozszerzeń. Baza danych Azure MySQL CLI rozszerzenia jest:

 {"extensionType": "whl", "name": "sieci wirtualnej rdbms", "version": "10.0.0"}

> [!NOTE]
> Aby odinstalować rozszerzenia interfejsu wiersza polecenia, uruchom `az extension remove -n rdbms-vnet` polecenia. 

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az_account_set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi można skonfigurować w sieciach wirtualnych niezależnie, przez użytkownika z uprawnieniami do zapisu do sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia do elementu „Microsoft.Network/JoinServicetoaSubnet” dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby usługi sieć wirtualną i Azure znajdują się w różnych subskrypcji, w ramach tej samej dzierżawy usługi Active Directory (AD), powinna być zasobów w czasie tej wersji zapoznawczej.

> [!IMPORTANT]
> Zdecydowanie zaleca się w niniejszym artykule konfiguracji punktu końcowego usługi i zaleceń dotyczących przed uruchomieniem Poniższy przykładowy skrypt, lub konfigurowania punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** A [punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) podsieć, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi Azure. Punkty końcowe usługi sieci wirtualnej, użyj nazwy typu usługi **Microsoft.Sql**, które odwołuje się do usługi Azure o nazwie bazy danych SQL. Ten numer seryjny usługi ma również zastosowanie do usługi Azure SQL Database, baza danych Azure dla usług PostgreSQL i MySQL. Ważne jest, aby podczas stosowania należy uwzględnić **Microsoft.Sql** usługi tagu dla punktu końcowego sieci wirtualnej, ponieważ konfiguruje ruch punkt końcowy usługi dla wszystkich usług Azure bazy danych, w tym usługi Azure SQL Database, baza danych Azure dla PostgreSQL i Baza danych Azure, serwerów MySQL w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt do tworzenia bazy danych Azure dla bazy danych MySQL, Utwórz sieć wirtualną, a punkt końcowy usługi sieci wirtualnej i zabezpieczyć serwer do podsieci za pomocą reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp identyfikator subskrypcji używane w `az account set --subscription` z identyfikatorem subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

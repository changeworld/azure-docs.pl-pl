---
title: Skrypt interfejsu wiersza polecenia platformy Azure — zmiana konfiguracji serwera
description: Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych konfiguracji serwera i aktualizuje wartość opcji innodb_lock_wait_timeout.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 75b1228df8bc19ced1d8377768c08048e6ef9150
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441660"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Uzyskiwanie listy i aktualizowanie konfiguracji serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia zwraca listę wszystkich dostępnych parametrów konfiguracji oraz ich dopuszczalnych wartości dla serwera usługi Azure Database for MySQL, a także ustawia parametr *innodb_lock_wait_timeout* na wartość inną niż domyślna.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić wersję interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
W tym przykładowym skrypcie dokonaj edycji wyróżnionych wierszy w celu zmiany nazwy użytkownika i hasła administratora na swoje własne.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=18-19 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mysql server create](/cli/azure/mysql/server#az-msql-server-create) | Tworzy serwer MySQL hostujący bazy danych. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-msql-server-configuration-list) | Zwraca listę konfiguracji serwera usługi Azure Database for MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-msql-server-configuration-set) | Aktualizuje konfigurację serwera usługi Azure Database for MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-msql-server-configuration-show) | Wyświetla konfigurację serwera usługi Azure Database for MySQL. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat interfejsu wiersza polecenia platformy Azure: [dokumentacja interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Jak konfigurować parametry serwera w usłudze Azure Database for MySQL](../howto-server-parameters.md).

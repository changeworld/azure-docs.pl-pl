---
title: Uruchom ponownie usługi Azure Database dla serwera MySQL przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak można ponownie uruchomić serwer Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: d00aa35437f93c010ce48f3036b4a684910702c0
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623156"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Uruchom ponownie usługi Azure Database dla serwera MySQL przy użyciu wiersza polecenia platformy Azure
W tym temacie opisano, jak można ponownie uruchomić serwer Azure Database for MySQL. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację powoduje krótki przestój serwera wykonuje operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzania wcześniej żądanej operacji, takich jak skalowanie rdzeni wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia, zależy od procesu odzyskiwania MySQL. Celu skrócenia czasu ponownego uruchomienia, zalecane jest minimalizacja ilości działań mających miejsce na serwerze przed ponownym uruchomieniu.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database for MySQL server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten poradnik wymaga użycie wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić, która wersja, w wierszu polecenia wiersza polecenia platformy Azure, wprowadź `az --version`. Aby zainstalować lub uaktualnić, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer za pomocą następującego polecenia:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [sposób ustawiania parametrów w usłudze Azure Database for MySQL](howto-configure-server-parameters-using-cli.md)
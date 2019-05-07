---
title: Uruchom ponownie usługi Azure Database for PostgreSQL — jeden serwer przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak można uruchomić ponownie usługi Azure Database for PostgreSQL — jeden serwer przy użyciu wiersza polecenia platformy Azure
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 0a7cd815724fcebd6311860576e620eb9273523b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068976"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Uruchom ponownie usługi Azure Database for PostgreSQL — jeden serwer przy użyciu wiersza polecenia platformy Azure
W tym temacie opisano, jak można uruchomić ponownie usługi Azure Database for postgresql w warstwie serwera. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację powoduje krótki przestój serwera wykonuje operację.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzania wcześniej żądanej operacji, takich jak skalowanie rdzeni wirtualnych.
 
Czas wymagany do ukończenia ponownego uruchomienia, zależy od procesu odzyskiwania PostgreSQL. Celu skrócenia czasu ponownego uruchomienia, zalecane jest minimalizacja ilości działań mających miejsce na serwerze przed ponownym uruchomieniu.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database for postgresql w warstwie serwera](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten poradnik wymaga użycie wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić, która wersja, w wierszu polecenia wiersza polecenia platformy Azure, wprowadź `az --version`. Aby zainstalować lub uaktualnić, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer za pomocą następującego polecenia:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [sposób ustawiania parametrów w usłudze Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
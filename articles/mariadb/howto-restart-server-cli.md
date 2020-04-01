---
title: Uruchom ponownie serwer — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database for MariaDB
description: W tym artykule opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f5572611b99245fd62b4e0a9d73e6ed728e42f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530652"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Ponowne uruchamianie usługi Azure Database dla serwera MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure
W tym temacie opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MariaDB. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację, co powoduje krótką awarię podczas wykonywania operacji przez serwer.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie owo wirtualnych.

Czas wymagany do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MariaDB. Aby skrócić czas ponownego uruchomienia, zaleca się zminimalizowanie aktywności występującej na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik instrukcja wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby potwierdzić wersję, w wierszu `az --version`polecenia interfejsu wiersza polecenia platformy Azure należy wprowadzić polecenie . Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer za pomocą następującego polecenia:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak ustawić parametry w usłudze Azure Database for MariaDB](howto-configure-server-parameters-cli.md)
---
title: Uruchom ponownie serwer — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database for MySQL
description: W tym artykule opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c1fd688fbfd892e3d0dfc3ebb1712dd931e0ed39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063495"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Ponowne uruchamianie usługi Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia platformy Azure
W tym temacie opisano, jak można ponownie uruchomić usługę Azure Database dla serwera MySQL. Może być konieczne ponowne uruchomienie serwera ze względu na konserwację, co powoduje krótką awarię podczas wykonywania operacji przez serwer.

Ponowne uruchomienie serwera zostanie zablokowane, jeśli usługa jest zajęta. Na przykład usługa może przetwarzać wcześniej żądaną operację, taką jak skalowanie owo wirtualnych.

Czas potrzebny do ukończenia ponownego uruchomienia zależy od procesu odzyskiwania MySQL. Aby skrócić czas ponownego uruchomienia, zaleca się zminimalizowanie aktywności występującej na serwerze przed ponownym uruchomieniem.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera MySQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik instrukcja wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby potwierdzić wersję, w wierszu `az --version`polecenia interfejsu wiersza polecenia platformy Azure należy wprowadzić polecenie . Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Uruchom ponownie serwer.

Uruchom ponownie serwer za pomocą następującego polecenia:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak ustawić parametry w usłudze Azure Database dla mysql](howto-configure-server-parameters-using-cli.md)
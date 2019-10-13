---
title: 'Używanie interfejsu wiersza polecenia Databricks z poziomu usługi Azure Cloud Shell '
description: Dowiedz się, jak używać interfejsu wiersza polecenia datakosteks w Azure Cloud Shell, aby wykonywać operacje na Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: b327403b29f26f6c1f43f9dfe7aabd5c3c7e0a68
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299914"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Używanie interfejsu wiersza polecenia Databricks z poziomu usługi Azure Cloud Shell

Dowiedz się, jak używać interfejsu wiersza polecenia datakosteks w Azure Cloud Shell do wykonywania operacji na kostkach.

## <a name="prerequisites"></a>Wymagania wstępne

* Azure Databricks obszar roboczy i klaster. Aby uzyskać instrukcje, zobacz [wprowadzenie do Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Konfigurowanie osobistego tokenu dostępu w kostkach. Aby uzyskać instrukcje, zobacz [zarządzanie tokenami](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Użyj Azure Cloud Shell

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
 
2. W prawym górnym rogu kliknij ikonę **Cloud Shell** .

   ![Uruchom Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "uruchamiania Azure Cloud Shell")

3. Upewnij się, że wybrano **bash** dla środowiska Cloud Shell. Można wybrać opcję z listy rozwijanej, jak pokazano na poniższym zrzucie ekranu.

   ![Wybierz pozycję bash dla środowiska Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Wybierz pozycję bash") 

4. Utwórz środowisko wirtualne, w którym można zainstalować interfejs wiersza polecenia datakosteks. W poniższym fragmencie kodu utworzysz środowisko wirtualne o nazwie `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Przejdź do utworzonego środowiska wirtualnego.

       source databrickscli/bin/activate

6. Zainstaluj interfejs wiersza polecenia datakosteks.

       pip install databricks-cli

7. Skonfiguruj uwierzytelnianie z użyciem elementów datafigur przy użyciu tokenu dostępu, który należy utworzyć, na liście w ramach wymagań wstępnych. Użyj następującego polecenia:

       databricks configure --token

    Zostanie wyświetlony następujący komunikat:

    * Po pierwsze zostanie wyświetlony monit o wprowadzenie hosta datakostki. Wprowadź wartość w formacie `https://eastus2.azuredatabricks.net`. Tutaj **Wschodnie stany USA 2** to region platformy Azure, w którym został utworzony obszar roboczy Azure Databricks.

    * Następnie zostanie wyświetlony monit o wprowadzenie tokenu. Wprowadź wcześniej utworzony token.

Po wykonaniu tych kroków można rozpocząć korzystanie z interfejsu wiersza polecenia datakostek z Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Korzystanie z interfejsu wiersza polecenia datakosteks

Teraz możesz zacząć korzystać z interfejsu wiersza polecenia datakosteks. Na przykład uruchom następujące polecenie, aby wyświetlić listę wszystkich klastrów datakostków, które znajdują się w obszarze roboczym.

    databricks clusters list

Można również użyć następującego polecenia, aby uzyskać dostęp do systemu plików datakostek (DBFS).

    databricks fs ls


Aby uzyskać pełne informacje na temat poleceń, zobacz [interfejs wiersza polecenia datakosteks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [Omówienie interfejsu wiersza polecenia platformy Azure](../cloud-shell/overview.md)
* Aby wyświetlić listę poleceń interfejsu wiersza polecenia platformy Azure, zobacz [informacje dotyczące interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Aby wyświetlić listę poleceń dla interfejsu wiersza polecenia dla elementów datakostks, zobacz [CLI — interfejs wiersza](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) polecenia



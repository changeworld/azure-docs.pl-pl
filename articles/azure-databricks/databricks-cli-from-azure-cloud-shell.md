---
title: 'Używanie interfejsu wiersza polecenia Databricks z poziomu usługi Azure Cloud Shell '
description: Dowiedz się, jak używać interfejsu wiersza polecenia Databricks z usługi Azure Cloud Shell do wykonywania operacji na platformie Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605717"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Używanie interfejsu wiersza polecenia Databricks z poziomu usługi Azure Cloud Shell

Dowiedz się, jak używać interfejsu wiersza polecenia Databricks z usługi Azure Cloud Shell do wykonywania operacji na databricks.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy i klaster usługi Azure Databricks. Aby uzyskać instrukcje, zobacz [Wprowadzenie do usługi Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Konfigurowanie tokenu dostępu osobistego w uchwytów danych. Aby uzyskać instrukcje, zobacz [Zarządzanie tokenami](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Korzystanie z powłoki chmury azure

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
 
2. W prawym górnym rogu kliknij ikonę **Cloud Shell.**

   ![Uruchom powłokę chmury](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Uruchamianie usługi Azure Cloud Shell")

3. Upewnij się, że wybrano **opcję Bash** dla środowiska Cloud Shell. Możesz wybrać z listy rozwijanej, jak pokazano na poniższym zrzucie ekranu.

   ![Wybierz bash dla środowiska Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Wybierz Bash") 

4. Utwórz środowisko wirtualne, w którym można zainstalować plik wiersza polecenia Databricks. We we wyliciach poniżej utworzysz środowisko wirtualne o nazwie `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Przełącz się do utworzonego środowiska wirtualnego.

       source databrickscli/bin/activate

6. Zainstaluj plik WIERSZA POLECENIA Databricks.

       pip install databricks-cli

7. Skonfiguruj uwierzytelnianie za pomocą databricks przy użyciu tokenu dostępu, który musi zostać utworzony, wymienione jako część wymagań wstępnych. Użyj następującego polecenia:

       databricks configure --token

    Otrzymasz następujące monity:

    * Najpierw zostanie wyświetlony monit o wprowadzenie hosta Databricks. Wprowadź wartość w `https://eastus2.azuredatabricks.net`formacie . W tym miejscu **wschodnie stany USA 2** to region platformy Azure, w którym utworzono obszar roboczy usługi Azure Databricks.

    * Następnie zostanie wyświetlony monit o wprowadzenie tokenu. Wprowadź token utworzony wcześniej.

Po wykonaniu tych kroków można rozpocząć korzystanie z interfejsu wiersza polecenia Databricks z usługi Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Korzystanie z interfejsu wiersza polecenia Databricks

Teraz można rozpocząć korzystanie z interfejsu wiersza polecenia Databricks. Na przykład uruchom następujące polecenie, aby wyświetlić listę wszystkich klastrów Databricks, które masz w obszarze roboczym.

    databricks clusters list

Można również użyć następującego polecenia, aby uzyskać dostęp do systemu plików Databricks (DBFS).

    databricks fs ls


Aby uzyskać pełne odwołanie do poleceń, zobacz [Databricks CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o platformie Azure CLI, zobacz [omówienie interfejsu wiersza polecenia platformy Azure](../cloud-shell/overview.md)
* Aby wyświetlić listę poleceń dla interfejsu wiersza polecenia platformy Azure, zobacz [odwołanie do interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Aby wyświetlić listę poleceń interfejsu wiersza polecenia databricks, zobacz [Databricks CLI](/azure/databricks/dev-tools/databricks-cli)



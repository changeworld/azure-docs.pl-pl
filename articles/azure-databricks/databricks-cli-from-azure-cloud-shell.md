---
title: Użyj interfejsu wiersza polecenia Databricks z powłoki chmury Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia Databricks z powłoki chmury Azure.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: c20ad02f962fbee22bb16653c5eab351d9f3de17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598729"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Używanie interfejsu wiersza polecenia Databricks z poziomu usługi Azure Cloud Shell

Dowiedz się, jak używać do wykonywania operacji na Databricks Databricks interfejsu wiersza polecenia z powłoki chmury Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy Azure Databricks i klastra. Aby uzyskać instrukcje, zobacz [wprowadzenie do usługi Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Konfigurowanie osobisty token dostępu w Databricks. Aby uzyskać instrukcje, zobacz [Token zarządzania](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Użyj powłoki chmury Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
 
2. W prawym górnym rogu kliknij **powłoki chmury** ikony.

   ![Uruchom powłokę chmury](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "uruchamianie ODBC z programu Excel")

3. Upewnij się, że wybrano **Bash** dla enviornment powłoki chmury. Możesz wybrać z listy rozwijanej opcję, jak pokazano na poniższym zrzucie ekranu.

   ![Uruchom powłokę chmury](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "uruchamianie ODBC z programu Excel") 

4. Tworzenie środowiska wirtualnego, w którym chcesz zainstalować Databtricks interfejsu wiersza polecenia. We fragmencie poniżej, tworzenie środowiska wirtualnego o nazwie `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Przełączanie do środowiska wirtualnego, który został utworzony.

       source databrickscli/bin/activate

6. Zainstaluj Databricks interfejsu wiersza polecenia.

       pip install databricks-cli

7. Konfigurowanie uwierzytelniania za pomocą Databricks przy użyciu tokenu dostępu, który musi zostały utworzone, wyświetlane jako część wymagań wstępnych. Użyj następującego polecenia:

       databricks configure --token

    Pojawi się następujących wskazówek:

    * Monit o wprowadzenie Databricks hosta. Wprowadź wartość w formacie `https://eastus2.azuredatabricks.net`. W tym miejscu **wschodnie stany USA 2** jest region platformy Azure, w którym utworzono obszar roboczy Azure Databricks.

    * Monit o wprowadzenie nazwy użytkownika. Wprowadź **tokenu**.

    * Ponadto monit o wprowadzenie hasła. Wprowadź token, który został utworzony wcześniej.

Po wykonaniu tych kroków można uruchomić przy użyciu interfejsu wiersza polecenia Databricks z powłoki chmury Azure.

## <a name="use-databricks-cli"></a>Użyj Databricks interfejsu wiersza polecenia

Można teraz uruchomić przy użyciu interfejsu wiersza polecenia Databricks. Na przykład uruchom następujące polecenie, aby wyświetlić listę wszystkich klastrach Databricks znajdujących się w obszarze roboczym.

    databricks clusters list

Następujące polecenie umożliwia również dostęp do plików Databricks (DBFS).

    databricks fs ls


Aby uzyskać pełną dokumentację poleceń, zobacz [Databricks interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat interfejsu wiersza polecenia Azure, zobacz [wiersza polecenia platformy Azure — omówienie](../cloud-shell/overview.md)
* Aby wyświetlić listę poleceń interfejsu wiersza polecenia Azure, zobacz [odwołanie do wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Aby wyświetlić listę poleceń interfejsu wiersza polecenia Databricks, zobacz [Databricks interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)



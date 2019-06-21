---
title: 'Używanie interfejsu wiersza polecenia Databricks z poziomu usługi Azure Cloud Shell '
description: Dowiedz się, jak używać interfejsu wiersza polecenia Databricks z usługi Azure Cloud Shell.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: dae481fb477223f149404c6a09cad024bc15cd90
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204956"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Używanie interfejsu wiersza polecenia Databricks z poziomu usługi Azure Cloud Shell

Dowiedz się, jak wykonywać operacje w usłudze Databricks za pomocą interfejsu wiersza polecenia Databricks z usługi Azure Cloud Shell.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Databricks oraz klaster. Aby uzyskać instrukcje, zobacz [Rozpoczynanie pracy z usługą Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Skonfiguruj osobistego tokenu dostępu w usłudze Databricks. Aby uzyskać instrukcje, zobacz [Token zarządzania](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Użyj usługi Azure Cloud Shell

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
 
2. W prawym górnym rogu, kliknij **Cloud Shell** ikony.

   ![Uruchom usługę Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Uruchom usługę Azure Cloud Shell")

3. Upewnij się, możesz wybrać **Bash** środowiska Cloud Shell. Z opcji listy rozwijanej można wybrać, jak pokazano na poniższym zrzucie ekranu.

   ![Wybierz środowiska Cloud Shell Bash](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "wybierz Bash") 

4. Tworzenie środowiska wirtualnego, w którym chcesz zainstalować interfejs wiersza polecenia usługi Databricks. W poniższym fragmencie kodu, tworzenie środowiska wirtualnego o nazwie `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Przełącz się do środowiska wirtualnego, który został utworzony.

       source databrickscli/bin/activate

6. Instalowanie interfejsu wiersza polecenia Databricks.

       pip install databricks-cli

7. Konfigurowanie uwierzytelniania za pomocą usługi Databricks za pomocą tokenu dostępu, musi mieć utworzone wyświetlany jako część wymagań wstępnych. Użyj następującego polecenia:

       databricks configure --token

    Zostanie wyświetlony według następujących instrukcji:

    * Najpierw monit o podanie hosta usługi Databricks. Wprowadź wartość w formacie `https://eastus2.azuredatabricks.net`. W tym miejscu **wschodnie stany USA 2** to region platformy Azure, w której utworzono obszar roboczy usługi Azure Databricks.

    * Następnie monit o podanie tokenu. Wprowadź token, który został utworzony wcześniej.

Po wykonaniu tych kroków można uruchomić przy użyciu interfejsu wiersza polecenia Databricks z usługi Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Użyj interfejsu wiersza polecenia Databricks

Możesz rozpocząć korzystanie z interfejsu wiersza polecenia Databricks. Na przykład uruchom następujące polecenie, aby wyświetlić listę wszystkich klastrach usługi Databricks, które mają w obszarze roboczym.

    databricks clusters list

Następujące polecenie umożliwia również dostęp do systemu plików usługi Databricks (DBFS).

    databricks fs ls


Aby uzyskać pełną dokumentację dotyczącą poleceń, zobacz [interfejsu wiersza polecenia Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat interfejsu wiersza polecenia platformy Azure, zobacz [wiersza polecenia platformy Azure — omówienie](../cloud-shell/overview.md)
* Aby wyświetlić listę poleceń interfejsu wiersza polecenia platformy Azure, zobacz [odwołanie do wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Aby wyświetlić listę poleceń interfejsu wiersza polecenia Databricks, zobacz [interfejsu wiersza polecenia Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)



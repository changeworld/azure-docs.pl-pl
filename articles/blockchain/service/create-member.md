---
title: Create an Azure Blockchain Service member - Azure portal
description: Create an Azure Blockchain Service member for a blockchain consortium using the Azure portal.
ms.date: 11/18/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9cdc38993aec79552dca0bb51ca2d75462c5306b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325264"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Quickstart: Create an Azure Blockchain Service blockchain member using the Azure portal

In this quickstart, you deploy a new blockchain member and consortium in Azure Blockchain Service using the Azure portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Tworzenie węzłów członkowskich łańcucha bloków

Create a blockchain member that runs the Quorum ledger protocol in a new or existing consortium.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Select **Blockchain** > **Azure Blockchain Service (preview)** .

    ![Create Service](./media/create-member/create-member.png)

    Ustawienie | Opis
    --------|------------
    Subskrypcja | Select the Azure subscription that you want to use for your service. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | Create a new resource group name or choose an existing one from your subscription.
    Region | Choose a region to create the member. All members of the consortium must be in the same location.
    Protocol (Protokół) | Currently, Azure Blockchain Service Preview supports the Quorum protocol.
    Konsorcjum | For a new consortium, enter a unique name. If joining a consortium through an invite, choose the consortium you are joining.
    Nazwa | Choose a unique name for the Azure Blockchain Service member. The blockchain member name can only contain lowercase letters and numbers. Pierwszy znak musi być literą. The value must be between 2 and 20 characters long.
    Member account password | The member account password is used to encrypt the private key for the Ethereum account that is created for your member. You use the member account and member account password for consortium management.
    Opis | Description of the consortium.
    Cennik | The node configuration and cost for your new service. Select the **Change** link to choose between **Standard** and **Basic** tiers.
    Node password | The password for the member's default transaction node. Use the password for basic authentication when connecting to blockchain member's default transaction node public endpoint.

1. Select **Review + create** to validate your settings. Select **Create** to provision the service. Provisioning takes about 10 minutes.
1. Select **Notifications** on the toolbar to monitor the deployment process.
1. After deployment, navigate to your blockchain member.

Select **Overview**, you can view the basic information about your service including the RootContract address and member account.

![Blockchain member overview](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

You can use the member you created for the next quickstart or tutorial. When no longer needed, you can delete the resources by deleting the `myResourceGroup` resource group you created for the quickstart.

To delete the resource group:

1. In the Azure portal, navigate to **Resource group** in the left navigation pane and select the resource group you want to delete.
2. Wybierz pozycję **Usuń grupę zasobów**. Verify deletion by entering the resource group name and select **Delete**.

## <a name="next-steps"></a>Następne kroki

In this quickstart, you deployed an Azure Blockchain Service member and a new consortium. Try the next quickstart to use  Azure Blockchain Development Kit for Ethereum to attach to a consortium on Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Use Visual Studio Code to connect to an Azure Blockchain Service consortium network](connect-vscode.md)

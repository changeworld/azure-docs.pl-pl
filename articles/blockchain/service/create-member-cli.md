---
title: Tworzenie usługi Azure Blockchain, przy użyciu wiersza polecenia platformy Azure
description: Usługa Azure Blockchain Aby utworzyć składową łańcucha bloków przy użyciu wiersza polecenia platformy Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: e1b7558ea83c8948a8984215e15040e4d929cb1b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141376"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Szybki start: Tworzenie usługi Azure Service łańcucha bloków łańcucha bloków składową za pomocą wiersza polecenia platformy Azure

Usługa łańcuch bloków to platforma łańcucha bloków, służących do wykonywania logiki biznesowej w inteligentne kontraktu. Ten przewodnik Szybki Start pokazano, jak rozpocząć pracę od utworzenia składową łańcucha bloków za pomocą wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki Start wymaga interfejsu wiersza polecenia Azure w wersji 2.0.51 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli potrzebujesz instalacja lub uaktualnienie, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Utwórz członka łańcucha bloków

W usłudze Azure Service łańcucha bloków, działający protokół księgi kworum w nowych konsorcjum, należy utworzyć element członkowski łańcucha bloków.

Istnieje kilka parametrów i właściwości, które należy przekazać. Zastąp następujące parametry, z własnymi wartościami.

| Parametr | Opis |
|---------|-------------|
| **resource-group** | Nazwa grupy zasobów, których są tworzone zasoby platformy Azure Blockchain usługi. Użyj grupy zasobów, który został utworzony w poprzedniej sekcji.
| **name** | Unikatową nazwę, która identyfikuje elementu członkowskiego usługi Azure Blockchain Service łańcucha bloków. Nazwa jest używana dla adresu publicznego punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`.
| **location** | Region platformy Azure, w której tworzona jest łańcuch bloków elementu członkowskiego. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure.
| **Hasło** | Hasło konta członkowskiego. Element członkowski hasło do konta jest używany do uwierzytelniania przy użyciu uwierzytelniania podstawowego punktu końcowego publicznego elementu członkowskiego łańcucha bloków.
| **consortium** | Nazwa konsorcjum, które chcesz dołączyć, lub utworzyć.
| **consortiumManagementAccountPassword** | Hasło zarządzania konsorcjum. Służy to do przyłączania konsorcjum.
| **skuName** | Typ warstwy. Na użytek S0 Standard i B0 Basic.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

Utworzenie elementu członkowskiego łańcucha bloków i zasoby pomocnicze trwa około 10 minut.

Poniższej przedstawiono przykładowe dane wyjściowe przedstawiająca pomyślny operacji tworzenia.

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Można użyć Członkowskiego łańcucha bloków, który został utworzony dla następnej szybkiego startu lub samouczku. Gdy nie jest już potrzebny, możesz usunąć zasoby, usuwając `myResourceGroup` grupę zasobów utworzoną przez usługę Azure Service łańcucha bloków.

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie pokrewne zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, po utworzeniu członka łańcucha bloków, wypróbuj jedną z nawiązywania połączenia z transakcji węzła Przewodniki Szybki Start dotyczące [Geth](connect-geth.md), [MetaMask](connect-metamask.md), lub [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Użyj Truffle, aby nawiązać połączenie z siecią Azure Blockchain Service](connect-truffle.md)

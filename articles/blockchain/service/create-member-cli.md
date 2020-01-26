---
title: Tworzenie elementu członkowskiego usługi Azure łańcucha bloków — interfejs wiersza polecenia platformy Azure
description: Utwórz członka usługi Azure łańcucha bloków dla konsorcjum łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 01/23/2020
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 703444b6077c2301e1ffec77c8096fb76ddaa731
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759930"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Szybki Start: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start wdrożono nowego członka łańcucha bloków i konsorcjum w usłudze Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.51 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Tworzenie węzłów członkowskich łańcucha bloków

Członek usługi Azure łańcucha bloków to węzeł łańcucha bloków w sieci prywatnej łańcucha bloków. Podczas aprowizacji elementu członkowskiego można utworzyć sieć konsorcjum lub dołączyć do niej. Potrzebujesz co najmniej jednego elementu członkowskiego dla sieci konsorcjum. Liczba członków łańcucha bloków wymaganych przez uczestników zależy od danego scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą współdzielić członków z innymi uczestnikami. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).

Istnieje kilka parametrów i właściwości, które należy przekazać. Zastąp przykładowe parametry wartościami.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. Użyj grupy zasobów utworzonej w poprzedniej sekcji.
| **Nazwa** | Unikatowa nazwa identyfikująca członka usługi Azure łańcucha bloków Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`.
| **location** | Region świadczenia usługi Azure, w którym jest tworzony element członkowski łańcucha bloków. Na przykład `westus2`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure.
| **Hasło** | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków.
| **ustanawiające** | Nazwa konsorcjum do przyłączenia lub utworzenia. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).
| **consortiumAccountPassword** | Hasło konta konsorcjum jest również znane jako hasło do konta elementu członkowskiego. Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego.
| **skuName** | Typ warstwy. Użyj S0 dla Standard i B0 dla warstwy Podstawowa.

Utworzenie elementu członkowskiego łańcucha bloków i obsłudze zasobów trwa około 10 minut.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz użyć elementu członkowskiego łańcucha bloków utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy zasoby nie będą już potrzebne, można je usunąć, usuwając grupę zasobów `myResourceGroup` utworzoną dla przewodnika Szybki Start.

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono członka usługi Azure łańcucha bloków i nowego konsorcjum. Skorzystaj z następnego przewodnika Szybki Start, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum w celu dołączenia do członka usług Azure łańcucha bloków.

> [!div class="nextstepaction"]
> [Użyj Visual Studio Code, aby nawiązać połączenie z usługą Azure łańcucha bloków](connect-vscode.md)

---
title: Tworzenie członka usługi Azure Blockchain — interfejs wiersza polecenia platformy Azure
description: Utwórz członka usługi Azure Blockchain dla konsorcjum łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 322b1884726b6dfe322560032ed1b8a98c600154
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529618"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Szybki start: tworzenie członka łańcucha bloków usługi Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start można wdrożyć nowego członka łańcucha bloków i konsorcjum w usłudze Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/bash](https://shell.azure.com/bash)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować i używać interfejsu wiersza polecenia lokalnie, ten szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.51 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli chcesz zainstalować lub uaktualnić, zobacz [instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Tworzenie członka łańcucha bloków

Członek usługi Azure Blockchain Service jest węzłem łańcucha bloków w sieci łańcucha bloków prywatnego konsorcjum. Podczas inicjowania obsługi administracyjnej członka, można utworzyć lub dołączyć do sieci konsorcjum. Potrzebujesz co najmniej jednego członka sieci konsorcjum. Liczba członków łańcucha bloków potrzebnych uczestnikom zależy od scenariusza. Uczestnicy konsorcjum mogą mieć jednego lub więcej członków łańcucha bloków lub mogą dzielić się członkami z innymi uczestnikami. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [konsorcjum usługi Azure Blockchain Service](consortium.md).

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
| **grupa zasobów** | Nazwa grupy zasobów, w której tworzone są zasoby usługi Azure Blockchain Service. Użyj grupy zasobów utworzonej w poprzedniej sekcji.
| **Nazwa** | Unikatowa nazwa identyfikująca członka łańcucha bloków usługi Azure Blockchain. Nazwa jest używana dla adresu publicznego punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`.
| **Lokalizacji** | Region platformy Azure, w którym jest tworzony członek łańcucha bloków. Na przykład `westus2`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure.
| **hasło** | Hasło do domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas łączenia się z domyślnym węzłem transakcji publicznej punktu końcowego członka łańcucha bloków.
| **Konsorcjum** | Nazwa konsorcjum do przyłączenia lub utworzenia. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [konsorcjum usługi Azure Blockchain Service](consortium.md).
| **konsorcjumAccountPassword** | Hasło konta konsorcjum jest również znane jako hasło konta członkowskiego. Hasło konta członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum, który jest tworzony dla twojego członka. Do zarządzania konsorcjum używasz konta członkowskiego i hasła do konta członkowskiego.
| **skuName (nazwa skuname)** | Typ warstwy. Użyj S0 dla standardu i B0 dla basic. Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji pojęć. Użyj warstwy *Standardowa* dla wdrożeń na poziomie produkcyjnym. Należy również użyć *warstwy Standardowa,* jeśli używasz Menedżera danych Blockchain lub wysyłasz dużą liczbę transakcji prywatnych. Zmiana warstwy cenowej między podstawową a standardową po utworzeniu elementu członkowskiego nie jest obsługiwana.

Tworzenie członka łańcucha bloków i wspieranie zasobów zajmuje około 10 minut.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz użyć członka łańcucha bloków utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy nie są już potrzebne, można usunąć `myResourceGroup` zasoby, usuwając grupę zasobów utworzoną dla szybkiego startu.

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono członka usługi Azure Blockchain service i nowe konsorcjum. Wypróbuj następny przewodnik Szybki start, aby dołączyć do członka usługi Azure Blockchain Service za pomocą zestawu Azure Blockchain Development Kit for Ethereum.

> [!div class="nextstepaction"]
> [Łączenie się z usługą Azure Blockchain Service za pomocą kodu programu Visual Studio](connect-vscode.md)

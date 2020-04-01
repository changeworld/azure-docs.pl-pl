---
title: Szybki start — tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082451"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Szybki start: tworzenie centrum powiadomień platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Aby uzyskać więcej informacji na temat usługi, zobacz [Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

W tym przewodniku Szybki start utworzysz centrum powiadomień przy użyciu interfejsu wiersza polecenia platformy Azure. Pierwsza sekcja zawiera kroki tworzenia obszaru nazw centrum powiadomień i informacji o zasadach dostępu do kwerend dla tego obszaru nazw. Druga sekcja zawiera kroki, aby utworzyć centrum powiadomień w istniejącej przestrzeni nazw.  Dowiesz się również, jak utworzyć niestandardowe zasady dostępu.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Centra powiadomień wymaga wersji 2.0.67 lub nowszej interfejsu wiersza polecenia platformy Azure. Uruchom, `az --version` aby znaleźć zainstalowaną wersję i biblioteki zależne. Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Zaloguj się.

   Zaloguj się za pomocą polecenia [logowania az,](/cli/azure/reference-index#az-login) jeśli używasz lokalnej instalacji interfejsu wiersza polecenia.

    ```azurecli-interactive
    az login
    ```

    Wykonaj kroki wyświetlane w terminalu, aby zakończyć proces uwierzytelniania.

2. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

   Aby uruchomić polecenia interfejsu wiersza polecenia platformy Azure dla centrów powiadomień, zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure [dla centrów powiadomień](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Utwórz grupę zasobów.

   Centra powiadomień platformy Azure, podobnie jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

   W przypadku tego przewodnika Szybki start utwórz grupę zasobów o nazwie *spnhubrg* w lokalizacji *eastus* z [następującym poleceniem tworzenia grupy az:](/cli/azure/group#az-group-create)

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Tworzenie obszaru nazw centrum powiadomień

1. Tworzenie przestrzeni nazw dla centrów powiadomień

   Obszar nazw zawiera co najmniej jeden koncentratorów, a nazwa musi być unikatowa we wszystkich subskrypcjach platformy Azure.  Aby sprawdzić dostępność danego obszaru nazw usługi, użyj polecenia [az notification-hub namespace check-availability.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)  Uruchom polecenie [tworzenia obszaru nazw centrum powiadomień az](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) w celu utworzenia obszaru nazw.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Listy kluczy i ciągów połączeń dla zasad dostępu obszaru nazw.

   Dla nowej przestrzeni nazw automatycznie tworzona jest zasada dostępu o nazwie **RootManageSharedAccessKey.**  Każda zasada dostępu ma dwa zestawy kluczy i parametry połączenia.  Aby wyświetlić listę kluczy i ciągów połączeń dla obszaru nazw, uruchom polecenie [az notification-hub autoryzacji-reguły listy kluczy.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Tworzenie centrów powiadomień

1. Utwórz pierwsze centrum powiadomień.

   Centrum powiadomień można teraz utworzyć w nowym obszarze nazw.  Uruchom polecenie [az notification-hub create,](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) aby utworzyć centrum powiadomień.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Utwórz drugie centrum powiadomień.

   Wiele centrów powiadomień można utworzyć w jednej przestrzeni nazw.  Aby utworzyć drugie centrum powiadomień w tym `az notification-hub create` samym obszarze nazw, uruchom polecenie ponownie przy użyciu innej nazwy koncentratora.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Praca z zasadami dostępu

1. Utwórz nową regułę autoryzacji dla centrum powiadomień.

   Zasady dostępu są tworzone automatycznie dla każdego nowego centrum powiadomień.  Aby utworzyć i dostosować własne zasady dostępu, użyj polecenia [az notification-hub authorization-rule create.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create)

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Lista zasad dostępu dla centrum powiadomień.

   Aby zbadać, jakie zasady dostępu istnieją dla centrum powiadomień, użyj polecenia [az notification-hub authorization-rule.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list)

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Nie należy używać zasad **DefaultFullSharedAccessSignature** w aplikacji. Jest on przeznaczony tylko do użycia w zapleczu.  Użyj tylko **nasłuchiwać** zasad dostępu w aplikacji klienckiej.

3. Listy kluczy i ciągów połączeń dla zasad dostępu centrum powiadomień

   Istnieją dwa zestawy kluczy i parametry połączenia dla każdej zasady dostępu.  Będziesz ich potrzebować później do obsługi powiadomień wypychanych.  Aby wyświetlić listę ciągów kluczy i połączeń dla zasad dostępu centrum powiadomień, użyj polecenia [az notification-hub authorization-rule list-keys.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Obszar nazw centrum powiadomień](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) i centrum [powiadomień](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) mają oddzielne zasady dostępu.  Upewnij się, że używasz poprawnego odwołania interfejsu wiersza polecenia platformy Azure podczas wykonywania zapytań o klucze i parametry połączenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj polecenia [usuń grupę az,](/cli/azure/group) aby usunąć grupę zasobów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Zobacz też

Odkryj pełne możliwości zarządzania centrami powiadomień za pomocą interfejsu wiersza polecenia platformy Azure.

* [Centrum powiadomień pełna lista odwołań interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/notification-hub/notification-hub)
* [Lista odwołań interfejsu wiersza polecenia platformy cli obszaru nazw powiadomień](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Reguła autoryzacji usługi Azure CLI reguły powiadomień](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Lista odwołań poświadczania usługi Azure CLI dotyczące usług Hubs](/cli/azure/ext/notification-hub/notification-hub/credential)

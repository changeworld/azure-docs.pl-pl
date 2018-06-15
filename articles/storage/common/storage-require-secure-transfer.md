---
title: Wymaga zapewnienia bezpiecznego transferu w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Więcej informacji na temat funkcji "Bezpieczny transfer wymagane" dla usługi Azure Storage i jak je włączyć.
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.openlocfilehash: 0ec36864377d724795197851341cbb837f47c4c6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599802"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Wymaga zapewnienia bezpiecznego transferu w usłudze Azure Storage

Opcja "Bezpieczny transfer wymagane" podnosi poziom bezpieczeństwa konta magazynu, zezwalając tylko żądania do konta z bezpiecznych połączeń. Na przykład gdy w przypadku wywoływania interfejsów API REST, aby uzyskać dostęp do konta magazynu, należy połączyć przy użyciu protokołu HTTPS. "Bezpieczny transfer wymagane" odrzuca żądania, które używają protokołu HTTP.

Gdy używasz usługi pliki Azure każde połączenie bez szyfrowania nie powiedzie się po włączeniu "Bezpieczny transfer wymagane". W tym scenariuszy, które korzystają z protokołu SMB 2.1, SMB 3.0 bez szyfrowania i niektóre wersje klienta SMB w systemie Linux. 

Domyślnie opcja "Bezpieczny transfer wymagane" jest wyłączona.

> [!NOTE]
> Ponieważ Magazyn Azure nie obsługuje protokołu HTTPS dla niestandardowych nazw domen, ta opcja nie została zastosowana podczas korzystania z niestandardowej nazwy domeny. I klasycznych kont magazynu nie są obsługiwane.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Włącz "Bezpieczny transfer wymagane" w portalu Azure

Można włączyć "bezpieczny transfer wymagane" ustawienie podczas tworzenia konta magazynu w [portalu Azure](https://portal.azure.com). Można również włączyć dla istniejących kont magazynu.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Wymaga zapewnienia bezpiecznego transferu dla nowego konta magazynu

1. Otwórz **utworzyć konto magazynu** okienku w portalu Azure.
1. W obszarze **bezpieczny transfer wymagane**, wybierz pozycję **włączone**.

  ![Tworzenie bloku konto magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Wymaga zapewnienia bezpiecznego transferu do istniejącego konta magazynu

1. Wybierz istniejące konto magazynu w portalu Azure.
1. W magazynie konta menu okienku w obszarze **ustawienia**, wybierz pozycję **konfiguracji**.
1. W obszarze **bezpieczny transfer wymagane**, wybierz pozycję **włączone**.

  ![Okienko menu konta magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Włącz "Bezpieczny transfer wymagane" programowo

Aby wymagać zapewnienia bezpiecznego transferu programowo, użyj ustawienia _supportsHttpsTrafficOnly_ we właściwościach konta magazynu z interfejsu API REST, narzędzia i biblioteki:

* [Interfejs API REST](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (wersja: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (wersja: 4.1.0)
* [Interfejs wiersza polecenia](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (wersja: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (wersja: 1.1.0)
* [Zestaw .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (wersja: 6.3.0)
* [Zestaw SDK Python](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (wersja: 1.1.0)
* [Zestaw SDK dopisków fonetycznych](https://rubygems.org/gems/azure_mgmt_storage) (wersja: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Włącz "Zapewnienia bezpiecznego transferu wymagane" ustawienie przy użyciu programu PowerShell

W tym przykładzie wymaga programu Azure PowerShell w wersji modułu 4.1 lub nowszą. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenia z platformą Azure.

 Aby sprawdzić ustawienia, należy użyć następującego polecenia:

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Aby włączyć ustawienie, należy użyć następującego polecenia:

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Włącz "Zapewnienia bezpiecznego transferu wymagane" Ustawianie za pomocą interfejsu wiersza polecenia

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Aby sprawdzić ustawienia, należy użyć następującego polecenia:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Aby włączyć ustawienie, należy użyć następującego polecenia:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Kolejne kroki
Magazyn Azure oferuje rozbudowany zestaw funkcji zabezpieczeń, które razem umożliwiają deweloperom tworzenie bezpiecznych aplikacji. Aby uzyskać więcej informacji, przejdź do [przewodnik zabezpieczeń magazynu](storage-security-guide.md).

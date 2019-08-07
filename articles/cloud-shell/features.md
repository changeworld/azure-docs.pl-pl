---
title: Funkcje Azure Cloud Shell | Microsoft Docs
description: Omówienie funkcji w Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 1354f7befd8c38537a555e17733f431dd488cf60
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742051"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkcje & narzędzia dla Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell uruchomiony `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funkcje

### <a name="secure-automatic-authentication"></a>Bezpieczne uwierzytelnianie automatyczne

Cloud Shell bezpiecznie i automatycznie uwierzytelnia dostęp do konta dla interfejsu wiersza polecenia platformy Azure i Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME trwałość między sesjami

Aby utrwalać pliki między sesjami, Cloud Shell przeprowadzi Cię przez dołączenie udziału plików platformy Azure przy pierwszym uruchomieniu.
Po zakończeniu Cloud Shell automatycznie dołączy magazyn (zainstalowany jako `$HOME\clouddrive`) dla wszystkich przyszłych sesji.
`$HOME` Ponadto katalog jest utrwalony jako. img w udziale plików platformy Azure.
Pliki poza programem `$HOME` i stan komputera nie są utrwalane między sesjami. W przypadku przechowywania wpisów tajnych, takich jak klucze SSH, należy stosować najlepsze rozwiązania. Usługi, takie jak [Azure Key Vault, mają samouczki dla Instalatora](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Dowiedz się więcej o utrwalaniu plików w Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Dysk platformy Azure (Azure:)

Program PowerShell w Cloud Shell uruchamia Cię w usłudze Azure`Azure:`Drive ().
Dysk platformy Azure umożliwia łatwe odnajdowanie zasobów platformy Azure, takich jak obliczenia, Sieć, magazyn itp., podobnie jak nawigowanie po systemie plików.
Aby zarządzać tymi zasobami niezależnie od używanego dysku, można nadal używać znanych [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure) .
Wszystkie zmiany wprowadzone w zasobach platformy Azure, które zostały wprowadzone bezpośrednio w Azure Portal lub za pomocą poleceń cmdlet Azure PowerShell, są odzwierciedlone na dysku platformy Azure.  Aby odświeżyć `dir -Force` zasoby, można uruchomić.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Zarządzanie usługą Exchange Online

Program PowerShell w Cloud Shell zawiera prywatną kompilację modułu usługi Exchange Online.  Uruchom `Connect-EXOPSSession` polecenie, aby uzyskać polecenia cmdlet programu Exchange.

![](media/features-powershell/exchangeonline.png)

 Uruchom `Get-Command -Module tmp_*`
> [!NOTE]
> Nazwa modułu powinna rozpoczynać `tmp_`się od, jeśli zainstalowano moduły z tym samym prefiksem, zostaną również wyświetlone te polecenia cmdlet. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Ścisła integracja z narzędziami Open Source

Cloud Shell obejmuje wstępnie skonfigurowane uwierzytelnianie dla narzędzi typu "open source", takich jak Terraform, rozwiązania ansible i Chef. Wypróbuj ją z przykładowych przewodników.

## <a name="tools"></a>Narzędzia

|Kategoria   |Name (Nazwa)   |
|---|---|
|Narzędzia systemu Linux            |bash<br> zsh<br> pok<br> tmux<br> Dig<br>               |
|Narzędzia platformy Azure            |[Interfejs wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli) i [klasyczny interfejs wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli)<br> [Narzędzie AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Interfejs wiersza polecenia Azure Functions](https://github.com/Azure/azure-functions-core-tools)<br> [Interfejs wiersza polecenia usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Usługa Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Edytory tekstu           |kod (Edytor Cloud Shell)<br> vim<br> Nano<br> Emacs:    |
|Kontrola źródła         |git                    |
|Narzędzia kompilacji            |SprawdY<br> Maven<br> npm<br> graczy         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [INTERFEJS WIERSZA POLECENIA DC/OS](https://github.com/dcos/dcos-cli)         |
|Bazy danych              |Klient MySQL<br> Klient PostgreSql<br> [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Inne                  |Klient iPython<br> [Interfejs wiersza polecenia Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Rozwiązania ansible](https://www.ansible.com/microsoft-azure)<br> [Program chef InSpec](https://www.chef.io/inspec/)<br> [Puppet](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Obsługa języków

|Język   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Z rzeczywistym użyciem         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.2.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 i 3,5 (wartość domyślna)|

## <a name="next-steps"></a>Kolejne kroki
[Bash w Cloud Shell Szybki Start](quickstart.md) <br>
[Program PowerShell w Cloud Shell Szybki Start](quickstart-powershell.md) <br>
[Informacje o interfejsie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/) <br>
[Dowiedz się więcej o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>

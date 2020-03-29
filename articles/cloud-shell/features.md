---
title: Funkcje usługi Azure Cloud Shell | Dokumenty firmy Microsoft
description: Omówienie funkcji w usłudze Azure Cloud Shell
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
ms.openlocfilehash: 0aa71e4b78df8087093f183b146c525d2a8a0f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366244"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkcje & narzędzia dla usługi Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Usługa Azure Cloud `Ubuntu 16.04 LTS`Shell działa na .

## <a name="features"></a>Funkcje

### <a name="secure-automatic-authentication"></a>Bezpieczne uwierzytelnianie automatyczne

Usługa Cloud Shell bezpiecznie i automatycznie uwierzytelnia dostęp do konta dla interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME wytrwałość w sesjach

Aby utrwalić pliki w sesjach, usługa Cloud Shell przeprowadzi Cię przez dołączanie udziału plików platformy Azure przy pierwszym uruchomieniu.
Po zakończeniu Cloud Shell automatycznie dołączy pamięć `$HOME\clouddrive`masową (zamontowaną jako) dla wszystkich przyszłych sesji.
Ponadto katalog `$HOME` jest zachowywany jako .img w udziale pliku azure.
Pliki poza `$HOME` i stan komputera nie są utrwalone w sesjach. Należy użyć najlepszych rozwiązań podczas przechowywania wpisów tajnych, takich jak klucze SSH. Usługi takie jak [Azure Key Vault mają samouczki dotyczące konfiguracji](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Dowiedz się więcej o utrwalaniu plików w usłudze Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Dysk platformy Azure (Azure:)

Program PowerShell w usłudze Cloud`Azure:`Shell uruchamia cię na dysku platformy Azure ( ).
Dysk platformy Azure umożliwia łatwe wykrywanie i nawigację zasobów platformy Azure, takich jak zasoby obliczeniowe, sieć, magazyn itp., podobnie jak nawigacja w systemie plików.
Możesz nadal używać znanych [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure) do zarządzania tymi zasobami, niezależnie od dysku, na który się znajdujesz.
Wszelkie zmiany wprowadzone w zasobach platformy Azure, wprowadzone bezpośrednio w witrynie Azure portal lub za pośrednictwem poleceń cmdlet programu Azure PowerShell, są odzwierciedlane na dysku platformy Azure.  Można uruchomić, `dir -Force` aby odświeżyć swoje zasoby.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Zarządzanie usługą Exchange Online

Program PowerShell w usłudze Cloud Shell zawiera prywatną kompilację modułu usługi Exchange Online.  Uruchom, `Connect-EXOPSSession` aby uzyskać polecenia cmdlet exchange.

![](media/features-powershell/exchangeonline.png)

 Uruchom polecenie `Get-Command -Module tmp_*`
> [!NOTE]
> Nazwa modułu powinna `tmp_`zaczynać się od , jeśli zainstalowano moduły z tym samym prefiksem, ich polecenia cmdlet również zostaną ukazywany. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Głęboka integracja z narzędziami typu open source

Usługa Cloud Shell zawiera wstępnie skonfigurowane uwierzytelnianie dla narzędzi typu open source, takich jak Terraform, Ansible i Chef InSpec. Wypróbuj go z przykładowych instruktajm.

## <a name="tools"></a>Narzędzia

|Kategoria   |Nazwa   |
|---|---|
|Narzędzia linuksa            |bash<br> Zsh<br> Sh<br> tmux<br> Kopać<br>               |
|Narzędzia platformy Azure            |[Interfejs wiersza polecenia interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli) i [klasycznego interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy (Polski)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Interfejs wiersza polecenia usługi Azure Functions](https://github.com/Azure/azure-functions-core-tools)<br> [Interfejs wiersza polecenia usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Usługa Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Edytory tekstu           |(edytor Cloud Shell)<br> vim<br> nano<br> emacs    |
|Kontrola źródła         |git                    |
|Tworzenie narzędzi            |make<br> maven<br> npm<br> Pip         |
|Kontenery             |[Docker Machine](https://github.com/docker/machine)<br> [Okręg wyborczy Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Hełm](https://github.com/kubernetes/helm)<br> [Interfejsu wiersza polecenia DC/OS](https://github.com/dcos/dcos-cli)         |
|Bazy danych              |Klient MySQL<br> Klient PostgreSql<br> [Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Inne                  |Klient iPython<br> [Cli odlewnicza chmury](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Możliwość ansible](https://www.ansible.com/microsoft-azure)<br> [Szef kuchni InSpec](https://www.chef.io/inspec/)<br> [Lalkowa śruba](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Obsługa języków

|Język   |Wersja   |
|---|---|
|.NET Core  |2.2.402       |
|Przejdź         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 i 3.5 (domyślnie)|

## <a name="next-steps"></a>Następne kroki
[Bash w Cloud Shell Szybki start](quickstart.md) <br>
[Program PowerShell w programie Cloud Shell Szybki start](quickstart-powershell.md) <br>
[Dowiedz się więcej o platformie Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Dowiedz się więcej o programie Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>

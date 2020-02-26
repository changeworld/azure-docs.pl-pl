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
ms.openlocfilehash: 20ac4fabdc5f0e70191d6886d43a5376bcf4cad5
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586295"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkcje & narzędzia dla Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell działa na `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funkcje

### <a name="secure-automatic-authentication"></a>Bezpieczne uwierzytelnianie automatyczne

Cloud Shell bezpiecznie i automatycznie uwierzytelnia dostęp do konta dla interfejsu wiersza polecenia platformy Azure i Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME trwałość między sesjami

Aby utrwalać pliki między sesjami, Cloud Shell przeprowadzi Cię przez dołączenie udziału plików platformy Azure przy pierwszym uruchomieniu.
Po zakończeniu Cloud Shell automatycznie dołączy magazyn (zainstalowany jako `$HOME\clouddrive`) dla wszystkich przyszłych sesji.
Ponadto katalog `$HOME` jest utrwalony jako. img w udziale plików platformy Azure.
Pliki poza `$HOME` i stan komputera nie są utrwalane między sesjami. W przypadku przechowywania wpisów tajnych, takich jak klucze SSH, należy stosować najlepsze rozwiązania. Usługi, takie jak [Azure Key Vault, mają samouczki dla Instalatora](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Dowiedz się więcej o utrwalaniu plików w Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Dysk platformy Azure (Azure:)

Program PowerShell w Cloud Shell uruchamia Cię w usłudze Azure Drive (`Azure:`).
Dysk platformy Azure umożliwia łatwe odnajdowanie zasobów platformy Azure, takich jak obliczenia, Sieć, magazyn itp., podobnie jak nawigowanie po systemie plików.
Aby zarządzać tymi zasobami niezależnie od używanego dysku, można nadal używać znanych [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure) .
Wszystkie zmiany wprowadzone w zasobach platformy Azure, które zostały wprowadzone bezpośrednio w Azure Portal lub za pomocą poleceń cmdlet Azure PowerShell, są odzwierciedlone na dysku platformy Azure.  Aby odświeżyć zasoby, można uruchomić `dir -Force`.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Zarządzanie usługą Exchange Online

Program PowerShell w Cloud Shell zawiera prywatną kompilację modułu usługi Exchange Online.  Uruchom `Connect-EXOPSSession`, aby pobrać polecenia cmdlet programu Exchange.

![](media/features-powershell/exchangeonline.png)

 Uruchom polecenie `Get-Command -Module tmp_*`
> [!NOTE]
> Nazwa modułu powinna rozpoczynać się od `tmp_`, jeśli zainstalowano moduły z tym samym prefiksem, zostaną również wyświetlone ich polecenia cmdlet. 

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
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Polecenia kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [INTERFEJS WIERSZA POLECENIA DC/OS](https://github.com/dcos/dcos-cli)         |
|Bazy danych              |Klient MySQL<br> Klient PostgreSql<br> [Narzędzie sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [MSSQL-Scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Inne                  |Klient iPython<br> [Interfejs wiersza polecenia Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Rozwiązania ansible](https://www.ansible.com/microsoft-azure)<br> [Chef — Specyfikacja](https://www.chef.io/inspec/)<br> [Puppet](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Obsługa języków

|Język   |Wersja   |
|---|---|
|.NET Core  |2.2.402       |
|Przejdź         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0 — RC. 2](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 i 3,5 (wartość domyślna)|

## <a name="next-steps"></a>Następne kroki
[Bash w Cloud Shell Szybki Start](quickstart.md) <br>
[Program PowerShell w Cloud Shell Szybki Start](quickstart-powershell.md) <br>
[Informacje o interfejsie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/) <br>
[Dowiedz się więcej o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>

---
title: Funkcje usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Omówienie funkcji Bash w usłudze Azure Cloud Shell
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
ms.openlocfilehash: 6b5f0e96b90ee0515c0a86f41c6ee2161d6c54a6
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752715"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkcje i narzędzia dla usługi Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Usługa Azure Cloud Shell jest uruchamiany na `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funkcje

### <a name="secure-automatic-authentication"></a>Bezpieczne uwierzytelnianie automatyczne

Usługa cloud Shell bezpiecznie i automatycznie przeprowadza uwierzytelnianie dostępu do konta dla wiersza polecenia platformy Azure i programu Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>Trwałości $HOME między sesjami

Aby utrwalać pliki między sesjami, usługa Cloud Shell przeprowadzi Cię przez dołączenie udziału plików platformy Azure przy pierwszym uruchomieniu.
Po zakończeniu Cloud Shell spowoduje automatyczne dołączanie usługi storage (zainstalowanego jako `$HOME\clouddrive`) dla wszystkich przyszłych sesji.
Ponadto usługi `$HOME` katalogu są utrwalane jako .img w udziale plików platformy Azure.
Pliki poza `$HOME` i stan maszyny nie są zachowywane między sesjami. W przypadku przechowywania wpisów tajnych, takich jak klucze SSH, należy stosować najlepsze rozwiązania. Usługi takie jak [usługi Azure Key Vault ma samouczków dotyczących instalacji](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Dowiedz się więcej na temat utrwalanie plików w usłudze Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Dysk Azure (Azure:)

Program PowerShell w usłudze Cloud Shell rozpoczyna się na dysku platformy Azure (`Azure:`).
Dysk Azure umożliwia łatwe odnajdowanie i nawigacji zasobów platformy Azure, takich jak obliczeniowych, sieci, Magazyn itp. podobne do nawigacji systemu plików.
Możesz użyć znanej [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure) do zarządzania tymi zasobami, niezależnie od dysku znajdują się w.
Wszelkie zmiany wprowadzone do zasobów platformy Azure, albo bezpośrednio w witrynie Azure portal lub za pomocą poleceń cmdlet programu Azure PowerShell są odzwierciedlane na dysku platformy Azure.  Możesz uruchomić `dir -Force` odświeżyć swoje zasoby.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Zarządzanie programem Exchange Online

Program PowerShell w usłudze Cloud Shell zawiera prywatne kompilacje moduł usługi Exchange Online.  Uruchom `Connect-EXOPSSession` można pobrać poleceń cmdlet programu Exchange.

![](media/features-powershell/exchangeonline.png)

 Uruchom polecenie `Get-Command -Module tmp_*`
> [!NOTE]
> Nazwa modułu powinien zaczynać się `tmp_`, jeśli zainstalowano moduły z tym samym prefiksem, ich poleceń cmdlet również zostaną wyświetlone. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Ścisła integracja z narzędziami open source

Usługa cloud Shell oferuje wstępnie skonfigurowane uwierzytelnianie dla narzędzi typu open source, takich jak narzędzia Terraform, Ansible i Chef InSpec. Wypróbuj ten produkt z przewodników przykładu.

## <a name="tools"></a>Narzędzia

|Category   |Name (Nazwa)   |
|---|---|
|Narzędzia systemu Linux            |Bash<br> zsh<br> SH<br> tmux<br> Postaraj się<br>               |
|Narzędzia platformy Azure            |[Interfejs wiersza polecenia Azure](https://github.com/Azure/azure-cli) i [klasycznego wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli)<br> [Narzędzie AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Interfejs wiersza polecenia usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Usługa Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Edytory tekstu           |Kod (Edytor usługi Cloud Shell)<br> vim<br> nano<br> emacs:    |
|Kontrola źródła         |git                    |
|Narzędzia do kompilacji            |Wprowadź<br> Narzędzia maven<br> npm<br> Narzędzie PIP         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [INTERFEJS WIERSZA POLECENIA DC/OS](https://github.com/dcos/dcos-cli)         |
|Bazy danych              |Klienta MySQL<br> Klientem PostgreSql<br> [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Inne                  |iPython Client<br> [Usługi cloud Foundry interfejsu wiersza polecenia](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Program chef InSpec](https://www.chef.io/inspec/)|

## <a name="language-support"></a>Obsługa języków

|Język   |Wersja   |
|---|---|
|.NET Core  |2.0.0       |
|Przejdź         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.2.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 i 3.5 (ustawienie domyślne)|

## <a name="next-steps"></a>Kolejne kroki
[Powłoka bash w przewodniku Szybki Start Cloud Shell](quickstart.md) <br>
[Program PowerShell w przewodniku Szybki Start Cloud Shell](quickstart-powershell.md) <br>
[Dowiedz się więcej o wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/) <br>
[Więcej informacji na temat programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>

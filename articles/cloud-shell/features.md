---
title: Bash w funkcji powłoki chmury Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji Bash w powłoce chmury Azure
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: juluk
ms.openlocfilehash: 3e71f2307ab68b2bc4694c7782f7a0f0d43b159b
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757046"
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Funkcje i narzędzia do Bash w powłoce chmury Azure

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Funkcje i narzędzia w [PowerShell](features-powershell.md) jest również dostępna.

Bash w chmurze powłoki działa na `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funkcje

### <a name="secure-automatic-authentication"></a>Bezpieczne uwierzytelnianie automatyczne

Bash w chmurze powłoki bezpiecznie i automatycznie służy do uwierzytelniania dostępu do konta dotyczące interfejsu wiersza polecenia platformy Azure w wersji 2.0.

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH do maszyn wirtualnych systemu Linux platformy Azure

Tworzenie maszyny Wirtualnej systemu Linux z 2.0 interfejsu wiersza polecenia platformy Azure można utworzyć domyślny klucz SSH i umieścić go w sieci `$Home` katalogu. Wprowadzenie do protokołu SSH klucze w `$Home` umożliwia połączeń SSH do maszyn wirtualnych systemu Linux platformy Azure bezpośrednio z powłoki chmury. Klucze są przechowywane w acc_<user>.img w udziale plików, stosować najlepsze rozwiązania lub udostępniania dostęp do udziału plików lub kluczy.

### <a name="home-persistence-across-sessions"></a>Trwałość $Home między sesjami

Aby zachować pliki między sesjami, powłoki chmury przeprowadzi Cię przez dołączenie udziału plików na platformę Azure przy pierwszym uruchomieniu.
Po ukończeniu powłoki chmury będą automatycznie dołączać magazynu (zainstalowany jako `$Home\clouddrive`) dla wszystkich przyszłych sesji.
Ponadto w Bash w powłoce chmurze Twoje `$Home` katalogu jest utrwalony jako .img w udziale plików Azure.
Pliki poza `$Home` i stan maszyny nie są zachowywane między sesjami.

[Dowiedz się więcej o plikach utrwalanie w Bash w powłoce chmury.](persisting-shell-storage.md)

### <a name="integration-with-open-source-tooling"></a>Integracja z narzędziami open source

Bash w chmurze powłoki obejmuje wstępnie skonfigurowane uwierzytelnianie open source narzędzi, takich jak Terraform, Ansible i Chef InSpec. Wypróbuj ją z wskazówki przykład.

## <a name="tools"></a>Narzędzia

|Kategoria   |Name (Nazwa)   |
|---|---|
|Narzędzia systemu Linux            |Bash<br> Pokaż<br> tmux<br> Dig<br>               |
|Narzędzia platformy Azure            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) i [1.0](https://github.com/Azure/azure-xplat-cli)<br> [Narzędzie AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Usługa Batch Shipyard](https://github.com/Azure/batch-shipyard) <br> [Interfejs wiersza polecenia usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) <br> [blobxfer](https://github.com/Azure/blobxfer#blobxfer) |
|Edytory tekstów           |vim<br> nano<br> emacs:       |
|Kontrola źródła         |git                    |
|Narzędzi do kompilacji            |Wprowadź<br> maven<br> npm<br> PIP         |
|Containers             |[Interfejs wiersza polecenia docker](https://github.com/docker/cli)/[Docker maszyny](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [INTERFEJS WIERSZA POLECENIA DC/OS](https://github.com/dcos/dcos-cli)         |
|Bazy danych              |Klienta MySQL<br> PostgreSql klienta<br> [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Inne                  |iPython klienta<br> [Chmura Foundry interfejsu wiersza polecenia](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>Obsługa języków

|Język   |Wersja   |
|---|---|
|.NET       |2.0.0       |
|Przejdź         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.0.2](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 i 3.5 (ustawienie domyślne)|

## <a name="next-steps"></a>Kolejne kroki
[Bash w chmurze powłoki — Szybki Start](quickstart.md) <br>
[Więcej informacji na temat usługi Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)

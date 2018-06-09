---
title: Włącz wiersza polecenia platformy Azure dla użytkowników stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać do zarządzania i wdrażania zasobów na stosie Azure międzyplatformowego interfejsu wiersza polecenia (CLI)
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: mabrigg
ms.openlocfilehash: d0103d211608514848da7d789d32d37d8385f33f
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247860"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Włącz wiersza polecenia platformy Azure dla użytkowników usługi Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Nie ma żadnych zadań specyficznych dla operatora stosu Azure, które można wykonywać przy użyciu wiersza polecenia platformy Azure. Jednak przed użytkownicy mogą zarządzać zasobami za pośrednictwem interfejsu wiersza polecenia, Operatorzy stosu Azure podać je z następujących czynności:

* **Certyfikat główny urzędu certyfikacji stosu Azure** jest wymagany, jeśli użytkownicy korzystają z interfejsu wiersza polecenia na stacji roboczej poza Azure stosu Development Kit.  

* **Punkt końcowy maszyny wirtualnej aliasy** zapewnia aliasu, takich jak "UbuntuLTS" lub "Win2012Datacenter", który odwołuje się do wydawcy obrazu, oferty, jednostki SKU i wersji jako pojedynczy parametr podczas wdrażania maszyn wirtualnych.  

W poniższych sekcjach opisano, jak uzyskać te wartości.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Wyeksportuj certyfikat główny urzędu certyfikacji Azure stosu

Certyfikat główny urzędu certyfikacji stosu Azure jest dostępna w zestawie i na maszynie wirtualnej dzierżawy, która działa w środowisku development kit. Aby wyeksportować certyfikat główny stos Azure w formacie PEM, zaloguj się do swojego zestawu rozwoju lub maszyna wirtualna dzierżawcy i uruchom następujący skrypt:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Konfigurowanie punktu końcowego aliasy maszyny wirtualnej

Operatory stosu Azure należy skonfigurować publicznie punktu końcowego, który znajduje się plik alias maszyny wirtualnej. Plik aliasu maszyny wirtualnej jest pliku JSON, który zawiera nazwę pospolitą dla obrazu. Następnie określono tej nazwy, po wdrożeniu maszyny Wirtualnej jako parametr wiersza polecenia platformy Azure.  

Przed dodaniem wpisu do pliku aliasu, upewnij się, że możesz [pobrać obrazów w witrynie Azure Marketplace](azure-stack-download-azure-marketplace-item.md), lub mieć [opublikowane niestandardowego obrazu](azure-stack-add-vm-image.md). W przypadku publikowania niestandardowego obrazu, zwróć uwagę na wydawcy, oferty, jednostki SKU i wersji informacji, które zostały określone podczas publikowania. Jeśli jest obrazu z witryny marketplace, można wyświetlić informacje przy użyciu ```Get-AzureVMImage``` polecenia cmdlet.  

A [przykładowy plik alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z obrazem wspólnej wiele aliasów jest dostępna. Można użyć który jako punktu wyjścia. Hostowanie tego pliku w miejscu, gdzie klienci interfejsu wiersza polecenia można uzyskać do niej dostęp. Jednym ze sposobów jest przechowywać plik w koncie magazynu obiektów blob i udostępniać adres URL użytkownikom:

1. Pobierz [przykładowy plik](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z usługi GitHub.
2. Utwórz nowe konto magazynu Azure stosu. Po zakończeniu tej operacji należy utworzyć nowy kontener obiektów blob. Ustawienie zasad dostępu do "public".  
3. Przekaż plik JSON do nowego kontenera. Po zakończeniu tej operacji można wyświetlić adres URL obiektu blob wybieraniu nazwy obiektów blob, a następnie wybierając adres URL z właściwości obiektu blob.

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie szablonów z wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)

- [Łączenie się z programem PowerShell](azure-stack-connect-powershell.md)

- [Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)

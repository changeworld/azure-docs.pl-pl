---
title: Włączanie interfejsu wiersza polecenia platformy Azure dla użytkowników usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać zasoby w usłudze Azure Stack i zarządzać nimi za pomocą międzyplatformowego interfejsu wiersza polecenia (CLI)
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
ms.date: 06/11/2018
ms.author: mabrigg
ms.openlocfilehash: 09c551ea7196ae20a60a5dd34c1cda889ff5df46
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091070"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Włączanie interfejsu wiersza polecenia platformy Azure dla użytkowników usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Certyfikat główny urzędu certyfikacji można udostępnić użytkownikom usługi Azure Stack pozwala używać wiersza polecenia platformy Azure na swoich komputerach deweloperskich. Użytkownicy muszą certyfikatów do zarządzania zasobami za pomocą interfejsu wiersza polecenia.

* **Certyfikat główny urzędu certyfikacji usługi Azure Stack** jest wymagany, jeśli użytkownicy korzystają z interfejsu wiersza polecenia na stacji roboczej poza Azure Stack Development Kit.  

* **Punkt końcowy aliasy maszyny wirtualnej** zawiera alias, takich jak "UbuntuLTS" lub "Win2012Datacenter", który odwołuje się do wydawcy obrazu, oferty, jednostki SKU i wersji jako pojedynczy parametr podczas wdrażania maszyn wirtualnych.  

W następujących sekcjach opisano sposób uzyskiwania tych wartości.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Wyeksportuj certyfikat głównego urzędu certyfikacji usługi Azure Stack

Certyfikat główny urzędu certyfikacji usługi Azure Stack można znaleźć w zestawie deweloperskim i na maszynie wirtualnej dzierżawy, który jest uruchomiony w środowisku deweloperskim zestaw. Aby wyeksportować certyfikat główny usługi Azure Stack w formacie PEM, zaloguj się do zestawu SDK usługi lub maszyny wirtualnej dzierżawcy, a następnie uruchom następujący skrypt:

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

Operatorzy usługi Azure Stack, należy skonfigurować publicznie dostępnym punkcie końcowym hostujący plik alias maszyny wirtualnej. Plik alias maszyny wirtualnej znajduje się plik w formacie JSON, który zawiera nazwę pospolitą obrazu. Następnie określono tej nazwy, podczas wdrażania maszyny Wirtualnej jako parametr wiersza polecenia platformy Azure.  

Przed dodaniem wpis do pliku aliasu, upewnij się, że możesz [pobieranie obrazów w portalu Azure Marketplace](azure-stack-download-azure-marketplace-item.md), lub [opublikowane swój własny obraz niestandardowy](azure-stack-add-vm-image.md). Jeśli publikujesz niestandardowego obrazu, zanotuj informacje o wydawcy, oferty, jednostki SKU i wersji, które zostały określone podczas publikowania. Jeśli jest to obraz z witryny marketplace, można wyświetlić informacje przy użyciu ```Get-AzureVMImage``` polecenia cmdlet.  

A [przykładowy plik alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) za pomocą wielu typowych obrazu aliasów jest dostępna. Używając, jako punktu wyjścia. Hostowanie tego pliku w miejscu, gdzie klienci interfejsu wiersza polecenia można uzyskiwać dostęp go niego. Jednym ze sposobów jest do hostowania plików na koncie usługi blob storage, a następnie Udostępnij adres URL z użytkownikami:

1. Pobierz [przykładowy plik](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z usługi GitHub.
2. Utwórz nowe konto magazynu w usłudze Azure Stack. Gdy zostanie to zrobione, należy utworzyć nowy kontener obiektów blob. Ustawienie zasad dostępu do "publicznej".  
3. Przekaż plik JSON do nowego kontenera. Gdy zostanie to zrobione, możesz wyświetlić adres URL obiektu blob przy wybieraniu nazwy obiektu blob, a następnie wybierając adres URL z właściwości obiektu blob.

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie szablonów za pomocą wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)

- [Łączenie się z programem PowerShell](azure-stack-connect-powershell.md)

- [Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)

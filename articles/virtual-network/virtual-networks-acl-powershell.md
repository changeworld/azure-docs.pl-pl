---
title: Zarządzanie listami kontroli dostępu do punktu końcowego platformy Azure | Program PowerShell | Klasyczny | Microsoft Docs
description: Dowiedz się, jak zarządzać listami ACL przy użyciu programu PowerShell
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056771"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Zarządzanie listami kontroli dostępu punktów końcowych przy użyciu programu PowerShell w klasycznym modelu wdrażania
Możesz tworzyć listy Access Control sieciowych (ACL) i zarządzać nimi dla punktów końcowych, używając Azure PowerShell lub w portal zarządzania. W tym temacie znajdziesz procedury dotyczące typowych zadań ACL, które można wykonać za pomocą programu PowerShell. Aby uzyskać listę poleceń cmdlet Azure PowerShell, zobacz [polecenia cmdlet zarządzania platformy Azure](https://go.microsoft.com/fwlink/?LinkId=317721). Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [co to jest lista Access Control sieciowych (ACL)?](virtual-networks-acl.md) Jeśli chcesz zarządzać listami ACL przy użyciu portal zarządzania, zobacz [jak skonfigurować punkty końcowe na maszynie wirtualnej](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Zarządzanie listami ACL sieci przy użyciu Azure PowerShell
Za pomocą poleceń cmdlet Azure PowerShell można tworzyć, usuwać i konfigurować listy Access Control sieci (ACL). Dodaliśmy kilka przykładów niektórych sposobów konfigurowania listy ACL przy użyciu programu PowerShell.

Aby pobrać kompletną listę poleceń cmdlet programu PowerShell dla listy ACL, można użyć jednej z następujących czynności:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Tworzenie listy ACL sieci z regułami, które zezwalają na dostęp z podsieci zdalnej
Poniższy przykład ilustruje sposób tworzenia nowej listy ACL zawierającej reguły. Ta lista kontroli dostępu zostanie następnie zastosowana do punktu końcowego maszyny wirtualnej. Reguły listy ACL w poniższym przykładzie umożliwią dostęp z podsieci zdalnej. Aby utworzyć nową listę ACL sieci z regułami zezwalania dla podsieci zdalnej, Otwórz Azure PowerShell ISE. Skopiuj i wklej poniższy skrypt, konfigurując skrypt przy użyciu własnych wartości, a następnie uruchom skrypt.

1. Utwórz nowy obiekt listy ACL sieci.
   
        $acl1 = New-AzureAclConfig
2. Ustaw regułę, która zezwala na dostęp z podsieci zdalnej. W poniższym przykładzie ustawisz regułę *100* (która ma priorytet nad regułą 200 lub wyższą), aby umożliwić zdalne podsieci *10.0.0.0/8* dostęp do punktu końcowego maszyny wirtualnej. Zastąp wartości własnymi wymaganiami dotyczącymi konfiguracji. Nazwa "Konfiguracja listy ACL programu SharePoint" powinna zostać zamieniona na przyjazną nazwę, która ma być wywoływana przez tę regułę.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Aby uzyskać dodatkowe reguły, powtórz polecenie cmdlet, zastępując wartości własnymi wymaganiami dotyczącymi konfiguracji. Należy zmienić kolejność numerów reguł, aby odzwierciedlała kolejność, w jakiej mają być stosowane reguły. Niższy numer reguły ma pierwszeństwo przed większą liczbą.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Następnie można utworzyć nowy punkt końcowy (Dodaj) lub ustawić listę kontroli dostępu dla istniejącego punktu końcowego (zestawu). W tym przykładzie dodamy nowy punkt końcowy maszyny wirtualnej o nazwie "Web" i zaktualizujesz punkt końcowy maszyny wirtualnej za pomocą ustawień listy ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Następnie połącz polecenia cmdlet i uruchom skrypt. W tym przykładzie połączone polecenia cmdlet będą wyglądać następująco:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Usuń regułę listy ACL sieci, która zezwala na dostęp z podsieci zdalnej
Poniższy przykład ilustruje sposób usuwania reguły listy ACL sieci.  Aby usunąć regułę listy ACL sieci z regułami zezwolenia dla podsieci zdalnej, Otwórz Azure PowerShell ISE. Skopiuj i wklej poniższy skrypt, konfigurując skrypt przy użyciu własnych wartości, a następnie uruchom skrypt.

1. Pierwszym krokiem jest uzyskanie obiektu listy ACL sieci dla punktu końcowego maszyny wirtualnej. Następnie usuniesz regułę listy ACL. W tym przypadku usuwamy go według identyfikatora reguły. Spowoduje to usunięcie tylko reguły o IDENTYFIKATORze 0 z listy ACL. Nie usuwa obiektu ACL z punktu końcowego maszyny wirtualnej.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Następnie należy zastosować obiekt listy ACL sieci do punktu końcowego maszyny wirtualnej i zaktualizować maszynę wirtualną.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Usuwanie listy ACL sieci z punktu końcowego maszyny wirtualnej
W niektórych scenariuszach może być konieczne usunięcie obiektu listy ACL sieci z punktu końcowego maszyny wirtualnej. Aby to zrobić, Otwórz Azure PowerShell ISE. Skopiuj i wklej poniższy skrypt, konfigurując skrypt przy użyciu własnych wartości, a następnie uruchom skrypt.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
[Co to jest lista Access Control sieciowych?](virtual-networks-acl.md)


---
title: Zarządzanie listami kontroli dostępu do punktu końcowego platformy Azure | PowerShell | Klasycznym | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać listy kontroli dostępu przy użyciu programu PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: c3476908447380ccd7e8b9c0f1c2a55ae763cc1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23928853"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Zarządzanie listami kontroli dostępu do punktu końcowego w klasycznym modelu wdrażania przy użyciu programu PowerShell
Można tworzyć i zarządzać sieci listy kontroli dostępu (ACL) dla punktów końcowych przy użyciu programu Azure PowerShell lub w portalu zarządzania. W tym temacie znajdziesz procedury ACL typowych zadań, które można wykonać przy użyciu programu PowerShell. Lista programu Azure PowerShell polecenia cmdlet zobacz [poleceń cmdlet do zarządzania Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Aby uzyskać więcej informacji na temat listy kontroli dostępu, zobacz [co to jest sieć listy kontroli dostępu (ACL)?](virtual-networks-acl.md). Jeśli chcesz zarządzać z listy ACL za pomocą portalu zarządzania, zobacz [jak się punkty końcowe z maszyną wirtualną](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Zarządzanie listy kontroli dostępu w sieci za pomocą programu Azure PowerShell
Można użyć poleceń cmdlet programu Azure PowerShell do tworzenia, usuwania i konfigurowania (set) sieci listy kontroli dostępu (ACL). Kilka przykładów niektóre metody można skonfigurować listy ACL za pomocą środowiska PowerShell zostały włączone.

Aby uzyskać pełną listę poleceń cmdlet programu PowerShell listy ACL, użyj jednej z następujących czynności:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Utwórz ACL sieci przy użyciu reguł, które zezwalają na dostęp z podsieci zdalnej
W poniższym przykładzie przedstawiono sposób tworzenia nowej listy ACL, który zawiera reguły. Ta lista ACL jest następnie stosowany do punktu końcowego maszyny wirtualnej. Reguły list kontroli dostępu w poniższym przykładzie zezwoli na dostęp z podsieci zdalnej. Aby utworzyć nową listę ACL sieci przy użyciu reguł zezwalania na dla podsieci zdalnej, Otwórz program Azure PowerShell ISE. Skopiuj i wklej skrypt poniżej, konfigurując skrypt z własne wartości, a następnie uruchom skrypt.

1. Utwórz nowy obiekt listy kontroli dostępu w sieci.
   
        $acl1 = New-AzureAclConfig
2. Ustaw regułę, która pozwala na dostęp z podsieci zdalnej. W poniższym przykładzie Ustaw regułę *100* (które mają pierwszeństwo przed reguły 200 i nowsze) umożliwiają podsieci zdalnej *10.0.0.0/8* dostęp do punktu końcowego maszyny wirtualnej. Zastąp wartości z wymaganiami konfiguracji. Nazwa "Konfiguracji ACL programu SharePoint" należy zastąpić o przyjaznej nazwie, która ma zostać wywołana tej reguły.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Dodatkowe zasady powtórz polecenie cmdlet, zastępując wartości z wymaganiami konfiguracji. Należy zmienić reguły number zamówienie do kolejności, w której ma zostać zasad, które mają być stosowane. Niższy numer reguły mają pierwszeństwo przed większej liczby.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Następnie można utworzyć nowy punkt końcowy (Dodaj) lub ustawić listę kontroli dostępu dla istniejącego punktu końcowego (Set). W tym przykładzie możemy dodać nowy punkt końcowy maszyny wirtualnej o nazwie "web" i zaktualizować punktu końcowego maszyny wirtualnej z ustawieniami listy kontroli dostępu.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Następnie należy połączyć polecenia cmdlet i uruchom skrypt. Na przykład połączonych poleceń cmdlet będzie wyglądać następująco:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Usuń regułę listy ACL sieci, która pozwala na dostęp z podsieci zdalnej
W poniższym przykładzie przedstawiono sposób, aby usunąć regułę listy ACL sieci.  Aby usunąć regułę listy ACL sieci przy użyciu reguł zezwalania na dla podsieci zdalnej, Otwórz program Azure PowerShell ISE. Skopiuj i wklej skrypt poniżej, konfigurując skrypt z własne wartości, a następnie uruchom skrypt.

1. Pierwszym krokiem jest można pobrać obiektu ACL sieci dla punktu końcowego maszyny wirtualnej. Aby następnie usunąć reguły listy ACL. W takim przypadku możemy usunięcie go przez identyfikator reguły. Identyfikator reguły 0 spowoduje tylko usunięcie z listy ACL. Nie powoduje usunięcia obiektu ACL z punktu końcowego maszyny wirtualnej.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Następnie należy dotyczą obiektu sieciowej listy ACL punktu końcowego maszyny wirtualnej i zaktualizować maszyny wirtualnej.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Usuń ACL sieci z punktu końcowego maszyny wirtualnej
W pewnych sytuacjach może chcesz usunąć obiekt listy kontroli dostępu w sieci z punktu końcowego maszyny wirtualnej. Aby to zrobić, Otwórz program Azure PowerShell ISE. Skopiuj i wklej skrypt poniżej, konfigurując skrypt z własne wartości, a następnie uruchom skrypt.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
[Co to jest sieć listy kontroli dostępu (ACL)?](virtual-networks-acl.md)


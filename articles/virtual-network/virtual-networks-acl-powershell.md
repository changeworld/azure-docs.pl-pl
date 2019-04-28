---
title: Zarządzanie listami kontroli dostępu do punktu końcowego platformy Azure | Program PowerShell | Klasyczny | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie listami ACL, przy użyciu programu PowerShell
services: virtual-network
documentationcenter: na
author: genlin
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1f6c14c15d4930902ced642bd02d1d1833e0b361
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032541"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Zarządzanie listami kontroli dostępu do punktu końcowego w klasycznym modelu wdrażania przy użyciu programu PowerShell
Można tworzyć i zarządzać sieci list kontroli dostępu (ACL) dla punktów końcowych przy użyciu programu Azure PowerShell lub w portalu zarządzania. W tym temacie znajdziesz procedury dla listy ACL typowych zadań, które można wykonać przy użyciu programu PowerShell. Na liście programu Azure PowerShell polecenia cmdlet zobacz [poleceń cmdlet do zarządzania platformy Azure](https://go.microsoft.com/fwlink/?LinkId=317721). Aby uzyskać więcej informacji na temat list ACL, zobacz [co to jest sieć kontroli listy dostępu (ACL)?](virtual-networks-acl.md). Jeśli chcesz zarządzać Twojej listy kontroli dostępu za pomocą portalu zarządzania, zobacz [jak się punkty końcowe do maszyny wirtualnej](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Zarządzanie listami ACL sieci przy użyciu programu Azure PowerShell
Można użyć poleceń cmdlet programu Azure PowerShell do tworzenia, usuwania i konfigurowania (set) sieci list kontroli dostępu (ACL). Wprowadziliśmy kilka przykładów niektóre metody, można skonfigurować listy ACL za pomocą programu PowerShell.

Aby uzyskać pełną listę poleceń cmdlet programu PowerShell listy ACL, można użyć jednej z następujących czynności:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Tworzenie listy ACL sieci przy użyciu reguły zezwalające na dostęp z podsieci zdalnej
W poniższym przykładzie przedstawiono sposób tworzenia nowej listy kontroli dostępu, który zawiera reguły. Ta lista ACL jest następnie stosowany do punktu końcowego maszyny wirtualnej. Reguły listy ACL w poniższym przykładzie zezwoli na dostęp z podsieci zdalnej. Aby utworzyć nową listę ACL sieci przy użyciu reguły zezwolenia dla podsieci zdalnej, Otwórz program Azure PowerShell ISE. Skopiuj i wklej skrypt poniżej, konfigurując skrypt przy użyciu własnych wartości, a następnie uruchom skrypt.

1. Utwórz nowy obiekt listy ACL sieci.
   
        $acl1 = New-AzureAclConfig
2. Ustaw regułę, która zezwala na dostęp z podsieci zdalnej. W poniższym przykładzie jest ustawić regułę *100* (które mają pierwszeństwo przed reguły 200 lub nowszy) aby umożliwić podsieci zdalnej *10.0.0.0/8* dostęp do punktu końcowego maszyny wirtualnej. Zastąp wartości z wymaganiami konfiguracji. Nazwa "Konfiguracji listy ACL programu SharePoint" należy zastąpić z przyjazną nazwą, którą chcesz wywołać tę regułę.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Dodatkowe reguły powtórz polecenie cmdlet, zastępując wartości z wymaganiami konfiguracji. Pamiętaj zmienić reguły numer zamówienia na odzwierciedla kolejność, w której chcesz reguł, które mają być stosowane. Niższy numer reguły mają pierwszeństwo przed większej liczby.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Następnie można utworzyć nowy punkt końcowy (Dodaj) lub ustawić listy ACL dla istniejącego punktu końcowego (Set). W tym przykładzie możemy dodać nowy punkt końcowy maszyny wirtualnej o nazwie "web" i zaktualizować ustawienia listy ACL punktu końcowego maszyny wirtualnej.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Następnie należy połączyć polecenia cmdlet, a następnie uruchom skrypt. W tym przykładzie łączny poleceń cmdlet będzie wyglądać następująco:
   
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
W poniższym przykładzie przedstawiono sposób, aby usunąć regułę listy ACL sieci.  Aby usunąć regułę listy ACL sieci za pomocą reguły zezwolenia dla podsieci zdalnej, Otwórz program Azure PowerShell ISE. Skopiuj i wklej skrypt poniżej, konfigurując skrypt przy użyciu własnych wartości, a następnie uruchom skrypt.

1. Pierwszym krokiem jest Pobierz obiekt sieci listy ACL dla punktu końcowego maszyny wirtualnej. Następnie będzie usunąć regułę listy ACL. W tym przypadku możemy usunięcie go za pomocą identyfikatora reguły. Spowoduje to usunięcie tylko identyfikator reguły 0 z listy ACL. Nie powoduje usunięcia obiektu listy ACL z punktu końcowego maszyny wirtualnej.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Następnie należy zastosować obiekt listy ACL sieci do punktu końcowego maszyny wirtualnej i zaktualizuj maszynę wirtualną.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Usuń listę ACL sieci z punktu końcowego maszyny wirtualnej
W niektórych scenariuszach może chcesz usunąć obiekt listy ACL sieci z punktu końcowego maszyny wirtualnej. Aby to zrobić, Otwórz program Azure PowerShell ISE. Skopiuj i wklej skrypt poniżej, konfigurując skrypt przy użyciu własnych wartości, a następnie uruchom skrypt.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Kolejne kroki
[Co to jest sieć kontroli listy dostępu (ACL)?](virtual-networks-acl.md)


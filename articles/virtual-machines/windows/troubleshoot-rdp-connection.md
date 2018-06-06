---
title: Nie można połączyć z protokołem RDP do maszyny Wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów, gdy nie można połączyć się z maszyną wirtualną systemu Windows na platformie Azure przy użyciu pulpitu zdalnego
keywords: Błąd pulpitu zdalnego, błąd połączeń usług pulpitu zdalnego, nie można połączyć z maszyną wirtualną, rozwiązywania problemów pulpitu zdalnego
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: danis
ms.openlocfilehash: c1444901fa46a62761d6b94ccb8e7ea3ff3d057f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701891"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Rozwiązywanie problemów z połączeniami pulpitu zdalnego do maszyny wirtualnej platformy Azure
Połączenia protokołu RDP (Remote Desktop) do systemu Windows Azure maszyny wirtualnej (VM) może zakończyć się niepowodzeniem z różnych powodów, pozostawiając użytkownik mógł uzyskać dostępu do maszyny Wirtualnej. Problem może być usługą pulpitu zdalnego na Maszynie wirtualnej, połączenie sieciowe lub klienta pulpitu zdalnego na komputerze hosta. W tym artykule przedstawiono niektóre z najczęściej metod, aby rozwiązać problemy z połączeniami RDP. 

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Szybkie kroki rozwiązywania problemów
Po wykonaniu każdego kroku rozwiązywania problemów spróbuj połączyć się ponownie do maszyny Wirtualnej:

1. Zresetuj konfigurację pulpitu zdalnego.
2. Grupy zabezpieczeń sieci Sprawdź zasady / w chmurze usługi punktów końcowych.
3. Przejrzyj dzienniki konsoli maszyny Wirtualnej.
4. Resetuj karty Sieciowej dla maszyny Wirtualnej.
5. Sprawdź kondycję zasobu maszyny Wirtualnej.
6. Resetowanie hasła maszyny Wirtualnej.
7. Ponowne uruchomienie maszyny Wirtualnej.
8. Należy ponownie wdrożyć maszyny Wirtualnej.

Aby uzyskać bardziej szczegółowy opis kroków i wyjaśnienia, Kontynuuj lekturę. Sprawdź ten lokalnych urządzeń sieciowych, takich jak routery i zapory nie blokują ruchu wychodzącego TCP portu 3389, zgodnie z opisem w [szczegółowe RDP scenariuszach rozwiązywania problemów z](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Jeśli **Connect** przycisk dla maszyny Wirtualnej jest wyszarzony limit w portalu i nie są połączone z platformą Azure [Express Route](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) połączenia, należy utworzyć i przypisać maszynie Wirtualnej publiczny adres IP, zanim będzie możliwe użycie protokołu RDP. Więcej informacji o [publicznych adresach IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Sposoby rozwiązywania problemów protokołu RDP
Można rozwiązać, maszyny wirtualne utworzone przy użyciu modelu wdrażania usługi Resource Manager przy użyciu jednej z następujących metod:

* [Azure portal](#using-the-azure-portal) — jest to doskonały, jeśli chcesz szybko zresetować RDP konfiguracji lub poświadczenia i nie być zainstalowane narzędzia Azure.
* [Program Azure PowerShell](#using-azure-powershell) — Jeśli masz doświadczenia z wiersza polecenia programu PowerShell szybko zresetować RDP konfiguracji lub poświadczenia użytkownika przy użyciu poleceń cmdlet programu Azure PowerShell.

Możesz również znaleźć kroki dotyczące rozwiązywania problemów z maszyn wirtualnych utworzonych przy użyciu [klasycznego modelu wdrożenia](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu portalu Azure
Każdy krok rozwiązywania problemów a następnie spróbuj ponownie nawiązać połączenie z maszyną Wirtualną. Jeśli nadal nie można połączyć się, przejdź do następnego kroku.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów spowoduje zresetowanie konfiguracji RDP podczas połączenia zdalne są wyłączone lub reguł zapory systemu Windows blokuje RDP, na przykład.
   
    Wybierz maszyny Wirtualnej w portalu Azure. Przewiń w dół okienko ustawienia, aby **pomocy technicznej i rozwiązywania problemów** sekcji w dolnej części listy. Kliknij przycisk **resetowania hasła** przycisku. Ustaw **tryb** do **Resetowanie tylko konfiguracji** , a następnie kliknij przycisk **aktualizacji** przycisk:
   
    ![Zresetowanie konfiguracji RDP w portalu Azure](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Zasady grupy zabezpieczeń sieci Sprawdź**. Użyj funkcji [weryfikacji przepływu adresu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) w celu potwierdzenia, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Można również przejrzeć efektywnym elementem systemu zabezpieczeń zasady grupy w celu zapewnienia ruchu przychodzącego "Zezwalaj" NSG reguły istnieje i jest priorytety dla portu protokołu RDP (ustawienie domyślne 3389). Aby uzyskać więcej informacji, zobacz [przepływu ruchu przy użyciu skuteczne reguły zabezpieczeń rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Przejrzyj diagnostyki rozruchu maszyny Wirtualnej**. Ten krok rozwiązywania problemów przegląda dzienniki konsoli maszyny Wirtualnej, aby określić, czy maszyna wirtualna jest zgłoszenie problemu. Nie wszystkie maszyny wirtualne mają diagnostyki rozruchu włączone, więc ten krok rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale może wskazywać na problem szersze, który ma wpływ na połączenie RDP. Aby uzyskać więcej informacji na temat przeglądania dzienniki konsoli i zrzut ekranu maszyny Wirtualnej, zobacz [diagnostyki rozruchu dla maszyn wirtualnych](boot-diagnostics.md).

4. **Resetuj karty Sieciowej dla maszyny Wirtualnej**. Aby uzyskać więcej informacji, zobacz [sposób resetowania karty Sieciowej dla maszyny Wirtualnej systemu Windows Azure](reset-network-interface.md).
5. **Sprawdź kondycję zasobu maszyny Wirtualnej**. Ten krok rozwiązywania problemów sprawdza, czy nie ma żadnych znanych problemów z platformą Azure, które mogą mieć wpływ na połączenie z maszyną wirtualną.
   
    Wybierz maszyny Wirtualnej w portalu Azure. Przewiń w dół okienko ustawienia, aby **pomocy technicznej i rozwiązywania problemów** sekcji w dolnej części listy. Kliknij przycisk **kondycja zasobów** przycisku. Raporty dobrej kondycji maszyny Wirtualnej jako **dostępne**:
   
    ![Sprawdź kondycję zasobu maszyny Wirtualnej w portalu Azure](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Resetowanie poświadczeń użytkownika**. Ten krok rozwiązywania problemów resetuje hasło konta administratora lokalnego nie wiesz lub zapomniał poświadczenia.  Gdy użytkownik zalogował się do maszyny Wirtualnej, należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszyny Wirtualnej w portalu Azure. Przewiń w dół okienko ustawienia, aby **pomocy technicznej i rozwiązywania problemów** sekcji w dolnej części listy. Kliknij przycisk **resetowania hasła** przycisku. Upewnij się, że **tryb** ustawiono **resetowania hasła** , a następnie wprowadź nazwy użytkownika i nowe hasło. Na koniec kliknij **aktualizacji** przycisk:
   
    ![Resetowanie poświadczeń użytkownika w portalu Azure](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Ponowne uruchomienie maszyny Wirtualnej**. Ten krok rozwiązywania problemów można rozwiązać problemów źródłowy ma samej maszyny Wirtualnej.
   
    Wybierz maszyny Wirtualnej w portalu Azure i kliknij przycisk **omówienie** kartę. Kliknij przycisk **ponowne uruchomienie** przycisk:
   
    ![Uruchom ponownie maszynę Wirtualną w portalu Azure](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Wdrożenie maszyny Wirtualnej**. Ten krok rozwiązywania problemów wdraża ponownie maszyny Wirtualnej do innego hosta w obrębie platformy Azure, aby poprawić wszelkie podstawowej platformy lub problemy z siecią.
   
    Wybierz maszyny Wirtualnej w portalu Azure. Przewiń w dół okienko ustawienia, aby **pomocy technicznej i rozwiązywania problemów** sekcji w dolnej części listy. Kliknij przycisk **ponownie wdrożyć** przycisk, a następnie kliknij przycisk **ponownie wdrożyć**:
   
    ![Wdrożenie maszyny Wirtualnej w portalu Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po zakończeniu tej operacji, tymczasowych dysku dane zostaną utracone, a zaktualizowane dynamicznych adresów IP, które są skojarzone z maszyną Wirtualną.

9. **Sprawdź routing**. Użyj obserwatora sieciowego [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) możliwości, aby upewnić się, czy trasa nie jest uniemożliwia ruchu z rozsyłane do lub z maszyny wirtualnej. Można również przejrzeć skuteczne trasy, aby zobaczyć wszystkie skuteczne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [przepływu ruchu trasy efektywne korzystanie rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-routing-problem.md).

10. Upewnij się, że wszelkie zapory lokalnymi lub zapora na komputerze, zezwala na ruch wychodzący TCP 3389 na platformie Azure.

Jeśli nadal wystąpią problemy RDP, możesz [otwarcia żądania pomocy technicznej](https://azure.microsoft.com/support/options/) lub odczytać [bardziej szczegółowe Rozwiązywanie problemów z założenia i kroki RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Rozwiązywanie problemów przy użyciu programu Azure PowerShell
Jeśli nie jest jeszcze, [Instalowanie i konfigurowanie najnowsze programu Azure PowerShell](/powershell/azure/overview).

W poniższych przykładach użyto zmiennych `myResourceGroup`, `myVM`, i `myVMAccessExtension`. Zastąp własne wartości tych zmiennych nazwy i lokalizacje.

> [!NOTE]
> Resetowanie poświadczeń użytkownika i konfiguracji RDP za pomocą [AzureRmVMAccessExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmaccessextension) polecenia cmdlet programu PowerShell. W poniższych przykładach `myVMAccessExtension` jest nazwą, określony jako część procesu. Jeśli wcześniej mający doświadczenie z VMAccessAgent, możesz pobrać nazwę rozszerzenia istniejącej przy użyciu `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` do sprawdzenia właściwości maszyny wirtualnej. Aby wyświetlić nazwę, poszukaj w sekcji "Rozszerzenia" w danych wyjściowych.

Każdy krok rozwiązywania problemów a następnie spróbuj ponownie nawiązać połączenie z maszyną Wirtualną. Jeśli nadal nie można połączyć się, przejdź do następnego kroku.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów spowoduje zresetowanie konfiguracji RDP podczas połączenia zdalne są wyłączone lub reguł zapory systemu Windows blokuje RDP, na przykład.
   
    W przykładzie poniżej resetuje połączenie RDP na maszynie Wirtualnej o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Zasady grupy zabezpieczeń sieci Sprawdź**. Ten krok rozwiązywania problemów sprawdza, czy zastosowano reguły w sieciowej grupy zabezpieczeń w celu zezwolenia na ruch RDP. Domyślnym portem dla protokołu RDP jest portu 3389 protokołu TCP. Regułę zezwalającą na ruch RDP nie mogą być tworzone automatycznie podczas tworzenia maszyny Wirtualnej.
   
    Najpierw należy przypisać wszystkie dane konfiguracyjne dla sieciowej grupy zabezpieczeń do `$rules` zmiennej. Poniższy przykład uzyskuje informacje o sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup` w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Teraz Wyświetl reguły, które są skonfigurowane dla tej grupy zabezpieczeń sieci. Sprawdź, czy reguła istnieje umożliwia portu 3389 protokołu TCP dla połączeń przychodzących w następujący sposób:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Poniższy przykład przedstawia regułę podmioty zabezpieczeń, która zezwala na ruch RDP. Widać `Protocol`, `DestinationPortRange`, `Access`, i `Direction` są prawidłowo skonfigurowane:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Jeśli nie masz regułę zezwalającą na ruch RDP, [utworzyć regułę grupy zabezpieczeń sieci](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Zezwalaj na portu 3389 protokołu TCP.
3. **Resetowanie poświadczeń użytkownika**. Ten krok rozwiązywania problemów resetuje hasło konta administratora lokalnego, określona podczas pewności lub zapomniał poświadczenia.
   
    Najpierw określ nazwę użytkownika i nowe hasło, przypisując poświadczeń w celu `$cred` zmiennej w następujący sposób:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Teraz Zaktualizuj poświadczenia na maszynie Wirtualnej. Poniższy przykład aktualizuje poświadczenia na maszynie Wirtualnej o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Ponowne uruchomienie maszyny Wirtualnej**. Ten krok rozwiązywania problemów można rozwiązać problemów źródłowy ma samej maszyny Wirtualnej.
   
    Poniższy przykład ponowne uruchomienie maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Wdrożenie maszyny Wirtualnej**. Ten krok rozwiązywania problemów wdraża ponownie maszyny Wirtualnej do innego hosta w obrębie platformy Azure, aby poprawić wszelkie podstawowej platformy lub problemy z siecią.
   
    Poniższy przykład wdraża ponownie maszyny Wirtualnej o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Sprawdź routing**. Użyj obserwatora sieciowego [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) możliwości, aby upewnić się, czy trasa nie jest uniemożliwia ruchu z rozsyłane do lub z maszyny wirtualnej. Można również przejrzeć skuteczne trasy, aby zobaczyć wszystkie skuteczne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [przepływu ruchu trasy efektywne korzystanie rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-routing-problem.md).

7. Upewnij się, że wszelkie zapory lokalnymi lub zapora na komputerze, zezwala na ruch wychodzący TCP 3389 na platformie Azure.

Jeśli nadal wystąpią problemy RDP, możesz [otwarcia żądania pomocy technicznej](https://azure.microsoft.com/support/options/) lub odczytać [bardziej szczegółowe Rozwiązywanie problemów z założenia i kroki RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Rozwiązywanie problemów z maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrożenia
Po wykonaniu każdego kroku rozwiązywania problemów spróbuj połączyć się ponownie do maszyny Wirtualnej.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów spowoduje zresetowanie konfiguracji RDP podczas połączenia zdalne są wyłączone lub reguł zapory systemu Windows blokuje RDP, na przykład.
   
    Wybierz maszyny Wirtualnej w portalu Azure. Kliknij przycisk **... Więcej** przycisk, a następnie kliknij przycisk **Resetuj dostęp zdalny**:
   
    ![Zresetowanie konfiguracji RDP w portalu Azure](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Sprawdzanie punktów końcowych usługi w chmurze**. Ten krok rozwiązywania problemów sprawdza mają punkty końcowe w celu zezwolenia na ruch RDP usług w chmurze. Domyślnym portem dla protokołu RDP jest portu 3389 protokołu TCP. Regułę zezwalającą na ruch RDP nie mogą być tworzone automatycznie podczas tworzenia maszyny Wirtualnej.
   
   Wybierz maszyny Wirtualnej w portalu Azure. Kliknij przycisk **punkty końcowe** przycisk, aby wyświetlić punkty końcowe obecnie skonfigurowane dla maszyny Wirtualnej. Sprawdź, czy istnieją punkty końcowe, które zezwalają na ruch RDP na portu 3389 protokołu TCP.
   
   W poniższym przykładzie przedstawiono prawidłowe punkty końcowe, które zezwala na ruch RDP:
   
   ![Sprawdzanie punktów końcowych usługi w chmurze w portalu Azure](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Jeśli nie masz punktu końcowego, który zezwala na ruch RDP, [Tworzenie punktu końcowego usługi w chmurze](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Umożliwić ruch TCP do prywatnego portu 3389.
3. **Przejrzyj diagnostyki rozruchu maszyny Wirtualnej**. Ten krok rozwiązywania problemów przegląda dzienniki konsoli maszyny Wirtualnej, aby określić, czy maszyna wirtualna jest zgłoszenie problemu. Nie wszystkie maszyny wirtualne mają diagnostyki rozruchu włączone, więc ten krok rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale może wskazywać na problem szersze, który ma wpływ na połączenie RDP. Aby uzyskać więcej informacji na temat przeglądania dzienniki konsoli i zrzut ekranu maszyny Wirtualnej, zobacz [diagnostyki rozruchu dla maszyn wirtualnych](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Sprawdź kondycję zasobu maszyny Wirtualnej**. Ten krok rozwiązywania problemów sprawdza, czy nie ma żadnych znanych problemów z platformą Azure, które mogą mieć wpływ na połączenie z maszyną wirtualną.
   
    Wybierz maszyny Wirtualnej w portalu Azure. Przewiń w dół okienko ustawienia, aby **pomocy technicznej i rozwiązywania problemów** sekcji w dolnej części listy. Kliknij przycisk **kondycja zasobów** przycisku. Raporty dobrej kondycji maszyny Wirtualnej jako **dostępne**:
   
    ![Sprawdź kondycję zasobu maszyny Wirtualnej w portalu Azure](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Resetowanie poświadczeń użytkownika**. Ten krok rozwiązywania problemów resetuje hasło konta administratora lokalnego, określona podczas pewności lub pamiętasz poświadczenia.  Gdy użytkownik zalogował się do maszyny Wirtualnej, należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszyny Wirtualnej w portalu Azure. Przewiń w dół okienko ustawienia, aby **pomocy technicznej i rozwiązywania problemów** sekcji w dolnej części listy. Kliknij przycisk **resetowania hasła** przycisku. Wprowadź nazwy użytkownika i nowe hasło. Na koniec kliknij **zapisać** przycisk:
   
    ![Resetowanie poświadczeń użytkownika w portalu Azure](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Ponowne uruchomienie maszyny Wirtualnej**. Ten krok rozwiązywania problemów można rozwiązać problemów źródłowy ma samej maszyny Wirtualnej.
   
    Wybierz maszyny Wirtualnej w portalu Azure i kliknij przycisk **omówienie** kartę. Kliknij przycisk **ponowne uruchomienie** przycisk:
   
    ![Uruchom ponownie maszynę Wirtualną w portalu Azure](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Upewnij się, że wszelkie zapory lokalnymi lub zapora na komputerze, zezwala na ruch wychodzący TCP 3389 na platformie Azure.

Jeśli nadal wystąpią problemy RDP, możesz [otwarcia żądania pomocy technicznej](https://azure.microsoft.com/support/options/) lub odczytać [bardziej szczegółowe Rozwiązywanie problemów z założenia i kroki RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Rozwiązywanie problemów z określonych błędów protokołu RDP
Możesz napotkać komunikat o błędzie podczas próby nawiązania połączenia z maszyną Wirtualną za pomocą protokołu RDP. Najbardziej typowe komunikaty o błędach są następujące:

* [Sesja zdalna została rozłączona, ponieważ nie nie zdalne serwery licencji usług pulpitu do dyspozycji licencji](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Pulpit zdalny nie może odnaleźć komputera "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędu zabezpieczeń lokalnych](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Błąd zabezpieczeń systemu Windows: Twoje poświadczenia nie działają](troubleshoot-specific-rdp-errors.md#wincred).
* [Ten komputer nie może połączyć się z komputerem zdalnym](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Zasoby dodatkowe
Jeśli nadal nie można połączyć się z maszyną Wirtualną za pośrednictwem pulpitu zdalnego wystąpił żaden z tych błędów, przeczytaj szczegółowe [Podręcznik rozwiązywania problemów dotyczących usług pulpitu zdalnego](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Kroki podczas uzyskiwania dostępu do aplikacji działających na maszynie Wirtualnej, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie Wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Jeśli występują problemy przy użyciu protokołu Secure Shell (SSH) do połączenia z maszyną wirtualną systemu Linux na platformie Azure, zobacz [połączeń Rozwiązywanie problemów z SSH z maszyną wirtualną systemu Linux na platformie Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


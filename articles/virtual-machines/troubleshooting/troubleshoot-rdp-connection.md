---
title: Nie można połączyć się z usługą RDP z maszyną wirtualną systemu Windows na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów, gdy nie można połączyć się z maszyną wirtualną systemu Windows na platformie Azure przy użyciu pulpitu zdalnego
keywords: Błąd pulpitu zdalnego,błąd połączenia pulpitu zdalnego,nie można połączyć się z maszyną wirtualną,rozwiązywanie problemów z pulpitem zdalnym
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: cbca8e631da8b99aa0ea4bdc6d099f3dbd2ed9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916612"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną platformy Azure
Połączenie RDP (Remote Desktop Protocol) z maszyną wirtualną platformy Azure bazującą na systemie Windows może ulec awarii z wielu powodów, uniemożliwiając uzyskanie dostępu do maszyny wirtualnej. Ten problem może być związany z usługą pulpitu zdalnego na maszynie wirtualnej, połączeniem sieciowym lub klientem pulpitu zdalnego na komputerze hosta. Ten artykuł przeprowadzi Cię przez niektóre z najbardziej popularnych metod rozwiązywania problemów z połączeniem RDP. 

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Szybkie kroki rozwiązywania problemów
Po każdym kroku rozwiązywania problemów spróbuj ponownie połączyć się z maszyną wirtualną:

1. Resetowanie konfiguracji pulpitu zdalnego.
2. Sprawdź reguły sieciowej grupy zabezpieczeń / punkty końcowe usług w chmurze.
3. Przejrzyj dzienniki konsoli maszyn wirtualnych.
4. Zresetuj kartę sieciową dla maszyny Wirtualnej.
5. Sprawdź kondycję zasobu maszyny Wirtualnej.
6. Zresetuj hasło maszyny Wirtualnej.
7. Uruchom ponownie maszynę wirtualną.
8. Ponowne wdrożenie maszyny Wirtualnej.

Czytaj dalej, jeśli potrzebujesz bardziej szczegółowych kroków i wyjaśnień. Sprawdź, czy urządzenia sieci lokalnej, takie jak routery i zapory, nie blokują wychodzącego portu TCP 3389, jak wspomniano w [szczegółowych scenariuszach rozwiązywania problemów z protokom.](detailed-troubleshoot-rdp.md)

> [!TIP]
> Jeśli przycisk **Połącz** dla maszyny Wirtualnej jest wyszarzony w portalu i nie masz połączenia z platformą Azure za pośrednictwem połączenia sieci VPN [z routsykiem ekspresowym](../../expressroute/expressroute-introduction.md) lub [siecią WEB lokacja,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) przed użyciem protokołu RDP należy utworzyć i przypisać maszynę wirtualną publiczny adres IP. Więcej informacji o [publicznych adresach IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Sposoby rozwiązywania problemów z PROW
Można rozwiązywać problemy z maszynami wirtualnymi utworzonymi przy użyciu modelu wdrażania Menedżera zasobów przy użyciu jednej z następujących metod:

* Portal Azure — świetnie, jeśli chcesz szybko zresetować konfigurację protokołu RDP lub poświadczenia użytkownika i nie masz zainstalowanych narzędzi platformy Azure.
* Azure PowerShell — jeśli masz komfort z monitem programu PowerShell, szybko zresetować konfigurację protokołu RDP lub poświadczenia użytkownika przy użyciu poleceń cmdlet programu Azure PowerShell.

Można również znaleźć kroki dotyczące rozwiązywania problemów z maszynami wirtualnymi utworzonymi przy użyciu [klasycznego modelu wdrażania](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów podczas korzystania z witryny Azure portal
Po każdym kroku rozwiązywania problemów spróbuj ponownie połączyć się z maszyną wirtualną. Jeśli nadal nie możesz się połączyć, spróbuj wykonać następny krok.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów resetuje konfigurację protokołu RDP, gdy połączenia zdalne są wyłączone lub reguły Zapory systemu Windows blokują na przykład prow.
   
    Wybierz maszynę wirtualną w witrynie Azure portal. Przewiń w dół okienko ustawień do sekcji **Pomoc techniczna + Rozwiązywanie problemów** u dołu listy. Kliknij przycisk **Resetuj hasło.** Ustaw tylko **konfigurację resetowania** **trybu,** a następnie kliknij przycisk **Aktualizuj:**
   
    ![Resetowanie konfiguracji protokołu RDP w witrynie Azure portal](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Sprawdź reguły sieciowej grupy zabezpieczeń**. Użyj funkcji [weryfikacji przepływu adresu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) w celu potwierdzenia, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Można również przejrzeć reguły skutecznej grupy zabezpieczeń, aby upewnić się, że przychodząca reguła sieciowej grupy zabezpieczeń istnieje i jest priorytetowa dla portu RDP (domyślnie 3389). Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z przepływem ruchu maszyn wirtualnych przy użyciu reguł efektywnego zabezpieczeń](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Przejrzyj diagnostykę rozruchu maszyny Wirtualnej**. Ten krok rozwiązywania problemów przegląda dzienniki konsoli maszyny Wirtualnej, aby ustalić, czy maszyna wirtualna zgłasza problem. Nie wszystkie maszyny wirtualne mają włączoną diagnostykę rozruchu, więc ten krok rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale mogą wskazywać na szerszy problem, który wpływa na łączność RDP. Aby uzyskać więcej informacji na temat przeglądania dzienników konsoli i zrzutu ekranu maszyny Wirtualnej, zobacz [Diagnostyka rozruchu maszyn wirtualnych](boot-diagnostics.md).

4. **Zresetuj kartę sieciową dla maszyny Wirtualnej**. Aby uzyskać więcej informacji, zobacz [jak zresetować kartę sieciową dla maszyny Wirtualnej systemu Windows platformy Azure](../windows/reset-network-interface.md).
5. **Sprawdź kondycję zasobu maszyny Wirtualnej**. Ten krok rozwiązywania problemów weryfikuje, że nie są znane problemy z platformą Azure, które mogą mieć wpływ na łączność z maszyną wirtualną.
   
    Wybierz maszynę wirtualną w witrynie Azure portal. Przewiń w dół okienko ustawień do sekcji **Pomoc techniczna + Rozwiązywanie problemów** u dołu listy. Kliknij przycisk **Kondycja zasobu.** Zdrowe raporty maszyny wirtualnej jako **dostępne:**
   
    ![Sprawdzanie kondycji zasobów maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Resetowanie poświadczeń użytkownika**. Ten krok rozwiązywania problemów resetuje hasło na lokalnym koncie administratora, gdy nie masz pewności lub nie pamiętasz poświadczeń.  Po zalogowaniu się do maszyny Wirtualnej należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszynę wirtualną w witrynie Azure portal. Przewiń w dół okienko ustawień do sekcji **Pomoc techniczna + Rozwiązywanie problemów** u dołu listy. Kliknij przycisk **Resetuj hasło.** Upewnij się, że **w trybie** jest **ustawiona resetowanie hasła,** a następnie wprowadź nazwę użytkownika i nowe hasło. Na koniec kliknij przycisk **Aktualizuj:**
   
    ![Resetowanie poświadczeń użytkownika w witrynie Azure portal](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Uruchom ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów można rozwiązać wszelkie podstawowe problemy, które sama maszyna wirtualna ma.
   
    Wybierz maszynę wirtualną w witrynie Azure portal i kliknij kartę **Przegląd.** Kliknij przycisk **Uruchom ponownie:**
   
    ![Ponowne uruchamianie maszyny wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Ponowne wdrożenie maszyny Wirtualnej**. Ten krok rozwiązywania problemów ponownie wdroży maszynę wirtualną do innego hosta na platformie Azure, aby rozwiązać wszelkie podstawowe problemy z platformą lub siecią.
   
    Wybierz maszynę wirtualną w witrynie Azure portal. Przewiń w dół okienko ustawień do sekcji **Pomoc techniczna + Rozwiązywanie problemów** u dołu listy. Kliknij przycisk **Ponownie wdrożyć,** a następnie kliknij pozycję **Ponownie wdrożyć:**
   
    ![Ponowne wdrożenie maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po zakończeniu tej operacji dane dysku efemerycznego zostaną utracone, a dynamiczne adresy IP skojarzone z maszyną wirtualną są aktualizowane.

9. **Sprawdź routing**. Użyj funkcji [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) obserwatora sieci, aby potwierdzić, że trasa nie uniemożliwia kierowania ruchu do lub z maszyny wirtualnej. Można również przejrzeć efektywne trasy, aby wyświetlić wszystkie skuteczne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z przepływem ruchu maszyn wirtualnych za pomocą skutecznych tras](../../virtual-network/diagnose-network-routing-problem.md).

10. Upewnij się, że wszystkie zapory lokalnej lub zapory na komputerze, umożliwia wychodzących TCP 3389 ruchu na platformie Azure.

Jeśli nadal występują problemy z prok. [open a support request](https://azure.microsoft.com/support/options/) [more detailed RDP troubleshooting concepts and steps](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-using-azure-powershell"></a>Rozwiązywanie problemów przy użyciu programu Azure PowerShell
Jeśli jeszcze tego nie zrobiłeś, [zainstaluj i skonfiguruj najnowszą usługę Azure PowerShell](/powershell/azure/overview).

W poniższych przykładach użyto zmiennych, takich jak `myResourceGroup`, `myVM`i `myVMAccessExtension`. Zastąp te nazwy zmiennych i lokalizacje własnymi wartościami.

> [!NOTE]
> Poświadczenia użytkownika i konfigurację protokołu RDP można zresetować przy użyciu polecenia cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. W poniższych przykładach jest nazwą, `myVMAccessExtension` którą można określić jako część procesu. Jeśli wcześniej pracowałeś z VMAccessAgent, można uzyskać nazwę istniejącego rozszerzenia `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` za pomocą, aby sprawdzić właściwości maszyny Wirtualnej. Aby wyświetlić nazwę, poszukaj w sekcji "Rozszerzenia" danych wyjściowych.

Po każdym kroku rozwiązywania problemów spróbuj ponownie połączyć się z maszyną wirtualną. Jeśli nadal nie możesz się połączyć, spróbuj wykonać następny krok.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów resetuje konfigurację protokołu RDP, gdy połączenia zdalne są wyłączone lub reguły Zapory systemu Windows blokują na przykład prow.
   
    Poniższy przykład resetuje połączenie RDP na `myVM` maszynie wirtualnej o nazwie w `WestUS` lokalizacji i w grupie zasobów o nazwie: `myResourceGroup`
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Sprawdź reguły sieciowej grupy zabezpieczeń**. Ten krok rozwiązywania problemów sprawdza, czy w sieciowej grupie zabezpieczeń jest reguła zezwalająca na ruch RDP. Domyślnym portem protokołu RDP jest port TCP 3389. Reguła zezwalana na ruch RDP może nie być tworzona automatycznie podczas tworzenia maszyny Wirtualnej.
   
    Najpierw należy przypisać wszystkie dane konfiguracyjne `$rules` sieciowej grupy zabezpieczeń do zmiennej. Poniższy przykład uzyskuje informacje o sieciowej grupie zabezpieczeń o nazwie `myNetworkSecurityGroup` w grupie zasobów o nazwie: `myResourceGroup`
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Teraz wyświetl reguły skonfigurowane dla tej sieciowej grupy zabezpieczeń. Sprawdź, czy istnieje reguła zezwalania na port TCP 3389 dla połączeń przychodzących w następujący sposób:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    W poniższym przykładzie przedstawiono prawidłową regułę zabezpieczeń zezwalającą na ruch RDP. Możesz zobaczyć `Protocol` `DestinationPortRange`, `Access`, `Direction` i są poprawnie skonfigurowane:
   
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
   
    Jeśli nie masz reguły zezwalajej na ruch RDP, [utwórz regułę sieciowej grupy zabezpieczeń](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Zezwalaj na port TCP 3389.
3. **Resetowanie poświadczeń użytkownika**. Ten krok rozwiązywania problemów resetuje hasło na koncie administratora lokalnego, które określisz, gdy nie masz pewności co do poświadczeń lub nie pamiętasz.
   
    Najpierw określ nazwę użytkownika i nowe hasło, `$cred` przypisując poświadczenia do zmiennej w następujący sposób:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Teraz zaktualizuj poświadczenia na maszynie Wirtualnej. Poniższy przykład aktualizuje poświadczenia na `myVM` maszynie `WestUS` wirtualnej o nazwie `myResourceGroup`w lokalizacji i w grupie zasobów o nazwie:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Uruchom ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów można rozwiązać wszelkie podstawowe problemy, które sama maszyna wirtualna ma.
   
    Poniższy przykład ponownie uruchamia `myVM` maszynę wirtualną `myResourceGroup`o nazwie w grupie zasobów o nazwie:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Ponowne wdrożenie maszyny Wirtualnej**. Ten krok rozwiązywania problemów ponownie wdroży maszynę wirtualną do innego hosta na platformie Azure, aby rozwiązać wszelkie podstawowe problemy z platformą lub siecią.
   
    W poniższym przykładzie ponownie wcieli `WestUS` się maszyna wirtualna `myResourceGroup`o nazwie `myVM` w lokalizacji i w grupie zasobów o nazwie:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Sprawdź routing**. Użyj funkcji [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) obserwatora sieci, aby potwierdzić, że trasa nie uniemożliwia kierowania ruchu do lub z maszyny wirtualnej. Można również przejrzeć efektywne trasy, aby wyświetlić wszystkie skuteczne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z przepływem ruchu maszyn wirtualnych za pomocą skutecznych tras](../../virtual-network/diagnose-network-routing-problem.md).

7. Upewnij się, że wszystkie zapory lokalnej lub zapory na komputerze, umożliwia wychodzących TCP 3389 ruchu na platformie Azure.

Jeśli nadal występują problemy z prok. [open a support request](https://azure.microsoft.com/support/options/) [more detailed RDP troubleshooting concepts and steps](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Rozwiązywanie problemów z maszynami wirtualnymi utworzonymi przy użyciu klasycznego modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Po każdym kroku rozwiązywania problemów spróbuj ponownie połączyć się z maszyną wirtualną.

1. **Zresetuj połączenie RDP**. Ten krok rozwiązywania problemów resetuje konfigurację protokołu RDP, gdy połączenia zdalne są wyłączone lub reguły Zapory systemu Windows blokują na przykład prow.
   
    Wybierz maszynę wirtualną w witrynie Azure portal. Kliknij **przycisk ... Przycisk Więcej,** a następnie kliknij przycisk **Resetuj dostęp zdalny:**
   
    ![Resetowanie konfiguracji protokołu RDP w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Weryfikuj punkty końcowe usług w chmurze**. Ten krok rozwiązywania problemów sprawdza, czy masz punkty końcowe w usługach w chmurze, aby umożliwić ruch RDP. Domyślnym portem protokołu RDP jest port TCP 3389. Reguła zezwalana na ruch RDP może nie być tworzona automatycznie podczas tworzenia maszyny Wirtualnej.
   
   Wybierz maszynę wirtualną w witrynie Azure portal. Kliknij przycisk **Punkty końcowe,** aby wyświetlić punkty końcowe aktualnie skonfigurowane dla maszyny Wirtualnej. Sprawdź, czy istnieją punkty końcowe zezwalają na ruch RDP w porcie TCP 3389.
   
   W poniższym przykładzie przedstawiono prawidłowe punkty końcowe zezwalane na ruch RDP:
   
   ![Weryfikowanie punktów końcowych usług w chmurze w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Jeśli nie masz punktu końcowego umożliwiającego ruch RDP, [utwórz punkt końcowy usług w chmurze](../windows/classic/setup-endpoints.md). Zezwól na port prywatny TCP 3389.
3. **Przejrzyj diagnostykę rozruchu maszyny Wirtualnej**. Ten krok rozwiązywania problemów przegląda dzienniki konsoli maszyny Wirtualnej, aby ustalić, czy maszyna wirtualna zgłasza problem. Nie wszystkie maszyny wirtualne mają włączoną diagnostykę rozruchu, więc ten krok rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale mogą wskazywać na szerszy problem, który wpływa na łączność RDP. Aby uzyskać więcej informacji na temat przeglądania dzienników konsoli i zrzutu ekranu maszyny Wirtualnej, zobacz [Diagnostyka rozruchu maszyn wirtualnych](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Sprawdź kondycję zasobu maszyny Wirtualnej**. Ten krok rozwiązywania problemów weryfikuje, że nie są znane problemy z platformą Azure, które mogą mieć wpływ na łączność z maszyną wirtualną.
   
    Wybierz maszynę wirtualną w witrynie Azure portal. Przewiń w dół okienko ustawień do sekcji **Pomoc techniczna + Rozwiązywanie problemów** u dołu listy. Kliknij przycisk **Kondycja zasobów.** Zdrowe raporty maszyny wirtualnej jako **dostępne:**
   
    ![Sprawdzanie kondycji zasobów maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Resetowanie poświadczeń użytkownika**. Ten krok rozwiązywania problemów resetuje hasło na koncie administratora lokalnego, które określisz, gdy nie masz pewności lub nie pamiętasz poświadczeń.  Po zalogowaniu się do maszyny Wirtualnej należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszynę wirtualną w witrynie Azure portal. Przewiń w dół okienko ustawień do sekcji **Pomoc techniczna + Rozwiązywanie problemów** u dołu listy. Kliknij przycisk **Resetuj hasło.** Wprowadź swoją nazwę użytkownika i nowe hasło. Na koniec kliknij przycisk **Zapisz:**
   
    ![Resetowanie poświadczeń użytkownika w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Uruchom ponownie maszynę wirtualną**. Ten krok rozwiązywania problemów można rozwiązać wszelkie podstawowe problemy, które sama maszyna wirtualna ma.
   
    Wybierz maszynę wirtualną w witrynie Azure portal i kliknij kartę **Przegląd.** Kliknij przycisk **Uruchom ponownie:**
   
    ![Ponowne uruchamianie maszyny wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Upewnij się, że wszystkie zapory lokalnej lub zapory na komputerze, umożliwia wychodzących TCP 3389 ruchu na platformie Azure.

Jeśli nadal występują problemy z prok. [open a support request](https://azure.microsoft.com/support/options/) [more detailed RDP troubleshooting concepts and steps](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="troubleshoot-specific-rdp-errors"></a>Rozwiązywanie problemów dotyczących konkretnych błędów protokołu RDP
Podczas próby nawiązania połączenia z maszyną wirtualną za pośrednictwem protokołu RDP może wystąpić określony komunikat o błędzie. Oto najczęstsze komunikaty o błędach:

* [Sesja zdalna została rozłączona, ponieważ nie ma dostępnych serwerów licencji usług pulpitu zdalnego, aby zapewnić licencję](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Pulpit zdalny nie może znaleźć "nazwy" komputera](troubleshoot-specific-rdp-errors.md#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z lokalnym urzędem bezpieczeństwa](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Błąd zabezpieczeń systemu Windows: poświadczenia nie działały](troubleshoot-specific-rdp-errors.md#wincred).
* [Ten komputer nie może połączyć się z komputerem zdalnym](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Zasoby dodatkowe
Jeśli nie wystąpił żaden z tych błędów i nadal nie można połączyć się z maszyną wirtualną za pośrednictwem pulpitu zdalnego, przeczytaj szczegółowy [przewodnik rozwiązywania problemów dla pulpitu zdalnego](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby uzyskać instrukcje rozwiązywania problemów z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie Wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Jeśli masz problemy z używaniem secure shell (SSH) do łączenia się z maszyną wirtualną z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną z systemem Linux na platformie Azure.](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



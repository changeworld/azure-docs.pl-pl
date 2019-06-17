---
title: Nie można nawiązać połączenia przy użyciu protokołu RDP do maszyny Wirtualnej z systemem Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów, gdy nie można połączyć się z maszyną wirtualną Windows na platformie Azure przy użyciu pulpitu zdalnego
keywords: Błąd połączenia zdalnego, błąd połączeń usług pulpitu zdalnego, nie można nawiązać połączenia z maszyną Wirtualną, rozwiązywania problemów usług pulpitu zdalnego
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: 50adab1eaa199473a8da857d38c3a08c424c677a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728938"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Rozwiązywanie problemów z połączeniami pulpitu zdalnego na maszynie wirtualnej platformy Azure
Połączenie RDP (Remote Desktop Protocol) z maszyną wirtualną platformy Azure bazującą na systemie Windows może ulec awarii z wielu powodów, uniemożliwiając uzyskanie dostępu do maszyny wirtualnej. Ten problem może być związany z usługą pulpitu zdalnego na maszynie wirtualnej, połączeniem sieciowym lub klientem pulpitu zdalnego na komputerze hosta. Ten artykuł przeprowadzi Cię przez niektóre z najbardziej typowych metod, aby rozwiązać problemy z połączeniem RDP. 

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Szybkie kroki rozwiązywania problemów
Po każdym kroku rozwiązywania problemów spróbuj ponowne nawiązywanie połączenia z maszyną wirtualną:

1. Zresetuj konfigurację pulpitu zdalnego.
2. Sprawdź sieciowej grupy zabezpieczeń reguły / Cloud Services — punkty końcowe.
3. Przejrzyj dzienniki konsoli maszyny Wirtualnej.
4. Zresetuj kartę Sieciową dla maszyny Wirtualnej.
5. Sprawdzanie kondycji zasobu maszyny Wirtualnej.
6. Resetowanie hasła maszyny Wirtualnej.
7. Uruchom ponownie maszynę Wirtualną.
8. Ponowne wdrażanie maszyny Wirtualnej.

Czytaj dalej, aby uzyskać bardziej szczegółowy opis kroków i wyjaśnienia. Sprawdź ten lokalnych urządzeń sieciowych, takich jak routery i zapory nie blokują ruchu wychodzącego portu TCP 3389, jak wspomniano w [szczegółowe protokołu RDP, rozwiązywanie problemów ze scenariuszami](detailed-troubleshoot-rdp.md).

> [!TIP]
> Jeśli **Connect** przycisku dla maszyny Wirtualnej jest nieaktywny w portalu, a nie są połączone na platformie Azure za pośrednictwem [Express Route](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) połączenie, musisz utworzyć i przypisać maszynie Wirtualnej publiczny adres IP przed użyciem pulpitu zdalnego. Więcej informacji o [publicznych adresach IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Sposoby rozwiązywania problemów z protokołu RDP
Można rozwiązywać problemy z maszyn wirtualnych utworzonych przy użyciu modelu wdrażania usługi Resource Manager przy użyciu jednej z następujących metod:

* Witryna Azure portal — dobrze, jeśli muszą błyskawicznie Zresetuj RDP konfiguracji lub poświadczenia użytkownika i nie ma zainstalowane narzędzia platformy Azure.
* Azure PowerShell — Jeśli masz doświadczenia w wierszu polecenia programu PowerShell błyskawicznie Zresetuj RDP konfiguracji lub poświadczenia użytkownika przy użyciu poleceń cmdlet programu Azure PowerShell.

Możesz również znaleźć instrukcje dotyczące rozwiązywania problemów z maszyn wirtualnych utworzonych za pomocą [klasycznego modelu wdrażania](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu witryny Azure portal
Każdy krok rozwiązywania problemów a następnie spróbuj ponownie nawiązać połączenie z maszyną wirtualną. Jeśli nadal nie możesz się połączyć, przejdź do następnego kroku.

1. **Zresetuj połączenie RDP**. W tym kroku rozwiązywania problemów powoduje zresetowanie konfiguracji RDP podczas połączenia zdalne są wyłączone lub reguły zapory Windows blokują protokołu RDP, na przykład.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal. Przewiń w dół w okienku ustawień do **pomoc techniczna i rozwiązywanie problemów** sekcji w dolnej części listy. Kliknij przycisk **Resetuj hasło** przycisku. Ustaw **tryb** do **tylko konfiguracji resetowania** a następnie kliknij przycisk **aktualizacji** przycisku:
   
    ![Resetowanie konfiguracji RDP w witrynie Azure portal](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Sprawdź, czy sieciowa grupa zabezpieczeń reguły**. Użyj funkcji [weryfikacji przepływu adresu IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) w celu potwierdzenia, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące reguły grupy zabezpieczeń do zapewnienia ruchu przychodzącego "Zezwalaj" sieciowa grupa zabezpieczeń istnieje priorytetowa reguła dla portu RDP (domyślnie: 3389). Aby uzyskać więcej informacji, zobacz [przepływu ruchu przy użyciu efektywne reguły zabezpieczeń, aby rozwiązać maszyn wirtualnych](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Przejrzyj Diagnostyka rozruchu maszyny Wirtualnej**. W tym kroku rozwiązywania problemów sprawdza dzienników konsoli maszyny Wirtualnej, aby ustalić, czy maszyna wirtualna jest Raportowanie problemu. Nie wszystkie maszyny wirtualne mają diagnostyki rozruchu, włączone, dlatego w tym kroku rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale może wskazywać na problem szersze, który ma wpływ na łączność RDP. Aby uzyskać więcej informacji na temat przeglądania zrzut ekranu maszyny Wirtualnej i dzienniki konsoli, zobacz [diagnostyki rozruchu dla maszyn wirtualnych](boot-diagnostics.md).

4. **Resetowanie karty Sieciowej dla maszyny Wirtualnej**. Aby uzyskać więcej informacji, zobacz [jak zresetować karty Sieciowej dla maszyny Wirtualnej Windows Azure](../windows/reset-network-interface.md).
5. **Sprawdzanie kondycji zasobu maszyny Wirtualnej**. W tym kroku rozwiązywania problemów sprawdza, czy nie ma żadnych znanych problemów z platformą Azure, które mogą mieć wpływ na łączność z maszyną Wirtualną.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal. Przewiń w dół w okienku ustawień do **pomoc techniczna i rozwiązywanie problemów** sekcji w dolnej części listy. Kliknij przycisk **Resource health** przycisku. Dobrej kondycji maszyny Wirtualnej zgłasza jako **dostępne**:
   
    ![Sprawdzanie kondycji zasobu maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Resetowanie poświadczeń użytkownika**. W tym kroku rozwiązywania problemów resetuje hasło konta administratora lokalnego podczas pewności lub zapomniał poświadczenia.  Po zalogowaniu się do maszyny Wirtualnej, należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal. Przewiń w dół w okienku ustawień do **pomoc techniczna i rozwiązywanie problemów** sekcji w dolnej części listy. Kliknij przycisk **Resetuj hasło** przycisku. Upewnij się, że **tryb** ustawiono **Resetuj hasło** a następnie wprowadź swoją nazwę użytkownika i nowe hasło. Na koniec kliknij **aktualizacji** przycisku:
   
    ![Resetuj poświadczenia użytkownika w witrynie Azure portal](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Uruchom ponownie maszynę Wirtualną**. W tym kroku rozwiązywania problemów można poprawić podstawowych problemów, które występują w samej maszyny Wirtualnej.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal, a następnie kliknij przycisk **Przegląd** kartę. Kliknij przycisk **ponowne uruchomienie** przycisku:
   
    ![Uruchom ponownie maszynę Wirtualną w witrynie Azure portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Ponowne wdrażanie maszyny Wirtualnej**. W tym kroku rozwiązywania problemów ponownie wdraża maszynę Wirtualną do innego hosta w obrębie platformy Azure, aby naprawić wszelkie podstawowej platformy lub problemy z siecią.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal. Przewiń w dół w okienku ustawień do **pomoc techniczna i rozwiązywanie problemów** sekcji w dolnej części listy. Kliknij przycisk **ponownie wdrożyć** przycisk, a następnie kliknij przycisk **ponownie wdrożyć**:
   
    ![Ponowne wdrożenie maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Po zakończeniu tej operacji efemerycznego dysku dane zostaną utracone i zostaną zaktualizowane dynamiczne adresy IP, które są skojarzone z maszyną Wirtualną.

9. **Sprawdź routing**. Usługa Network Watcher [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) możliwości, aby upewnić się, trasa nie jest zapobieganie ruch jest kierowany do lub z maszyny wirtualnej. Możesz również sprawdzić skuteczne trasy, aby wyświetlić wszystkie obowiązujące trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [przepływu ruchu Using obowiązujących tras, rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-routing-problem.md).

10. Upewnij się, że wszelkie zapory w środowisku lokalnym lub zapora na komputerze, zezwala na ruch wychodzący TCP 3389 na platformie Azure.

Jeśli są wciąż występują problemy z RDP, możesz to zrobić [Otwórz żądanie obsługi](https://azure.microsoft.com/support/options/) lub przeczytaj [bardziej szczegółowe protokołu RDP, rozwiązywanie problemów z koncepcje i kroki](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Rozwiązywanie problemów przy użyciu programu Azure PowerShell
Jeśli jeszcze nie, [zainstalować i skonfigurować najnowszą wersję programu Azure PowerShell](/powershell/azure/overview).

W poniższych przykładach używane zmienne `myResourceGroup`, `myVM`, i `myVMAccessExtension`. Zastąp następujące nazwy zmiennych i lokalizacje własnymi wartościami.

> [!NOTE]
> Zresetuj poświadczenia użytkownika i Konfiguracja protokołu RDP przy użyciu [AzVMAccessExtension zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) polecenia cmdlet programu PowerShell. W poniższych przykładach `myVMAccessExtension` jest nazwą, które można określić jako część procesu. Jeśli poprzednio korzystano z VMAccessAgent, można uzyskać nazwę istniejącego rozszerzenia za pomocą `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` do sprawdzenia właściwości maszyny Wirtualnej. Aby wyświetlić nazwę, poszukaj w sekcji "Extensions" w danych wyjściowych.

Każdy krok rozwiązywania problemów a następnie spróbuj ponownie nawiązać połączenie z maszyną wirtualną. Jeśli nadal nie możesz się połączyć, przejdź do następnego kroku.

1. **Zresetuj połączenie RDP**. W tym kroku rozwiązywania problemów powoduje zresetowanie konfiguracji RDP podczas połączenia zdalne są wyłączone lub reguły zapory Windows blokują protokołu RDP, na przykład.
   
    W przykładzie poniżej resetuje połączenie RDP na maszynie Wirtualnej o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Sprawdź, czy sieciowa grupa zabezpieczeń reguły**. W tym kroku rozwiązywania problemów sprawdza mieć regułę w grupie zabezpieczeń sieci zezwalającą na ruch RDP. Domyślny port dla protokołu RDP jest TCP port 3389. Regułę zezwalającą na ruch RDP nie mogą być tworzone automatycznie podczas tworzenia maszyny Wirtualnej.
   
    Najpierw Przypisz wszystkie dane konfiguracyjne dla sieciowej grupy zabezpieczeń do `$rules` zmiennej. W poniższym przykładzie uzyskano informacje o sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup` w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Teraz Wyświetl reguły, które są skonfigurowane dla tej grupy zabezpieczeń sieci. Sprawdź, czy istnieje Reguła zezwalająca na porcie TCP 3389 dla połączeń przychodzących w następujący sposób:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    Poniższy przykład pokazuje regułę podmioty zabezpieczeń, która zezwala na ruch RDP. Możesz zobaczyć `Protocol`, `DestinationPortRange`, `Access`, i `Direction` są prawidłowo skonfigurowane:
   
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
   
    Jeśli nie masz regułę zezwalającą na ruch RDP [Utwórz regułę sieciowej grupy zabezpieczeń](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Zezwalaj na porcie TCP 3389.
3. **Resetowanie poświadczeń użytkownika**. W tym kroku rozwiązywania problemów resetuje hasło konta administratora lokalnego, określające pewności lub zapomniał poświadczenia.
   
    Najpierw należy określić nazwę użytkownika i nowe hasło, przypisując poświadczenia, aby `$cred` zmiennej w następujący sposób:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Teraz zaktualizować poświadczenia na maszynie Wirtualnej. Poniższy przykład aktualizuje poświadczenia na maszynie Wirtualnej o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Uruchom ponownie maszynę Wirtualną**. W tym kroku rozwiązywania problemów można poprawić podstawowych problemów, które występują w samej maszyny Wirtualnej.
   
    Poniższy przykład powoduje ponowne uruchomienie maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Ponowne wdrażanie maszyny Wirtualnej**. W tym kroku rozwiązywania problemów ponownie wdraża maszynę Wirtualną do innego hosta w obrębie platformy Azure, aby naprawić wszelkie podstawowej platformy lub problemy z siecią.
   
    Poniższy przykład ponownie wdraża maszynę Wirtualną o nazwie `myVM` w `WestUS` lokalizacji i w grupie zasobów o nazwie `myResourceGroup`:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Sprawdź routing**. Usługa Network Watcher [następnego przeskoku](../../network-watcher/network-watcher-check-next-hop-portal.md) możliwości, aby upewnić się, trasa nie jest zapobieganie ruch jest kierowany do lub z maszyny wirtualnej. Możesz również sprawdzić skuteczne trasy, aby wyświetlić wszystkie obowiązujące trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [przepływu ruchu Using obowiązujących tras, rozwiązywać problemy z maszyny Wirtualnej](../../virtual-network/diagnose-network-routing-problem.md).

7. Upewnij się, że wszelkie zapory w środowisku lokalnym lub zapora na komputerze, zezwala na ruch wychodzący TCP 3389 na platformie Azure.

Jeśli są wciąż występują problemy z RDP, możesz to zrobić [Otwórz żądanie obsługi](https://azure.microsoft.com/support/options/) lub przeczytaj [bardziej szczegółowe protokołu RDP, rozwiązywanie problemów z koncepcje i kroki](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Rozwiązywanie problemów z maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania
Po każdym kroku rozwiązywania problemów spróbuj ponowne nawiązywanie połączenia z maszyną wirtualną.

1. **Zresetuj połączenie RDP**. W tym kroku rozwiązywania problemów powoduje zresetowanie konfiguracji RDP podczas połączenia zdalne są wyłączone lub reguły zapory Windows blokują protokołu RDP, na przykład.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal. Kliknij przycisk **... Więcej** przycisk, a następnie kliknij przycisk **Resetuj dostęp zdalny**:
   
    ![Resetowanie konfiguracji RDP w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Sprawdź punktów końcowych usługi w chmurze**. W tym kroku rozwiązywania problemów sprawdza mają punkty końcowe w usługach w chmurze, aby zezwalać na ruch RDP. Domyślny port dla protokołu RDP jest TCP port 3389. Regułę zezwalającą na ruch RDP nie mogą być tworzone automatycznie podczas tworzenia maszyny Wirtualnej.
   
   Wybierz maszynę Wirtualną w witrynie Azure portal. Kliknij przycisk **punktów końcowych** przycisk, aby wyświetlić punkty końcowe obecnie skonfigurowane dla maszyny Wirtualnej. Sprawdź, czy istnieją punkty końcowe, które zezwalają na ruch RDP na porcie TCP 3389.
   
   Poniższy przykład przedstawia prawidłowych punktów końcowych, które zezwala na ruch RDP:
   
   ![Sprawdź punktów końcowych usługi w chmurze, w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Jeśli nie masz punkt końcowy, który zezwala na ruch RDP [utworzyć punkt końcowy usługi w chmurze](../windows/classic/setup-endpoints.md). Zezwalaj na TCP do portu prywatnego 3389.
3. **Przejrzyj Diagnostyka rozruchu maszyny Wirtualnej**. W tym kroku rozwiązywania problemów sprawdza dzienników konsoli maszyny Wirtualnej, aby ustalić, czy maszyna wirtualna jest Raportowanie problemu. Nie wszystkie maszyny wirtualne mają diagnostyki rozruchu, włączone, dlatego w tym kroku rozwiązywania problemów może być opcjonalny.
   
    Określone kroki rozwiązywania problemów wykraczają poza zakres tego artykułu, ale może wskazywać na problem szersze, który ma wpływ na łączność RDP. Aby uzyskać więcej informacji na temat przeglądania zrzut ekranu maszyny Wirtualnej i dzienniki konsoli, zobacz [diagnostyki rozruchu dla maszyn wirtualnych](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Sprawdzanie kondycji zasobu maszyny Wirtualnej**. W tym kroku rozwiązywania problemów sprawdza, czy nie ma żadnych znanych problemów z platformą Azure, które mogą mieć wpływ na łączność z maszyną Wirtualną.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal. Przewiń w dół w okienku ustawień do **pomoc techniczna i rozwiązywanie problemów** sekcji w dolnej części listy. Kliknij przycisk **Resource Health** przycisku. Dobrej kondycji maszyny Wirtualnej zgłasza jako **dostępne**:
   
    ![Sprawdzanie kondycji zasobu maszyny Wirtualnej w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Resetowanie poświadczeń użytkownika**. W tym kroku rozwiązywania problemów resetuje hasło konta administratora lokalnego, określające pewności lub zapomniał poświadczenia.  Po zalogowaniu się do maszyny Wirtualnej, należy zresetować hasło dla tego użytkownika.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal. Przewiń w dół w okienku ustawień do **pomoc techniczna i rozwiązywanie problemów** sekcji w dolnej części listy. Kliknij przycisk **Resetuj hasło** przycisku. Wprowadź swoją nazwę użytkownika i nowe hasło. Na koniec kliknij **Zapisz** przycisku:
   
    ![Resetuj poświadczenia użytkownika w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Uruchom ponownie maszynę Wirtualną**. W tym kroku rozwiązywania problemów można poprawić podstawowych problemów, które występują w samej maszyny Wirtualnej.
   
    Wybierz maszynę Wirtualną w witrynie Azure portal, a następnie kliknij przycisk **Przegląd** kartę. Kliknij przycisk **ponowne uruchomienie** przycisku:
   
    ![Uruchom ponownie maszynę Wirtualną w witrynie Azure portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Upewnij się, że wszelkie zapory w środowisku lokalnym lub zapora na komputerze, zezwala na ruch wychodzący TCP 3389 na platformie Azure.

Jeśli są wciąż występują problemy z RDP, możesz to zrobić [Otwórz żądanie obsługi](https://azure.microsoft.com/support/options/) lub przeczytaj [bardziej szczegółowe protokołu RDP, rozwiązywanie problemów z koncepcje i kroki](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Rozwiązywanie problemów z określonymi błędami protokołu RDP
Możesz napotkać komunikat o błędzie podczas próby nawiązania połączenia z maszyną wirtualną za pośrednictwem protokołu RDP. Poniżej przedstawiono najbardziej typowe komunikaty o błędach:

* [Sesja zdalna została rozłączona, ponieważ nie zdalnego pulpitu dostępnych serwerów licencji mogą udostępnić licencję](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Pulpit zdalny nie może znaleźć komputera "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Błąd zabezpieczeń Windows: Twoje poświadczenia nie działają](troubleshoot-specific-rdp-errors.md#wincred).
* [Ten komputer nie może połączyć się z komputerem zdalnym](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Dodatkowe zasoby
Jeśli nadal nie można połączyć z maszyną wirtualną za pośrednictwem pulpitu zdalnego wystąpił żaden z tych błędów, przeczytaj szczegółowe [przewodnik rozwiązywania problemów dla pulpitu zdalnego](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Rozwiązywanie problemów podczas uzyskiwania dostępu do aplikacji działających na maszynie Wirtualnej, dla [Rozwiązywanie problemów z dostępem do aplikacji uruchamianej na Maszynie wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Jeśli występują problemy przy użyciu protokołu Secure Shell (SSH), aby nawiązać połączenie z maszyny Wirtualnej z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z połączeń protokołu SSH z maszyny Wirtualnej z systemem Linux na platformie Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



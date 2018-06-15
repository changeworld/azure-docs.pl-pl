---
title: Jak włączyć wirtualizacji zagnieżdżonych w maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Jak włączyć wirtualizacji zagnieżdżonych w maszynach wirtualnych platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 0648a7555cb94543dadf5d73e0187927a90f5b5a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526522"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Jak włączyć zagnieżdżonych wirtualizacji w maszynie Wirtualnej platformy Azure

Zagnieżdżone wirtualizacji jest obsługiwane w serii Dv3 i Ev3 maszyn wirtualnych platformy Azure. Ta funkcja zapewnia dużą elastyczność w obsługi scenariuszy, takich jak środowiska programowania, testowania, szkolenia i pokaz. 

Ta procedura artykułu przez włączenie zagnieżdżonych wirtualizacji na maszynie Wirtualnej platformy Azure i konfigurowanie łączności z Internetem do tej maszyny wirtualnej gościa.

## <a name="create-a-dv3-or-ev3-series-azure-vm"></a>Utwórz Dv3 lub Ev3 serii maszyny Wirtualnej Azure

Tworzenie nowego systemu Windows Server 2016 Azure maszyny Wirtualnej, a następnie wybierz rozmiar z serii Dv3 lub Ev3. Upewnij się, że wybierz rozmiar wystarczająco duży, aby obsługiwał wymagań maszyny wirtualnej gościa. W tym przykładzie użyto D3_v3 rozmiar maszyny Wirtualnej platformy Azure. 

Możesz wyświetlić regionalnych dostępność maszyn wirtualnych serii Dv3 lub Ev3 [tutaj](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Aby uzyskać szczegółowe instrukcje dotyczące tworzenia nowej maszyny wirtualnej, zobacz [tworzenie i zarządzanie maszynami wirtualnymi systemu Windows z modułu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Połączenie z maszyną wirtualną Azure

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

1. Kliknij przycisk **Połącz** we właściwościach maszyny wirtualnej. Zostanie utworzony i pobrany plik Remote Desktop Protocol (rdp).

2. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

3. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie kliknij przycisk **OK**.

4. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Włączanie funkcji Hyper-V na maszynie Wirtualnej Azure
Te ustawienia można skonfigurować ręcznie lub firma Microsoft umieściła skrypt programu PowerShell, aby zautomatyzować konfigurację.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opcja 1: Umożliwia skonfigurowanie zagnieżdżonych wirtualizacji skrypt programu PowerShell
Skrypt programu PowerShell, aby włączyć zagnieżdżonych wirtualizacji na hoście systemu Windows Server 2016 jest dostępna w [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skrypt sprawdza wymagania wstępne, a następnie konfiguruje zagnieżdżonych wirtualizacji na maszynie Wirtualnej Azure. Ponowne uruchomienie maszyny wirtualnej Azure jest niezbędne do ukończenia konfiguracji. Ten skrypt może działać w innych środowiskach, ale nie jest gwarantowana. Sprawdź wpis w blogu Azure z pokaz wideo na żywo na zagnieżdżonych wirtualizacji działających na platformie Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opcja 2: Skonfiguruj ręcznie zagnieżdżonych wirtualizacji

1. Na maszynie Wirtualnej Azure Otwórz program PowerShell jako Administrator. 

2. Włączanie funkcji Hyper-V i narzędzia do zarządzania.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >To polecenie powoduje ponowne uruchomienie maszyny Wirtualnej platformy Azure. Zostaną utracone podczas procesu ponownego połączenia RDP.
    
3. Po ponownym uruchomieniu maszyny Wirtualnej Azure, ponownie nawiąż połączenie z maszyną Wirtualną za pomocą protokołu RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Skonfiguruj połączenie z Internetem dla maszyny wirtualnej gościa
Utwórz nowy wirtualnej karty sieciowej dla maszyny wirtualnej gościa i skonfigurować bramę translatora adresów Sieciowych, aby umożliwić łączność z Internetem.

### <a name="create-a-nat-virtual-network-switch"></a>Utwórz przełącznik sieci wirtualnej translatora adresów Sieciowych

1. Na maszynie Wirtualnej Azure Otwórz program PowerShell jako Administrator.
   
2. Tworzenie przełącznika wewnętrznego.

    ```powershell
    New-VMSwitch -Name "InternalNATSwitch" -SwitchType Internal
    ```

3. Wyświetl właściwości przełącznika i zanotuj ifIndex nowej karty.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Zanotuj "ifIndex" dla przełącznika wirtualnego, który został utworzony.
    
4. Utwórz adres IP dla bramy translatora adresów Sieciowych.
    
Aby skonfigurować bramę, należy niektóre informacje o sieci:    
  * Adres IP — adres IP translatora adresów Sieciowych bramy Określa adres IPv4 lub IPv6 do użycia jako adres bramy domyślnej dla podsieci sieci wirtualnej. Formularz ogólny to a.b.c.1 (na przykład "192.168.0.1"). Podczas końcowego pozycji nie musi być.1, jego zwykle (oparte na długość prefiksu). Zwykle należy używać RFC 1918 przestrzeń adresową sieci prywatnej. 
  * PrefixLength - długość prefiksu podsieci określa rozmiar podsieci lokalnej (maski podsieci). Długość prefiksu podsieci będzie całkowitą z przedziału od 0 do 32. 0 może mapować cały internet, 32 umożliwiałyby tylko jeden adres IP mapowane. Typowe wartości należą do zakresu od 24 do 12, w zależności od tego, jak wiele adresów IP muszą być podłączone do translatora adresów sieciowych. Typowe PrefixLength jest 24 — jest to maską podsieci 255.255.255.0.
  * InterfaceIndex - **ifIndex** jest indeksem przełącznik wirtualny utworzony w poprzednim kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Tworzenie sieci translatora adresów Sieciowych

Aby skonfigurować bramę, konieczne będzie podawanie informacji o sieci i translatora adresów Sieciowych bramy:
  * Nazwa — jest to nazwa sieci translatora adresów Sieciowych. 
  * Prefiks podsieci translatora adresów Sieciowych InternalIPInterfaceAddressPrefix - opisano zarówno prefiks IP translatora adresów Sieciowych bramy z powyższych, jak i długość prefiksu podsieci translatora adresów Sieciowych z powyższych. Formularz ogólny będzie a.b.c.0/NAT długość prefiksu podsieci. 

W programie PowerShell Utwórz nową sieć translatora adresów Sieciowych.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Tworzenie maszyny wirtualnej gościa

1. Otwórz Menedżera funkcji Hyper-V i tworzenie nowej maszyny wirtualnej. Skonfiguruj maszynę wirtualną do używania nowych sieci wewnętrznej, utworzony.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Zainstaluj system operacyjny na maszynie wirtualnej gościa.
    
    >[!NOTE] 
    >
    >Potrzebny jest nośnik instalacyjny systemu operacyjnego do zainstalowania na maszynie Wirtualnej. W tym przypadku użyto systemu Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Przypisz adres IP na maszynie wirtualnej gościa

Adres IP można przypisać do maszyny wirtualnej gościa, ręcznie Ustawianie statycznego adresu IP na maszynie wirtualnej gościa lub konfigurowanie protokołu DHCP na maszynie Wirtualnej Azure można dynamicznie przypisać adres IP.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opcja 1: Konfigurowanie protokołu DHCP można dynamicznie przypisać adres IP na maszynie wirtualnej gościa
Wykonaj poniższe kroki, aby skonfigurować serwer DHCP na host maszyny wirtualnej do przypisywania adresów dynamicznych.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Zainstaluj serwer DHCP na Maszynie wirtualnej platformy Azure

1. Otwórz Menedżera serwera. Na pulpicie nawigacyjnym kliknij **Dodaj role i funkcje**. Dodaj role i funkcje kreatora jest wyświetlana.
  
2. W kreatorze kliknij pozycję **dalej** aż do strony role serwera.
  
3. Kliknij, aby wybrać **serwera DHCP** pole wyboru, kliknij przycisk **Dodaj funkcje**, a następnie kliknij przycisk **dalej** aż kreator zakończy działanie.
  
4. Kliknij pozycję **Zainstaluj**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurowanie nowego zakresu DHCP

1. Otwórz Menedżera usługi DHCP.
  
2. W okienku nawigacji rozwiń nazwę serwera, kliknij prawym przyciskiem myszy **IPv4**i kliknij przycisk **nowego zakresu**. Kreator nowych zakresów, kliknij polecenie **dalej**.
  
3. Wprowadź nazwę i opis dla zakresu, a następnie kliknij przycisk **dalej**.
  
4. Zdefiniuj zakres IP serwera DHCP (na przykład 192.168.0.100: aby 192.168.0.200).
  
5. Kliknij przycisk **dalej** aż do strony brama domyślna. Wprowadź adres IP został utworzony wcześniej (na przykład 192.168.0.1) jako brama domyślna.
  
6. Kliknij przycisk **dalej** aż kreator zakończy pracę, pozostawiając wszystkie wartości domyślne, następnie kliknij przycisk **Zakończ**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opcja 2: Ręcznie ustawić statyczny adres IP na maszynie wirtualnej gościa
Jeśli nie skonfigurowano protokołu DHCP można dynamicznie przypisać adres IP na maszynie wirtualnej gościa, wykonaj następujące kroki, aby określić statyczny adres IP.

1. Na maszynie Wirtualnej Azure Otwórz program PowerShell jako Administrator.

2. Kliknij prawym przyciskiem myszy maszyny wirtualnej gościa, a następnie kliknij przycisk Połącz.

3. Zaloguj się na maszynie wirtualnej gościa.

4. Na maszynie wirtualnej gościa Otwórz Centrum sieci i udostępniania.

5. Skonfiguruj kartę sieciową dla adresów w zasięgu sieci translatora adresów Sieciowych, utworzony w poprzedniej sekcji.

W tym przykładzie będzie używać adresu w zakresie 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testowanie łączności w maszynie wirtualnej gościa

Na maszynie wirtualnej gościa Otwórz przeglądarkę i przejdź do strony sieci web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

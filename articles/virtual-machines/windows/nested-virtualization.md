---
title: Jak włączać wirtualizację zagnieżdżoną w usłudze Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Jak włączyć wirtualizacji zagnieżdżonej na maszynach wirtualnych platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: acb44a34eae84d8a5718ebcc0003d3cf50b9d43a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510061"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Jak włączyć wirtualizacji zagnieżdżonej na Maszynie wirtualnej platformy Azure

Wirtualizacja zagnieżdżona jest obsługiwana w kilku rodzin maszyn wirtualnych platformy Azure. Ta funkcja zapewnia dużą elastyczność w obsłudze scenariuszy, takich jak środowiska programowania, testowania, szkolenia i demonstracyjnych.   

Tym artykule omówiono włączanie funkcji Hyper-V na Maszynie wirtualnej platformy Azure i konfigurowanie połączenia internetowego z tej maszyny wirtualnej gościa.

## <a name="create-a-nesting-capable-azure-vm"></a>Tworzenie zagnieżdżenia zdolne do maszyny Wirtualnej platformy Azure

Utwórz nowy systemu Windows Server 2016 maszyny Wirtualnej platformy Azure.  Dla szybkiego odwołania wszystkie maszyny wirtualne w wersji 3 obsługuje wirtualizację zagnieżdżoną. Aby uzyskać pełną listę maszyn wirtualnych o rozmiarach zagnieżdżanie tej pomocy technicznej, zapoznaj się z [artykułu jednostek obliczeniowych Azure](acu.md).

Pamiętaj wybrać rozmiar maszyny Wirtualnej wystarczająco duży, do obsługi wymagań maszyny wirtualnej gościa. W tym przykładzie używamy D3_v3 rozmiar maszyny Wirtualnej platformy Azure. 

Możesz wyświetlić dostępności regionalnej maszyn wirtualnych serii Dv3 i Ev3 [tutaj](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Aby uzyskać szczegółowe instrukcje dotyczące tworzenia nowej maszyny wirtualnej, zobacz [tworzenie i zarządzanie maszynami wirtualnymi Windows za pomocą modułu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Łączenie do maszyny Wirtualnej platformy Azure

Utwórz połączenie pulpitu zdalnego z maszyną wirtualną.

1. Kliknij przycisk **Połącz** we właściwościach maszyny wirtualnej. Zostanie utworzony i pobrany plik Remote Desktop Protocol (rdp).

2. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

3. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie kliknij przycisk **OK**.

4. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Włącz funkcję Hyper-V na maszynie Wirtualnej platformy Azure
Te ustawienia można skonfigurować ręcznie lub zostały zamieszczone, aby skrypt programu PowerShell, aby zautomatyzować konfigurację.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opcja 1: Użyj skryptu programu PowerShell do konfigurowania wirtualizacji zagnieżdżonej
Skrypt programu PowerShell, aby włączyć wirtualizacji zagnieżdżonej na hoście systemu Windows Server 2016 jest dostępny w [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skrypt sprawdza wymagania wstępne, a następnie konfiguruje wirtualizacji zagnieżdżonej na maszynie Wirtualnej platformy Azure. Ponowne uruchomienie maszyny Wirtualnej platformy Azure jest niezbędne do ukończenia konfiguracji. Ten skrypt może działać w innych środowiskach, ale nie ma żadnej gwarancji. Sprawdź wpis w blogu platformy Azure przy użyciu pokaz wideo na żywo na wirtualizacji zagnieżdżonej na platformie Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opcja 2: Ręczne konfigurowanie wirtualizacji zagnieżdżonej

1. Na maszynie Wirtualnej platformy Azure Otwórz program PowerShell jako Administrator. 

2. Włącz funkcję Hyper-V i narzędzia do zarządzania.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >To polecenie powoduje ponowne uruchomienie maszyny Wirtualnej platformy Azure. Użytkownik utraci połączenie RDP podczas procesu ponownego uruchomienia.
    
3. Po ponownym uruchomieniu maszyny Wirtualnej platformy Azure, ponownie nawiązać połączenie z maszyną Wirtualną przy użyciu protokołu RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Skonfiguruj połączenie z Internetem dla maszyny wirtualnej gościa
Tworzenie nowego wirtualnej karty sieciowej dla maszyny wirtualnej gościa i skonfigurować bramę translatora adresów Sieciowych, aby włączyć łączność z Internetem.

### <a name="create-a-nat-virtual-network-switch"></a>Tworzenie przełącznika sieci wirtualnej translatora adresów Sieciowych

1. Na maszynie Wirtualnej platformy Azure Otwórz program PowerShell jako Administrator.
   
2. Tworzenie przełącznika wewnętrznego.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Wyświetlanie właściwości przełącznika i zanotuj ifIndex nowej karty.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Zwróć uwagę na "ifIndex" dla przełącznika wirtualnego, który został utworzony.
    
4. Utwórz adres IP dla bramy translatora adresów Sieciowych.
    
Aby skonfigurować bramę, potrzebne będą pewne informacje o sieci:    
  * Adres IP — adres IP translatora adresów Sieciowych bramy Określa adres IPv4 lub IPv6 do użycia jako adres bramy domyślnej dla podsieci sieci wirtualnej. Formularz ogólny to a.b.c.1 (na przykład "192.168.0.1"). Gdy pozycja końcowego nie musi być.1, on zazwyczaj (oparte na długość prefiksu). Zazwyczaj należy użyć przestrzeń adresową sieci prywatnej RFC 1918. 
  * PrefixLength — długość prefiksu podsieci definiuje rozmiar podsieci lokalnej (maska podsieci). Długość prefiksu podsieci będzie całkowitą z zakresu od 0 do 32. 0 mapującej cały internet, 32 pozwoliłoby tylko jeden adres IP zamapowany. Typowe wartości należą do zakresu od 24 do 12, w zależności od tego, jak wiele adresów IP muszą być dołączone do translatora adresów sieciowych. Typowe PrefixLength wynosi 24 — jest to maską podsieci 255.255.255.0.
  * InterfaceIndex - **ifIndex** jest indeksem przełącznika wirtualnego utworzonego w poprzednim kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Tworzenie sieci NAT.

Aby skonfigurować bramę, należy podać informacje dotyczące sieci i translatora adresów Sieciowych bramy:
  * Nazwa — jest to nazwa sieci translatora adresów Sieciowych. 
  * InternalIPInterfaceAddressPrefix — prefiks podsieci translatora adresów Sieciowych w tym artykule opisano zarówno prefiks IP bramy translatora adresów Sieciowych z powyższych, jak i długość prefiksu podsieci translatora adresów Sieciowych z góry. Formularz ogólny będzie a.b.c.0/NAT długość prefiksu podsieci. 

W programie PowerShell należy utworzyć nową sieć translatora adresów Sieciowych.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Tworzenie maszyny wirtualnej gościa

1. Otwórz Menedżera funkcji Hyper-V i utworzenie nowej maszyny wirtualnej. Skonfiguruj maszynę wirtualną, aby użyć nowych sieci wewnętrznej, utworzony.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Zainstaluj system operacyjny na maszynie wirtualnej gościa.
    
    >[!NOTE] 
    >
    >Potrzebny jest nośnik instalacyjny systemu operacyjnego do zainstalowania na maszynie Wirtualnej. W tym przypadku używamy systemu Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Przypisz adres IP do maszyny wirtualnej gościa

Adres IP można przypisać do maszyny wirtualnej gościa, ręcznie ustawienie statycznego adresu IP na maszynie wirtualnej gościa lub konfigurowanie protokołu DHCP na maszynie Wirtualnej platformy Azure, aby dynamicznie przypisać adres IP.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opcja 1: Konfigurowanie serwera DHCP, aby dynamicznie przypisany adres IP maszyny wirtualnej gościa
Wykonaj poniższe kroki, aby skonfigurować serwer DHCP na hosta maszyny wirtualnej w przypadku przypisywania adresów dynamicznych.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Zainstaluj serwer DHCP na maszynie Wirtualnej platformy Azure

1. Otwórz Menedżera serwera. Na pulpicie nawigacyjnym kliknij **Dodaj role i funkcje**. Dodaj role i funkcje kreatora jest wyświetlana.
  
2. W kreatorze kliknij pozycję **dalej** aż do strony role serwera.
  
3. Kliknij, aby wybrać **serwera DHCP** pole wyboru, kliknij przycisk **Dodaj funkcje**, a następnie kliknij przycisk **dalej** aż kreator zakończy działanie.
  
4. Kliknij pozycję **Zainstaluj**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurowanie nowego zakresu DHCP

1. Otwórz Menedżera usługi DHCP.
  
2. W okienku nawigacji rozwiń nazwę serwera, kliknij prawym przyciskiem myszy **IPv4**i kliknij przycisk **nowego zakresu**. Pojawi się Kreator nowych zakresów, kliknij przycisk **dalej**.
  
3. Wprowadź nazwę i opis dla zakresu, a następnie kliknij przycisk **dalej**.
  
4. Zdefiniuj zakres adresów IP dla serwera DHCP (na przykład 192.168.0.100: aby 192.168.0.200).
  
5. Kliknij przycisk **dalej** aż do strony brama domyślna. Wprowadź adres IP utworzonego wcześniej (na przykład od 192.168.0.1) jako brama domyślna, a następnie kliknij przycisk **Dodaj**.
  
6. Kliknij przycisk **dalej** aż kreator zakończy pracę, pozostawiając wszystkich wartości domyślnych, następnie kliknij przycisk **Zakończ**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opcja 2: Ręcznie ustawić statyczny adres IP na maszynie wirtualnej gościa
Jeśli nie skonfigurowano serwera DHCP do dynamiczne przypisywanie adresu IP do maszyny wirtualnej gościa, wykonaj następujące kroki, aby skonfigurować statyczny adres IP.

1. Na maszynie Wirtualnej platformy Azure Otwórz program PowerShell jako Administrator.

2. Kliknij prawym przyciskiem myszy maszyny wirtualnej gościa, a następnie kliknij przycisk Połącz.

3. Zaloguj się do maszyny wirtualnej gościa.

4. Na maszynie wirtualnej gościa Otwórz Centrum sieci i udostępniania.

5. Skonfiguruj kartę sieciową dla adresów w zakresie sieci translatora adresów Sieciowych, który został utworzony w poprzedniej sekcji.

W tym przykładzie będzie używać adresu w zakresie 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testowanie łączności w maszynie wirtualnej gościa

Na maszynie wirtualnej gościa Otwórz przeglądarkę i przejdź do strony sieci web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Konfigurowanie połączenia intranetowego dla maszyny wirtualnej gościa

Aby uzyskać instrukcje dotyczące włączania przezroczyste łączność maszyn wirtualnych gościa oraz maszyny wirtualne platformy Azure, odwołać [w tym dokumencie](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).

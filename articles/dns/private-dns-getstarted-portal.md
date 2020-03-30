---
title: Szybki start — tworzenie prywatnej strefy DNS platformy Azure przy użyciu portalu Azure
description: W tym przewodniku Szybki start tworzysz i testujesz prywatną strefę DNS i rejestrujesz w usłudze Azure DNS. Jest to przewodnik krok po kroku, aby utworzyć pierwszą prywatną strefę DNS i nagrywać pierwszą prywatną strefę DNS i zarządzać nią za pomocą witryny Azure portal.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: rohink
ms.openlocfilehash: 52bf9e061eb57c7ce6ea698b7468b5ba5e11b4e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244976"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Szybki start: tworzenie prywatnej strefy DNS platformy Azure przy użyciu portalu Azure

Ten przewodnik Szybki start przeprowadzi Cię przez kroki, aby utworzyć pierwszą prywatną strefę DNS i nagrywać za pomocą witryny Azure portal.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są one nazywane *połączonymi* sieciami wirtualnymi. Gdy autoregistration jest włączona, usługa Azure DNS aktualizuje również rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia swój adres IP lub jest usuwany.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie prywatnej strefy DNS
> * Tworzenie sieci wirtualnej
> * Łączenie sieci wirtualnej
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Jeśli wolisz, możesz ukończyć ten szybki start przy użyciu [programu Azure PowerShell](private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure.](private-dns-getstarted-cli.md)

## <a name="create-a-private-dns-zone"></a>Tworzenie prywatnej strefy DNS

Poniższy przykład tworzy strefę DNS o nazwie **private.contoso.com** w grupie zasobów o nazwie **MyAzureResourceGroup**.

Strefa DNS zawiera wpisy DNS dla domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, należy utworzyć strefę DNS dla tej nazwy domeny.

![Wyszukiwanie prywatnych stref DNS](media/private-dns-portal/search-private-dns.png)

1. Na pasku wyszukiwania portalu wpisz **prywatne strefy dns** w polu tekstowym wyszukiwania i naciśnij klawisz **Enter**.
1. Wybierz **pozycję Prywatna strefa DNS**.
2. Wybierz **pozycję Utwórz prywatną strefę dns**.

1. Na stronie **Tworzenie prywatnej strefy DNS** wpisz lub wybierz następujące wartości:

   - **Grupa zasobów**: Wybierz **pozycję Utwórz nowy**, wprowadź *myAzureResourceGroup*i wybierz **przycisk OK**. Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure.
   -  **Nazwa**: Wpisz *private.contoso.com* w tym przykładzie.
1. W **obszarze Lokalizacja grupy zasobów**wybierz pozycję Zachodnie **środkowe stany USA**.

1. Wybierz **pozycję Recenzja + Utwórz**.

1. Wybierz **pozycję Utwórz**.

Tworzenie strefy może potrwać kilka minut.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | MyAzureResourceGroup (Wybierz istniejącą grupę zasobów) |
| **\<>nazwa sieci wirtualnej** | MyAzureVNet (MyAzureVNet)          |
| **\<nazwa regionu>**          | Zachodnio-środkowe stany USA      |
| **\<>przestrzeni adresowej IPv4**   | 10.2.0.0\16          |
| **\<>nazwa podsieci**          | MyAzureSubnet ( MyAzureSubnet )        |
| **\<>zakresu adresu podsieci** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Łączenie sieci wirtualnej

Aby połączyć prywatną strefę DNS z siecią wirtualną, należy utworzyć łącze sieci wirtualnej.

![Dodawanie łącza do sieci wirtualnej](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otwórz grupę zasobów **MyAzureResourceGroup** i wybierz **strefę prywatną private.contoso.com.**
2. W lewym okienku wybierz pozycję **Łącza sieci wirtualnej**.
3. Wybierz pozycję **Dodaj**.
4. Wpisz **myLink** dla **nazwy łącza**.
5. W przypadku **sieci wirtualnej**wybierz **myAzureVNet**.
6. Zaznacz pole wyboru **Włącz automatyczną rejestrację.**
7. Kliknij przycisk **OK**.

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

1. Na stronie portalu w lewym górnym rogu wybierz pozycję **Utwórz zasób**, a następnie wybierz pozycję **Centrum danych systemu Windows Server 2016**.
1. Wybierz **myazureResourceGroup** dla grupy zasobów.
1. Wpisz **myVM01** - dla nazwy maszyny wirtualnej.
1. Wybierz **pozycję Zachodnie środkowe stany USA** dla **regionu**.
1. Wprowadź nazwę użytkownika administratora.
2. Wprowadź hasło i potwierdź hasło.
5. W obszarze **Publiczne porty przychodzące**wybierz pozycję **Zezwalaj na wybrane porty**, a następnie wybierz pozycję **RDP (3389)** dla **wybierz pozycję Wybierz porty przychodzące**.
10. Zaakceptuj inne ustawienia domyślne strony, a następnie kliknij przycisk **Dalej: Dyski >**.
11. Zaakceptuj ustawienia domyślne na stronie **Dyski,** a następnie kliknij przycisk **Dalej: >sieci **.
1. Upewnij się, że **myAzureVNet** jest wybrany dla sieci wirtualnej.
1. Zaakceptuj inne ustawienia domyślne strony, a następnie kliknij przycisk **Dalej: Zarządzanie >**.
2. W polu **Diagnostyka rozruchowa**wybierz pozycję **Wyłączone**, zaakceptuj inne wartości domyślne, a następnie wybierz **pozycję Przejrzyj + utwórz**.
1. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.

Powtórz te kroki i utwórz kolejną maszynę wirtualną o nazwie **myVM02**.

Zajmie kilka minut dla obu maszyn wirtualnych, aby zakończyć.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

 Poniższy przykład tworzy rekord o względnej nazwie **db** w **strefie**DNS private.contoso.com , w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów jest **db.private.contoso.com**. Typ rekordu to "A", z adresem IP **myVM01**.

1. Otwórz grupę zasobów **MyAzureResourceGroup** i wybierz **strefę prywatną private.contoso.com.**
2. Wybierz pozycję **+ Zestaw rekordów**.
3. W **yjaj nazwę**wpisz **db**.
4. W przypadku **adresu IP**wpisz adres IP, który jest widoczny dla **myVM01**. Powinno to być automatycznie rejestrowane po uruchomieniu maszyny wirtualnej.
5. Kliknij przycisk **OK**.

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz możesz przetestować rozpoznawanie nazw dla **private.contoso.com** strefy prywatnej.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Powtórz dla maszyny wirtualnej myVM02.

### <a name="ping-the-vms-by-name"></a>Wysyłanie polecenia ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell maszyny wirtualnej myVM02 wyślij polecenie ping do maszyny wirtualnej myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:
   ```
   ping myVM01.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Teraz wyślij polecenie ping do utworzonej wcześniej nazwy **db**:
   ```
   ping db.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Gdy nie jest już potrzebna, usuń grupę zasobów **MyAzureResourceGroup,** aby usunąć zasoby utworzone w tym przewodniku Szybki start.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Scenariusze usługi Azure DNS Private Zones](private-dns-scenarios.md)


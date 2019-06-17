---
title: Tworzenie strefy prywatnej DNS platformy Azure przy użyciu witryny Azure portal
description: W tym samouczku utworzysz i przetestujesz strefę prywatną i rekord DNS w usłudze Azure DNS. Jest to przewodnik krok po kroku, aby utworzyć i zarządzać po raz pierwszy prywatnej strefy DNS i rekordów przy użyciu witryny Azure portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: e3772d8f385ed93c96f8aa2f7ee2ded8f46d4e00
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147819"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-the-azure-portal"></a>Samouczek: Tworzenie strefy prywatnej DNS platformy Azure przy użyciu witryny Azure portal

Ten samouczek przeprowadzi Cię przez kroki umożliwiające utworzenie po raz pierwszy prywatnej strefy DNS i rekordów przy użyciu witryny Azure portal.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są to tak zwane *połączone* sieci wirtualnych. Po włączeniu autorejestracją system DNS Azure aktualizuje również rekordy strefy zawsze wtedy, gdy maszyna wirtualna jest tworzona, zmiany jego "adres IP lub został usunięty.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie strefy prywatnej DNS
> * Tworzenie sieci wirtualnej
> * Łączenie sieci wirtualnych
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli wolisz, możesz wykonać ten samouczek przy użyciu [programu Azure PowerShell](private-dns-getstarted-powershell.md) lub [wiersza polecenia platformy Azure](private-dns-getstarted-cli.md).

## <a name="create-a-dns-private-zone"></a>Tworzenie strefy prywatnej DNS

Poniższy przykład tworzy strefę DNS o nazwie **private.contoso.com** w grupie zasobów o nazwie **MyAzureResourceGroup**.

Strefa DNS zawiera wpisy DNS dla domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, należy utworzyć strefę DNS dla tej nazwy domeny.

![Prywatna strefa DNS strefy wyszukiwania](media/private-dns-portal/search-private-dns.png)

1. Na pasku wyszukiwania portalu, wpisz **strefy prywatnej dns** w polu tekstowym wyszukiwania, a następnie naciśnij klawisz **Enter**.
1. Wybierz **strefy prywatnej DNS**.
2. Wybierz **strefy prywatnej strefy dns Utwórz**.

1. Na **Tworzenie prywatnej strefy DNS** strony, wpisz lub wybierz następujące wartości:

   - **Grupa zasobów**: Wybierz **Utwórz nową**, wprowadź *MyAzureResourceGroup*i wybierz **OK**. Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure. 
   -  **Nazwa**: Typ *private.contoso.com* w tym przykładzie.
1. Aby uzyskać **lokalizację grupy zasobów**, wybierz opcję **zachodnio-środkowe stany USA**.

1. Wybierz pozycję **Przegląd + utwórz**.

1. Wybierz pozycję **Utwórz**.

Tworzenie strefy może potrwać kilka minut.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W lewym górnym rogu strony portalu wybierz **Utwórz zasób**, następnie **sieć**, a następnie wybierz **sieć wirtualna**.
2. Aby uzyskać **nazwa**, typ **myAzureVNet**.
3. Aby uzyskać **grupy zasobów**, wybierz opcję **MyAzureResourceGroup**.
4. Aby uzyskać **lokalizacji**, wybierz opcję **zachodnio-środkowe stany USA**.
5. Zaakceptuj pozostałe wartości domyślne i wybierz **Utwórz**.

## <a name="link-the-virtual-network"></a>Łączenie sieci wirtualnych

Aby połączyć prywatnej strefy DNS w sieci wirtualnej, należy utworzyć łącze sieci wirtualnej.

![Dodaj łącze sieci wirtualnej](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otwórz **MyAzureResourceGroup** zasobu, grupy i wybierz **private.contoso.com** stref prywatnych.
2. W okienku po lewej stronie wybierz **łączy sieci wirtualnej**.
3. Wybierz pozycję **Dodaj**.
4. Typ **myLink** dla **nazwę łącza**.
5. Aby uzyskać **sieć wirtualna**, wybierz opcję **myAzureVNet**.
6. Wybierz **włączyć automatyczne rejestrowanie** pole wyboru.
7. Kliknij przycisk **OK**.

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

1. W lewym górnym rogu strony portalu wybierz **Utwórz zasób**, a następnie wybierz pozycję **systemu Windows Server 2016 Datacenter**.
1. Wybierz **MyAzureResourceGroup** dla grupy zasobów.
1. Typ **myVM01** — jako nazwę maszyny wirtualnej.
1. Wybierz **zachodnie środkowe stany USA** dla **Region**.
1. Typ **azureadmin** dla nazwy użytkownika administratora.
2. Typ **Azure12345678** o hasło i Potwierdź hasło.

5. Dla **publiczne porty wejściowe**, wybierz opcję **Zezwalaj na wybranych portach**, a następnie wybierz pozycję **protokołu RDP (3389)** dla **wybierz porty dla ruchu przychodzącego**.
10. Zaakceptuj ustawienia domyślne dla strony, a następnie kliknij przycisk **dalej: Dyski >** .
11. Zaakceptuj wartości domyślne na **dysków** stronie, a następnie kliknij przycisk **dalej: Sieć >** .
1. Upewnij się, że **myAzureVNet** została wybrana sieć wirtualna.
1. Zaakceptuj ustawienia domyślne dla strony, a następnie kliknij przycisk **dalej: Zarządzanie >** .
2. Dla **diagnostykę rozruchu**, wybierz opcję **poza**, Zaakceptuj pozostałe wartości domyślne, a następnie wybierz **przeglądu + Utwórz**.
1. Przejrzyj ustawienia a następnie kliknij przycisk **Utwórz**.

Powtórz te kroki i utworzyć inną maszynę wirtualną o nazwie **myVM02**.

Potrwa kilka minut, aż obie maszyny wirtualne zakończyć.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

 Poniższy przykład tworzy rekord o nazwie względnej **db** w strefie DNS **private.contoso.com**, w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów to **db.private.contoso.com**. Typ rekordu to "A", adres IP **myVM01**.

1. Otwórz **MyAzureResourceGroup** zasobu, grupy i wybierz **private.contoso.com** stref prywatnych.
2. Wybierz pozycję **+ Zestaw rekordów**.
3. Aby uzyskać **nazwa**, typ **db**.
4. Aby uzyskać **adresu IP**, wpisz adres IP, zobaczysz **myVM01**. Powinna to być automatycznie rejestrowane po uruchomieniu maszyny wirtualnej.
5. Kliknij przycisk **OK**.

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz możesz sprawdzić rozpoznawanie nazw dla swojej **private.contoso.com** stref prywatnych.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, możesz je usunąć, usuwając grupę zasobów **MyAzureResourceGroup**.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wdrożono strefę prywatną DNS, utworzono rekord DNS oraz przetestowano strefę.
Następnie możesz dowiedzieć się więcej na temat stref prywatnych DNS.

> [!div class="nextstepaction"]
> [Używanie usługi Azure DNS na potrzeby domen prywatnych](private-dns-overview.md)

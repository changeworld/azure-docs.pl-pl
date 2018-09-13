---
title: Łączenie usługi Azure Stack na platformie Azure przy użyciu sieci VPN
description: Jak połączyć sieci wirtualne w usłudze Azure Stack z sieciami wirtualnymi na platformie Azure przy użyciu sieci VPN.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: scottnap
ms.openlocfilehash: bd36f423fe2c7acb05b805154da8f6e6c35f9455
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718138"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Łączenie usługi Azure Stack na platformie Azure przy użyciu sieci VPN

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule pokazano, jak utworzyć sieć VPN lokacja lokacja do łączenia sieci wirtualnych w usłudze Azure Stack z siecią wirtualną na platformie Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć konfigurację połączenia, upewnij się, że masz następujące elementy, przed rozpoczęciem:

* Azure Stack zintegrowane wdrożenie systemów (z wieloma węzłami), który jest bezpośrednio połączony z Internetem. Zewnętrzne publiczne zakres adresów IP musi być bezpośrednio osiągalna z publicznego Internetu.
* Ważnej subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto platformy Azure, w tym miejscu](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagram połączenia sieci VPN

Na poniższym diagramie przedstawiono konfigurację połączenia jak powinien wyglądać po wykonaniu tych czynności:

![Konfiguracja połączenia sieci VPN typu lokacja lokacja](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Wartości przykład konfiguracji sieci

Tabela przykłady konfiguracji sieci zawiera wartości, które są używane na potrzeby przykładów w tym artykule. Można użyć tych wartości, lub mogą odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Przykłady konfiguracji sieci**

|   |Azure Stack|Azure|
|---------|---------|---------|
|Nazwa sieci wirtualnej     |Azs-VNet|AzureVNet |
|Przestrzeń adresowa sieci wirtualnej |10.1.0.0/16|10.100.0.0/16|
|Nazwa podsieci     |FrontEnd|FrontEnd|
|Zakres adresów podsieci|10.1.0.0/24 |10.100.0.0/24 |
|Podsieć bramy     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Tworzenie zasobów sieciowych na platformie Azure

Najpierw należy utworzyć zasobów sieciowych na platformie Azure. Poniższe instrukcje przedstawiają sposób tworzenia zasobów za pomocą [witryny Azure portal](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny wirtualnej (VM)

1. Zaloguj się do [witryny Azure portal](http://portal.azure.com/) przy użyciu konta platformy Azure.
2. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
3. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
4. Wybierz **sieć wirtualna**.
5. Użyj informacji z tabeli konfiguracji sieci, aby zidentyfikować wartości dla platformy Azure **nazwa**, **przestrzeń adresowa**, **Nazwa podsieci**, i **adres podsieci zakres**.
6. Aby uzyskać **grupy zasobów**, Utwórz nową grupę zasobów, lub jeśli już masz, wybierz **Użyj istniejącej**.
7. Wybierz **lokalizacji** Twojej sieci wirtualnej.  Jeśli używasz przykładowych wartości, wybierz opcję **wschodnie stany USA** lub użyć innej lokalizacji, jeśli użytkownik sobie tego życzy.
8. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
9. Wybierz pozycję **Utwórz**.

### <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. Otwórz właśnie utworzony zasób sieci wirtualnej (**AzureVNet**) z poziomu pulpitu nawigacyjnego.
2. Na **ustawienia** zaznacz **podsieci**.
3. Wybierz **podsieci bramy** dodać podsieć bramy do sieci wirtualnej.
4. Domyślna nazwa podsieci to **GatewaySubnet**.

   >[!IMPORTANT]
   >Podsieci bramy są specjalne i **musi** mieć dokładnie tę nazwę, aby działać prawidłowo.

5. W **zakres adresów** pola, sprawdź adres jest **10.100.1.0/24**.
6. Wybierz **OK** utworzyć podsieć bramy.

### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.  
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Wybierz z listy zasobów sieciowych, **bramy sieci wirtualnej**.
4. W **nazwa**, typ **Azure-GW**.
5. Aby wybrać sieć wirtualną, wybrać **sieć wirtualna**. Następnie wybierz pozycję **AzureVnet** z listy.
6. Wybierz **publiczny adres IP**. Gdy **wybierz publiczny adres IP** zostanie otwarta sekcja, wybierz opcję **Utwórz nową**.
7. W **nazwa**, typ **Azure-GW-PiP**, a następnie wybierz pozycję **OK**.
8. Domyślnie dla **typu sieci VPN**, **oparte na trasach** jest zaznaczone. Zachowaj **oparte na trasach** typu sieci VPN.
9. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Możesz przypiąć zasób do pulpitu nawigacyjnego. Wybierz pozycję **Utwórz**.

### <a name="create-the-local-network-gateway-resource"></a>Tworzenie zasobu Brama sieci lokalnej

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Z listy zasobów wybierz **bramy sieci lokalnej**.
4. W **nazwa**, typ **Azs-GW**.
5. W **adres IP**, wpisz publiczny adres IP dla usługi Azure Stack bramy sieci wirtualnej wymienionych wcześniej w tabeli konfiguracji sieci.
6. W **przestrzeni adresowej**, z usługi Azure Stack, wpisz **10.1.0.0/24** i **10.1.1.0/24** przestrzeń adresowa **AzureVNet**.
7. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** są poprawne, a następnie wybierz **Utwórz**.

## <a name="create-the-connection"></a>Tworzenie połączenia

1. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Z listy zasobów wybierz **połączenia**.
4. Na **podstawowe** sekcji Ustawienia dla **typu połączenia**, wybierz **lokacja lokacja (IPSec)**.
5. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
6. Na **ustawienia** zaznacz **bramy sieci wirtualnej**, a następnie wybierz pozycję **Azure-GW**.
7. Wybierz **bramy sieci lokalnej**, a następnie wybierz pozycję **Azs-GW**.
8. W **nazwa połączenia**, typ **Azure Azs**.
9. W **klucz współużytkowany (PSK)**, typ **12345**. Kliknij przycisk **OK**.

   >[!NOTE]
   >Jeśli używasz innej wartości dla klucza współużytkowanego, należy pamiętać, że *musi* pasuje do wartości dla klucza współużytkowanego, tworzona na drugiej stronie połączenia.

10. Przegląd **Podsumowanie** sekcji, a następnie wybierz **OK**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Teraz Utwórz maszynę wirtualną na platformie Azure i umieść je w podsieci maszyny Wirtualnej w sieci wirtualnej.

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **obliczenia**.
3. Z listy obrazów maszyn wirtualnych wybierz **systemu Windows Server 2016 Datacenter Oval** obrazu.
4. Na **podstawy** sekcji dla **nazwa**, typ **AzureVM**.
5. Wpisz prawidłową nazwę użytkownika i hasło. Użyjesz tego konta do logowania się do maszyny wirtualnej, po jego utworzeniu.
6. Podaj **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
7. Na **rozmiar** sekcji, wybierz rozmiar maszyny wirtualnej dla tego wystąpienia, a następnie wybierz **wybierz**.
8. Na **ustawienia** sekcji, można użyć ustawień domyślnych. Zanim wybierzesz OK, upewnij się, że:

   * **AzureVnet** została wybrana sieć wirtualnych.
   * Podsieć jest ustawiona na **10.100.0.0/24**.

   Kliknij przycisk **OK**.

9. Sprawdź ustawienia na **Podsumowanie** sekcji, a następnie wybierz **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Tworzenie zasobów sieciowych w usłudze Azure Stack

Następnie należy utworzyć zasobów sieciowych w usłudze Azure Stack.

### <a name="sign-in-as-a-user"></a>Zaloguj się jako użytkownik

Administrator usługi może Zaloguj się jako użytkownik Aby przetestować plany, oferty i subskrypcje, które mogą używać swoich użytkowników. Jeśli nie masz jeszcze jeden, [Utwórz konto użytkownika](azure-stack-add-new-user-aad.md) przed zalogowaniem.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny Wirtualnej

1. Użyj konta użytkownika do logowania do portalu użytkowników.
2. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.

    ![Utwórz nową sieć wirtualną](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
4. Wybierz **sieć wirtualna**.
5. Dla **nazwa**, **przestrzeń adresowa**, **Nazwa podsieci**, i **zakres adresów podsieci**, użyj wartości z tabeli konfiguracji sieci.
6. W **subskrypcji**, pojawi się w subskrypcji, która została utworzona wcześniej.
7. Aby uzyskać **grupy zasobów**, możesz utworzyć grupę zasobów lub jeśli już istnieje, wybrać **Użyj istniejącej**.
8. Sprawdź lokalizację domyślną.
9. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
10. Wybierz pozycję **Utwórz**.

### <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. Na pulpicie nawigacyjnym otwórz utworzony zasób sieci wirtualnej Azs-sieć wirtualna.
2. Na **ustawienia** zaznacz **podsieci**.
3. Aby dodać podsieć bramy do sieci wirtualnej, wybierz **podsieci bramy**.

    ![Dodaj podsieć bramy](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Domyślnie nazwa podsieci jest równa **GatewaySubnet**. Podsieć bramy to specjalne. Aby działać poprawnie, muszą oni korzystać z *GatewaySubnet* nazwy.
5. W **zakres adresów**, sprawdź, czy adres jest **10.1.1.0/24**.
6. Wybierz **OK** utworzyć podsieć bramy.

### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W portalu usługi Azure Stack, wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Wybierz z listy zasobów sieciowych, **bramy sieci wirtualnej**.
4. W **nazwa**, typ **Azs-GW**.
5. Wybierz **sieć wirtualna** element, aby wybrać sieć wirtualną. Wybierz **Azs-sieć wirtualna** z listy.
6. Wybierz **publiczny adres IP** elementu menu. Gdy **wybierz publiczny adres IP** zostanie otwarta sekcja, wybierz opcję **Utwórz nową**.
7. W **nazwa**, typ **Azs-GW-PiP**, a następnie wybierz pozycję **OK**.
8. Domyślnie **oparte na trasach** wybrano **typu sieci VPN**. Zachowaj **oparte na trasach** typu sieci VPN.
9. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Możesz przypiąć zasób do pulpitu nawigacyjnego. Wybierz pozycję **Utwórz**.

### <a name="create-the-local-network-gateway"></a>Tworzenie bramy sieci lokalnej

Pojęcie *bramy sieci lokalnej* w usłudze Azure Stack jest nieco inna niż we wdrożeniu platformy Azure.

We wdrożeniu platformy Azure bramy sieci lokalnej reprezentuje lokalne (w lokalizacji użytkownika) urządzenia fizycznego, którym jest nawiązywane połączenie bramy sieci wirtualnej na platformie Azure. Ale w usłudze Azure Stack obu końcach połączenia bramy sieci wirtualnej!

Bardziej ogólny sposób można to porównać jest zasobu Brama sieci lokalnej będzie zawsze informuje bramę zdalną po drugiej stronie połączenia.

### <a name="create-the-local-network-gateway-resource"></a>Tworzenie zasobu Brama sieci lokalnej

1. Zaloguj się do portalu usługi Azure Stack.
2. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
3. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
4. Z listy zasobów wybierz **bramy sieci lokalnej**.
5. W **nazwa**, typ **Azure-GW**.
6. W **adresu IP**, wpisz publiczny adres IP dla bramy sieci wirtualnej na platformie Azure **Azure-GW-PiP**. Ten adres będzie widoczny we wcześniejszej części tabeli konfiguracji sieci.
7. W **przestrzeni adresowej**, przestrzeni adresowej sieci Wirtualnej platformy Azure, który został utworzony, można wpisać **10.100.0.0/24** i **10.100.1.0/24**.
8. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** są poprawne, a następnie wybierz **Utwórz**.

### <a name="create-the-connection"></a>Tworzenie połączenia

1. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Z listy zasobów wybierz **połączenia**.
4. Na **podstawy** sekcji Ustawienia dla **typu połączenia**, wybierz opcję **lokacja lokacja (IPSec)**.
5. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
6. Na **ustawienia** zaznacz **bramy sieci wirtualnej**, a następnie wybierz pozycję **Azs-GW**.
7. Wybierz **bramy sieci lokalnej**, a następnie wybierz pozycję **Azure-GW**.
8. W **nazwa połączenia**, typ **Azs-Azure**.
9. W **klucz współużytkowany (PSK)**, typ **12345**, a następnie wybierz pozycję **OK**.
10. Na **Podsumowanie** zaznacz **OK**.

### <a name="create-a-virtual-machine-vm"></a>Utwórz maszynę wirtualną (VM)

Aby sprawdzić połączenie sieci VPN, należy utworzyć dwie maszyny wirtualne, jeden na platformie Azure i jeden w usłudze Azure Stack. Po utworzeniu tych maszyn wirtualnych, można je wysyłać i odbierać dane za pośrednictwem tunelu sieci VPN.

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **obliczenia**.
3. Z listy obrazów maszyn wirtualnych wybierz **systemu Windows Server 2016 Datacenter Oval** obrazu.
4. Na **podstawy** sekcji w **nazwa**, typ **Azs-VM**.
5. Wpisz prawidłową nazwę użytkownika i hasło. Użyjesz tego konta do logowania się do maszyny Wirtualnej, po jego utworzeniu.
6. Podaj **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
7. Na **rozmiar** sekcji dla tego wystąpienia, wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **wybierz**.
8. Na **ustawienia** sekcji, zaakceptuj wartości domyślne. Upewnij się, że **Azs-sieć wirtualna** została wybrana sieć wirtualnych. Sprawdź, czy podsieci jest równa **10.1.0.0/24**. Następnie wybierz przycisk **OK**.
9. Na **Podsumowanie** sekcji, przejrzyj ustawienia a następnie wybierz **OK**.

## <a name="test-the-connection"></a>Testowanie połączenia

Po nawiązaniu połączenia lokacja lokacja należy sprawdzić, czy można uzyskać danych przepływających w obu kierunkach. Najprostszym sposobem, aby przetestować połączenie jest wykonanie testu ping:

* Zaloguj się do maszyny wirtualnej utworzonej w usłudze Azure Stack i ping maszyny wirtualnej na platformie Azure.
* Zaloguj się do maszyny wirtualnej utworzonej na platformie Azure i wykonać polecenie ping do maszyny wirtualnej w usłudze Azure Stack.

>[!NOTE]
>Aby upewnić się, że wysyłasz ruch przez połączenie lokacja lokacja, wysłać polecenie ping na bezpośredni adres IP (DIP) adres maszyny wirtualnej w podsieci zdalnej, a nie adres VIP.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Zaloguj się do maszyny Wirtualnej użytkownika w usłudze Azure Stack

1. Zaloguj się do portalu usługi Azure Stack.
2. Na pasku nawigacyjnym po lewej stronie wybierz **maszyn wirtualnych**.
3. Na liście maszyn wirtualnych Znajdź **Azs-VM** , który został wcześniej utworzony, a następnie wybierz ją.
4. W sekcji dla maszyny wirtualnej, wybierz **Connect**, a następnie otwórz plik Azs VM.rdp.

     ![Przycisk Połącz](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Zaloguj się przy użyciu konta, które zostały skonfigurowane podczas tworzenia maszyny wirtualnej.
6. Otworzyć z podwyższonym poziomem uprawnień **programu Windows PowerShell** okna.
7. Wpisz polecenie **ipconfig /all**.
8. W danych wyjściowych, Znajdź **adres IPv4**, a następnie Zapisz adres w celu późniejszego użycia. Jest to adres, który wyślesz polecenie ping z platformy Azure. W środowisku przykładowym jest adres **10.1.0.4**, ale w Twoim środowisku może on być inny. Należy włączyć w **10.1.0.0/24** podsieci, który został utworzony wcześniej.
9. Aby utworzyć regułę zapory, która umożliwia maszynę wirtualną, aby odpowiadać na polecenia ping, uruchom następujące polecenie programu PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Zaloguj się do dzierżawy maszyn wirtualnych na platformie Azure

1. Zaloguj się do Portalu Azure.
2. Na pasku nawigacyjnym po lewej stronie wybierz **maszyn wirtualnych**.
3. Z listy maszyn wirtualnych, należy znaleźć **maszyny Wirtualnej platformy Azure** , który został wcześniej utworzony, a następnie wybierz ją.
4. W sekcji dla maszyny wirtualnej, wybierz **Connect**.
5. Zaloguj się przy użyciu konta, które zostały skonfigurowane podczas tworzenia maszyny wirtualnej.
6. Otworzyć z podwyższonym poziomem uprawnień **programu Windows PowerShell** okna.
7. Wpisz polecenie **ipconfig /all**.
8. Powinien zostać wyświetlony adres IPv4, który mieści się w **10.100.0.0/24**. W środowisku przykładowym jest adres **10.100.0.4**, ale Twój adres mogą się różnić.
9. Aby utworzyć regułę zapory, która umożliwia maszynę wirtualną, aby odpowiadać na polecenia ping, uruchom następujące polecenie programu PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Z maszyny wirtualnej na platformie Azure należy wysłać polecenie ping maszyny wirtualnej w usłudze Azure Stack za pośrednictwem tunelu. Aby to zrobić, należy wysłać polecenie ping na bezpośredni adres IP zarejestrowany na maszynie Wirtualnej Azs. W środowisku przykładowym jest to **10.1.0.4**, ale pamiętaj wysłać komunikat ping adres, możesz zauważyć, że w środowisku laboratoryjnym. Powinien zostać wyświetlony wynik, który wygląda jak poniższy zrzut ekranu:

    ![Żądanie ping zakończone powodzeniem](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)

11. Odpowiedź ze zdalnej maszyny wirtualnej wskazuje pomyślnego testowego! Możesz zamknąć okno maszyny wirtualnej.

Należy również wykonać bardziej rygorystyczne transfer danych, testowanie. Na przykład kopiowanie różnych wielkości pliki w obu kierunkach.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Wyświetlanie statystyk transferu danych za pośrednictwem połączenia bramy

Jeśli chcesz dowiedzieć się, jak dużo danych przechodzą przez połączenie lokacja lokacja, te informacje są dostępne na **połączenia** sekcji. Ten test jest również innym sposobem, aby sprawdzić, czy wysłane polecenie ping rzeczywiście przesłane przez połączenie VPN.

1. Zalogowano się do maszyny wirtualnej użytkownika w usłudze Azure Stack, użyć konta użytkownika do logowania do portalu użytkowników.
2. Przejdź do **wszystkie zasoby**, a następnie wybierz pozycję **Azs-Azure** połączenia. **Połączenia** pojawia się.
3. Na **połączenia** sekcji Statystyki dotyczące **dane w** i **dane wyjściowe** są wyświetlane. Na poniższym zrzucie ekranu dużych liczb są przypisane do transfer dodatkowych plików. Powinien zostać wyświetlony niektóre wartości wartość różną od zera.

    ![Dane wejściowe i wyjściowe](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie aplikacji na platformie Azure i usługi Azure Stack](azure-stack-solution-pipeline.md)

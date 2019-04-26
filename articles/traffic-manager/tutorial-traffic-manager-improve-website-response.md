---
title: Samouczek — kierowanie ruchu do poprawy odpowiedzi witryny sieci Web przy użyciu usługi Azure Traffic Manager
description: Ten artykuł zawierający samouczek zawiera opis sposobu tworzenia profilu usługi Traffic Manager, aby tworzyć witryny sieci Web o wysokiej dynamice.
services: traffic-manager
author: kumudd
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: kumud
ms.openlocfilehash: 6dea36afd3a426bbbd0c28a96f21ccad1a82ea88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60329855"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Samouczek: Poprawa odpowiedzi witryny sieci Web przy użyciu usługi Traffic Manager

W tym samouczku opisano, jak utworzyć witrynę sieci Web o wysokiej dynamice dzięki kierowaniu ruchu użytkownika do witryny internetowej z najniższych opóźnieniem za pomocą usługi Traffic Manager. Zazwyczaj centrum danych, która zapewnia najniższe opóźnienie jest znajdującej się na geograficznej odległości.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych z podstawową witryną internetową w usługach IIS
> * Tworzenie dwóch testowych maszyn wirtualnych, aby zobaczyć usługę Traffic Manager w działaniu
> * Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS
> * Utwórz profil usługi Traffic Manager dla wydajności ulepszone witryny sieci Web
> * Dodawanie punktów końcowych maszyny wirtualnej do profilu usługi Traffic Manager
> * Wyświetlanie informacji o działaniu usługi Traffic Manager

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby w tym samouczku zobaczyć usługi Traffic Manager w działaniu, trzeba wdrożyć:
- dwa wystąpienia podstawowych witryn internetowych działających w różnych regionach świadczenia usługi Azure — **Wschodnie stany USA** i **Europa Zachodnia**.
- dwa testowe maszyny wirtualne do testowania usługi Traffic Manager — jedną maszynę wirtualną w regionie **Wschodnie stany USA** i drugą maszynę wirtualna w regionie **Europa Zachodnia**. Testowych maszyn wirtualnych są używane, aby zilustrować, jak usługa Traffic Manager kieruje ruchu użytkowników do witryny sieci Web, która działa w tym samym regionie, ponieważ zapewnia najniższe opóźnienie.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

### <a name="create-websites"></a>Tworzenie witryn internetowych

W tej sekcji opisano tworzenie dwóch wystąpień witryny internetowej, które zapewniają dwa punkty końcowe usługi dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Tworzenie dwóch witryn internetowej obejmuje następujące kroki:
1. Utwórz dwie maszyny wirtualne w celu uruchomienia podstawowej witryny internetowej — jedną w regionie **Wschodnie stany USA**, a drugą w regionie **Europa Zachodnia**.
2. Zainstaluj serwer IIS na każdej maszynie wirtualnej i zaktualizuj domyślną stronę witryny internetowej, aby informowała o nazwie maszyny wirtualnej, z którą łączy się użytkownik podczas odwiedzania witryny internetowej.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn internetowych
W tej sekcji utworzysz dwie maszyny wirtualne *myIISVMEastUS* i *myIISVMWEurope* w regionach świadczenia usługi Azure **Wschodnie stany USA** i **Europa Zachodnia**.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Środowisko obliczeniowe** > **Maszyna wirtualna z systemem Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myIISVMEastUS|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Nowa**, a następnie wpisz *myResourceGroupTM1*.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:
    
    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet1*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |Sieciowa grupa zabezpieczeń|Wybierz opcję **Podstawowa**, a następnie z listy rozwijanej **Dodaj publiczne porty wejściowe** wybierz opcje **HTTP** i **RDP** |
    |Diagnostyka rozruchu|Wybierz opcję **Wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki 1–6 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Grupa zasobów | Wybierz pozycję **Nowa**, a następnie wpisz *myResourceGroupTM2*|
    |Lokalizacja|Europa Zachodnia|
    |Nazwa maszyny wirtualnej | myIISVMWEurope|
    |Sieć wirtualna | Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **nazwa** wprowadź *myVNet2*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |||

8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

   ![Tworzenie maszyny wirtualnej](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji zainstaluj serwer usług IIS na dwóch maszynach wirtualnych — *myIISVMEastUS* &  i *myIISVMWEurope*, a następnie zaktualizuj domyślną stronę witryny internetowej. Niestandardowa strona witryny internetowej przedstawia nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Menedżer serwera**.
7. Uruchom program Windows PowerShell na maszynie wirtualnej VM1 i użyj poniższych poleceń, aby zainstalować serwer usług IIS i zaktualizować domyślny plik HTM.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalowanie usług IIS i dostosowywanie strony internetowej](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Zamknij połączenie protokołu RDP z maszyną wirtualną *myIISVMEastUS*.
9. Powtórz kroki 1–8, tworząc połączenie RDP z maszyną wirtualną *myIISVMWEurope* w grupie zasobów *myResourceGroupTM2*, aby zainstalować usługi IIS i dostosować domyślną stronę internetową.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS dla serwerów usług IIS — *myIISVMEastUS* i *myIISVMWEurope*.

1. Kliknij opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1–3 dla maszyny wirtualnej o nazwie *myIISVMWEurope* znajdującej się w grupie zasobów *myResourceGroupTM1*.

### <a name="create-test-vms"></a>Tworzenie testowych maszyn wirtualnych

W tej sekcji utworzysz Maszynę wirtualną (*mVMEastUS* i *myVMWestEurope*) w każdym regionie platformy Azure (**wschodnie stany USA** i **Europa Zachodnia**. Te maszyny wirtualne posłużą do testowania, jak usługa Traffic Manager kieruje ruch do najbliższego serwera usług IIS, kiedy użytkownik nawiguje do witryny internetowej.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Środowisko obliczeniowe** > **Maszyna wirtualna z systemem Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVMEastUS|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Istniejąca** i wybierz grupę *myResourceGroupTM1*.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet3*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |Sieciowa grupa zabezpieczeń|Wybierz opcję **Podstawowa**, a następnie z listy rozwijanej **Dodaj publiczne porty wejściowe** wybierz opcje **HTTP** i **RDP** |
    |Diagnostyka rozruchu|Wybierz opcję **Wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki 1–5 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa maszyny wirtualnej | *myVMWEurope*|
    |Grupa zasobów | Wybierz pozycję **Istniejąca**, a następnie wpisz *myResourceGroupTM2*|
    |Sieć wirtualna | Wybierz **sieć wirtualna**w **Utwórz sieć wirtualną**, dla **nazwa**, wprowadź *myVNet4*, podsieć, wprowadź  *mySubnet*.|
    |||

8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
Tworzenie profilu usługi Traffic Manager, który kieruje ruch użytkowników tak, wysyłając je do punktu końcowego z najniższym opóźnieniem.

1. W lewej górnej części ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Profil usługi Traffic Manager** > **Utwórz**.
2. W obszarze **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Name (Nazwa)                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz **wydajności** metody routingu.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz **Utwórz nową** i wprowadź *myResourceGroupTM1*. |
    | Lokalizacja                | Wybierz pozycję **Wschodnie stany USA**. To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.                              |
    |

    ![Tworzenie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne uruchomione usługi IIS serwery — *myIISVMEastUS* & *myIISVMWEurope* na potrzeby kierowania ruchu użytkowników do najbliższego punktu końcowego dla użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Type                    | Punkt końcowy platformy Azure                                   |
    | Name (Nazwa)           | myEastUSEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *myIISVMEastUS-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać innego punktu końcowego o nazwie *myWestEuropeEndpoint* za publiczny adres IP *myIISVMWEurope ip* skojarzony z serwerem IIS maszyny Wirtualnej o nazwie *myIISVMWEurope* .
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

    ![Dodawanie punktu końcowego usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager
W tej sekcji możesz przetestować, usługi Traffic Manager kierowanie ruchu użytkowników do najbliższego maszyny wirtualne z witryny sieci Web do zapewnienia minimalnego opóźnienia. Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:
1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl informacje o działaniu usługi Traffic Manager w następujący sposób:
    - Z testową maszynę Wirtualną (*myVMEastUS*) znajdującą się w **wschodnie stany USA** regionu, w przeglądarce internetowej przejdź do profilu usługi Traffic Manager nazwę DNS.
    - Z testową maszynę Wirtualną (*myVMEastUS*) znajdującą się w **Europa Zachodnia** regionu, w przeglądarce internetowej przejdź do profilu usługi Traffic Manager nazwę DNS.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager
Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
1. Kliknij pozycję **Przegląd**.
2. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

   ![Nazwa DNS usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
1. W przeglądarce internetowej na maszynie wirtualnej *myVMEastUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Ponieważ maszyna wirtualna znajduje się w **wschodnie stany USA**, są kierowane do najbliższych witryny sieci Web hostowanych na serwerze usług IIS w najbliższej *myIISVMEastUS* znajdującą się w **wschodnie stany USA**.

   ![Testowanie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Następnie połącz się z maszyną wirtualną *myVMWestEurope* na terenie **Europa Zachodnia** korzystając z kroków 1 – 5 i przejdź do nazwy domeny profilu usługi Traffic Manager z tej maszyny Wirtualnej. Ponieważ maszyna wirtualna znajduje się w **Europa Zachodnia**, możesz teraz są kierowane do witryny sieci Web hostowana na najbliższych na serwerze usług IIS *myIISVMWEurope* znajdującą się w **Europa Zachodnia**.

   ![Testowanie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager
Kiedy grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**) przestaną być potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dystrybuowanie ruchu do zestawu punktów końcowych](traffic-manager-configure-weighted-routing-method.md)

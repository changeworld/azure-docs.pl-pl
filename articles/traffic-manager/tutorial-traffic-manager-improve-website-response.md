---
title: Samouczek — ulepszanie reakcji witryny sieci Web za pomocą usługi Azure Traffic Manager
description: W tym artykule samouczka opisano sposób tworzenia profilu usługi Traffic Manager w celu utworzenia wysoce responsywnej witryny sieci Web.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: rohink
ms.openlocfilehash: f7778b60a5e84f5d8900b8e37bfa655a7915d403
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77136382"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Samouczek: Poprawa reakcji na witrynę za pomocą Usługi Traffic Manager

W tym samouczku opisano sposób używania usługi Traffic Manager do tworzenia wysoce responsywnej witryny sieci Web przez kierowanie ruchu użytkowników do witryny sieci Web z najniższym opóźnieniem. Zazwyczaj centrum danych o najniższym opóźnieniu jest tym, które znajduje się najbliżej odległości geograficznej.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych z podstawową witryną internetową w usługach IIS
> * Tworzenie dwóch testowych maszyn wirtualnych, aby zobaczyć usługę Traffic Manager w działaniu
> * Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS
> * Tworzenie profilu usługi Traffic Manager w celu zwiększenia wydajności witryny
> * Dodawanie punktów końcowych maszyny wirtualnej do profilu usługi Traffic Manager
> * Wyświetlanie informacji o działaniu usługi Traffic Manager

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby w tym samouczku zobaczyć usługi Traffic Manager w działaniu, trzeba wdrożyć:

- Dwa wystąpienia podstawowych witryn sieci Web działających w różnych regionach platformy Azure — **wschodnie stany USA** i **Europa Zachodnia**.
- Dwie testowe maszyny wirtualne do testowania menedżera ruchu — jedna maszyna wirtualna we **wschodnich stanach USA** i druga maszyna wirtualna w **Europie Zachodniej.** Maszyny wirtualne testowe są używane do zilustrowania, jak Usługa Traffic Manager kieruje ruch użytkowników do witryny sieci Web uruchomionej w tym samym regionie, ponieważ zapewnia najniższe opóźnienie.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Tworzenie witryn internetowych

W tej sekcji opisano tworzenie dwóch wystąpień witryny internetowej, które zapewniają dwa punkty końcowe usługi dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Tworzenie dwóch witryn internetowej obejmuje następujące kroki:

1. Utwórz dwie maszyny wirtualne w celu uruchomienia podstawowej witryny internetowej — jedną w regionie **Wschodnie stany USA**, a drugą w regionie **Europa Zachodnia**.
2. Zainstaluj serwer IIS na każdej maszynie wirtualnej i zaktualizuj domyślną stronę witryny internetowej, aby informowała o nazwie maszyny wirtualnej, z którą łączy się użytkownik podczas odwiedzania witryny internetowej.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn internetowych

W tej sekcji utworzysz dwie maszyny wirtualne *myIISVMEastUS* i *myIISVMWestEurope* we wschodnich regionach platformy Azure **dla wschodnich stanów USA** i Europy **Zachodniej.**

1. W lewym górnym rogu witryny Azure portal wybierz pozycję **Utwórz źródło zasobów** > **Oblicza** > **centrum danych systemu Windows Server 2019**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Grupa** > **zasobów**subskrypcji: wybierz pozycję **Utwórz nowy,** a następnie wpisz **myResourceGroupTM1**.
   - **Szczegóły wystąpienia** > **Nazwa maszyny wirtualnej:** Wpisz *myIISVMEastUS*.
   - **Region szczegółów** > **Region**wystąpienia: wybierz **wschodnie stany USA**.
   - **Nazwa** > **konta**administratora: Wprowadź wybraną nazwę użytkownika.
   - **Hasło konta** > **administratora**: Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Reguły portów** > **przychodzących Porty przychodzące**Publiczne porty : Wybierz opcję **Zezwalaj na wybrane porty**.
   - **Reguły portów** > **przychodzących Wybierz porty przychodzące:** Wybierz **protokół RDP** i **HTTP** w polu rozwijania.

3. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej: Dyski**, a następnie **dalej: Sieć**, a następnie **dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przegląd + utwórz**.
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.  
6. Wykonaj kroki, aby utworzyć drugą maszynę wirtualną o nazwie *myIISVMWestEurope,* z nazwą **grupy zasobów** *myResourceGroupTM2*, **lokalizacją** *Europy Zachodniej*i wszystkimi innymi ustawieniami, taką samą jak *myIISVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

   ![Tworzenie maszyny wirtualnej](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji należy zainstalować serwer IIS na dwóch maszynach wirtualnych *myIISVMEastUS* i *myIISVMWestEurope*, a następnie zaktualizować domyślną stronę witryny sieci Web. Niestandardowa strona witryny internetowej przedstawia nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do**Menedżera serwera** **narzędzi administracyjnych**>systemu Windows .
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
9. Powtórz kroki 1-8, tworząc połączenie RDP z maszyną wirtualną *myIISVMWestEurope* w grupie zasobów *myResourceGroupTM2,* aby zainstalować usługi IIS i dostosować jego domyślną stronę sieci Web.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji można skonfigurować nazwy DNS dla serwerów IIS - *myIISVMEastUS* i *myIISVMWestEurope*.

1. Kliknij opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1-3 dla maszyny Wirtualnej o nazwie *myIISVMWestEurope,* która znajduje się w grupie zasobów *myResourceGroupTM2.*

### <a name="create-test-vms"></a>Tworzenie testowych maszyn wirtualnych

W tej sekcji utworzysz maszynę wirtualną *(myVMEastUS* i *myVMWestEurope)* w każdym regionie platformy Azure **(Wschodnie stany USA** i **Europa Zachodnia).** Te maszyny wirtualne posłużą do testowania, jak usługa Traffic Manager kieruje ruch do najbliższego serwera usług IIS, kiedy użytkownik nawiguje do witryny internetowej.

1. W lewym górnym rogu witryny Azure portal wybierz pozycję **Utwórz źródło zasobów** > **Oblicza** > **centrum danych systemu Windows Server 2019**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupTM1**.
   - **Szczegóły** > wystąpienia**Nazwa maszyny wirtualnej:** Wpisz *myVMEastUS*.
   - **Region szczegółów** > **Region**wystąpienia: wybierz **wschodnie stany USA**.
   - **Nazwa** > **konta**administratora: Wprowadź wybraną nazwę użytkownika.
   - **Hasło konta** > **administratora**: Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Reguły portów** > **przychodzących Porty przychodzące**Publiczne porty : Wybierz opcję **Zezwalaj na wybrane porty**.
   - **Reguły portów** > **przychodzących Wybierz porty przychodzące:** Wybierz **rdp** w polu ściągania.

3. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej: Dyski**, a następnie **dalej: Sieć**, a następnie **dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przegląd + utwórz**.
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.  
6. Wykonaj kroki, aby utworzyć drugą maszynę wirtualną o nazwie *myVMWestEurope,* z nazwą **grupy zasobów** *myResourceGroupTM2*, **lokalizacją** *Europy Zachodniej*i wszystkimi innymi ustawieniami, takie same jak *myVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager, który kieruje ruch użytkowników, wysyłając go do punktu końcowego o najniższym opóźnieniu.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz** > **profil** > menedżera ruchu**sieciowego** > **Utwórz**.
2. W obszarze **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz metodę routingu **wydajności.**                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz grupę zasobów *myResourceGroupTM1*. |
    | Lokalizacja                | Wybierz pozycję **Wschodnie stany USA**. To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.                              |
    |

    ![Tworzenie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne z uruchomionymi serwerami IIS - *myIISVMEastUS* & *myIISVMWestEurope,* aby kierować ruch użytkownika do najbliższego punktu końcowego do użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz następujące informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **przycisk OK:**

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Punkt końcowy platformy Azure                                   |
    | Nazwa           | myEastUSEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *myIISVMEastUS-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać inny punkt końcowy o nazwie *myWestEuropeEndpoint* dla publicznego adresu IP *myIISVMWestEurope-ip,* który jest skojarzony z serwerem IIS VM o nazwie *myIISVMWestEurope*.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

    ![Dodawanie punktu końcowego usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

W tej sekcji można przetestować, jak Usługa Traffic Manager kieruje ruch użytkowników do najbliższych maszyn wirtualnych z systemem witryny sieci Web, aby zapewnić minimalne opóźnienia. Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:

1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl informacje o działaniu usługi Traffic Manager w następujący sposób:
    - Z testowej maszyny Wirtualnej *(myVMEastUS),* która znajduje się w regionie **Wschodnie stany USA,** w przeglądarce internetowej przejdź do nazwy DNS profilu usługi Traffic Manager.
    - Z testowej maszyny Wirtualnej *(myVMWestEurope),* która znajduje się w regionie **Europy Zachodniej,** w przeglądarce internetowej przejdź do nazwy DNS profilu usługi Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager

Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
2. Kliknij pozycję **Przegląd**.
3. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

   ![Nazwa DNS usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager

W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
1. W przeglądarce internetowej na maszynie wirtualnej *myVMEastUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Ponieważ maszyna wirtualna znajduje się we **wschodnich stanach USA,** użytkownik jest kierowany do najbliższej witryny sieci Web hostowanego na najbliższym serwerze IIS *myIISVMEastUS,* który znajduje się we **wschodnich stanach USA.**

   ![Testowanie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Następnie połącz się z wirtualną *maszyną wirtualną myVMWestEurope* znajdującą się w **Europie Zachodniej,** wykonując kroki 1-5 i przejdź do nazwy domeny profilu usługi Traffic Manager z tej maszyny Wirtualnej. Ponieważ maszyna wirtualna znajduje się w **Europie Zachodniej,** jesteś teraz kierowane do strony internetowej hostowane na najbliższym serwerze IIS *myIISVMWestEurope,* który znajduje się w **Europie Zachodniej**.

   ![Testowanie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager

Kiedy grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**) przestaną być potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dystrybuowanie ruchu do zestawu punktów końcowych](traffic-manager-configure-weighted-routing-method.md)

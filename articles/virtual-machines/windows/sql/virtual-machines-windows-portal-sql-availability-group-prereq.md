---
title: 'Samouczek: Wymagania wstępne dotyczące grupy dostępności'
description: W tym samouczku pokazano, jak skonfigurować wymagania wstępne dotyczące tworzenia grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: c80a4c07e0649612b4c024cac79833c4b730f55e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060168"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Wymagania wstępne dotyczące tworzenia grup dostępności always on na platformie SQL Server na maszynach wirtualnych platformy Azure

W tym samouczku pokazano, jak ukończyć wymagania wstępne dotyczące tworzenia [grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure .](virtual-machines-windows-portal-sql-availability-group-tutorial.md) Po zakończeniu wymagań wstępnych masz kontroler domeny, dwie maszyny wirtualne programu SQL Server i serwer monitora w jednej grupie zasobów.

**Szacowanie czasu:** może upłynąć kilka godzin, aby zakończyć wymagania wstępne. Większość tego czasu spędza na tworzeniu maszyn wirtualnych.

Na poniższym diagramie przedstawiono, co można utworzyć w samouczku.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Przeglądanie dokumentacji grupy dostępności

W tym samouczku przyjęto założenie, że masz podstawową wiedzę na temat grup dostępności programu SQL Server Always On. Jeśli nie znasz tej technologii, zobacz [Omówienie grup dostępności always on (SQL Server).](https://msdn.microsoft.com/library/ff877884.aspx)


## <a name="create-an-azure-account"></a>Tworzenie konta platformy Azure
Musisz mieć konto platformy Azure. Możesz [otworzyć bezpłatne konto platformy Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) lub [aktywować korzyści subskrybenta programu Visual Studio.](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Kliknij, **+** aby utworzyć nowy obiekt w portalu.

   ![Nowy obiekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Wpisz **grupę zasobów** w oknie wyszukiwania **w portalu Marketplace.**

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kliknij **pozycję Grupa zasobów**.
5. Kliknij przycisk **Utwórz**.
6. W **obszarze Nazwa grupy zasobów**wpisz nazwę grupy zasobów. Na przykład wpisz **sql-ha-rg**.
7. Jeśli masz wiele subskrypcji platformy Azure, sprawdź, czy subskrypcja jest subskrypcją platformy Azure, w której chcesz utworzyć grupę dostępności.
8. Wybierz lokalizację. Lokalizacja jest region platformy Azure, gdzie chcesz utworzyć grupę dostępności. W tym artykule skompilowaj dotyczy wszystkich zasobów w jednej lokalizacji platformy Azure.
9. Sprawdź, czy **pole wyboru Przyp.** To ustawienie opcjonalne umieszcza skrót dla grupy zasobów na pulpicie nawigacyjnym portalu Azure.

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kliknij pozycję **Utwórz**, aby utworzyć grupę zasobów.

Platforma Azure tworzy grupę zasobów i przypina skrót do grupy zasobów w portalu.

## <a name="create-the-network-and-subnets"></a>Tworzenie sieci i podsieci
Następnym krokiem jest utworzenie sieci i podsieci w grupie zasobów platformy Azure.

Rozwiązanie używa jednej sieci wirtualnej z dwoma podsieciami. Omówienie [sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md) zawiera więcej informacji na temat sieci na platformie Azure.

Aby utworzyć sieć wirtualną:

1. W witrynie Azure portal w grupie zasobów kliknij przycisk **+ Dodaj**. 

   ![Nowy przedmiot](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Wyszukaj **sieć wirtualną**.

     ![Wyszukiwanie w sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kliknij **pozycję Sieć wirtualna**.
4. W **sieci wirtualnej**kliknij model wdrażania **Menedżera zasobów,** a następnie kliknij przycisk **Utwórz**.

    W poniższej tabeli przedstawiono ustawienia sieci wirtualnej:

   | **Pole** | Wartość |
   | --- | --- |
   | **Nazwa** |autoHAVNET |
   | **Przestrzeń adresowa** |10.33.0.0/24 |
   | **Nazwa podsieci** |Administrator |
   | **Zakres adresów podsieci** |10.33.0.0/29 |
   | **Subskrypcja** |Określ subskrypcję, której chcesz użyć. **Subskrypcja** jest pusta, jeśli masz tylko jedną subskrypcję. |
   | **Grupa zasobów** |Wybierz **pozycję Użyj istniejącego** i wybierz nazwę grupy zasobów. |
   | **Lokalizacja** |Określ lokalizację platformy Azure. |

   Zakres adresów i zakres adresów podsieci mogą się różnić od zakresu tabeli. W zależności od subskrypcji portal sugeruje dostępną przestrzeń adresową i odpowiedni zakres adresów podsieci. Jeśli nie jest dostępna wystarczająca ilość miejsca adresowego, użyj innej subskrypcji.

   W przykładzie użyto nazwy podsieci **Admin**. Ta podsieć jest dla kontrolerów domeny.

5. Kliknij przycisk **Utwórz**.

   ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Platforma Azure zwraca użytkownika do pulpitu nawigacyjnego portalu i powiadamia o utworzeniu nowej sieci.

### <a name="create-a-second-subnet"></a>Tworzenie drugiej podsieci
Nowa sieć wirtualna ma jedną podsieć o nazwie **Admin**. Kontrolery domeny używają tej podsieci. Maszyny wirtualne programu SQL Server używają drugiej podsieci o nazwie **SQL**. Aby skonfigurować tę podsieć:

1. Na pulpicie nawigacyjnym kliknij utworzoną grupę zasobów **SQL-HA-RG**. Znajdź sieć w grupie zasobów w obszarze **Zasoby**.

    Jeśli **SQL-HA-RG** nie jest widoczny, znajdź go, klikając **grupy zasobów** i filtrując według nazwy grupy zasobów.
2. Kliknij **autoHAVNET** na liście zasobów. 
3. W sieci wirtualnej **autoHAVNET** w obszarze **Ustawienia** wybierz pozycję **Podsieci**.

    Zanotuj już utworzoną podsieć podsieci.

   ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Utwórz drugą podsieć. Kliknij **+ Podsieć**.
6. W **obszarze Dodaj podsieć**skonfiguruj podsieć, wpisując **sqlsubnet** w obszarze **Nazwa**. Platforma Azure automatycznie określa prawidłowy **zakres adresów**. Sprawdź, czy ten zakres adresów ma co najmniej 10 adresów. W środowisku produkcyjnym może być wymagana więcej adresów.
7. Kliknij przycisk **OK**.

    ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

W poniższej tabeli podsumowano ustawienia konfiguracji sieci:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |**autoHAVNET** |
| **Przestrzeń adresowa** |Ta wartość zależy od dostępnych przestrzeni adresowych w ramach subskrypcji. Typowa wartość to 10.0.0.0/16. |
| **Nazwa podsieci** |**administracja** |
| **Zakres adresów podsieci** |Ta wartość zależy od dostępnych zakresów adresów w ramach subskrypcji. Typowa wartość to 10.0.0.0/24. |
| **Nazwa podsieci** |**sqlsubnet ( sqlsubnet )** |
| **Zakres adresów podsieci** |Ta wartość zależy od dostępnych zakresów adresów w ramach subskrypcji. Typowa wartość to 10.0.1.0/24. |
| **Subskrypcja** |Określ subskrypcję, której chcesz użyć. |
| **Grupa zasobów** |**SQL-HA-RG** |
| **Lokalizacja** |Określ tę samą lokalizację, którą wybrano dla grupy zasobów. |

## <a name="create-availability-sets"></a>Tworzenie zestawów dostępności

Przed utworzeniem maszyn wirtualnych należy utworzyć zestawy dostępności. Zestawy dostępności skracają przestoje w przypadku planowanych lub nieplanowanych zdarzeń konserwacji. Zestaw dostępności platformy Azure to logiczna grupa zasobów umieszczanych przez platformę Azure w domenach błędów fizycznych i domenach aktualizacji. Domena błędów zapewnia, że członkowie zestawu dostępności mają oddzielne zasoby zasilania i sieci. Domena aktualizacji zapewnia, że członkowie zestawu dostępności nie są wyprowadzone do konserwacji w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Manage the availability of virtual machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Zarządzanie dostępnością maszyn wirtualnych).

Potrzebne są dwa zestawy dostępności. Jednym z nich jest kontrolery domeny. Drugi dotyczy maszyn wirtualnych programu SQL Server.

Aby utworzyć zestaw dostępności, przejdź do grupy zasobów i kliknij przycisk **Dodaj**. Filtruj wyniki, wpisując **zestaw dostępności**. Kliknij pozycję **Zestaw dostępności** w wynikach, a następnie kliknij pozycję **Utwórz**.

Skonfiguruj dwa zestawy dostępności zgodnie z parametrami w poniższej tabeli:

| **Pole** | Zestaw dostępności kontrolera domeny | Zestaw dostępności programu SQL Server |
| --- | --- | --- |
| **Nazwa** |zestaw dostępności |sqlavailabilityset |
| **Grupa zasobów** |SQL-HA-RG |SQL-HA-RG |
| **Domeny błędów** |3 |3 |
| **Domeny aktualizacji** |5 |3 |

Po utworzeniu zestawów dostępności wróć do grupy zasobów w witrynie Azure portal.

## <a name="create-domain-controllers"></a>Tworzenie kontrolerów domeny
Po utworzeniu zestawów sieci, podsieci i dostępności można przystąpić do tworzenia maszyn wirtualnych dla kontrolerów domeny.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Tworzenie maszyn wirtualnych dla kontrolerów domeny
Aby utworzyć i skonfigurować kontrolery domeny, wróć do grupy zasobów **SQL-HA-RG.**

1. Kliknij przycisk **Dodaj**. 
2. Wpisz **centrum danych systemu Windows Server 2016**.
3. Kliknij **pozycję Centrum danych systemu Windows Server 2016**. W **centrum danych systemu Windows Server 2016**sprawdź, czy model wdrażania to Menedżer **zasobów,** a następnie kliknij przycisk **Utwórz**. 

Powtórz poprzednie kroki, aby utworzyć dwie maszyny wirtualne. Nazwij dwie maszyny wirtualne:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > Maszyna wirtualna **ad-secondary-dc** jest opcjonalna, aby zapewnić wysoką dostępność usług domenowych Active Directory.
  >
  >

W poniższej tabeli przedstawiono ustawienia dla tych dwóch maszyn:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |Pierwszy kontroler domeny: *ad-primary-dc*.</br>Drugi kontroler domeny *ad-secondary-dc*. |
| **Typ dysku maszyny Wirtualnej** |SSD |
| **Nazwa użytkownika** |DomainAdmin (Domena) |
| **Hasło** |Contoso!0000 |
| **Subskrypcja** |*Twoja subskrypcja* |
| **Grupa zasobów** |SQL-HA-RG |
| **Lokalizacja** |*Twoja lokalizacja* |
| **Rozmiar** |DS1_V2 |
| **Magazyn** | **Używanie dysków zarządzanych** - **Tak** |
| **Sieć wirtualna** |autoHAVNET |
| **Podsieci** |administrator |
| **Publiczny adres IP** |*Taka sama nazwa co maszyna wirtualna* |
| **Grupa zabezpieczeń sieci** |*Taka sama nazwa co maszyna wirtualna* |
| **Zestaw dostępności** |zestaw dostępności </br>**Domeny błędów**:2 </br>**Aktualizuj domeny**:2|
| **Diagnostyka** |Enabled (Włączony) |
| **Konto magazynu diagnostyki** |*Tworzenie automatycznie* |

   >[!IMPORTANT]
   >Maszynę wirtualną można umieścić tylko w zestawie dostępności podczas jej tworzenia. Nie można zmienić zestawu dostępności po utworzeniu maszyny Wirtualnej. Zobacz [Zarządzanie dostępnością maszyn wirtualnych](../manage-availability.md).

Platforma Azure tworzy maszyny wirtualne.

Po utworzeniu maszyn wirtualnych skonfiguruj kontroler domeny.

### <a name="configure-the-domain-controller"></a>Konfigurowanie kontrolera domeny
W poniższych krokach skonfiguruj komputer **ad-primary-dc** jako kontroler domeny dla corp.contoso.com.

1. W portalu otwórz grupę zasobów **SQL-HA-RG** i wybierz komputer **ad-primary-dc.** W **przypadku reklamy głównej dc**kliknij pozycję **Połącz,** aby otworzyć plik RDP w celu uzyskania dostępu do pulpitu zdalnego.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Zaloguj się przy za pomocą skonfigurowanego konta administratora (**\DomainAdmin**) i hasła (**Contoso!0000**).
3. Domyślnie powinien być wyświetlany pulpit nawigacyjny **Menedżera serwera.**
4. Kliknij łącze **Dodaj role i funkcje** na pulpicie nawigacyjnym.

    ![Menedżer serwera — dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Wybierz **przycisk Dalej,** aż dojdziesz do sekcji **Role serwera.**
6. Wybierz role **Usług domenowych Active Directory** i **serwera DNS.** Po wyświetleniu monitu dodaj dodatkowe funkcje, które są wymagane przez te role.

   > [!NOTE]
   > System Windows ostrzega, że nie ma statycznego adresu IP. Jeśli testujesz konfigurację, kliknij przycisk **Kontynuuj**. W scenariuszach produkcyjnych ustaw adres IP na statyczny w witrynie Azure portal lub [użyj programu PowerShell, aby ustawić statyczny adres IP komputera kontrolera domeny](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Okno dialogowe Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Kliknij **przycisk Dalej,** aż dotrzesz do sekcji **Potwierdzenie.** Zaznacz pole wyboru **Uruchom ponownie serwer docelowy automatycznie, jeśli jest to wymagane.**
8. Kliknij **pozycję Zainstaluj**.
9. Po zakończeniu instalacji funkcji wróć do pulpitu nawigacyjnego **Menedżera serwera.**
10. Wybierz nową opcję **usług AD DS** w lewym okienku.
11. Kliknij link **Więcej** na żółtym pasku ostrzeżenia.

    ![Okno dialogowe USŁUG AD DS na maszynie wirtualnej serwera DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. W kolumnie **Akcja** okna dialogowego **Wszystkie szczegóły zadania serwera** kliknij pozycję **Podwyższyj wartość tego serwera na kontroler domeny**.
13. W **Kreatorze konfiguracji usług domenowych Active Directory**użyj następujących wartości:

    | **Strona** | Ustawienie |
    | --- | --- |
    | **Konfiguracja wdrażania** |**Dodawanie nowego lasu**<br/> **Nazwa domeny głównej** = corp.contoso.com |
    | **Opcje kontrolera domeny** |**Hasło DSRM** = Contoso!0000<br/>**Potwierdź hasło** = Contoso!0000 |
14. Kliknij **przycisk Dalej,** aby przejść przez inne strony kreatora. Na stronie **Sprawdzanie wymagań wstępnych** sprawdź, czy zostanie wyświetlony następujący komunikat: Wszystkie kontrole wymagań wstępnych przeszły **pomyślnie**. Możesz przejrzeć wszystkie odpowiednie komunikaty ostrzegawcze, ale można kontynuować instalację.
15. Kliknij **pozycję Zainstaluj**. Maszyna wirtualna **ad-primary-dc** automatycznie uruchamia się ponownie.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Zanotuj adres IP podstawowego kontrolera domeny

Użyj podstawowego kontrolera domeny dla systemu DNS. Zanotuj podstawowy adres IP kontrolera domeny.

Jednym ze sposobów uzyskania podstawowego adresu IP kontrolera domeny jest za pośrednictwem witryny Azure portal.

1. W witrynie Azure portal otwórz grupę zasobów.

2. Kliknij podstawowy kontroler domeny.

3. Na głównym kontrolerze domeny kliknij pozycję **Interfejsy sieciowe**.

![Interfejsy sieciowe](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Zanotuj prywatny adres IP dla tego serwera.

### <a name="configure-the-virtual-network-dns"></a>Konfigurowanie sieci wirtualnej DNS
Po utworzeniu pierwszego kontrolera domeny i włączeniu systemu DNS na pierwszym serwerze skonfiguruj sieć wirtualną tak, aby używała tego serwera dla systemu DNS.

1. W witrynie Azure portal kliknij sieć wirtualną.

2. W obszarze **Ustawienia**kliknij pozycję **Serwer DNS**.

3. Kliknij pozycję **Niestandardowy**i wpisz prywatny adres IP podstawowego kontrolera domeny.

4. Kliknij przycisk **Zapisz**.

### <a name="configure-the-second-domain-controller"></a>Konfigurowanie drugiego kontrolera domeny
Po ponownym uruchomieniu głównego kontrolera domeny można skonfigurować drugi kontroler domeny. Ten opcjonalny krok jest dla wysokiej dostępności. Wykonaj następujące kroki, aby skonfigurować drugi kontroler domeny:

1. W portalu otwórz grupę zasobów **SQL-HA-RG** i wybierz komputer **ad-secondary-dc.** W **przypadku ad-secondary-dc**kliknij pozycję **Połącz,** aby otworzyć plik RDP w celu uzyskania dostępu do pulpitu zdalnego.
2. Zaloguj się do maszyny Wirtualnej przy użyciu skonfigurowanego konta**administratora (BUILTIN\DomainAdmin)** i hasła (**Contoso!0000**).
3. Zmień preferowany adres serwera DNS na adres kontrolera domeny.
4. W **Centrum sieci i udostępniania**kliknij interfejs sieciowy.
   ![Interfejs sieciowy](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kliknij pozycję **Właściwości**.
6. Wybierz **protokół internetowy w wersji 4 (TCP/IPv4)** i kliknij przycisk **Właściwości**.
7. Wybierz **pozycję Użyj następujących adresów serwera DNS** i określ adres podstawowego kontrolera domeny w **preferowanym serwerze DNS**.
8. Kliknij **przycisk OK**, a następnie przycisk **Zamknij,** aby zatwierdzić zmiany. Teraz możesz dołączyć do maszyny Wirtualnej, aby **corp.contoso.com**.

   >[!IMPORTANT]
   >Jeśli utracisz połączenie z pulpitem zdalnym po zmianie ustawienia DNS, przejdź do witryny Azure portal i uruchom ponownie maszynę wirtualną.

9. Z pulpitu zdalnego do pomocniczego kontrolera domeny otwórz **pulpit nawigacyjny Menedżera serwera**.
10. Kliknij łącze **Dodaj role i funkcje** na pulpicie nawigacyjnym.

    ![Menedżer serwera — dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Wybierz **przycisk Dalej,** aż dojdziesz do sekcji **Role serwera.**
12. Wybierz role **Usług domenowych Active Directory** i **serwera DNS.** Po wyświetleniu monitu dodaj dodatkowe funkcje, które są wymagane przez te role.
13. Po zakończeniu instalacji funkcji wróć do pulpitu nawigacyjnego **Menedżera serwera.**
14. Wybierz nową opcję **usług AD DS** w lewym okienku.
15. Kliknij link **Więcej** na żółtym pasku ostrzeżenia.
16. W kolumnie **Akcja** okna dialogowego **Wszystkie szczegóły zadania serwera** kliknij pozycję **Podwyższyj wartość tego serwera na kontroler domeny**.
17. W obszarze **Konfiguracja wdrażania**wybierz pozycję **Dodaj kontroler domeny do istniejącej domeny**.
    ![Konfiguracja wdrażania](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kliknij **pozycję Wybierz**.
19. Połącz się przy użyciu konta administratora (**CORP. CONTOSO.COM\domainadmin**) i hasło (**Contoso!0000**).
20. W **obszarze Wybierz domenę z lasu**kliknij domenę, a następnie kliknij przycisk **OK**.
21. W **obszarze Opcje kontrolera domeny**użyj wartości domyślnych i ustaw hasło DSRM.

    >[!NOTE]
    >Strona **Opcje DNS** może ostrzegać, że nie można utworzyć delegowania dla tego serwera DNS. To ostrzeżenie można zignorować w środowiskach nieprodukcyjnych.
22. Kliknij **przycisk Dalej,** aż okno dialogowe osiągnie **wymagania wstępne.** Następnie kliknij pozycję **Zainstaluj**.

Po zakończeniu konfiguracji serwera uruchom ponownie serwer.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Dodawanie prywatnego adresu IP do drugiego kontrolera domeny do serwera DNS sieci VPN

W witrynie Azure portal w obszarze sieć wirtualna zmień serwer DNS na adres IP pomocniczego kontrolera domeny. To ustawienie umożliwia nadmiarowość usługi DNS.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>Konfigurowanie kont domeny

W następnych krokach skonfiguruj konta usługi Active Directory. W poniższej tabeli przedstawiono konta:

| |Konto instalacji<br/> |serwer sqlserver-0 <br/>Konto usługi SQL Server i SQL Agent Service |serwer sqlserver-1<br/>Konto usługi SQL Server i SQL Agent Service
| --- | --- | --- | ---
|**Imię** |Zainstaluj |SQLSvc1 (w języku sql) | SQLSvc2 (w języku)
|**Użytkownik SamAccountName** |Zainstaluj |SQLSvc1 (w języku sql) | SQLSvc2 (w języku)

Aby utworzyć każde konto, należy wykonać następujące czynności.

1. Zaloguj się do komputera **ad-primary-dc.**
2. W **Menedżerze serwera**wybierz pozycję **Narzędzia**, a następnie kliknij pozycję **Centrum administracyjne usługi Active Directory**.   
3. Wybierz **corp (lokalny)** z lewego okienka.
4. W prawym okienku **Zadania** wybierz pozycję **Nowy**, a następnie kliknij pozycję **Użytkownik**.
   ![Centrum administracyjne usługi Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Ustaw złożone hasło dla każdego konta.<br/> W środowiskach nieprodukcyjnych ustaw konto użytkownika tak, aby nigdy nie wygasło.

5. Kliknij **przycisk OK,** aby utworzyć użytkownika.
6. Powtórz poprzednie kroki dla każdego z trzech kont.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Udzielanie wymaganych uprawnień do konta instalacyjnego
1. W **Centrum administracyjnym usługi Active Directory**wybierz **corp (lokalny)** w lewym okienku. Następnie w okienku **Zadania** po prawej stronie kliknij pozycję **Właściwości**.

    ![Właściwości użytkownika CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Wybierz **pozycję Rozszerzenia**, a następnie kliknij przycisk **Zaawansowane** na karcie **Zabezpieczenia.**
3. W oknie dialogowym **Zaawansowane ustawienia zabezpieczeń dla firm** kliknij pozycję **Dodaj**.
4. Kliknij **pozycję Wybierz głównego zobowiązanego**, wyszukaj **corp\Install**, a następnie kliknij przycisk **OK**.
5. Zaznacz pole wyboru **Odczyt wszystkich właściwości.**

6. Zaznacz pole wyboru **Utwórz obiekty komputera.**

     ![Uprawnienia użytkownika corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kliknij przycisk **OK**, a następnie kliknij ponownie przycisk **OK**. Zamknij okno właściwości **corp.**

Po zakończeniu konfigurowania usługi Active Directory i obiektów użytkownika należy utworzyć dwie maszyny wirtualne programu SQL Server i maszynę wirtualną serwera monitora. Następnie dołącz wszystkie trzy do domeny.

## <a name="create-sql-server-vms"></a>Tworzenie maszyn wirtualnych programu SQL Server

Utwórz trzy dodatkowe maszyny wirtualne. Rozwiązanie wymaga dwóch maszyn wirtualnych z wystąpieniami programu SQL Server. Trzecia maszyna wirtualna będzie działać jako monitor. System Windows Server 2016 może używać [monitora w chmurze,](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)jednak ze spójności z poprzednimi systemami operacyjnymi ten dokument używa maszyny wirtualnej dla monitora.  

Przed przystąpieniem należy wziąć pod uwagę następujące decyzje projektowe.

* **Magazyn — dyski zarządzane platformy Azure**

   W przypadku magazynu maszyn wirtualnych użyj dysków zarządzanych platformy Azure. Firma Microsoft zaleca dyski zarządzane dla maszyn wirtualnych programu SQL Server. Funkcja Dyski zarządzane obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z funkcją Dyski zarządzane znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. Aby uzyskać więcej informacji, zobacz [Omówienie funkcji Dyski zarządzane platformy Azure](../managed-disks-overview.md). Aby uzyskać szczegółowe informacje na temat dysków zarządzanych w zestawie dostępności, zobacz [Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Sieć — prywatne adresy IP w produkcji**

   W przypadku maszyn wirtualnych w tym samouczku użyto publicznych adresów IP. Publiczny adres IP umożliwia zdalne połączenie bezpośrednio z maszyną wirtualną przez Internet - ułatwia to wykonywanie czynności konfiguracyjnych. W środowiskach produkcyjnych firma Microsoft zaleca tylko prywatne adresy IP w celu zmniejszenia śladu podatności na luki w zasobie maszyny Wirtualnej wystąpienia programu SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Tworzenie i konfigurowanie maszyn wirtualnych programu SQL Server
Następnie utwórz trzy maszyny wirtualne — dwie maszyny wirtualne programu SQL Server i maszynę wirtualną dla dodatkowego węzła klastra. Aby utworzyć każdą z maszyn wirtualnych, wróć do grupy zasobów **SQL-HA-RG,** kliknij przycisk **Dodaj**, wyszukaj odpowiedni element galerii, kliknij pozycję **Maszyna wirtualna**, a następnie kliknij pozycję **Z galerii**. Użyj informacji w poniższej tabeli, aby ułatwić tworzenie maszyn wirtualnych:


| Strona | Maszyna wirtualna 1 | Maszyna wirtualna 2 | Maszyna wirtualna 3 |
| --- | --- | --- | --- |
| Wybierz odpowiedni element galerii |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise w systemie Windows Server 2016** |**SQL Server 2016 SP1 Enterprise w systemie Windows Server 2016** |
| Podstawowe informacje o konfiguracji maszyny **wirtualnej** |**Nazwa** = cluster-fsw<br/>**Nazwa użytkownika** = DomainAdmin<br/>**Hasło** = Contoso!0000<br/>**Subskrypcja** = Twoja subskrypcja<br/>**Grupa zasobów** = SQL-HA-RG<br/>**Lokalizacja** = Twoja lokalizacja na platformie Azure |**Nazwa** = sqlserver-0<br/>**Nazwa użytkownika** = DomainAdmin<br/>**Hasło** = Contoso!0000<br/>**Subskrypcja** = Twoja subskrypcja<br/>**Grupa zasobów** = SQL-HA-RG<br/>**Lokalizacja** = Twoja lokalizacja na platformie Azure |**Nazwa** = sqlserver-1<br/>**Nazwa użytkownika** = DomainAdmin<br/>**Hasło** = Contoso!0000<br/>**Subskrypcja** = Twoja subskrypcja<br/>**Grupa zasobów** = SQL-HA-RG<br/>**Lokalizacja** = Twoja lokalizacja na platformie Azure |
| **Rozmiar** konfiguracji maszyny wirtualnej |**ROZMIAR** =\_DS1 V2 (1 vCPU, 3,5 GB) |**SIZE** =\_DS2 V2 (2 procesory wirtualne, 7 GB)</br>Rozmiar musi obsługiwać pamięć masową SSD (obsługa dysków premium. )) |**SIZE** =\_DS2 V2 (2 procesory wirtualne, 7 GB) |
| **Ustawienia** konfiguracji maszyny wirtualnej |**Magazyn:** Użyj dysków zarządzanych.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Podsieć** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** generowany automatycznie.<br/>**Sieć grupa zabezpieczeń** = Brak<br/>**Diagnostyka monitorowania** = Włączono<br/>**Konto magazynu diagnostyki** = Użyj automatycznie wygenerowanego konta magazynu<br/>**Zestaw dostępności** = sqlDostępnośćSet<br/> |**Magazyn:** Użyj dysków zarządzanych.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Podsieć** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** generowany automatycznie.<br/>**Sieć grupa zabezpieczeń** = Brak<br/>**Diagnostyka monitorowania** = Włączono<br/>**Konto magazynu diagnostyki** = Użyj automatycznie wygenerowanego konta magazynu<br/>**Zestaw dostępności** = sqlDostępnośćSet<br/> |**Magazyn:** Użyj dysków zarządzanych.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Podsieć** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** generowany automatycznie.<br/>**Sieć grupa zabezpieczeń** = Brak<br/>**Diagnostyka monitorowania** = Włączono<br/>**Konto magazynu diagnostyki** = Użyj automatycznie wygenerowanego konta magazynu<br/>**Zestaw dostępności** = sqlDostępnośćSet<br/> |
| Ustawienia programu **SQL Server** konfiguracji maszyny wirtualnej |Nie dotyczy |**Łączność SQL** = prywatna (w sieci wirtualnej)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = Wyłącz<br/>**Konfiguracja magazynu** = Ogólne<br/>**Automatyczne łatanie** = niedziela o 2:00<br/>**Automatyczna kopia zapasowa** = wyłączona</br>**Integracja z usługą Azure Key Vault** = wyłączona |**Łączność SQL** = prywatna (w sieci wirtualnej)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = Wyłącz<br/>**Konfiguracja magazynu** = Ogólne<br/>**Automatyczne łatanie** = niedziela o 2:00<br/>**Automatyczna kopia zapasowa** = wyłączona</br>**Integracja z usługą Azure Key Vault** = wyłączona |

<br/>

> [!NOTE]
> Sugerowane w tym miejscu rozmiary komputerów są przeznaczone do testowania grup dostępności na maszynach wirtualnych platformy Azure. Aby uzyskać najlepszą wydajność w obciążeniach produkcyjnych, zobacz zalecenia dotyczące rozmiarów komputerów programu SQL Server i konfiguracji w [sekcji Najważniejsze wskazówki dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>

Po pełnej aprowizacji trzech maszyn wirtualnych należy dołączyć je do domeny **corp.contoso.com** i przyznać corp\install prawa administracyjne do komputerów.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Dołączanie serwerów do domeny

Teraz możesz dołączyć do maszyn wirtualnych, aby **corp.contoso.com**. Wykonaj następujące kroki zarówno dla maszyn wirtualnych programu SQL Server, jak i serwera monitora udziału plików:

1. Zdalnie połącz się z maszyną wirtualną za pomocą **funkcji BUILTIN\DomainAdmin**.
2. W **Menedżerze serwera**kliknij pozycję **Serwer lokalny**.
3. Kliknij **łącze GRUPA ROBOCZA.**
4. W sekcji **Nazwa komputera** kliknij pozycję **Zmień**.
5. Zaznacz pole wyboru **Domena** i wpisz **corp.contoso.com** w polu tekstowym. Kliknij przycisk **OK**.
6. W oknie podręcznym **Zabezpieczenia systemu Windows** określ poświadczenia domyślnego konta administratora domeny (**CORP\DomainAdmin**) i hasła (**Contoso!0000**).
7. Gdy zostanie wyświetlony komunikat "Witamy w domenie corp.contoso.com", kliknij przycisk **OK**.
8. Kliknij **przycisk Zamknij**, a następnie kliknij pozycję Uruchom ponownie **teraz** w oknie dialogowym okna dialogowego.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Dodawanie użytkownika Corp\Install jako administratora na każdej maszynie wirtualnej klastra

Po ponownym uruchomieniu każdej maszyny wirtualnej jako członek domeny dodaj **CORP\Install** jako członka lokalnej grupy administratorów.

1. Poczekaj na ponowne uruchomienie maszyny Wirtualnej, a następnie uruchom plik RDP ponownie z podstawowego kontrolera domeny, aby zalogować się do **sqlserver-0** przy użyciu konta **CORP\DomainAdmin.**
   >[!TIP]
   >Upewnij się, że logujesz się za pomocą konta administratora domeny. W poprzednich krokach używasz wbudowanego konta administratora. Teraz, gdy serwer znajduje się w domenie, użyj konta domeny. W sesji RDP określ*nazwę użytkownika* *domeny*\\.

2. W **Menedżerze serwera**wybierz pozycję **Narzędzia**, a następnie kliknij pozycję **Zarządzanie komputerem**.
3. W oknie **Zarządzanie komputerem** rozwiń węzeł **Użytkownicy i grupy lokalne**, a następnie wybierz pozycję **Grupy**.
4. Kliknij dwukrotnie grupę **Administratorzy.**
5. W oknie dialogowym **Właściwości administratorów** kliknij przycisk **Dodaj.**
6. Wprowadź użytkownika **CORP\Install**, a następnie kliknij przycisk **OK**.
7. Kliknij **przycisk OK,** aby zamknąć okno dialogowe **Właściwości administratora.**
8. Powtórz poprzednie kroki na **sqlserver-1** i **cluster-fsw**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Ustawianie kont usługi PROGRAMU SQL Server

Na każdej maszynie wirtualnej programu SQL Server ustaw konto usługi PROGRAMU SQL Server. Użyj kont utworzonych podczas konfigurowania kont domeny.

1. Otwórz **Menedżera konfiguracji programu SQL Server**.
2. Kliknij prawym przyciskiem myszy usługę SQL Server, a następnie kliknij polecenie **Właściwości**.
3. Ustaw konto i hasło.
4. Powtórz te kroki na innej maszynie wirtualnej programu SQL Server.  

W przypadku grup dostępności programu SQL Server każda maszyna wirtualna programu SQL Server musi działać jako konto domeny.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Tworzenie logowania na każdej maszynie Wirtualnej programu SQL Server dla konta instalacyjnego

Użyj konta instalacyjnego (CORP\install), aby skonfigurować grupę dostępności. To konto musi być członkiem roli serwera stałego **sysadmin** na każdej maszynie wirtualnej programu SQL Server. Następujące kroki tworzenia logowania dla konta instalacyjnego:

1. Połącz się z serwerem za pośrednictwem protokołu RDP (Remote Desktop Protocol) przy użyciu konta * \<MachineName\>\DomainAdmin.*

1. Otwórz program SQL Server Management Studio i połącz się z lokalnym wystąpieniem programu SQL Server.

1. W **Eksploratorze obiektów**kliknij pozycję **Zabezpieczenia**.

1. Kliknij prawym przyciskiem myszy **pozycję Logins**. Kliknij **pozycję Nowy login**.

1. W **polu Logowanie — nowy**kliknij pozycję **Wyszukaj**.

1. Kliknij **pozycję Lokalizacje**.

1. Wprowadź poświadczenia sieci administratora domeny.

1. Użyj konta instalacyjnego.

1. Ustaw logowanie jako członek roli serwera stałego **sysadmin.**

1. Kliknij przycisk **OK**.

Powtórz poprzednie kroki na innej maszynie wirtualnej programu SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Dodawanie funkcji klastrowania trybu failover do obu maszyn wirtualnych programu SQL Server

Aby dodać funkcje klastrowania trybu failover, wykonaj następujące czynności dotyczące obu maszyn wirtualnych programu SQL Server:

1. Połącz się z maszyną wirtualną programu SQL Server za pośrednictwem protokołu RDP (Remote Desktop Protocol) przy użyciu konta *CORP\install.* Otwórz **pulpit nawigacyjny Menedżera serwera**.
2. Kliknij łącze **Dodaj role i funkcje** na pulpicie nawigacyjnym.

    ![Menedżer serwera — dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Wybierz **przycisk Dalej,** aż dojdziesz do sekcji **Funkcje serwera.**
4. W **obszarze Funkcje**wybierz pozycję **Klastrowanie trybu failover**.
5. Dodaj dodatkowe wymagane funkcje.
6. Kliknij **przycisk Zainstaluj,** aby dodać funkcje.

Powtórz kroki na innej maszynie wirtualnej programu SQL Server.

  >[!NOTE]
  > Ten krok, wraz z faktycznym dołączeniem maszyn wirtualnych programu SQL Server do klastra trybu failover, można teraz zautomatyzować za pomocą [interfejsu wiersza polecenia platformy Azure SQL VM i](virtual-machines-windows-sql-availability-group-cli.md) [szablonów szybki start platformy Azure.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Konfigurowanie zapory na każdej maszynie wirtualnej programu SQL Server

Rozwiązanie wymaga otwarcia następujących portów TCP w zaporze:

- **Maszyna wirtualna programu SQL Server:**<br/>
   Port 1433 dla domyślnego wystąpienia programu SQL Server.
- **Sonda modułu równoważenia obciążenia platformy Azure:**<br/>
   Dowolny dostępny port. Przykłady często używają 59999.
- **Dublowanie bazy danych punktu końcowego:** <br/>
   Dowolny dostępny port. Przykłady często używają 5022.

Porty zapory muszą być otwarte na obu maszynach wirtualnych programu SQL Server.

Metoda otwierania portów zależy od używanego rozwiązania zapory. W następnej sekcji wyjaśniono, jak otworzyć porty w Zaporze systemu Windows. Otwórz wymagane porty na każdej z maszyn wirtualnych programu SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otwieranie portu TCP w zaporze

1. Na pierwszym ekranie **startowym** programu SQL Server uruchom **Zaporę systemu Windows z programem Advanced Security**.
2. W lewym okienku wybierz pozycję **Reguły przychodzące**. W prawym okienku kliknij pozycję **Nowa reguła**.
3. W przypadku **opcji Typ reguły**wybierz pozycję **Port**.
4. Dla portu określ **protokół TCP** i wpisz odpowiednie numery portów. Zobacz poniższy przykład:

   ![Zapora SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kliknij przycisk **alej**.
6. Na stronie **Akcja** **zachowaj pozycję Zezwalaj na połączenie,** a następnie kliknij przycisk **Dalej**.
7. Na stronie **Profil** zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **Dalej**.
8. Na stronie **Nazwa** określ nazwę reguły (na przykład **Sonda LB platformy Azure)** w polu **tekstowym Nazwa,** a następnie kliknij przycisk **Zakończ**.

Powtórz te kroki na drugiej maszynie wirtualnej programu SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurowanie uprawnień konta systemowego

Aby utworzyć konto dla konta systemowego i udzielić odpowiednich uprawnień, wykonaj następujące kroki w każdym wystąpieniu programu SQL Server:

1. Utwórz konto `[NT AUTHORITY\SYSTEM]` dla każdego wystąpienia programu SQL Server. Następujący skrypt tworzy to konto:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Udziel następujących uprawnień do `[NT AUTHORITY\SYSTEM]` każdego wystąpienia programu SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Następujący skrypt udziela tych uprawnień:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

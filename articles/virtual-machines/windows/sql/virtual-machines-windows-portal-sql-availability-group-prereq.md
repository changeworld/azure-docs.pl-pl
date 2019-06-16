---
title: Grupy dostępności programu SQL Server — usługa Azure virtual machines — wymagania wstępne | Dokumentacja firmy Microsoft
description: W tym samouczku pokazano, jak skonfigurować wymagania wstępne dotyczące tworzenia grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 1d0f3bfa03eb4bafdd10222e28782c318848b7f7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60592245"
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Należy spełnić wymagania wstępne dotyczące tworzenia zawsze włączonych grup dostępności na maszynach wirtualnych platformy Azure

W tym samouczku pokazano, jak należy spełnić wymagania wstępne dotyczące tworzenia [programu SQL Server zawsze włączonej grupy dostępności na maszynach wirtualnych Azure (maszyny wirtualne)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Po zakończeniu wymagania wstępne, masz kontrolera domeny, dwie maszyny wirtualne programu SQL Server i serwera monitora w pojedynczej grupy zasobów.

**Szacowany czas**: Może upłynąć kilka godzin, aby spełnić wymagania wstępne. Większość tego czasu poświęconego, tworzenia maszyn wirtualnych.

Na poniższym diagramie przedstawiono kompilacji w tym samouczku.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Zapoznaj się z dokumentacją grupy dostępności

Ten samouczek zakłada, że masz podstawową wiedzę na temat programu SQL Server zawsze włączonych grup dostępności. Jeśli nie jesteś zaznajomiony z tej technologii, zobacz [przegląd z zawsze włączonych grup dostępności (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Utwórz konto systemu Azure
Musisz mieć konto platformy Azure. Możesz [bezpłatne konto platformy Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) lub [aktywować korzyści dla subskrybentów programu Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **+** można utworzyć nowego obiektu w portalu.

   ![Nowy obiekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Typ **grupy zasobów** w **Marketplace** okno wyszukiwania.

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kliknij przycisk **grupy zasobów**.
5. Kliknij pozycję **Utwórz**.
6. W obszarze **nazwy grupy zasobów**, wpisz nazwę grupy zasobów. Na przykład wpisz **sql-ha-rg**.
7. Jeśli masz wiele subskrypcji platformy Azure, sprawdź, czy subskrypcja jest subskrypcja platformy Azure, którą chcesz utworzyć grupy dostępności w programie.
8. Wybierz lokalizację. Lokalizacja jest region platformy Azure, w którym chcesz utworzyć grupę dostępności. W tym artykule opiera się wszystkie zasoby w jednej lokalizacji platformy Azure.
9. Upewnij się, że **Przypnij do pulpitu nawigacyjnego** jest zaznaczone. To ustawienie opcjonalne umieszcza skrót dla grupy zasobów na pulpicie nawigacyjnym witryny Azure portal.

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kliknij pozycję **Utwórz**, aby utworzyć grupę zasobów.

Platforma Azure tworzy grupę zasobów i kodów skrótów do grupy zasobów w portalu.

## <a name="create-the-network-and-subnets"></a>Tworzenie sieci i podsieci
Następnym krokiem jest, aby utworzyć podsieci i sieci w grupie zasobów platformy Azure.

Rozwiązanie używa jednej sieci wirtualnej z dwiema podsieciami. [Omówienie usługi Virtual network](../../../virtual-network/virtual-networks-overview.md) zawiera więcej informacji o sieci na platformie Azure.

Aby utworzyć sieć wirtualną:

1. W witrynie Azure portal, w grupie zasobów, kliknij przycisk **+ Dodaj**. 

   ![Nowy element](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Wyszukaj **sieci wirtualnej**.

     ![Szukaj sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kliknij przycisk **sieć wirtualna**.
4. Na **sieć wirtualna**, kliknij przycisk **usługi Resource Manager** modelu wdrażania, a następnie kliknij **Utwórz**.

    W poniższej tabeli przedstawiono ustawienia dla sieci wirtualnej:

   | **Pole** | Wartość |
   | --- | --- |
   | **Nazwa** |autoHAVNET |
   | **Przestrzeń adresowa** |10.33.0.0/24 |
   | **Nazwa podsieci** |Jednostka administracyjna |
   | **Zakres adresów podsieci** |10.33.0.0/29 |
   | **Subskrypcja** |Określ subskrypcję, która ma być używana. **Subskrypcja** jest puste, jeśli masz tylko jedną subskrypcję. |
   | **Grupa zasobów** |Wybierz **Użyj istniejącej** i wybierz nazwę grupy zasobów. |
   | **Lokalizacja** |Określ lokalizację platformy Azure. |

   Zakres adresów miejsce i podsieci adresu mogą się różnić z tabeli. W zależności od subskrypcji portalu sugeruje dostępny obszar adresowania i odpowiedni zakres adresów podsieci. Jeśli nie wystarczającą przestrzeń adresowa jest dostępna, należy użyć innej subskrypcji.

   W przykładzie użyto nazwy podsieci **administratora**. Ta podsieć jest dla kontrolerów domeny.

5. Kliknij pozycję **Utwórz**.

   ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Platforma Azure, powrót do pulpitu nawigacyjnego portalu i powiadamia o utworzeniu nowej sieci.

### <a name="create-a-second-subnet"></a>Utwórz drugą podsieć
Nowa sieć wirtualna ma jedną podsieć, o nazwie **administratora**. Kontrolery domeny używają tej podsieci. Użyj maszyn wirtualnych programu SQL Server, drugą podsieć o nazwie **SQL**. Aby skonfigurować tę podsieć:

1. Na pulpicie nawigacyjnym, kliknij grupę zasobów, która została utworzona, **SQL-HA-RG**. Znajdź sieci w grupie zasobów, w obszarze **zasobów**.

    Jeśli **SQL-HA-RG** nie jest widoczny, ją znaleźć, klikając pozycję **grup zasobów** i filtrowanie według nazwy grupy zasobów.
2. Kliknij przycisk **autoHAVNET** na liście zasobów. 
3. Na **autoHAVNET** wirtualnych sieci, w obszarze **ustawienia** wybierz **podsieci**.

    Należy pamiętać, podsieci, który został już utworzony.

   ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Utwórz drugą podsieć. Kliknij przycisk **+ podsieć**.
6. Na **Dodaj podsieć**, skonfigurowanie podsieci, wpisując **sqlsubnet** w obszarze **nazwa**. Azure automatycznie określa prawidłową **zakres adresów**. Sprawdź, czy ten zakres adresów zawiera co najmniej 10 adresów. W środowisku produkcyjnym mogą wymagać większej liczby adresów.
7. Kliknij przycisk **OK**.

    ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

Poniższa tabela zawiera podsumowanie ustawień konfiguracji sieci:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |**autoHAVNET** |
| **Przestrzeń adresowa** |Ta wartość jest zależna od dostępnych przestrzeni adresowych w ramach subskrypcji. Typowa wartość to 10.0.0.0/16. |
| **Nazwa podsieci** |**admin** |
| **Zakres adresów podsieci** |Ta wartość jest zależna od zakresy adresów dostępnych w ramach subskrypcji. Typowa wartość to 10.0.0.0/24. |
| **Nazwa podsieci** |**sqlsubnet** |
| **Zakres adresów podsieci** |Ta wartość jest zależna od zakresy adresów dostępnych w ramach subskrypcji. Typowa wartość jest 10.0.1.0/24. |
| **Subskrypcja** |Określ subskrypcję, która ma być używana. |
| **Grupa zasobów** |**SQL-HA-RG** |
| **Lokalizacja** |Określenie tej samej lokalizacji, która została wybrana dla grupy zasobów. |

## <a name="create-availability-sets"></a>Tworzenie zestawów dostępności

Przed przystąpieniem do tworzenia maszyn wirtualnych, musisz utworzyć zestawy dostępności. Zestawy dostępności zmniejszenie przestojów planowanych albo nieplanowanych zdarzeń konserwacji. Zestawu dostępności platformy Azure to logiczna grupa zasobów, które platforma Azure umieszcza w fizycznych domenach awarii i domenach aktualizacji. Domena błędów gwarantuje, że elementy członkowskie zestawu dostępności oddzielnych zasobów sieci i zasilania. Domena aktualizacji gwarantuje, że elementy członkowskie zestawu dostępności nie są obniżył konserwacji w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Potrzebujesz dwóch zestawów dostępności. Jeden jest przeznaczony dla kontrolerów domeny. Druga jest przeznaczona dla maszyn wirtualnych programu SQL Server.

Aby utworzyć zestaw dostępności, przejdź do grupy zasobów, a następnie kliknij przycisk **Dodaj**. Filtrowanie wyników, wpisując **zestaw dostępności**. Kliknij przycisk **zestawu dostępności** wyniki, a następnie kliknij **Utwórz**.

W poniższej tabeli, należy skonfigurować dwa zestawy dostępności zgodnie z parametrami:

| **Pole** | Zestaw dostępności kontrolera domeny | Zestaw dostępności programu SQL Server |
| --- | --- | --- |
| **Nazwa** |adavailabilityset |sqlavailabilityset |
| **Grupa zasobów** |SQL-HA-RG |SQL-HA-RG |
| **Domeny błędów** |3 |3 |
| **Domeny aktualizacji** |5 |3 |

Po utworzeniu zestawów dostępności, wróć do grupy zasobów w witrynie Azure portal.

## <a name="create-domain-controllers"></a>Tworzenie kontrolerów domeny
Po utworzeniu sieci, podsieci, zestawy dostępności i modułu równoważenia obciążenia dostępnego z Internetu, możesz przystąpić do tworzenia maszyn wirtualnych kontrolerów domeny.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Tworzenie maszyn wirtualnych kontrolerów domeny
Aby utworzyć i skonfigurować kontrolery domeny, wróć do **SQL-HA-RG** grupy zasobów.

1. Kliknij pozycję **Add** (Dodaj). 
2. Typ **systemu Windows Server 2016 Datacenter**.
3. Kliknij przycisk **systemu Windows Server 2016 Datacenter**. W **systemu Windows Server 2016 Datacenter**, sprawdź, czy w modelu wdrażania jest **usługi Resource Manager**, a następnie kliknij przycisk **Utwórz**. 

Powtórz wcześniejsze kroki, aby utworzyć dwie maszyny wirtualne. Nazwa dwie maszyny wirtualne:

* kontrolera domeny podstawowej usługi AD
* ad-secondary-dc

  > [!NOTE]
  > **Kontrolerów domeny pomocniczy ad** maszyna wirtualna jest opcjonalne, w celu zapewnienia wysokiej dostępności dla usług domenowych w usłudze Active Directory.
  >
  >

W poniższej tabeli przedstawiono ustawienia dla tych dwóch maszyn:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |Pierwszy kontroler domeny: *kontrolera domeny podstawowej usługi ad*.</br>Drugi kontroler domeny *kontrolerów domeny pomocniczy ad*. |
| **Typ dysku maszyny wirtualnej** |SSD |
| **Nazwa użytkownika** |DomainAdmin |
| **Hasło** |Contoso!0000 |
| **Subskrypcja** |*Twoja subskrypcja* |
| **Grupa zasobów** |SQL-HA-RG |
| **Lokalizacja** |*Twoja lokalizacja* |
| **Rozmiar** |DS1_V2 |
| **Storage** | **Użyj usługi managed disks** - **tak** |
| **Sieć wirtualna** |autoHAVNET |
| **Podsieć** |admin |
| **Publiczny adres IP** |*Tej samej nazwie co maszyna wirtualna* |
| **Sieciowa grupa zabezpieczeń** |*Tej samej nazwie co maszyna wirtualna* |
| **Zestaw dostępności** |adavailabilityset </br>**Domeny błędów**: 2 </br>**Domeny aktualizacji**: 2|
| **Diagnostyka** |Enabled (Włączony) |
| **Konto magazynu diagnostyki** |*Automatycznie utworzone* |

   >[!IMPORTANT]
   >Tylko można umieścić maszyny Wirtualnej w zestawie podczas jego tworzenia dostępności. Nie można zmienić zestawu dostępności po utworzeniu maszyny Wirtualnej. Zobacz [Zarządzanie dostępnością maszyn wirtualnych](../manage-availability.md).

Platforma Azure tworzy maszyny wirtualne.

Po utworzeniu maszyny wirtualnej, należy skonfigurować kontroler domeny.

### <a name="configure-the-domain-controller"></a>Konfigurowanie kontrolera domeny
W poniższych krokach skonfigurować **kontrolera domeny podstawowej usługi ad** maszyn jako kontroler domeny corp.contoso.com.

1. Otwórz w portalu, **SQL-HA-RG** zasobu, grupy i wybierz **kontrolera domeny podstawowej usługi ad** maszyny. Na **kontrolera domeny podstawowej usługi ad**, kliknij przycisk **Connect** do otwarcia pliku RDP, aby uzyskać dostęp do pulpitu zdalnego.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Zaloguj się przy użyciu konta administratora skonfigurowane ( **\DomainAdmin**) i hasło (**Contoso! 0000**).
3. Domyślnie **Menedżera serwera** powinien zostać wyświetlony pulpit nawigacyjny.
4. Kliknij przycisk **Dodaj role i funkcje** łącze na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Wybierz **dalej** aż dojdziesz do **ról serwera** sekcji.
6. Wybierz **Active Directory Domain Services** i **serwer DNS** ról. Po wyświetleniu monitu, Dodaj wszelkie dodatkowe funkcje, które są wymagane przez tych ról.

   > [!NOTE]
   > Windows wyświetli ostrzeżenie, że nie istnieje żaden adres IP. Jeśli testujesz konfigurację, kliknij przycisk **Kontynuuj**. Na potrzeby scenariuszy produkcyjnych należy ustawić adres IP na statyczną w witrynie Azure portal lub [ustawić statyczny adres IP maszyny kontrolera domeny przy użyciu programu PowerShell](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Dodaj okno dialogowe ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Kliknij przycisk **dalej** aż **potwierdzenie** sekcji. Wybierz **automatycznego ponownego uruchomienia serwera docelowego, jeśli jest to wymagane** pole wyboru.
8. Kliknij pozycję **Zainstaluj**.
9. Po funkcji zakończyć instalację, wróć do **Menedżera serwera** pulpitu nawigacyjnego.
10. Wybierz nową **usług AD DS** opcji w okienku po lewej stronie.
11. Kliknij przycisk **więcej** łącze na żółty pasek ostrzeżenie.

    ![Usługi AD DS z okna dialogowego na maszynie Wirtualnej z serwera DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. W **akcji** kolumny **wszystkie szczegóły zadania serwera** okno dialogowe, kliknij przycisk **podwyższanie poziomu tego serwera do poziomu kontrolera domeny**.
13. W **Kreator konfiguracji usług domenowych Active Directory**, użyj następujących wartości:

    | **Strona** | Ustawienie |
    | --- | --- |
    | **Konfiguracja wdrożenia** |**Dodawanie nowego lasu**<br/> **Nazwa domeny głównej** = corp.contoso.com |
    | **Opcje kontrolera domeny** |**Hasło trybu DSRM** = Contoso! 0000<br/>**Potwierdź hasło** = Contoso! 0000 |
14. Kliknij przycisk **dalej** przechodzić przez inne strony w kreatorze. Na **Sprawdzanie wymagań wstępnych** upewnij się, że zostanie wyświetlony następujący komunikat: **Wszystkie testy wymagań wstępnych zakończyło się pomyślnie**. Możesz przejrzeć wszystkie odpowiednie komunikaty ostrzegawcze, ale można kontynuować instalacji.
15. Kliknij pozycję **Zainstaluj**. **Kontrolera domeny podstawowej usługi ad** automatycznie rozruchu maszyny wirtualnej.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Należy pamiętać, adres IP podstawowego kontrolera domeny

Użyj podstawowego kontrolera domeny dla serwera DNS. Należy pamiętać, adres IP kontrolera domeny głównej.

Jest jednym ze sposobów, aby uzyskać adres IP kontrolera domeny głównej za pomocą witryny Azure portal.

1. W witrynie Azure portal Otwórz grupę zasobów.

2. Kliknij na podstawowym kontrolerze domeny.

3. Na podstawowym kontrolerze domeny, kliknij przycisk **interfejsy sieciowe**.

![Interfejsy sieciowe](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Należy pamiętać, prywatny adres IP dla tego serwera.

### <a name="configure-the-virtual-network-dns"></a>Konfigurowanie sieci wirtualnej DNS
Po utworzeniu pierwszego kontrolera domeny i Włącz DNS na pierwszym serwerze, należy skonfigurować sieci wirtualnej, aby użyć tego serwera DNS.

1. W witrynie Azure portal kliknij sieć wirtualną.

2. W obszarze **ustawienia**, kliknij przycisk **serwer DNS**.

3. Kliknij przycisk **niestandardowe**, a następnie wpisz prywatny adres IP podstawowego kontrolera domeny.

4. Kliknij pozycję **Zapisz**.

### <a name="configure-the-second-domain-controller"></a>Konfigurowanie kontrolera domeny
Po rozruchu z podstawowym kontrolerem domeny, można skonfigurować kontrolera domeny. Ten opcjonalny etap jest wysoka dostępność. Wykonaj następujące kroki, aby skonfigurować kontrolera domeny:

1. Otwórz w portalu, **SQL-HA-RG** zasobu, grupy i wybierz **kontrolerów domeny pomocniczy ad** maszyny. Na **kontrolerów domeny pomocniczy ad**, kliknij przycisk **Connect** do otwarcia pliku RDP, aby uzyskać dostęp do pulpitu zdalnego.
2. Zaloguj się do maszyny Wirtualnej przy użyciu konta administratora skonfigurowane (**BUILTIN\DomainAdmin**) i hasło (**Contoso! 0000**).
3. Zmień adres preferowanego serwera DNS na adres kontrolera domeny.
4. W **Centrum sieci i udostępniania**, kliknij pozycję interfejs sieciowy.
   ![Interfejs sieciowy](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kliknij pozycję **Właściwości**.
6. Wybierz **protokół internetowy w wersji 4 (TCP/IPv4)** i kliknij przycisk **właściwości**.
7. Wybierz **Użyj następujących adresów serwerów DNS** i określ adres podstawowy kontroler domeny w **preferowany serwer DNS**.
8. Kliknij przycisk **OK**, a następnie **Zamknij** aby zatwierdzić zmiany. Jesteś teraz maszynę Wirtualną, aby przyłączyć się **corp.contoso.com**.

   >[!IMPORTANT]
   >Jeśli w przypadku utraty połączenia do pulpitu zdalnego, po zmianie ustawienia DNS, przejdź do witryny Azure portal, a następnie uruchom ponownie maszynę wirtualną.

9. Korzystając z pulpitu zdalnego na dodatkowy kontroler domeny, otwórz **pulpitu nawigacyjnego Menedżera serwera**.
10. Kliknij przycisk **Dodaj role i funkcje** łącze na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Wybierz **dalej** aż dojdziesz do **ról serwera** sekcji.
12. Wybierz **Active Directory Domain Services** i **serwer DNS** ról. Po wyświetleniu monitu, Dodaj wszelkie dodatkowe funkcje, które są wymagane przez tych ról.
13. Po funkcji zakończyć instalację, wróć do **Menedżera serwera** pulpitu nawigacyjnego.
14. Wybierz nową **usług AD DS** opcji w okienku po lewej stronie.
15. Kliknij przycisk **więcej** łącze na żółty pasek ostrzeżenie.
16. W **akcji** kolumny **wszystkie szczegóły zadania serwera** okno dialogowe, kliknij przycisk **podwyższanie poziomu tego serwera do poziomu kontrolera domeny**.
17. W obszarze **konfiguracji wdrożenia**, wybierz opcję **dodać kontroler domeny do istniejącej domeny**.
    ![Konfiguracja wdrożenia](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kliknij pozycję **Wybierz**.
19. Połącz przy użyciu konta administratora (**CORP. CONTOSO.COM\domainadmin**) i hasło (**Contoso! 0000**).
20. W **wybierz domeny lasu**, kliknij swoją domenę, a następnie kliknij **OK**.
21. W **opcje kontrolera domeny**, użyj wartości domyślnych i ustawić hasło DSRM.

    >[!NOTE]
    >**Opcje serwera DNS** strony może być ostrzega użytkownika, że nie można utworzyć delegowania dla tego serwera DNS. Możesz zignorować to ostrzeżenie w środowiskach nieprodukcyjnych.
22. Kliknij przycisk **dalej** do momentu osiągnięcia okna dialogowego **wymagania wstępne** Sprawdź. Następnie kliknij pozycję **Zainstaluj**.

Po zakończeniu zmiany konfiguracji, uruchom ponownie serwer.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Dodaj prywatny adres IP do drugiego kontrolera domeny na serwerze DNS sieci VPN

W witrynie Azure portal w ramach sieci wirtualnej należy zmienić serwer DNS, aby uwzględnić adres IP dodatkowy kontroler domeny. To ustawienie umożliwia nadmiarowość usług DNS.

### <a name="DomainAccounts"></a> Konfigurowanie kont domeny

W następnych krokach konfigurowania kont usługi Active Directory. W poniższej tabeli przedstawiono kont:

| |Konto instalacji<br/> |sqlserver-0 <br/>Konto programu SQL Server i usługi agenta SQL |sqlserver-1<br/>Konto programu SQL Server i usługi agenta SQL
| --- | --- | --- | ---
|**Imię** |Instalowanie |SQLSvc1 | SQLSvc2
|**SamAccountName użytkownika** |Instalowanie |SQLSvc1 | SQLSvc2

Poniższe kroki umożliwiają tworzenie każdego konta.

1. Zaloguj się do **kontrolera domeny podstawowej usługi ad** maszyny.
2. W **Menedżera serwera**, wybierz opcję **narzędzia**, a następnie kliknij przycisk **Centrum administracyjne usługi Active Directory**.   
3. Wybierz **corp (lokalna)** z okienka po lewej stronie.
4. Po prawej stronie **zadania** okienku wybierz **New**, a następnie kliknij przycisk **użytkownika**.
   ![Centrum administracyjne usługi Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Ustaw złożone hasło dla każdego konta.<br/> W środowiskach nieprodukcyjnych Ustaw konto użytkownika, nigdy nie wygasa.

5. Kliknij przycisk **OK** do utworzenia użytkownika.
6. Powtórz poprzednie kroki dla każdego z trzech kont.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Udzielenia wymaganych uprawnień do konta instalacji
1. W **Centrum administracyjne usługi Active Directory**, wybierz opcję **corp (lokalna)** w okienku po lewej stronie. Następnie w prawym **zadania** okienku kliknij **właściwości**.

    ![Właściwości użytkownika domeny Corp.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Wybierz **rozszerzenia**, a następnie kliknij przycisk **zaawansowane** znajdujący się na **zabezpieczeń** kartę.
3. W **Zaawansowane ustawienia zabezpieczeń dla corp** okno dialogowe, kliknij przycisk **Dodaj**.
4. Kliknij przycisk **Wybierz podmiot zabezpieczeń**, wyszukaj **CORP\Install**, a następnie kliknij przycisk **OK**.
5. Wybierz **odczytywanie wszystkich właściwości** pole wyboru.

6. Wybierz **Tworzenie obiektów komputerów** pole wyboru.

     ![Uprawnienia użytkownika Corp.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kliknij przycisk **OK**, a następnie kliknij przycisk **OK** ponownie. Zamknij **corp** okna właściwości.

Teraz, gdy zakończysz, skonfigurowanie usługi Active Directory i obiektów użytkowników, Utwórz dwie maszyny wirtualne programu SQL Server i serwera monitora maszyny Wirtualnej. Następnie dołącz wszystkie trzy do domeny.

## <a name="create-sql-server-vms"></a>Tworzenie maszyn wirtualnych programu SQL Server

Utwórz trzy dodatkowe maszyny wirtualne. To rozwiązanie wymaga dwóch maszyn wirtualnych za pomocą wystąpienia programu SQL Server. Trzeci maszyna wirtualna będzie działać jako monitor. Można użyć systemu Windows Server 2016 [monitora w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), ale pod kątem zgodności ze starszymi systemami operacyjnymi w tym dokumencie używa maszyny wirtualnej dla monitora.  

Przed kontynuowaniem należy wziąć pod uwagę następujące decyzje dotyczące projektu.

* **Magazyn — usługa Azure Managed Disks**

   W przypadku magazynu maszyny wirtualnej Użyj usługi Azure Managed Disks. Firma Microsoft zaleca dysków zarządzanych dla maszyn wirtualnych programu SQL Server. Funkcja Dyski zarządzane obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z funkcją Dyski zarządzane znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. Aby uzyskać więcej informacji, zobacz [Omówienie funkcji Dyski zarządzane platformy Azure](../managed-disks-overview.md). Aby uzyskać szczegółowe informacje na temat dysków zarządzanych w zestawie dostępności, zobacz [Użyj dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Sieć — prywatne adresy IP w środowisku produkcyjnym**

   W przypadku maszyn wirtualnych w tym samouczku używa publicznych adresów IP. Publiczny adres IP umożliwia połączenia zdalnego bezpośrednio do maszyny wirtualnej za pośrednictwem Internetu — ułatwia czynności konfiguracyjnych. W środowiskach produkcyjnych firma Microsoft zaleca tylko prywatnych adresów IP w celu zmniejszenia rozmiaru luk w zabezpieczeniach wystąpienia programu SQL Server zasobu maszyny Wirtualnej.

### <a name="create-and-configure-the-sql-server-vms"></a>Tworzenie i konfigurowanie maszyn wirtualnych programu SQL Server
Następnie należy utworzyć trzy maszyny wirtualne — dwie maszyny wirtualne programu SQL Server i maszyny Wirtualnej na dodatkowym węźle klastra. Do tworzenia poszczególnych maszyn wirtualnych, wróć do **SQL-HA-RG** grupy zasobów, kliknij przycisk **Dodaj**, wyszukaj element galerii odpowiednie, kliknij przycisk **maszyny wirtualnej**, a następnie kliknij przycisk  **Z galerii**. Użyj informacji w poniższej tabeli ułatwiają tworzenie maszyn wirtualnych:


| Strona | Maszyna wirtualna 1 | Maszyna wirtualna 2 | VM3 |
| --- | --- | --- | --- |
| Wybierz element galerii odpowiednią |**Windows Server 2016 Datacenter** |**SQL Server 2016 z dodatkiem SP1 Enterprise w systemie Windows Server 2016** |**SQL Server 2016 z dodatkiem SP1 Enterprise w systemie Windows Server 2016** |
| Konfiguracja maszyny wirtualnej **podstawy** |**Nazwa** = fsw klastra<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = Contoso! 0000<br/>**Subskrypcja** = subskrypcji<br/>**Grupa zasobów** = SQL-HA-RG<br/>**Lokalizacja** = swojej lokalizacji platformy azure |**Nazwa** = sqlserver 0<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = Contoso! 0000<br/>**Subskrypcja** = subskrypcji<br/>**Grupa zasobów** = SQL-HA-RG<br/>**Lokalizacja** = swojej lokalizacji platformy azure |**Nazwa** = sqlserver 1<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = Contoso! 0000<br/>**Subskrypcja** = subskrypcji<br/>**Grupa zasobów** = SQL-HA-RG<br/>**Lokalizacja** = swojej lokalizacji platformy azure |
| Konfiguracja maszyny wirtualnej **rozmiar** |**ROZMIAR** = DS1\_V2 (1 vCPU, 3,5 GB) |**ROZMIAR** = DS2\_w wersji 2 (2 procesorów wirtualnych Vcpu, 7 GB)</br>Rozmiar musi obsługiwać magazyn SSD (obsługę dysku Premium. )) |**ROZMIAR** = DS2\_w wersji 2 (2 procesorów wirtualnych Vcpu, 7 GB) |
| Konfiguracja maszyny wirtualnej **ustawienia** |**Magazyn**: Korzystają z dysków zarządzanych.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** automatycznie wygenerowany.<br/>**Sieciowa grupa zabezpieczeń** = None<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj konta usługi storage automatycznie generowanych<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |**Magazyn**: Korzystają z dysków zarządzanych.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** automatycznie wygenerowany.<br/>**Sieciowa grupa zabezpieczeń** = None<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj konta usługi storage automatycznie generowanych<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |**Magazyn**: Korzystają z dysków zarządzanych.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** automatycznie wygenerowany.<br/>**Sieciowa grupa zabezpieczeń** = None<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj konta usługi storage automatycznie generowanych<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |
| Konfiguracja maszyny wirtualnej **ustawień programu SQL Server** |Nie dotyczy |**Łączność z serwerem SQL** = prywatne (wewnątrz sieci wirtualnej)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = wyłączone<br/>**Konfiguracja magazynu** = ogólne<br/>**Automatyczne stosowanie poprawek** = niedziela, 2:00<br/>**Zautomatyzowane tworzenie kopii zapasowej** = wyłączone</br>**Integracja z usługą Azure Key Vault** = wyłączone |**Łączność z serwerem SQL** = prywatne (wewnątrz sieci wirtualnej)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = wyłączone<br/>**Konfiguracja magazynu** = ogólne<br/>**Automatyczne stosowanie poprawek** = niedziela, 2:00<br/>**Zautomatyzowane tworzenie kopii zapasowej** = wyłączone</br>**Integracja z usługą Azure Key Vault** = wyłączone |

<br/>

> [!NOTE]
> Rozmiary maszyn zalecane w tym miejscu są przeznaczone do celów testowania grup dostępności na maszynach wirtualnych Azure. Aby uzyskać najlepszą wydajność dla obciążeń produkcyjnych Zobacz zaleceń dotyczących rozmiarów maszyny programu SQL Server i konfiguracji w [najlepsze rozwiązania dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Po trzy maszyny wirtualne są w pełni zaaprowizowana, należy połączyć je **corp.contoso.com** domeny i przyznać uprawnienia administracyjne CORP\Install do maszyn.

### <a name="joinDomain"></a>Dołącz serwery do domeny

Teraz możesz połączyć maszyny wirtualne do **corp.contoso.com**. Zarówno maszyny wirtualne programu SQL Server, jak i serwera monitora udziału plików, wykonaj następujące czynności:

1. Połączenie zdalne z maszyną wirtualną za pomocą **BUILTIN\DomainAdmin**.
2. W **Menedżera serwera**, kliknij przycisk **lokalnego serwera**.
3. Kliknij przycisk **grupy roboczej** łącza.
4. W **nazwy komputera** kliknij **zmiany**.
5. Wybierz **domeny** pole wyboru i wpisz **corp.contoso.com** w polu tekstowym. Kliknij przycisk **OK**.
6. W **zabezpieczeń Windows** okno podręczne okno dialogowe, określ poświadczenia dla konta administratora domeny domyślne (**CORP\DomainAdmin**) i hasło (**Contoso! 0000**) .
7. Gdy zostanie wyświetlony komunikat "Witaj do domeny corp.contoso.com", kliknij przycisk **OK**.
8. Kliknij przycisk **Zamknij**, a następnie kliknij przycisk **Uruchom ponownie teraz** w oknie podręcznym.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Dodaj użytkownika Corp\Install jako administrator w każdym klastrze maszyny Wirtualnej

Po uruchomieniu każda maszyna wirtualna jest członkiem domeny, Dodaj **CORP\Install** jako członek lokalnej grupy administratorów.

1. Poczekaj na zakończenie ponownego uruchomienia maszyny Wirtualnej, a następnie uruchom plik RDP, ponownie z podstawowego kontrolera domeny do logowania się w **sqlserver 0** przy użyciu **CORP\DomainAdmin** konta.
   >[!TIP]
   >Upewnij się, że możesz zalogować się przy użyciu konta administratora domeny. W poprzednich krokach były przy użyciu konta administratora WBUDOWANE w. Teraz, gdy serwer jest w domenie, należy użyć konta domeny. W sesji protokołu RDP, określ *domeny*\\*username*.

2. W **Menedżera serwera**, wybierz opcję **narzędzia**, a następnie kliknij przycisk **Zarządzanie komputerem**.
3. W **Zarządzanie komputerem** okna, rozwiń węzeł **lokalnych użytkowników i grup**, a następnie wybierz pozycję **grup**.
4. Kliknij dwukrotnie **Administratorzy** grupy.
5. W **właściwości: Administratorzy** okno dialogowe, kliknij przycisk **Dodaj** przycisku.
6. Wprowadź nazwę użytkownika **CORP\Install**, a następnie kliknij przycisk **OK**.
7. Kliknij przycisk **OK** zamknąć **właściwości administratora** okna dialogowego.
8. Powtórz poprzednie kroki w **sqlserver 1** i **fsw klastra**.

### <a name="setServiceAccount"></a>Ustawianie konta usług SQL Server

Na każdej maszynie Wirtualnej serwera SQL Skonfiguruj konto usługi programu SQL Server. Użyj konta, utworzone podczas konfigurowania konta domeny.

1. Otwórz **Menedżera konfiguracji SQL Server**.
2. Kliknij prawym przyciskiem myszy usługę programu SQL Server, a następnie kliknij przycisk **właściwości**.
3. Ustaw nazwę konta i hasło.
4. Powtórz te kroki dla innych maszyn wirtualnych serwera SQL.  

Dla grup dostępności programu SQL Server każda maszyna wirtualna serwera SQL musi zostać uruchomiony jako konto domeny.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Utwórz Zaloguj się na każdej maszynie Wirtualnej serwera SQL dla konta instalacji

Aby skonfigurować grupę dostępności, należy użyć konta instalacji (CORP\install). To konto musi być członkiem **sysadmin** stałej roli serwera na każdej maszynie Wirtualnej serwera SQL. Poniższe kroki umożliwiają utworzenie logowania dla konta instalacji:

1. Łączenie z serwerem za pośrednictwem protokołu RDP (Remote Desktop) za pomocą  *\<MachineName\>\DomainAdmin* konta.

1. Otwórz program SQL Server Management Studio i Połącz z lokalnego wystąpienia programu SQL Server.

1. W **Eksplorator obiektów**, kliknij przycisk **zabezpieczeń**.

1. Kliknij prawym przyciskiem myszy **logowania**. Kliknij przycisk **nowy identyfikator logowania**.

1. W **identyfikator logowania — nowy**, kliknij przycisk **wyszukiwania**.

1. Kliknij przycisk **lokalizacje**.

1. Wprowadź poświadczenia domeny administratora sieci.

1. Użyj konta instalacji.

1. Ustaw Logowanie jako członek **sysadmin** stałej roli serwera.

1. Kliknij przycisk **OK**.

Powtórz te czynności na drugą maszyną Wirtualną serwera SQL.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Dodawanie funkcji klastra trybu Failover na obu maszynach wirtualnych serwera SQL

Aby dodać funkcje klaster trybu Failover, wykonaj następujące czynności na obu maszynach wirtualnych serwera SQL:

1. Łączenie z maszyną wirtualną programu SQL Server za pośrednictwem protokołu RDP (Remote Desktop) przy użyciu *CORP\install* konta. Otwórz **pulpitu nawigacyjnego Menedżera serwera**.
2. Kliknij przycisk **Dodaj role i funkcje** łącze na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Wybierz **dalej** aż dojdziesz do **funkcji serwera** sekcji.
4. W **funkcji**, wybierz opcję **klastra trybu Failover**.
5. Dodaj wszelkie dodatkowe wymagane funkcje.
6. Kliknij przycisk **zainstalować** Aby dodać funkcje.

Powtórz kroki na drugą maszyną Wirtualną serwera SQL.

  >[!NOTE]
  > Ten krok, wraz z faktycznie przyłączania maszyny wirtualne programu SQL Server do klastra trybu failover, teraz można zautomatyzować za pomocą [wiersza polecenia platformy Azure SQL VM](virtual-machines-windows-sql-availability-group-cli.md) i [szablony szybkiego startu platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Skonfiguruj zaporę na każdej maszynie Wirtualnej serwera SQL

To rozwiązanie wymaga następujących portów TCP były otwarte w zaporze:

- **Program SQL Server VM**:<br/>
   Port 1433 dla domyślnego wystąpienia programu SQL Server.
- **Sonda modułu równoważenia obciążenia platformy Azure:**<br/>
   Dowolny dostępny port. Przykłady często używają 59999.
- **Końcowego dublowania bazy danych:** <br/>
   Dowolny dostępny port. Przykłady często używają 5022.

Porty zapory muszą być otwarte na obu maszynach wirtualnych serwera SQL.

Sposób otwierania portów zależy od zaporę, którego używasz. Następnej sekcji opisano sposób otwierania portów w Zaporze Windows. Otworzyć wymagane porty na wszystkich maszynach wirtualnych programu SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otwórz TCP port w zaporze

1. Na pierwszym serwerze SQL **Start** ekranu, uruchom **Zapora Windows z zabezpieczeniami zaawansowanymi**.
2. W okienku po lewej stronie wybierz **reguły ruchu przychodzącego**. W okienku po prawej stronie kliknij **nową regułę**.
3. Aby uzyskać **typ reguły**, wybierz **portu**.
4. Za pośrednictwem portu usługi, należy określić **TCP** i wpisz numery odpowiedni port. Zobacz poniższy przykład:

   ![Zapory SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kliknij przycisk **Dalej**.
6. Na **akcji** Zachowaj **Zezwalaj na połączenie** zaznaczone, a następnie kliknij przycisk **dalej**.
7. Na **profilu** strony, zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **dalej**.
8. Na **nazwa** Określ nazwę reguły, (takie jak **sondy modułu równoważenia obciążenia Azure**) w **nazwa** polu tekstowym, a następnie kliknij przycisk **Zakończ**.

Powtórz te czynności na drugiej maszynie Wirtualnej programu SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurowanie uprawnień konta systemu

Aby utworzyć konto usługi dla konta systemowego i Przyznaj odpowiednie uprawnienia, wykonaj następujące czynności na każdym wystąpieniu programu SQL Server:

1. Tworzenie konta na potrzeby `[NT AUTHORITY\SYSTEM]` w każdym wystąpieniu programu SQL Server. Poniższy skrypt tworzy to konto:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Przyznaj następujące uprawnienia do `[NT AUTHORITY\SYSTEM]` w każdym wystąpieniu programu SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Poniższy skrypt te uprawnienia:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie grupy dostępności programu SQL Server Always On na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

---
title: Grupy dostępności programu SQL Server — maszyn wirtualnych platformy Azure — wymagania wstępne | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób konfigurowania wymagań wstępnych dotyczących tworzenia grupy dostępności programu SQL Server AlwaysOn na maszynach wirtualnych Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.openlocfilehash: f2a0af65af068f3a78a08e46e0e42caefd87d7b1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Spełnić wymagania wstępne dotyczące tworzenia zawsze włączonych grup dostępności na maszynach wirtualnych Azure

W tym samouczku przedstawiono sposób spełnić wymagania wstępne dotyczące tworzenia [programu SQL Server zawsze włączone grupy dostępności na maszynach wirtualnych platformy Azure (VM)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Po zakończeniu wymagania wstępne są kontrolera domeny, dwóch maszyn wirtualnych programu SQL Server i serwer monitora w pojedynczej grupy zasobów.

**Szacowanie czasu**: może potrwać kilka godzin, aby spełnić wymagania wstępne. Większość tego czasu jest spędzana na tworzenie maszyn wirtualnych.

Na poniższym diagramie przedstawiono kompilacji w samouczku.

![Grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Przejrzyj dokumentację grupy dostępności

Ten samouczek zakłada, że masz podstawową wiedzę na temat programu SQL Server zawsze włączonych grup dostępności. Jeśli nie masz doświadczenia w obsłudze tej technologii, zobacz [Omówienie programu zawsze włączonych grup dostępności (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Utwórz konto systemu Azure
Musisz mieć konto platformy Azure. Możesz [Załóż bezpłatne konto platformy Azure](/pricing/free-trial/?WT.mc_id=A261C142F) lub [aktywować korzyści dla subskrybentów programu Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się w [Portalu Azure](http://portal.azure.com).
2. Kliknij przycisk **+** można utworzyć nowego obiektu w portalu.

   ![Nowy obiekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Typ **grupy zasobów** w **Marketplace** okno wyszukiwania.

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kliknij przycisk **grupy zasobów**.
5. Kliknij przycisk **Utwórz**.
6. W obszarze **Nazwa grupy zasobów**, wpisz nazwę grupy zasobów. Na przykład wpisz **sql-ha-zarządcy zasobów**.
7. Jeśli masz wiele subskrypcji Azure, sprawdź, czy subskrypcja jest subskrypcja platformy Azure, który chcesz utworzyć grupy dostępności w.
8. Wybierz lokalizację. Lokalizacja jest region platformy Azure, której chcesz utworzyć grupy dostępności. W tym artykule kompilacje wszystkie zasoby w jednej lokalizacji platformy Azure.
9. Sprawdź, czy **Przypnij do pulpitu nawigacyjnego** jest zaznaczony. To ustawienie opcjonalne umieszcza skrót dla grupy zasobów na pulpicie nawigacyjnym portalu Azure.

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kliknij przycisk **Utwórz** do utworzenia grupy zasobów.

Azure tworzy grupę zasobów i numerów PIN skrót do grupy zasobów w portalu.

## <a name="create-the-network-and-subnets"></a>Tworzenie sieci i podsieci
Następnym krokiem jest można utworzyć sieci i podsieci w grupie zasobów platformy Azure.

W tym rozwiązaniu zastosowano jedną sieć wirtualną z dwiema podsieciami. [Omówienie sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md) zawiera więcej informacji dotyczących sieci na platformie Azure.

Aby utworzyć sieć wirtualną:

1. W portalu Azure, w grupie zasobów, kliknij przycisk **+ Dodaj**. 

   ![Nowy element](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Wyszukaj **sieci wirtualnej**.

     ![Sieć wirtualna wyszukiwania](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kliknij przycisk **sieci wirtualnej**.
4. Na **sieci wirtualnej**, kliknij przycisk **Resource Manager** model wdrażania, a następnie kliknij przycisk **Utwórz**.

    W poniższej tabeli przedstawiono ustawienia dla sieci wirtualnej:

   | **Pole** | Wartość |
   | --- | --- |
   | **Nazwa** |autoHAVNET |
   | **Przestrzeń adresowa** |10.33.0.0/24 |
   | **Nazwa podsieci** |Jednostka administracyjna |
   | **Zakres adresów podsieci** |10.33.0.0/29 |
   | **Subskrypcja** |Określ subskrypcję, która ma być używana. **Subskrypcja** jest puste, jeśli masz tylko jedną subskrypcję. |
   | **Grupa zasobów** |Wybierz **Użyj istniejącego** i wybierz nazwę grupy zasobów. |
   | **Lokalizacja** |Określ lokalizację platformy Azure. |

   Zakres adresów miejsca i podsieć adresu mogą się różnić od tabeli. W zależności od subskrypcji portalu sugeruje przestrzeni adresów dostępne i odpowiedni zakres adresów podsieci. Jeśli nie ma wystarczającej przestrzeni adresów dostępne, użyj innej subskrypcji.

   W przykładzie użyto nazwy podsieci **Admin**. Ta podsieć jest dla kontrolerów domeny.

5. Kliknij przycisk **Utwórz**.

   ![Skonfiguruj sieć wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure powrót do pulpitu nawigacyjnego portalu i powiadamia użytkownika, podczas tworzenia nowej sieci.

### <a name="create-a-second-subnet"></a>Tworzenie drugiej podsieci
Nowa sieć wirtualna ma jedną podsieć o nazwie **Admin**. Kontrolery domeny używają tej podsieci. Użyj maszyn wirtualnych programu SQL Server o nazwie drugiej podsieci **SQL**. Aby skonfigurować tej podsieci:

1. Na pulpicie nawigacyjnym kliknij grupę zasobów, która została utworzona, **SQL-HA-zarządcy zasobów**. Zlokalizuj sieci w grupie zasobów w obszarze **zasobów**.

    Jeśli **SQL-HA-zarządcy zasobów** nie jest widoczny, znaleźć, klikając **grup zasobów** i filtrowanie według nazwy grupy zasobów.
2. Kliknij przycisk **autoHAVNET** na liście zasobów. 
3. Na **autoHAVNET** wirtualnych sieci, w obszarze **ustawienia** , kliknij przycisk **podsieci**.

    Należy pamiętać, podsieci, które już utworzone.

   ![Skonfiguruj sieć wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Tworzenie drugiej podsieci. Kliknij przycisk **+ podsieci**.
6. Na **Dodaj podsieć**, skonfiguruj podsieci, wpisując **sqlsubnet** w obszarze **nazwa**. Azure automatycznie określa prawidłową **zakres adresów**. Sprawdź, czy ten zakres adresów ma co najmniej 10 adresów w nim. W środowisku produkcyjnym może wymagać więcej adresów.
7. Kliknij przycisk **OK**.

    ![Skonfiguruj sieć wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

Poniższa tabela zawiera podsumowanie ustawień konfiguracji sieci:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |**autoHAVNET** |
| **Przestrzeń adresowa** |Ta wartość zależy od przestrzeni adresowych dostępne w ramach subskrypcji. Typowe wartości to 10.0.0.0/16. |
| **Nazwa podsieci** |**admin** |
| **Zakres adresów podsieci** |Ta wartość zależy od zakresy adresów dostępne w ramach subskrypcji. Typowe wartości to 10.0.0.0/24. |
| **Nazwa podsieci** |**sqlsubnet** |
| **Zakres adresów podsieci** |Ta wartość zależy od zakresy adresów dostępne w ramach subskrypcji. Typowe wartości to 10.0.1.0/24. |
| **Subskrypcja** |Określ subskrypcję, która ma być używana. |
| **Grupa zasobów** |**SQL-HA-RG** |
| **Lokalizacja** |Określenie tej samej lokalizacji, który został wybrany dla grupy zasobów. |

## <a name="create-availability-sets"></a>Tworzenie zestawów dostępności

Przed utworzeniem maszyny wirtualnej, musisz utworzyć zestawy dostępności. Zestawy dostępności skrócić czas przestojów planowana lub nieplanowana konserwacja zdarzeń. Zestaw dostępności Azure jest logiczną grupa zasobów Azure umieszcza w fizycznych domenach awarii i Aktualizacja domeny. Domeny błędów gwarantuje, że elementy członkowskie w zestawie dostępności oddzielne zasilania i zasobów sieciowych. Domeny aktualizacji gwarantuje, że członkowie zestawu dostępności nie są obniżył konserwacji w tym samym czasie. Aby uzyskać dodatkowe informacje, zobacz [Zarządzaj dostępnością maszyn wirtualnych](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Należy dwóch zestawów dostępności. Jedna jest dla kontrolerów domeny. Druga jest przeznaczona dla maszyn wirtualnych serwera SQL.

Aby utworzyć zbiór dostępności, przejdź do grupy zasobów, a następnie kliknij przycisk **Dodaj**. Filtrowanie wyników, wpisując **zestawu dostępności**. Kliknij przycisk **zestawu dostępności** wyników, a następnie kliknij polecenie **Utwórz**.

W poniższej tabeli, należy skonfigurować dwa zestawy dostępności zgodnie z parametrami:

| **Pole** | Zestaw dostępności kontrolera domeny | Zestaw dostępności programu SQL Server |
| --- | --- | --- |
| **Nazwa** |adavailabilityset |sqlavailabilityset |
| **Grupa zasobów** |SQL-HA-RG |SQL-HA-RG |
| **Domen błędów** |3 |3 |
| **Aktualizowanie domeny** |5 |3 |

Po utworzeniu zestawów dostępności, wróć do grupy zasobów w portalu Azure.

## <a name="create-domain-controllers"></a>Utworzyć kontrolerów domeny
Po utworzeniu sieci, podsieci, zestawów dostępności i równoważenia obciążenia internetowy, możesz przystąpić do tworzenia maszyn wirtualnych kontrolerów domeny.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Tworzenie maszyn wirtualnych kontrolerów domeny
Aby utworzyć i skonfigurować na kontrolerach domeny, wróć do **SQL-HA-zarządcy zasobów** grupy zasobów.

1. Kliknij pozycję **Add** (Dodaj). 
2. Typ **systemu Windows Server 2016 Datacenter**.
3. Kliknij przycisk **systemu Windows Server 2016 Datacenter**. W **systemu Windows Server Datacenter 2016**, sprawdź, czy model wdrażania **Resource Manager**, a następnie kliknij przycisk **Utwórz**. 

Powtórz poprzednie kroki, aby utworzyć dwie maszyny wirtualne. Nazwa dwóch maszyn wirtualnych:

* kontrolerów domeny podstawowej AD
* kontrolerów domeny pomocniczy AD

  > [!NOTE]
  > **Kontrolerów domeny pomocniczy ad** maszyny wirtualnej jest opcjonalne zapewnić wysoką dostępność usług domenowych w usłudze Active Directory.
  >
  >

W poniższej tabeli przedstawiono ustawienia dla tych dwóch maszyn:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |Pierwszy kontroler domeny: *kontrolerów domeny podstawowej ad*.</br>Drugi kontroler domeny *kontrolerów domeny pomocniczy ad*. |
| **Typ dysku maszyny wirtualnej** |SSD |
| **Nazwa użytkownika** |DomainAdmin |
| **Hasło** |Contoso! 0000 |
| **Subskrypcja** |*Twoja subskrypcja* |
| **Grupa zasobów** |SQL-HA-RG |
| **Lokalizacja** |*Lokalizacja* |
| **Rozmiar** |DS1_V2 |
| **Storage** | **Użyj zarządzanego dysków** - **tak** |
| **Sieć wirtualna** |autoHAVNET |
| **Podsieć** |administrator |
| **Publiczny adres IP** |*Tej samej nazwy co maszyna wirtualna* |
| **Sieciowa grupa zabezpieczeń** |*Tej samej nazwy co maszyna wirtualna* |
| **Zestaw dostępności** |adavailabilityset </br>**Odporność domen**: 2</br>**Aktualizowanie domeny**: 2|
| **Diagnostyka** |Enabled (Włączony) |
| **Konto magazynu diagnostyki** |*Automatycznie utworzone* |

   >[!IMPORTANT]
   >Maszyny Wirtualnej można umieścić tylko w dostępności po jego utworzeniu. Nie można zmienić dostępności ustawić po utworzeniu maszyny Wirtualnej. Zobacz [Zarządzaj dostępnością maszyn wirtualnych](../manage-availability.md).

Platforma Azure tworzy maszyny wirtualne.

Po utworzeniu maszyny wirtualnej, należy skonfigurować kontroler domeny.

### <a name="configure-the-domain-controller"></a>Konfiguracja kontrolera domeny
W poniższych krokach, należy skonfigurować **kontrolerów domeny podstawowej ad** komputera jako kontroler domeny corp.contoso.com.

1. W portalu, otwórz **SQL-HA-zarządcy zasobów** zasobu, grupy i wybierz **kontrolerów domeny podstawowej ad** maszyny. Na **kontrolerów domeny podstawowej ad**, kliknij przycisk **Connect** można otworzyć pliku RDP do dostępu do pulpitu zdalnego.

    ![Połącz z maszyną wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Zaloguj się przy użyciu konta administratora skonfigurowane (**\DomainAdmin**) i hasło (**Contoso! 0000**).
3. Domyślnie **Menedżera serwera** powinien zostać wyświetlony pulpit nawigacyjny.
4. Kliknij przycisk **Dodaj role i funkcje** łącza na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Wybierz **dalej** aż do **ról serwera** sekcji.
6. Wybierz **usług domenowych w usłudze Active Directory** i **serwera DNS** ról. Po wyświetleniu monitu, Dodaj wszelkie dodatkowe funkcje, które są wymagane przez tych ról.

   > [!NOTE]
   > System Windows wyświetli ostrzeżenie, że nie istnieje żaden adres IP. W przypadku testowania konfiguracji, kliknij przycisk **Kontynuuj**. W scenariuszach produkcji, należy ustawić adres IP statyczne w portalu Azure lub [ustawić statyczny adres IP komputera kontrolera domeny przy użyciu programu PowerShell](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Dodawanie ról okna dialogowego](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Kliknij przycisk **dalej** aż **potwierdzenie** sekcji. Wybierz **automatycznie ponownie uruchom serwer docelowy, jeśli jest to wymagane** pole wyboru.
8. Kliknij pozycję **Zainstaluj**.
9. Po funkcji zakończyć instalację, wróć do **Menedżera serwera** pulpitu nawigacyjnego.
10. Wybierz nową **usług AD DS** opcji w okienku po lewej stronie.
11. Kliknij przycisk **więcej** łącze na żółty pasek ostrzeżenie.

    ![Usługi AD DS z okna dialogowego na maszynie Wirtualnej z serwera DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. W **akcji** kolumny **wszystkie szczegóły zadania serwera** okna dialogowego, kliknij przycisk **podwyższyć ten serwer do poziomu kontrolera domeny**.
13. W **Kreator konfiguracji usług domenowych Active Directory**, użyj następujących wartości:

    | **Strona** | Ustawienie |
    | --- | --- |
    | **Konfiguracja wdrażania** |**Dodaj nowy las**<br/> **Nazwa domeny głównej** = corp.contoso.com |
    | **Opcje kontrolera domeny** |**Hasło trybu DSRM** = Contoso! 0000<br/>**Potwierdź hasło** = Contoso! 0000 |
14. Kliknij przycisk **dalej** przechodzić przez innych stron w kreatorze. Na **Sprawdzanie wymagań wstępnych** upewnij się, że zostanie wyświetlony następujący komunikat: **sprawdzanie wszystkich wymagań wstępnych zakończyło się pomyślnie**. Możesz przejrzeć wszystkie odpowiednie komunikaty ostrzegawcze, ale można kontynuować instalację.
15. Kliknij pozycję **Zainstaluj**. **Kontrolerów domeny podstawowej ad** maszyny wirtualnej zostanie automatycznie uruchomiony ponownie.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Należy pamiętać, adres IP podstawowego kontrolera domeny

Użyj podstawowego kontrolera domeny dla serwera DNS. Zanotuj adres IP kontrolera domeny głównej.

Jednym ze sposobów uzyskać adres IP kontrolera domeny jest za pośrednictwem portalu Azure.

1. W portalu Azure Otwórz grupę zasobów.

2. Kliknij przycisk z podstawowym kontrolerem domeny.

3. Na podstawowym kontrolerze domeny, kliknij przycisk **interfejsy sieciowe**.

![Interfejsy sieciowe](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Należy pamiętać, prywatnego adresu IP dla tego serwera.

### <a name="configure-the-virtual-network-dns"></a>Skonfiguruj sieć wirtualną DNS
Po utworzeniu pierwszego kontrolera domeny i włączyć na pierwszym serwerze DNS, należy skonfigurować wirtualnej sieci do używania tego serwera DNS.

1. W portalu Azure kliknij w sieci wirtualnej.

2. W obszarze **ustawienia**, kliknij przycisk **serwera DNS**.

3. Kliknij przycisk **niestandardowe**i wpisz prywatny adres IP podstawowego kontrolera domeny.

4. Kliknij pozycję **Zapisz**.

### <a name="configure-the-second-domain-controller"></a>Konfigurowanie kontrolera domeny
Po ponownym uruchomieniu podstawowego kontrolera domeny, można skonfigurować kontrolera domeny. Jest to krok opcjonalny wysokiej dostępności. Wykonaj następujące kroki, aby skonfigurować kontrolera domeny:

1. W portalu, otwórz **SQL-HA-zarządcy zasobów** zasobu, grupy i wybierz **kontrolerów domeny pomocniczy ad** maszyny. Na **kontrolerów domeny pomocniczy ad**, kliknij przycisk **Connect** można otworzyć pliku RDP do dostępu do pulpitu zdalnego.
2. Zaloguj się do maszyny Wirtualnej przy użyciu konta administratora skonfigurowane (**BUILTIN\DomainAdmin**) i hasło (**Contoso! 0000**).
3. Zmienianie adresu preferowanego serwera DNS na adres kontrolera domeny.
4. W **Centrum sieci i udostępniania**, kliknij przycisk interfejsu sieciowego.
   ![Interfejs sieciowy](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kliknij pozycję **Właściwości**.
6. Wybierz **protokół internetowy w wersji 4 (TCP/IPv4)** i kliknij przycisk **właściwości**.
7. Wybierz **Użyj następujących adresów serwerów DNS** i określenie adresu podstawowego kontrolera domeny w **preferowany serwer DNS**.
8. Kliknij przycisk **OK**, a następnie **Zamknij** aby zatwierdzić zmiany. Jesteś teraz maszyny Wirtualnej, aby przyłączyć się do **corp.contoso.com**.

   >[!IMPORTANT]
   >W przypadku utraty połączenia z pulpitem zdalnym po zmianie ustawienia DNS, przejdź do portalu Azure i uruchom ponownie maszynę wirtualną.

9. Korzystając z pulpitu zdalnego na kontrolerze domeny dodatkowej, otwórz **pulpitu nawigacyjnego Menedżera serwera**.
10. Kliknij przycisk **Dodaj role i funkcje** łącza na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Wybierz **dalej** aż do **ról serwera** sekcji.
12. Wybierz **usług domenowych w usłudze Active Directory** i **serwera DNS** ról. Po wyświetleniu monitu, Dodaj wszelkie dodatkowe funkcje, które są wymagane przez tych ról.
13. Po funkcji zakończyć instalację, wróć do **Menedżera serwera** pulpitu nawigacyjnego.
14. Wybierz nową **usług AD DS** opcji w okienku po lewej stronie.
15. Kliknij przycisk **więcej** łącze na żółty pasek ostrzeżenie.
16. W **akcji** kolumny **wszystkie szczegóły zadania serwera** okna dialogowego, kliknij przycisk **podwyższyć ten serwer do poziomu kontrolera domeny**.
17. W obszarze **konfiguracji wdrożenia**, wybierz pozycję **dodać kontroler domeny do istniejącej domeny**.
   ![Konfiguracja wdrażania](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kliknij pozycję **Wybierz**.
19. Połącz przy użyciu konta administratora (**CORP. CONTOSO.COM\domainadmin**) i hasło (**Contoso! 0000**).
20. W **wybierz domenę z lasu**, kliknij swoją domenę, a następnie kliknij przycisk **OK**.
21. W **opcje kontrolera domeny**, użyj wartości domyślnych i ustaw hasło DSRM.

   >[!NOTE]
   >**Opcje DNS** strony może ostrzeżenie, że nie można utworzyć delegowania dla tego serwera DNS. Można zignorować to ostrzeżenie w środowiskach nieprodukcyjnych.
22. Kliknij przycisk **dalej** do momentu osiągnięcia okna dialogowego **wymagania wstępne** Sprawdź. Następnie kliknij pozycję **Zainstaluj**.

Po zakończeniu zmiany konfiguracji, uruchom ponownie serwer.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Dodaj prywatny adres IP do kontrolera domeny na serwerze DNS sieci VPN

W portalu Azure w ramach sieci wirtualnej należy zmienić obejmują adres IP kontrolera domeny pomocniczy serwer DNS. To ustawienie umożliwia nadmiarowości usługi DNS.

### <a name=DomainAccounts></a> Konfigurowanie kont domeny

W następnych krokach konfigurowania kont usługi Active Directory. W poniższej tabeli przedstawiono kont:

| |Konto instalacji<br/> |sqlserver-0 <br/>Konto programu SQL Server i usługi agenta SQL |sqlserver-1<br/>Konto programu SQL Server i usługi agenta SQL
| --- | --- | --- | ---
|**Imię** |Instalowanie |SQLSvc1 | SQLSvc2
|**SamAccountName użytkownika** |Instalowanie |SQLSvc1 | SQLSvc2

Poniższe kroki umożliwiają utworzenie poszczególnych kont.

1. Zaloguj się do **kontrolerów domeny podstawowej ad** maszyny.
2. W **Menedżera serwera**, wybierz pozycję **narzędzia**, a następnie kliknij przycisk **Centrum administracyjne usługi Active Directory**.   
3. Wybierz **corp (lokalna)** w lewym okienku.
4. Po prawej stronie **zadania** okienku wybierz **nowy**, a następnie kliknij przycisk **użytkownika**.
   ![Centrum administracyjne usługi Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Ustaw złożone hasło dla poszczególnych kont.<br/> W środowiskach nieprodukcyjnych ustawić konta użytkownika nigdy nie wygasa.

5. Kliknij przycisk **OK** do utworzenia użytkownika.
6. Powtórz poprzednie kroki dla każdego z trzech kont.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Udzielanie uprawnień wymaganych do konta instalacji
1. W **Centrum administracyjne usługi Active Directory**, wybierz pozycję **corp (lokalna)** w okienku po lewej stronie. Następnie w prawej **zadania** okienku, kliknij przycisk **właściwości**.

    ![Właściwości użytkownika CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Wybierz **rozszerzenia**, a następnie kliknij przycisk **zaawansowane** znajdującego się na **zabezpieczeń** kartę.
3. W **Zaawansowane ustawienia zabezpieczeń dla corp** okna dialogowego, kliknij przycisk **Dodaj**.
4. Kliknij przycisk **Wybierz podmiot zabezpieczeń**, wyszukaj **CORP\Install**, a następnie kliknij przycisk **OK**.
5. Wybierz **odczytywanie wszystkich właściwości** pole wyboru.

6. Wybierz **Tworzenie obiektów komputerów** pole wyboru.

     ![Uprawnienia użytkownika Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kliknij przycisk **OK**, a następnie kliknij przycisk **OK** ponownie. Zamknij **corp** okna właściwości.

Teraz, po zakończeniu konfigurowania usługi Active Directory i obiektów użytkowników, Utwórz dwie maszyny wirtualne z serwera SQL i serwera monitora maszyny Wirtualnej. Następnie dołącz wszystkie trzy do domeny.

## <a name="create-sql-server-vms"></a>Tworzenie maszyn wirtualnych programu SQL Server

Utwórz trzy dodatkowe maszyny wirtualne. Rozwiązanie wymaga dwóch maszyn wirtualnych z wystąpień programu SQL Server. Trzeci maszyny wirtualnej będą działać jako monitor. Można użyć systemu Windows Server 2016 [chmury monitora](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), jednak zgodności ze starszymi systemami operacyjnymi niniejszym dokumencie użyto maszyny wirtualnej dla monitora.  

Przed kontynuowaniem należy wziąć pod uwagę następujące decyzje dotyczące projektu.

* **Magazyn — dyskach zarządzanych Azure**

   Do przechowywania maszyn wirtualnych dysków Azure zarządzanych. Firma Microsoft zaleca zarządzane dyski dla maszyn wirtualnych programu SQL Server. Usługa Managed Disks obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z usługą Managed Disks znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Managed Disks](../managed-disks-overview.md). Aby uzyskać szczegóły dotyczące dysków zarządzanych w zestawie dostępności, zobacz [używać dysków zarządzanych maszyn wirtualnych w zestawie dostępności](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Adresy IP prywatnej sieci — w środowisku produkcyjnym**

   W przypadku maszyn wirtualnych w tym samouczku korzysta z publicznych adresów IP. Publiczny adres IP umożliwia połączenia zdalnego bezpośrednio do maszyny wirtualnej przez internet — ułatwia czynności konfiguracyjne. W środowiskach produkcyjnych firma Microsoft zaleca tylko prywatnych adresów IP w celu zmniejszenia luk wystąpienia programu SQL Server zasobu maszyny Wirtualnej.

### <a name="create-and-configure-the-sql-server-vms"></a>Tworzenie i konfigurowanie maszyn wirtualnych serwera SQL
Następnie należy utworzyć trzy maszyny wirtualne — dwóch maszyn wirtualnych serwera SQL i maszyny Wirtualnej na dodatkowym węźle klastra. Do tworzenia poszczególnych maszyn wirtualnych, przejdź wstecz do **SQL-HA-zarządcy zasobów** grupy zasobów, kliknij przycisk **Dodaj**, Wyszukiwanie elementu galerii odpowiednie, kliknij przycisk **maszyny wirtualnej**, a następnie kliknij przycisk  **Z galerii**. Użyj informacji w poniższej tabeli ułatwiają tworzenie maszyn wirtualnych:


| Strona | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Wybierz element galerii odpowiednie |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise w systemie Windows Server 2016** |**SQL Server 2016 SP1 Enterprise w systemie Windows Server 2016** |
| Konfiguracja maszyny wirtualnej **podstawy** |**Nazwa** = fsw klastra<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = Contoso! 0000<br/>**Subskrypcja** = subskrypcji<br/>**Grupa zasobów** = SQL-HA-zarządcy zasobów<br/>**Lokalizacja** = Twojej lokalizacji platformy azure |**Name** = sqlserver-0<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = Contoso! 0000<br/>**Subskrypcja** = subskrypcji<br/>**Grupa zasobów** = SQL-HA-zarządcy zasobów<br/>**Lokalizacja** = Twojej lokalizacji platformy azure |**Nazwa** sqlserver-1<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = Contoso! 0000<br/>**Subskrypcja** = subskrypcji<br/>**Grupa zasobów** = SQL-HA-zarządcy zasobów<br/>**Lokalizacja** = Twojej lokalizacji platformy azure |
| Konfiguracja maszyny wirtualnej **rozmiaru** |**ROZMIAR** = DS1\_V2 (1 vCPU, 3.5 GB) |**ROZMIAR** = DS2\_V2 (2 Vcpu, 7 GB)</br>Rozmiar musi obsługiwać magazynu SSD (obsługi dysków Premium. )) |**ROZMIAR** = DS2\_V2 (2 Vcpu, 7 GB) |
| Konfiguracja maszyny wirtualnej **ustawienia** |**Magazyn**: Użyj zarządzanego dysków.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** wygenerowanej automatycznie.<br/>**Grupy zabezpieczeń sieci** = Brak<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj konta usługi storage automatycznie generowanych<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |**Magazyn**: Użyj zarządzanego dysków.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** wygenerowanej automatycznie.<br/>**Grupy zabezpieczeń sieci** = Brak<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj konta usługi storage automatycznie generowanych<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |**Magazyn**: Użyj zarządzanego dysków.<br/>**Sieć wirtualna** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Publiczny adres IP** wygenerowanej automatycznie.<br/>**Grupy zabezpieczeń sieci** = Brak<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj konta usługi storage automatycznie generowanych<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |
| Konfiguracja maszyny wirtualnej **ustawienia programu SQL Server** |Nie dotyczy |**Łączność z serwerem SQL** = Private (w ramach sieci wirtualnej)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = wyłączone<br/>**Konfiguracja magazynu** = ogólne<br/>**Automatyczne stosowanie poprawek** = niedziela, 2:00<br/>**Automatyczne kopie zapasowe** = wyłączone</br>**Integracja magazynu kluczy Azure** = wyłączone |**Łączność z serwerem SQL** = Private (w ramach sieci wirtualnej)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = wyłączone<br/>**Konfiguracja magazynu** = ogólne<br/>**Automatyczne stosowanie poprawek** = niedziela, 2:00<br/>**Automatyczne kopie zapasowe** = wyłączone</br>**Integracja magazynu kluczy Azure** = wyłączone |

<br/>

> [!NOTE]
> Rozmiary sugerowane tutaj są przeznaczone do testowania grup dostępności w maszynach wirtualnych platformy Azure. Aby uzyskać najlepszą wydajność w przypadku obciążeń produkcyjnych, zobacz zalecenia dotyczące programu SQL Server rozmiary i konfiguracji [wydajności najlepsze rozwiązania dotyczące programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Po trzy maszyny wirtualne są w pełni zaaprowizowanym, należy połączyć je **corp.contoso.com** domeny i przyznać prawa administracyjne CORP\Install maszyn.

### <a name="joinDomain"></a>Przyłącz serwer do domeny

Teraz możesz dołączyć maszyn wirtualnych do **corp.contoso.com**. Dla maszyn wirtualnych serwera SQL i serwera monitora udziału plików, wykonaj następujące czynności:

1. Zdalne połączenia z maszyną wirtualną o **BUILTIN\DomainAdmin**.
2. W **Menedżera serwera**, kliknij przycisk **lokalnego serwera**.
3. Kliknij przycisk **grupy roboczej** łącza.
4. W **nazwy komputera** kliknij **zmiany**.
5. Wybierz **domeny** pole wyboru i wpisz **corp.contoso.com** w polu tekstowym. Kliknij przycisk **OK**.
6. W **zabezpieczenia systemu Windows** okno podręczne, podaj poświadczenia dla konta administratora domeny domyślnej (**CORP\DomainAdmin**) i hasło (**Contoso! 0000**) .
7. Gdy zostanie wyświetlony komunikat "Zapraszamy do domeny corp.contoso.com", kliknij przycisk **OK**.
8. Kliknij przycisk **Zamknij**, a następnie kliknij przycisk **Uruchom ponownie teraz** w oknie podręcznym.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Dodaj użytkownika Corp\Install jako administrator na każdym klastrze maszyny Wirtualnej

Po uruchomieniu każdej maszyny wirtualnej jest członkiem domeny, Dodaj **CORP\Install** jako członek lokalnej grupy administratorów.

1. Poczekaj, aż do ponownego uruchomienia maszyny Wirtualnej, a następnie uruchom ponownie z podstawowego kontrolera domeny do logowania się w pliku RDP **sqlserver 0** za pomocą **CORP\DomainAdmin** konta.
   >[!TIP]
   >Upewnij się, że zalogujesz się przy użyciu konta administratora domeny. W poprzednich krokach zostały przy użyciu konta administratora w SKOMPILOWANY. Teraz, że serwer jest w domenie, należy użyć konta domeny. W sesji protokołu RDP, określ *domeny*\\*username*.

2. W **Menedżera serwera**, wybierz pozycję **narzędzia**, a następnie kliknij przycisk **Zarządzanie komputerem**.
3. W **Zarządzanie komputerem** okna, rozwiń węzeł **lokalnych użytkowników i grup**, a następnie wybierz **grup**.
4. Kliknij dwukrotnie **Administratorzy** grupy.
5. W **właściwości Administratorzy** okna dialogowego, kliknij przycisk **Dodaj** przycisku.
6. Wprowadź nazwę danego użytkownika **CORP\Install**, a następnie kliknij przycisk **OK**.
7. Kliknij przycisk **OK** zamknąć **właściwości administratora** okna dialogowego.
8. Powtórz poprzednie kroki na **sqlserver 1** i **fsw klastra**.

### <a name="setServiceAccount"></a>Ustawianie konta usługi programu SQL Server

Na każdej maszynie Wirtualnej serwera SQL ustawić konto usługi programu SQL Server. Użyj konta, które zostały utworzone podczas obliczania możesz [skonfigurowane konta domeny](#DomainAccounts).

1. Otwórz **programu SQL Server Configuration Manager**.
2. Kliknij prawym przyciskiem myszy usługę programu SQL Server, a następnie kliknij przycisk **właściwości**.
3. Ustaw nazwę konta i hasło.
4. Powtórz te kroki dla innych maszyn wirtualnych serwera SQL.  

Dla grup dostępności programu SQL Server każda z maszyn wirtualnych serwera SQL musi być uruchamiane jako konto domeny.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Utwórz logowanie na każdej maszynie Wirtualnej serwera SQL dla konta instalacji

Aby skonfigurować grupę dostępności, należy użyć konta instalacji (CORP\install). To konto musi być członkiem **sysadmin** stałej roli serwera na każdej maszynie Wirtualnej serwera SQL. Poniższe kroki tworzenia logowanie dla konta instalacji:

1. Połączyć się z serwerem za pośrednictwem protokołu RDP (Remote Desktop) przy użyciu  *\<MachineName\>\DomainAdmin* konta.

1. Otwórz program SQL Server Management Studio i Połącz z lokalnym wystąpieniem programu SQL Server.

1. W **Eksplorator obiektów**, kliknij przycisk **zabezpieczeń**.

1. Kliknij prawym przyciskiem myszy **logowania**. Kliknij przycisk **nowe dane logowania**.

1. W **nowe dane logowania -**, kliknij przycisk **wyszukiwania**.

1. Kliknij przycisk **lokalizacje**.

1. Wprowadź poświadczenia domeny administratora sieci.

1. Użyj konta instalacji.

1. Ustaw Logowanie jako członek **sysadmin** stałej roli serwera.

1. Kliknij przycisk **OK**.

Powtórz te czynności na innych maszyn wirtualnych serwera SQL.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Dodawanie funkcji klastra trybu Failover na obu maszynach wirtualnych serwera SQL

Aby dodać funkcje klaster trybu Failover, wykonaj następujące czynności na obu maszynach wirtualnych serwera SQL:

1. Połączenie z maszyną wirtualną programu SQL Server za pośrednictwem protokołu RDP (Remote Desktop) przy użyciu *CORP\install* konta. Otwórz **pulpitu nawigacyjnego Menedżera serwera**.
2. Kliknij przycisk **Dodaj role i funkcje** łącza na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Wybierz **dalej** aż do **funkcji serwera** sekcji.
4. W **funkcje**, wybierz pozycję **klaster pracy awaryjnej**.
5. Dodaj wszelkie dodatkowe funkcje wymagane.
6. Kliknij przycisk **zainstalować** Aby dodać funkcje.

Powtórz kroki na inne maszyny Wirtualnej serwera SQL.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Skonfigurować zaporę na każdej maszynie Wirtualnej serwera SQL

Rozwiązanie wymaga następujące porty TCP, aby być otwarty w zaporze:

- **Program SQL Server wirtualna**:<br/>
   Port 1433 dla domyślnego wystąpienia programu SQL Server.
- **Sondę modułu równoważenia obciążenia Azure:**<br/>
   Dowolny dostępny port. Przykłady często używają 59999.
- **Punktu końcowego dublowania bazy danych:** <br/>
   Dowolny dostępny port. Przykłady często używają 5022.

Porty zapory muszą być otwarte na obu maszynach wirtualnych serwera SQL.

Metoda otwarcia portów zależy od zaporę, którego używasz. W następnej sekcji objaśniono sposób otwierania portów w Zaporze systemu Windows. Otwórz wymagane porty dla wszystkich maszyn wirtualnych programu SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otwórz TCP port w zaporze

1. Na pierwszym serwerze SQL **Start** ekranu, uruchom **Zapora systemu Windows z zabezpieczeniami zaawansowanymi**.
2. W lewym okienku wybierz **reguły ruchu przychodzącego**. W okienku po prawej stronie, kliknij polecenie **nową regułę**.
3. Aby uzyskać **typ reguły**, wybierz **portu**.
4. Dla portu, określ **TCP** i wpisz numery odpowiedni port. Zobacz poniższy przykład:

   ![Zapory SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kliknij przycisk **Dalej**.
6. Na **akcji** Zachowaj **zezwalały na połączenie** zaznaczone, a następnie kliknij przycisk **dalej**.
7. Na **profilu** Zaakceptuj ustawienia domyślne, a następnie kliknij pozycję **dalej**.
8. Na **nazwa** Określ nazwę reguły (takich jak **Azure LB sondowania**) w **nazwa** polu tekstowym, a następnie kliknij przycisk **Zakończ**.

Powtórz te kroki dla drugiej maszyny Wirtualnej programu SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurowanie uprawnień konta systemu

Aby utworzyć konta dla konta system i udzielić odpowiednich uprawnień, wykonaj następujące kroki na każde wystąpienie programu SQL Server:

1. Tworzenie konta usługi dla `[NT AUTHORITY\SYSTEM]` na każde wystąpienie programu SQL Server. Poniższy skrypt tworzy tego konta:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Przyznaj następujące uprawnienia do `[NT AUTHORITY\SYSTEM]` na każde wystąpienie programu SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Poniższy skrypt następujące uprawnienia:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Kolejne kroki

* [Utwórz grupę dostępności programu SQL Server AlwaysOn na maszynach wirtualnych Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

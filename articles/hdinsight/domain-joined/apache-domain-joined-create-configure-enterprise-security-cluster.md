---
title: Tworzenie, konfigurowanie klastrów pakietów zabezpieczeń przedsiębiorstwa — Azure
description: Dowiedz się, jak tworzyć i konfigurować klastry pakietów zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436031"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Tworzenie i konfigurowanie klastrów pakietów zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight

Pakiet zabezpieczeń przedsiębiorstwa (ESP) dla usługi Azure HDInsight zapewnia dostęp do uwierzytelniania opartego na usłudze Active Directory, obsługi wielu użytkowniczu i kontroli dostępu opartej na rolach dla klastrów Apache Hadoop na platformie Azure. Klastry ESP usługi HDInsight umożliwiają organizacjom przestrzegaącym ścisłych zasad zabezpieczeń firmy bezpieczne przetwarzanie poufnych danych.

W tym przewodniku pokazano, jak utworzyć klaster usługi Azure HDInsight z obsługą platformy ESP. Pokazano również, jak utworzyć maszynę wirtualną IaaS systemu Windows, na której włączono usługę Active Directory i system nazw domen (DNS). Ten przewodnik służy do konfigurowania niezbędnych zasobów, aby umożliwić użytkownikom lokalnym logowanie się do klastra HDInsight z włączoną funkcją ESP.

Utworzony serwer będzie zastępować *rzeczywiste* środowisko lokalne. Użyjesz go do czynności podczas czynności konfiguracyjnych i konfiguracyjnych. Później powtórzysz kroki we własnym środowisku.

Ten przewodnik pomoże również utworzyć środowisko tożsamości hybrydowej przy użyciu synchronizacji skrótów haseł z usługą Azure Active Directory (Azure AD). Przewodnik uzupełnia [Użyj ESP w HDInsight](apache-domain-joined-architecture.md).

Przed użyciem tego procesu we własnym środowisku:

* Konfigurowanie usługi Active Directory i dns.
* Włącz usługę Azure AD.
* Synchronizowanie lokalnych kont użytkowników z usługą Azure AD.

![Diagram architektury usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Tworzenie środowiska lokalnego

W tej sekcji użyjesz szablonu wdrażania przewodnika Szybki start platformy Azure, aby utworzyć nowe maszyny wirtualne, skonfigurować system DNS i dodać nowy las usługi Active Directory.

1. Przejdź do szablonu wdrażania przewodnika Szybki start, aby [utworzyć maszynę wirtualną platformy Azure z nowym lasem usługi Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Wybierz **pozycję Wdrażanie na platformie Azure**.
1. Zaloguj się do subskrypcji platformy Azure.
1. Na stronie **Tworzenie maszyny Wirtualnej platformy Azure z nowym lasem usługi AD** podaj następujące informacje:

    |Właściwość | Wartość |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję, w której chcesz wdrożyć zasoby.|
    |Grupa zasobów|Wybierz **pozycję Utwórz nowy**i wprowadź nazwę`OnPremADVRG`|
    |Lokalizacja|Wybierz lokalizację.|
    |Nazwa użytkownika administratora|`HDIFabrikamAdmin`|
    |Hasło administratora|Wprowadź hasło.|
    |Nazwa domeny|`HDIFabrikam.com`|
    |Prefiks dns|`hdifabrikam`|

    Pozostaw pozostałe wartości domyślne.

    ![Szablon tworzenia maszyny wirtualnej platformy Azure przy nowym lesie usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Zapoznaj się z **Regulaminem,** a następnie wybierz opcję **Zgadzam się z warunkami określonymi powyżej.**
1. Wybierz **pozycję Zakup**i monitoruj wdrożenie i poczekaj na jego zakończenie. Wdrożenie trwa około 30 minut.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurowanie użytkowników i grup dostępu do klastra

W tej sekcji utworzysz użytkowników, którzy będą mieli dostęp do klastra HDInsight do końca tego przewodnika.

1. Połącz się z kontrolerem domeny przy użyciu pulpitu zdalnego.
    1. W witrynie Azure portal przejdź do **sekcji** > Grupy zasobów**OnPremADVRG** > **adVM** > **Connect**.
    1. Z listy rozwijanej **Adres IP** wybierz publiczny adres IP.
    1. Wybierz **pozycję Pobierz plik RDP**, a następnie otwórz plik.
    1. Użyj `HDIFabrikam\HDIFabrikamAdmin` jako nazwy użytkownika.
    1. Wprowadź hasło wybrane dla konta administratora.
    1. Kliknij przycisk **OK**.

1. Z pulpitu nawigacyjnego **Menedżera serwera** kontrolera domeny przejdź do **pozycji Użytkownicy** > i komputery usługi Tools**Active Directory**.

    ![Na pulpicie nawigacyjnym Menedżera serwera otwórz usługę Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Utwórz dwóch nowych użytkowników: **HDIAdmin** i **HDIUser**. Ci dwaj użytkownicy zalogują się do klastrów HDInsight.

    1. Na stronie **Użytkownicy i komputery usługi Active Directory** kliknij prawym przyciskiem `HDIFabrikam.com`myszy , a następnie przejdź do **pozycji Nowy** > **użytkownik**.

        ![Tworzenie nowego użytkownika usługi Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na stronie Nowy obiekt — `HDIUser` **Użytkownik** wprowadź pozycję **Imię** i nazwa **logowania użytkownika**. Pozostałe pola będą automatycznie wypełniane. Następnie wybierz **przycisk Dalej**.

        ![Tworzenie pierwszego obiektu użytkownika administratora](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. W wyświetlonym wyskakującym oknie wprowadź hasło do nowego konta. Wybierz **opcję Hasło nigdy nie wygaśnie**, a następnie **ok** w wyskakującym okienku.
    1. Wybierz **pozycję Dalej**, a następnie **zakończ,** aby utworzyć nowe konto.
    1. Powtórz powyższe kroki, `HDIAdmin`aby utworzyć użytkownika .

        ![Tworzenie drugiego obiektu użytkownika administratora](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Tworzenie globalnej grupy zabezpieczeń.

    1. Z **pozycji Użytkownicy i komputery usługi Active Directory**kliknij prawym przyciskiem myszy `HDIFabrikam.com`, a następnie przejdź do pozycji **Nowa** > **grupa**.

    1. Wprowadź `HDIUserGroup` w polu tekstowym **Nazwa grupy.**

    1. Kliknij przycisk **OK**.

    ![Tworzenie nowej grupy usługi Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Tworzenie nowego obiektu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Dodaj członków do **grupy HDIUserGroup**.

    1. Kliknij prawym przyciskiem myszy **hdiuser** i wybierz pozycję **Dodaj do grupy...**.
    1. W polu **Wprowadź nazwy obiektów, aby zaznaczyć** tekst, wprowadź . `HDIUserGroup` Następnie wybierz **przycisk OK**i **PONOWNIE OK** w wyskakującym okienku.
    1. Powtórz poprzednie kroki dla konta **HDIAdmin.**

        ![Dodawanie elementu członkowskiego HDIUser do grupy HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Utworzono środowisko usługi Active Directory. Dodano dwóch użytkowników i grupę użytkowników, która może uzyskać dostęp do klastra HDInsight.

Użytkownicy zostaną zsynchronizowani z usługą Azure AD.

### <a name="create-an-azure-ad-directory"></a>Tworzenie katalogu usługi Azure AD

1. Zaloguj się do Portalu Azure.
1. Wybierz **pozycję Utwórz zasób** i wpisz `directory`. Wybierz **pozycję Azure Active Directory** > **Create**.
1. W obszarze Nazwa `HDIFabrikam` **organizacji**wpisz .
1. W **obszarze Początkowa nazwa domeny**wprowadź `HDIFabrikamoutlook`.
1. Wybierz **pozycję Utwórz**.

    ![Tworzenie katalogu usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Tworzenie domeny niestandardowej

1. Z nowej **usługi Azure Active Directory**w obszarze **Zarządzanie**wybierz pozycję **Niestandardowe nazwy domen**.
1. Wybierz **+ Dodaj domenę niestandardową**.
1. W **obszarze Niestandardowa nazwa domeny**wprowadź pozycję Dodaj `HDIFabrikam.com`domenę , a następnie pozycję Dodaj **domenę**.
1. Następnie uzupełnij [Dodaj informacje DNS do rejestratora domen](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Tworzenie domeny niestandardowej](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Tworzenie grupy

1. Z nowej **usługi Azure Active Directory**w obszarze **Zarządzanie**wybierz pozycję **Grupy**.
1. Wybierz **+ Nowa grupa**.
1. W polu tekstowym nazwa `AAD DC Administrators` **grupy** wprowadź .
1. Wybierz **pozycję Utwórz**.

## <a name="configure-your-azure-ad-tenant"></a>Konfigurowanie dzierżawy usługi Azure AD

Teraz skonfigurujesz dzierżawę usługi Azure AD, aby można było synchronizować użytkowników i grupy z lokalnego wystąpienia usługi Active Directory do chmury.

Utwórz administratora dzierżawy usługi Active Directory.

1. Zaloguj się do witryny Azure portal i wybierz dzierżawę usługi Azure AD, **HDIFabrikam**.

1. Przejdź do **pozycji Zarządzaj** > **użytkownikami** > **Nowy użytkownik**.

1. Wprowadź następujące szczegóły dla nowego użytkownika:

    **Tożsamość**

    |Właściwość |Opis |
    |---|---|
    |Nazwa użytkownika|Wprowadź `fabrikamazureadmin` w polu tekstowym. Z listy rozwijanej nazwa domeny wybierz pozycję`hdifabrikam.com`|
    |Nazwa| Wprowadź polecenie `fabrikamazureadmin`.|

    **Hasło**
    1. Wybierz **pozycję Pozwól mi utworzyć hasło**.
    1. Wprowadź wybrane bezpieczne hasło.

    **Grupy i role**
    1. Wybierz **0 wybranych grup**.
    1. Wybierz **pozycję Administratorzy kontrolera domeny AAD**, a następnie **wybierz opcję .**

    ![Okno dialogowe Grupy usług Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Wybierz **user**.
    1. Wybierz **pozycję Administrator globalny**, a następnie wybierz **pozycję**.

    ![Okno dialogowe roli usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Wybierz **pozycję Utwórz**.

1. Następnie nowy użytkownik zaloguj się do witryny Azure portal, gdzie zostanie wyświetlony monit o zmianę hasła. Należy to zrobić przed skonfigurowaniem usługi Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizowanie użytkowników lokalnych z usługą Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Konfigurowanie połączenia usługi Microsoft Azure Active Directory

1. Pobierz z kontrolera domeny [usługi Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Otwórz pobrany plik wykonywalny i zaakceptuj postanowienia licencyjne. Wybierz przycisk **Kontynuuj**.

1. Wybierz **pozycję Użyj ustawień ekspresowych**.

1. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło administratora globalnego usługi Azure AD. Użyj nazwy `fabrikamazureadmin@hdifabrikam.com` użytkownika utworzonej podczas konfigurowania dzierżawy usługi Active Directory. Następnie wybierz **przycisk Dalej**.

    ![Strona "Połącz się z usługą Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na stronie **Łączenie się z Usługami domenowymi Active Directory** wprowadź nazwę użytkownika i hasło konta administratora przedsiębiorstwa. Użyj nazwy `HDIFabrikam\HDIFabrikamAdmin` użytkownika i jej hasła, które zostały utworzone wcześniej. Następnie wybierz **przycisk Dalej**.

   ![Strona "Połącz się z usługą Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na stronie **konfiguracji logowania usługi Azure AD** wybierz pozycję **Dalej**.
   ![Strona "Konfiguracja logowania usługi Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Na stronie **Gotowe do skonfigurowania** wybierz pozycję **Zainstaluj**.

   ![Strona "Gotowe do skonfigurowania"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na stronie **Konfiguracja zakończona** wybierz pozycję **Zakończ**.
   ![Strona "Konfiguracja zakończona"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Po zakończeniu synchronizacji upewnij się, że użytkownicy utworzyli w katalogu IaaS są synchronizowane z usługą Azure AD.
   1. Zaloguj się do Portalu Azure.
   1. Wybierz pozycję Użytkownicy**hdifabrikam** >  **usługi Azure Active Directory** > **.**

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz przypisaną przez użytkownika tożsamość zarządzaną, której można użyć do skonfigurowania usług domenowych usługi Azure AD Domain Services (Azure AD DS). Aby uzyskać więcej informacji, zobacz [Tworzenie, listy, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika przy użyciu portalu Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Zaloguj się do Portalu Azure.
1. Wybierz **pozycję Utwórz zasób** i wpisz `managed identity`. Wybierz pozycję Tworzenie tożsamości >  **zarządzanej przypisanej przez użytkownika****.**
1. W przypadku nazwy `HDIFabrikamManagedIdentity` **zasobu**wprowadź .
1. Wybierz subskrypcję.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy** i wprowadź `HDIFabrikam-CentralUS`.
1. W obszarze **Lokalizacja**wybierz pozycję **Central US**.
1. Wybierz **pozycję Utwórz**.

![Tworzenie nowej tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Włączanie usług Azure AD DS

Wykonaj następujące kroki, aby włączyć usługi Azure AD DS. Aby uzyskać więcej informacji, zobacz [Włączanie usług Azure AD DS przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Utwórz sieć wirtualną do obsługi usług Azure AD DS. Uruchom następujący kod programu PowerShell.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Zaloguj się do Portalu Azure.
1. Wybierz **pozycję Utwórz zasób**, wprowadź `Domain services`i wybierz pozycję Azure AD Domain **Services** > **Create**.
1. Na stronie **Podstawy:**
    1. W obszarze **Nazwa katalogu**wybierz utworzony katalog usługi Azure AD: **HDIFabrikam**.
    1. W przypadku **nazwy domeny DNS**wprowadź *HDIFabrikam.com*.
    1. Wybierz subskrypcję.
    1. Określ grupę zasobów **HDIFabrikam-CentralUS**. W obszarze **Lokalizacja**wybierz pozycję **Central US**.

        ![Podstawowe informacje o usługach Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na stronie **Sieć** wybierz sieć **(HDIFabrikam-VNET)** i podsieć **(AADDS-podsieć)** utworzoną przy użyciu skryptu programu PowerShell. Możesz też wybrać **pozycję Utwórz nową,** aby utworzyć sieć wirtualną teraz.

    ![Krok "Tworzenie sieci wirtualnej"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na stronie **Grupa Administrator** powinien zostać wyświetlone powiadomienie, że grupa o nazwie **Administratorzy kontrolera domeny usługi AAD** została już utworzona w celu administrowania tą grupą. Można zmodyfikować członkostwo tej grupy, jeśli chcesz, ale w tym przypadku nie trzeba go zmieniać. Kliknij przycisk **OK**.

    ![Wyświetlanie grupy administratorów usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na stronie **Synchronizacja** włącz pełną synchronizację, wybierając pozycję **Wszystko** > **OK**.

    ![Włączanie synchronizacji usług Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na stronie **Podsumowanie** sprawdź szczegóły dotyczące usług Azure AD DS i wybierz przycisk **OK**.

    ![Podsumowanie "Włącz usługi domenowe usługi azure ad"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Po włączeniu usług Azure AD DS lokalny serwer DNS jest uruchamiany na maszynach wirtualnych usługi Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurowanie sieci wirtualnej usług Ad DS

Aby skonfigurować sieć wirtualną usług Azure AD DS **(HDIFabrikam-AADDSVNET),** należy wykonać następujące kroki do używania niestandardowych serwerów DNS.

1. Znajdź adresy IP niestandardowych serwerów DNS.
    1. Wybierz `HDIFabrikam.com` zasób usług Ad DS platformy Azure.
    1. W obszarze **Zarządzanie**wybierz pozycję **Właściwości**.
    1. Znajdź adresy IP pod **adresem IP w sieci wirtualnej**.

    ![Lokalizowanie niestandardowych adresów IP DNS dla usług Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Skonfiguruj **HDIFabrikam-AADDSVNET** do używania niestandardowych adresów IP 10.0.0.4 i 10.0.0.5.

    1. W obszarze **Ustawienia**wybierz pozycję **Serwery DNS**.
    1. wybierz pozycję **Niestandardowe**.
    1. W polu tekstowym wprowadź pierwszy adres IP (*10.0.0.4*).
    1. Wybierz **pozycję Zapisz**.
    1. Powtórz kroki, aby dodać inny adres IP (*10.0.0.5*).

W naszym scenariuszu skonfigurowaliśmy usługi Azure AD DS do używania adresów IP 10.0.0.4 i 10.0.0.5, ustawiając ten sam adres IP w sieci wirtualnej usług Ad DS platformy Azure:

![Strona niestandardowych serwerów DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Zabezpieczanie ruchu LDAP

LDAP (Lightweight Directory Access Protocol) służy do odczytu i zapisu w usłudze Azure Active Directory. Ruch LDAP można poufć i zabezpieczyć za pomocą technologii Secure Sockets Layer (SSL) lub Transport Layer Security (TLS). Usługę LDAP można włączyć za pomocą protokołu SSL (LDAPS), instalując poprawnie sformatowany certyfikat.

Aby uzyskać więcej informacji na temat bezpiecznego protokołu LDAP, zobacz [Konfigurowanie protokołu LDAPS dla domeny zarządzanej usług Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

W tej sekcji utworzysz certyfikat z podpisem własnym, pobierz certyfikat i skonfigurujesz usługę LDAPS dla domeny zarządzanej usługi AZURE AD **DS.**

Poniższy skrypt tworzy certyfikat dla **HDIFabrikam**. Certyfikat jest zapisywany w *localmachine* ścieżki.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Do utworzenia żądania certyfikatu SSL można użyć dowolnego \#narzędzia lub aplikacji, która tworzy prawidłowe żądanie PKCS (Public Key Cryptography Standards) 10.

Sprawdź, czy certyfikat jest zainstalowany w magazynie **osobistym** komputera:

1. Uruchom program Microsoft Management Console (MMC).
1. Dodaj przystawkę **Certyfikaty,** która zarządza certyfikatami na komputerze lokalnym.
1. Rozwiń **certyfikaty (komputery lokalne)** > **certyfikaty****osobiste** > . Nowy certyfikat powinien istnieć w magazynie **osobistym.** Ten certyfikat jest wystawiany w pełni kwalifikowanej nazwie hosta.

    ![Weryfikowanie tworzenia certyfikatów lokalnych](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. W okienku po prawej stronie kliknij prawym przyciskiem myszy utworzony certyfikat. Wskaż pozycję **Wszystkie zadania**, a następnie wybierz pozycję **Eksportuj**.

1. Na stronie **Eksportowanie klucza prywatnego** wybierz pozycję **Tak, eksportuj klucz prywatny**. Komputer, na którym zostanie zaimportowany klucz, potrzebuje klucza prywatnego do odczytu zaszyfrowanych wiadomości.

    ![Strona Eksportuj klucz prywatny Kreatora eksportu certyfikatów](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na stronie **Eksportuj format pliku** pozostaw ustawienia domyślne, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Hasło** wpisz hasło klucza prywatnego. W przypadku **szyfrowania**wybierz **tripledes-SHA1**. Następnie wybierz **przycisk Dalej**.
1. Na stronie **Plik do eksportu** wpisz ścieżkę i nazwę eksportowanego pliku certyfikatu, a następnie wybierz pozycję **Dalej**. Nazwa pliku musi mieć rozszerzenie .pfx. Ten plik jest skonfigurowany w witrynie Azure portal w celu ustanowienia bezpiecznego połączenia.
1. Włącz usługę LDAPS dla domeny zarządzanej usług Azure AD DS.
    1. W witrynie Azure portal `HDIFabrikam.com`wybierz domenę .
    1. W obszarze **Zarządzanie**wybierz **pozycję Secure LDAP**.
    1. Na stronie **Bezpieczne LDAP** w obszarze **Bezpieczne LDAP**wybierz pozycję **Włącz**.
    1. Wyszukaj plik certyfikatu .pfx wyeksportowany na komputerze.
    1. Wprowadź hasło certyfikatu.

    ![Włącz bezpieczną usługę LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Po włączeniu LDAPS upewnij się, że jest on osiągalny, włączając port 636.
    1. W grupie zasobów **HDIFabrikam-CentralUS** wybierz grupę zabezpieczeń sieci **AADDS-HDIFabrikam.com-NSG**.
    1. W obszarze **Ustawienia**wybierz pozycję **Przychodzące reguły** > zabezpieczeń**Dodaj**.
    1. Na stronie **Dodawanie reguł zabezpieczeń przychodzących** wprowadź następujące właściwości i wybierz pozycję **Dodaj:**

        | Właściwość | Wartość |
        |---|---|
        | Element źródłowy | Dowolne |
        | Zakresy portów źródłowych | * |
        | Element docelowy | Dowolne |
        | Zakres portów docelowych | 636 |
        | Protocol (Protokół) | Dowolne |
        | Akcja | Zezwalaj |
        | Priorytet | \<Żądana liczba> |
        | Nazwa | Port_LDAP_636 |

    ![Okno dialogowe "Dodawanie reguły zabezpieczeń przychodzących"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** jest tożsamością zarządzaną przypisaną przez użytkownika. Rola współautora usług domenowych HDInsight jest włączona dla tożsamości zarządzanej, która umożliwia tej tożsamości odczytywanie, tworzenie, modyfikowanie i usuwanie operacji usług domenowych.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Tworzenie klastra HDInsight z obsługą ESP

Ten krok wymaga następujących wymagań wstępnych:

1. Utwórz nową grupę zasobów *HDIFabrikam-WestUS* w lokalizacji **Zachodnie stany USA**.
1. Utwórz sieć wirtualną, która będzie obsługiwać klaster HDInsight z włączoną funkcją ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Utwórz relację równorzędną między`HDIFabrikam-AADDSVNET`siecią wirtualną obsługującą usługi Azure AD DS`HDIFabrikam-HDIVNet`( ) a siecią wirtualną, która będzie obsługiwać klaster HDInsight z włączoną funkcją ESP ( ). Użyj następującego kodu programu PowerShell, aby równorzędować dwie sieci wirtualne.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Utwórz nowe konto usługi Azure Data Lake Storage Gen2 o nazwie **Hdigen2store**. Skonfiguruj konto przy obliczu tożsamości zarządzanej przez użytkownika **HDIFabrikamManagedIdentity**. Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight.](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

1. Skonfiguruj niestandardowy system DNS w sieci wirtualnej **HDIFabrikam-AADDSVNET.**
    1. Przejdź do witryny Azure portal > **grupy** > zasobów**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **serwerów DNS**.
    1. Wybierz **Opcję Niestandardowa** i wprowadź *10.0.0.4* i *10.0.0.5*.
    1. Wybierz **pozycję Zapisz**.

        ![Zapisywanie niestandardowych ustawień DNS dla sieci wirtualnej](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Utwórz nowy klaster platformy ISkry HDInsight z obsługą ESP.
    1. Wybierz **opcję Niestandardowe (rozmiar, ustawienia, aplikacje)**.
    1. Wprowadź szczegóły dotyczące **podstaw** (sekcja 1). Upewnij się, że **typem klastra** jest **Spark 2.3 (HDI 3.6)**. Upewnij się, że **grupa Zasobów** to **HDIFabrikam-CentralUS**.

    1. W przypadku **aplikacji Security + networking** (sekcja 2) wprowadź następujące szczegóły:
        * W obszarze **Pakiet zabezpieczeń przedsiębiorstwa**wybierz pozycję **Włączone**.
        * Wybierz **pozycję Administrator klastra** i wybierz konto **HDIAdmin** utworzone jako lokalny użytkownik administratora. Kliknij **pozycję Wybierz**.
        * Wybierz **grupę** > dostępu klastra**HDIUserGroup**. Każdy użytkownik, który zostanie dodany do tej grupy w przyszłości, będzie mógł uzyskać dostęp do klastrów HDInsight.

            ![Wybieranie grupy dostępu do klastra HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Wykonaj pozostałe kroki konfiguracji klastra i sprawdź szczegóły **podsumowania klastra**. Wybierz **pozycję Utwórz**.

1. Zaloguj się do interfejsu użytkownika Ambari dla `https://CLUSTERNAME.azurehdinsight.net`nowo utworzonego klastra w pliku . Użyj nazwy `hdiadmin@hdifabrikam.com` użytkownika administratora i jej hasła.

    ![Okno logowania interfejsu użytkownika Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Na pulpicie nawigacyjnym klastra wybierz pozycję **Role**.
1. Na stronie **Role** w obszarze **Przypisywanie ról do tych**programów obok roli Administrator **klastrów** wprowadź grupę *hdiusergroup*. 

    ![Przypisywanie roli administratora klastra do grupy hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Otwórz klienta Secure Shell (SSH) i zaloguj się do klastra. Użyj **hdiuser utworzonego** w lokalnym wystąpieniu usługi Active Directory.

    ![Logowanie się do klastra przy użyciu klienta SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Jeśli możesz zalogować się za pomocą tego konta, klaster ESP został poprawnie skonfigurowany do synchronizacji z lokalnym wystąpieniem usługi Active Directory.

## <a name="next-steps"></a>Następne kroki

Przeczytaj [wprowadzenie do bezpieczeństwa Apache Hadoop z ESP](hdinsight-security-overview.md).

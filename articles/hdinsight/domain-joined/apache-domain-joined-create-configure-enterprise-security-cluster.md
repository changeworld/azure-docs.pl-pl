---
title: Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight
description: Informacje na temat tworzenia i konfigurowania klastrów pakiet Enterprise Security w usłudze Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: bb6a3cff46c975ae6b59f0c6f97e37037f638620
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845779"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight

Pakiet Enterprise Security usługi Azure HDInsight zapewnia dostęp do uwierzytelniania opartego na Active Directoryach, obsługa przez wiele użytkowników oraz kontrolę dostępu opartą na rolach dla klastrów Apache Hadoop na platformie Azure. Klastry usługi HDInsight ESP umożliwiają organizacjom, które przestrzegają rygorystycznych firmowych zasad zabezpieczeń, aby bezpiecznie przetwarzać poufne dane.

Celem tego przewodnika jest poprawna konfiguracja niezbędnych zasobów, dzięki czemu użytkownicy lokalni mogą zalogować się do klastra usługi HDInsight z włączoną obsługą ESP. W tym artykule opisano kroki niezbędne do utworzenia pakiet Enterprise Security włączeniu klastra usługi Azure HDInsight. Kroki te pozwolą utworzyć maszynę wirtualną z systemem Windows IaaS z włączoną Active Directory & usługi nazw domen (DNS). Ten serwer będzie działać jako zamiennik dla **rzeczywistego** środowiska lokalnego i umożliwi przeprowadzenie kroków instalacji i konfiguracji, aby można było je powtórzyć później w Twoim środowisku. Ten przewodnik pomoże również utworzyć hybrydowe środowisko tożsamości przy użyciu funkcji synchronizacji skrótów haseł z Azure Active Directory.

Ten przewodnik jest przeznaczony do uzupełniania [użycia pakiet Enterprise Security w usłudze HDInsight](apache-domain-joined-architecture.md)

Przed rozpoczęciem korzystania z tego procesu we własnym środowisku Skonfiguruj Active Directory i usługi nazw domen (DNS). Ponadto Włącz Azure Active Directory i zsynchronizuj lokalne konta użytkowników, aby Azure Active Directory.

![Diagram architektury](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Tworzenie środowiska lokalnego

Omówienie: W tej sekcji zostanie użyty szablon szybkiego wdrożenia platformy Azure do tworzenia nowych maszyn wirtualnych, konfigurowania usług nazw domen (DNS) i nowego lasu usługi AD.

1. Przejdź do [tworzenia maszyny wirtualnej platformy Azure z nowym lasem usługi AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), aby wyświetlić szablon szybkiego wdrażania.

1. Kliknij pozycję **Wdróż na platformie Azure**.
1. Zaloguj się do subskrypcji platformy Azure.
1. Na ekranie **Tworzenie maszyny wirtualnej platformy Azure z nowym lasem usługi AD** wykonaj następujące czynności:
    1. Wybierz subskrypcję, w której chcesz wdrożyć zasoby z listy rozwijanej **subskrypcji** .
    1. Wybierz pozycję **Utwórz nowy** obok pozycji **Grupa zasobów** i wprowadź nazwę **OnPremADVRG**
    1. Wprowadź następujące szczegóły dotyczące pozostałej części pól szablonu:

        * **Lokalizacja**: Środkowe stany USA
        * **Nazwa użytkownika administratora**: HDIFabrikamAdmin
        * **Hasło administratora**: < YOUR_PASSWORD >
        * **Domena**: HDIFabrikam.com
        * **Prefiks DNS**: hdifabrikam

        ![Tworzenie szablonu maszyny wirtualnej platformy Azure i lasu usługi AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Kliknij pozycję **Kup**
    1. Monitoruj wdrożenie i poczekaj na jego zakończenie.
    1. Potwierdź, że zasoby są tworzone w ramach poprawnej grupy `OnPremADVRG`zasobów.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurowanie użytkowników i grup na potrzeby dostępu do klastra

Omówienie: W tej sekcji utworzysz użytkowników, którzy będą mieli dostęp do klastra usługi HDInsight na końcu tego przewodnika.

1. Połącz się z kontrolerem domeny za pomocą Pulpit zdalny.
    1. Jeśli został użyty szablon wymieniony na początku, kontroler domeny jest maszyną wirtualną o nazwie **adVM** w `OnPremADVRG` grupie zasobów.
    1. Przejdź do Azure Portal > **grup** > zasobów**OnPremADVRG** > **adVM** > **Connect**.
    1. Kliknij kartę **RDP** , a następnie kliknij pozycję **Pobierz plik RDP**.
    1. Zapisz plik na komputerze i otwórz go.
    1. Po wyświetleniu monitu o `HDIFabrikam\HDIFabrikamAdmin` poświadczenia Użyj nazwy użytkownika, a następnie wprowadź hasło wybrane dla konta administratora.

1. Po otwarciu sesji Pulpit zdalny na maszynie wirtualnej kontrolera domeny należy uruchomić **Active Directory użytkowników i komputery** z poziomu pulpitu nawigacyjnego **Menedżer serwera** . Kliknij przycisk **Narzędzia** w prawym górnym rogu, a następnie **Active Directory Użytkownicy i komputery** z listy rozwijanej.

    ![Menedżer serwera otworzyć Active Directory zarządzania](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Utwórz dwóch nowych użytkowników, **HDIAdmin**, **HDIUser**. Ci dwaj użytkownicy będą używani do logowania się do klastrów usługi HDInsight.

    1. Na ekranie **Active Directory Użytkownicy i komputery** kliknij pozycję **Akcja** > **Nowy** > **użytkownik**.

        ![Utwórz nowego użytkownika Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Na ekranie **nowy obiekt — użytkownik** wprowadź `HDIUser` **nazwę logowania użytkownika** , a następnie kliknij przycisk **dalej**.

        ![Utwórz pierwszego użytkownika administracyjnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. W wyświetlonym oknie podręcznym wprowadź odpowiednie hasło dla nowego konta. Zaznacz pole wyboru mówiące, że **hasło nigdy nie wygasa**. HDIClick **OK**.
    1. Kliknij przycisk **Zakończ** , aby utworzyć nowe konto.
    1. Utwórz innego użytkownika `HDIAdmin`.

        ![Utwórz drugiego użytkownika administracyjnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Na ekranie **Active Directory Użytkownicy i komputery** kliknij pozycję **Akcja** > **Nowa** > **Grupa**. Utwórz `HDIUserGroup` jako nową grupę.

    ![Utwórz nową grupę Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Utwórz nowy group2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Dodaj **HDIUser** utworzone w poprzednim kroku do **HDIUserGroup** jako element członkowski.

    1. Kliknij prawym przyciskiem myszy **HDIUserGroup** , a następnie kliknij pozycję **Właściwości**.
    1. Przejdź do karty **Członkowie** , a następnie kliknij przycisk **Dodaj**.
    1. Wprowadź `HDIUser` w polu Etykieta **Wprowadź nazwy obiektów do wybrania** , a następnie kliknij przycisk **OK**.
    1. Powtórz poprzednie kroki dla drugiego konta`HDIAdmin`

        ![Dodaj członków do grupy](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Środowisko Active Directory zostało utworzone wraz z dwoma użytkownikami i grupą użytkowników w celu uzyskania dostępu do klastra usługi HDInsight.

Ci użytkownicy zostaną zsynchronizowani z usługą Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Utwórz nowy Azure Active Directory

1. Zaloguj się do Portalu Azure.
1. Kliknij pozycję **Utwórz zasób** i wpisz **katalog**. Wybierz pozycję **Azure Active Directory** > **Utwórz**.
1. Wprowadź **HDIFabrikam** w polu **Nazwa organizacji**.
1. Wprowadź **HDIFabrikamoutlook** w polu **początkowa nazwa domeny**.
1. Kliknij przycisk **Utwórz**.
1. Po lewej stronie Azure Portal kliknij pozycję **Azure Active Directory**.
1. W razie potrzeby kliknij pozycję **Przełącz katalog** , aby przejść do nowego katalogu utworzonego **HDIFabrikamoutlook**.
1. W obszarze **Zarządzanie** kliknij pozycję **niestandardowe nazwy** > domen**Dodaj domenę niestandardową**.
1. Wprowadź **HDIFabrikam.com** w polu **nazwa domeny niestandardowej** , a następnie kliknij pozycję **Dodaj domenę**.

![Tworzenie nowej usługi Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Utwórz nową domenę niestandardową](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurowanie dzierżawy usługi Azure AD

Omówienie: Teraz skonfigurujesz dzierżawę usługi Azure AD, aby można było synchronizować użytkowników i grupy z lokalnej usługi AD do chmury.

1. Utwórz administratora dzierżawy usługi AD.
    1. Zaloguj się do Azure Portal i wybierz dzierżawę usługi Azure AD **HDIFabrikam**
    1. Wybierz opcję **Użytkownicy** w obszarze **Zarządzaj** , a następnie **nowy użytkownik**.
    1. Wprowadź następujące szczegóły dotyczące nowego użytkownika:

        * Nazwa: fabrikamazureadmin
        * Nazwa użytkownika:fabrikamazureadmin@hdifabrikam.com
        * Hasło: bezpieczne hasło

    1. Kliknij sekcję **grupy** , Wyszukaj pozycję **administratorzy kontrolera domeny usługi AAD**, a następnie kliknij pozycję **Wybierz**.

        ![Grupy](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Kliknij sekcję **rola katalogu** i wybierz pozycję **administrator globalny** po prawej stronie. Kliknij przycisk **OK**.

        ![Rola katalogu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Wprowadź hasło dla użytkownika. Kliknij przycisk **Utwórz**.

1. Jeśli chcesz zmienić hasło nowo utworzonego użytkownika <fabrikamazureadmin@hdifabrikam.com>. Zaloguj się do Azure Portal przy użyciu tożsamości, a następnie wyświetli się monit o zmianę hasła.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizuj użytkowników lokalnych z usługą Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Pobierz i zainstaluj Microsoft Azure Active Directory Connect

1. [Pobierz Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Zainstaluj Microsoft Azure Active Directory Connect na kontrolerze domeny.
    1. Otwórz plik wykonywalny pobrany w poprzednim kroku i zaakceptuj postanowienia licencyjne. Kliknij pozycję **Kontynuuj**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Kliknij pozycję **Użyj ustawień ekspresowych** i Ukończ instalację.

        ![Użyj ustawień ekspresowych](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Konfigurowanie synchronizacji z lokalnym kontrolerem domeny

1. Na ekranie **łączenie z usługą Azure AD** wprowadź nazwę użytkownika i hasło administratora globalnego usługi Azure AD. Kliknij przycisk **dalej**. Jest to nazwa użytkownika `fabrikamazureadmin@hdifabrikam.com` , która została utworzona podczas konfigurowania dzierżawy usługi AD.
    ![Łączenie z usługą Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)
1. Na ekranie **Połącz z Active Directory Domain Services** wprowadź nazwę użytkownika i hasło dla konta administratora przedsiębiorstwa. Kliknij przycisk **dalej**. To jest nazwa użytkownika `HDIFabrikam\HDIFabrikamAdmin` i pasujące do niego hasło, które zostało utworzone wcześniej.

   ![Połącz z Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na stronie **Konfiguracja logowania usługi Azure AD** kliknij przycisk **dalej**.
    ![Konfiguracja logowania za pomocą usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)
1. Na ekranie gotowy do konfiguracji kliknij przycisk **Instaluj**.
    ![Zainstaluj](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Po wyświetleniu ekranu **Konfiguracja ukończona** kliknij przycisk **Zakończ**.
    ![Konfiguracja ukończona](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Po zakończeniu synchronizacji upewnij się, że użytkownicy utworzeni w Active Directory IAAS są zsynchronizowani z Azure Active Directory.
    1. Zaloguj się do Portalu Azure.
    1. Wybierz **Azure Active Directory** > **użytkowników** **HDIFabrikam** > .

### <a name="create-an-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz tożsamość zarządzaną przypisaną przez użytkownika, która będzie używana do konfigurowania Azure Active Directory Domain Services (Azure AD-DS). Aby uzyskać więcej informacji na temat tworzenia tożsamości zarządzanej przypisanej przez użytkownika, zobacz [Tworzenie, wyświetlanie, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Zaloguj się do Portalu Azure.
1. Kliknij pozycję **Utwórz zasób** i wpisz **tożsamość zarządzana**. Wybierz pozycję >  **Zarządzanie tożsamościami zarządzanymi przez użytkownika** **.**
1. Wprowadź **HDIFabrikamManagedIdentity** jako **nazwę zasobu**.
1. Wybierz subskrypcję.
1. W obszarze **Grupa zasobów** kliknij pozycję **Utwórz nową** , a następnie wprowadź **HDIFabrikam-środkowe**.
1. W obszarze **Lokalizacja**wybierz pozycję **środkowe stany USA** .
1. Kliknij przycisk **Utwórz**.

![Tworzenie nowej tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Włączanie usług Azure Active Directory Domain Services

Aby uzyskać więcej informacji, zobacz [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Utwórz Virtual Network do hostowania usługi Azure AD — DS. Uruchom następujący kod programu PowerShell.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Zaloguj się do Portalu Azure.
1. Kliknij pozycję **Utwórz zasób**, wprowadź **usługi domenowe** i wybierz **Azure AD Domain Services**.
1. Na ekranie **podstawowe** wykonaj następujące czynności:
    1. W obszarze **Nazwa katalogu** wybierz Azure Active Directory utworzone w tym artykule **HDIFabrikam**.
    1. Wprowadź **nazwę domeny DNS** **HDIFabrikam.com**.
    1. Wybierz subskrypcję.
    1. Określ grupę zasobów **HDIFabrikam-środkowe** i **lokalizację** **środkowe stany USA**.

        ![podstawowe informacje o usłudze Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Na ekranie **sieci** wybierz sieć (**HDIFabrikam-VNET**) i podsieć (**AADDS-Subnet**), która została utworzona przy użyciu poprzedniego skryptu programu PowerShell. Możesz też użyć opcji **Utwórz nową** , aby utworzyć sieć wirtualną teraz.

    ![Wybieranie sieci](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na ekranie **grupy administratorów** powinna zostać wyświetlona powiadomienie z informacją o tym, że grupa o nazwie **Administratorzy usługi AAD** jest już utworzona w celu administrowania tą grupą. Można opcjonalnie zmodyfikować członkostwo w tej grupie, ale nie jest to wymagane w przypadku czynności opisanych w tym artykule. Kliknij przycisk **OK**.

    ![Wyświetl grupę administratorów](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na ekranie **Synchronizacja** Włącz pełną synchronizację, wybierając pozycję **wszystkie** , a następnie kliknij przycisk **OK**.

    ![Włącz synchronizację](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Na ekranie **Podsumowanie** Sprawdź szczegóły dotyczące usługi Azure AD — DS, a następnie kliknij przycisk **OK**.

    ![Weryfikuj szczegóły](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. Po włączeniu usługi Azure AD-DS lokalny serwer usługi nazw domen (DNS) jest uruchamiany na Virtual Machines usługi AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurowanie sieci wirtualnej usługi Azure AD — DS

Kroki opisane w tej sekcji ułatwią skonfigurowanie sieci wirtualnej usługi Azure AD-DS (**HDIFabrikam-AADDSVNET**) w celu korzystania z niestandardowych serwerów DNS.

1. Zlokalizuj adresy IP niestandardowych serwerów DNS. Kliknij zasób **HDIFabrikam.com** usługi AD DS, kliknij pozycję **Właściwości** w obszarze **Zarządzaj**   i Sprawdź adresy IP wymienione w obszarze **adres IP na Virtual Network**.

    ![Lokalizowanie niestandardowych adresów IP DNS dla usługi Azure AD — DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Skonfiguruj niestandardowe adresy IP `10.0.0.4` **HDIFabrikam-AADDSVNET** i `10.0.0.5`.

    1. Wybierz pozycję **serwery** DNS w kategorii **Ustawienia** . następnie kliknij przycisk radiowy obok pozycji **niestandardowy**, wprowadź pierwszy adres IP (10.0.0.4) w polu tekstowym poniżej, a następnie kliknij przycisk **Zapisz**.
    1. Dodaj dodatkowe adresy IP (10.0.0.5), korzystając z tych samych kroków.

1. W naszym scenariuszu usługa Azure AD-DS została skonfigurowana pod kątem używania adresów IP 10.0.0.4 i 10.0.0.5, ustawiając ten sam adres IP w sieci wirtualnej AADDS, jak pokazano na poniższej ilustracji.

    ![Wyświetlanie niestandardowych serwerów DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Zabezpieczanie ruchu LDAP

Protokół LDAP (Lightweight Directory Access Protocol) służy do odczytu i zapisu w Active Directory. Ruch związany z protokołem LDAP można uczynić poufnym i bezpiecznym przy użyciu technologii SSL (SSL)/Transport Layer Security (TLS). Protokół LDAP można włączyć za pośrednictwem protokołu SSL (LDAPs) przez zainstalowanie prawidłowo sformatowanego certyfikatu.

Aby uzyskać więcej informacji na temat bezpiecznego protokołu LDAP, zobacz [Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

W tej sekcji utworzysz certyfikat z podpisem własnym, pobierzesz certyfikat i skonfigurujesz Secure LDAP (LDAPs) dla domeny zarządzanej **hdifabrikam** Azure AD-DS.

Poniższy skrypt tworzy certyfikat dla hdifabrikam. Certyfikat jest zapisywany pod ścieżką "LocalMachine".

> [!Note] 
> Do utworzenia żądania certyfikatu SSL można użyć dowolnego narzędzia \#lub aplikacji, która tworzy prawidłowe żądanie PKCS 10.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Sprawdź, czy certyfikat jest zainstalowany w osobistym\'magazynie komputera. Wykonaj następujące czynności:

1. Uruchom program Microsoft Management Console (MMC).
1. Dodaj przystawkę Certyfikaty, która zarządza certyfikatami na komputerze lokalnym.
1. Rozwiń węzeł **Certyfikaty (komputer lokalny)** , rozwiń węzeł **osobiste**, a następnie rozwiń węzeł **Certyfikaty**. W magazynie osobistym powinien istnieć nowy certyfikat. Ten certyfikat jest wystawiony dla w pełni kwalifikowanej nazwy hosta.

    ![Weryfikowanie tworzenia certyfikatu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. W prawym okienku kliknij prawym przyciskiem myszy certyfikat utworzony w poprzednim kroku, wskaż polecenie **wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**.

1. Na stronie **Eksportowanie klucza** prywatnego kliknij przycisk **tak, Eksportuj prywatny klucz**. Klucz prywatny jest wymagany, aby szyfrowane wiadomości były odczytywane z komputera, na którym klucz zostanie zaimportowany.

    ![Eksportuj klucz prywatny](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Na stronie **Format pliku eksportu**Pozostaw ustawienia domyślne, a następnie kliknij przycisk **dalej**. 
1. Na stronie **hasło** wpisz hasło klucza prywatnego, wybierz pozycję **TripleDES-SHA1** do **szyfrowania** i kliknij przycisk **dalej**.
1. Na stronie **Eksportuj** plik wpisz ścieżkę i nazwę wyeksportowanego pliku certyfikatu, a następnie kliknij przycisk **dalej**.
1. Nazwa pliku musi mieć rozszerzenie PFX. ten plik jest skonfigurowany na Azure Portal w celu nawiązania bezpiecznego połączenia.
1. Włącz Secure LDAP (LDAPs) dla Azure AD Domain Services domeny zarządzanej.
    1. Wybierz domenę **HDIFabrikam.com** z Azure Portal.
    1. Kliknij **Secure LDAP** w obszarze **Zarządzaj**.
    1. Na ekranie **Secure LDAP** kliknij pozycję **włącz** w obszarze **Secure LDAP**.
    1. Wyszukaj plik certyfikatu PFX wyeksportowany na komputerze.
    1. Wprowadź hasło certyfikatu.

    ![Włącz bezpieczny protokół LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Teraz, po włączeniu Secure LDAP Upewnij się, że jest osiągalny przez włączenie portu 636.
    1. Kliknij grupę zabezpieczeń sieci **AADDS-HDIFabrikam.com-sieciowej grupy zabezpieczeń** w grupie zasobów **HDIFabrikam-centrald** .
    1. W obszarze **Ustawienia** kliknij pozycję **reguły** > zabezpieczeń ruchu przychodzącego**Dodaj**.
    1. Na ekranie **Dodawanie reguły zabezpieczeń ruchu przychodzącego** wprowadź następujące właściwości, a następnie kliknij przycisk **Dodaj**:

        | Właściwość | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Miejsce docelowe | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number\> |
        | Name | Port_LDAP_636 |

    ![Reguła zabezpieczeń dla ruchu przychodzącego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`to tożsamość zarządzana przypisana przez użytkownika, rola współautor usług domenowych w usłudze HDInsight jest włączona dla tożsamości zarządzanej, która umożliwi tej tożsamości odczytywanie, tworzenie, modyfikowanie i usuwanie operacji usług domenowych.

    ![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Tworzenie klastra usługi HDInsight z włączonym pakiet Enterprise Security

Ten krok wymaga spełnienia następujących wymagań wstępnych:

1. Utwórz nową grupę `HDIFabrikam-WestUS` zasobów w lokalizacji `West US`.
1. Utwórz sieć wirtualną, która będzie hostować klaster usługi HDInsight z obsługą ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Utwórz relację równorzędną między Virtual Network, która obsługuje AADDS (`HDIFabrikam-AADDSVNET`) i Virtual Network, które będą hostować klaster usługi HDInsight z włączoną`HDIFabrikam-HDIVNet`obsługą ESP (). Użyj poniższego kodu programu PowerShell do komunikacji równorzędnej z tymi dwiema sieciami wirtualnymi.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Utwórz nowe konto Azure Data Lake Storage Gen2 **Hdigen2store**, które jest skonfigurowane przy użyciu tożsamości zarządzanej przez użytkownika **HDIFabrikamManagedIdentity**. Aby uzyskać więcej informacji na temat tworzenia kont Data Lake Storage Gen2 włączonych z tożsamościami zarządzanymi przez użytkownika, zobacz [używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Skonfiguruj niestandardowy serwer DNS w sieci wirtualnej **HDIFabrikam-AADDSVNET** .
    1. Przejdź do Azure Portal > **grup** > zasobów**OnPremADVRG** > **HDIFabrikam AADDSVNET** > **serwerów DNS**.
    1. Wybierz opcję **niestandardowe** i `10.0.0.4` wprowadź `10.0.0.5`i.
    1. Kliknij polecenie **Zapisz**.

        ![Zapisz niestandardowe ustawienia DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Utwórz nowy klaster usługi HDInsight Spark z włączoną obsługą ESP.
    1. Kliknij pozycję **niestandardowy (rozmiar, ustawienia, aplikacje)** .
    2. Wprowadź odpowiednie szczegóły dotyczące sekcji 1 **podstawy**. Upewnij się, że **Typ klastra** to **Spark 2,3 (HDI 3,6)** , a **Grupa zasobów** to **HDIFabrikam — środkowe**

    1. W sekcji 2 **zabezpieczenia i sieć**wykonaj następujące czynności:
        1. Kliknij pozycję **włączone** w obszarze **pakiet Enterprise Security**.
        1. Kliknij pozycję **administrator klastra** , a następnie wybierz konto **HDIAdmin** utworzone wcześniej jako administrator lokalny. Kliknij przycisk **wybierz**.

        1. Kliknij pozycję **Grupa dostępu do klastra** , a następnie wybierz pozycję **HDIUserGroup**. Każdy użytkownik dodany do tej grupy w przyszłości będzie mógł uzyskać dostęp do klastrów usługi HDInsight.

            ![Wybierz grupę dostępu do klastra](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Wykonaj pozostałe kroki konfiguracji klastra i sprawdź szczegóły dotyczące **podsumowania klastra**. Kliknij przycisk **Utwórz**.

1. Zaloguj się do interfejsu użytkownika Ambari dla nowo utworzonego klastra `https://CLUSTERNAME.azurehdinsight.net` przy użyciu nazwy `hdiadmin@hdifabrikam.com` użytkownika i hasła administratora.

    ![Zaloguj się do Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Na pulpicie nawigacyjnym klastra kliknij pozycję **role** .
1. Na stronie **role** wprowadź grupę **hdiusergroup** , aby przypisać ją do roli **administrator klastra** w obszarze **Przypisz role do nich**.

    ![Przypisywanie roli administratora klastra do hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Otwórz klienta SSH i zaloguj się do klastra przy użyciu **hdiuser** utworzonego wcześniej w Active Directory lokalnej.

    ![Logowanie do klastra przy użyciu protokołu SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Jeśli możesz zalogować się przy użyciu tego konta, klaster ESP został prawidłowo skonfigurowany do synchronizacji z lokalną usługą Active Directory.

## <a name="next-steps"></a>Następne kroki

* [An introduction to Apache Hadoop security with Enterprise Security Package (Wprowadzenie do zabezpieczeń usługi Apache Hadoop z pakietem Enterprise Security)](hdinsight-security-overview.md)

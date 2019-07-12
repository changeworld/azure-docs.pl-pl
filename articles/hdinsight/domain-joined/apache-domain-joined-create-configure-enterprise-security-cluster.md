---
title: Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight
description: Dowiedz się, jak utworzyć i skonfigurować pakiet Enterprise Security klastrów w usłudze Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 98bd222212d616a5d2c608779c607bb431d184b9
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657329"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight

Pakiet Enterprise Security dla usługi Azure HDInsight umożliwia dostęp do uwierzytelniania opartego na usłudze Active Directory, obsługa wielu użytkowników i kontrola dostępu oparta na rolach, klastrów Apache Hadoop na platformie Azure. Klastry HDInsight ESP umożliwiają organizacji, które obowiązują zasady ścisłymi zasadami kontrolnymi zabezpieczeń firmy, przetwarzanie danych poufnych w bezpieczny sposób.

Celem tego przewodnika jest, aby poprawnie skonfigurować niezbędne zasoby, dzięki czemu w środowisku lokalnym, użytkownik może zalogować się do ESP włączone klastra HDInsight. W tym artykule przedstawiono kroki niezbędne do utworzenia klastra HDInsight systemu Azure w pakiet Enterprise Security włączone. Kroki obejmują tworzenie maszyny Wirtualnej IaaS z systemem Windows za pomocą usługi Active Directory i usługi DNS (Domain Name) włączone. Ten serwer będzie działał jako zamiennika dla Twojego **rzeczywiste** środowiska lokalne i umożliwi postępuj zgodnie z instrukcjami kroków instalacji i konfiguracji, aby można go powtarzać później we własnym środowisku. Ten przewodnik ułatwi również utworzenie środowiska tożsamości hybrydowej za pomocą synchronizacji skrótów haseł w usłudze Azure Active Directory.

Ten przewodnik jest przeznaczony do uzupełnienia [pakiet Enterprise Security użycia w HDInsight](apache-domain-joined-architecture.md)

Przed użyciem tego procesu we własnym środowisku, skonfiguruj usługi Active Directory i usługi nazw domen (DNS, Domain Name System). Ponadto należy włączyć usługi Azure Active Directory i synchronizacji kont użytkowników lokalnych z usługą Azure Active Directory.

![Diagram architektury](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Tworzenie w środowisku lokalnym

Omówienie: W tej sekcji użyjesz szablonu szybkiego wdrażania platformy Azure do tworzenia nowych maszyn wirtualnych, konfigurowanie usług nazw domen (DNS, Domain Name System), a nowy las usługi AD.

1. Przejdź do [Utwórz Maszynę wirtualną platformy Azure z nowego lasu usługi AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), aby wyświetlić szablon szybkiego wdrażania.

1. Kliknij pozycję **wdrażanie na platformie Azure**.
1. Zaloguj się do subskrypcji platformy Azure.
1. Na **Utwórz Maszynę wirtualną platformy Azure z nowego lasu usługi AD** ekranu, wykonaj następujące czynności:
    1. Wybierz subskrypcję, w którym zasoby wdrożone z **subskrypcji** listy rozwijanej.
    1. Wybierz **Utwórz nową** obok **grupy zasobów** i wprowadź nazwę **OnPremADVRG**
    1. Wprowadź następujące informacje dla pozostałych pól szablonu:

        * **Lokalizacja**: Środkowe stany USA
        * **Nazwa użytkownika administratora**: HDIFabrikamAdmin
        * **Hasło administratora**: < YOUR_PASSWORD >
        * **Domeny**: HDIFabrikam.com
        * **Prefiks DNS**: hdifabrikam

        ![Szablon tworzenia maszyny Wirtualnej platformy Azure i lasem usługi AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Kliknij przycisk **zakupu**
    1. Monitorowanie wdrożenia i zaczekaj na jej zakończenie.
    1. Upewnij się, zasoby są tworzone w ramach grupy zasobów poprawne `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurowanie użytkowników i grup dla dostępu do klastra

Omówienie: W tej sekcji utworzysz użytkowników, którzy będą mieli dostęp do klastra HDInsight do końca tego przewodnika.

1. Podłącz do kontrolera domeny przy użyciu pulpitu zdalnego.
    1. Jeśli używasz szablonu, o których wspomniano na początku, kontroler domeny jest Maszynę wirtualną o nazwie **adVM** w `OnPremADVRG` grupy zasobów.
    1. Przejdź do witryny Azure portal > **grup zasobów** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Kliknij przycisk **RDP** kartę, a następnie kliknij przycisk **Pobierz plik RDP**.
    1. Zapisz plik na komputerze, a następnie otwórz go.
    1. Gdy zostanie wyświetlony monit o poświadczenia, `HDIFabrikam\HDIFabrikamAdmin` jako nazwa użytkownika, a następnie wprowadź hasło, który został wybrany dla konta administratora.

1. Uruchom po otwarciu sesji pulpitu zdalnego na kontrolerze domeny, maszyna wirtualna **użytkownicy usługi Active Directory i komputery** z **Menedżera serwera** pulpitu nawigacyjnego. Kliknij przycisk **narzędzia** w prawym górnym rogu, a następnie **użytkownicy usługi Active Directory i komputery** z listy rozwijanej.

    ![Menedżera serwera Otwórz Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Tworzenie dwóch nowych użytkowników, **HDIAdmin**, **HDIUser**. Ci dwaj użytkownicy będą używane do logowania się z klastrami HDInsight.

    1. W **użytkownicy usługi Active Directory i komputery** ekranu, kliknij przycisk **akcji** > **New** > **użytkownika**.

        ![Tworzenie nowych użytkowników usługi Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. W **nowy obiekt — użytkownik** ekranu, należy wprowadzić `HDIUser` jako **nazwa logowania użytkownika** i kliknij przycisk **dalej**.

        ![Tworzenie pierwszego użytkownika administratora](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. W wyświetlonym oknie podręcznym wprowadź odpowiednie hasło dla nowego konta. Zaznacz pole wyboru, które mówi **hasło nigdy nie wygasa**. HDIClick **OK**.
    1. Kliknij przycisk **Zakończ** do utworzenia nowego konta.
    1. Utwórz inny użytkownik `HDIAdmin`.

        ![Utwórz drugi administratora](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. W **użytkownicy usługi Active Directory i komputery** ekranu, kliknij przycisk **akcji** > **New** > **grupy**. Utwórz `HDIUserGroup` jako nową grupę.

    ![Tworzenie nowej grupy usługi Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Utwórz nowy group2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Dodaj **HDIUser** utworzony w poprzednim kroku, aby **HDIUserGroup** jako członka.

    1. Kliknij prawym przyciskiem myszy **HDIUserGroup** i kliknij przycisk **właściwości**.
    1. Przejdź do **członków** kartę, a następnie kliknij przycisk **Dodaj**.
    1. Wprowadź `HDIUser` w polu **wprowadź nazwy obiektów do wybrania** i kliknij przycisk **OK**.
    1. Powtórz poprzednie kroki dla innego konta `HDIAdmin`

        ![Dodaj członków do grupy](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Utworzono środowisku usługi Active Directory oraz dwóch użytkowników i grupy użytkowników do uzyskiwania dostępu do klastra HDInsight.

Ci użytkownicy zostaną zsynchronizowane z usługą Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Tworzenie nowej usługi Azure Active Directory

1. Zaloguj się do Portalu Azure.
1. Kliknij przycisk **Utwórz zasób** i typ **katalogu**. Wybierz **usługi Azure Active Directory** > **tworzenie**.
1. Wprowadź **HDIFabrikam** w obszarze **nazwa organizacji**.
1. Wprowadź **HDIFabrikamoutlook** w obszarze **początkowa nazwa domeny**.
1. Kliknij przycisk **Utwórz**.
1. Po lewej stronie w witrynie Azure portal, kliknij przycisk **usługi Azure Active Directory**.
1. W razie potrzeby kliknij **Przełącz katalog** można zmienić do nowego katalogu, który został utworzony **HDIFabrikamoutlook**.
1. W obszarze **Zarządzaj** kliknij **niestandardowe nazwy domen** > **Dodaj domenę niestandardową**.
1. Wprowadź **HDIFabrikam.com** w obszarze **niestandardowej nazwy domeny** i kliknij przycisk **Dodaj domenę**.

![Tworzenie nowej usługi azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Tworzenie nowej domeny niestandardowej](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurowanie dzierżawy usługi Azure AD

Omówienie: Teraz skonfigurujesz dzierżawy usługi Azure AD, dzięki czemu można synchronizować użytkowników i grup z lokalnej usługi AD w chmurze.

1. Utwórz administratora dzierżawy usługi AD.
    1. Zaloguj się do witryny Azure portal i wybierz dzierżawę usługi Azure AD **HDIFabrikam**
    1. Wybierz **użytkowników** w obszarze **Zarządzaj** i następnie **nowego użytkownika**.
    1. Wprowadź następujące informacje dla nowego użytkownika:

        * Nazwa: fabrikamazureadmin
        * Nazwa użytkownika: fabrikamazureadmin@hdifabrikam.com
        * Hasło: bezpieczne hasło wybranego

    1. Kliknij pozycję **grup** sekcji, wyszukaj **Administratorzy usługi AAD DC**i kliknij przycisk **wybierz**.

        ![Grupy](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Kliknij pozycję **roli w katalogu** i wybierz pozycję **administratora globalnego** po prawej stronie. Kliknij przycisk **OK**.

        ![Rola katalogu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Wprowadź hasło dla użytkownika. Kliknij przycisk **Utwórz**.

1. Jeśli chcesz zmienić hasło dla nowo utworzonego użytkownika <fabrikamazureadmin@hdifabrikam.com>. Zaloguj się do usługi Azure portal przy użyciu tożsamości, a następnie będą monit o zmianę hasła.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizuj lokalnych użytkowników z usługą Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Pobierz i zainstaluj program Microsoft Azure Active Directory connect

1. [Pobieranie programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Zainstaluj program Microsoft Azure Active Directory connect na kontrolerze domeny.
    1. Otwórz plik wykonywalny, który został pobrany w poprzednim kroku i zaakceptuj postanowienia licencyjne. Kliknij pozycję **Kontynuuj**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Kliknij przycisk **Użyj ustawień ekspresowych** i ukończyć instalację.

        ![Użyj ustawień ekspresowych](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Konfigurowanie synchronizacji z kontrolerem domeny w środowisku lokalnym

1. Na **nawiązywanie połączenia z usługi Azure AD** ekranu, należy wprowadzić nazwę użytkownika i hasło administratora globalnego dla usługi Azure AD. Kliknij przycisk **dalej**. Jest to nazwa użytkownika `fabrikamazureadmin@hdifabrikam.com` utworzony podczas konfigurowania dzierżawy usługi AD.
    ![Łączenie z usługą Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Na **nawiązywanie połączenia z usług domenowych Active Directory** ekranu, należy wprowadzić nazwę użytkownika i hasło dla konta administratora przedsiębiorstwa. Kliknij przycisk **dalej**. Jest to nazwa użytkownika `HDIFabrikam\HDIFabrikamAdmin` i jego hasło zgodnego, który został utworzony wcześniej.

   ![Nawiązać połączenie z usługami domenowymi Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Na **Konfiguracja logowania w usłudze Azure AD** kliknij **dalej**.
    ![Konfiguracja logowania w usłudze AD platformy Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. Na stronie gotowy do skonfigurowania ekranu, kliknij przycisk **zainstalować**.
    ![install](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Gdy **ukończyć konfiguracji** ekran jest wyświetlany, kliknij przycisk **zakończenia**.
    ![Kończenie konfiguracji](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Po ukończeniu synchronizacji upewnij się, jeśli użytkownicy, którzy utworzonym w usłudze Active Directory IAAS są synchronizowane z usługą Azure Active Directory.
    1. Zaloguj się do Portalu Azure.
    1. Select **Azure Active Directory** > **HDIFabrikam** > **Users**.

### <a name="create-an-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika

Utwórz przypisanych do użytkowników zarządzanych tożsamości, która będzie służyć do konfigurowania Azure Active Directory Domain Services (Azure AD DS). Aby uzyskać więcej informacji na temat tworzenia przypisanych przez użytkownika tożsamości zarządzanej, zobacz [tworzenia, listy, usuwania lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Zaloguj się do Portalu Azure.
1. Kliknij przycisk **Utwórz zasób** i typ **tożsamości zarządzanej**. Wybierz **użytkownik przypisany tożsamość zarządzaną** > **tworzenie**.
1. Wprowadź **HDIFabrikamManagedIdentity** jako **Nazwa zasobu**.
1. Wybierz subskrypcję.
1. W obszarze **grupy zasobów** kliknij **Utwórz nową** i wprowadź **HDIFabrikam CentralUS**.
1. Wybierz **środkowe stany USA** w obszarze **lokalizacji**.
1. Kliknij przycisk **Utwórz**.

![Tworzenie nowej tożsamości zarządzanych przypisanych przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Włączanie usług Azure Active Directory Domain Services

Aby uzyskać więcej informacji, zobacz [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Tworzenie sieci wirtualnej do hosta usług AD DS platformy Azure. Uruchom następujący kod programu powershell.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Zaloguj się do Portalu Azure.
1. Kliknij przycisk **Utwórz zasób**, wprowadź **usługi domenowe** i wybierz **usług domenowych Azure AD**.
1. Na **podstawy** ekranu, wykonaj następujące czynności:
    1. W obszarze **nazwy katalogu** wybierz pozycję Azure Active Directory, utworzone w tym artykule **HDIFabrikam**.
    1. Wprowadź **nazwy domeny DNS** z **HDIFabrikam.com**.
    1. Wybierz subskrypcję.
    1. Określ grupę zasobów **HDIFabrikam CentralUS** i **lokalizacji** z **środkowe stany USA**.

        ![podstawowe szczegóły dotyczące usługi Azure ad ds](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Na **sieci** ekranu pełną, wybierz sieć (**między SIECIAMI wirtualnymi HDIFabrikam**) i podsiecią (**AADDS podsieci**) które zostały utworzone z poprzednim skrypcie programu powershell. Możesz też **Utwórz nową** opcję, aby utworzyć sieć wirtualną teraz.

    ![Wybierz sieć](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Na **grupy Administrator** ekranu, powinien zostać wyświetlony powiadomienie, że grupa o nazwie **Administratorzy usługi AAD DC** została już utworzona do administrowania tej grupy. Opcjonalnie możesz zmodyfikować członkostwo tej grupy, ale nie jest to wymagane na potrzeby procedury w tym artykule. Kliknij przycisk **OK**.

    ![Widok grupy administratorów](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Na **synchronizacji** ekranu, włączyć pełną synchronizację, wybierając **wszystkich** a następnie kliknij przycisk **OK**.

    ![Włącz synchronizację](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Na **Podsumowanie** ekranu, sprawdź szczegóły dla usługi Azure AD — DS i kliknij przycisk **Ok**.

    ![Sprawdź szczegóły](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Po włączeniu usług AD DS platformy Azure, lokalnego serwera usługi nazw domen (DNS, Domain Name System) działa na maszynach wirtualnych (VM) AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurowanie sieci wirtualnej w usługach AD DS platformy Azure

Kroki opisane w tej sekcji będzie pomocne w konfigurowaniu sieci wirtualnej usług AD DS Azure (**HDIFabrikam AADDSVNET**) do użycia z niestandardowych serwerów DNS.

1. Znajdź adresów IP z niestandardowych serwerów DNS. Kliknij pozycję **HDIFabrikam.com** zasobów usług AD DS, kliknij przycisk **właściwości** w obszarze **Zarządzaj**   i spójrz na adresy IP na liście w obszarze **adresu IP Adres w sieci wirtualnej**.

    ![Znajdź niestandardowe adresy IP serwera DNS dla usługi Azure AD — DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Konfigurowanie **HDIFabrikam AADDSVNET** do niestandardowych adresów IP `10.0.0.4` i `10.0.0.5`.

    1. Wybierz **serwerów DNS** w obszarze **ustawienia** kategorii. następnie kliknij przycisk radiowy obok pola **niestandardowe**, wprowadź pierwszy adres IP (10.0.0.4) w poniższym polu tekstowym i kliknij przycisk **Zapisz**.
    1. Dodaj dodatkowe adresy IP (10.0.0.5) przy użyciu tych samych kroków.

1. W naszym scenariuszu usług AD DS Azure został skonfigurowany do używania adresów IP 10.0.0.4 i 10.0.0.5 takie same ustawienia adresu IP w sieci wirtualnej AADDS jako Pokaż na poniższej ilustracji.

    ![Wyświetl serwery dns niestandardowe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Zabezpieczanie ruchu protokołu LDAP

Dostęp do protokołu LDAP (Lightweight Directory) służy do odczytu i zapisu do usługi Active Directory. Aby włączyć LDAP ruchu poufność i bezpieczeństwo za pomocą Secure Sockets Layer (SSL) / technologii zabezpieczeń TLS (Transport Layer). Można włączyć LDAP za pośrednictwem protokołu SSL (LDAPS) przez zainstalowanie certyfikatu poprawnie sformatowana.

Aby uzyskać więcej informacji na temat bezpiecznego protokołu LDAP, zobacz [domeny zarządzanej przez Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla usługi Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

W tej sekcji Tworzenie certyfikatu z podpisem własnym, Pobierz certyfikat i Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla **hdifabrikam** domeny zarządzanej przez usług AD DS platformy Azure.

Poniższy skrypt tworzy certyfikat hdifabrikam. Certyfikat zostanie zapisany w ścieżce "LocalMachine".

> [!Note] 
> Narzędzie dowolnej aplikacji, która tworzy prawidłowy PKCS \#10 żądań może służyć do tworzenia żądania certyfikatu protokołu SSL.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Sprawdź, czy certyfikat jest zainstalowany na komputerze\'magazynu osobistej s. Wykonaj następujące czynności:

1. Uruchom program Microsoft Management Console (MMC).
1. Dodaj przystawkę Certyfikaty, która zarządza certyfikatów na komputerze lokalnym.
1. Rozwiń **certyfikaty (komputer lokalny)** , rozwiń węzeł **osobistych**, a następnie rozwiń węzeł **certyfikaty**. Nowego certyfikatu powinny istnieć w magazynie osobistym. Ten certyfikat został wystawiony dla nazwy FQDN hosta.

    ![Sprawdź tworzenia certyfikatów](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. W okienku po prawej stronie kliknij prawym przyciskiem myszy certyfikat, który został utworzony w poprzednim kroku, wskaż polecenie **wszystkie zadania**, a następnie kliknij przycisk **wyeksportować**.

1. Na **eksportowanie klucza prywatnego** kliknij **tak, Eksportuj klucz prywatny,** . Klucz prywatny jest wymagany dla szyfrowanych komunikatów do odczytu z komputera, na którym zostanie zaimportowany klucz.

    ![Eksportowanie klucza prywatnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Na **Format pliku eksportu** strony, pozostaw ustawienia domyślne, a następnie kliknij przycisk **dalej**. 
1. Na **hasło** strony, wpisz hasło klucza prywatnego, wybierz opcję **TripleDES SHA1** dla **szyfrowania** i kliknij przycisk **dalej**.
1. Na **Eksport pliku** stronie, wpisz ścieżkę i nazwę wyeksportowanego pliku certyfikatu, a następnie kliknij **dalej**.
1. Nazwa pliku musi być rozszerzeniem .pfx, ten plik jest skonfigurowany w witrynie Azure portal do nawiązania bezpiecznego połączenia.
1. Włączanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej usług domenowych Azure AD.
    1. Wybierz domenę **HDIFabrikam.com** w witrynie Azure portal.
    1. Kliknij przycisk **bezpieczny protokół LDAP** w obszarze **zarządzanie**.
    1. Na **Secure LDAP** ekranu, kliknij przycisk **Włącz** w obszarze **Secure LDAP**.
    1. Wyszukaj plik certyfikatu PFX, który został wyeksportowany na tym komputerze.
    1. Wprowadź hasło certyfikatu.

    ![Włączanie protokołu secure ldap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Teraz, po włączeniu protokołu Secure LDAP, upewnij się, że jest dostępny, włączając portu 636.
    1. Kliknij grupę zabezpieczeń sieci **AADDS-HDIFabrikam.com-NSG** w **HDIFabrikam CentralUS** grupy zasobów.
    1. W obszarze **ustawienia** kliknij **reguły zabezpieczeń dla ruchu przychodzącego** > **Dodaj**.
    1. Na **Dodawanie reguły zabezpieczeń dla ruchu przychodzącego** ekranu, wprowadź następujące właściwości i kliknij przycisk **Dodaj**:

        | Właściwość | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Miejsce docelowe | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priorytet | \<Żądany numer\> |
        | Name (Nazwa) | Port_LDAP_636 |

    ![reguły zabezpieczeń ruchu przychodzącego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` to użytkownik przypisany tożsamości zarządzanej roli współautora usługi domeny HDInsight jest włączone, aby zarządzanych tożsamości, która umożliwi tej tożsamości do odczytu, tworzenie, modyfikowanie i usuwanie operacji usługi domeny.

    ![Tworzenie zarządzanych tożsamości przypisanych przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Tworząc pakiet Enterprise Security włączone klaster HDInsight

Ten krok wymaga następujących wymagań wstępnych:

1. Utwórz nową grupę zasobów `HDIFabrikam-WestUS` w lokalizacji `West US`.
1. Tworzenie wirtualnej sieci, który będzie hostował ESP włączone klastra HDInsight.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Tworzenie relacji między siecią wirtualną, który jest hostem AADDS elementu równorzędnego (`HDIFabrikam-AADDSVNET`) i sieci wirtualnej, który będzie hostował ESP włączone klastra HDInsight (`HDIFabrikam-HDIVNet`). Użyj poniższego kodu programu powershell, aby nawiązać komunikację równorzędną między tymi dwiema sieciami wirtualnymi.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Utwórz nowe konto usługi Azure Data Lake Storage Gen2 **Hdigen2store**, która jest skonfigurowana przy użyciu tożsamości użytkownika zarządzanego **HDIFabrikamManagedIdentity**. Aby uzyskać więcej informacji na temat tworzenia konta Data Lake Storage Gen2 włączone użytkownikowi zarządzanych tożsamości, zobacz [użycia usługi Azure Data Lake magazynu Gen2 za pomocą usługi Azure HDInsight clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Konfigurowanie niestandardowych DNS na **HDIFabrikam AADDSVNET** sieci wirtualnej.
    1. Przejdź do witryny Azure portal > **grup zasobów** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >   **Serwery DNS**.
    1. Wybierz **niestandardowe** i wprowadź `10.0.0.4` i `10.0.0.5`.
    1. Kliknij polecenie **Zapisz**.

        ![Zapisz ustawienia niestandardowe dns](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Utwórz nowy klaster z obsługą ESP HDInsight Spark.
    1. Kliknij przycisk **niestandardowe (rozmiar, ustawienia, aplikacje)** .
    2. Wprowadź szczegóły odpowiednią dla sekcji 1 **podstawy**. Upewnij się, że **typ klastra** jest **2.3 Spark (HDI 3.6)** i **grupy zasobów** jest **HDIFabrikam CentralUS**

    1. W sekcji 2 **bezpieczeństwo i obsługa sieci**, wykonaj następujące czynności:
        1. Kliknij przycisk **włączone** w obszarze **pakiet Enterprise Security**.
        1. Kliknij przycisk **użytkownika administratora klastra** i wybierz **HDIAdmin** utworzonego wcześniej w środowisku lokalnym użytkownika jako administratora konta. Kliknij przycisk **wybierz**.

        1. Kliknij przycisk **grupy dostęp do klastra** , a następnie wybierz **HDIUserGroup**. Każdy użytkownik, który można dodać do tej grupy w przyszłości będą mogli korzystać z klastrów HDInsight.

            ![Wybierz grupę dostępu do klastra](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Wykonaj kolejne kroki konfiguracji klastra i sprawdź szczegóły w **klastra Podsumowanie**. Kliknij przycisk **Utwórz**.

1. Zaloguj się do nowo utworzonego klastra w Interfejsie użytkownika Ambari `https://CLUSTERNAME.azurehdinsight.net` przy użyciu swojej nazwy użytkownika administratora `hdiadmin@hdifabrikam.com` i hasło.

    ![Zaloguj się do systemu Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Kliknij przycisk **role** na pulpicie nawigacyjnym klastra.
1. Na **role** strony, wprowadź nazwę grupy **hdiusergroup** przypisać ją do **Administrator klastra** rolę w ramach **przypisać role do tych**.

    ![Przypisywanie roli administratora klastra do hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Otworzyć klienta SSH i zaloguj się do klastra przy użyciu **hdiuser** utworzonego wcześniej w usłudze Active Directory w środowisku lokalnym.

    ![Zaloguj się do klastra przy użyciu protokołu SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Jeśli jesteś w stanie się zalogować za pomocą tego konta, następnie skonfigurowano klaster ESP poprawnie do synchronizacji z usługą active directory w środowisku lokalnym.

## <a name="next-steps"></a>Następne kroki

* [An introduction to Apache Hadoop security with Enterprise Security Package (Wprowadzenie do zabezpieczeń usługi Apache Hadoop z pakietem Enterprise Security)](apache-domain-joined-introduction.md)

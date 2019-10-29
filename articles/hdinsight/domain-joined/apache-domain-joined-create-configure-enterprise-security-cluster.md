---
title: Tworzenie, konfigurowanie klastrów pakiet Enterprise Security — platforma Azure
description: Informacje na temat tworzenia i konfigurowania klastrów pakiet Enterprise Security w usłudze Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 6247a6b2eeeb421773400cc60d05696f973a1dff
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044688"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight

Pakiet Enterprise Security (ESP) dla usługi Azure HDInsight zapewnia dostęp do uwierzytelniania opartego na Active Directoryach, pomocy technicznej dla wieloużytkownika i kontroli dostępu opartej na rolach dla klastrów Apache Hadoop na platformie Azure. Klastry usługi HDInsight ESP umożliwiają organizacjom, które stosują ścisłe zasady zabezpieczeń firmowych w celu bezpiecznego przetwarzania poufnych danych.

W tym przewodniku pokazano, jak utworzyć klaster usługi Azure HDInsight z włączoną obsługą ESP. Przedstawiono w nim również, jak utworzyć maszynę wirtualną z systemem Windows IaaS, na Active Directory której jest włączony system nazw domen (DNS). Ten przewodnik umożliwia skonfigurowanie zasobów niezbędnych do zalogowania się lokalnych użytkowników do klastra usługi HDInsight z włączoną funkcją ESP.

Tworzony serwer działa jako zamiennik dla *rzeczywistego* środowiska lokalnego. Zostanie ona użyta na potrzeby instalacji i konfiguracji. W dalszej części tego procesu będziesz powtarzać kroki w Twoim środowisku. 

Ten przewodnik pomoże również utworzyć hybrydowe środowisko tożsamości przy użyciu funkcji synchronizacji skrótów haseł z Azure Active Directory (Azure AD). Przewodnik uzupełnia [użycie ESP w usłudze HDInsight](apache-domain-joined-architecture.md).

Przed rozpoczęciem korzystania z tego procesu w Twoim środowisku:
* Skonfiguruj Active Directory i system DNS.
* Włącz usługę Azure AD.
* Synchronizowanie lokalnych kont użytkowników z usługą Azure AD.

![Diagram architektury usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Tworzenie środowiska lokalnego

W tej sekcji zostanie użyty szablon wdrożenia szybkiego startu platformy Azure w celu utworzenia nowych maszyn wirtualnych, skonfigurowania systemu DNS i dodania nowego lasu Active Directory.

1. Przejdź do szablonu wdrażania szybkiego startu, aby [utworzyć maszynę wirtualną platformy Azure z nowym lasem Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Wybierz pozycję **Wdróż na platformie Azure**.
1. Zaloguj się do subskrypcji platformy Azure.
1. Na stronie **Tworzenie maszyny wirtualnej platformy Azure z nowym lasem usługi AD** :
    1. Z listy rozwijanej **subskrypcja** wybierz subskrypcję, w której chcesz wdrożyć zasoby.
    1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *OnPremADVRG*.
    1. W pozostałej części pól szablonu wprowadź następujące szczegóły:

        * **Lokalizacja**: środkowe stany USA
        * **Nazwa użytkownika administratora**: HDIFabrikamAdmin
        * **Hasło administratora**: \<YOUR_PASSWORD >
        * **Nazwa domeny**: HDIFabrikam.com
        * **Prefiks DNS**: hdifabrikam

        ![Szablon dotyczący tworzenia maszyny wirtualnej platformy Azure z nowym lasem usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Wybierz pozycję **Kup**.
    1. Monitoruj wdrożenie i poczekaj na jego zakończenie.
    1. Upewnij się, że zasoby są tworzone pod poprawną grupą zasobów, **OnPremADVRG**.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurowanie użytkowników i grup na potrzeby dostępu do klastra

W tej sekcji utworzysz użytkowników, którzy będą mieli dostęp do klastra usługi HDInsight na końcu tego przewodnika.

1. Połącz się z kontrolerem domeny za pomocą Pulpit zdalny.
    1. Jeśli został użyty szablon wymieniony na początku, kontroler domeny jest maszyną wirtualną o nazwie **adVM** w grupie zasobów **OnPremADVRG** .
    1. W Azure Portal wybierz pozycję **grupy zasobów** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Wybierz kartę **rdp** > **Pobierz plik RDP**.
    1. Zapisz plik na komputerze i otwórz go.
    1. Po wyświetleniu monitu o poświadczenia Użyj *HDIFabrikam\HDIFabrikamAdmin* jako nazwy użytkownika. Następnie wprowadź hasło wybrane dla konta administratora.

1. Po otwarciu sesji Pulpit zdalny na maszynie wirtualnej kontrolera domeny na pulpicie nawigacyjnym **Menedżer serwera** Otwórz **Active Directory Użytkownicy i komputery**. W prawym górnym rogu wybierz pozycję **narzędzia** > **Active Directory Użytkownicy i komputery**.

    ![Na pulpicie nawigacyjnym Menedżer serwera Otwórz Active Directory Zarządzanie](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Utwórz dwóch nowych użytkowników: **HDIAdmin** i **HDIUser**. Ci dwaj użytkownicy będą logować się do klastrów usługi HDInsight.

    1. Na stronie **Active Directory Użytkownicy i komputery** wybierz pozycję **Akcja** > **Nowy** > **użytkownika**.

        ![Utwórz nowego użytkownika Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na stronie **nowy obiekt — użytkownik** w polu **Nazwa logowania użytkownika**wprowadź *HDIUser*. Następnie wybierz przycisk **Dalej**.

        ![Tworzenie pierwszego obiektu użytkownika administracyjnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. W wyświetlonym oknie podręcznym wprowadź hasło dla nowego konta. Wybierz pozycję **hasło nigdy nie wygasa** > **OK**.
    1. Wybierz pozycję **Zakończ** , aby utworzyć nowe konto.
    1. Utwórz innego użytkownika o nazwie *HDIAdmin*.

        ![Tworzenie drugiego obiektu użytkownika administracyjnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Na stronie **Active Directory Użytkownicy i komputery** wybierz pozycję **Akcja** > **Nowa** **Grupa** > . Dodaj grupę o nazwie *HDIUserGroup*.

    ![Utwórz nową grupę Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Utwórz nowy obiekt](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Dodaj **HDIUser** do **HDIUserGroup** jako członka grupy.

    1. Kliknij prawym przyciskiem myszy pozycję **HDIUserGroup** i wybierz pozycję **Właściwości**.
    1. Na karcie **Członkowie** wybierz pozycję **Dodaj**.
    1. W polu **Wprowadź nazwy obiektów do wybrania** wprowadź *HDIUser*. Następnie wybierz przycisk **OK**.
    1. Powtórz poprzednie kroki dla konta **HDIAdmin** .

        ![Dodaj element członkowski HDIUser do grupy HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Środowisko Active Directory zostało utworzone. Dodano dwóch użytkowników i grupę użytkowników, którzy mogą uzyskiwać dostęp do klastra usługi HDInsight.

Użytkownicy będą synchronizowani z usługą Azure AD.

### <a name="create-an-azure-ad-directory"></a>Tworzenie katalogu usługi Azure AD

1. Zaloguj się do Portalu Azure.
1. Wybierz pozycję **Utwórz zasób** i wpisz *katalog*. Wybierz pozycję **Azure Active Directory** > **Utwórz**.
1. W obszarze **Nazwa organizacji**wprowadź *HDIFabrikam*.
1. W obszarze **początkowa nazwa domeny**wprowadź *HDIFabrikamoutlook*.
1. Wybierz pozycję **Utwórz**.

    ![Tworzenie katalogu usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. Po lewej stronie Azure Portal wybierz pozycję **Azure Active Directory**.
1. W razie potrzeby wybierz pozycję **Przełącz katalog** , aby przejść do nowego katalogu **HDIFabrikamoutlook** .
1. W obszarze **Zarządzaj**wybierz opcję **niestandardowe nazwy domen** > **Dodaj domenę niestandardową**.
1. W obszarze **nazwa domeny niestandardowej**wpisz *HDIFabrikam.com* i wybierz pozycję **Dodaj domenę**.

![Utwórz domenę niestandardową](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurowanie dzierżawy usługi Azure AD

Teraz skonfigurujesz dzierżawę usługi Azure AD, aby można było synchronizować użytkowników i grupy z wystąpienia Active Directory lokalnego z chmurą.

1. Utwórz Active Directory administratora dzierżawy.
    1. Zaloguj się do Azure Portal i wybierz dzierżawę usługi Azure AD, **HDIFabrikam**.
    1. W obszarze **Zarządzanie**wybierz pozycję **Użytkownicy** > **nowy użytkownik**.
    1. Wprowadź następujące szczegóły dotyczące nowego użytkownika:
        * **Nazwa**: fabrikamazureadmin
        * **Nazwa użytkownika**: fabrikamazureadmin@hdifabrikam.com
        * **Hasło**: bezpieczne hasło

    1. W sekcji **grupy** Wyszukaj **administratorów kontrolera domeny usługi AAD** , a następnie kliknij przycisk **Wybierz**.

        ![Okno dialogowe grupy usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Otwórz sekcję **rola katalogu** i po prawej stronie wybierz pozycję **administrator globalny** > **OK**.

        ![Okno dialogowe Rola usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Wprowadź hasło dla użytkownika. Następnie wybierz przycisk **Utwórz**.

1. Jeśli chcesz zmienić hasło dla nowo utworzonego \<użytkownikafabrikamazureadmin@hdifabrikam.com>, Użyj tożsamości, aby zalogować się do Azure Portal. Następnie zostanie wyświetlony monit o zmianę hasła.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizuj użytkowników lokalnych z usługą Azure AD

### <a name="download-and-install-azure-ad-connect"></a>Pobieranie i instalowanie programu Azure AD Connect

1. [Pobierz Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Zainstaluj Azure AD Connect na kontrolerze domeny.

    1. Otwórz pobrany plik wykonywalny i zaakceptuj postanowienia licencyjne. Wybierz przycisk **Kontynuuj**.

        ![Strona "Zapraszamy do Azure AD Connect"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Wybierz pozycję **Użyj ustawień ekspresowych** i Ukończ instalację.

        ![Azure AD Connect ustawień ekspresowych](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Konfigurowanie synchronizacji z lokalnym kontrolerem domeny

1. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło administratora globalnego usługi Azure AD. Użyj nazwy użytkownika `fabrikamazureadmin@hdifabrikam.com`, która została utworzona podczas konfigurowania dzierżawy Active Directory. Następnie wybierz przycisk **Dalej**. 

    ![Strona "łączenie z usługą Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na stronie **Połącz z Active Directory Domain Services** wprowadź nazwę użytkownika i hasło dla konta administratora przedsiębiorstwa. Użyj nazwy użytkownika `HDIFabrikam\HDIFabrikamAdmin` i utworzonego wcześniej hasła. Następnie wybierz przycisk **Dalej**. 

   ![Strona "łączenie z usługą Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na stronie **Konfiguracja logowania usługi Azure AD** wybierz pozycję **dalej**.
   ![strony "Konfiguracja logowania za pomocą usługi Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Na stronie **gotowy do skonfigurowania** wybierz pozycję **Zainstaluj**.

   ![Strona "gotowe do konfiguracji"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na stronie **Konfiguracja ukończona** wybierz pozycję **Zakończ**.
   ![strony "Konfiguracja ukończona"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Po zakończeniu synchronizacji upewnij się, że użytkownicy utworzeni w katalogu IaaS są zsynchronizowani z usługą Azure AD.
   1. Zaloguj się do Portalu Azure.
   1. Wybierz **Azure Active Directory** > **HDIFabrikam** > **użytkowników**.

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Tworzenie tożsamości zarządzanej przypisanej przez użytkownika, której można użyć do skonfigurowania Azure AD Domain Services (AD DS platformy Azure). Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Zaloguj się do Portalu Azure.
1. Wybierz pozycję **Utwórz zasób** i wpisz *tożsamość zarządzana*. Wybierz opcję **tożsamość zarządzana przypisana przez użytkownika** > **Utwórz**.
1. W polu **nazwa zasobu**wprowadź *HDIFabrikamManagedIdentity*.
1. Wybierz subskrypcję.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** i wprowadź *HDIFabrikam-środkowe*.
1. W obszarze **Lokalizacja**wybierz pozycję **środkowe stany USA**.
1. Wybierz pozycję **Utwórz**.

![Tworzenie nowej tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Włącz AD DS platformy Azure

Wykonaj następujące kroki, aby włączyć usługę Azure AD DS. Aby uzyskać więcej informacji, zobacz [Włączanie usługi Azure AD DS przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Utwórz sieć wirtualną, aby hostować AD DS platformy Azure. Uruchom następujący kod programu PowerShell.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Zaloguj się do Portalu Azure.
1. Wybierz pozycję **Utwórz zasób**, wprowadź pozycję *usługi domenowe*, a następnie wybierz pozycję **Azure AD Domain Services**.
1. Na stronie **podstawowe** :
    1. W obszarze **Nazwa katalogu**wybierz utworzony katalog usługi Azure AD: **HDIFabrikam**.
    1. W obszarze **nazwa domeny DNS**wprowadź *HDIFabrikam.com*.
    1. Wybierz subskrypcję.
    1. Określ grupę zasobów **HDIFabrikam-środkowe**. W obszarze **Lokalizacja**wybierz pozycję **środkowe stany USA**.

        ![Podstawowe informacje dotyczące usługi Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na stronie **Sieć** wybierz sieć (**HDIFabrikam-VNET**) i podsieć (**AADDS-Subnet**), która została utworzona przy użyciu skryptu programu PowerShell. Lub wybierz pozycję **Utwórz nową** , aby teraz utworzyć sieć wirtualną.

    ![Krok "Utwórz sieć wirtualną"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na stronie **Grupa administratorów** powinna zostać wyświetlona powiadomienie z informacją o tym, że grupa o nazwie **Administratorzy domeny usługi AAD** została już utworzona w celu administrowania tą grupą. Możesz zmodyfikować członkostwo w tej grupie, jeśli chcesz, ale w tym przypadku nie musisz go zmieniać. Kliknij przycisk **OK**.

    ![Wyświetlanie grupy administratorów usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na stronie **Synchronizacja** Włącz pełną synchronizację, wybierając pozycję **wszystkie** > **OK**.

    ![Włączanie synchronizacji usługi Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na stronie **Podsumowanie** sprawdź szczegóły AD DS platformy Azure i wybierz **przycisk OK**.

    ![Podsumowanie "Włącz Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Po włączeniu usługi Azure AD DS lokalny serwer DNS jest uruchamiany na maszynach wirtualnych usługi Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Skonfiguruj sieć wirtualną AD DS platformy Azure

Wykonaj następujące kroki, aby skonfigurować sieć wirtualną usługi Azure AD DS (**HDIFabrikam-AADDSVNET**) do korzystania z niestandardowych serwerów DNS.

1. Zlokalizuj adresy IP niestandardowych serwerów DNS. 
    1. Wybierz zasób AD DS platformy Azure **HDIFabrikam.com** . 
    1. W obszarze **Zarządzaj**wybierz pozycję **Właściwości**. 
    1. Znajdź adresy IP w obszarze **adres IP w sieci wirtualnej**.

    ![Lokalizowanie niestandardowych adresów IP DNS dla usługi Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Skonfiguruj **HDIFabrikam-AADDSVNET** , aby używać niestandardowych adresów IP 10.0.0.4 i 10.0.0.5.

    1. W obszarze **Ustawienia**wybierz pozycję **serwery DNS**. 
    1. Wybierz pozycję **niestandardowy**.
    1. W polu tekstowym wprowadź pierwszy adres IP (*10.0.0.4*).
    1. Wybierz pozycję **Zapisz**.
    1. Powtórz kroki, aby dodać inny adres IP (*10.0.0.5*).

W naszym scenariuszu skonfigurujemy AD DS platformy Azure do używania adresów IP 10.0.0.4 i 10.0.0.5, ustawiając ten sam adres IP w sieci wirtualnej Azure AD DS:

![Strona niestandardowe serwery DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Zabezpieczanie ruchu LDAP

Protokół LDAP (Lightweight Directory Access Protocol) służy do odczytu i zapisu w Azure Active Directory. Istnieje możliwość poufności i bezpiecznego ruchu LDAP przy użyciu technologii SSL (SSL) lub Transport Layer Security (TLS). Protokół LDAP można włączyć za pośrednictwem protokołu SSL (LDAPs) przez zainstalowanie prawidłowo sformatowanego certyfikatu.

Aby uzyskać więcej informacji na temat bezpiecznego protokołu LDAP, zobacz [Konfigurowanie LDAPS dla domeny zarządzanej AD DS platformy Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

W tej sekcji utworzysz certyfikat z podpisem własnym, pobierzesz certyfikat i skonfigurujesz LDAPs dla domeny zarządzanej usługi **HDIFabrikam** Azure AD DS.

Poniższy skrypt tworzy certyfikat dla **HDIFabrikam**. Certyfikat jest zapisywany w ścieżce *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> Do utworzenia żądania certyfikatu protokołu SSL można użyć dowolnego narzędzia lub aplikacji, która tworzy prawidłowe żądanie \#10.

Sprawdź, czy certyfikat został zainstalowany w **osobistym** magazynie komputera:

1. Uruchom program Microsoft Management Console (MMC).
1. Dodaj przystawkę **Certyfikaty** , która zarządza certyfikatami na komputerze lokalnym.
1. Rozwiń węzeł **Certyfikaty (komputer lokalny)**  > **osobiste** **Certyfikaty** > . W magazynie **osobistym** powinien istnieć nowy certyfikat. Ten certyfikat jest wystawiony dla w pełni kwalifikowanej nazwy hosta.

    ![Weryfikowanie tworzenia certyfikatu lokalnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. W okienku po prawej stronie kliknij prawym przyciskiem myszy utworzony certyfikat. Wskaż **wszystkie zadania**, a następnie wybierz pozycję **Eksportuj**.

1. Na stronie **Eksportowanie klucza prywatnego** wybierz opcję **tak, eksportuj klucz prywatny**. Komputer, na którym zostanie zaimportowany klucz, musi mieć klucz prywatny odczytywanie zaszyfrowanych wiadomości.

    ![Strona eksportowanie klucza prywatnego Kreatora eksportu certyfikatów](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na stronie **Format pliku eksportu** Pozostaw ustawienia domyślne, a następnie wybierz przycisk **dalej**. 
1. Na stronie **hasło** wpisz hasło klucza prywatnego. W obszarze **szyfrowanie**wybierz pozycję **TripleDES-SHA1**. Następnie wybierz przycisk **Dalej**.
1. Na stronie **Eksportuj plik** wpisz ścieżkę i nazwę wyeksportowanego pliku certyfikatu, a następnie wybierz przycisk **dalej**. Nazwa pliku musi mieć rozszerzenie PFX. Ten plik jest skonfigurowany w Azure Portal w celu nawiązania bezpiecznego połączenia.
1. Włącz LDAPs dla domeny zarządzanej AD DS platformy Azure.
    1. Na Azure Portal wybierz domenę **HDIFabrikam.com**.
    1. W obszarze **Zarządzaj**wybierz pozycję **Secure LDAP**.
    1. Na stronie **Secure LDAP** w obszarze **Secure LDAP**wybierz pozycję **Włącz**.
    1. Wyszukaj plik certyfikatu PFX wyeksportowany na komputerze.
    1. Wprowadź hasło certyfikatu.

    ![Włącz bezpieczny protokół LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Po włączeniu protokołów LDAP Upewnij się, że jest dostępna przez włączenie portu 636.
    1. W grupie zasobów **HDIFabrikam-środkowe** wybierz grupę zabezpieczeń sieci **AADDS-HDIFabrikam.com-sieciowej grupy zabezpieczeń**.
    1. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego** > **Dodaj**.
    1. Na stronie **Dodawanie reguły zabezpieczeń ruchu przychodzącego** wprowadź następujące właściwości, a następnie wybierz pozycję **Dodaj**:

        | Właściwość | Wartość |
        |---|---|
        | Źródło | Dowolne |
        | Zakresy portów źródłowych | * |
        | Cel | Dowolne |
        | Zakres portów docelowych | 636 |
        | Protocol (Protokół) | Dowolne |
        | Działanie | Zezwól |
        | Priorytet | \<żądany numer > |
        | Nazwa | Port_LDAP_636 |

    ![Okno dialogowe Dodawanie reguły zabezpieczeń ruchu przychodzącego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** to zarządzana tożsamość przypisana przez użytkownika. Rola współautor usług domenowych w usłudze HDInsight jest włączona dla tożsamości zarządzanej, która będzie zezwalać tej tożsamości na odczytywanie, tworzenie, modyfikowanie i usuwanie usług domenowych.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Tworzenie klastra usługi HDInsight z obsługą ESP

Ten krok wymaga następujących wymagań wstępnych:

1. Utwórz nową grupę zasobów *HDIFabrikam-zachodni* w lokalizacji **zachodnie stany USA**.
1. Utwórz sieć wirtualną, która będzie hostować klaster usługi HDInsight z obsługą ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Utwórz relację równorzędną między siecią wirtualną, która hostuje AD DS platformy Azure (`HDIFabrikam-AADDSVNET`) i sieci wirtualnej, która będzie hostować klaster usługi HDInsight z włączoną funkcją ESP (`HDIFabrikam-HDIVNet`). Użyj poniższego kodu programu PowerShell do komunikacji równorzędnej z dwiema sieciami wirtualnymi.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Utwórz nowe konto Azure Data Lake Storage Gen2 o nazwie **Hdigen2store**. Skonfiguruj konto przy użyciu tożsamości zarządzanej przez użytkownika **HDIFabrikamManagedIdentity**. Aby uzyskać więcej informacji, zobacz [używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Skonfiguruj niestandardowy serwer DNS w sieci wirtualnej **HDIFabrikam-AADDSVNET** .
    1. Przejdź do Azure Portal > **grup zasobów** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **serwerów DNS**.
    1. Wybierz pozycję **niestandardowe** i wprowadź *10.0.0.4* i *10.0.0.5*.
    1. Wybierz pozycję **Zapisz**.

        ![Zapisz niestandardowe ustawienia DNS dla sieci wirtualnej](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Utwórz nowy klaster usługi HDInsight Spark z włączoną obsługą ESP.
    1. Wybierz pozycję **niestandardowy (rozmiar, ustawienia, aplikacje)** .
    2. Wprowadź szczegóły dotyczące **podstaw** (sekcja 1). Upewnij się, że **Typ klastra** to **Spark 2,3 (HDI 3,6)** . Upewnij się, że **Grupa zasobów** to **HDIFabrikam-środkowe**.

    1. W przypadku **zabezpieczeń i sieci** (sekcja 2) wprowadź następujące informacje:
        * W obszarze **pakiet Enterprise Security**wybierz pozycję **włączone**.
        * Wybierz pozycję **administrator klastra** i wybierz konto **HDIAdmin** utworzone jako administrator lokalny. Kliknij pozycję **Wybierz**.
        * Wybierz pozycję **Grupa dostępu do klastra** > **HDIUserGroup**. Każdy użytkownik dodany do tej grupy w przyszłości będzie mógł uzyskać dostęp do klastrów usługi HDInsight.

            ![Wybierz grupę dostępu do klastra HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Wykonaj pozostałe kroki konfiguracji klastra i sprawdź szczegóły dotyczące **podsumowania klastra**. Wybierz pozycję **Utwórz**.

1. Zaloguj się do interfejsu użytkownika Ambari dla nowo utworzonego klastra w `https://CLUSTERNAME.azurehdinsight.net`. Użyj nazwy użytkownika administratora `hdiadmin@hdifabrikam.com` i jego hasła.

    ![Okno logowania interfejsu użytkownika Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Na pulpicie nawigacyjnym klastra wybierz pozycję **role**.
1. Na stronie **role** , w obszarze **Przypisz role do tych**, obok roli **administrator klastra** wprowadź grupę *hdiusergroup*. 

    ![Przypisywanie roli administratora klastra do hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Otwórz klienta Secure Shell (SSH) i zaloguj się w klastrze. Użyj **hdiuser** , który został utworzony w lokalnym wystąpieniu Active Directory.

    ![Logowanie do klastra przy użyciu klienta SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Jeśli możesz zalogować się przy użyciu tego konta, klaster ESP został prawidłowo skonfigurowany do synchronizacji z lokalnym wystąpieniem Active Directory.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [wprowadzeniem do Apache Hadoop zabezpieczenia przy użyciu protokołu ESP](hdinsight-security-overview.md).

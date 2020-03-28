---
title: Samouczek — konfigurowanie usługi LDAPS dla usług domenowych Active Directory platformy Azure | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak skonfigurować bezpieczny protokół dostępu do LDAPS (LDAPS) dla domeny zarządzanej usług domenowych Usługi active directory platformy Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 6db2c907abc495ca3c88e1e73e885043a8f19997
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481538"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Samouczek: Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej usług domenowych usługi active directory platformy Azure

Aby komunikować się z domeną zarządzaną usług domenowych Usługi active directory platformy Azure (Usługi Azure AD DS), używany jest protokół LDAP (Lightweight Directory Access Protocol). Domyślnie ruch LDAP nie jest szyfrowany, co stanowi problem bezpieczeństwa dla wielu środowisk. Za pomocą usługi Azure AD DS można skonfigurować domenę zarządzalącą tak, aby używała bezpiecznego protokołu LDAPS (Lightweight Directory Access Protocol). Podczas korzystania z bezpiecznego protokołu LDAP ruch jest szyfrowany. Secure LDAP jest również znany jako LDAP over Secure Sockets Layer (SSL) / Transport Layer Security (TLS).

W tym samouczku pokazano, jak skonfigurować usługę LDAPS dla domeny zarządzanej usług Azure AD DS.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu cyfrowego do użytku z usługą Azure AD DS
> * Włączanie bezpiecznego protokołu LDAP dla usług Azure AD DS
> * Konfigurowanie bezpiecznego protokołu LDAP do użytku przez publiczny Internet
> * Powiąż i przetestuj bezpieczną usługę LDAP dla domeny zarządzanej usług Azure AD DS

Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance].
* Narzędzie *LDP.exe* zainstalowane na komputerze.
    * W razie potrzeby [zainstaluj narzędzia RSAT (Remote Server Administration Tools)][rsat] dla *Usług domenowych Active Directory i LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku skonfigurujesz bezpieczny LDAP dla domeny zarządzanej usługi Azure AD DS przy użyciu witryny Azure portal. Aby rozpocząć, najpierw zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Tworzenie certyfikatu dla bezpiecznego protokołu LDAP

Aby użyć bezpiecznego protokołu LDAP, do szyfrowania komunikacji używany jest certyfikat cyfrowy. Ten certyfikat cyfrowy jest stosowany do domeny zarządzanej usługi Azure AD DS i umożliwia narzędziom takim jak *LDP.exe* używanie bezpiecznej szyfrowanej komunikacji podczas wykonywania zapytań o dane. Istnieją dwa sposoby tworzenia certyfikatu bezpiecznego dostępu LDAP do domeny zarządzanej:

* Certyfikat z publicznego urzędu certyfikacji (CA) lub urzędu certyfikacji przedsiębiorstwa.
    * Jeśli twoja organizacja pobiera certyfikaty od publicznego urzędu certyfikacji, uzyskaj bezpieczny certyfikat LDAP od tego publicznego urzędu certyfikacji. Jeśli używasz urzędu certyfikacji przedsiębiorstwa w organizacji, pobierz bezpieczny certyfikat LDAP od urzędu certyfikacji przedsiębiorstwa.
    * Publiczny urząd certyfikacji działa tylko wtedy, gdy używasz niestandardowej nazwy DNS z domeną zarządzaną usługą Azure AD DS. Jeśli nazwa domeny zarządzanej DNS domeny zarządzanej kończy się na *onmicrosoft.com,* nie można utworzyć certyfikatu cyfrowego w celu zabezpieczenia połączenia z tą domeną domyślną. Firma Microsoft jest właścicielem domeny *onmicrosoft.com,* więc publiczny urząd certyfikacji nie wystawi certyfikatu. W tym scenariuszu utwórz certyfikat z podpisem własnym i użyj go do skonfigurowania bezpiecznego protokołu LDAP.
* Certyfikat z podpisem własnym, który tworzysz samodzielnie.
    * To podejście jest dobre do celów testowych i jest to, co pokazuje ten samouczek.

Wymagany lub utworzony certyfikat musi spełniać następujące wymagania. Domena zarządzana napotyka problemy, jeśli włączysz bezpieczną usługę LDAP z nieprawidłowym certyfikatem:

* **Zaufany wystawca** — certyfikat musi być wystawiony przez urząd zaufany przez komputery łączące się z domeną zarządzaną przy użyciu bezpiecznego protokołu LDAP. Ten urząd może być publicznym urzędem certyfikacji lub urzędem certyfikacji przedsiębiorstwa, któremu zaufały te komputery.
* **Okres istnienia** — certyfikat musi być ważny przez co najmniej następne 3-6 miesięcy. Bezpieczny dostęp LDAP do domeny zarządzanej zostanie zakłócony po wygaśnięciu certyfikatu.
* **Nazwa podmiotu** — nazwa podmiotu na certyfikacie musi być domeną zarządzaną. Na przykład, jeśli domena ma nazwę *aaddscontoso.com,* nazwa podmiotu certyfikatu musi być **.aaddscontoso.com*.
    * Nazwa DNS lub nazwa alternatywna podmiotu certyfikatu musi być certyfikatem wieloznacznym, aby upewnić się, że bezpieczny protokół LDAP działa poprawnie z usługami domenowymi usługi Azure AD. Kontrolery domeny używają losowych nazw i mogą zostać usunięte lub dodane, aby upewnić się, że usługa pozostaje dostępna.
* **Użycie klucza** — certyfikat musi być skonfigurowany pod kątem *podpisów cyfrowych* i *szyfrowania kluczy.*
* **Cel certyfikatu** — certyfikat musi być ważny do uwierzytelniania serwera TLS.

Istnieje kilka narzędzi dostępnych do tworzenia certyfikatów z podpisem własnym, takich jak OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet itp. W tym samouczku utwórzmy certyfikat z podpisem własnym dla bezpiecznego protokołu LDAP przy użyciu polecenia cmdlet [New-SelfSignedCertificate.][New-SelfSignedCertificate] Otwórz okno programu PowerShell jako **administrator** i uruchom następujące polecenia. Zastąp zmienną *$dnsName* nazwą DNS używaną przez własną domenę zarządzaną, taką jak *aaddscontoso.com:*

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Poniższy przykładowy wynik pokazuje, że certyfikat został pomyślnie wygenerowany i jest przechowywany w lokalnym magazynie certyfikatów (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Zrozumienie i eksport wymaganych certyfikatów

Aby korzystać z bezpiecznego protokołu LDAP, ruch sieciowy jest szyfrowany przy użyciu infrastruktury kluczy publicznych (PKI).

* Klucz **prywatny** jest stosowany do domeny zarządzanej usług Azure AD DS.
    * Ten klucz prywatny służy do *odszyfrowywania* bezpiecznego ruchu LDAP. Klucz prywatny powinien być stosowany tylko do domeny zarządzanej usług Azure AD DS i nie jest szeroko rozpowszechniony na komputerach klienckich.
    * Certyfikat zawierający klucz prywatny używa pliku *. *format pliku PFX.
* Klucz **publiczny** jest stosowany do komputerów klienckich.
    * Ten klucz publiczny służy do *szyfrowania* bezpiecznego ruchu LDAP. Klucz publiczny można dystrybuować na komputerach klienckich.
    * Certyfikaty bez klucza prywatnego używają *pliku . *cer.

Te dwa klucze, klucze *prywatne* i *publiczne,* upewnij się, że tylko odpowiednie komputery mogą pomyślnie komunikować się ze sobą. Jeśli używasz publicznego urzędu certyfikacji lub urzędu certyfikacji przedsiębiorstwa, wystawia się certyfikat zawierający klucz prywatny i może być stosowany do domeny zarządzanej usług Azure AD DS. Klucz publiczny powinien być już znany i zaufany przez komputery klienckie. W tym samouczku utworzono certyfikat z podpisem własnym z kluczem prywatnym, więc należy wyeksportować odpowiednie składniki prywatne i publiczne.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Eksportowanie certyfikatu dla usług Ad DS

Aby można było użyć certyfikatu cyfrowego utworzonego w poprzednim kroku z domeną zarządzaną usługą Azure AD DS, wyeksportuj certyfikat do *pliku . *Plik certyfikatu PFX zawierający klucz prywatny.

1. Aby otworzyć okno dialogowe *Uruchom,* wybierz klawisze **Windows** i **R.**
1. Otwórz program Microsoft Management Console (MMC), wprowadzając **program mmc** w oknie dialogowym *Uruchom,* a następnie wybierz przycisk **OK**.
1. W wierszu **Kontrola konta użytkownika** kliknij przycisk **Tak,** aby uruchomić program MMC jako administrator.
1. W menu **Plik** kliknij polecenie **Dodaj/Usuń przystawkę...**
1. W kreatorze **przystawki Certyfikaty** wybierz pozycję **Konto komputera**, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Wybierz komputer** wybierz pozycję **Komputer lokalny: (komputer, na który jest uruchomiona)**, a następnie wybierz pozycję **Zakończ**.
1. W oknie dialogowym **Dodawanie lub usuwanie przystawek** kliknij przycisk **OK,** aby dodać przystawkę certyfikaty do programu MMC.
1. W oknie programu MMC rozwiń pozycję **Katalog główny konsoli**. Wybierz **pozycję Certyfikaty (komputer lokalny),** a następnie rozwiń węzeł **osobisty,** a następnie węzeł **Certyfikaty.**

    ![Otwieranie magazynu certyfikatów osobistych w programie Microsoft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Zostanie wyświetlony certyfikat z podpisem własnym utworzony w poprzednim kroku, na przykład *aaddscontoso.com*. Wybierz ten certyfikat z prawej strony, a następnie wybierz pozycję **Wszystkie zadania > eksportu...**

    ![Certyfikat eksportu w programie Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. W **Kreatorze eksportu certyfikatów**wybierz pozycję **Dalej**.
1. Klucz prywatny certyfikatu musi zostać wyeksportowany. Jeśli klucz prywatny nie jest uwzględniony w wyeksportowanym certyfikacie, akcja umożliwiająca bezpieczne LDAP dla domeny zarządzanej nie powiedzie się.

    Na stronie **Eksportowanie klucza prywatnego** wybierz pozycję **Tak, eksportuj klucz prywatny**, a następnie wybierz pozycję **Dalej**.
1. Domeny zarządzane usług Azure AD DS obsługują tylko *domenę . *Format pliku certyfikatu PFX zawierający klucz prywatny. Nie eksportuj certyfikatu jako *. Cer format* pliku bez klucza prywatnego.

    Na stronie **Eksportuj format pliku** wybierz pozycję **Wymiana informacji osobistych — PKCS #12 (. PFX)** jako format pliku wyeksportowanego certyfikatu. Zaznacz pole wyboru *Uwzględnij wszystkie certyfikaty w ścieżce certyfikacji, jeśli to możliwe:*

    ![Wybierz opcję wyeksportowania certyfikatu w PKCS 12 (. PFX) format pliku](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Ponieważ ten certyfikat jest używany do odszyfrowywania danych, należy dokładnie kontrolować dostęp. Hasło może służyć do ochrony korzystania z certyfikatu. Bez poprawnego hasła certyfikatu nie można zastosować do usługi.

    Na stronie **Zabezpieczenia** wybierz opcję **Hasło,** aby chronić *plik . *plik certyfikatu PFX. Wprowadź i potwierdź hasło, a następnie wybierz przycisk **Dalej**. To hasło jest używane w następnej sekcji, aby włączyć bezpieczne LDAP dla domeny zarządzanej usług Azure AD DS.
1. Na stronie **Plik do eksportu** określ nazwę pliku i lokalizację, w której chcesz wyeksportować certyfikat, na przykład *C:\Users\accountname\azure-ad-ds.pfx*. Zanotuj hasło i lokalizację *pliku . PFX,* ponieważ informacje te będą wymagane w następnych krokach.
1. Na stronie recenzji wybierz pozycję **Zakończ,** aby wyeksportować certyfikat do *pliku . *plik certyfikatu PFX. Okno dialogowe potwierdzenia jest wyświetlane po pomyślnym wyeksportowanym certyfikacie.
1. Pozostaw program MMC otwarty do użycia w poniższej sekcji.

### <a name="export-a-certificate-for-client-computers"></a>Eksportowanie certyfikatu dla komputerów klienckich

Komputery klienckie muszą ufać wystawcy bezpiecznego certyfikatu LDAP, aby móc pomyślnie połączyć się z domeną zarządzana przy użyciu protokołu LDAPS. Komputery klienckie potrzebują certyfikatu do pomyślnego szyfrowania danych, które są odszyfrowywane przez usługi Azure AD DS. Jeśli używasz publicznego urzędu certyfikacji, komputer powinien automatycznie ufać tym wystawcom certyfikatów i mieć odpowiedni certyfikat. W tym samouczku używasz certyfikatu z podpisem własnym i wygenerowałeś certyfikat zawierający klucz prywatny w poprzednim kroku. Teraz eksportujmy, a następnie zainstalujmy certyfikat z podpisem własnym do magazynu certyfikatów zaufanych na komputerze klienckim:

1. Wróć do magazynu certyfikatów programu MMC dla *certyfikatów (komputera lokalnego) > magazynu certyfikatów >* osobistych. Zostanie wyświetlony certyfikat z podpisem własnym utworzony w poprzednim kroku, na przykład *aaddscontoso.com*. Wybierz ten certyfikat z prawej strony, a następnie wybierz pozycję **Wszystkie zadania > eksportu...**
1. W **Kreatorze eksportu certyfikatów**wybierz pozycję **Dalej**.
1. Ponieważ klucz prywatny nie jest potrzebny klientom, na stronie **Eksportuj klucz prywatny** wybierz pozycję **Nie, nie eksportuj klucza prywatnego,** a następnie wybierz przycisk **Dalej**.
1. Na stronie **Eksportuj format pliku** wybierz **pozycję Kod X.509 zakodowany w bazie bazowej.64 (. CER)** jako format pliku eksportowanego certyfikatu:

    ![Wybierz opcję wyeksportowania certyfikatu w kodowanym X.509 kodowanym numerze X.509 base-64 (. CER) format pliku](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na stronie **Plik do eksportu** określ nazwę pliku i lokalizację, w której chcesz wyeksportować certyfikat, na przykład *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Na stronie recenzji wybierz pozycję **Zakończ,** aby wyeksportować certyfikat do *pliku . *cer. Okno dialogowe potwierdzenia jest wyświetlane po pomyślnym wyeksportowanym certyfikacie.

W *. *Plik certyfikatu CER można teraz dystrybuować do komputerów klienckich, które muszą ufać bezpiecznemu połączeniu LDAP z domeną zarządza zarządza zarządza zarządzana usługą Azure AD DS. Zainstalujmy certyfikat na komputerze lokalnym.

1. Otwórz Eksploratora plików i przejdź do lokalizacji, w której został zapisany *plik . Cer,* na przykład *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Wybierz prawym przyciskiem *wyboru . cer,* a następnie wybierz polecenie **Zainstaluj certyfikat**.
1. W **Kreatorze importu certyfikatów**wybierz opcję przechowywania certyfikatu na *komputerze lokalnym,* a następnie wybierz pozycję **Dalej:**

    ![Wybierz opcję zaimportowania certyfikatu do lokalnego magazynu maszyn](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Po wyświetleniu monitu wybierz pozycję **Tak,** aby zezwolić komputerowi na wprowadzanie zmian.
1. Wybierz opcję **Automatycznie wybierz magazyn certyfikatów na podstawie typu certyfikatu,** a następnie wybierz pozycję **Dalej**.
1. Na stronie recenzji wybierz pozycję **Zakończ,** aby zaimportować *plik . certyfikat CER.* plik Okno dialogowe potwierdzenia jest wyświetlane po pomyślnym zaimportowaniu certyfikatu.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Włączanie bezpiecznego protokołu LDAP dla usług Azure AD DS

Po utworzeniu i wyeksportowanym certyfikatie cyfrowym zawierającym klucz prywatny i komputerze klienckim ustawionym na zaufanie połączeniu włącz teraz bezpieczną usługę LDAP w domenie zarządzanej usług Azure AD DS. Aby włączyć bezpieczne ldap w domenie zarządzanej usług Azure AD DS, wykonaj następujące kroki konfiguracji:

1. W [witrynie Azure portal](https://portal.azure.com)wprowadź *usługi domeny* w polu **Zasoby wyszukiwania.** Wybierz **usługi domenowe usługi Azure AD** z wyniku wyszukiwania.

    ![Wyszukiwanie i wybieranie domeny zarządzanej usług Azure AD DS w witrynie Azure portal](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. Wybierz domenę zarządzana, na przykład *aaddscontoso.com*.
1. Po lewej stronie okna usług Ad DS wybierz pozycję **Secure LDAP**.
1. Domyślnie bezpieczny dostęp LDAP do domeny zarządzanej jest wyłączony. Przełącz **secure LDAP,** aby **włączyć**.
1. Bezpieczny dostęp LDAP do domeny zarządzanej przez Internet jest domyślnie wyłączony. Po włączeniu publicznego bezpiecznego dostępu LDAP domena jest podatna na ataki siłowe hasłem przez Internet. W następnym kroku sieciowa grupa zabezpieczeń jest skonfigurowana do blokowania dostępu tylko do wymaganych zakresów źródłowych adresów IP.

    Przełącz **Zezwalaj na bezpieczny dostęp LDAP przez Internet,** aby **włączyć**.

1. Wybierz ikonę folderu obok **pozycji . PFX z bezpiecznym certyfikatem LDAP**. Przejdź do ścieżki pliku *. PFX,* a następnie wybierz certyfikat utworzony w poprzednim kroku, który zawiera klucz prywatny.

    Jak wspomniano w poprzedniej sekcji dotyczącej wymagań dotyczących certyfikatów, nie można używać certyfikatu z publicznego urzędu certyfikacji z domyślną domeną *onmicrosoft.com.* Firma Microsoft jest właścicielem domeny *onmicrosoft.com,* więc publiczny urząd certyfikacji nie wystawi certyfikatu. Upewnij się, że certyfikat jest w odpowiednim formacie. Jeśli tak nie jest, platforma Azure generuje błędy sprawdzania poprawności certyfikatów po włączeniu bezpiecznego protokołu LDAP.

1. Wprowadź **hasło, aby odszyfrować . PFX** zestaw w poprzednim kroku, gdy certyfikat został wyeksportowany do *. pfx.*
1. Wybierz **pozycję Zapisz,** aby włączyć bezpieczną usługę LDAP.

    ![Włączanie bezpiecznego protokołu LDAP dla domeny zarządzanej usług Azure AD DS w witrynie Azure portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Zostanie wyświetlone powiadomienie, że dla domeny zarządzanej jest konfigurowany bezpieczny LDAP. Nie można zmodyfikować innych ustawień domeny zarządzanej, dopóki ta operacja nie zostanie zakończona.

Włączenie bezpiecznego protokołu LDAP dla domeny zarządzanej zajmuje kilka minut. Jeśli podasz bezpieczny certyfikat LDAP nie spełnia wymaganych kryteriów, akcja umożliwiająca bezpieczne LDAP dla domeny zarządzanej nie powiedzie się. Niektóre typowe przyczyny niepowodzenia są, jeśli nazwa domeny jest niepoprawna lub certyfikat wygasa wkrótce lub już wygasł. Można ponownie utworzyć certyfikat z prawidłowymi parametrami, a następnie włączyć bezpieczny LDAP przy użyciu tego zaktualizowanego certyfikatu.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Blokowanie bezpiecznego dostępu LDAP przez Internet

Po włączeniu bezpiecznego dostępu LDAP przez Internet do domeny zarządzanej usług Azure AD DS, tworzy zagrożenie bezpieczeństwa. Domena zarządzana jest osiągalna z Internetu na porcie TCP 636. Zaleca się ograniczenie dostępu do domeny zarządzanej do określonych znanych adresów IP w danym środowisku. Reguła grupy zabezpieczeń sieci platformy Azure może służyć do ograniczania dostępu do bezpiecznego protokołu LDAP.

Utwórzmy regułę zezwalaną na przychodzący bezpieczny dostęp LDAP za sprawą portu TCP 636 z określonego zestawu adresów IP. Domyślna *reguła DenyAll* o niższym priorytecie ma zastosowanie do całego innego ruchu przychodzącego z Internetu, więc tylko określone adresy mogą dotrzeć do domeny zarządzanej usług Azure AD DS przy użyciu bezpiecznego protokołu LDAP.

1. W witrynie Azure portal wybierz *grup zasobów* w nawigacji po lewej stronie.
1. Wybierz grupę zasobów, taką jak *myResourceGroup,* a następnie wybierz grupę zabezpieczeń sieci, taką jak *aaads-nsg*.
1. Wyświetlana jest lista istniejących reguł zabezpieczeń przychodzących i wychodzących. Po lewej stronie okien sieciowej grupy zabezpieczeń wybierz pozycję **Ustawienia > Reguły zabezpieczeń przychodzących**.
1. Wybierz **pozycję Dodaj**, a następnie utwórz regułę zezwalania na port *TCP* *636*. Aby zwiększyć bezpieczeństwo, wybierz źródło jako *adresy IP,* a następnie określ własny prawidłowy adres IP lub zakres dla organizacji.

    | Ustawienie                           | Wartość        |
    |-----------------------------------|--------------|
    | Element źródłowy                            | Adresy IP |
    | Źródłowe adresy IP / zakresy CIDR | Prawidłowy adres IP lub zakres dla twojego środowiska |
    | Zakresy portów źródłowych                | *            |
    | Element docelowy                       | Dowolne          |
    | Zakresy portów docelowych           | 636          |
    | Protocol (Protokół)                          | TCP          |
    | Akcja                            | Zezwalaj        |
    | Priorytet                          | 401          |
    | Nazwa                              | AllowLDAPS   |

1. Gdy będzie gotowy, wybierz pozycję **Dodaj,** aby zapisać i zastosuj regułę.

    ![Tworzenie reguły sieciowej grupy zabezpieczeń w celu zabezpieczenia dostępu LDAPS przez Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Konfigurowanie strefy DNS dla dostępu zewnętrznego

Dzięki aktywnej bezpiecznej usłudze LDAP dostęp w Internecie zaktualizuj strefę DNS, aby komputery klienckie mogły znaleźć tę domenę zarządzalną. *Bezpieczny zewnętrzny adres IP protokołu LDAP* znajduje się na karcie **Właściwości** domeny zarządzanej usług Azure AD DS:

![Wyświetlanie bezpiecznego zewnętrznego adresu IP protokołu LDAP dla domeny zarządzanej usług Azure AD DS w witrynie Azure portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Skonfiguruj zewnętrznego dostawcę DNS, aby utworzyć rekord hosta, taki jak *ldaps*, aby rozwiązać ten zewnętrzny adres IP. Aby najpierw przetestować lokalnie na komputerze, można utworzyć wpis w pliku hostów systemu Windows. Aby pomyślnie edytować plik hosts na komputerze lokalnym, otwórz *Notatnik* jako administrator, a następnie otwórz plik *C:\Windows\System32\drivers\etc*

Poniższy przykładowy wpis DNS z zewnętrznym dostawcą DNS lub w pliku hostów lokalnych rozwiązuje ruch dla *ldaps.aaddscontoso.com* z zewnętrznym adresem IP *40.121.19.239:*

```
40.121.19.239    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testowanie zapytań do domeny zarządzanej

Aby połączyć się i powiązać z domeną zarządzałą usługą Azure AD DS i przeszukiwać za pomocą protokołu LDAP, należy użyć narzędzia *LDP.exe.* To narzędzie znajduje się w pakiecie Narzędzia administracji zdalnej serwera (RSAT). Aby uzyskać więcej informacji, zobacz [instalowanie narzędzi administracji zdalnej serwera][rsat].

1. Otwórz *program LDP.exe* i połącz się z domeną zarządzana. Wybierz **opcję Połączenie**, a następnie wybierz pozycję **Połącz...**.
1. Wprowadź bezpieczną nazwę domeny DNS LDAP domeny zarządzanej utworzoną w poprzednim kroku, na przykład *ldaps.aaddscontoso.com*. Aby użyć bezpiecznego protokołu LDAP, ustaw **port** na *636*, a następnie zaznacz pole wyboru **SSL**.
1. Wybierz **przycisk OK,** aby połączyć się z domeną zarządzana.

Następnie należy powiązać z domeną zarządzana usługą Azure AD DS. Użytkownicy (i konta usług) nie mogą wykonywać prostych powiązań LDAP, jeśli wyłączono synchronizację skrótów haseł NTLM w wystąpieniu usług Azure AD DS. Aby uzyskać więcej informacji na temat wyłączania synchronizacji skrótów haseł NTLM, zobacz [Zabezpieczanie domeny zarządzanej usług Azure AD DS][secure-domain].

1. Wybierz opcję menu **Połączenie,** a następnie wybierz polecenie **Powiąż...**.
1. Podaj poświadczenia konta użytkownika należącego do grupy *Administratorzy kontrolera domeny usługi AAD,* na przykład *contosoadmin*. Wprowadź hasło konta użytkownika, a następnie wprowadź domenę, na przykład *aaddscontoso.com*.
1. W przypadku **typu powiązania**wybierz opcję Bind *z poświadczeniami*.
1. Wybierz **przycisk OK,** aby powiązać z domeną zarządza zarządzana usługą Azure AD DS.

Aby wyświetlić obiekty przechowywane w domenie zarządzanej usług Azure AD DS:

1. Wybierz opcję menu **Widok,** a następnie wybierz polecenie **Drzewo**.
1. Pozostaw pole *BaseDN* puste, a następnie wybierz **przycisk OK**.
1. Wybierz kontener, na przykład *Użytkownicy usługi AADDC,* a następnie wybierz kontener z prawej i wybierz polecenie **Wyszukaj**.
1. Pozostaw wstępnie wypełnione pola, a następnie wybierz pozycję **Uruchom**. Wyniki kwerendy są wyświetlane w oknie po prawej stronie.

    ![Wyszukiwanie obiektów w domenie zarządzanej usług Azure AD DS przy użyciu programu LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Aby bezpośrednio zbadać określony kontener, z menu **Wyświetl > drzewa** można określić numer **BaseDN,** taki jak *OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* lub *OU=AADDC Computers,DC=AADSCONTOSO,DC=COM*. Aby uzyskać więcej informacji na temat formatowania i tworzenia kwerend, zobacz [Podstawowe kwerendy LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli wpis DNS został dodany do pliku hostów lokalnych komputera w celu przetestowania łączności dla tego samouczka, usuń ten wpis i dodaj oficjalny rekord w strefie DNS. Aby usunąć wpis z pliku hostów lokalnych, wykonaj następujące czynności:

1. Na komputerze lokalnym otwórz *Notatnik* jako administrator
1. Przejdź do pliku *C:\Windows\System32\drivers\etc*
1. Usuwanie wiersza dodanego rekordu, takiego jak`40.121.19.239    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu cyfrowego do użytku z usługą Azure AD DS
> * Włączanie bezpiecznego protokołu LDAP dla usług Azure AD DS
> * Konfigurowanie bezpiecznego protokołu LDAP do użytku przez publiczny Internet
> * Powiąż i przetestuj bezpieczną usługę LDAP dla domeny zarządzanej usług Azure AD DS

> [!div class="nextstepaction"]
> [Konfigurowanie synchronizacji skrótów haseł dla hybrydowego środowiska usługi Azure AD](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate

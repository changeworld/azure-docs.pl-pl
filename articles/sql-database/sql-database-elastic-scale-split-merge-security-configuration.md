---
title: Zabezpieczenia dzielenia i scalania konfiguracji | Dokumentacja firmy Microsoft
description: Konfigurowanie x409 certyfikaty szyfrowania przy użyciu usługi dzielenia i scalania kątem elastycznego skalowania.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 7ca7e653cc42323f4313ef955de40416154b4ecf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60335227"
---
# <a name="split-merge-security-configuration"></a>Konfiguracja zabezpieczenia dzielenia i scalania

Aby użyć usługi dzielenia i scalania, należy poprawnie skonfigurować zabezpieczenia. Usługa jest częścią funkcji elastyczne skalowanie systemu Microsoft Azure SQL Database. Aby uzyskać więcej informacji, zobacz [elastycznej podziału skali i scalić samouczek usługi](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurowanie certyfikatów

Certyfikaty są skonfigurowane na dwa sposoby. 

1. [Aby skonfigurować certyfikat SSL](#to-configure-the-ssl-certificate)
2. [Aby skonfigurować certyfikaty klienta](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Uzyskiwanie certyfikatów

Certyfikaty można uzyskać od publicznych urzędów certyfikacji (CA) lub z [Windows certyfikatu usługi](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Są to preferowane metody służące do uzyskania certyfikatów.

Jeśli te opcje nie są dostępne, można wygenerować **certyfikaty z podpisem własnym**.

## <a name="tools-to-generate-certificates"></a>Narzędzia do generowania certyfikatów

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Aby uruchomić narzędzia

* W wierszu polecenia dla deweloperów programu Visual Studio, zobacz [wiersza polecenia programu Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Jeśli zainstalowano, przejdź do:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Pobierz WDK z [Windows 8.1: Pobieranie zestawów i narzędzi](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Aby skonfigurować certyfikat SSL

Certyfikat SSL jest wymagany do szyfrowania komunikacji i uwierzytelniania serwera. Wybierz najbardziej odpowiednią z trzech poniższych scenariuszy, a wykonywanie wszystkich jej kroków:

### <a name="create-a-new-self-signed-certificate"></a>Utwórz nowy certyfikat z podpisem własnym

1. [Utwórz certyfikat z podpisem własnym](#create-a-self-signed-certificate)
2. [Utwórz plik PFX dla certyfikatu SSL z podpisem własnym](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Przekaż certyfikat SSL do usługi w chmurze](#upload-ssl-certificate-to-cloud-service)
4. [Aktualizuj certyfikat SSL w pliku konfiguracji usługi](#update-ssl-certificate-in-service-configuration-file)
5. [Importowanie urzędu certyfikacji SSL](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Aby użyć istniejącego certyfikatu z magazynu certyfikatów
1. [Eksportowanie certyfikatu protokołu SSL z Store certyfikatu](#export-ssl-certificate-from-certificate-store)
2. [Przekaż certyfikat SSL do usługi w chmurze](#upload-ssl-certificate-to-cloud-service)
3. [Aktualizuj certyfikat SSL w pliku konfiguracji usługi](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Aby użyć istniejącego certyfikatu w pliku PFX
1. [Przekaż certyfikat SSL do usługi w chmurze](#upload-ssl-certificate-to-cloud-service)
2. [Aktualizuj certyfikat SSL w pliku konfiguracji usługi](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Aby skonfigurować certyfikaty klienta
Certyfikaty klienta są wymagane do uwierzytelniania żądań do usługi. Wybierz najbardziej odpowiednią z trzech poniższych scenariuszy, a wykonywanie wszystkich jej kroków:

### <a name="turn-off-client-certificates"></a>Wyłącz certyfikaty klienta
1. [Wyłącz uwierzytelnianie na podstawie certyfikatu klienta](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Wydać nowe certyfikaty klienta z podpisem własnym
1. [Tworzenie urzędu certyfikacji z podpisem własnym](#create-a-self-signed-certification-authority)
2. [Przekaż certyfikat urzędu certyfikacji do usługi w chmurze](#upload-ca-certificate-to-cloud-service)
3. [Aktualizowanie certyfikatu urzędu certyfikacji w pliku konfiguracji usługi](#update-ca-certificate-in-service-configuration-file)
4. [Wystawianie certyfikatów klienta](#issue-client-certificates)
5. [Tworzenie plików PFX certyfikatów klienta](#create-pfx-files-for-client-certificates)
6. [Importowanie certyfikatu klienta](#import-client-certificate)
7. [Skopiuj odciski palców certyfikatu klienta](#copy-client-certificate-thumbprints)
8. [Konfigurowanie klientów dozwolonych w pliku konfiguracji usługi](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Użyj istniejących certyfikatów klienta
1. [Znajdź klucz publiczny urzędu certyfikacji](#find-ca-public-key)
2. [Przekaż certyfikat urzędu certyfikacji do usługi w chmurze](#upload-ca-certificate-to-cloud-service)
3. [Aktualizowanie certyfikatu urzędu certyfikacji w pliku konfiguracji usługi](#update-ca-certificate-in-service-configuration-file)
4. [Skopiuj odciski palców certyfikatu klienta](#copy-client-certificate-thumbprints)
5. [Konfigurowanie klientów dozwolonych w pliku konfiguracji usługi](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurowanie sprawdzanie odwołania certyfikatu klienta](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Dostęp do punktów końcowych usługi może być ograniczone do określonych zakresów adresów IP.

## <a name="to-configure-encryption-for-the-store"></a>Aby skonfigurować szyfrowanie magazynu
Certyfikat jest wymagany do szyfrowania poświadczeń, które są przechowywane w magazynie metadanych. Wybierz najbardziej odpowiednią z trzech poniższych scenariuszy, a wykonywanie wszystkich jej kroków:

### <a name="use-a-new-self-signed-certificate"></a>Użyj nowego certyfikatu z podpisem własnym
1. [Utwórz certyfikat z podpisem własnym](#create-a-self-signed-certificate)
2. [Utwórz plik PFX certyfikatu szyfrowania z podpisem własnym](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Przekaż certyfikat szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
4. [Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Użyj istniejącego certyfikatu z magazynu certyfikatów
1. [Eksportowanie certyfikatu szyfrowania z Store certyfikatu](#export-encryption-certificate-from-certificate-store)
2. [Przekaż certyfikat szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
3. [Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Użyj istniejącego certyfikatu w pliku PFX
1. [Przekaż certyfikat szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
2. [Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Domyślna konfiguracja
Domyślna konfiguracja nie zezwala na dostęp do punktu końcowego HTTP. Jest to zalecane ustawienia, ponieważ żądania do tych punktów końcowych może posiadać poufne informacje, takie jak poświadczenia bazy danych.
Domyślna konfiguracja umożliwia dostęp do punktu końcowego HTTPS. To ustawienie może być ograniczony dalej.

### <a name="changing-the-configuration"></a>Zmienianie konfiguracji
Grupa reguł kontroli dostępu, które dotyczą i punktu końcowego są konfigurowane w  **\<EndpointAcls >** sekcji **pliku konfiguracji usługi**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Zasady w grupie kontroli dostępu są konfigurowane w \<AccessControl name = "" > sekcji pliku konfiguracji usługi. 

Format opisanej w dokumentacji list kontroli dostępu w sieci.
Na przykład aby zezwolić tylko adresy IP w zakresie 100.100.0.0 do 100.100.255.255, dostęp do punktu końcowego HTTPS, zasady będzie wyglądać następująco:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Odmowa usługi zapobiegania
Istnieją dwa różne mechanizmy obsługiwane do wykrywania i zapobiegania atakom typu odmowa usługi:

* Ogranicz liczbę równoczesnych żądań na host zdalny (funkcja domyślnie wyłączona)
* Ograniczanie szybkości dostępu do jednego hosta zdalnego (na domyślnie)

Są one oparte na funkcji opisano w zabezpieczeń dynamicznych adresów IP w usługach IIS. Gdy zmiana ta konfiguracja ostrożnie następujące czynniki:

* Zachowanie serwery proxy i urządzenia translatora adresów sieciowych na informacje hosta zdalnego
* Każde żądanie do dowolnego zasobu w roli sieci web jest uznawany za (na przykład podczas ładowania skryptów, obrazy, itp.)

## <a name="restricting-number-of-concurrent-accesses"></a>Ograniczenie liczby równoczesnych dostępy do
Dostępne są następujące ustawienia, które skonfigurowania tego zachowania:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Zmień DynamicIpRestrictionDenyByConcurrentRequests na wartość true, aby włączyć tę ochronę.

## <a name="restricting-rate-of-access"></a>Ograniczanie szybkości dostępu
Dostępne są następujące ustawienia, które skonfigurowania tego zachowania:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurowanie odpowiedzi na żądanie odrzuconych
Następujące ustawienie umożliwia skonfigurowanie odpowiedzi na żądanie odmowy:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Zapoznaj się z dokumentacją dla dynamicznych zabezpieczeń protokołu IP w usługach IIS dla innych obsługiwanych wartości.

## <a name="operations-for-configuring-service-certificates"></a>Operacje dotyczące konfigurowania usług certyfikatów
Ten temat dotyczy tylko do celów referencyjnych. Wykonaj kroki konfiguracji opisane w temacie:

* Konfigurowanie certyfikatu SSL
* Skonfiguruj certyfikaty klienta

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Należy wykonać:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Aby dostosować:

* -n za pomocą adresu URL usługi. Wildcards ("CN=*.cloudapp.net") and alternative names ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") are supported.
* -e z datą wygaśnięcia certyfikatu Utwórz silne hasło i określ go po wyświetleniu monitu.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Tworzenie pliku PFX certyfikatu SSL z podpisem własnym
Należy wykonać:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Wprowadź hasło, a następnie wyeksportować certyfikat za pomocą tych opcji:

* Tak, Eksportuj klucz prywatny
* Eksportuj wszystkie właściwości rozszerzone

## <a name="export-ssl-certificate-from-certificate-store"></a>Wyeksportuj certyfikat protokołu SSL z magazynu certyfikatów
* Znajdź certyfikat
* Kliknij pozycję Operacje -> Wszystkie zadania -> Eksportuj...
* Wyeksportuj certyfikat w. Plik PFX za pomocą tych opcji:
  * Tak, Eksportuj klucz prywatny
  * Jeśli to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji * wyeksportować wszystkie rozszerzone właściwości

## <a name="upload-ssl-certificate-to-cloud-service"></a>Przekaż certyfikat SSL do usługi w chmurze
Przekazywanie certyfikatu z istniejącym lub wygenerowane. Plik PFX parą kluczy SSL:

* Wprowadź hasło chroniące informacje o kluczu prywatnym

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Aktualizuj certyfikat SSL w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca, następujące ustawienia w pliku konfiguracji usługi odcisk palca certyfikatu przekazany do usługi w chmurze:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importowanie urzędu certyfikacji SSL
Wykonaj następujące kroki na wszystkie konta/maszynie, która będzie komunikować się z usługą:

* Kliknij dwukrotnie. Plik CER w Eksploratorze Windows
* W oknie dialogowym certyfikat kliknij przycisk Zainstaluj certyfikat...
* Zaimportuj certyfikat do magazynu zaufanych głównych urzędów certyfikacji

## <a name="turn-off-client-certificate-based-authentication"></a>Wyłącz uwierzytelnianie na podstawie certyfikatu klienta
Tylko uwierzytelnianie oparte na certyfikatach klienta jest obsługiwane i wyłączenie go pozwoli na publiczny dostęp do punktów końcowych usługi, chyba że inne mechanizmy znajdują się w miejscu (na przykład, Microsoft Azure Virtual Network).

Zmień te ustawienia na wartość false w pliku konfiguracji usługi, aby wyłączyć tę funkcję:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Następnie skopiuj ten sam odcisk palca jako certyfikat SSL w ustawieniu certyfikatu urzędu certyfikacji:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Tworzenie urzędu certyfikacji z podpisem własnym
Wykonaj poniższe kroki, aby utworzyć certyfikat z podpisem własnym do działania jako urząd certyfikacji:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Aby dostosować ją

* -e z datą wygaśnięcia certyfikacji

## <a name="find-ca-public-key"></a>Znajdź klucz publiczny urzędu certyfikacji
Wszystkie certyfikaty klienta muszą został wystawiony przez urząd certyfikacji zaufany przez usługę. Znajdź klucz publiczny do urzędu certyfikacji, który wystawił certyfikaty, które mają być używane do uwierzytelniania w celu przekazania go do usługi w chmurze klienta.

Jeśli plik przy użyciu klucza publicznego jest niedostępny, należy wyeksportować go z magazynu certyfikatów:

* Znajdź certyfikat
  * Wyszukaj z certyfikatem klienta wystawionym przez tego samego urzędu certyfikacji
* Kliknij dwukrotnie certyfikat.
* Wybierz kartę ścieżki certyfikacji, w oknie dialogowym certyfikat.
* Kliknij dwukrotnie wpis urzędu certyfikacji w ścieżce.
* Zanotować właściwości certyfikatu.
* Zamknij **certyfikatu** okna dialogowego.
* Znajdź certyfikat
  * Wyszukaj urzędu certyfikacji, o których wspomniano powyżej.
* Kliknij pozycję Operacje -> Wszystkie zadania -> Eksportuj...
* Wyeksportuj certyfikat w. CER za pomocą tych opcji:
  * **Nie eksportuj klucza prywatnego**
  * Dołącz wszystkie certyfikaty do ścieżki certyfikacji, jeśli jest to możliwe.
  * Eksportuj wszystkie właściwości rozszerzone.

## <a name="upload-ca-certificate-to-cloud-service"></a>Przekaż certyfikat urzędu certyfikacji do usługi w chmurze
Przekazywanie certyfikatu z istniejącym lub wygenerowane. Plik CER przy użyciu klucza publicznego urzędu certyfikacji.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Aktualizuj urząd certyfikacji certyfikatu w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca, następujące ustawienia w pliku konfiguracji usługi odcisk palca certyfikatu przekazany do usługi w chmurze:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Z tym samym odciskiem palca, zaktualizuj wartość następujące ustawienia:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Wystawianie certyfikatów klienta
Poszczególnym uprawnień do uzyskania dostępu do usługi powinny mieć z certyfikatem klienta wystawionym ich do wyłącznego użytku i wybrać własne silne hasło, aby chronić jego klucz prywatny. 

W tym samym komputerze, na którym generowane i przechowywane certyfikat urzędu certyfikacji z podpisem własnym należy wykonać następujące czynności:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Dostosowywanie:

* n - o identyfikatorze dla klienta, która będzie uwierzytelniana przy użyciu tego certyfikatu
* -e z datą wygaśnięcia certyfikatu
* MyID.pvk i MyID.cer przy użyciu unikatowych nazw plików dla tego certyfikatu klienta.

To polecenie wyświetli monit o hasło, aby być tworzone i następnie użyć raz. Należy używać silnych haseł.

## <a name="create-pfx-files-for-client-certificates"></a>Tworzenie plików PFX dla klientów certyfikatów
Dla każdego certyfikatu wygenerowanego klienta należy wykonać:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Dostosowywanie:

    MyID.pvk and MyID.cer with the filename for the client certificate

Wprowadź hasło, a następnie wyeksportować certyfikat za pomocą tych opcji:

* Tak, Eksportuj klucz prywatny
* Eksportuj wszystkie właściwości rozszerzone
* Osoba, dla którego ten certyfikat został wystawiony, należy wybrać hasło eksportu

## <a name="import-client-certificate"></a>Importowanie certyfikatu klienta
Każda osoba, dla którego został wystawiony certyfikat klienta należy zaimportować pary kluczy na maszynach, na których będą używać do komunikowania się z usługą:

* Kliknij dwukrotnie. Plik PFX w Eksploratorze Windows
* Importuj certyfikat do osobistego przechowywania z co najmniej tej opcji:
  * Dołącz wszystkie właściwości rozszerzone zaznaczone

## <a name="copy-client-certificate-thumbprints"></a>Skopiuj odciski palców certyfikatu klienta
Każda osoba, dla którego został wystawiony certyfikat klienta, należy wykonać następujące kroki, aby uzyskać odcisk palca certyfikatu, który zostanie dodany do pliku konfiguracji usługi:

* Uruchom certmgr.exe
* Wybierz kartę osobiste.
* Kliknij dwukrotnie certyfikat klienta, który ma być używany do uwierzytelniania
* W oknie dialogowym certyfikat, który zostanie otwarty wybierz kartę szczegółów
* Upewnij się, że wyświetla wszystkie Show
* Wybierz pole o nazwie odcisk palca na liście
* Skopiuj wartość odcisku palca
  * Usuń niewidoczne znaki Unicode przed pierwszą
  * Usuń wszystkie spacje

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurowanie klientów dozwolone w pliku konfiguracji usługi
Zaktualizuj wartość następujące ustawienia w pliku konfiguracji usługi rozdzielaną przecinkami listę odcisków palców certyfikatów klientów, zezwolenie na dostęp do usługi:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurowanie sprawdzanie odwołania certyfikatu klienta
Ustawienie domyślne nie sprawdza się z urzędem certyfikacji dla stanu odwołania certyfikatu klienta. Aby włączyć sprawdza, czy urząd certyfikacji, który wystawił certyfikaty klienta obsługuje takich kontroli, Zmień następujące ustawienie przy użyciu jednej z wartości zdefiniowanych w wyliczeniu X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Utwórz plik PFX certyfikatów z podpisem szyfrowania
Aby uzyskać certyfikat szyfrowania należy wykonać:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Dostosowywanie:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Wprowadź hasło, a następnie wyeksportować certyfikat za pomocą tych opcji:

* Tak, Eksportuj klucz prywatny
* Eksportuj wszystkie właściwości rozszerzone
* Konieczne będzie hasło podczas przekazywania certyfikatu do usługi w chmurze.

## <a name="export-encryption-certificate-from-certificate-store"></a>Wyeksportuj certyfikat szyfrowania z magazynu certyfikatów
* Znajdź certyfikat
* Kliknij pozycję Operacje -> Wszystkie zadania -> Eksportuj...
* Wyeksportuj certyfikat w. Plik PFX za pomocą tych opcji: 
  * Tak, Eksportuj klucz prywatny
  * Jeśli to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji 
* Eksportuj wszystkie właściwości rozszerzone

## <a name="upload-encryption-certificate-to-cloud-service"></a>Przekaż certyfikat szyfrowania do usługi w chmurze
Przekazywanie certyfikatu z istniejącym lub wygenerowane. Plik PFX parą kluczy szyfrowania:

* Wprowadź hasło chroniące informacje o kluczu prywatnym

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca z następujących ustawień w pliku konfiguracji usługi odcisk palca certyfikatu przekazany do usługi w chmurze:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Typowe operacje dotyczące certyfikatu
* Konfigurowanie certyfikatu SSL
* Skonfiguruj certyfikaty klienta

## <a name="find-certificate"></a>Znajdź certyfikat
Wykonaj następujące kroki:

1. Uruchom mmc.exe.
2. Plik -> Dodaj/Usuń przystawkę...
3. Wybierz **certyfikaty**.
4. Kliknij pozycję **Add** (Dodaj).
5. Wybierz lokalizację magazynu certyfikatów.
6. Kliknij przycisk **Zakończ**.
7. Kliknij przycisk **OK**.
8. Rozwiń **certyfikaty**.
9. Rozwiń węzeł magazynu certyfikatów.
10. Rozwiń węzeł podrzędny certyfikatu.
11. Wybierz certyfikat z listy.

## <a name="export-certificate"></a>Eksportowanie certyfikatu
W **Kreator eksportu certyfikatów**:

1. Kliknij przycisk **Dalej**.
2. Wybierz **tak**, następnie **Eksportuj klucz prywatny**.
3. Kliknij przycisk **Dalej**.
4. Wybierz format pliku żądaną produktu wyjściowego.
5. Sprawdź wybrane opcje.
6. Sprawdź **hasło**.
7. Wpisz silne hasło i potwierdź je.
8. Kliknij przycisk **Dalej**.
9. Wpisz lub Przeglądaj, nazwa_pliku miejsce przechowywania certyfikatu (Użyj. Rozszerzenie PFX).
10. Kliknij przycisk **Dalej**.
11. Kliknij przycisk **Zakończ**.
12. Kliknij przycisk **OK**.

## <a name="import-certificate"></a>Importowanie certyfikatu
W Kreatorze importu certyfikatów:

1. Wybierz lokalizację magazynu.
   
   * Wybierz **bieżącego użytkownika** czy tylko procesów uruchomionych w ramach bieżącego użytkownika uzyskają dostęp do usługi
   * Wybierz **komputera lokalnego** Jeśli inne procesy, w tym komputerze uzyskają dostęp do usługi
2. Kliknij przycisk **Dalej**.
3. W przypadku importowania z pliku upewnij się, ścieżkę pliku.
4. W przypadku importowania. Plik PFX:
   1. Wprowadź hasło ochrony klucza prywatnego
   2. Wybierz opcje importowania
5. Wybierz "W miejscu" certyfikaty w następującym magazynie
6. Kliknij pozycję **Browse (Przeglądaj)** .
7. Wybierz żądany magazyn.
8. Kliknij przycisk **Zakończ**.
   
   * Jeśli wybrano magazynie zaufany główny urząd certyfikacji, kliknij przycisk **tak**.
9. Kliknij przycisk **OK** na wszystkie okna dialogowe.

## <a name="upload-certificate"></a>Przekazywanie certyfikatu
W witrynie [Azure Portal](https://portal.azure.com/)

1. Wybierz **usług w chmurze**.
2. Wybierz usługę w chmurze.
3. W górnym menu, kliknij przycisk **certyfikaty**.
4. Na pasku dolnym, kliknij przycisk **przekazywanie**.
5. Wybierz plik certyfikatu.
6. Jeśli istnieje. Plik PFX pliku, wprowadź hasło klucza prywatnego.
7. Po zakończeniu skopiuj odcisk palca certyfikatu z nowy wpis na liście.

## <a name="other-security-considerations"></a>Inne uwagi dotyczące zabezpieczeń
Ustawienia protokołu SSL, opisane w niniejszym dokumencie szyfrowania komunikacji między usługą i jej klientów, stosowania punktu końcowego HTTPS. Jest to ważne, ponieważ poświadczenia na potrzeby dostępu do bazy danych i inne poufne informacje znajdują się w komunikacie. Należy jednak pamiętać, że usługa będzie się powtarzał wewnętrzny stan, w tym poświadczeń w jego wewnętrznych tabel w bazie Microsoft Azure SQL, które zostały podane dla magazynu metadanych w ramach subskrypcji Microsoft Azure. Tej bazy danych został zdefiniowany jako część następujące ustawienie w pliku konfiguracji usługi (. Plik CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Poświadczenia przechowywane w tej bazie danych są szyfrowane. Jednak najlepszym rozwiązaniem jest upewnienie się, czy role sieci web i proces roboczy wdrożeń usługi są zawsze aktualne i bezpieczne, jak one mają dostęp do bazy danych metadanych i certyfikat używany do szyfrowania i odszyfrowywania przechowywanych poświadczeń. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


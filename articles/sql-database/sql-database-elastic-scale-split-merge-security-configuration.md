---
title: Konfiguracja zabezpieczeń scalania dzielonego
description: Konfigurowanie certyfikatów x409 do szyfrowania za pomocą usługi dzielenia/scalania dla skali elastycznej.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a5ea0fd252d1792d4c40cc6d7869f4ba57edc1ad
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011365"
---
# <a name="split-merge-security-configuration"></a>Konfiguracja zabezpieczeń scalania dzielonego

Aby korzystać z usługi Dzielenie/Scalanie, należy poprawnie skonfigurować zabezpieczenia. Usługa jest częścią funkcji skalowania elastycznego bazy danych SQL Platformy Microsoft Azure. Aby uzyskać więcej informacji, zobacz [Elastyczne skalowanie podziału i scalania usługi samouczka](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurowanie certyfikatów

Certyfikaty są konfigurowane na dwa sposoby. 

1. [Aby skonfigurować certyfikat TLS/SSL](#to-configure-the-tlsssl-certificate)
2. [Aby skonfigurować certyfikaty klientów](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Aby uzyskać certyfikaty

Certyfikaty można uzyskać od publicznych urzędów certyfikacji lub z [Usługi certyfikatów systemu Windows.](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx) Są to preferowane metody uzyskiwania certyfikatów.

Jeśli te opcje nie są dostępne, można wygenerować **certyfikaty z podpisem własnym**.

## <a name="tools-to-generate-certificates"></a>Narzędzia do generowania certyfikatów

* [Makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [plik pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Aby uruchomić narzędzia

* W wierszu polecenia dewelopera dla programu Visual Studios zobacz [Wiersz polecenia programu Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Jeśli jest zainstalowany, przejdź do:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Pobierz zestaw WDK z [systemu Windows 8.1: Pobierz zestawy i narzędzia](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Aby skonfigurować certyfikat TLS/SSL

Certyfikat TLS/SSL jest wymagany do szyfrowania komunikacji i uwierzytelniania serwera. Wybierz najbardziej odpowiednie z trzech poniższych scenariuszy i wykonaj wszystkie jego kroki:

### <a name="create-a-new-self-signed-certificate"></a>Tworzenie nowego certyfikatu z podpisem własnym

1. [Tworzenie certyfikatu z podpisem własnym](#create-a-self-signed-certificate)
2. [Tworzenie pliku PFX dla certyfikatu TLS/SSL z podpisem własnym](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Przekazywanie certyfikatu TLS/SSL do usługi w chmurze](#upload-tlsssl-certificate-to-cloud-service)
4. [Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi](#update-tlsssl-certificate-in-service-configuration-file)
5. [Importowanie urzędu certyfikacji protokołu TLS/SSL](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Aby użyć istniejącego certyfikatu z magazynu certyfikatów
1. [Eksportowanie certyfikatu TLS/SSL z magazynu certyfikatów](#export-tlsssl-certificate-from-certificate-store)
2. [Przekazywanie certyfikatu TLS/SSL do usługi w chmurze](#upload-tlsssl-certificate-to-cloud-service)
3. [Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Aby użyć istniejącego certyfikatu w pliku PFX
1. [Przekazywanie certyfikatu TLS/SSL do usługi w chmurze](#upload-tlsssl-certificate-to-cloud-service)
2. [Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Aby skonfigurować certyfikaty klientów
Certyfikaty klienta są wymagane w celu uwierzytelnienia żądań do usługi. Wybierz najbardziej odpowiednie z trzech poniższych scenariuszy i wykonaj wszystkie jego kroki:

### <a name="turn-off-client-certificates"></a>Wyłączanie certyfikatów klientów
1. [Wyłączanie uwierzytelniania opartego na certyfikatach klienta](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Wystawianie nowych certyfikatów klientów z podpisem własnym
1. [Tworzenie urzędu certyfikacji z podpisem własnym](#create-a-self-signed-certification-authority)
2. [Przekazywanie certyfikatu urzędu certyfikacji do usługi w chmurze](#upload-ca-certificate-to-cloud-service)
3. [Aktualizowanie certyfikatu urzędu certyfikacji w pliku konfiguracyjnym usługi](#update-ca-certificate-in-service-configuration-file)
4. [Wystawianie certyfikatów klientów](#issue-client-certificates)
5. [Tworzenie plików PFX dla certyfikatów klienta](#create-pfx-files-for-client-certificates)
6. [Importowanie certyfikatu klienta](#import-client-certificate)
7. [Kopiowanie odcisków palców certyfikatu klienta](#copy-client-certificate-thumbprints)
8. [Konfigurowanie dozwolonych klientów w pliku konfiguracji usługi](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Używanie istniejących certyfikatów klientów
1. [Znajdź klucz publiczny urzędu certyfikacji](#find-ca-public-key)
2. [Przekazywanie certyfikatu urzędu certyfikacji do usługi w chmurze](#upload-ca-certificate-to-cloud-service)
3. [Aktualizowanie certyfikatu urzędu certyfikacji w pliku konfiguracyjnym usługi](#update-ca-certificate-in-service-configuration-file)
4. [Kopiowanie odcisków palców certyfikatu klienta](#copy-client-certificate-thumbprints)
5. [Konfigurowanie dozwolonych klientów w pliku konfiguracji usługi](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurowanie sprawdzania odwołania certyfikatu klienta](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Dostęp do punktów końcowych usługi może być ograniczony do określonych zakresów adresów IP.

## <a name="to-configure-encryption-for-the-store"></a>Aby skonfigurować szyfrowanie dla sklepu
Certyfikat jest wymagany do szyfrowania poświadczeń, które są przechowywane w magazynie metadanych. Wybierz najbardziej odpowiednie z trzech poniższych scenariuszy i wykonaj wszystkie jego kroki:

### <a name="use-a-new-self-signed-certificate"></a>Używanie nowego certyfikatu z podpisem własnym
1. [Tworzenie certyfikatu z podpisem własnym](#create-a-self-signed-certificate)
2. [Tworzenie pliku PFX dla certyfikatu szyfrowania z podpisem własnym](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Przekazywanie certyfikatu szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
4. [Aktualizowanie certyfikatu szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Używanie istniejącego certyfikatu z magazynu certyfikatów
1. [Eksportuj certyfikat szyfrowania z magazynu certyfikatów](#export-encryption-certificate-from-certificate-store)
2. [Przekazywanie certyfikatu szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
3. [Aktualizowanie certyfikatu szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Używanie istniejącego certyfikatu w pliku PFX
1. [Przekazywanie certyfikatu szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
2. [Aktualizowanie certyfikatu szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Konfiguracja domyślna
Domyślna konfiguracja odmawia dostępu do punktu końcowego HTTP. Jest to zalecane ustawienie, ponieważ żądania do tych punktów końcowych mogą przenosić poufne informacje, takie jak poświadczenia bazy danych.
Domyślna konfiguracja umożliwia dostęp do punktu końcowego HTTPS. To ustawienie może być dodatkowo ograniczone.

### <a name="changing-the-configuration"></a>Zmiana konfiguracji
Grupa reguł kontroli dostępu, które mają zastosowanie do punktu końcowego i punktu końcowego są konfigurowane w ** \<endpointAcls>** sekcji w **pliku konfiguracji usługi**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Reguły w grupie kontroli dostępu są \<konfigurowane w sekcji AccessControl name=""> pliku konfiguracji usługi. 

Format jest wyjaśniony w dokumentacji list kontroli dostępu do sieci.
Na przykład, aby zezwolić tylko adresom IP w zakresie od 100.100.0.0 do 100.100.255.255, aby uzyskać dostęp do punktu końcowego HTTPS, reguły będą wyglądać następująco:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Zapobieganie odmowie usługi
Istnieją dwa różne mechanizmy obsługiwane w celu wykrywania ataków typu "odmowa usługi" i zapobiegania im:

* Ograniczanie liczby równoczesnych żądań na host zdalny (domyślnie wyłączone)
* Ograniczanie szybkości dostępu na host zdalny (domyślnie włączone)

Są one oparte na funkcjach dodatkowo udokumentowanych w programie Dynamic IP Security w u źródłach IIS. Podczas zmiany tej konfiguracji uważaj na następujące czynniki:

* Zachowanie serwerów proxy i urządzeń do tłumaczenia adresów sieciowych za pośrednictwem informacji o hoście zdalnym
* Każde żądanie do dowolnego zasobu w roli sieci web jest brane pod uwagę (na przykład ładowanie skryptów, obrazów itp.)

## <a name="restricting-number-of-concurrent-accesses"></a>Ograniczanie liczby równoczesnych dostępów
Ustawienia, które konfigurują to zachowanie są następujące:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Zmień DynamicIpRestrictionDenyByConcurrentRequests true, aby włączyć tę ochronę.

## <a name="restricting-rate-of-access"></a>Ograniczanie szybkości dostępu
Ustawienia, które konfigurują to zachowanie są następujące:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurowanie odpowiedzi na żądanie odmowy
Następujące ustawienie konfiguruje odpowiedź na żądanie odmowy:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Informacje na temat innych obsługiwanych wartości można znaleźć w dokumentacji programu Dynamic IP Security w u źródła.

## <a name="operations-for-configuring-service-certificates"></a>Operacje konfigurowania certyfikatów usług
Ten temat jest tylko w celach informacyjnych. Postępuj zgodnie z instrukcjami konfiguracji opisanymi w:

* Konfigurowanie certyfikatu TLS/SSL
* Konfigurowanie certyfikatów klientów

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Wykonać:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Aby dostosować:

* -n z adresem URL usługi. Obsługiwane są symbole wieloznaczne ("CN=*.cloudapp.net") i nazwy alternatywne ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net").
* -e z datą wygaśnięcia certyfikatu Utwórz silne hasło i określ je po wyświetleniu monitu.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Tworzenie pliku PFX dla certyfikatu TLS/SSL z podpisem własnym
Wykonać:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Wprowadź hasło, a następnie wyeksportuj certyfikat z tymi opcjami:

* Tak, eksportuj klucz prywatny
* Eksportowanie wszystkich właściwości rozszerzonych

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Eksportowanie certyfikatu TLS/SSL z magazynu certyfikatów
* Znajdowanie certyfikatu
* Kliknij pozycję Akcje -> Wszystkie zadania -> Eksportuj...
* Eksportuj certyfikat do pliku . PFX z tymi opcjami:
  * Tak, eksportuj klucz prywatny
  * Jeśli to możliwe, uwzględnij wszystkie certyfikaty w ścieżce certyfikacji *Wyeksportuj wszystkie właściwości rozszerzone

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Przekazywanie certyfikatu TLS/SSL do usługi w chmurze
Przekaż certyfikat z istniejącym lub wygenerowanym . PFX z parą kluczy TLS:

* Wprowadź hasło chroniące informacje o kluczu prywatnym

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca następującego ustawienia w pliku konfiguracji usługi za pomocą odcisku palca certyfikatu przekazanego do usługi w chmurze:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-tlsssl-certification-authority"></a>Importowanie urzędu certyfikacji TLS/SSL
Wykonaj następujące kroki na wszystkich kontach/komputerze, które będą komunikować się z usługą:

* Kliknij dwukrotnie przycisk . CER w Eksploratorze Windows
* W oknie dialogowym Certyfikat kliknij pozycję Zainstaluj certyfikat...
* Importowanie certyfikatu do magazynu zaufanych głównych urzędów certyfikacji

## <a name="turn-off-client-certificate-based-authentication"></a>Wyłączanie uwierzytelniania opartego na certyfikatach klienta
Obsługiwane jest tylko uwierzytelnianie oparte na certyfikatach klienta, a wyłączenie go umożliwi publiczny dostęp do punktów końcowych usługi, chyba że istnieją inne mechanizmy (na przykład sieć wirtualna platformy Microsoft Azure).

Zmień te ustawienia na false w pliku konfiguracji usługi, aby wyłączyć tę funkcję:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Następnie skopiuj ten sam odcisk palca co certyfikat TLS/SSL w ustawieniu certyfikatu urzędu certyfikacji:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Tworzenie urzędu certyfikacji z podpisem własnym
Wykonaj następujące kroki, aby utworzyć certyfikat z podpisem własnym, aby działać jako urząd certyfikacji:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Aby go dostosować

* -e z datą wygaśnięcia certyfikatu

## <a name="find-ca-public-key"></a>Znajdowanie klucza publicznego urzędu certyfikacji
Wszystkie certyfikaty klientów muszą być wystawione przez urząd certyfikacji zaufany przez usługę. Znajdź klucz publiczny dla urzędu certyfikacji, który wystawił certyfikaty klienta, które będą używane do uwierzytelniania w celu przekazania go do usługi w chmurze.

Jeśli plik z kluczem publicznym nie jest dostępny, wyeksportuj go z magazynu certyfikatów:

* Znajdowanie certyfikatu
  * Wyszukiwanie certyfikatu klienta wystawionego przez ten sam urząd certyfikacji
* Kliknij dwukrotnie certyfikat.
* Wybierz kartę Ścieżka certyfikacji w oknie dialogowym Certyfikat.
* Kliknij dwukrotnie wpis urzędu certyfikacji w ścieżce.
* Ramić notatki właściwości certyfikatu.
* Zamknij okno dialogowe **Certyfikat.**
* Znajdowanie certyfikatu
  * Wyszukaj wyżej wymienioną urząd certyfikacji.
* Kliknij pozycję Akcje -> Wszystkie zadania -> Eksportuj...
* Eksportuj certyfikat do pliku . CER z tymi opcjami:
  * **Nie, nie wyeksportuj klucza prywatnego**
  * Jeśli to możliwe, uwzględnij wszystkie certyfikaty w ścieżce certyfikacji.
  * Eksportuj wszystkie właściwości rozszerzone.

## <a name="upload-ca-certificate-to-cloud-service"></a>Przekazywanie certyfikatu urzędu certyfikacji do usługi w chmurze
Przekaż certyfikat z istniejącym lub wygenerowanym . CER z kluczem publicznym urzędu certyfikacji.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Aktualizowanie certyfikatu urzędu certyfikacji w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca następującego ustawienia w pliku konfiguracji usługi za pomocą odcisku palca certyfikatu przekazanego do usługi w chmurze:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Zaktualizuj wartość następującego ustawienia za pomocą tego samego odcisku palca:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Wystawianie certyfikatów klientów
Każda osoba upoważniona do dostępu do usługi powinna mieć certyfikat klienta wystawiony do wyłącznego użytku i powinna wybrać własne silne hasło, aby chronić swój klucz prywatny. 

Następujące kroki muszą być wykonywane na tym samym komputerze, na którym został wygenerowany i przechowywany certyfikat urzędu certyfikacji z podpisem własnym:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Dostosowywanie:

* -n z identyfikatorem dla klienta, który zostanie uwierzytelniony za pomocą tego certyfikatu
* -e z datą wygaśnięcia certyfikatu
* MyID.pvk i MyID.cer z unikatowymi nazwami plików dla tego certyfikatu klienta

To polecenie wyświetli monit o utworzenie hasła, a następnie użycie go raz. Użyj silnego hasła.

## <a name="create-pfx-files-for-client-certificates"></a>Tworzenie plików PFX dla certyfikatów klienta
Dla każdego wygenerowanego certyfikatu klienta wykonaj:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Dostosowywanie:

    MyID.pvk and MyID.cer with the filename for the client certificate

Wprowadź hasło, a następnie wyeksportuj certyfikat z tymi opcjami:

* Tak, eksportuj klucz prywatny
* Eksportowanie wszystkich właściwości rozszerzonych
* Osoba, której wystawiany jest ten certyfikat, powinna wybrać hasło eksportu

## <a name="import-client-certificate"></a>Importowanie certyfikatu klienta
Każda osoba, dla której wystawiono certyfikat klienta, powinna zaimportować parę kluczy na komputerach, których użyje do komunikowania się z usługą:

* Kliknij dwukrotnie przycisk . Plik PFX w Eksploratorze Windows
* Zaimportuj certyfikat do magazynu osobistego z co najmniej tą opcją:
  * Uwzględnij wszystkie sprawdzone właściwości rozszerzone

## <a name="copy-client-certificate-thumbprints"></a>Kopiowanie odcisków palców certyfikatu klienta
Każda osoba, dla której wystawiono certyfikat klienta, musi wykonać następujące kroki w celu uzyskania odcisku palca swojego certyfikatu, który zostanie dodany do pliku konfiguracji usługi:

* Uruchom plik certmgr.exe
* Wybierz kartę Osobiste
* Kliknij dwukrotnie certyfikat klienta, który ma być używany do uwierzytelniania
* W otwierane okno dialogowe Certyfikat wybierz kartę Szczegóły
* Upewnij się, że w programie Pokaż jest
* Zaznacz pole o nazwie Odcisk palca na liście
* Kopiowanie wartości odcisku palca
  * Usuwanie niewidocznych znaków Unicode przed pierwszą cyfrą
  * Usuwanie wszystkich spacji

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurowanie dozwolonych klientów w pliku konfiguracji usługi
Zaktualizuj wartość następującego ustawienia w pliku konfiguracji usługi za pomocą listy odcisków palców certyfikatów klienta, które mają dostęp do usługi:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurowanie sprawdzania odwołania certyfikatu klienta
Ustawienie domyślne nie sprawdza stanu odwołania certyfikatu klienta w urzędzie certyfikacji. Aby włączyć kontrole, jeśli Urząd Certyfikacji, który wystawił certyfikaty klienta obsługuje takie kontrole, zmień następujące ustawienie z jedną z wartości zdefiniowanych w wyliczeniu X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Tworzenie pliku PFX dla certyfikatów szyfrowania z podpisem własnym
W przypadku certyfikatu szyfrowania wykonaj:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Dostosowywanie:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Wprowadź hasło, a następnie wyeksportuj certyfikat z tymi opcjami:

* Tak, eksportuj klucz prywatny
* Eksportowanie wszystkich właściwości rozszerzonych
* Hasło będzie potrzebne podczas przekazywania certyfikatu do usługi w chmurze.

## <a name="export-encryption-certificate-from-certificate-store"></a>Eksportowanie certyfikatu szyfrowania z magazynu certyfikatów
* Znajdowanie certyfikatu
* Kliknij pozycję Akcje -> Wszystkie zadania -> Eksportuj...
* Eksportuj certyfikat do pliku . PFX z tymi opcjami: 
  * Tak, eksportuj klucz prywatny
  * Jeśli to możliwe, uwzględnij wszystkie certyfikaty na ścieżce certyfikacji 
* Eksportowanie wszystkich właściwości rozszerzonych

## <a name="upload-encryption-certificate-to-cloud-service"></a>Przekazywanie certyfikatu szyfrowania do usługi w chmurze
Przekaż certyfikat z istniejącym lub wygenerowanym . PFX z parą kluczy szyfrowania:

* Wprowadź hasło chroniące informacje o kluczu prywatnym

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualizowanie certyfikatu szyfrowania w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca następujących ustawień w pliku konfiguracji usługi za pomocą odcisku palca certyfikatu przekazanego do usługi w chmurze:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Wspólne operacje certyfikatów
* Konfigurowanie certyfikatu TLS/SSL
* Konfigurowanie certyfikatów klientów

## <a name="find-certificate"></a>Znajdowanie certyfikatu
Wykonaj następujące kroki:

1. Uruchom program mmc.exe.
2. Plik -> Dodaj/Usuń przystawkę...
3. wybierz opcję **Certyfikaty**.
4. Kliknij przycisk **Dodaj**.
5. Wybierz lokalizację magazynu certyfikatów.
6. Kliknij przycisk **Zakończ**.
7. Kliknij przycisk **OK**.
8. Rozwiń **certyfikaty**.
9. Rozwiń węzeł magazynu certyfikatów.
10. Rozwiń węzeł podrzędny certyfikatu.
11. Wybierz certyfikat na liście.

## <a name="export-certificate"></a>Eksportowanie certyfikatu
W **Kreatorze eksportu certyfikatów**:

1. Kliknij przycisk **Dalej**.
2. Wybierz **pozycję Tak**, a następnie pozycję **Eksportuj klucz prywatny**.
3. Kliknij przycisk **Dalej**.
4. Wybierz żądany format pliku wyjściowego.
5. Sprawdź żądane opcje.
6. Sprawdź **hasło**.
7. Wprowadź silne hasło i potwierdź go.
8. Kliknij przycisk **Dalej**.
9. Wpisz lub przejrzyj nazwę pliku, gdzie ma być przechowywane certyfikat (użyj pliku . pfx).
10. Kliknij przycisk **Dalej**.
11. Kliknij przycisk **Zakończ**.
12. Kliknij przycisk **OK**.

## <a name="import-certificate"></a>Importowanie certyfikatu
W Kreatorze importu certyfikatów:

1. Wybierz lokalizację sklepu.
   
   * Wybierz **bieżącego użytkownika,** jeśli tylko procesy uruchomione w obszarze bieżącego użytkownika będą uzyskiwać dostęp do usługi
   * Wybierz **opcję Komputer lokalny,** jeśli inne procesy na tym komputerze będą uzyskiwać dostęp do usługi
2. Kliknij przycisk **Dalej**.
3. Jeśli importowanie z pliku, potwierdź ścieżkę pliku.
4. W przypadku importowania pliku . PLIK PFX:
   1. Wprowadź hasło chroniące klucz prywatny
   2. Wybieranie opcji importu
5. Wybierz certyfikaty "Umieść" w następującym magazynie
6. Kliknij pozycję **Browse (Przeglądaj)**.
7. Wybierz żądany magazyn.
8. Kliknij przycisk **Zakończ**.
   
   * Jeśli wybrano magazyn zaufanego głównego urzędu certyfikacji, kliknij przycisk **Tak**.
9. Kliknij **przycisk OK** we wszystkich oknach dialogowych.

## <a name="upload-certificate"></a>Przekazywanie certyfikatu
W [witrynie Azure portal](https://portal.azure.com/)

1. Wybierz pozycję **Usługi w chmurze**.
2. Wybierz usługę w chmurze.
3. W górnym menu kliknij pozycję **Certyfikaty**.
4. Na dolnym pasku kliknij pozycję **Prześlij**.
5. Wybierz plik certyfikatu.
6. Jeśli jest to plik . PFX, wprowadź hasło do klucza prywatnego.
7. Po zakończeniu skopiuj odcisk palca certyfikatu z nowego wpisu na liście.

## <a name="other-security-considerations"></a>Inne względy bezpieczeństwa
Ustawienia TLS opisane w tym dokumencie szyfrują komunikację między usługą a jej klientami, gdy używany jest punkt końcowy HTTPS. Jest to ważne, ponieważ poświadczenia dostępu do bazy danych i potencjalnie inne poufne informacje są zawarte w komunikacie. Należy jednak pamiętać, że usługa będzie zachowywać stan wewnętrzny, w tym poświadczenia, w swoich tabelach wewnętrznych w bazie danych SQL platformy Microsoft Azure, która została udostępniona dla magazynu metadanych w ramach subskrypcji platformy Microsoft Azure. Ta baza danych została zdefiniowana jako część następującego ustawienia w pliku konfiguracji usługi (. Plik CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Poświadczenia przechowywane w tej bazie danych są szyfrowane. Jednak najlepszym rozwiązaniem jest zapewnienie, że role sieci web i proces roboczy wdrożeń usługi są aktualizowane i bezpieczne, ponieważ oba mają dostęp do bazy danych metadanych i certyfikatu używanego do szyfrowania i odszyfrowywania przechowywanych poświadczeń. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


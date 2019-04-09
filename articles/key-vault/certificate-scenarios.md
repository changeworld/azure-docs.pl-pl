---
title: Rozpoczynanie pracy z certyfikatami usługi Key Vault
description: Następujące scenariusze przedstawiają kilka podstawowe sposoby użycia usługi Key Vault certyfikatu zarządzania tym dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 20c05bddddce4c7748e29551fe78d3e5609b2fa5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275899"
---
# <a name="get-started-with-key-vault-certificates"></a>Rozpoczynanie pracy z certyfikatami usługi Key Vault
Następujące scenariusze przedstawiają kilka podstawowe sposoby użycia usługi Key Vault certyfikatu zarządzania tym dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy.

Podane są:
- Tworzenie swojej pierwszej certyfikatem usługi Key Vault
- Tworzenie certyfikatu z urzędu certyfikacji jest partnerstwo z usługą Key Vault
- Tworzenie certyfikatu z urzędu certyfikacji jest nie użyły usługi Key Vault
- Importowanie certyfikatu

## <a name="certificates-are-complex-objects"></a>Certyfikaty są złożone obiekty
Certyfikaty składają się z trzech zasoby powiązane ze sobą połączone ze sobą jako certyfikatem usługi Key Vault; metadanymi certyfikatu, klucza i wpisu tajnego.


![Certyfikaty są złożone](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Tworzenie swojej pierwszej certyfikatem usługi Key Vault  
 Certyfikat można było utworzyć w magazynie Key (KV), wstępnie wymagane kroki 1 i 2, muszą być pomyślnie wykonywane i magazynu kluczy musi istnieć dla tego użytkownika / organizacji.  

**Krok 1** -certyfikatu urzędu certyfikacji dostawców  
-   Proces wdrażania jako administratora IT, administrator infrastruktury kluczy publicznych lub każdy zarządzania kontami z urzędów certyfikacji dla danej firmy (np. Contoso) to warunek wstępny do korzystania z certyfikatów usługi Key Vault.  
    Następujące urzędy certyfikacji są bieżącego dostawcy utworzyły z usługą Key Vault:  
    -   Firmy DigiCert - Key Vault oferuje OV-SSL certyfikatów przy użyciu firmy DigiCert.  
    -   GlobalSign - Key Vault oferuje OV-SSL certyfikatów za pomocą GlobalSign  
    -   WoSign - Key Vault oferuje OV-SSL lub certyfikatów protokołu SSL z Weryfikacją z WoSign na podstawie ustawienia skonfigurowane przez klienta na jego koncie WoSign w portalu WoSign.  

**Krok 2** — administrator konta dla dostawcy urzędu certyfikacji powoduje utworzenie poświadczenia do użycia przez usługę Key Vault, aby się zarejestrować, odnowienia i korzystać z certyfikatów protokołu SSL za pośrednictwem usługi Key Vault.

**Krok 3** — administrator firmy Contoso, wraz z pracownik firmy Contoso (Key Vault użytkownika), który jest właścicielem certyfikatów, w zależności od urzędu certyfikacji, można uzyskać certyfikat od administratora lub bezpośrednio z konta z urzędem certyfikacji.  

- Rozpocznij operacja dodawania poświadczeń do magazynu kluczy przez [ustawienie wystawcy certyfikatu](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) zasobów. Wystawca certyfikatu jest reprezentowana w usłudze Azure klucza magazynu (KV) jako zasób CertificateIssuer jednostki. Umożliwia podawanie informacji o źródle certyfikatu KV; Nazwa wystawcy, dostawca, poświadczeń i inne szczegóły administracyjne.
  - Np. MyDigiCertIssuer  
    -   Dostawca  
    -   Poświadczenia — poświadczenia konta urzędu certyfikacji. Każdym urzędzie certyfikacji ma swój własny określonych danych.  

    Aby uzyskać więcej informacji o tworzeniu konta przy użyciu dostawcy urzędu certyfikacji, zobacz wpis powiązane [blog usługi Key Vault](https://aka.ms/kvcertsblog).  

**Krok 3.1** — Konfigurowanie [certyfikatu kontakty](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) powiadomień. Jest to kontaktu dla użytkownika usługi Key Vault. Usługa Key Vault nie wymusza ten krok.  

Uwaga — Ten proces, za pomocą krok 3.1, jest to jednorazowa operacja.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędu certyfikacji we współpracy z usługi Key Vault

![Utwórz certyfikat z urzędu certyfikacji usługi Key Vault partnerstwo](media/certificate-authority-2.png)

**Krok 4** -zielony ponumerowanych kroków na powyższym diagramie odpowiadają następujących opisów.  
  (1) — na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.  
  (2) — usługa Key Vault wysyła żądanie certyfikatu SSL z urzędem certyfikacji.  
  (3) — aplikacji ankiety w procesie pętli i oczekiwania dla usługi Key Vault do wypełnienia certyfikatu. Tworzenie certyfikatu została ukończona w przypadku, gdy usługi Key Vault odbiera odpowiedź urzędu certyfikacji za pomocą x509 certyfikatu.  
  (4) — urząd certyfikacji odpowiada na żądania certyfikatu w programie usługi Key Vault protokołu SSL za pomocą X509 certyfikatu SSL.  
  (5) — usługi tworzenia nowego certyfikatu kończy się z połączeniem X509 certyfikatu urzędu certyfikacji.  

  Użytkownik usługi Key Vault — tworzy certyfikat, określając zasady

  -   W razie potrzeby powtórz  
  -   Warunki ograniczające zasady  
      -   X509 właściwości  
      -   Właściwości klucza  
      -   Informacje dotyczące dostawcy - > np. MyDigiCertIssure  
      -   Informacje o odnowienie - > np. 90 dni przed wygaśnięciem  

  - Proces tworzenia certyfikatów jest zwykle proces asynchroniczny i obejmuje sondowanie stanu operacji certyfikatu tworzenia magazynu kluczy.  
[Operacja pobrania certyfikatu](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Stan: zakończenia, nie powiodło się. informacje o błędach lub anulowane  
      -   Ze względu na opóźnienie do tworzenia można zainicjować operacji anulowania. Anuluj mogą być lub może nie obowiązywać.  

## <a name="import-a-certificate"></a>Importowanie certyfikatu  
 Alternatywnie — certyfikatu można zaimportować do usługi Key Vault — PFX lub PEM.  

 Aby uzyskać więcej informacji na PEM format, zobacz sekcję certyfikaty [o kluczy, wpisów tajnych i certyfikatów](about-keys-secrets-and-certificates.md).  

 Zaimportuj certyfikat — wymaga PEM lub PFX znajdować się na dysku, klucz prywatny. 
-   Należy określić: Magazyn nazwę oraz nazwę certyfikatu (zasad jest opcjonalny)

-   PEM / pliki PFX zawiera atrybuty, które KV można przeanalizować i służy do wypełniania zasad certyfikatu. Jeśli zasady certyfikatów jest już określona, KV podejmie próbę dopasowania danych z PFX / PEM pliku.  

-   Po ostatnim importu kolejne operacje będą korzystać z nowych zasad (nowe wersje).  

-   Jeśli nie istnieją żadne dalsze operacje, pierwszą rzeczą, jaką usługa Key Vault robi to, Wyślij powiadomienie o wygaśnięciu. 

-   Ponadto użytkownik może edytować zasadę, która będzie działać w czasie importowania, ale zawiera ustawienia domyślne, gdzie określono żadnych informacji o importu. Np. Brak informacji o wystawcy  

### <a name="formats-of-import-we-support"></a>Formaty obsługiwane jest importowanie
Firma Microsoft obsługuje następujący typ importu dla formatu plików PEM. Wraz z PKCS #8 zakodowane, klucz niezaszyfrowany, który ma jeden certyfikat zakodowany w formacie PEM

---BEGIN CERTYFIKATU------END CERTYFIKATU---

---BEGIN PRYWATNEGO KLUCZA------END PRYWATNE KLUCZ---

Na scalanie certyfikatów, firma Microsoft obsługuje 2 PEM na podstawie formatów. Albo można scalać pojedynczego zakodowanego certyfikatu PKCS #8 lub plik P7B zakodowany w formacie base64. ---BEGIN CERTYFIKATU------END CERTYFIKATU---

Obecnie nie obsługujemy WE kluczy w formacie PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędu certyfikacji nie użyły usługi Key Vault  
 Ta metoda umożliwia pracę z innych urzędów certyfikacji od dostawcy utworzyły usługi Key Vault, co oznacza, że Twoja organizacja może współpracować z urzędu certyfikacji do własnego wyboru.  

![Utwórz certyfikat z urzędu certyfikacji](media/certificate-authority-1.png)  

 Poniższe opisy kroków odpowiadają zielony kroki własną literą na powyższym diagramie.  

  (1) — na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie rozpoczyna się przez utworzenie klucza w magazynie kluczy.  

  (2) — usługa Key Vault zwraca do aplikacji żądania podpisania certyfikatu (CSR).  

  (3) — aplikacja przekazuje plik CSR do wybranego urzędu certyfikacji.  

  (4) — wybranego urzędu certyfikacji odpowiada za pomocą X509 certyfikatu.  

  (5) — nowe tworzenia certyfikatów przy użyciu połączenia X509 zakończeniu aplikacji certyfikatu z urzędu certyfikacji.

## <a name="see-also"></a>Zobacz też

- [Informacje o kluczach, wpisów tajnych i certyfikatów](about-keys-secrets-and-certificates.md)

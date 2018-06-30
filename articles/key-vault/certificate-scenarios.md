---
title: Wprowadzenie do magazynu kluczy certyfikatów
description: Poniższe scenariusze przedstawiają kilka głównej użycia usługi Key Vault certyfikatu zarządzania dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy, w tym.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: f1a1a2fa083dd1bf02132e08981d736a17a2c58f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109488"
---
# <a name="get-started-with-key-vault-certificates"></a>Wprowadzenie do magazynu kluczy certyfikatów
Poniższe scenariusze przedstawiają kilka głównej użycia usługi Key Vault certyfikatu zarządzania dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy, w tym.

Podane są:
- Tworzenie pierwszego certyfikatu usługi Key Vault
- Tworzenie certyfikatu z urzędu certyfikacji jest we współpracy z magazynu kluczy
- Tworzenie certyfikatu z urzędu certyfikacji, który nie jest współpracę z magazynu kluczy
- Importowanie certyfikatu

## <a name="certificates-are-complex-objects"></a>Certyfikaty są złożonych obiektów
Certyfikaty składają się z trzech zasobów połączonych ze sobą połączone ze sobą jako certyfikat usługi Key Vault; metadane certyfikatu, klucza i klucz tajny.


![Certyfikaty są złożone](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Tworzenie pierwszego certyfikatu usługi Key Vault  
 Certyfikat można było utworzyć w magazynie kluczy (KV), musi pomyślnie wykonać wymagane wstępnie kroki 1 i 2 oraz magazynu kluczy musi istnieć dla tego użytkownika / organizacji.  

**Krok 1** -certyfikatu urzędu certyfikacji dostawców  
-   Dołączania jako administrator IT, administrator infrastruktury kluczy publicznych lub każdy zarządzania kontami z urzędów certyfikacji, dla danej firmy (np. Firma Contoso) jest wymagana do korzystania z certyfikatów usługi Key Vault.  
    Następujące adresy są bieżącego dostawcy partnered z magazynu kluczy:  
    -   DigiCert - Key Vault oferuje OV SSL z DigiCert certyfikatów.  
    -   GlobalSign - Key Vault oferuje OV SSL z GlobalSign certyfikatów  
    -   WoSign - ofert usługi Key Vault OV SSL lub certyfikatów SSL Weryfikacją z WoSign na podstawie ustawienia skonfigurowane przez klienta na koncie WoSign portalu WoSign.  

**Krok 2** -administrator konta dla dostawcy urzędu certyfikacji powoduje utworzenie poświadczenia do użycia przez magazyn kluczy, aby zarejestrować, odnowienia i korzystać z certyfikatów SSL za pośrednictwem usługi Key Vault.

**Krok 3** -admin A Contoso, wraz z pracownika firmy Contoso (Key Vault użytkownika), który jest właścicielem certyfikatów, w zależności od urzędu certyfikacji, można uzyskać certyfikat od administratora lub bezpośrednio z konta z urzędem certyfikacji.  

-   Rozpocznij od utworzenia operacja dodawania poświadczeń do magazynu kluczy [wystawca certyfikatu](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers) zasobów. 
    -   Przykład. MyDigiCertIssuer  
        -   Dostawca  
        -   Poświadczenia — poświadczenia konta urzędu certyfikacji. W każdym urzędzie certyfikacji ma własną określonych danych.  

     Aby uzyskać więcej informacji na temat tworzenia kont z dostawcami urzędu certyfikacji, zobacz wpis pokrewne na [blogu usługi Key Vault](http://aka.ms/kvcertsblog).  

**Krok 3.1** — Ustawianie [certyfikatu kontaktów](https://docs.microsoft.com/rest/api/keyvault/certificate-contacts) powiadomień. To jest kontakt dla użytkownika usługi Key Vault. Magazyn kluczy nie obsługuje wymuszania tego kroku.  

Uwaga — Ten proces, za pomocą kroku 3.1, jest jednorazowe operacji.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędu certyfikacji we współpracy z magazynu kluczy

![Utwórz certyfikat z urzędu certyfikacji współpracę usługi Key Vault](media/certificate-authority-2.png)

**Krok 4** -poniższymi opisami odpowiadają zielony ponumerowane kroki na powyższym diagramie.  
  (1) — na powyższym diagramie, aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.  
  (2) - Key Vault wysyła żądanie certyfikatu SSL do urzędu certyfikacji.  
  (3) - aplikacji sonduje, w ramach procesu pętli i oczekiwania dla magazynu kluczy do wypełnienia certyfikatu. Utworzenie certyfikatu została ukończona, gdy usługi Key Vault otrzyma odpowiedź urzędu certyfikacji z x509 certyfikatu.  
  (4) - odpowiada Key Vault żądania certyfikatu SSL za z X509 urzędu certyfikacji certyfikatu SSL.  
  (5) - zakończeniu Twojej Tworzenie nowego certyfikatu z połączeniem X509 certyfikatu urzędu certyfikacji.  

  Użytkownik usługi Key Vault — tworzy certyfikaty, określając zasady

  -   W razie potrzeby powtórz  
  -   Warunki ograniczające zasady  
      -   X509 właściwości  
      -   Właściwości klucza  
      -   Informacje dotyczące dostawcy - > np. MyDigiCertIssure  
      -   Informacje o odnowienie - > np. 90 dni przed wygaśnięciem  

  - Proces tworzenia certyfikatu jest zwykle proces asynchroniczny i obejmuje sondowania magazynu kluczy dla stanu operacji tworzenia certyfikatu.  
[Pobierz certyfikat operacji](https://docs.microsoft.com/en-us/rest/api/keyvault/getcertificateoperation)  
      -   Stan: zakończona, nie powiodło się. informacje o błędzie lub anulowane  
      -   Z powodu opóźnienia, aby utworzyć można zainicjować operacji anulowania. Anuluj może lub nie może być skuteczne.  

## <a name="import-a-certificate"></a>Importowanie certyfikatu  
 Alternatywnie — certyfikatów można zaimportować do magazynu kluczy — PFX lub PEM.  

 Aby uzyskać więcej informacji o formacie PEM, zobacz sekcję certyfikaty [o kluczy, kluczy tajnych i certyfikaty](about-keys-secrets-and-certificates.md).  

 Zaimportuj certyfikat — wymaga PEM lub PFX może znajdować się na dysku, a klucz prywatny. 
-   Należy określić: Magazyn nazwy i nazwy certyfikatu (zasad jest opcjonalny)

-   PEM / pliki PFX zawiera atrybuty, które KV można przeanalizować i służy do wypełniania zasady certyfikatów. Jeśli zasady dotyczące certyfikatów został już określony, KV podejmie próbę dopasowania danych z PFX / pliku PEM.  

-   Po ostatnim importu kolejnych operacji będzie korzystać z nowych zasad (nowe wersje).  

-   Jeśli nie są żadne dalsze operacje, w pierwszej kolejności Key Vault robi to wysłać powiadomienie o wygaśnięciu. 

-   Ponadto użytkownik może edytować zasadę, która będzie działać w czasie importu, ale zawiera ustawienia domyślne, gdzie na import nie określono żadnych informacji. Przykład. Brak informacji o wystawcy  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędu certyfikacji nie we współpracy z magazynu kluczy  
 Ta metoda umożliwia pracę z innych urzędów certyfikacji niż Key Vault współpracę dostawców, co oznacza, że Twoja organizacja może współpracować z urzędu certyfikacji siebie.  

![Utwórz certyfikat z urzędu certyfikacji](media/certificate-authority-1.png)  

 Poniższe opisy kroków odpowiadają zielony własną literą kroki na powyższym diagramie.  

  (1) — na powyższym diagramie, aplikacja tworzy certyfikat, który wewnętrznie rozpoczyna się od utworzenia klucza w magazynie kluczy.  

  (2) - Key Vault zwraca do aplikacji żądanie (Podpisania certyfikatu).  

  (3) — aplikacja przekazuje obsługi do wybranego urzędu certyfikacji.  

  (4) - odpowiada X509 wybranego urzędu certyfikacji certyfikatu.  

  (5) — aplikacja zakończy tworzenie nowego certyfikatu z połączenia z X509 certyfikatu z urzędu certyfikacji.

## <a name="see-also"></a>Zobacz też
- [Operacji certyfikatu](/rest/api/keyvault/certificate-operations)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)

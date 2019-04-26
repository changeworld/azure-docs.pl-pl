---
title: Metody tworzenia certyfikatów
description: Sposoby, aby utworzyć certyfikat w usłudze Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 53b4d03ac8f5d22595d3a4e840a04583f7ec963d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640852"
---
# <a name="certificate-creation-methods"></a>Metody tworzenia certyfikatów

 Certyfikat klucza magazynu (KV) można utworzyć lub zaimportować do magazynu kluczy. Po utworzeniu certyfikatu KV klucz prywatny jest tworzony w magazynie kluczy i nie są udostępniane do właściciela certyfikatu. Poniżej przedstawiono sposoby tworzenia certyfikatu w usłudze Key Vault:  

-   **Utwórz certyfikat z podpisem własnym:** Spowoduje to utworzenie pary kluczy publiczny prywatny i skojarzyć go z certyfikatu. Certyfikat zostanie podpisany przez własnego klucza.  

-    **Ręczne tworzenie nowego certyfikatu:** Spowoduje to utworzenie pary kluczy publiczny prywatny i wygenerować żądanie podpisania certyfikatu X.509. Żądanie podpisania może być podpisany przez urząd rejestrowania lub urzędu certyfikacji. Sparuj x509 podpisanego certyfikatu będzie mogło zostać scalone z kluczem oczekujące do ukończenia KV certyfikatu w usłudze Key Vault. Mimo że ta metoda wymaga większej liczby kroków, jego zapewniają większe bezpieczeństwo, ponieważ klucz prywatny jest tworzone w i ograniczone do usługi Key Vault. Jest to wyjaśnione na poniższym diagramie.  

![Utwórz certyfikat z urzędu certyfikacji](media/certificate-authority-1.png)  

Poniższe opisy odpowiadają zielony kroki własną literą na powyższym diagramie.

1. Na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Usługa Key Vault zwraca do aplikacji żądania podpisania certyfikatu (CSR)
3. Aplikacja przekazuje plik CSR do wybranego urzędu certyfikacji.
4. Wybranego urzędu certyfikacji odpowiada za pomocą X509 certyfikatu.
5. Nowe tworzenia certyfikatów przy użyciu połączenia X509 zakończeniu aplikacji certyfikatu z urzędu certyfikacji.

-   **Utwórz certyfikat z dostawcą znanych wystawcy:** Ta metoda wymaga wykonania jednorazowe zadania tworzenia obiektu wystawcy. Po utworzeniu obiektu wystawcy w przypadku magazynu kluczy, nazwy mogą być przywoływane w zasadach certyfikatów KV. Żądanie, aby utworzyć certyfikat KV spowoduje tworzenie pary kluczy w magazynie i komunikować się z usługą dostawcy wystawcy, korzystając z informacji w obiekcie wystawcy odwołania można pobrać x509 certyfikatu. X509 certyfikat jest pobierany z usługi wystawcy i jest scalany z pary kluczy, aby ukończyć KV tworzenia certyfikatów.  

![Utwórz certyfikat z urzędu certyfikacji usługi Key Vault partnerstwo](media/certificate-authority-2.png)  

Poniższe opisy odpowiadają zielony kroki własną literą na powyższym diagramie.

1. Na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Wysyła do usługi Key Vault i żądanie certyfikatu SSL z urzędem certyfikacji.
3. Aplikacji ankiety w procesie pętli i oczekiwania dla usługi Key Vault do wypełnienia certyfikatu. Tworzenie certyfikatu została ukończona w przypadku, gdy usługi Key Vault odbiera odpowiedź urzędu certyfikacji za pomocą x509 certyfikatu.
4. Urząd certyfikacji odpowiada na żądania certyfikatu w programie usługi Key Vault protokołu SSL za pomocą X509 certyfikatu SSL.
5. Twoje Tworzenie nowego certyfikatu kończy się z połączeniem X509 certyfikatu urzędu certyfikacji.

## <a name="asynchronous-process"></a>Proces asynchroniczny
Tworzenie certyfikatu KV jest proces asynchroniczny. Ta operacja spowoduje tworzenia żądania certyfikatu KV i zwrócić kod stanu http 202 (zaakceptowano). Stan żądania mogą być śledzone przez sondowanie oczekujące obiekt utworzony przez tę operację. Pełny identyfikator URI obiektu oczekujące jest zwracany w nagłówku LOCATION.  

Po zakończeniu żądania można utworzyć certyfikatu KV, stan obiektu oczekujące zmieni się na "ukończone" od "w toku", a następnie zostanie utworzona nowa wersja certyfikatu KV. Stanie się bieżącą wersję.  

## <a name="first-creation"></a>Tworzenie pierwszej
 Po utworzeniu certyfikatu KV po raz pierwszy adresowalnych kluczem i wpisem tajnym również jest tworzony o takiej samej nazwie, jak w przypadku certyfikatu. Jeśli nazwa jest już w użyciu, operacja zakończy się niepowodzeniem z kodem stanu http 409 (konflikt).
Mogą być adresowane kluczem i wpisem tajnym uzyskiwanie atrybuty certyfikatu KV ich atrybutów. Mogą być adresowane klucza i wpisu tajnego, utworzone w ten sposób są oznaczane jako zarządzanych kluczy i wpisów tajnych, którego okres istnienia jest zarządzany przez usługę Key Vault. Zarządzanych kluczy i wpisów tajnych są przeznaczone tylko do odczytu. Uwaga: Jeśli certyfikat KV wygaśnie lub jest wyłączona, odpowiedniego klucza i wpisu tajnego staną się przestanie działać.  

 Jeśli jest to pierwszy operację, aby utworzyć certyfikat KV, zasady są wymagane.  Zasady można również otrzymywać kolejne operacje, aby zastąpić zasób zasad tworzenia. Jeśli zasada nie zostanie podany, zasób zasad w usłudze jest używany do tworzenia następnej wersji KV certyfikatu. Należy zauważyć, że gdy żądanie do tworzenia następnej wersji jest w trakcie wykonywania bieżącego certyfikatu KV i odpowiedniego klucza adresowalnych i klucz tajny, pozostają bez zmian.  

## <a name="self-issued-certificate"></a>Certyfikat wystawiony samodzielnie
 Aby utworzyć własny wystawionego certyfikatu, ustaw nazwę wystawcy jako "Self" w zasadach certyfikatów, jak pokazano w poniższy fragment kodu z zasady dotyczące certyfikatów.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Jeśli nie określono nazwy wystawcy, następnie nazwa wystawcy jest równa "Nieznane". Gdy wystawcy jest "Nieznana", właściciela certyfikatu będzie musiał ręcznie Pobierz x509 certyfikat od wystawcy wybranego przez siebie, a następnie scalania x509 publicznego certyfikatu z certyfikatem usługi key vault oczekujące obiekt, aby zakończyć tworzenie certyfikatu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Dostawców utworzyły urzędu certyfikacji
Tworzenie certyfikatu może być ukończone ręcznie lub przy użyciu "Self" wystawcy. Usługi Key Vault współpracuje również z niektórych dostawców wystawcy, ułatwiając tworzenie certyfikatów. Następujące typy certyfikatów może zostać określona dla usługi key vault przy użyciu tych dostawców wystawcy partnera.  

|Dostawca|Typ certyfikatu|  
|--------------|----------------------|  
|DigiCert|Usługa Key Vault oferuje OV lub Weryfikacją certyfikatów SSL z firmy DigiCert|
|GlobalCert|Usługa Key Vault oferuje OV lub Weryfikacją certyfikatów SSL z GlobalSign|

 Wystawca certyfikatu jest reprezentowana w usłudze Azure klucza magazynu (KV) jako zasób CertificateIssuer jednostki. Umożliwia podawanie informacji o źródle certyfikatu KV; Nazwa wystawcy, dostawca, poświadczeń i inne szczegóły administracyjne.

Należy pamiętać, że gdy zamówienie jest umieszczany za pomocą dostawcy wystawcy, jego może uwzględnić lub zastąpienie x509 rozszerzenia certyfikatu i okres ważności certyfikatu na podstawie typu certyfikatu.  

 Autoryzacja: Wymagane jest uprawnienie/tworzenia certyfikatów.

## <a name="see-also"></a>Zobacz też
 - [Temat kluczy, wpisów tajnych i certyfikatów](about-keys-secrets-and-certificates.md)
 - [Monitorowanie tworzenia certyfikatów i zarządzanie nim](create-certificate-scenarios.md)

---
title: Metody tworzenia certyfikatów
description: Metody tworzenia certyfikatu w magazynie kluczy.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 7b71c6a8daa97300ecf3b37ec6ab47207fece98e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="certificate-creation-methods"></a>Metody tworzenia certyfikatów

 Certyfikat klucza magazynu (KV) można utworzyć lub zaimportować do magazynu kluczy. Po utworzeniu certyfikatu KV klucza prywatnego jest wewnątrz magazynu kluczy i nigdy nie są widoczne dla właściciela certyfikatu. Poniżej przedstawiono sposoby tworzenia certyfikatu w magazynie kluczy:  

-   **Utwórz certyfikat z podpisem własnym:** spowoduje to utworzenie pary kluczy publiczno prywatnych i skojarzyć go z certyfikatu. Certyfikat zostanie podpisana przez własnego klucza.  

-    **Utwórz nowy certyfikat ręcznie:** spowoduje to utworzenie pary kluczy publiczno prywatnych i wygenerować żądania podpisania certyfikatu X.509. Żądania podpisywania może być podpisana przez urząd certyfikacji lub urzędu rejestrowania. Sparuj podpisem x509 certyfikatu można łączone przy użyciu klucza oczekujące przeprowadzenie KV certyfikat w magazynie kluczy. Ta metoda wymaga większej liczby kroków, jego umożliwiają większe bezpieczeństwo, ponieważ klucz prywatny jest tworzony w i ograniczona do magazynu kluczy. Wyjaśnienie jest zawarte na poniższym diagramie.  

![Utwórz certyfikat z urzędu certyfikacji](media/certificate-authority-1.png)  

Poniższe opisy odpowiadają zielony własną literą kroki na powyższym diagramie.

1. Na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Key Vault zwraca do aplikacji żądanie (Podpisania certyfikatu)
3. Aplikacja przekazuje obsługi do wybranego urzędu certyfikacji.
4. Odpowiada wybranego urzędu certyfikacji X509 certyfikatu.
5. Aplikacja kończy tworzenie nowego certyfikatu z połączenia z X509 certyfikatu z urzędu certyfikacji.

-   **Utwórz certyfikat z dostawcą znane wystawcy:** tej metody wymaga wykonania jednorazowe zadania tworzenia obiektu wystawcy. Po utworzeniu obiektu wystawcy należy klucz magazynu, można odwoływać się w zasadach certyfikatów KV jego nazwy. Żądanie utworzenia certyfikatu KV utworzy pary kluczy w magazynie i komunikować się z usługą dostawcy wystawcy, korzystając z informacji w obiekcie przywoływanego wystawcy uzyskanie x509 certyfikatu. X509 certyfikat są pobierane z usługi wystawców i jest scalany z pary kluczy do ukończenia KV certyfikatów tworzenia.  

![Utwórz certyfikat z urzędu certyfikacji współpracę usługi Key Vault](media/certificate-authority-2.png)  

Poniższe opisy odpowiadają zielony własną literą kroki na powyższym diagramie.

1. Na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Wysyła magazyn kluczy i żądania certyfikatu SSL do urzędu certyfikacji.
3. Aplikacji sonduje, w ramach procesu pętli i oczekiwania w usłudze magazyn kluczy do wypełnienia certyfikatu. Utworzenie certyfikatu została ukończona, gdy usługi Key Vault otrzyma odpowiedź urzędu certyfikacji z x509 certyfikatu.
4. Urząd certyfikacji odpowiada na żądania certyfikatu w programie usługi Key Vault SSL z X509 certyfikatu SSL.
5. Kończy tworzenie z nowego certyfikatu z połączeniem X509 certyfikatu urzędu certyfikacji.

## <a name="asynchronous-process"></a>Proces asynchroniczny
Utworzenie certyfikatu KV jest proces asynchroniczny. Ta operacja spowoduje Utwórz żądanie certyfikatu KV i zwraca kod stanu http 202 (zaakceptowane). Stan żądania mogą być śledzone przez sondowanie Oczekujący obiekt utworzony przez tę operację. Pełny identyfikator URI obiektu oczekujące jest zwrócony w nagłówku lokalizacji.  

Po ukończeniu żądanie, aby utworzyć certyfikat KV stan Oczekujący obiekt zostanie zmieniony na "ukończone" od "w toku", i zostanie utworzona nowa wersja KV certyfikatu. Będzie to bieżącej wersji.  

## <a name="first-creation"></a>Tworzenie pierwszego
 Po utworzeniu certyfikatu KV po raz pierwszy adresowanego klucza i klucz tajny tworzona jest również o takiej samej nazwie jak certyfikatu. Jeśli nazwa jest już w użyciu, operacja zakończy się niepowodzeniem z kodem stanu http 409 (konflikt).
Klucz adresowanego i klucz tajny odczytać atrybuty certyfikatu KV ich atrybutów. Klucz adresowanego i klucz tajny utworzone w ten sposób zostały oznaczone jako zarządzanych kluczy i kluczy tajnych, którego okres istnienia jest zarządzany przez magazyn kluczy. Zarządzanych kluczy i kluczy tajnych są tylko do odczytu. Uwaga: Jeśli KV certyfikat wygaśnie lub jest wyłączona, odpowiedniego klucza i klucz tajny będzie przestać działać.  

 Jeśli jest to pierwszy operację, aby utworzyć certyfikat KV zasad jest wymagane.  Zasady należy dostarczyć także z kolejnych tworzenia operacji, aby zastąpić zasób zasad. Jeśli nie podano zasady, zasobu zasad w usłudze jest używany do tworzenia następnej wersji KV certyfikatu. Należy pamiętać, że gdy żądanie do tworzenia następnej wersji jest w trakcie wykonywania bieżącego certyfikatu KV i odpowiadające im klucze mogą być adresowane i klucz tajny, pozostają bez zmian.  

## <a name="self-issued-certificate"></a>Samodzielnie wystawiony certyfikat
 Aby utworzyć certyfikat wystawiony samodzielnie, Ustaw jako nazwa wystawcy "Auto" w zasadach certyfikatów, jak pokazano w poniższym fragmencie zasady dotyczące certyfikatów.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Jeśli nazwa wystawcy nie jest określony, nazwa wystawcy jest wartość "Nieznane". Gdy wystawcy nie jest "Nieznana", właściciela certyfikatu będzie musiał ręcznie pobrać x509 certyfikatu od wystawcy wybranego przez siebie, a następnie scalania x509 publicznego certyfikatu z magazynu kluczy certyfikatu oczekujących obiekt, aby zakończyć tworzenie certyfikatu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Dostawców współpracę urzędu certyfikacji
Tworzenie certyfikatu może być wykonane ręcznie lub używając "własnym" wystawcy. Magazyn kluczy także współpracuje z niektórych dostawcy wystawcy uproszczenie procesu tworzenia certyfikatów. Następujące typy certyfikatów może zostać określona dla magazynu kluczy z tych dostawców wystawcy partnera.  

|Dostawca|Typ certyfikatu|  
|--------------|----------------------|  
|DigiCert|Magazyn kluczy oferuje OV lub Weryfikacją certyfikatów SSL z DigiCert|
|GlobalCert|Magazyn kluczy oferuje OV lub Weryfikacją certyfikatów SSL z GlobalSign|

 Aby uzyskać więcej informacji, w tym dostępność tych dostawców wystawcy, zobacz [wystawców certyfikatów](/rest/api/keyvault/certificate-issuers.md).

Zauważ, że gdy jest zamówienie dostawcy wystawcy, mogą uznawać go lub zastąpienie x509 rozszerzenia certyfikatu i okres ważności certyfikatu na podstawie typu certyfikatu.  

 Autoryzacji: Wymaga uprawnienia do tworzenia/certyfikatów.

 ## <a name="see-also"></a>Zobacz też
 - [Temat kluczy, kluczy tajnych i certyfikatów](about-keys-secrets-and-certificates.md)
 - [Monitorowanie i zarządzanie nimi Tworzenie certyfikatu](create-certificate-scenarios.md)

---
title: Dozwolone urzędów certyfikacji umożliwiające niestandardowego protokołu HTTPS w usłudze Azure Service wejściu | Dokumentacja firmy Microsoft
description: Jeśli używasz własnego certyfikatu, aby włączyć protokół HTTPS w domenie niestandardowej, musi być go utworzyć urzędu dozwolonych certyfikacji (CA).
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 5cf94079dcd68887d9725ffbe9124f9b6c897dd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736160"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Dozwolone urzędów certyfikacji umożliwiające niestandardowego protokołu HTTPS w usłudze Azure Service drzwi

Dla domeny niestandardowej usługi Azure Service drzwiami frontowymi podczas możesz [Włączanie funkcji protokołu HTTPS przy użyciu własnego certyfikatu](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), należy użyć urzędu certyfikacji dozwolonych (CA) do utworzenia certyfikatu SSL. W przeciwnym razie jeśli używasz innego niż dozwolone urzędu certyfikacji lub certyfikatu z podpisem własnym, Twoje żądanie zostanie odrzucone.

Następujące urzędów certyfikacji są dozwolone podczas tworzenia własnego certyfikatu:

- Główny AddTrust zewnętrznego urzędu certyfikacji
- AlphaSSL głównego urzędu certyfikacji
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- Wschodni Region Azji i głównego urzędu certyfikacji
- Wschodni Region Azji i główny urząd certyfikacji 2013
- Wschodni Region Azji i główny urząd certyfikacji 2014
- APCA-DM3P
- Rozwiązania autopilot głównego urzędu certyfikacji
- Główny firmy CyberTrust Baltimore
- Klasa 3 publicznego podstawowej urzędu certyfikacji
- Urząd certyfikacji COMODO RSA
- COMODO RSA domeny sprawdzania poprawności serwera zabezpieczeń urzędu certyfikacji
- D-TRUST Root Class 3 CA 2 2009
- Usługi w chmurze firmy DigiCert 1 urzędu certyfikacji
- DigiCert Global Root CA
- Zapewnienie wysokiej DigiCert urzędu certyfikacji-3
- Wysoka DigiCert Assurance Weryfikacją głównego urzędu certyfikacji
- Algorytmu DigiCert SHA2 rozszerzone sprawdzania poprawności serwera urzędu certyfikacji
- Algorytmu DigiCert SHA2 wysokiego bezpieczeństwa serwera urzędu certyfikacji
- Algorytmu DigiCert SHA2 serwera zabezpieczeń urzędu certyfikacji
- DST Root CA X3
- D-trust Root Class 3 CA 2 2009
- Wszędzie, gdzie szyfrowanie TLS DV urzędu certyfikacji
- Główny urząd certyfikacji Entrust
- Entrust główny urząd certyfikacji — G2
- Urząd certyfikacji Entrust.NET (2048)
- GeoTrust globalnego urzędu certyfikacji
- Podstawowy GeoTrust urzędu certyfikacji
- Urząd certyfikacji podstawowy GeoTrust - G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign rozszerzone G2 CA - SHA256 - sprawdzania poprawności
- Sprawdzanie poprawności organizacji GlobalSign CA - G2
- GlobalSign głównego urzędu certyfikacji
- Go Daddy główny urząd certyfikacji — G2
- Go Daddy bezpiecznego certyfikatu urzędu - G2
- RapidSSL RSA CA 2018
- Agencja głównego
- Klasa Symantec 3 Weryfikacją certyfikatu SSL urzędu - G3
- Klasy firmy Symantec 3 serwera zabezpieczeń urzędu certyfikacji - 4. generacji
- Symantec Enterprise Mobile Root for Microsoft
- Podstawowy Thawte głównego urzędu certyfikacji
- Podstawowy Thawte głównego urzędu certyfikacji — G2
- Podstawowy Thawte głównego urzędu certyfikacji — G3
- RSA Thawte 2018 urzędu certyfikacji
- Sygnatura czasowa Thawte urzędu certyfikacji
- TrustAsia TLS RSA CA
- Klasa VeriSign 3 rozszerzona Walidacja certyfikatu SSL urzędu
- Klasa VeriSign 3 rozszerzona Walidacja SSL SGC urzędu certyfikacji
- Klasa VeriSign 3 publiczny podstawowy urząd certyfikacji — 5. generacji
- VeriSign międzynarodowych serwera urzędu certyfikacji — klasa 3
- Czas VeriSign sygnatur element główny usługi
- Uniwersalny VeriSign głównego urzędu certyfikacji

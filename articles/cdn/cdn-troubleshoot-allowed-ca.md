---
title: Dozwolone urzędów certyfikacji umożliwiające niestandardowy protokół HTTPS dla usługi Azure CDN | Dokumentacja firmy Microsoft
description: Jeśli używasz własnego certyfikatu, aby włączyć protokół HTTPS w domenie niestandardowej, musi być go utworzyć urzędu dozwolonych certyfikacji (CA).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e9d9f5b053537b3d6881763bcb39d7894ba78869
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969512"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Dozwolone urzędów certyfikacji umożliwiające niestandardowy protokół HTTPS dla usługi Azure CDN

Dla domeny niestandardowej usługi Azure Content Delivery Network (CDN) w **Azure CDN w warstwie standardowa firmy Microsoft** punktu końcowego, gdy użytkownik [Włączanie funkcji protokołu HTTPS przy użyciu własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), należy użyć dozwolone Urząd certyfikacji (CA) do utworzenia certyfikatu SSL. W przeciwnym razie jeśli używasz innego niż dozwolone urzędu certyfikacji lub certyfikatu z podpisem własnym, Twoje żądanie zostanie odrzucone.

> [!NOTE]
> Możliwość korzystania z własnego certyfikatu włączyć niestandardowy protokół HTTPS jest dostępna tylko w przypadku **Azure CDN Standard from Microsoft** profilów. 
>

Następujące urzędów certyfikacji są dozwolone podczas tworzenia własnego certyfikatu:

- Główny AddTrust zewnętrznego urzędu certyfikacji
- AlphaSSL głównego urzędu certyfikacji
- Nazwa infrastruktury urzędu certyfikacji 01
- Nazwa infrastruktury urzędu certyfikacji 02
- Ameroot
- Wschodni Region Azji i głównego urzędu certyfikacji
- Wschodni Region Azji i główny urząd certyfikacji 2013
- Wschodni Region Azji i główny urząd certyfikacji 2014
- APCA DM3P
- Rozwiązania autopilot głównego urzędu certyfikacji
- Główny firmy CyberTrust Baltimore
- Klasa 3 publicznego podstawowej urzędu certyfikacji
- Urząd certyfikacji COMODO RSA
- COMODO RSA domeny sprawdzania poprawności serwera zabezpieczeń urzędu certyfikacji
- Urząd certyfikacji klasy 3 głównym zaufania D 2 2009
- Usługi w chmurze firmy DigiCert 1 urzędu certyfikacji
- Firmy DigiCert globalnego głównego urzędu certyfikacji
- Zapewnienie wysokiej DigiCert urzędu certyfikacji-3
- Wysoka DigiCert Assurance Weryfikacją głównego urzędu certyfikacji
- Algorytmu DigiCert SHA2 rozszerzone sprawdzania poprawności serwera urzędu certyfikacji
- Algorytmu DigiCert SHA2 wysokiego bezpieczeństwa serwera urzędu certyfikacji
- Algorytmu DigiCert SHA2 serwera zabezpieczeń urzędu certyfikacji
- Czas letni głównego urzędu certyfikacji X3
- Urząd certyfikacji klasy 3 głównym zaufania D 2 2009
- Wszędzie, gdzie szyfrowanie TLS DV urzędu certyfikacji
- Główny urząd certyfikacji Entrust
- Entrust główny urząd certyfikacji — G2
- Urząd certyfikacji Entrust.NET (2048)
- GeoTrust globalnego urzędu certyfikacji
- Podstawowy GeoTrust urzędu certyfikacji
- Urząd certyfikacji podstawowy GeoTrust - G2
- RSA Geotrust 2018 urzędu certyfikacji
- GlobalSign
- GlobalSign rozszerzone G2 CA - SHA256 - sprawdzania poprawności
- Sprawdzanie poprawności organizacji GlobalSign CA - G2
- GlobalSign głównego urzędu certyfikacji
- Go Daddy główny urząd certyfikacji — G2
- Go Daddy bezpiecznego certyfikatu urzędu - G2
- Microsoft Authenticode(tm) głównego urzędu certyfikacji
- Usługi Microsoft Exchange 2015 urzędu certyfikacji
- Wewnętrznym głównym firmy Microsoft
- Dział IT firmy Microsoft Pawłowski SSL urzędu certyfikacji 1
- Algorytm SHA1 SSL dział IT firmy Microsoft
- Algorytmu SHA2 SSL dział IT firmy Microsoft
- Dział IT firmy Microsoft TLS certyfikacji 1
- Dział IT firmy Microsoft TLS certyfikacji 2
- Dział IT firmy Microsoft TLS certyfikacji 4
- Dział IT firmy Microsoft TLS certyfikacji 5
- Microsoft głównego urzędu certyfikacji
- Microsoft główny urząd certyfikacji
- Microsoft główny urząd certyfikacji 2010
- Microsoft główny urząd certyfikacji 2011
- Microsoft bezpiecznego Server 2011 urzędu certyfikacji
- Główny Partner usług firmy Microsoft
- Główny usługi Oznaczanie sygnaturą czasową firmy Microsoft
- Zgodność sprzętu Microsoft Windows
- MSIT URZĘDU CERTYFIKACJI Z2
- Urząd certyfikacji przedsiębiorstwa MSIT 1
- Urząd certyfikacji przedsiębiorstwa MSIT 3
- RSA RapidSSL 2018 urzędu certyfikacji
- Agencja głównego
- Klasa Symantec 3 Weryfikacją certyfikatu SSL urzędu - G3
- Klasy firmy Symantec 3 serwera zabezpieczeń urzędu certyfikacji - 4. generacji
- Urząd certyfikacji Symantec Enterprise głównego mobilnych dla firmy Microsoft
- Podstawowy Thawte głównego urzędu certyfikacji
- Podstawowy Thawte głównego urzędu certyfikacji — G2
- Podstawowy Thawte głównego urzędu certyfikacji — G3
- RSA Thawte 2018 urzędu certyfikacji
- Sygnatura czasowa Thawte urzędu certyfikacji
- RSA TLS TrustAsia urzędu certyfikacji
- UTN USERFirst obiektu
- Klasa VeriSign 3 rozszerzona Walidacja certyfikatu SSL urzędu
- Klasa VeriSign 3 rozszerzona Walidacja SSL SGC urzędu certyfikacji
- Klasa VeriSign 3 publiczny podstawowy urząd certyfikacji — 5. generacji
- VeriSign międzynarodowych serwera urzędu certyfikacji — klasa 3
- Czas VeriSign sygnatur element główny usługi
- Uniwersalny VeriSign głównego urzędu certyfikacji
- WMSvc-algorytmu SHA2-DALEDGE1008


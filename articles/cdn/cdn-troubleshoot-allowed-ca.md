---
title: Dozwolone urzędów certyfikacji do włączania niestandardowych HTTPS na Azure CDN | Dokumentacja firmy Microsoft
description: Jeśli używasz swój własny certyfikat, aby włączyć protokół HTTPS na domenę niestandardową, możesz korzystać dozwolonych certyfikacji (CA) do jego utworzenia.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3c41ca7e375324ff784bf7bee347bb56400ddfbd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238281"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Dozwolone urzędów certyfikacji do włączania niestandardowych HTTPS na Azure CDN

Dla domeny niestandardowej Azure sieci dostarczania zawartości (CDN) na **Azure CDN Standard from Microsoft** punktu końcowego, gdy użytkownik [włączyć funkcję HTTPS przy użyciu własnego certyfikatu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), należy użyć dozwolone Urząd certyfikacji (CA) do utworzenia certyfikatu SSL. W przeciwnym razie jeśli korzystasz z systemem innym niż dozwolone urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

> [!NOTE]
> Przy użyciu własnego certyfikatu, aby włączyć protokół HTTPS niestandardowego opcja jest dostępna tylko w przypadku **Azure CDN Standard from Microsoft** profilów. 
>

Następujące adresy są dozwolone podczas tworzenia własnego certyfikatu:

- Główny AddTrust zewnętrznego urzędu certyfikacji
- Region głównego urzędu certyfikacji
- Region główny urząd certyfikacji 2013
- Region główny urząd certyfikacji 2014 r.
- APCA DM3P
- Autopilot głównego urzędu certyfikacji
- Główny firmy CyberTrust Baltimore
- Klasa 3 publicznego głównej urzędu certyfikacji
- Urząd certyfikacji COMODO RSA
- COMODO RSA domeny sprawdzania poprawności serwera zabezpieczeń urzędu certyfikacji
- Głównego zaufania D klasy 3 urzędu certyfikacji 2 2009
- 1 urzędu certyfikacji usług w chmurze firmy DigiCert
- DigiCert globalne głównego urzędu certyfikacji
- Zapewnienie wysokiej DigiCert urzędu certyfikacji-3
- Wysoki DigiCert gwarancji Weryfikacją głównego urzędu certyfikacji
- DigiCert algorytmu SHA2 wysokiego bezpieczeństwa serwera urzędu certyfikacji
- DigiCert algorytmu SHA2 serwera zabezpieczeń urzędu certyfikacji
- GlobalSign
- GlobalSign rozszerzonej weryfikacji urzędu certyfikacji - SHA256 - G2
- Sprawdzanie poprawności organizacji GlobalSign urzędu certyfikacji - G2
- GlobalSign głównego urzędu certyfikacji
- Microsoft Authenticode(tm) głównego urzędu certyfikacji
- Usługi Microsoft Exchange 2015 urzędu certyfikacji
- Wewnętrznego głównego firmy Microsoft
- Dział IT firmy Microsoft SSL Pawłowski urzędu certyfikacji 1
- Dział IT firmy Microsoft SSL SHA1
- Algorytmu SHA2 SSL dział IT firmy Microsoft
- Dział IT firmy Microsoft TLS certyfikacji 1
- Dział IT firmy Microsoft TLS certyfikacji 2
- Dział IT firmy Microsoft TLS certyfikacji 4
- Dział IT firmy Microsoft TLS certyfikacji 5
- Microsoft głównego urzędu certyfikacji
- Microsoft główny urząd certyfikacji
- Microsoft główny urząd certyfikacji 2010
- Microsoft główny urząd certyfikacji 2011
- Microsoft Server bezpiecznego 2011 urzędu certyfikacji
- Główny Partner usług firmy Microsoft
- Katalogu głównego usługi sygnatura czasu firmy Microsoft
- Zgodności sprzętu systemu Microsoft Windows
- MSIT Z2 URZĘDU CERTYFIKACJI
- Urząd certyfikacji przedsiębiorstwa MSIT 1
- Urząd certyfikacji przedsiębiorstwa MSIT 3
- Agencji głównego
- Klasa Symantec 3 Weryfikacją SSL certyfikacji - G3
- Klasa Symantec 3 serwera zabezpieczeń urzędu certyfikacji - G4
- Przenośnych główny przedsiębiorstwa firmy Symantec dla firmy Microsoft
- Sygnatura czasowa Thawte urzędu certyfikacji
- UTN USERFirst obiektów
- Urząd certyfikacji SSL 3 rozszerzonej weryfikacji VeriSign — klasa
- Klasa VeriSign 3 rozszerzonej weryfikacji SSL SGC urzędu certyfikacji
- Klasa VeriSign 3 publicznego głównej urzędu certyfikacji - G5
- VeriSign międzynarodowe serwera urzędu certyfikacji - 3 — klasa
- Katalogu głównego usługi VeriSign czasu sygnaturze
- Uniwersalny VeriSign głównego urzędu certyfikacji
- WMSvc-algorytmu SHA2-DALEDGE1008


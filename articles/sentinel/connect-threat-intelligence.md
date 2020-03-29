---
title: Łączenie danych analizy zagrożeń z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane analizy zagrożeń z usługą Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588046"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Łączenie danych z dostawców analizy zagrożeń

> [!IMPORTANT]
> Łączniki danych analizy zagrożeń w usłudze Azure Sentinel są obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Sentinel umożliwia importowanie wskaźników zagrożeń używanych przez organizację, co może zwiększyć zdolność analityków zabezpieczeń do wykrywania i określania priorytetów znanych zagrożeń. Kilka funkcji usługi Azure Sentinel stają się dostępne lub są ulepszone:

- **Analytics** zawiera zestaw zaplanowanych szablonów reguł, które można włączyć do generowania alertów i incydentów na podstawie dopasowań zdarzeń dziennika ze wskaźników zagrożeń.

- **Skoroszyty** zawierają podsumowane informacje o wskaźnikach zagrożeń zaimportowanych do usługi Azure Sentinel i alertach generowanych na podstawie reguł analizy, które pasują do wskaźników zagrożeń.

- **Zapytania myśliwskie** umożliwiają badaczom bezpieczeństwa stosowanie wskaźników zagrożeń w kontekście typowych scenariuszy polowania.

- **Notesy** mogą używać wskaźników zagrożeń podczas badania anomalii i polowania na złośliwe zachowania.

Wskaźniki zagrożeń można przesyłać strumieniowo do usługi Azure Sentinel przy użyciu jednego z produktów zintegrowanej platformy analizy zagrożeń (TIP) wymienionych w następnej sekcji, łączenia się z serwerami TAXII lub przy użyciu bezpośredniej integracji z [interfejsem API tiIndicators zabezpieczeń programu Microsoft Graph](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Zintegrowane produkty platformy analizy zagrożeń

- [Platforma analizy zagrożeń open source MISP](https://www.misp-project.org/)
    
    Przykładowy skrypt, który udostępnia klientom wystąpienia MISP do migracji wskaźników zagrożeń do interfejsu API zabezpieczeń programu Microsoft Graph, zobacz [misp do programu Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Sieci MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Aby uzyskać instrukcje z [przewodnikiem, zobacz Wysyłanie międzynastocze adresów IOC do interfejsu API zabezpieczeń programu Microsoft Graph przy użyciu programu MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Platforma ThreatConnect](https://threatconnect.com/solution/)

    Aby uzyskać więcej informacji, zobacz [Integracje threatconnect](https://threatconnect.com/integrations/) i poszukaj interfejsu API zabezpieczeń programu Microsoft Graph na stronie.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Połącz usługę Azure Sentinel z platformą analizy zagrożeń

## <a name="prerequisites"></a>Wymagania wstępne  

- Rola usługi Azure AD administratora globalnego lub administratora zabezpieczeń w celu przyznania uprawnień do produktu TIP lub niestandardowej aplikacji korzystającej z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń programu Microsoft Graph.

- Uprawnienia do odczytu i zapisu w obszarze roboczym usługi Azure Sentinel do przechowywania wskaźników zagrożeń.

## <a name="instructions"></a>Instrukcje

1. [Zarejestruj aplikację](/graph/auth-v2-service#1-register-your-app) w usłudze Azure Active Directory, aby uzyskać identyfikator aplikacji, klucz tajny aplikacji i identyfikator dzierżawy usługi Azure Active Directory. Te wartości są potrzebne podczas konfigurowania zintegrowanego produktu lub aplikacji TIP, która korzysta z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń programu Microsoft Graph.

2. [Konfigurowanie uprawnień interfejsu API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) dla zarejestrowanej aplikacji: Dodaj uprawnienie **ThreatIndicators.ReadWrite.OwnedBy** do zarejestrowanej aplikacji.

3. Poproś administratora dzierżawy usługi Azure Active Directory o udzielenie zgody administratora zarejestrowanej aplikacji dla twojej organizacji. Z witryny Azure portal:**\<_Nazwa_>** > aplikacji**rejestracji** > usługi **Azure Active Directory** > **Wyświetl uprawnienia** > interfejsu API**Przyznanie zgody administratora \<dla nazwy> _dzierżawy_**.

4. Skonfiguruj swój produkt lub aplikację TIP, która używa bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń programu Microsoft Graph, aby wysyłać wskaźniki do usługi Azure Sentinel, określając następujące elementy:
    
    a. Wartości identyfikatora, klucza tajnego i identyfikatora dzierżawy zarejestrowanej aplikacji.
    
    b. Dla produktu docelowego określ usługę Azure Sentinel.
    
    d. Dla akcji określ alert.

5. W witrynie Azure portal przejdź do łączników **usługi Azure Sentinel** > **Data,** a następnie wybierz łącznik **platform analizy zagrożeń (wersja zapoznawcza).**

6. Wybierz **pozycję Otwórz stronę łącznika**, a następnie pozycję **Połącz**.

7. Aby wyświetlić wskaźniki zagrożeń zaimportowane do usługi Azure Sentinel, przejdź do **usługi Azure Sentinel — Logs** > **SecurityInsights**, a następnie rozwiń **pozycję ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Łączenie usługi Azure Sentinel z serwerami TAXII

## <a name="prerequisites"></a>Wymagania wstępne  

- Uprawnienia do odczytu i zapisu w obszarze roboczym usługi Azure Sentinel do przechowywania wskaźników zagrożeń.

- Identyfikator URI serwera TAXII 2.0 i identyfikator kolekcji.

## <a name="instructions"></a>Instrukcje

1. W witrynie Azure portal przejdź do łączników **usługi Azure Sentinel** > **Data,** a następnie wybierz łącznik **analizy zagrożeń — TAXII (wersja zapoznawcza).**

2. Wybierz **pozycję Otwórz stronę łącznika**.

3. Określ wymagane i opcjonalne informacje w polach tekstowych.

4. Wybierz **dodaj,** aby włączyć połączenie z serwerem TAXII 2.0.

5. Jeśli masz dodatkowe serwery TAXII 2.0: Powtórz kroki 3 i 4.

6. Aby wyświetlić wskaźniki zagrożeń zaimportowane do usługi Azure Sentinel, przejdź do **usługi Azure Sentinel — Logs** > **SecurityInsights**, a następnie rozwiń **pozycję ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak połączyć dostawcę analizy zagrożeń z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły.

- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).

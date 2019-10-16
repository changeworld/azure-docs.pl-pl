---
title: Łączenie danych analizy zagrożeń z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane analizy zagrożeń z wskaźnikiem kontrolnym platformy Azure.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 44b3830465bf2b5aa06612aa868b086b120f1ece
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372274"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Łączenie danych od dostawców analizy zagrożeń

> [!IMPORTANT]
> Łącznik danych platformy analizy zagrożeń w usłudze Azure wskaźnikowej jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wskaźnik "platformy Azure" umożliwia importowanie wskaźników zagrożeń, które są używane w organizacji, co może pomóc analitykom zabezpieczeń w wykrywaniu i ustalaniu priorytetów znanych zagrożeń. Niektóre funkcje ze wskaźnikiem na platformie Azure są dostępne lub udoskonalone:

- **Analiza** obejmuje zestaw szablonów zaplanowanych reguł, które można włączyć, aby generować alerty i zdarzenia oparte na dopasowaniach zdarzeń dziennika ze wskaźników zagrożeń.

- **Skoroszyty** zawierają podsumowane informacje o wskaźnikach zagrożeń zaimportowanych do platformy Azure, a także wszystkie alerty wygenerowane na podstawie reguł analizy, które pasują do wskaźników zagrożeń.

- Zapytania **polowania** umożliwiają badaczom zabezpieczeń korzystanie z wskaźników zagrożeń w kontekście typowych scenariuszy polowania.

- **Notesy** mogą używać wskaźników zagrożeń podczas badania anomalii i wyszukiwania złośliwych zachowań.

Za pomocą jednego z produktów zintegrowanej platformy do analizy zagrożeń (TIP), które znajdują się w następnej sekcji lub przy użyciu bezpośredniej integracji [Microsoft Graph z interfejsem API TiIndicators zabezpieczeń](https://aka.ms/graphsecuritytiindicators), można przesyłać wskaźniki zagrożeń do wskaźnika wskaźnikowego platformy Azure.

## <a name="integrated-threat-intelligence-platform-products"></a>Zintegrowane produkty platformy do analizy zagrożeń

- [MISP platforma analizy zagrożeń Open Source](https://www.misp-project.org/)
    
    Przykładowy skrypt, który zapewnia klientom z wystąpieniami MISP Migrowanie wskaźników zagrożeń do interfejsu API zabezpieczeń Microsoft Graph, można znaleźć w artykule [MISP do Microsoft Graph skrypt zabezpieczeń](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Aby uzyskać instrukcje z przewodnikiem, zobacz [wysyłanie IOCs do interfejsu API zabezpieczeń Microsoft Graph przy użyciu MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Platforma ThreatConnect](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Wymagania wstępne  

- Rola usługi Azure AD administratora globalnego lub administratora zabezpieczeń umożliwiająca Przyznawanie uprawnień do produktu TIP lub aplikacji niestandardowej korzystającej Microsoft Graph z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń.

- Uprawnienia do odczytu i zapisu do obszaru roboczego wskaźnikowego platformy Azure w celu przechowywania wskaźników zagrożeń.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Łączenie wskaźnikowego platformy Azure z dostawcą analizy zagrożeń

1. [Zarejestruj aplikację](/graph/auth-v2-service#1-register-your-app) w Azure Active Directory, aby uzyskać identyfikator aplikacji, klucz tajny aplikacji i identyfikator dzierżawy Azure Active Directory. Te wartości są potrzebne podczas konfigurowania zintegrowanego produktu lub aplikacji TIP, która korzysta Microsoft Graph z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń.

2. [Skonfiguruj uprawnienia interfejsu API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) dla zarejestrowanej aplikacji: dodaj uprawnienie Microsoft Graph aplikacji **ThreatIndicators. ReadWrite. OwnedBy** do zarejestrowanej aplikacji.

3. Skontaktuj się z administratorem dzierżawy Azure Active Directory, aby przyznać administratorowi zgodę na zarejestrowana aplikacja w organizacji. W Azure Portal: **Azure Active Directory** > **rejestracje aplikacji** >  **\<_Nazwa aplikacji_>**  > **Wyświetl uprawnienia interfejsu API**0**Udziel zgody administratora na 2_Nazwa dzierżawy_ 4**.

4. Skonfiguruj produkt lub aplikację TIP, która korzysta Microsoft Graph z bezpośredniej integracji z interfejsem API tiIndicators zabezpieczeń w celu wysyłania wskaźników do wskaźnika platformy Azure, określając następujące kwestie:
    
    a. Wartości identyfikatora, wpisu tajnego i identyfikatora dzierżawy zarejestrowanej aplikacji.
    
    b. Dla produktu docelowego określ platformę Azure.
    
    d. Dla akcji Określ alert.

5. W Azure Portal przejdź do łącznika**danych** **Azure — wskaźnik**@no__t — 1, a następnie wybierz łącznik **platformy analizy zagrożeń (wersja zapoznawcza)** .

6. Wybierz **Otwórz stronę łącznika**, a następnie **Połącz się**.

7. Aby wyświetlić wskaźniki zagrożeń, które zostały zaimportowane do wskaźnika wskaźnikowego platformy Azure, przejdź do okna **wskaźnikowego platformy Azure — dzienniki** > **SecurityInsights**, a następnie rozwiń węzeł **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia dostawcy analizy zagrożeń z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły.

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

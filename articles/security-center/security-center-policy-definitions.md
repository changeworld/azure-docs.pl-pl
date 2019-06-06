---
title: Usługa Azure policy definicje monitorowane w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Definicje zasad platformy Azure, monitorowany w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/19/2019
ms.author: monhaber
ms.openlocfilehash: 25ed9cb624474d5da56d385f4e9c155918ec8eab
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428338"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Zasady zabezpieczeń platformy Azure, monitorowany przez usługę Security Center
Ten artykuł zawiera listę definicji zasad platformy Azure, które można monitorować w usłudze Azure Security Center. Aby uzyskać więcej informacji na temat zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Dostępne zasady zabezpieczeń

Aby uzyskać informacje dotyczące wbudowanych zasad, które są monitorowane przez usługę Security Center, zobacz poniższej tabeli:

| Zasady | Zadania zasad |
| --- | --- |
|Dzienniki diagnostyczne w zestawach skalowania maszyn wirtualnych powinna być włączona.|Firma Microsoft zaleca, Włącz dzienniki, aby dziennik aktywności jest dostępny dla badania po zdarzenie bądź naruszenia zabezpieczeń.|
|Wszystkie reguły autoryzacji, z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw Centrum zdarzeń|Usługa Azure Event Hubs klientów nie należy używać zasad dostępu na poziomie przestrzeni nazw, który zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby zorganizować projekt z modelem uprawnień zabezpieczeń, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Reguły autoryzacji na jednostce Centrum zdarzeń powinien być zdefiniowany.|Przeprowadzaj inspekcję na obecność reguły autoryzacji na jednostkach usługi Event Hubs, aby udzielić uprawnień dostępu.|
|Dostęp do konta magazynu z zaporą i konfiguracji sieci wirtualnej powinny być ograniczone|Przeprowadź inspekcję dostępu do sieci bez ograniczeń w ustawieniach zapory konta magazynu. Konfiguruj reguły sieciowych tak, aby tylko aplikacje z dozwolonych sieci mają dostęp do konta magazynu. Aby zezwolić na połączenia z określonym internet lub na klientach lokalnych, należy udzielić dostępu do ruchu z określonych sieciach wirtualnych platformy Azure lub do publicznej sieci internet, adres IP, zakresy adresów.|
|Inspekcja użycia niestandardowych reguł RBAC|Inspekcja wbudowanych ról, takich jak "Właściciel, współautor, Czytelnik" zamiast ról kontroli dostępu opartej na rolach niestandardowych, które są podatne. Używanie ról niestandardowych jest traktowany jako wyjątek i wymaga przeglądu rygorystyczne i modelowanie zagrożeń.|
|Dzienniki diagnostyczne w usłudze Azure Stream Analytics powinno być włączone.|Przeprowadź inspekcję Włączanie dzienników i utrzymywania ich dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Bezpieczne przesyłanie danych do konta magazynu powinna być włączona.|Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza na koncie magazynu w celu umożliwienia akceptowania żądań tylko z bezpiecznego połączenia (HTTPS). Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługę. Chroni także przesyłane dane przed atakami w warstwie sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
|Administrator usługi Azure AD dla programu SQL server powinny zostać aprowizowane.|Przeprowadź inspekcję aprowizacji administrator usługi Azure Active Directory (Azure AD) dla programu SQL Server włączyć uwierzytelnianie usługi Azure AD. Uwierzytelnianie usługi Azure AD obsługuje zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft.|
|Wszystkie reguły autoryzacji, z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw usługi Service Bus|Klienci usługi Service Bus platformy Azure, nie należy używać zasad dostępu na poziomie przestrzeni nazw, który zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby zorganizować projekt z modelem uprawnień zabezpieczeń, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Dzienniki diagnostyczne w usłudze Service Bus powinien być włączony|Przeprowadź inspekcję włączenie dzienników i przechowywać dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Należy ustawić właściwość ClusterProtectionLevel EncryptAndSign w usłudze Service Fabric|Usługa Service Fabric udostępnia trzy poziomy ochrony dla komunikacji między węzłami, korzystającej z certyfikatu klastra podstawowego: Brak, logowania i EncryptAndSign. Ustaw poziom ochrony, aby zapewnić zaszyfrowany i podpisany cyfrowo wszystkie komunikaty węzła do węzła.|
|Uwierzytelnianie klienta, należy użyć usługi Azure Active Directory|Inspekcja użycia uwierzytelniania klienta, tylko za pomocą usługi Azure AD w usłudze Service Fabric.|
|Dzienniki diagnostyczne, w polu wyszukiwania, które powinno być włączone usługi|Przeprowadź inspekcję Włączanie dzienników i utrzymywania ich dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Powinien być włączony tylko bezpiecznych połączeń z usługą Redis Cache|Inspekcja, włączenie tylko połączenia za pośrednictwem protokołu SSL do usługi Azure Cache dla pamięci podręcznej Redis. Użyj bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługę. Chroni także przesyłane dane przed atakami w warstwie sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
|Dzienniki diagnostyczne usługi Logic Apps powinno być włączone.|Przeprowadź inspekcję Włączanie dzienników i utrzymywania ich dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Dzienniki diagnostyczne w usłudze Key Vault powinno być włączone.|Przeprowadź inspekcję Włączanie dzienników i utrzymywania ich dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Dzienniki diagnostyczne w Centrum zdarzeń powinien być włączony|Przeprowadź inspekcję Włączanie dzienników i utrzymywania ich dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Dzienniki diagnostyczne w usłudze Azure Data Lake Store powinno być włączone.|Przeprowadź inspekcję włączenie dzienników i utrzymywania roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Dzienniki diagnostyczne w usłudze Data Lake Analytics powinno być włączone.|Przeprowadź inspekcję Włączanie dzienników i utrzymywania ich dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Konta magazynu powinny być migrowane do nowych zasobów AzureRM|Użyj usługi Azure Resource Manager dla konta magazynu, aby dostarczyć wzmocnienia zabezpieczeń. Należą do nich: <br>-Silniejsze kontroli dostępu (RBAC)<br>-Lepiej inspekcji<br>— Wdrażanie opartego na usłudze Resource Manager platforma azure i zarządzanie<br>– Dostęp do zarządzanych tożsamości<br>– Dostęp do usługi Azure Key Vault dla wpisów tajnych<br>— Platforma azure uwierzytelniania w usłudze AD<br>— Obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami|
|Maszyny wirtualne powinny być migrowane do nowych zasobów AzureRM|Użyj usługi Azure Resource Manager dla maszyn wirtualnych, aby dostarczyć wzmocnienia zabezpieczeń.  Należą do nich: <br>-Silniejsze kontroli dostępu (RBAC)<br>-Lepiej inspekcji<br>— Wdrażanie opartego na usłudze Resource Manager platforma azure i zarządzanie<br>– Dostęp do zarządzanych tożsamości<br>– Dostęp do usługi Azure Key Vault dla wpisów tajnych<br>— Platforma azure uwierzytelniania w usłudze AD<br>— Obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami|
|Reguły alertów metryk, które należy skonfigurować na kontach usługi Batch|Konfiguracja inspekcji reguł alertów dotyczących metryk na kontach usługi Azure Batch, aby włączyć wymagane metryki.|
|Dzienniki diagnostyczne na kontach usługi Batch powinno być włączone.|Przeprowadź inspekcję Włączanie dzienników i utrzymywania ich dla roku. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Powinno być włączone szyfrowanie zmienne konta usługi Automation|Należy włączyć szyfrowanie zmiennych elementów zawartości konta usługi Azure Automation, jeśli przechowujesz ważne dane.|
|Dzienniki diagnostyki w usługach App powinno być włączone.|Inspekcja, Włączanie dzienników diagnostycznych w aplikacji. Spowoduje to utworzenie dzienników aktywności do badania, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Transparent Data Encryption w bazach danych SQL powinno być włączone.|Przeprowadź inspekcję stanu przezroczystego szyfrowania danych dla baz danych SQL.|
|Inspekcja programu SQL server, powinno być włączone|Przeprowadź inspekcję istnienia inspekcji SQL na poziomie serwera.|
|\[Wersja zapoznawcza]: Monitoruj nieszyfrowaną bazę danych SQL w usłudze Azure Security Center|Usługa Azure Security Center monitoruje nieszyfrowane serwery SQL lub bazy danych, zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Monitorowanie bazy danych SQL bez inspekcji w usłudze Azure Security Center|Usługa Azure Security Center monitoruje serwery SQL i baz danych, które nie mają włączonej zgodnie z zaleceniami funkcji inspekcji SQL.|
|\[Wersja zapoznawcza]: Aktualizacje systemu powinien być zainstalowany na komputerach|Usługa Azure Security Center monitoruje brakujące aktualizacje systemu zabezpieczeń na serwerach, zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Przeprowadź inspekcję brakującego szyfrowania elementu blob dla kont magazynu|Przeprowadzaj inspekcję kont magazynu, które nie są używane szyfrowanie obiektów blob. Dotyczy to tylko typów zasobów magazynu firmy Microsoft, a nie magazynu od innych dostawców. Usługa Azure Security Center monitoruje dostępu just in time ewentualny zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Kontrola dostępu do sieci Just-In-Time powinny być stosowane w przypadku maszyn wirtualnych|Usługa Azure Security Center monitoruje dostępu just in time ewentualny zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Adaptacyjne kontrole aplikacji powinno być włączone na maszynach wirtualnych|Usługa Azure Security Center monitoruje konfiguracji listy dozwolonych aplikacji to możliwe.|
|\[Wersja zapoznawcza]: Brakujące sieciowe grupy zabezpieczeń dla maszyn wirtualnych powinny być skonfigurowane|Usługi Azure Security Center monitoruje sieciowych grup zabezpieczeń, które mają zbyt liberalnych regułach zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Powinny zostać skorygowane luk w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach|Usługa Azure Security Center monitoruje serwery, które nie spełniają skonfigurowanego punktu odniesienia, zgodnie z zaleceniami.| 
|\[Wersja zapoznawcza]: Program Endpoint protection powinien być zainstalowany na maszynach wirtualnych|Usługa Azure Security Center monitoruje serwery, które nie mają zainstalowanego agenta programu Microsoft System Center Endpoint Protection, zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Szyfrowanie dysków powinny być stosowane w przypadku maszyn wirtualnych|Usługa Azure Security Center monitoruje maszyn wirtualnych, które nie mają włączone zgodnie z zaleceniami szyfrowanie dysku.|
|\[Wersja zapoznawcza]: Luki w zabezpieczeniach powinna zostać skorygowane przez rozwiązanie do oceny luk w zabezpieczeniach|Monitorowanie luk w zabezpieczeniach, które są wykrywane przez rozwiązanie do oceny luk w zabezpieczeniach i maszyny wirtualne, które nie mają rozwiązania do oceny luk w zabezpieczeniach w usłudze Azure Security Center, zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Monitoruj niechronione aplikacje internetowe w usłudze Azure Security Center|Usługa Azure Security Center monitoruje sieci web aplikacji, których brakuje ochrony przez zaporę aplikacji sieci web, zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Rozwiązanie do ochrony punktu końcowego, należy zainstalować na maszynach wirtualnych|Usługa Azure Security Center monitoruje sieci punktów końcowych, które nie mają ochrony przez zaporę następnej generacji, zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Powinny zostać skorygowane luk w zabezpieczeniach w bazach danych SQL|Ocena luk w zabezpieczeniach monitorowania wyników skanowania i zaleca się jak korygowanie luk w zabezpieczeniach bazy danych.|
|\[Wersja zapoznawcza]: Maksymalnie 3 właścicieli należy wyznaczyć dla Twojej subskrypcji|Firma Microsoft zaleca wyznaczonych do trzech właścicieli subskrypcji, aby zmniejszyć ryzyko naruszenia zabezpieczeń przez właściciela z naruszonymi zabezpieczeniami.|
|\[Wersja zapoznawcza]: Powinna istnieć więcej niż jeden właściciel został przypisany do Twojej subskrypcji|Zaleca się, że wyznaczenie więcej niż jednego właściciela subskrypcji, aby zapewnić dostęp administratora nadmiarowości.|
|\[Wersja zapoznawcza]: Uwierzytelnianie wieloskładnikowe powinno być włączone dla kont z uprawnieniami właściciela subskrypcji |Uwierzytelnianie wieloskładnikowe (MFA) powinna włączona dla wszystkich kont subskrypcji, które mają uprawnienia właściciela do zapobiegania naruszeniom zabezpieczeń kont lub zasobów.|
|\[Wersja zapoznawcza]: Uwierzytelnianie wieloskładnikowe powinna być włączona dla konta subskrypcji z uprawnieniami do zapisu|Usługę uwierzytelnianie wieloskładnikowe, należy włączyć dla wszystkich kont subskrypcji, które mają uprawnienia do zapisu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.|
|\[Wersja zapoznawcza]: Uwierzytelnianie wieloskładnikowe powinna być włączona dla konta subskrypcji z uprawnieniami do odczytu|Usługę uwierzytelnianie wieloskładnikowe, należy włączyć dla wszystkich kont subskrypcji, które ma uprawnienia odczytu do zapobiegania naruszeniom zabezpieczeń kont lub zasobów.|
|\[Wersja zapoznawcza]: Przestarzałe konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji|Przestarzałe konta, które mają uprawnienia właściciela powinny być usunięte z subskrypcji. Przestarzałe konta zostały zablokowane z logowania.|
|\[Wersja zapoznawcza]: Przestarzałe konta powinny zostać usunięte z subskrypcji|Przestarzałe konta powinny zostać usunięte z subskrypcji. Przestarzałe konta zostały zablokowane z logowania.|
|\[Wersja zapoznawcza]: Zewnętrzne konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji|Zewnętrzne konta, które mają uprawnienia właściciela powinny zostać usunięte z subskrypcji, aby uniemożliwić dostęp do uprawnień.|
|\[Wersja zapoznawcza]: Zewnętrzne konta z zapisu czy uprawnienia powinny zostać usunięte z subskrypcji|Konta zewnętrzne, które mają zapisu uprawnienia powinny zostać usunięte z subskrypcji, aby uniknąć niemonitorowanego dostępu.|
|\[Wersja zapoznawcza]: Zewnętrzne konta z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Zewnętrzne konta, które ma uprawnienia do odczytu powinny zostać usunięte ze swojej subskrypcji, aby uniknąć niemonitorowanego dostępu.|




## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej o usłudze Security Center, zobacz następujące artykuły.

* [Przewodnik planowania usługi Azure Security Center i obsługi](security-center-planning-and-operations-guide.md): Informacje o sposobie planowania i zagadnień projektowych w usłudze Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Zarządzanie i odpowiadanie na alerty zabezpieczeń w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [co to jest usługa Azure Policy?](../governance/policy/overview.md).

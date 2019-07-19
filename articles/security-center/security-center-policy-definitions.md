---
title: Definicje zasad platformy Azure monitorowane w Azure Security Center | Microsoft Docs
description: Definicje zasad platformy Azure monitorowane w Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: db7811a925846337487801a63e0f0c4584179c06
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295525"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Zasady zabezpieczeń platformy Azure monitorowane przez Security Center
Ten artykuł zawiera listę definicji Azure Policy, które można monitorować w programie Azure Security Center. Aby uzyskać więcej informacji na temat zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

## <a name="available-security-policies"></a>Dostępne zasady zabezpieczeń

Aby dowiedzieć się więcej na temat wbudowanych zasad monitorowanych przez Security Center, zobacz następującą tabelę:

| Zasady | Zadania zasad |
| --- | --- |
|Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone|Zalecamy włączenie dzienników, aby można było przeanalizować dziennik aktywności po zdarzeniu lub naruszeniu problemu.|
|Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń|Klienci usługi Azure Event Hubs nie powinni używać zasad dostępu na poziomie przestrzeni nazw, które zapewniają dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Należy zdefiniować reguły autoryzacji w jednostce centrum zdarzeń|Przeprowadź inspekcję istnienia reguł autoryzacji w jednostkach Event Hubs, aby przyznać dostęp z najniższymi uprawnieniami.|
|Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej należy ograniczyć|Inspekcja nieograniczonego dostępu do sieci w ustawieniach zapory konta magazynu. Skonfiguruj reguły sieciowe, tak aby tylko aplikacje z dozwolonych sieci mogły uzyskiwać dostęp do konta magazynu. Aby zezwolić na połączenia z określonych klientów internetowych lub lokalnych, Udziel dostępu do ruchu z określonych sieci wirtualnych platformy Azure lub do publicznych zakresów adresów IP.|
|Inspekcja użycia niestandardowych reguł RBAC|Przeprowadź inspekcję wbudowanych ról, takich jak "Owner, współautor, Reader" zamiast niestandardowych ról kontroli dostępu opartej na rolach (RBAC), które są podatne na błędy. Korzystanie z ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń.|
|Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich przez maksymalnie rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Należy włączyć bezpieczny transfer do kont magazynu|Przeprowadź inspekcję wymagań bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą. Chroni również dane przesyłane z ataków warstwy sieciowej, takich jak Man-in-the-Middle, podsłuchiwanie i przejmowanie sesji.|
|Należy zainicjować obsługę administracyjną administratora usługi Azure AD dla programu SQL Server|Inspekcja aprowizacji Azure Active Directory administratora usługi Azure AD w celu SQL Server w celu włączenia uwierzytelniania usługi Azure AD. Uwierzytelnianie usługi Azure AD obsługuje uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft.|
|Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw Service Bus|Klienci Azure Service Bus nie powinni używać zasad dostępu na poziomie przestrzeni nazw, które zapewniają dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, Utwórz zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Dzienniki diagnostyczne w Service Bus powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich przez rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Właściwość ClusterProtectionLevel do EncryptAndSign w Service Fabric powinna być ustawiona|Service Fabric zapewnia trzy poziomy ochrony komunikacji między węzłami, która używa podstawowego certyfikatu klastra: None, Sign i EncryptAndSign. Ustaw poziom ochrony, aby zapewnić zaszyfrowany i podpisany cyfrowo wszystkie komunikaty węzła do węzła.|
|Uwierzytelnianie klienta powinno być używane Azure Active Directory|Inspekcja użycia uwierzytelniania klienta tylko za pośrednictwem usługi Azure AD w Service Fabric.|
|Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich przez maksymalnie rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Należy włączyć tylko bezpieczne połączenia z Redis Cache|Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą. Chroni również dane przesyłane z ataków warstwy sieciowej, takich jak Man-in-the-Middle, podsłuchiwanie i przejmowanie sesji.|
|Dzienniki diagnostyczne w Logic Apps powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich przez maksymalnie rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Dzienniki diagnostyczne w Key Vault powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich przez maksymalnie rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich przez maksymalnie rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Dzienniki diagnostyczne w Azure Data Lake Store powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich na rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone|Inspekcja włączenia dzienników i utrzymywanie ich przez maksymalnie rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Konta magazynu należy migrować do nowych zasobów AzureRM|Aby zapewnić ulepszenia zabezpieczeń, użyj Azure Resource Manager dla kont magazynu. Należą do nich: <br>-Silniejsza kontrola dostępu (RBAC)<br>— Lepsza Inspekcja<br>Wdrożenie i zarządzanie oparte na Azure Resource Managerach<br>— Dostęp do tożsamości zarządzanych<br>— Dostęp do Azure Key Vault dla wpisów tajnych<br>— Uwierzytelnianie oparte na usłudze Azure AD<br>— Obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami|
|Maszyny wirtualne powinny być migrowane do nowych zasobów AzureRM|Użyj Azure Resource Manager na potrzeby maszyn wirtualnych, aby zapewnić ulepszenia zabezpieczeń.  Należą do nich: <br>-Silniejsza kontrola dostępu (RBAC)<br>— Lepsza Inspekcja<br>Wdrożenie i zarządzanie oparte na Azure Resource Managerach<br>— Dostęp do tożsamości zarządzanych<br>— Dostęp do Azure Key Vault dla wpisów tajnych<br>— Uwierzytelnianie oparte na usłudze Azure AD<br>— Obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami|
|Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych|Inspekcja konfiguracji reguł alertów metryk na kontach Azure Batch, aby włączyć wymaganą metrykę.|
|Należy włączyć dzienniki diagnostyczne na kontach wsadowych|Inspekcja włączenia dzienników i utrzymywanie ich przez maksymalnie rok. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Szyfrowanie powinno być włączone dla zmiennych konta usługi Automation|Ważne jest włączenie szyfrowania zasobów zmiennych konta Azure Automation, gdy przechowywane są poufne dane.|
|Dzienniki diagnostyczne w App Services powinny być włączone|Inspekcja włączenia dzienników diagnostycznych w aplikacji. Spowoduje to utworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Należy włączyć Transparent Data Encryption baz danych SQL|Inspekcja stanu przezroczystego szyfrowania danych dla baz danych SQL.|
|Inspekcja programu SQL Server powinna być włączona|Inspekcja istnienia inspekcji SQL na poziomie serwera.|
|\[Wersja zapoznawcza]: Monitorowanie nieszyfrowanej bazy danych SQL w Azure Security Center|Azure Security Center monitoruje nieszyfrowane serwery lub bazy danych SQL zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Monitoruj niepoddaną inspekcję bazy danych SQL w Azure Security Center|Azure Security Center monitoruje serwery SQL i bazy danych, dla których nie włączono inspekcji SQL zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Aktualizacje systemu powinny być zainstalowane na maszynach|Azure Security Center monitoruje brakujące aktualizacje systemu zabezpieczeń na serwerach zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Inspekcja braku szyfrowania obiektów BLOB dla kont magazynu|Przeprowadź inspekcję kont magazynu, które nie używają szyfrowania obiektów BLOB. Dotyczy to tylko typów zasobów magazynu firmy Microsoft, a nie magazynu pochodzących od innych dostawców. Azure Security Center monitoruje możliwy dostęp just in Time do sieci zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Kontrola dostępu do sieci just in Time powinna być stosowana na maszynach wirtualnych|Azure Security Center monitoruje możliwy dostęp just in Time do sieci zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji|Azure Security Center monitoruje możliwą konfigurację dozwolonych aplikacji.|
|\[Wersja zapoznawcza]: Należy skonfigurować brakujące sieciowe grupy zabezpieczeń dla maszyn wirtualnych|Azure Security Center monitoruje sieciowe grupy zabezpieczeń, które mają niezależne reguły zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować|Azure Security Center monitoruje serwery, które nie spełniają skonfigurowanej linii bazowej zgodnie z zaleceniami.| 
|\[Wersja zapoznawcza]: Program Endpoint Protection powinien być zainstalowany na maszynach wirtualnych|Azure Security Center monitoruje serwery, na których nie zainstalowano programu Microsoft System Center Endpoint Protection Agent zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Azure Security Center monitoruje maszyny wirtualne, na których nie włączono szyfrowania dysków zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach|Monitoruj luki w zabezpieczeniach wykryte przez rozwiązanie do oceny luk w zabezpieczeniach i maszyny wirtualne, które nie mają rozwiązania do oceny luk w zabezpieczeniach w Azure Security Center zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Monitoruj niechronione aplikacje sieci Web w Azure Security Center|Azure Security Center monitoruje aplikacje sieci Web, które nie mają ochrony zapory aplikacji sieci Web zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Rozwiązanie Endpoint Protection powinno być zainstalowane na maszynach wirtualnych|Azure Security Center monitoruje punkty końcowe sieci, które nie mają ochrony zapory nowej generacji zgodnie z zaleceniami.|
|\[Wersja zapoznawcza]: Luki w zabezpieczeniach baz danych SQL należy skorygować|Monitoruj wyniki skanowania oceny luk w zabezpieczeniach i zalecamy sposób korygowania luk w zabezpieczeniach bazy danych.|
|\[Wersja zapoznawcza]: Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli|Zalecamy wyznaczenie do trzech właścicieli subskrypcji, aby zmniejszyć prawdopodobieństwo naruszenia przez zagrożonego właściciela.|
|\[Wersja zapoznawcza]: Do subskrypcji powinien być przypisany więcej niż jeden właściciel|Zalecamy wyznaczanie więcej niż jednego właściciela subskrypcji, aby zapewnić nadmiarowość dostępu administratora.|
|\[Wersja zapoznawcza]: Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji |Uwierzytelnianie wieloskładnikowe (MFA) powinno być włączone dla wszystkich kont subskrypcji mających uprawnienia właściciela, aby zapobiec naruszeniu kont lub zasobów.|
|\[Wersja zapoznawcza]: Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach subskrypcji z uprawnieniami do zapisu|Uwierzytelnianie wieloskładnikowe powinno być włączone dla wszystkich kont subskrypcji, które mają uprawnienia do zapisu, aby zapobiec naruszeniu kont lub zasobów.|
|\[Wersja zapoznawcza]: Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach subskrypcji z uprawnieniami do odczytu|Uwierzytelnianie wieloskładnikowe powinno być włączone dla wszystkich kont subskrypcji, które mają uprawnienia do odczytu, aby zapobiec naruszeniu kont lub zasobów.|
|\[Wersja zapoznawcza]: Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji. Konta przestarzałe zostały zablokowane przed zalogowaniem się.|
|\[Wersja zapoznawcza]: Przestarzałe konta powinny zostać usunięte z subskrypcji|Przestarzałe konta powinny zostać usunięte z subskrypcji. Konta przestarzałe zostały zablokowane przed zalogowaniem się.|
|\[Wersja zapoznawcza]: Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby uniemożliwić dostęp do uprawnień.|
|\[Wersja zapoznawcza]: Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi.|
|\[Wersja zapoznawcza]: Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi.|




## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące artykuły.

* [Azure Security Center Przewodnik planowania i](security-center-planning-and-operations-guide.md)obsługi: Dowiedz się, jak planować i zrozumieć zagadnienia dotyczące projektowania w Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitoruj rozwiązania partnerskie Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat Azure Policy, zobacz [co to jest Azure Policy?](../governance/policy/overview.md).

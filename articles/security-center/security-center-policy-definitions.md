---
title: Usługa Azure policy definicje monitorowane w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Definicje zasad platformy Azure, monitorowany w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: e76464e294a3378bf3a275ec4fe7ccbd87c09475
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011644"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Zasady zabezpieczeń platformy Azure, monitorowany przez usługę Azure Security Center
Ten artykuł zawiera listę definicji zasad platformy Azure, które mogą być monitorowane w usłudze Security Center.

## <a name="available-security-policies"></a>Dostępne zasady zabezpieczeń

Aby poznać wbudowanych zasad, które są monitorowane przez usługę Security Center, można znaleźć w poniższej tabeli:

| Zasady | Zadania zasad |
| --- | --- |
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usługach Service Fabric i Virtual Machine Scale Sets|Zaleca się włączenie dzienników, aby możliwe było odtworzenie historii aktywności, gdy konieczne będzie przeprowadzenie badań w sytuacji wystąpienia incydentu lub naruszenia bezpieczeństwa.|
|Przeprowadź inspekcję reguł autoryzacji w przestrzeniach nazw usługi Event Hubs|Klienci Centrum zdarzeń nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby wyrównać z najniższymi uprawnieniami model zabezpieczeń, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Przeprowadź inspekcję istnienia reguł autoryzacji w jednostkach usługi Event Hubs|Przeprowadzaj inspekcję na obecność reguł autoryzacji na jednostek Centrum zdarzeń w celu przyznania najmniej uprzywilejowanego dostępu.|
|Przeprowadź inspekcję nieograniczonego dostępu do kont magazynu|Przeprowadź inspekcję dostępu do sieci bez ograniczeń w ustawieniach zapory konta magazynu. Zamiast tego należy skonfigurować zasady sieci, dzięki czemu tylko aplikacje z dozwolonych sieci mogą uzyskać dostępu do konta magazynu. Aby zezwolić na połączenia z określonym Internet lub klientów lokalnych, można udzielić dostępu do ruchu z określonych sieciach wirtualnych platformy Azure lub zakresy publicznych adresów IP w Internecie.|
|Przeprowadź inspekcję użycia niestandardowych reguł kontroli RBAC|Inspekcja wbudowanych ról, takich jak "Właściciel, współautor, Czytelnik" zamiast niestandardowe role RBAC, które są podatne. Za pomocą ról niestandardowych jest traktowany jako wyjątek i wymaga przeglądu rygorystyczne i modelowanie zagrożeń.|
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze Azure Stream Analytics|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Przeprowadź inspekcję bezpiecznego transferu do kont magazynu|Inspekcja Wymaganie bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza na koncie magazynu w celu umożliwienia akceptowania żądań tylko z bezpiecznego połączenia (HTTPS). Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługi oraz chroni przesyłane dane przed atakami warstwy sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
|Przeprowadź inspekcję aprowizowania administratora usługi Azure Active Directory dla programu SQL Server|Inspekcja, inicjowanie obsługi administracyjnej administratora usługi Azure Active Directory dla programu SQL server w celu włączenia uwierzytelniania usługi Azure AD. Uwierzytelnianie usługi Azure AD umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft.|
|Przeprowadź inspekcję reguł autoryzacji w przestrzeniach nazw usługi Service Bus|Klienci usługi Service Bus nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby wyrównać z najniższymi uprawnieniami model zabezpieczeń, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze Service Bus|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Przeprowadź inspekcję ustawienia właściwości ClusterProtectionLevel na wartość EncryptAndSign w usłudze Service Fabric|Usługa Service Fabric udostępnia trzy poziomy ochrony (Brak, logowania i EncryptAndSign) do komunikacji między węzłami, przy użyciu certyfikatu klastra podstawowego. Ustaw poziom ochrony, aby zapewnić zaszyfrowany i podpisany cyfrowo wszystkie komunikaty węzła do węzła.| 
|Przeprowadź inspekcję użycia usługi Azure Active Directory na potrzeby uwierzytelnienia klientów w usłudze Service Fabric|Przeprowadź inspekcję użycia uwierzytelniania klientów tylko za pomocą usługi Azure Active Directory w usłudze Service Fabric| 
|Przeprowadź inspekcję włączania dzienników diagnostycznych usługi Search|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.| 
|Przeprowadź inspekcję włączenie tylko bezpiecznych połączeń do pamięci podręcznej Azure dla usługi Redis|Inspekcja, włączenie tylko połączenia za pośrednictwem protokołu SSL do usługi Azure Cache dla pamięci podręcznej Redis. Użyj bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługi oraz chroni przesyłane dane przed atakami warstwy sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji| 
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze Logic Apps|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.| 
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze Key Vault|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.|
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze Event Hubs|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Pozwala to odtworzenie ślady działania na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.| 
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze Azure Data Lake Store|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.| 
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze Data Lake Analytics|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.| 
|Przeprowadź inspekcję użycia klasycznych kont magazynu|Usługa Azure Resource Manager dla konta magazynu zapewnienie wzmocnienia zabezpieczeń, takich jak: silniejsze kontroli dostępu (RBAC), lepiej inspekcji, wdrożenia usługi Azure Resource Manager i nadzoru, dostęp do zarządzanych tożsamości i dostępu do magazynu kluczy dla kluczy tajnych , Uwierzytelnianie na podstawie usługi AD systemu azure i obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami.| 
|Przeprowadź inspekcję użycia klasycznych maszyn wirtualnych|Usługa Azure Resource Manager dla maszyn wirtualnych zapewnienie wzmocnienia zabezpieczeń, takich jak: silniejsze kontroli dostępu (RBAC), lepiej inspekcji, wdrożenia usługi Azure Resource Manager i nadzoru, dostęp do zarządzanych tożsamości i dostępu do magazynu kluczy dla kluczy tajnych , Uwierzytelnianie na podstawie usługi AD systemu azure i obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami.| 
|Przeprowadź inspekcję konfiguracji reguł alertów dotyczących metryk w ramach kont usługi Batch|Konfiguracja inspekcji reguł alertów dotyczących metryk na koncie usługi Batch, aby włączyć wymagane metryki.| 
|Przeprowadź inspekcję konfiguracji reguł alertów dotyczących metryk w ramach kont usługi Batch|Konfiguracja inspekcji reguł alertów dotyczących metryk na koncie usługi Batch, aby włączyć wymagane metryki.| 
|Przeprowadź inspekcję włączania dzienników diagnostycznych na kontach usługi Batch|Przeprowadź inspekcję włączenie dzienników i przechowywać je do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci.| 
|Przeprowadź inspekcję włączania szyfrowania zmiennych konta usługi Automation|Ważne jest, aby włączyć szyfrowanie zasobów zmiennych konta usługi Automation w przypadku przechowywania danych poufnych.| 
|Przeprowadź inspekcję włączania dzienników diagnostycznych w usłudze App Services|Inspekcja, Włączanie dzienników diagnostycznych w aplikacji. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, jeśli wystąpi incydent zabezpieczeń lub złamania zabezpieczeń sieci.| 
|Inspekcja stanu przezroczystego szyfrowania danych|Przeprowadź inspekcję stanu przezroczystego szyfrowania danych dla baz danych SQL.| 
|Przeprowadź inspekcję ustawień inspekcji SQL na poziomie serwera|Przeprowadza inspekcję istnienia inspekcji SQL na poziomie serwera.| 
|[Wersja zapoznawcza]: Monitoruj niezaszyfrowaną bazę danych SQL w usłudze Azure Security Center|Nieszyfrowane serwery SQL lub bazy danych będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj bazę danych SQL bez inspekcji w usłudze Azure Security Center|Serwery SQL i baz danych, które nie włączona jest funkcja inspekcji usługi SQL będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj brakujące aktualizacje systemu w usłudze Azure Security Center|Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Przeprowadzaj inspekcję szyfrowania brakujących obiektów blob dla kont magazynu|Ta zasada przeprowadza inspekcję kont magazynu bez szyfrowania obiektów blob. Dotyczy tylko typów zasobów Microsoft.Storage, a nie do innych dostawców magazynu. Ewentualny dostęp Just In Time, będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj ewentualny dostęp dokładnie na czas (JIT, Just In Time) w usłudze Azure Security Center|Ewentualny dostęp tylko w czas (JIT), będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj ewentualną listę dozwolonych aplikacji w usłudze Azure Security Center|Możliwych konfiguracji listy dozwolonych aplikacji będą monitorowane przez usługę Azure Security Center.| 
|[Wersja zapoznawcza]: Monitoruj liberalny dostęp do sieci w usłudze Azure Security Center|Sieciowe grupy zabezpieczeń o zbyt liberalnych regułach będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj luki w zabezpieczeniach systemu operacyjnego w usłudze Azure Security Center|Serwery, które nie spełniają skonfigurowanego punktu odniesienia, będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj brakujący program Endpoint Protection w usłudze Azure Security Center|Serwery bez zainstalowanego agenta programu Endpoint Protection będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj niezaszyfrowane dyski maszyn wirtualnych w usłudze Azure Security Center|Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj luki w zabezpieczeniach maszyn wirtualnych w usłudze Azure Security Center|Monitoruje luki w zabezpieczeniach wykryte przez rozwiązanie Vulnerability Assessment oraz maszyny wirtualne bez rozwiązania Vulnerability Assessment w usłudze Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj niechronioną aplikację internetową w usłudze Azure Security Center|Aplikacje sieci Web bez ochrony zapory aplikacji sieci Web będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj niechronione punkty końcowe sieci w usłudze Azure Security Center|Punkty końcowe sieci bez ochrony dalej zaporę nowej generacji będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|[Wersja zapoznawcza]: Monitoruj wyniki oceny luk w zabezpieczeniach SQL w usłudze Azure Security Center|Monitoruj wyniki skanowania przy ocenie luk w zabezpieczeniach oraz zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych.| 
|[Wersja zapoznawcza]: Inspekcja maksymalnej liczby właścicieli dla subskrypcji|Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji, aby zmniejszyć ryzyko ataku przez naruszenie zabezpieczeń właściciela.| 
|[Wersja zapoznawcza]: Inspekcja minimalnej liczby właścicieli dla subskrypcji|Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu posiadania nadmiarowości dostępu administratora.| 
|[Wersja zapoznawcza]: Inspekcja kont z uprawnieniami właściciela, które nie mają włączonego uwierzytelniania MFA w subskrypcji|Usługa Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.| 
|[Wersja zapoznawcza]: Inspekcja kont z uprawnieniami do zapisu, które nie mają włączonego uwierzytelniania MFA w subskrypcji|Usługa Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.| 
|[Wersja zapoznawcza]: Inspekcja kont z uprawnieniami do odczytu, które nie mają włączonego uwierzytelniania MFA w subskrypcji|Usługa Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.| 
|[Wersja zapoznawcza]: Inspekcja przestarzałych kont z uprawnieniami właściciela w subskrypcji|Przestarzałe konta z uprawnieniami właściciela, powinny być usunięte z subskrypcji. Przestarzałe konta są kontami, które zostały zablokowane z logowania.| 
|[Wersja zapoznawcza]: Inspekcja przestarzałych kont w subskrypcji|Przestarzałe konta powinny zostać usunięte z subskrypcji. Przestarzałe konta są kontami, które zostały zablokowane z logowania.| 
|[Wersja zapoznawcza]: Inspekcja kont zewnętrznych z uprawnieniami właściciela w subskrypcji|Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji, aby uniknąć niemonitorowanego dostępu.| 
|[Wersja zapoznawcza]: Inspekcja kont zewnętrznych z uprawnieniami do zapisu w subskrypcji|Zewnętrzne konta z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji, aby uniknąć niemonitorowanego dostępu.| 
|[Wersja zapoznawcza]: Inspekcja kont zewnętrznych z uprawnieniami do odczytu w subskrypcji|Zewnętrzne konta z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji, aby uniknąć niemonitorowanego dostępu.| 




## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md) — informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../azure-policy/azure-policy-introduction.md)

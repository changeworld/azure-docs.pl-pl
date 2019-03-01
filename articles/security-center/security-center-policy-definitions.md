---
title: Usługa Azure policy definicje monitorowane w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Definicje zasad platformy Azure, monitorowany w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 895afb7af8ed7100af237f88e2953f4dc0991acb
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195255"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Zasady zabezpieczeń platformy Azure, monitorowany przez usługę Azure Security Center
Ten artykuł zawiera listę definicji zasad platformy Azure, które mogą być monitorowane w usłudze Security Center. Aby uzyskać więcej informacji na temat zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

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
|\[Wersja zapoznawcza]: Monitoruj nieszyfrowaną bazę danych SQL w usłudze Azure Security Center|Nieszyfrowane serwery SQL lub bazy danych będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitorowanie bazy danych SQL bez inspekcji w usłudze Azure Security Center|Serwery SQL i baz danych, które nie włączona jest funkcja inspekcji usługi SQL będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitoruj brakujące aktualizacje systemu w usłudze Azure Security Center|Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Przeprowadź inspekcję brakującego szyfrowania elementu blob dla kont magazynu|Ta zasada przeprowadza inspekcję kont magazynu bez szyfrowania obiektów blob. Dotyczy tylko typów zasobów Microsoft.Storage, a nie do innych dostawców magazynu. Ewentualny dostęp Just In Time, będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitoruj ewentualny dostęp tylko w czas (JIT) w usłudze Azure Security Center|Ewentualny dostęp tylko w czas (JIT), będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitor możliwe listę dozwolonych aplikacji w usłudze Azure Security Center|Możliwych konfiguracji listy dozwolonych aplikacji będą monitorowane przez usługę Azure Security Center.| 
|\[Wersja zapoznawcza]: Monitoruj liberalny dostęp do sieci w Centrum zabezpieczeń Azure|Sieciowe grupy zabezpieczeń o zbyt liberalnych regułach będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitorowanie luk w zabezpieczeniach systemu operacyjnego, w usłudze Azure Security Center|Serwery, które nie spełniają skonfigurowanego punktu odniesienia, będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitorowanie brakującej ochrony punktów końcowych w usłudze Azure Security Center|Serwery bez zainstalowanego agenta programu Endpoint Protection będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitoruj nieszyfrowane dyski maszyn wirtualnych w usłudze Azure Security Center|Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitoruj luki w zabezpieczeniach maszyn wirtualnych w usłudze Azure Security Center|Monitoruje luki w zabezpieczeniach wykryte przez rozwiązanie Vulnerability Assessment oraz maszyny wirtualne bez rozwiązania Vulnerability Assessment w usłudze Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitoruj niechronione aplikacje internetowe w usłudze Azure Security Center|Aplikacje sieci Web bez ochrony zapory aplikacji sieci Web będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitoruj niechronione punkty końcowe sieci w Centrum zabezpieczeń Azure|Punkty końcowe sieci bez ochrony dalej zaporę nowej generacji będą monitorowane przez usługę Azure Security Center jako zalecenia.| 
|\[Wersja zapoznawcza]: Monitoruj wyniki oceny luk w zabezpieczeniach SQL w usłudze Azure Security Center|Monitoruj wyniki skanowania przy ocenie luk w zabezpieczeniach oraz zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych.| 
|\[Wersja zapoznawcza]: Inspekcja maksymalną liczbę właścicieli subskrypcji|Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji, aby zmniejszyć ryzyko ataku przez naruszenie zabezpieczeń właściciela.| 
|\[Wersja zapoznawcza]: Minimalna liczba właścicieli subskrypcji inspekcji|Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu posiadania nadmiarowości dostępu administratora.| 
|\[Wersja zapoznawcza]: Inspekcja kont z uprawnieniami właściciela, którzy nie są włączone w ramach subskrypcji usługi MFA|Usługa Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.| 
|\[Wersja zapoznawcza]: Inspekcja kont z uprawnieniami do zapisu, którzy nie są włączone w ramach subskrypcji usługi MFA|Usługa Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.| 
|\[Wersja zapoznawcza]: Inspekcja kont z uprawnieniami do odczytu, którzy nie są włączone w ramach subskrypcji usługi MFA|Usługa Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.| 
|\[Wersja zapoznawcza]: Inspekcja przestarzałe konta z uprawnieniami właściciela na subskrypcji|Przestarzałe konta z uprawnieniami właściciela, powinny być usunięte z subskrypcji. Przestarzałe konta są kontami, które zostały zablokowane z logowania.| 
|\[Wersja zapoznawcza]: Inspekcja przestarzałe konta w ramach subskrypcji|Przestarzałe konta powinny zostać usunięte z subskrypcji. Przestarzałe konta są kontami, które zostały zablokowane z logowania.| 
|\[Wersja zapoznawcza]: Przeprowadź inspekcję konta zewnętrzne z uprawnieniami właściciela na subskrypcji|Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji, aby uniknąć niemonitorowanego dostępu.| 
|\[Wersja zapoznawcza]: Inspekcja konta zewnętrzne z uprawnień do zapisu w ramach subskrypcji|Zewnętrzne konta z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji, aby uniknąć niemonitorowanego dostępu.| 
|\[Wersja zapoznawcza]: Przeprowadź inspekcję konta zewnętrzne z uprawnieniami do odczytu w ramach subskrypcji|Zewnętrzne konta z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji, aby uniknąć niemonitorowanego dostępu.| 




## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania usługi Azure Security Center i obsługi](security-center-planning-and-operations-guide.md): Informacje o sposobie planowania i zagadnień projektowych dotyczących usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Zarządzanie i odpowiadanie na alerty zabezpieczeń w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../governance/policy/overview.md)

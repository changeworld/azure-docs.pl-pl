---
title: Ulepszony bezpieczny wynik (wersja zapoznawcza) w Azure Security Center
description: Opis rozszerzonego bezpiecznego wyniku (wersja zapoznawcza) i kontroli zabezpieczeń w Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: memildin
ms.openlocfilehash: 8f0b7b1225d78c7626c5be3e68c3dcd2dd557aa3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76158158"
---
# <a name="the-enhanced-secure-score-preview"></a>Ulepszony bezpieczny wynik (wersja zapoznawcza) 

W tym artykule wprowadzono ulepszony bezpieczny wynik (obecnie w wersji zapoznawczej), towarzyszące mechanizmy kontroli zabezpieczeń oraz korzyści, jakie zapewnia.

## <a name="introduction-to-secure-score"></a>Wprowadzenie do zabezpieczenia oceny

Azure Security Center ma dwa główne cele: aby pomóc Ci zrozumieć bieżącą sytuację bezpieczeństwa i pomóc efektywnie i efektywnie ulepszyć zabezpieczenia. Centralny aspekt Security Center, który umożliwia osiągnięcie tych celów, jest bezpiecznym wynikiem.

Security Center stale ocenia zasoby, subskrypcje i organizację pod kątem problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w postaci pojedynczego wyniku, dzięki czemu można szybko powiedzieć, że aktualna sytuacja zabezpieczeń: wyższy wynik, tym niższy poziom ryzyka. Wynik służy do śledzenia wysiłków w zakresie zabezpieczeń i projektów w organizacji. 

*Ulepszony* bezpieczny wynik (obecnie w wersji zapoznawczej) umożliwia **skoncentrowanie** się na koncentracji i nawiąże trzy korzyści:

- Kontrola zabezpieczeń — zalecenia dotyczące zabezpieczeń są teraz pogrupowane w zestawy logiczne, które lepiej odzwierciedlają podatne na ataki. Aby uzyskać więcej informacji, zobacz [jak poniżej jest obliczany bezpieczny wynik](secure-score-security-controls.md#how-the-secure-score-is-calculated) .

- Ogólny wynik lepiej odzwierciedla ogólne stan na poziomie rekomendacji. Dzięki temu ulepszeniu Ocena poprawi się tylko wtedy, gdy koryguje *wszystkie* zalecenia dotyczące pojedynczego zasobu w formancie. Oznacza to, że wynik jest ulepszany tylko wtedy, gdy zwiększa się bezpieczeństwo zasobu. 

- Informacje o stanie zabezpieczeń poszczególnych stron ataku są bardziej widoczne — pokazując wynik na kontrolę zabezpieczeń, Strona zabezpieczonych punktacji stanie się miejscem, w którym można uzyskać szczegółowy widok, w jaki sposób organizacja chroni poszczególne osoby atakujące.

Ulepszony bezpieczny wynik jest pokazywany jako wartość procentowa, jak pokazano na poniższym zrzucie ekranu:

[![zwiększony bezpieczny wynik (wersja zapoznawcza) zawiera teraz wartość procentową](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)


## <a name="locating-your-secure-score"></a>Lokalizowanie bezpiecznego wyniku

Security Center wyświetla wynik w widocznym miejscu: jest to pierwsza czynność wyświetlana na stronie Przegląd. Jeśli klikniesz pozycję przez użytkownika na stronie dedykowany zabezpieczony wskaźnik, zobaczysz wynik podzielony przez subskrypcję. Click a single subscription to see the detailed list of prioritized recommendations and the potential impact that remediating them will have on the subscription’s score.

## <a name="how-the-secure-score-is-calculated"></a>How the Secure Score is calculated 

Before this preview, Security Center considered each recommendation individually and assigned a value to it based on its severity. Security teams working to improve their security posture had to prioritize responses to Security Center recommendations based on the full list of findings. Every time you remediated a recommendation for a single resource, your Secure Score improved.

As part of the enhancements to the Secure Score, recommendations are now grouped into **Security Controls**. A control is a set of security recommendations and the instructions that help you implement those recommendations. Controls are logical groupings of related recommendations. Points are no longer awarded at the recommendation level. Instead, your score will only improve when you remediate *all* of the recommendations for a single resource within a control.

The contribution of each Security Control towards the overall Secure Score is shown clearly on the recommendations page.

[![The enhanced Secure Score (preview) introduces Security Controls](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

To get all the possible points for a Security Control, all your resources must comply with all of the security recommendations within the Security Control. For example, Security Center has multiple recommendations regarding how to secure your management ports. In the past, you could remediate some of those related and interdependent recommendations while leaving others unsolved, and your Secure Score would improve. When looked at objectively, it's easy to argue that your security hadn't improved until you had resolved them all. Now, you must remediate them all to make a difference to your Secure Score.

For example, the Security Control called "Apply system updates" has a maximum score of six points, which you can see in the tooltip on the potential increase value of the control:

[![The Security Control "Apply system updates"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

The value for the Security Control "Apply system updates" in the screenshot above shows "2% (1 Point)". That means that if you remediate all the recommendations in this control, your score will increase by 2% (which in this case is one point). For simplicity, values in the recommendations list's "Potential increase" column are rounded to whole numbers. The tooltips show the precise values:

* **Potential increase** - The remaining points available to you within the control. To get these points  added to your Secure Score, remediate all of the control's recommendations. In the example above, the one point shown for the control is actually 0.96 points.
* **Current score** - The current score for this control. Each control contributes towards the total score. In this example, the control is contributing 5.04 points to the total. 
* **Max score** - The sum of the previous two values.

### <a name="calculations---understanding-your-score"></a>Calculations - understanding your score

|Metryka|Formula and example|
|-|-|
|**Security Control's current score**|<br>![Equation for calculating a Security Control's current score](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Each individual Security Control contributes towards the Security Score. Each resource affected by a recommendation within the control, contributes towards the control's current score. The current score for each control is a measure of the status of the resources *within* the control.<br>![Tooltips showing the values used when calculating the Security Control's current score](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In this example, the max score of 6 would be divided by 78 because that's the sum of the healthy and unhealthy resources.<br>6 / 78 = 0.0769<br>Multiplying that by the number of healthy resources (74) results in the current score:<br>0.0769 * 74 = **5.69**<br><br>|
|**Wskaźnik bezpieczeństwa**<br>Subskrypcja pojedyncza|<br>![Equation for calculating the current Secure Score](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Single subscription secure score with all controls enabled](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In this example, there is a single subscription with all Security Controls available (a potential maximum score of 60 points). The score shows 28 points out of a possible 60 and the remaining 32 points are reflected in the "Potential score increase" figures of the Security Controls.<br>![List of controls and the potential score increase](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Wskaźnik bezpieczeństwa**<br>Multiple subscriptions|<br>The current score for all resources across all subscriptions are added and the calculation is then the same as for a single subscription<br><br>Podczas przeglądania wielu subskrypcji, funkcja Secure Score szacuje wszystkie zasoby we wszystkich włączonych zasadach i grupuje ich łączny wpływ na maksymalny wynik kontroli zabezpieczeń.<br>![zabezpieczonych wyników dla wielu subskrypcji z włączonymi wszystkimi kontrolkami](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Połączony wynik **nie** jest średni; jest to raczej oceniane stan stanu wszystkich zasobów we wszystkich subskrypcjach.<br>Tutaj, jeśli przejdziesz do strony rekomendacje i dodasz dostępne punkty, zobaczysz, że jest to różnica między bieżącym wynikiem (24) i maksymalnym dostępnym wynikiem (60).|
||||

## <a name="improving-your-secure-score"></a>Ulepszanie bezpiecznego wyniku

Aby ulepszyć bezpieczny wynik, skoryguj zalecenia dotyczące zabezpieczeń z listy rekomendacji. Wszystkie zalecenia można skorygować ręcznie dla każdego zasobu lub przy użyciu **szybkiej poprawki** . Opcja (jeśli jest dostępna) w celu szybkiego zastosowania skorygowania zalecenia do grupy zasobów. Aby uzyskać więcej informacji, zobacz temat [korygowanie zaleceń](security-center-remediate-recommendations.md).

Tylko wbudowane zalecenia mają wpływ na bezpieczny wynik.

## <a name="security-controls-and-their-recommendations"></a>Kontrola zabezpieczeń i ich zalecenia

W poniższej tabeli wymieniono kontrolki zabezpieczeń w Azure Security Center. Dla każdej kontrolki można zobaczyć maksymalną liczbę punktów, które można dodać do swojego bezpiecznego wyniku, jeśli korygujesz *wszystkie* zalecenia wymienione w formancie dla *wszystkich* zasobów. 

> [!TIP]
> Jeśli chcesz odfiltrować lub posortować tę listę inaczej, skopiuj ją i wklej do programu Excel.

|Kontrola zabezpieczeń|Maksymalna liczba bezpiecznych punktów punktacji|Polecane elementy|
|----------------|:-------------------:|---------------|
|**Włączanie usługi MFA**|10|-Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji<br>-Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji<br>-MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji|
|**Bezpieczne porty zarządzania**|8|— Kontrola dostępu do sieci just in Time powinna być stosowana na maszynach wirtualnych<br>-Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>-Należy zamknąć porty zarządzania na maszynach wirtualnych|
|**Zastosuj aktualizacje systemu**|6|-Monitorowanie problemów z kondycją agenta na maszynach<br>-Należy zainstalować agenta monitorowania w zestawach skalowania maszyn wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Należy zaktualizować wersję systemu operacyjnego dla ról usługi w chmurze<br>-Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych<br>-Aktualizacje systemu powinny być zainstalowane na maszynach<br>-Należy ponownie uruchomić maszyny, aby zastosować aktualizacje systemu<br>-Kubernetes Services należy uaktualnić do wersji Kubernetes, która nie jest zagrożona<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych|
|**Korygowanie luk w zabezpieczeniach**|6|-Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL<br>-Luki w zabezpieczeniach Azure Container Registry obrazów należy skorygować (wersja zapoznawcza)<br>— Luki w zabezpieczeniach baz danych SQL należy skorygować<br>-Luki w zabezpieczeniach należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach<br>-Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL<br>-Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL<br>-Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych|
|**Włącz szyfrowanie w spoczynku**|4|-Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych<br>-Należy włączyć Transparent Data Encryption baz danych SQL<br>-Zmienne konta usługi Automation powinny być szyfrowane<br>-Service Fabric klastrów powinna mieć Właściwość ClusterProtectionLevel ustawioną na wartość EncryptAndSign<br>-Funkcja ochrony SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza|
|**Szyfruj dane podczas przesyłania**|4|— Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>-Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>-Należy włączyć tylko bezpieczne połączenia z Redis Cache<br>-Należy włączyć bezpieczny transfer do kont magazynu<br>-Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS|
|**Zarządzanie dostępem i uprawnieniami**|4|-Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli<br>-Przestarzałe konta powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>-Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>— Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>-Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji<br>— Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>-Do subskrypcji powinien być przypisany co najmniej jeden właściciel<br>-Oparta na rolach Access Control (RBAC) powinna być używana w usługach Kubernetes Services (wersja zapoznawcza)<br>— Klastry Service Fabric powinny używać tylko Azure Active Directory do uwierzytelniania klientów|
|**Korygowanie konfiguracji zabezpieczeń**|4|-Pod należy zdefiniować zasady zabezpieczeń w usługach Kubernetes Services (wersja zapoznawcza)<br>— Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować<br>-Luki w zabezpieczeniach na maszynach należy skorygować<br>— Luki w zabezpieczeniach w konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Należy zainstalować agenta monitorowania w zestawach skalowania maszyn wirtualnych<br>-Monitorowanie problemów z kondycją agenta na maszynach|
|**Ogranicz nieautoryzowany dostęp do sieci**|4|-Przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone<br>-Autoryzowane zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes Services (wersja zapoznawcza)<br>-Dostęp do App Services powinien być ograniczony (wersja zapoznawcza)<br>-Reguły dla aplikacji sieci Web na IaaS sieciowych grup zabezpieczeń powinny być zaostrzone<br>-Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>— Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API<br>— Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacja funkcji<br>— Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web<br>-Zdalne debugowanie powinno zostać wyłączone dla aplikacji interfejsu API<br>-Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji<br>-Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web<br>-Dostęp powinien być ograniczony dla dozwolonych sieciowych grup zabezpieczeń z maszynami wirtualnymi z Internetu<br>-Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu powinny być zaostrzone|
|**Zastosuj adaptacyjną kontrolę aplikacji**|3|-Adaptacyjne kontrolki aplikacji powinny być włączone na maszynach wirtualnych<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Monitorowanie problemów z kondycją agenta na maszynach|
|**Zastosuj klasyfikację danych**|2|Dane poufne w bazach danych SQL powinny zostać sklasyfikowane (wersja zapoznawcza)|
|**Ochrona aplikacji przed atakami DDoS**|2|-DDoS Protection powinien być włączony Standard|
|**Włącz program Endpoint Protection**|2|-Błędy kondycji programu Endpoint Protection należy skorygować w przypadku zestawów skalowania maszyn wirtualnych<br>-Na maszynach należy rozwiązać problemy dotyczące kondycji programu Endpoint Protection<br>-Należy zainstalować rozwiązanie Endpoint Protection w zestawach skalowania maszyn wirtualnych<br>-Zainstaluj rozwiązanie Endpoint Protection na maszynach wirtualnych<br>-Monitorowanie problemów z kondycją agenta na maszynach<br>-Należy zainstalować agenta monitorowania w zestawach skalowania maszyn wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>— Zainstaluj rozwiązanie Endpoint Protection na swoich maszynach|
|**Włącz inspekcję i rejestrowanie**|1|-Inspekcja w programie SQL Server powinna być włączona<br>-Należy włączyć dzienniki diagnostyczne w App Services<br>-Należy włączyć dzienniki diagnostyczne w Azure Data Lake Store<br>-Należy włączyć dzienniki diagnostyczne w Azure Stream Analytics<br>-Należy włączyć dzienniki diagnostyczne na kontach wsadowych<br>-Należy włączyć dzienniki diagnostyczne w Data Lake Analytics<br>-Należy włączyć dzienniki diagnostyczne w centrum zdarzeń<br>-Należy włączyć dzienniki diagnostyczne w IoT Hub<br>-Należy włączyć dzienniki diagnostyczne w Key Vault<br>-Należy włączyć dzienniki diagnostyczne w Logic Apps<br>-Należy włączyć dzienniki diagnostyczne w usłudze wyszukiwania<br>-Należy włączyć dzienniki diagnostyczne w Service Bus<br>-Należy włączyć dzienniki diagnostyczne w Virtual Machine Scale Sets<br>-Dla kont wsadowych należy skonfigurować reguły alertów metryk<br>-Ustawienia inspekcji SQL powinny mieć skonfigurowane grupy akcji do przechwytywania działań krytycznych<br>— Serwery SQL powinny być skonfigurowane z okresem przechowywania inspekcji większym niż 90 dni.|
|**Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń**|0|-Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej należy ograniczyć<br>-Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń<br>-Administrator Azure Active Directory powinien być zainicjowany dla serwerów SQL<br>-Należy zdefiniować reguły autoryzacji w wystąpieniu centrum zdarzeń<br>-Konta magazynu należy migrować do nowych zasobów Azure Resource Manager<br>-Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager<br>— Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń<br>-Zaawansowane zabezpieczenia danych powinny być włączone w zarządzanych wystąpieniach<br>-Wszystkie typy zaawansowanej ochrony przed zagrożeniami powinny być włączone w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL<br>— Powiadomienia e-mail dla administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych programu SQL Server<br>-Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server<br>-Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń<br>-Wszystkie typy zaawansowanej ochrony przed zagrożeniami powinny być włączone w ustawieniach zaawansowanych danych programu SQL Server|
||||

## <a name="secure-score-faq"></a>Bezpieczna ocena — często zadawane pytania

### <a name="why-has-my-secure-score-gone-down"></a>Dlaczego mój bezpieczny wskaźnik został usunięty?
Ze zmianami wprowadzonymi w tym ulepszonym zakresie zabezpieczeń należy rozwiązać wszystkie zalecenia dotyczące zasobu, aby otrzymywać punkty. Wyniki również zostały zmienione na skalę 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Czy w ramach kontroli zabezpieczeń są adresowane tylko trzy z czterech zaleceń, czy mój bezpieczny wynik zmieni się?
Znaleziono nie ulegnie zmianie, dopóki nie zostaną skorygowane wszystkie zalecenia dotyczące pojedynczego zasobu. Aby uzyskać maksymalny wynik kontrolki, należy skorygować wszystkie zalecenia dla wszystkich zasobów.

### <a name="will-this-enhanced-secure-score-replace-the-existing-secure-score"></a>Czy ten rozszerzony wynik będzie zastępować istniejący wynik? 
Tak, ale przez czas działa obok siebie, aby ułatwić przejście.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Jeśli zalecenie nie dotyczy mnie i nie zostało wyłączone w zasadach, czy moja kontrola zabezpieczeń zostanie zrealizowana, a my zabezpieczony?
Tak. Zalecamy wyłączenie zaleceń, gdy nie są one stosowane w danym środowisku. Aby uzyskać instrukcje dotyczące sposobu wyłączania określonego zalecenia, zobacz temat [wyłączanie zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Jeśli kontrola zabezpieczeń oferuje mi zero punktów na rzecz mojego bezpiecznego oceny, należy ją zignorować?
W niektórych przypadkach zobaczysz maksymalny wynik kontrolki większy od zera, ale wpływ wynosi zero. Gdy przyrostowy wynik naprawiania zasobów jest nieznaczny, jest on zaokrąglony do zera. Nie należy ignorować tych zaleceń, ponieważ nadal wprowadzają ulepszenia zabezpieczeń. Jedynym wyjątkiem jest "dodatkowa kontrola najlepszych rozwiązań". Korygowaniem te zalecenia nie spowodują zwiększenia wyniku, ale zwiększy swoje ogólne zabezpieczenia.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano ulepszony, bezpieczny wynik i nowe funkcje kontroli zabezpieczeń, które wprowadza. W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami:

- [Poznaj różne elementy zalecenia](security-center-recommendations.md)
- [Dowiedz się, jak skorygować zalecenia](security-center-remediate-recommendations.md)
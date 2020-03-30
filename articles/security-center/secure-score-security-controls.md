---
title: Bezpieczny wynik w usłudze Azure Security Center
description: Opis bezpiecznego wyniku usługi Azure Security Center i jego kontroli bezpieczeństwa
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
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415835"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Ulepszony bezpieczny wynik (wersja zapoznawcza) w usłudze Azure Security Center

## <a name="introduction-to-secure-score"></a>Wprowadzenie do bezpiecznego wyniku

Usługa Azure Security Center ma dwa główne cele: aby pomóc Ci zrozumieć bieżącą sytuację zabezpieczeń i pomóc w wydajnym i skutecznie poprawić bezpieczeństwo. Centralnym aspektem Centrum zabezpieczeń, który umożliwia osiągnięcie tych celów, jest bezpieczny wynik.

Usługa Security Center stale ocenia zasoby, subskrypcje i organizację pod kątem problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w jeden wynik, dzięki czemu można powiedzieć, na pierwszy rzut oka, bieżącej sytuacji bezpieczeństwa: im wyższy wynik, tym niższy zidentyfikowany poziom ryzyka. Użyj wyniku do śledzenia wysiłków i projektów związanych z zabezpieczeniami w organizacji. 

Strona bezpiecznego wyniku w Centrum zabezpieczeń zawiera:

- **Wynik** — bezpieczny wynik jest wyświetlany jako wartość procentowa, ale wartości bazowe są również jasne:

    [![Bezpieczny wynik pokazany jako wartość procentowa z podstawowych liczb jasne zbyt](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Kontrole zabezpieczeń** — każda kontrola jest logiczną grupą powiązanych zaleceń dotyczących zabezpieczeń i odzwierciedla obszary ataków podatnych na ataki. Formant to zestaw zaleceń dotyczących zabezpieczeń z instrukcjami, które ułatwiają implementowanie tych zaleceń. Wynik poprawia się tylko wtedy, gdy korygujesz *wszystkie* zalecenia dotyczące pojedynczego zasobu w ramach formantu.

    Aby natychmiast zobaczyć, jak dobrze organizacja zabezpiecza poszczególne powierzchnie ataku, przejrzyj wyniki dla każdej kontroli zabezpieczeń.

    Aby uzyskać więcej informacji, zobacz [Jak obliczany jest bezpieczny wynik](secure-score-security-controls.md#how-the-secure-score-is-calculated) poniżej. 


>[!TIP]
> Starsze wersje programu Security Center przyznały punkty na poziomie rekomendacji: po skorygowaniu zalecenia dla pojedynczego zasobu poprawił się bezpieczny wynik. Obecnie wynik poprawia się tylko wtedy, gdy korygujesz *wszystkie* zalecenia dotyczące pojedynczego zasobu w ramach formantu. Więc twój wynik poprawia się tylko wtedy, gdy poprawisz bezpieczeństwo zasobu.
> Ta ulepszona wersja jest nadal w wersji zapoznawczej, wcześniejsze środowisko bezpiecznego wyniku jest dostępne jako opcja z witryny Azure Portal. 


## <a name="locating-your-secure-score"></a>Lokalizowanie bezpiecznego wyniku

Usługa Security Center wyświetla wynik w widocznym miejscu: jest to pierwsza rzecz pokazana na stronie Przegląd. Jeśli klikniesz na dedykowanej stronie bezpiecznego wyniku, zobaczysz wynik w podziale na subskrypcję. Kliknij pojedynczą subskrypcję, aby wyświetlić szczegółową listę priorytetowych zaleceń i potencjalny wpływ, jaki ich korygowanie będzie miało na wynik subskrypcji.

## <a name="how-the-secure-score-is-calculated"></a>Jak obliczany jest bezpieczny wynik 

Wkład każdej kontroli bezpieczeństwa w ogólny bezpieczny wynik jest wyraźnie widoczny na stronie zaleceń.

[![Ulepszony bezpieczny wynik wprowadza mechanizmy kontroli bezpieczeństwa](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Aby uzyskać wszystkie możliwe punkty kontroli zabezpieczeń, wszystkie zasoby muszą być zgodne ze wszystkimi zaleceniami dotyczącymi zabezpieczeń w ramach kontroli zabezpieczeń. Na przykład usługa Security Center ma wiele zaleceń dotyczących zabezpieczania portów zarządzania. W przeszłości można było skorygować niektóre z tych powiązanych i współzależnych zaleceń, pozostawiając inne nierozwiązane, a twój bezpieczny wynik poprawi. Patrząc obiektywnie, łatwo jest twierdzić, że twoje bezpieczeństwo nie poprawiło się, dopóki nie rozwiązałeś ich wszystkich. Teraz musisz naprawić je wszystkie, aby zmienić swój bezpieczny wynik.

Na przykład kontrola zabezpieczeń o nazwie "Zastosuj aktualizacje systemu" ma maksymalny wynik sześciu punktów, który można zobaczyć w etykietce narzędzia na potencjalnej wartości wzrostu formantu:

[![Kontrola zabezpieczeń "Zastosuj aktualizacje systemu"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Potencjał kontroli zabezpieczeń "Zastosuj aktualizacje systemu" na powyższym zrzucie ekranu pokazuje "2% (1 punkt)". Oznacza to, że jeśli skorygujesz wszystkie zalecenia w tej kontroli, twój wynik wzrośnie o 2% (w tym przypadku jeden punkt). Dla uproszczenia wartości w kolumnie "Potencjalny wzrost" listy rekomendacji są zaokrąglane do liczb całych. Etykietki narzędzi pokazują dokładne wartości:

* **Maksymalny wynik** - Maksymalna liczba punktów, którą możesz zdobyć, wypełniając wszystkie zalecenia w ramach kontroli. Maksymalny wynik dla formantu wskazuje względne znaczenie tego formantu. Użyj wartości maksymalnej punktacji, aby klasyfikować problemy, nad którymi mają być najpierw pracować. 
* **Potencjalny wzrost** - Pozostałe punkty dostępne w ramach kontroli. Aby uzyskać te punkty dodane do bezpiecznego wyniku, należy skorygować wszystkie zalecenia formantu. W powyższym przykładzie jeden punkt pokazany dla formantu wynosi w rzeczywistości 0,96 punktu.
* **Aktualny wynik** - Aktualny wynik dla tej kontroli. Każda kontrola przyczynia się do całkowitego wyniku. W tym przykładzie formant przyczynia się 5,04 punktów do sumy. 

### <a name="calculations---understanding-your-score"></a>Obliczenia - zrozumienie wyniku

|Metryka|Formuła i przykład|
|-|-|
|**Aktualny wynik kontroli bezpieczeństwa**|<br>![Równanie do obliczania bieżącego wyniku kontroli zabezpieczeń](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Każda indywidualna kontrola zabezpieczeń przyczynia się do wyniku bezpieczeństwa. Każdy zasób, którego dotyczy zalecenie w ramach formantu, przyczynia się do bieżącego wyniku formantu. Bieżący wynik dla każdego formantu jest miarą stanu zasobów *w formancie.*<br>![Etykietki narzędzi przedstawiające wartości używane podczas obliczania bieżącego wyniku formantu zabezpieczeń](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>W tym przykładzie maksymalny wynik 6 zostanie podzielony przez 78, ponieważ jest to suma zdrowych i niezdrowych zasobów.<br>6 / 78 = 0,0769<br>Pomnożenie tego przez liczbę zdrowych zasobów (4) powoduje bieżący wynik:<br>0,0769 * 4 = **0,31**<br><br>|
|**Wskaźnik bezpieczeństwa**<br>Subskrypcja pojedyncza|<br>![Równanie do obliczania bieżącego bezpiecznego wyniku](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Pojedynczy wynik bezpiecznej subskrypcji z włączonymi wszystkimi formantami](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>W tym przykładzie istnieje pojedyncza subskrypcja z dostępnych wszystkich kontroli zabezpieczeń (potencjalny maksymalny wynik 60 punktów). Wynik pokazuje 28 punktów z możliwych 60, a pozostałe 32 punkty znajdują odzwierciedlenie w danych "Potencjalny wzrost punktacji" kontroli bezpieczeństwa.<br>![Lista kontroli i potencjalny wzrost punktacji](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Wskaźnik bezpieczeństwa**<br>Wiele subskrypcji|<br>Bieżący wynik dla wszystkich zasobów we wszystkich subskrypcjach są dodawane, a obliczenia są wtedy takie same jak w przypadku pojedynczej subskrypcji<br><br>Podczas wyświetlania wielu subskrypcji, bezpieczny wynik ocenia wszystkie zasoby we wszystkich włączonych zasadach i grupuje ich łączny wpływ na maksymalny wynik każdego formantu zabezpieczeń.<br>![Bezpieczny wynik dla wielu subskrypcji z włączoną obsługą wszystkich formantów](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Łączny wynik **nie** jest średnią; raczej jest to oceniana postawa stanu wszystkich zasobów we wszystkich subskrypcjach.<br>Tutaj również, jeśli przejdziesz do strony rekomendacji i zsumujesz potencjalne dostępne punkty, przekonasz się, że jest to różnica między bieżącym wynikiem (24) a maksymalnym dostępnym wynikiem (60).|
||||

## <a name="improving-your-secure-score"></a>Poprawa bezpiecznego wyniku

Aby poprawić swój bezpieczny wynik, koryguj zalecenia dotyczące zabezpieczeń z listy rekomendacji. Każde zalecenie można korygować ręcznie dla każdego zasobu lub za pomocą **szybkiej poprawki!** (jeśli jest dostępna), aby szybko zastosować środki zaradcze dla zalecenia do grupy zasobów. Aby uzyskać więcej informacji, zobacz [Korygowanie zaleceń](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Tylko wbudowane zalecenia mają wpływ na bezpieczny wynik.

## <a name="security-controls-and-their-recommendations"></a>Kontrole bezpieczeństwa i ich zalecenia

W poniższej tabeli wymieniono formanty zabezpieczeń w usłudze Azure Security Center. Dla każdego formantu można zobaczyć maksymalną liczbę punktów, które można dodać do bezpiecznego wyniku, jeśli skorygować *wszystkie* zalecenia wymienione w formancie, dla *wszystkich* zasobów. 

> [!TIP]
> Jeśli chcesz filtrować lub sortować tę listę inaczej, skopiuj ją i wklej do programu Excel.

|Kontrola bezpieczeństwa|Maksymalna liczba punktów z bezpiecznym wynikiem|Zalecenia|
|----------------|:-------------------:|---------------|
|**Włączanie usługi MFA**|10|- Mfa powinny być włączone na kontach z uprawnieniami właściciela w subskrypcji<br>- Mfa powinny być włączone na kontach z uprawnieniami do odczytu w subskrypcji<br>- MfA powinny być włączone konta z uprawnieniami do zapisu w subskrypcji|
|**Bezpieczne porty zarządzania**|8|- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych<br>- Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>- Porty zarządzania powinny być zamknięte na maszynach wirtualnych|
|**Zastosuj aktualizacje systemu**|6|- Problemy zdrowotne agenta monitorującego powinny być rozwiązywane na komputerach<br>- Agent monitorowania powinien być zainstalowany na zestawach skalowania maszyny wirtualnej<br>- Na maszynach należy zainstalować agenta monitorującego<br>- Wersja systemu operacyjnego powinna być aktualizowana dla ról usługi w chmurze<br>- Aktualizacje systemu na zestawach skalowania maszyny wirtualnej powinny być zainstalowane<br>- Aktualizacje systemu powinny być zainstalowane na komputerach<br>- Urządzenia powinny zostać ponownie uruchomione, aby zastosować aktualizacje systemu<br>- Usługi Kubernetes powinny zostać uaktualnione do wersji kubernetes niechronionych<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych|
|**Korygowanie luk w zabezpieczeniach**|6|- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL<br>- Luki w zabezpieczeniach obrazów rejestru kontenerów platformy Azure powinny zostać naprawione<br>- Luki w bazach danych SQL powinny zostać naprawione<br>- Luki w zabezpieczeniach powinny zostać naprawione przez rozwiązanie do oceny luk w zabezpieczeniach<br>- Ocena podatności powinna być włączona w wystąpieniach zarządzanych SQL<br>- Ocena podatności powinna być włączona na serwerach SQL<br>- Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych|
|**Włącz szyfrowanie w spoczynku**|4|- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych<br>- Przezroczyste szyfrowanie danych w bazach danych SQL powinno być włączone<br>- Zmienne konta automatyzacji powinny być szyfrowane<br>- Klastry sieci szkieletowej usług powinny mieć właściwość ClusterProtectionLevel ustawiona na EncryptAndSign<br>- Ochrona TDE serwera SQL powinna być szyfrowana za pomocą własnego klucza|
|**Szyfrowanie danych podczas przesyłania**|4|- Aplikacja API powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>- Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>- Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis<br>- Bezpieczny transfer na konta magazynu powinny być włączone<br>- Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS|
|**Zarządzanie dostępem i uprawnieniami**|4|- Do subskrypcji powinno być wyznaczonych maksymalnie 3 właścicieli<br>- Przestarzałe konta powinny zostać usunięte z subskrypcji (Wersja zapoznawcza)<br>- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (Wersja zapoznawcza)<br>- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (Wersja zapoznawcza)<br>- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji<br>- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji (Wersja zapoznawcza)<br>- Powinien być więcej niż jeden właściciel przypisany do subskrypcji<br>- Kontrola dostępu oparta na rolach (RBAC) powinna być używana w usługach Kubernetes (wersja zapoznawcza)<br>- Klastry sieci szkieletowej usług powinny używać tylko usługi Azure Active Directory do uwierzytelniania klienta|
|**Korygowanie konfiguracji zabezpieczeń**|4|- Zasady zabezpieczeń zasobnika powinny być zdefiniowane w usługach Kubernetes<br>- Luki w zabezpieczeniach kontenerów powinny zostać naprawione<br>- Luki w konfiguracji zabezpieczeń na komputerach powinny zostać naprawione<br>- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>- Na maszynach należy zainstalować agenta monitorującego<br>- Agent monitorowania powinien być zainstalowany na zestawach skalowania maszyny wirtualnej<br>- Problemy zdrowotne agenta monitorującego powinny być rozwiązywane na komputerach|
|**Ograniczanie nieautoryzowanego dostępu do sieci**|4|- Przekazywanie ip na maszynie wirtualnej powinno być wyłączone<br>- Autoryzowane zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes (wersja zapoznawcza)<br>- (PRZESTARZAŁY) Dostęp do usług aplikacji powinien być ograniczony (wersja zapoznawcza)<br>- (DEPRECJONOWANE) Zasady dotyczące aplikacji internetowych w sieciach nd IaaS powinny zostać zaostrzone<br>- Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>- CORS nie powinien zezwalać każdemu zasobom na dostęp do aplikacji API<br>- CORS nie powinien zezwalać każdemu zasobom na dostęp do aplikacji function<br>- CORS nie powinien zezwalać każdemu zasobom na dostęp do aplikacji sieci Web<br>- Zdalne debugowanie powinno być wyłączone dla aplikacji API<br>- Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji<br>- Zdalne debugowanie powinno być wyłączone dla aplikacji sieci Web<br>- Dostęp powinien być ograniczony dla permisywnych grup zabezpieczeń sieciowych z maszynami wirtualnymi z dostępem do Internetu<br>- Zasady sieciowej grupy zabezpieczeń dla maszyn wirtualnych skierowanych do Internetu powinny zostać zaostrzone|
|**Stosowanie adaptacyjnej kontroli aplikacji**|3|- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>- Na maszynach należy zainstalować agenta monitorującego<br>- Problemy zdrowotne agenta monitorującego powinny być rozwiązywane na komputerach|
|**Stosowanie klasyfikacji danych**|2|- Poufne dane w bazach danych SQL powinny być klasyfikowane (Podgląd)|
|**Ochrona aplikacji przed atakami DDoS**|2|- Standard ochrony przed atakami DDoS powinien być włączony|
|**Włączanie ochrony punktów końcowych**|2|- Błędy kondycji ochrony punktu końcowego powinny być korygowane w zestawach skalowania maszyny wirtualnej<br>- Problemy zdrowotne związane z ochroną punktów końcowych powinny być rozwiązywane na komputerach<br>- Rozwiązanie do ochrony punktów końcowych powinno być zainstalowane na zestawach skalowania maszyn wirtualnych<br>- Zainstaluj rozwiązanie ochrony punktów końcowych na maszynach wirtualnych<br>- Problemy zdrowotne agenta monitorującego powinny być rozwiązywane na komputerach<br>- Agent monitorowania powinien być zainstalowany na zestawach skalowania maszyny wirtualnej<br>- Na maszynach należy zainstalować agenta monitorującego<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>- Zainstaluj rozwiązanie zabezpieczające punkty końcowe na swoich komputerach|
|**Włączanie inspekcji i rejestrowania**|1|- Inspekcja na serwerze SQL powinna być włączona<br>- Dzienniki diagnostyczne w usługach app services powinny być włączone<br>- Dzienniki diagnostyczne w usłudze Azure Data Lake Store powinny być włączone<br>- Dzienniki diagnostyczne w usłudze Azure Stream Analytics powinny być włączone<br>- Dzienniki diagnostyczne na kontach partii powinny być włączone<br>- Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone<br>- Dzienniki diagnostyczne w Centrum zdarzeń powinny być włączone<br>- Dzienniki diagnostyczne w U. IoT Hub powinny być włączone<br>- Dzienniki diagnostyczne w Przechowalni kluczy powinny być włączone<br>- Dzienniki diagnostyczne w aplikacjach logiki powinny być włączone<br>- Dzienniki diagnostyczne w usłudze wyszukiwania powinny być włączone<br>- Dzienniki diagnostyczne w usłudze Service Bus powinny być włączone<br>- Dzienniki diagnostyczne w zestawach skalowania maszyny wirtualnej powinny być włączone<br>- Reguły alertów metryki powinny być skonfigurowane na kontach wsadowych<br>- Ustawienia inspekcji SQL powinny mieć grupy akcji skonfigurowane do przechwytywania krytycznych działań<br>- Serwery SQL powinny być skonfigurowane z inspekcji dni przechowywania większe niż 90 dni.|
|**Wdrażanie najlepszych rozwiązań w zakresie zabezpieczeń**|0|- Dostęp do kont pamięci masowej z konfiguracją zapory i sieci wirtualnej powinien być ograniczony<br>- Wszystkie reguły autoryzacji z wyjątkiem funkcji RootManageSharedAccessKey powinny zostać usunięte z obszaru nazw Centrum zdarzeń<br>- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL<br>- Reguły autoryzacji wystąpienia Usługi Event Hub powinny być zdefiniowane<br>- Konta magazynu powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager<br>- Maszyny wirtualne powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager<br>- Zaawansowane ustawienia zabezpieczeń danych dla serwera SQL powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń<br>- Zaawansowane zabezpieczenia danych powinny być włączone w zarządzanych wystąpieniach<br>- Wszystkie zaawansowane typy ochrony przed zagrożeniami powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych w przypadku zarządzanym sql<br>- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w sql server zaawansowane ustawienia zabezpieczeń danych<br>- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystko" w ustawieniach zaawansowanego zabezpieczenia danych serwera SQL<br>- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń<br>- Wszystkie zaawansowane typy ochrony przed zagrożeniami powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych serwera SQL|
||||

## <a name="secure-score-faq"></a>Bezpieczne często zadawane pytania dotyczące wyników

### <a name="why-has-my-secure-score-gone-down"></a>Dlaczego mój bezpieczny wynik spadł?
Usługa Security Center przełączyła się na ulepszony bezpieczny wynik (obecnie w stanie podglądu), który zawiera zmiany w sposobie obliczania wyniku. Teraz musisz rozwiązać wszystkie zalecenia dla zasobu, aby otrzymać punkty. Wynik również zmienił się na skalę 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Jeśli zajmę się tylko trzema z czterech zaleceń w ramach kontroli zabezpieczeń, czy mój bezpieczny wynik się zmieni?
Nie. Nie zmieni się, dopóki nie skorygujesz wszystkich zaleceń dotyczących pojedynczego zasobu. Aby uzyskać maksymalny wynik dla formantu, należy skorygować wszystkie zalecenia dla wszystkich zasobów.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Czy poprzednie doświadczenie bezpiecznego wyniku jest nadal dostępne? 
Tak. Przez jakiś czas będą biegać obok siebie, aby ułatwić przejście. Spodziewaj się, że poprzedni model zostanie wycofany w czasie. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Jeśli zalecenie nie ma zastosowania do mnie i wyłączę je w zasadach, czy moja kontrola zabezpieczeń zostanie spełniona, a mój bezpieczny wynik zaktualizowany?
Tak. Zaleca się wyłączenie zaleceń, gdy nie mają one zastosowania w Twoim środowisku. Aby uzyskać instrukcje dotyczące wyłączania określonego zalecenia, zobacz [Wyłączanie zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Jeśli kontrola bezpieczeństwa oferuje mi zero punktów w kierunku mojego bezpiecznego wyniku, czy powinienem go zignorować?
W niektórych przypadkach zobaczysz wynik maksymalny formantu większy niż zero, ale wpływ wynosi zero. Gdy przyrostowy wynik do ustalania zasobów jest znikomy, jest zaokrąglany do zera. Nie ignoruj tych zaleceń, ponieważ nadal wprowadzają ulepszenia zabezpieczeń. Jedynym wyjątkiem jest formant "Dodatkowe najlepsze praktyki". Korygowanie tych zaleceń nie zwiększy twojego wyniku, ale zwiększy ogólne bezpieczeństwo.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano bezpieczny wynik i formanty zabezpieczeń, które wprowadza. Materiały pokrewne można znaleźć w następujących artykułach:

- [Dowiedz się więcej o różnych elementach rekomendacji](security-center-recommendations.md)
- [Dowiedz się, jak korygować zalecenia](security-center-remediate-recommendations.md)
---
title: Projektowanie bezpiecznych aplikacji w systemie Microsoft Azure
description: W tym artykule omówiono najlepsze rozwiązania, należy wziąć pod uwagę podczas faz wymagań i projektowania projektu aplikacji sieci web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653285"
---
# <a name="design-secure-applications-on-azure"></a>Projektowanie bezpiecznych aplikacji na platformie Azure
W tym artykule, firma Microsoft przedstawia działania dotyczące zabezpieczeń i kontroli należy wziąć pod uwagę podczas projektowania aplikacji dla chmury. Szkolenia zasobów oraz pytania zabezpieczające i koncepcji, które należy wziąć pod uwagę podczas wymagań i projektowania fazy Microsoft [cykl projektowania zabezpieczeń (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) zostały uwzględnione. Celem jest pomagającym w zdefiniowaniu działań i usług platformy Azure, które służą do projektowania bardziej bezpiecznych aplikacji.

W tym artykule omówione są następujące fazy SDL:

- Szkolenia
- Wymagania
- Projekt

## <a name="training"></a>Szkolenia
Przed rozpoczęciem tworzenia aplikacji w chmurze, potrwać objaśnienie zabezpieczeń i ochrony prywatności na platformie Azure. Wykonując ten krok, można zmniejszyć liczby i ważności luk w zabezpieczeniach możliwe do wykorzystania w aplikacji. Możliwe będzie lepiej przygotować się reagować odpowiednio na zagrożeniach kolejne zmiany.

Użyj następujących zasobów na etapie szkolenia, aby zapoznać się z usługami platformy Azure, które są dostępne dla deweloperów i najlepsze rozwiązania dotyczące zabezpieczeń na platformie Azure:

  - [Przewodnik dewelopera usługi Azure](https://azure.microsoft.com/campaigns/developer-guide/) dowiesz się, jak rozpocząć pracę z platformą Azure. Przewodnik pokazuje usług, które umożliwia uruchamianie aplikacji, przechowywania danych, zestawowi analizy, Kompiluj aplikacje IoT i wdrażaj swoje rozwiązania w sposób bardziej efektywny i bezpieczny.

  - [Wprowadzenie — przewodnik dla deweloperów platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) zawiera podstawowe informacje dla deweloperów, którzy chcą rozpocząć korzystanie z platformy Azure dla swoich narzędzi programistycznych.

  - [Zestawy SDK i narzędzia](https://docs.microsoft.com/azure/index#pivot=sdkstools) opisano narzędzia, które są dostępne na platformie Azure.

  - [Usługi Azure DevOps](https://docs.microsoft.com/azure/devops/) umożliwia tworzenie narzędzi do współpracy. Narzędzia te obejmują potoki o wysokiej wydajności, bezpłatne repozytoria Git, konfigurowalne tablice Kanban i testowania obciążenia w rozbudowane automatycznych i w chmurze.
    [Centrum zasobów DevOps](https://docs.microsoft.com/azure/devops/learn/) łączy nasze zaawansowane zasoby do nauki DevOps wskazówki, narzędzia Git kontroli wersji, metody agile, jak pracujemy z metodyki DevOps w firmie Microsoft i jak można ocenić własny postęp operacji deweloperskich.

  - [5 najpopularniejszych zabezpieczeń elementów do uwzględnienia przed wypchnięciem do środowiska produkcyjnego](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) pokazuje, jak pomóc w zabezpieczaniu aplikacji sieci web na platformie Azure i chronić aplikacje przed atakami najbardziej typowe i niebezpiecznych aplikacji sieci web.

  - [Secure DevOps Kit dla platformy Azure](https://azsk.azurewebsites.net/index.html) to kolekcja skryptów, narzędzia, rozszerzenia i automatyzacji, przeznaczoną do kompleksowej platformy Azure subskrypcji i zasobów zabezpieczeń potrzeb zespołów DevOps, korzystających z automatyzacji rozbudowane. Zestaw Secure DevOps Kit dla platformy Azure można pokazują, jak bezproblemowo zintegrować zabezpieczeń natywnych przepływów pracy metodyki DevOps. Zestaw adresów narzędzia, takie jak testy weryfikacji zabezpieczeń (SVTs), które ułatwiają deweloperom pisać bezpieczny kod i przetestować konfigurację bezpieczne aplikacje w chmurze w etapach tworzenia kodu i wczesne.

  - [Najlepsze rozwiązania dotyczące rozwiązań platformy Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) zawiera zbiór zabezpieczeń najlepszych rozwiązań, aby użyć jak projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.

## <a name="requirements"></a>Wymagania
Faza definicji wymagań jest to istotny krok, definiując aplikacja jest i do czego ona będzie po jego udostępnieniu. Faza wymagań jest również czasu wziąć pod uwagę środki bezpieczeństwa, utworzysz w aplikacji. W tej fazie możesz również rozpocząć wykonywanie kroków, które spowoduje przejście przez cały proces SDL, aby zwolnić i wdrażanie bezpiecznych aplikacji.

### <a name="consider-security-and-privacy-issues"></a>Należy wziąć pod uwagę problemy dotyczące zabezpieczeń i prywatności
Ta faza jest najlepszy moment na należy wziąć pod uwagę podstawowe zabezpieczeń i prywatności problemów. Definiowanie akceptowalnego poziomu zabezpieczeń i prywatności na początku projektu pomaga zespołu:

- Poznaj ryzyko związane z problemów z zabezpieczeniami.
- Identyfikowania i naprawiania błędów zabezpieczeń podczas programowania.
- Zastosowanie ustalonych poziomów zabezpieczeń i prywatności w całej całego projektu.

Kiedy piszesz wymagania aplikacji, pamiętaj, że należy wziąć pod uwagę kontroli zabezpieczeń, które mogą pomóc w zabezpieczeniu aplikacji i danych.

### <a name="ask-security-questions"></a>Zadawaj pytania zabezpieczeń
Zadawanie pytań zabezpieczeń, takich jak:

  - Czy Moja aplikacja zawiera dane poufne?

  - Zbieraj mojej aplikacji lub przechowywać dane, które wymaga na przestrzeganie standardów i zgodności programów, takich jak [Federal finansowych instytucji badania Council (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) lub [branży kart płatniczych Data Security Standards (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - Zbieraj mojej aplikacji lub zawierają dane poufne osobistego lub klienta, który może służyć, samodzielnie lub z innymi informacji, aby zidentyfikować, skontaktuj się z lub zlokalizować jedna osoba?

  - Moja aplikacja zbieranie ani zawierać dane, które mogą być używane do dostępu użytkownik indywidualny medycyna, prawo edukacyjnych, finansowych, lub informacje o zatrudnienia Identyfikowanie wrażliwości danych w fazie wymagania stałego klasyfikowania danych i zidentyfikować metodę ochrony danych, które będą używane dla aplikacji.

  - Gdzie i jak są przechowywane Moje dane? Należy wziąć pod uwagę, jak można monitorować usługi magazynu, których Twoja aplikacja używa nieoczekiwanych zmian (na przykład czasy odpowiedzi wolniejsze). Użytkownik będzie do wywierania wpływu na rejestrowanie do zbierania bardziej szczegółowych danych i analizowania problemu w głębi?

  - Moja aplikacja będzie dostępna publicznie (internet) lub tylko wewnętrznie? Jeśli aplikacja jest dostępna publicznie, jak zabezpieczasz danych, które mogą być zbierane z użycia w niewłaściwy sposób? Jeśli aplikacja jest dostępna wewnętrznie tylko, należy wziąć pod uwagę kto w organizacji mają dostęp do aplikacji i jak długo powinni mieć dostęp.

  - Model tożsamości zrozumieć przed rozpoczęciem projektowania aplikacji? Jak będzie stwierdzisz, że użytkownicy są kto mówią, że są one i jakie użytkownik jest autoryzowany w celu?

  - Powoduje Moja aplikacja wykonania poufne lub ważne zadania (np. przesyłania pieniądze, odblokowywanie drzwi lub dostarczania medycyna)?
    Należy wziąć pod uwagę, jak należy sprawdzić, który użytkownik wykonujący zadania poufnych jest autoryzowany do wykonania zadania oraz sposób uwierzytelniania osoby to kto mówią, że są one. Autoryzacja (autoryzacji) polega na przyznania uprawnienia podmiotu zabezpieczeń uwierzytelnionego zabezpieczeń, na coś zrobić. Uwierzytelnianie (uwierzytelniania) jest wyzwaniem strony do legalnych poświadczeń.

  - Moja aplikacja może wykonywać żadnych działań oprogramowania ryzykowne, takich jak Zezwalanie użytkownikom na przekazywanie lub pobieranie plików lub innych danych? Jeśli aplikacja wykonać ryzykowne działania, należy rozważyć, jak aplikacja będzie chronić użytkowników przed obsługi złośliwe pliki lub dane.

### <a name="review-owasp-top-10"></a>Przegląd OWASP top 10
Należy wziąć pod uwagę, przeglądając [ <span class="underline">OWASP Top 10 aplikacji zagrożeń</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
10 pierwszych OWASP adresów krytyczne zagrożenia bezpieczeństwa do aplikacji sieci web.
Rozpoznawanie te zagrożenia dla bezpieczeństwa mogą pomóc w decyzje dotyczące wymagań i projektowania, które zminimalizują te zagrożenia w aplikacji.

Ważne jest myśleć o kontrole bezpieczeństwa, aby uniknąć naruszeń.
Jednak warto [naruszenia](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) zostanie przeprowadzona. Zakładając, że w przypadku naruszenia zabezpieczeń pomaga w odpowiedzi na niektóre ważne pytania dotyczące zabezpieczeń z wyprzedzeniem, aby nie musieli musi być podana w nagłych:

  - Jak będą wykrywa atak?

  - Co będzie zrobić w przypadku ataku lub naruszenie zabezpieczeń?

  - Jak będą sprawności ataków, takich jak dane przeciek lub naruszeniem?

## <a name="design"></a>Projekt

Ważne do ustalenia najlepsze rozwiązania dotyczące projektowania i specyfikacje funkcjonalności jest na etapie projektowania. Również bardzo ważne jest wykonanie analizy ryzyka, które pomaga rozwiązywać problemy zabezpieczeń i prywatności w całym projekcie.

Po mają wymagania dotyczące zabezpieczeń w miejscu i zagadnienia dotyczące projektowania bezpiecznych, możesz uniknąć lub zminimalizować możliwości lukę w zabezpieczeniach. Lukę w zabezpieczeniach jest nadzoru w projektowaniu aplikacji, które mogą umożliwić użytkownikowi wykonywanie akcji złośliwego lub nieoczekiwany po udostępnieniu aplikacji.

W fazie projektowania również zastanów się, jak można stosować zabezpieczenia w warstwach; jeden poziom ochrony nie jest wystarczająco niekoniecznie. Co się stanie, jeśli osoba atakująca uzyska ostatnie zapory aplikacji sieci web (WAF)? Ma inną kontrolę zabezpieczeń w celu ochrony przed atakiem tego.

Pamiętając o tym omówimy następujące pojęcia bezpiecznego projektu i opcji zabezpieczeń, które należy rozwiązać podczas projektowania bezpiecznych aplikacji:

- Użyj bezpieczna biblioteka kodowania i struktury oprogramowania.
- Skanowanie składników na ataki.
- Należy użyć podczas projektowania aplikacji do modelowania zagrożeń.
- Zmniejszyć obszar ataków.
- Przyjęcie zasad tożsamości jako głównych zabezpieczeń obwodowych.
- Konieczne jest ponowne uwierzytelnienie dla ważnych transakcji.
- Rozwiązanie do zarządzania kluczami umożliwia zabezpieczanie kluczy, poświadczeń i innych wpisów tajnych.
- Ochrona poufnych danych.
- Zaimplementuj odporny na uszkodzenia miary.
- Korzystaj z obsługi błędów i wyjątków.
- Rejestrowanie użycia i alertów.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Użyj bezpiecznego biblioteki kodowania i architektura oprogramowania

Do tworzenia aplikacji użyj bezpieczna biblioteka kodowania i środowisko oprogramowania, zawierający osadzone zabezpieczeń. Deweloperzy mogą być używać istniejących, sprawdzonych funkcji (szyfrowania, higieną danych wejściowych, danych wyjściowych kodowania, klucze lub parametry połączenia i jakichkolwiek innych czynności, które mogą być uważane za kontroli zabezpieczeń) zamiast opracowywania zabezpieczenia od podstaw. Dzięki temu można zabezpieczyć się przed związanych z zabezpieczeniami projektowania i implementacji wady.

Pamiętaj, że używasz najnowszej wersji używanej platformy i wszystkie funkcje zabezpieczeń, które są dostępne w ramach. Firma Microsoft oferuje kompleksowy [zestaw narzędzi programistycznych](https://azure.microsoft.com/product-categories/developer-tools/) dla wszystkich deweloperów, pracy z dowolnej platformy i języka, aby dostarczać aplikacje w chmurze. Można tworzyć kod w wybranym języku, wybierając z różnymi [zestawów SDK](https://azure.microsoft.com/downloads/).
Możesz korzystać z zalet profesjonalnych zintegrowanych środowisk projektowych (IDE) i edytorów, które mają zaawansowane możliwości debugowania i wbudowanej pomocy technicznej platformy Azure.

Firma Microsoft oferuje szereg [języków, struktur i narzędzi](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) służące do tworzenia aplikacji na platformie Azure. Na przykład [platformy Azure dla deweloperów platformy .NET i .NET Core](https://docs.microsoft.com/dotnet/azure/). Dla każdego języka i struktury, które firma Microsoft oferuje znajdziesz, przewodniki Szybki Start, samouczki i dokumentacja interfejsu API, aby pomóc Ci szybko rozpocząć pracę.

Platforma Azure oferuje szereg usług, których można użyć do hostowania aplikacji sieci web i witryn sieci Web. Te usługi umożliwiają programować w ulubionym języku, czy to .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python.
[Usługa Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (aplikacje sieci Web) jest jednym z tych usług.

Usługa Web Apps zapewnia zaawansowane systemu Microsoft Azure do swojej aplikacji. Obejmuje on zabezpieczenia, równoważenie obciążenia, automatycznego skalowania i automatyczne zarządzanie. Zawsze możesz skorzystać z możliwości metodyki DevOps w aplikacji sieci Web, takich jak zarządzanie pakietami przemieszczania środowisk, domen niestandardowych, certyfikatami SSL/TLS i ciągłe wdrażanie z DevOps platformy Azure, GitHub, Docker Hub i innych źródeł.

Platforma Azure oferuje inne usługi, które można użyć do hostowania aplikacji sieci web i witryn sieci Web. W przypadku większości scenariuszy najlepszym wyborem jest usługa Web Apps. Architektura micro usługi, można rozważyć [usługi Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
W razie konieczności sprawowania większej kontroli nad maszynami wirtualnymi, na których działa kod, należy wziąć pod uwagę usługę [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Aby uzyskać więcej informacji o tym, jak dokonać wyboru między tych usług platformy Azure, zobacz [porównanie usługi Azure App Service, maszyny wirtualne, usługi Service Fabric i Cloud Services](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm).

### <a name="apply-updates-to-components"></a>Zastosuj aktualizacje składników

Aby zapobiec luk w zabezpieczeniach, powinny stale spisu składniki po stronie klienta i po stronie serwera (na przykład, struktury i biblioteki) i ich zależności dotyczących aktualizacji. Nowych luk w zabezpieczeniach i zaktualizowane oprogramowanie wersje są wydawane stale. Upewnij się, że masz plan ciągłego monitorowania, klasyfikowanie i stosowania aktualizacji lub zmiany konfiguracji do biblioteki i składniki, których używasz.

Zobacz [Otwórz sieci Web aplikacji Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) stronie [składników przy użyciu znanych luk w zabezpieczeniach](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) dla sugestie dotyczące narzędzi. Można również subskrybować alerty e-mail dotyczące luki w zabezpieczeniach, that are related to składniki, których używasz.

### <a name="use-threat-modeling-during-application-design"></a>Użyj podczas projektowania aplikacji do modelowania zagrożeń

Modelowanie zagrożeń to proces identyfikowania potencjalne zagrożenia bezpieczeństwa do sieci Web i aplikacji biznesowych i zapewnienie, że zostały spełnione odpowiednie środki zaradcze. Proces SDL Określa, że zagrożenia modelowania w fazie projektowania, gdy rozwiązywania potencjalnych problemów jest stosunkowo łatwa i ekonomiczna powinien zaangażowaniu zespołów. Przy użyciu modelowania w fazie projektowania może znacznie zmniejszyć całkowity koszt rozwoju zagrożeń.

Aby ułatwić zagrożeń procesu modelowania, został zaprojektowany [narzędzie do modelowania zagrożeń SDL](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) z ekspertami niezwiązanych z zabezpieczeniami na uwadze. To narzędzie ułatwia modelowanie zagrożeń dla wszystkich deweloperów, zapewniając wyraźne wskazówki dotyczące sposobu tworzenia i analizowania modele zagrożeń.

Modelowanie projektu aplikacji i wyliczania [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) zagrożenia — Spoofing, Tampering, Repudiation, ujawnienie informacji, Denial of Service i podniesienie poziomu uprawnień — we wszystkich relacji zaufania granice przebiegło efektywną metodę do przechwytywania błędów projektowania na wczesnym etapie. Poniższa lista zagrożeń STRIDE i zapewnia niektóre przykład środki zaradcze, które korzystają z funkcji udostępnianych przez platformę Azure. Te czynniki nie będą działać w każdej sytuacji.

| Przed zagrożeniami | Właściwości zabezpieczeń | Potencjalne środki zaradcze platformy Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Fałszowanie zawartości               | Authentication        | [Wymagaj połączeń HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manipulowanie              | Integralność             | Sprawdzanie poprawności certyfikatów SSL/TLS. Certyfikaty X.509 z jednostkami, z którymi łączą się z w pełni należy sprawdzić, aplikacje, które używają protokołu SSL/TLS. Certyfikaty usługi Azure Key Vault, aby użyć [Zarządzanie x509 swoje certyfikaty](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates). |
| odrzucenie            | Niemożność wyparcia się       | Włączanie usługi Azure [monitorowania i diagnostyki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).|
| Ujawnianie informacji | Poufność       | Szyfruj poufne dane [magazynowanych](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) i [przesyłanych](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit). |
| Odmowa usługi      | Dostępność          | Monitoruj metryki wydajności dla typu "odmowa potencjalnych" warunki usługi. Implementuje filtry połączenia. [Usługa Azure DDoS protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps), w połączeniu z najlepsze rozwiązania dotyczące projektowania aplikacji, zapewnia ochronę przed atakami DDoS.|
| Podniesienie uprawnień | Authorization         | Za pomocą usługi Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure).|

### <a name="reduce-your-attack-surface"></a>Zmniejszyć obszar ataków

Obszar narażony na ataki jest łączną sumę gdzie może wystąpić, potencjalnych lukach w zabezpieczeniach. W tym dokumencie możemy skoncentrować się na aplikacji obszar narażony na ataki.
Koncentruje się na ochronę aplikacji przed atakiem. Jest prosty i szybki sposób, aby zminimalizować obszar narażony na woluminie można usuwać nieużywane zasoby i kod z aplikacji. Mniejszego aplikacji, mniejszego ataków. Na przykład usunąć:

- Kod funkcji, które nie zostały jeszcze zwolnione.
- Debugowanie kodu pomocy technicznej.
- Interfejsów sieciowych i protokołów, które nie są używane, lub które zostały wycofane.
- Maszyny wirtualne i inne zasoby, które nie są używane.

Wykonywania regularnych Oczyszczanie zasobów i zapewnienie, że usunąć nieużywany kod to doskonały sposób, aby upewnić się, że są mniej możliwości dla uczestników złośliwych działań na ataki.

Bardziej szczegółowe i szczegółowe sposobem zmniejszyć obszar ataków jest aby zakończyć analiza obszaru narażonego na ataki. Analiza obszaru narażonego na ataki ułatwia mapowanie części systemu, które muszą zostać sprawdzone i przetestowane pod kątem luk w zabezpieczeniach.

Analiza obszaru narażonego na ataki ma na celu zrozumienie obszary ryzyka w aplikacji, dzięki czemu deweloperzy i specjaliści ds. zabezpieczeń świadomość, jakich części aplikacji są otwarte na ataki. Następnie można znaleźć sposobów, aby zminimalizować ten potencjalny, śledzenie, kiedy i jak obszar narażony na ataki zmiany, i mahesh thiagarajan z punktu widzenia ryzyka.

Analiza obszaru narażonego na ataki ułatwia identyfikowanie:

- Funkcje i części systemu należy przejrzeć i testować aplikacje dla luki w zabezpieczeniach.
- O wysokim ryzyku obszary kodu, które wymagają ochrony ochronę w głębi (części systemu, który chcesz chronić).
- Kiedy zmienić obszar narażony na ataki i musimy odświeżyć oceny zagrożeń.

Zmniejszenie możliwości dla osób atakujących w celu wykorzystania potencjalne słabe miejscu lub luk w zabezpieczeniach wymaga dokładnie analizować ogólną ataki Twojej aplikacji. Zawiera także wyłączenie lub ograniczenie dostępu do usług systemu, stosując zasadę najmniejszych uprawnień i wykorzystujących warstwową ochronę, gdy jest to możliwe.

Omówimy [przeprowadzenie przeglądu powierzchni ataku](secure-develop.md#conduct-attack-surface-review) w fazie weryfikacji SDL.

> [!NOTE]
> **Jaka jest różnica między analiza obszaru narażonego na ataki i modelowania zagrożeń?**
Modelowanie zagrożeń to proces identyfikowania potencjalne zagrożenia dla bezpieczeństwa do aplikacji i zapewnianie, że zostały spełnione odpowiednie środki zaradcze przed zagrożeniami. Analiza obszaru narażonego na ataki identyfikuje o wysokim ryzyku obszarów kodu, które są otwarte na ataki. Obejmuje to znalezienie sposobów na obrony o wysokim ryzyku obszarów aplikacji i przeglądanie i testowania tych obszarów kodu przed wdrożeniem aplikacji.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Przyjęcie zasad tożsamości jako głównych zabezpieczeń obwodowych

Podczas projektowania aplikacji w chmurze, należy rozwinąć zabezpieczeń aktywność obwodowej na interfejsach sieciowych podejście do podejście skoncentrowane na tożsamości. W przeszłości zabezpieczeń obwodowych głównego lokalnego był sieci organizacji. Większość projektów zabezpieczeń lokalnych użyć sieci jako przestawne głównych zabezpieczeń. Dla aplikacji w chmurze są lepiej byłoby, biorąc pod uwagę tożsamość jako głównych zabezpieczeń obwodowych.

Rzeczy można zrobić, aby tworzyć skoncentrowane na tożsamości podejście do projektowania aplikacji sieci web:

- Wymuszanie uwierzytelniania wieloskładnikowego dla użytkowników.
- Użyj silnego uwierzytelniania i autoryzacji platformy.
- Zastosuj zasadę najmniejszych uprawnień.
- Zaimplementowanie dostępu just in time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Wymuszanie uwierzytelniania wieloskładnikowego dla użytkowników

Użyj uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuskładnikowe jest bieżące normy, do uwierzytelniania i autoryzacji, ponieważ eliminuje luki zabezpieczeń, które są integralną częścią nazwy użytkownika i hasła typy uwierzytelniania. Dostęp do interfejsów zarządzania platformy Azure (Azure portal/zdalnego programu PowerShell) i przeznaczonych dla klientów usługi powinien być zaprojektowana i skonfigurowana do używania [usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Korzystać z silnego uwierzytelniania i autoryzacji platform

Użyj dostarczone przez platformę mechanizmy uwierzytelniania i autoryzacji, zamiast kodu niestandardowego. Jest to spowodowane opracowanie kodu, uwierzytelnianie niestandardowe mogą być podatne na błędy. Komercyjne kodu (na przykład od firmy Microsoft) często jest często zweryfikowane pod kątem bezpieczeństwa. [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) to Azure rozwiązanie do zarządzania tożsamościami i dostępem. Te narzędzia usługi Azure AD i usługi, pomoc związanych z bezpieczeństwem programowania:

- [Azure platforma tożsamości usługi AD (Azure AD dla deweloperów)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) to usługa tożsamości w chmurze używanego przez deweloperów do tworzenia aplikacji, które bezpieczne logowanie użytkowników. Usługa Azure AD pomaga deweloperom, którzy tworzą jedną dzierżawą, line-of-business apps (LOB) oraz deweloperów, którzy chcą tworzyć aplikacje wielodostępne. Oprócz podstawowego logowania aplikacje, które są tworzone za pomocą usługi Azure AD można wywoływać APIs firmy Microsoft i niestandardowych interfejsów API, które są oparte na platformie Azure AD. Platforma tożsamości usługi Azure AD obsługuje protokoły będące standardami branżowymi, takich jak OAuth 2.0 i OpenID Connect.

- [Usługa Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) to usługa zarządzania tożsamościami, służy do dostosowywania i kontrolować, jak klienci się rejestrują, zaloguj się i zarządzają swoimi profilami podczas korzystania z aplikacji. Obejmuje to aplikacje, które są tworzone dla systemów iOS, Android i platformy .NET, m.in. Usługa Azure AD B2C umożliwia te akcje przy jednoczesnej ochronie tożsamości klientów.

#### <a name="apply-the-principle-of-least-privilege"></a>Zastosuj zasadę najmniejszych uprawnień

Pojęcie [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) oznacza, zapewniając użytkownikom dokładny poziom dostępu i kontroli, których potrzebują do wykonania swoich zadań i od niczego więcej.

Deweloper oprogramowania potrzebuje uprawnienia administratora domeny? Asystenta administracyjnego potrzebować dostępu do administracyjnej formantów na swoim komputerze osobistym? Ocena dostęp do oprogramowania nie różni się. Jeśli używasz [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) aby nadać różne możliwości użytkowników i uprawnienia w aplikacji, nie podajesz każdy dostęp do wszystkiego. Ograniczanie dostępu do co jest wymagane dla każdej roli, ograniczyć ryzyko związane z zabezpieczeniami problem.

Upewnij się, że aplikacja wymusza [najniższych uprawnień](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) w całym jego wzorce dostępu.

> [!NOTE]
>  Reguły najniższych uprawnień należy do zastosowania do oprogramowania i osób, tworzenia oprogramowania. Deweloperzy oprogramowania może być duży zagrożenie dla bezpieczeństwa IT, jeśli otrzymują one zbyt szeroki dostęp. Konsekwencje może być poważny, jeśli deweloper ma złośliwego działania lub znajduje się zbyt szeroki dostęp. Firma Microsoft zaleca stosowanie reguły najniższych uprawnień dla deweloperów podczas cyklu tworzenia oprogramowania.

#### <a name="implement-just-in-time-access"></a>Zaimplementowanie dostępu just in time

Implementowanie *just-in-time* dostępu (JIT), aby dodatkowo zmniejszyć czas narażenia uprawnień. Użyj [usługi Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) do:

- Zapewnij użytkownikom uprawnień potrzebują tylko JIT.
- Przypisz role skróconą czasu trwania z pewnością automatycznie odwołać uprawnienia.

### <a name="require-re-authentication-for-important-transactions"></a>Konieczne jest ponowne uwierzytelnienie dla ważnych transakcji

[Fałszerstwo żądania międzywitrynowego](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (znany także jako *XSRF* lub *CSRF*) jest ataku na aplikacje hostowane w sieci web, w których aplikacja sieci web złośliwego wpływa na interakcję między przeglądarką klienta i sieci web Aplikacja, która ufa tej przeglądarki. Fałszerstwo żądania międzywitrynowego ataki są możliwe, ponieważ przeglądarki sieci web wysyłać niektóre typy tokenów uwierzytelniania automatycznie za pomocą każdego żądania do witryny sieci Web.
Ta forma wykorzystania jest także znana jako *ataku jednym kliknięciem* lub *sesji jazda* ponieważ ataku korzysta z zalet użytkownik wcześniej uwierzytelniona sesji.

Najlepszym sposobem obrony przed tego rodzaju atak jest poprosić użytkownika o coś, co przed każdej transakcji ważne, takie jak zakupu, dezaktywacji konta lub zmiana hasła można podać tylko użytkownika. Może poprosić użytkownika, aby ponownie wprowadzić swoje hasło, wykonaj captcha lub token wpisu tajnego, który tylko użytkownik musi przesłać. Najbardziej typowym podejściem jest token wpisu tajnego.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Zabezpieczanie kluczy, poświadczeń i innych wpisów tajnych przy użyciu rozwiązania zarządzania kluczami

Utraty kluczy i poświadczeń jest to powszechny problem. Jedyną czynnością co gorsza, nie tracąc kluczy i poświadczeń jest nieautoryzowana osoba uzyskać do nich dostęp. Osoby atakujące mogą korzystać z automatycznych i ręcznych techniki, aby znaleźć klucze i wpisy tajne, które są przechowywane w repozytoriach kodu, takich jak GitHub. Nie należy umieszczać kluczy i wpisów tajnych w tych repozytoriów publicznych, lub na innym serwerze.

Zawsze umieszczaj w rozwiązaniu do zarządzania kluczami klucze, certyfikaty, klucze tajne i parametry połączenia. Można użyć scentralizowanego rozwiązania, w którym klucze i wpisy tajne są przechowywane w sprzętowych modułach zabezpieczeń (HSM). Platforma Azure udostępnia w chmurze za pomocą modułu HSM [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).

Key Vault to *magazynu wpisów tajnych*: to usługa w chmurze scentralizowanego do przechowywania wpisów tajnych aplikacji. Usługa Key Vault chroni poufne dane przez przechowywanie wpisów tajnych aplikacji w jednym, centralnym miejscu i zapewnianie bezpiecznego dostępu, uprawnień kontroli i rejestrowania dostępu.

Klucze tajne są przechowywane w poszczególnych *magazynów*. Każdy magazyn ma swoją własną konfigurację i zasady zabezpieczeń w celu kontrolowania dostępu. Możesz uzyskać dostęp do danych za pośrednictwem interfejsu API REST lub klienckiego zestawu SDK, które są dostępne dla większości języków programowania.

> [!IMPORTANT]
> Usługa Azure Key Vault jest zaprojektowany do przechowywania tajemnic o konfiguracji dla aplikacji serwerowych. Nie jest przeznaczona do przechowywania danych, które należą do użytkowników aplikacji. Znajduje to odzwierciedlenie w jego cech wydajności interfejsu API i model kosztów.
>
> Dane użytkownika powinny znajdować się w innym miejscu, tak jak w wystąpieniu bazy danych SQL Azure, która ma przezroczyste szyfrowanie danych (TDE) lub na koncie magazynu, który używa usługi Azure Storage Service Encryption. Klucze tajne, które są używane przez aplikację, aby mieć dostęp do tych magazynów danych można przechowywać w usłudze Azure Key Vault.

### <a name="protect-sensitive-data"></a>Ochrona poufnych danych

Ochrona danych jest integralną część strategii zabezpieczeń.
Klasyfikowanie danych oraz identyfikowaniu ochronę danych wymaga ułatwia projektowanie aplikacji z myślą o bezpieczeństwie danych. Klasyfikowanie (kategoryzowanie) przechowywane dane według ważności i wpływ na działalność pomaga deweloperom określić ryzyko, które są skojarzone z danymi.

Podczas projektowania usługi formatów danych etykietą wrażliwych na wszystkich odpowiednich danych. Upewnij się, że aplikacji odpowiednie dane są traktowane jako poufne. Te zasady mogą pomóc w ochronie poufnych danych:

- Należy używać szyfrowania.
- Należy unikać niezmiennych wpisy tajne, takie jak klucze i hasła.
- Upewnij się, że mechanizmy kontroli dostępu i inspekcji zostały spełnione.

#### <a name="use-encryption"></a>Użyj szyfrowania

Ochrona danych powinien być integralną część strategii zabezpieczeń.
Jeśli dane są przechowywane w bazie danych lub przenosi i z powrotem między lokalizacjami, użyj szyfrowania [danych magazynowanych](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (podczas gdy w bazie danych) i szyfrowanie [przesyłane dane](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (na drodze do i z niego, Baza danych, interfejsu API lub punktu końcowego usługi). Firma Microsoft zaleca, zawsze używają protokołów SSL/TLS do wymiany danych. Upewnij się, że używasz najnowszej wersji protokołu TLS do szyfrowania (obecnie jest to wersja 1.2).

#### <a name="avoid-hard-coding"></a>Należy unikać niezmiennych

Kilka rzeczy, nigdy nie powinna być ustalone w oprogramowaniu. Niektóre przykłady są nazwy hostów lub adresów IP adresy, adresy URL, adresy e-mail, nazwy użytkowników, hasła, klucze konta magazynu i innych kluczy kryptograficznych. Rozważ zaimplementowanie wymagania co może lub nie może być ustalone w kodzie, w tym w sekcji komentarzy kodu.

Komentarze są umieszczane w kodzie, upewnij się, zaleca się zapisywania jakichkolwiek poufnych informacji. Obejmuje to adres e-mail, hasła, parametry połączenia, informacje o Twojej aplikacji, która może być znane tylko przez kogoś w organizacji oraz dowolne inne, mogą dać osobie atakującej korzyści w atakiem aplikacji lub organizacji .

Po prostu przyjęto założenie, że wszystko, co w projekcie rozwoju będą publicznych wiedzy po jego wdrożeniu. Należy unikać poufnych danych dowolnego rodzaju, w tym w projekcie.

Wcześniej Omówiliśmy [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Usługa Key Vault umożliwia przechowywanie wpisów tajnych, takich jak kluczy i haseł zamiast kodować je. Korzystając z usługi Key Vault w połączeniu z zarządzanych tożsamości dla zasobów platformy Azure, aplikacji internetowej platformy Azure dostęp wartości klucza tajnego konfiguracji łatwo i bezpiecznie bez przechowywania jakichkolwiek kluczy tajnych w kontroli źródła lub konfiguracji. Aby dowiedzieć się więcej, zobacz [zarządzania wpisami tajnymi w ramach aplikacji serwera przy użyciu usługi Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Zaimplementuj odporny na uszkodzenia miary

Aplikacja musi być w stanie obsłużyć [błędy](https://docs.microsoft.com/dotnet/standard/exceptions/) które występują podczas wykonywania w spójny sposób. Aplikację należy przechwytywać wszystkie błędy, a następnie przełączać bezpieczne lub zamknięte.

Należy również upewnić się, że błędy są rejestrowane przy użyciu wystarczający kontekst użytkownika, aby zidentyfikować podejrzane lub złośliwego działania. Powinny być przechowywane dzienniki wystarczającą ilość czasu zezwolić na opóźnione analizy śledczej. Dzienniki powinny być w formacie, który może być łatwo używane przez rozwiązania do zarządzania dziennikami. Upewnij się, że wyzwalane są alerty dotyczące błędów, które są związane z zabezpieczeniami. Za mało rejestrowania i monitorowania umożliwia dalsze atakują systemy i obsługa trwałości.

### <a name="take-advantage-of-error-and-exception-handling"></a>Skorzystaj z zalet obsługi błędów i wyjątków

Implementowanie poprawny błąd i [wyjątków](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) stanowi ważną część obrony kodowania. Obsługi błędów i wyjątków są krytyczne znaczenie systemu, niezawodną i bezpieczną. Błędy w obsłudze błędu może prowadzić do różnych rodzajów luk w zabezpieczeniach, takie jak przecieki informacji dla osób atakujących i pomagając osoby atakujące dowiedzieć się więcej o platformie i projektowania.

Upewnij się, że:

- Obsługa wyjątków w scentralizowany sposób, aby uniknąć zduplikowanych [bloki try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) w kodzie.

- Wszystkie nieoczekiwane zachowania są obsługiwane wewnątrz aplikacji.

- Wiadomości, które są widoczne dla użytkowników nie wycieku danych krytycznych, ale także zawierać informacje wystarczające do wyjaśnienia problem.

- Wyjątki są rejestrowane i zapewniają one wystarczającej ilości informacji o danych dowodowych i zdarzenie odpowiedzi dla zespołów służące do badania.

[Usługa Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) zapewnia najwyższej jakości środowisko do [Obsługa błędów i wyjątków](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) , są spowodowane przez systemów zależnych. Logic Apps umożliwia tworzenie przepływów pracy do automatyzacji zadań i procesów, które integrują aplikacje, dane, systemy i usługi dla przedsiębiorstw i organizacji.

### <a name="use-logging-and-alerting"></a>Rejestrowanie użycia i alerty

[Dziennik](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) bezpieczeństwa wysyła do badania zabezpieczeń i wyzwalania alertów dotyczących problemów, aby upewnić się, że ludzie wiedzieć o problemach w odpowiednim czasie. Włączanie inspekcji i rejestrowania do wszystkich składników. Dzienniki inspekcji należy przechwytywać kontekstu użytkownika i zidentyfikować wszystkie istotne zdarzenia.

Upewnij się, że nie Rejestruj wszystkie dane poufne, które użytkownik prześle do swojej witryny. Przykłady danych poufnych:

- Poświadczenia użytkownika
- Numery ubezpieczenia społecznego i inne informacje identyfikacyjne
- Numery kart kredytowych lub inne informacje finansowe
- Informacje o kondycji
- Klucze prywatne lub innych danych, który może służyć do odszyfrowania zaszyfrowanych informacji
- Informacje systemu lub aplikacji, który może służyć do bardziej efektywnego ataku aplikacji

Upewnij się, że aplikacja monitoruje zdarzeń zarządzania użytkownika, takich jak identyfikatory logowania użytkownika udane i nieudane, resetowania haseł, zmiany hasła, blokady konta i rejestracja użytkownika. Dla tych zdarzeń, ułatwia wykrywanie i reagowanie na potencjalnie podejrzanego zachowania. Umożliwia również zbieranie danych dotyczących operacji, takich jak kto uzyskuje dostęp do aplikacji.

## <a name="next-steps"></a>Następne kroki
W następujących artykułach firma Microsoft zaleca środki kontroli bezpieczeństwa i działań, które mogą pomóc Ci tworzenie i wdrażanie bezpiecznych aplikacji.

- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
- [Wdrażaj bezpieczne aplikacje](secure-deploy.md)

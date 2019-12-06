---
title: '& Oficjalne kontrolki przykładowe planów NHS w Wielkiej Brytanii'
description: Kontrolowanie mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS. Każda kontrolka jest zamapowana na co najmniej jedną zasadę platformy Azure, która pomaga w ocenie.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851370"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Kontrolowanie mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS w Wielkiej Brytanii

W poniższym artykule szczegółowo przedstawiono sposób mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS z brytyjskimi i BRYTYJSKImi formantami NHS. Aby uzyskać więcej informacji na temat kontrolek, zobacz [Zjednoczone Królestwo](https://www.gov.uk/government/publications/government-security-classifications).

Następujące mapowania są do **oficjalnych** i **brytyjskich NHS** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz **\[wersja Zapoznawcza\] Przeprowadź inspekcję oficjalnych i brytyjskich kontrolek NHS oraz Wdróż określone rozszerzenia maszyn wirtualnych, aby zapewnić obsługę zasad inspekcji** wbudowanej.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Data w ochronie tranzytowej

Plan pomaga zapewnić, że transfer informacji w ramach usług platformy Azure jest bezpieczny, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję niezabezpieczonych połączeń z kontami magazynu i Redis Cache.

- Powinny być włączone tylko bezpieczne połączenia z pamięcią podręczną Redis Cache
- Należy włączyć bezpieczny transfer na konta magazynu
- Pokaż wyniki inspekcji z serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Wdróż wymagania wstępne w celu inspekcji serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web
- Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji

## <a name="23-data-at-rest-protection"></a>2,3 danych w ramach ochrony REST

Ten plan pomaga wymusić zasady korzystania z formantów cryptograph, przypisując definicje [Azure Policy](../../../policy/overview.md) , które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych.
Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Zasady przypisane przez ten plan wymagają szyfrowania dla kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; Inspekcja braku szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych konta usługi Automation; Inspekcja niezabezpieczonych połączeń z kontami magazynu i Redis Cache; Inspekcja niesłabego szyfrowania hasła maszyny wirtualnej; i Przeprowadź inspekcję niezaszyfrowanej komunikacji Service Fabric.

- Należy zastosować szyfrowanie dysków na maszynach wirtualnych
- Zmienne konta usługi Automation powinny być szyfrowane
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- Należy włączyć Transparent Data Encryption baz danych SQL
- Wdróż przezroczyste szyfrowanie danych SQL DB
- Wymagaj szyfrowania na kontach Data Lake Store
- Dozwolone lokalizacje (zostały trwale zakodowane jako "Południowe Zjednoczone Królestwo" i "ZACHODNIe Zjednoczone Królestwo")
- Dozwolone lokalizacje dla grup zasobów (zostały trwale zakodowane jako "Południowe Zjednoczone Królestwo" i "ZACHODNIe Zjednoczone Królestwo")

## <a name="52-vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach 5,2

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , które monitorują brak programu Endpoint Protection, brakujące aktualizacje systemu operacyjnego, luki w zabezpieczeniach programu SQL i luki w zabezpieczeniach maszyn wirtualnych. Te szczegółowe dane zapewniają informacje o stanie zabezpieczeń wdrożonych zasobów i umożliwiają określanie priorytetów akcji korygowania.

- Monitoruj brakujący program Endpoint Protection w usłudze Azure Security Center
- Należy zainstalować aktualizacje systemu na maszynach
- Należy zainstalować aktualizacje systemu w zestawach skalowania maszyn wirtualnych
- Należy skorygować luki w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Ocena luk w zabezpieczeniach powinna być włączona na Twoich serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Należy skorygować luki w zabezpieczeniach w konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL
- Zaawansowane zabezpieczenia danych powinny być włączone na Twoich serwerach SQL

## <a name="53-protective-monitoring"></a>5,3 monitorowanie ochronne

Ten plan pomaga chronić zasoby systemu informacji przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które zapewniają ochronę przed nieograniczonym dostępem, zezwalanie na działanie listy i zagrożenia.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji
- Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii
- Należy włączyć usługę DDoS Protection w warstwie Standardowa
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Wdrażanie wykrywania zagrożeń na serwerach SQL
- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server

## <a name="9-secure-user-management"></a>9 Zarządzanie bezpiecznymi użytkownikami 

Platforma Azure implementuje funkcję kontroli dostępu opartej na rolach (RBAC), aby ułatwić zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan pomaga ograniczyć i kontrolować prawa dostępu, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub odczytu/zapisu i kontami z uprawnieniami właściciela, odczytu i/zapisu, które nie mają włączonej usługi uwierzytelniania wieloskładnikowego.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

## <a name="10-identity-and-authentication"></a>10 tożsamości i uwierzytelniania

Ten plan pomaga ograniczyć i kontrolować prawa dostępu, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub odczytu/zapisu i kontami z uprawnieniami właściciela, odczytu i/zapisu, które nie mają włączonej usługi uwierzytelniania wieloskładnikowego.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

Ten plan przypisuje definicje Azure Policy do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft.

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów

Ten plan przypisuje również definicje Azure Policy do kont inspekcji, dla których należy określić priorytety dla przeglądu, w tym konta z amortyzacją i konta zewnętrzne. W razie potrzeby konta mogą być blokowane przed zalogowaniem (lub usunięciem), które natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie Azure Policy definicje do inspekcji konta amortyzowanego, które należy wziąć pod uwagę podczas usuwania.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje również definicję Azure Policy, która przeprowadza inspekcję uprawnień pliku hasła maszyny wirtualnej systemu Linux do alertu, jeśli są ustawione nieprawidłowo. Ten projekt umożliwia podejmowanie działań naprawczych w celu zapewnienia, że uwierzytelniające nie zostały naruszone.

- Wersja zapoznawcza \[\]: Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które nie mają uprawnień do pliku haseł ustawione na 0644

Ten plan pomaga wymusić silne hasła, przypisując definicje Azure Policy, które umożliwiają inspekcję maszyn wirtualnych z systemem Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących hasła Świadomość maszyn wirtualnych w przypadku naruszenia zasad dotyczących siły haseł ułatwia podejmowanie działań naprawczych w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami.

- \[wersji zapoznawczej\]: Wdróż wymagania wstępne w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Podgląd\]: Wdróż wymagania wstępne w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- \[wersji zapoznawczej\]: Wdróż wymagania wstępne w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, których okres ważności hasła wynosi 1 dzień
- \[wersji zapoznawczej\]: Wdróż wymagania wstępne w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[wersji zapoznawczej\]: Wdróż wymagania wstępne w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które zezwalają na ponowne używanie poprzednich 24 haseł
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które zezwalają na ponowne używanie poprzednich 24 haseł

Ten plan pomaga również kontrolować dostęp do zasobów platformy Azure, przypisując definicje Azure Policy. Te zasady przeprowadzają inspekcję użycia typów zasobów i konfiguracji, które mogą zezwalać na dostęp do zasobów. Informacje o zasobach, które naruszają te zasady, mogą pomóc w podejmowaniu działań naprawczych w celu zapewnienia dostępu do zasobów platformy Azure tylko autoryzowanym użytkownikom.

- \[wersji zapoznawczej\]: Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Linux, które mają konta bez hasła
- \[wersji zapoznawczej\]: Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- \[Podgląd\]: Inspekcja maszyn wirtualnych z systemem Linux, które mają konta bez hasła
- \[Podgląd\]: Inspekcja maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Maszyny wirtualne powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager
- Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="11-external-interface-protection"></a>11 ochrona interfejsu zewnętrznego

W przypadku korzystania z więcej niż 25 zasad do odpowiedniego zarządzania bezpiecznymi użytkownikami ten plan pomaga chronić interfejsy usługi przed nieautoryzowanym dostępem, przypisując [Azure Policy](../../../policy/overview.md) definicję, która monitoruje nieograniczone konta magazynu. Konta magazynu z nieograniczonym dostępem mogą zezwalać na niezamierzony dostęp do informacji zawartych w systemie informacyjnym. Ten plan przypisuje również zasady, które umożliwiają adaptacyjne kontrole aplikacji na maszynach wirtualnych.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji
- Reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web w usłudze IaaS powinny być zaostrzone
- Dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony
- Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu powinny być zaostrzone
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web
- Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="12-secure-service-administration"></a>12 administrowanie bezpieczną usługą

Platforma Azure implementuje funkcję kontroli dostępu opartej na rolach (RBAC), aby ułatwić zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego przez przypisanie pięciu [Azure Policy](../../../policy/overview.md) definicji w celu inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub zapisu oraz kontami z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego.

Systemy używane do administrowania usługą w chmurze mają wysoce uprzywilejowany dostęp do tej usługi. Ich naruszenie miałoby znaczący wpływ, w tym środki do obejścia kontroli zabezpieczeń i kradzieży i manipulowania dużymi ilościami danych. Metody używane przez administratorów dostawcy usług do zarządzania usługą operacyjną powinny zostać zaprojektowane w celu ograniczenia ryzyka związanego z wykorzystaniem, które mogłyby nastąpić do naruszenia bezpieczeństwa usługi. Jeśli ta zasada nie jest zaimplementowana, osoba atakująca może mieć środki pozwalające ominąć kontrolę zabezpieczeń i wykraść lub manipulować dużymi ilościami danych.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje definicje Azure Policy do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft.

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów

Ten plan przypisuje również definicje Azure Policy do kont inspekcji, dla których należy określić priorytety dla przeglądu, w tym konta amortyzowane i konta zewnętrzne z podniesionymi uprawnieniami. W razie potrzeby konta mogą być blokowane przed zalogowaniem (lub usunięciem), które natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie Azure Policy definicje do inspekcji konta amortyzowanego, które należy wziąć pod uwagę podczas usuwania.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje również definicję Azure Policy, która przeprowadza inspekcję uprawnień pliku hasła maszyny wirtualnej systemu Linux do alertu, jeśli są ustawione nieprawidłowo. Ten projekt umożliwia podejmowanie działań naprawczych w celu zapewnienia, że uwierzytelniające nie zostały naruszone.

- \[Podgląd\]: Inspekcja pliku/etc/passwd maszyny wirtualnej z systemem Linux jest ustawiona na 0644

## <a name="13-audit-information-for-users"></a>13 informacji o inspekcji dla użytkowników

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Przypisane zasady również przeprowadzają inspekcję, jeśli maszyny wirtualne nie wysyłają dzienników do określonego obszaru roboczego usługi log Analytics.

- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server
- Przeprowadzanie inspekcji ustawienia diagnostyki
- \[Podgląd\]: Wdrażanie Log Analytics Agent na maszynach wirtualnych systemu Linux
- \[Podgląd\]: Wdrażanie Log Analytics Agent for Windows VMs
- Wdróż obserwatora sieciowego po utworzeniu sieci wirtualnych

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli w OFICJALNych i BRYTYJSKIch planach NHS, odwiedź następujące artykuły, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Plany NHS oficjalnego i Zjednoczonego Królestwa — omówienie](./index.md)
> [BRYTYJSKIch i BRYTYJSKIch planów NHSych](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

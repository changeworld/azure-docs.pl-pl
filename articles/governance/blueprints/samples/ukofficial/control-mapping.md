---
title: Przykład — oficjalne i BRYTYJSKIe plany NHS — Mapowanie formantów
description: Kontrolowanie mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7da31e09157b8877db4d36d0f061f29433d02d11
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515632"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Kontrolowanie mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS w Wielkiej Brytanii

W poniższym artykule szczegółowo przedstawiono sposób mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS z brytyjskimi i BRYTYJSKImi formantami NHS. Aby uzyskać więcej informacji na temat kontrolek, zobacz [Zjednoczone Królestwo](https://www.gov.uk/government/publications/government-security-classifications).

Następujące mapowania są do OFICJALNych i brytyjskich **NHS** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz  **\[kontrolę wersji zapoznawczej\] Zjednoczone oficjalne i Zjednoczone Królestwo NHS oraz Wdróż określone rozszerzenia maszyn wirtualnych, aby obsługiwać zasady inspekcji** wbudowanej inicjatywy.

## <a name="1-data-in-transit-protection"></a>1 Data w ochronie tranzytowej

Plan pomaga zapewnić, że transfer informacji w ramach usług platformy Azure jest bezpieczny, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję niezabezpieczonych połączeń z kontami magazynu i Redis Cache.

- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Należy włączyć bezpieczny transfer do kont magazynu

## <a name="23-data-at-rest-protection"></a>2,3 danych w ramach ochrony REST

Ten plan pomaga wymusić zasady korzystania z formantów cryptograph, przypisując definicje [Azure Policy](../../../policy/overview.md) , które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych.
Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Zasady przypisane przez ten plan wymagają szyfrowania dla kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; Inspekcja braku szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych konta usługi Automation; Inspekcja niezabezpieczonych połączeń z kontami magazynu i Redis Cache; Inspekcja niesłabego szyfrowania hasła maszyny wirtualnej; i Przeprowadź inspekcję niezaszyfrowanej komunikacji Service Fabric.

- Należy włączyć Transparent Data Encryption baz danych SQL
- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Zmienne konta usługi Automation powinny być szyfrowane
- Należy włączyć bezpieczny transfer do kont magazynu
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- Należy włączyć Transparent Data Encryption baz danych SQL
- Wdróż przezroczyste szyfrowanie danych usługi SQL DB
- Wymagaj szyfrowania na kontach Data Lake Store
- Dozwolone lokalizacje (zostały trwale zakodowane jako "Południowe Zjednoczone Królestwo" i "ZACHODNIe Zjednoczone Królestwo")
- Dozwolone lokalizacje dla grup zasobów (zostały trwale zakodowane jako "Południowe Zjednoczone Królestwo" i "ZACHODNIe Zjednoczone Królestwo")

## <a name="52-vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach 5,2

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacji przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , które monitorują brak programu Endpoint Protection, brakujące aktualizacje systemu operacyjnego, luki w zabezpieczeniach SQL i wirtualne luki w zabezpieczeniach komputera. Te szczegółowe dane zapewniają informacje o stanie zabezpieczeń wdrożonych zasobów i umożliwiają określanie priorytetów akcji korygowania.

- Monitoruj brakujący program Endpoint Protection w usłudze Azure Security Center
- Aktualizacje systemu powinny być zainstalowane na maszynach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="53-protective-monitoring"></a>5,3 monitorowanie ochronne

Ten plan pomaga chronić zasoby systemu informacji przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które zapewniają ochronę przed nieograniczonym dostępem, dozwolonych aktywność i zagrożeniami.

- Przeprowadź inspekcję nieograniczonego dostępu do kont magazynu
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji
- Wdróż wykrywanie zagrożeń na serwerach SQL
- Wdróż domyślne rozszerzenie programu Microsoft IaaSe chroniące przed złośliwym oprogramowaniem dla systemu Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 zarządzanie użytkownikami i tożsamość 10

Platforma Azure implementuje funkcję kontroli dostępu opartej na rolach (RBAC), aby ułatwić zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan pomaga ograniczyć i kontrolować prawa dostępu, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub odczytu/zapisu i kontami z uprawnieniami właściciela, odczytu i/zapisu, które nie mają wieloskładnikowego włączono uwierzytelnianie.

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

- \[Wersja\]zapoznawcza: Inspekcja pliku/etc/passwd maszyny wirtualnej z systemem Linux jest ustawiona na 0644

Ten plan pomaga wymusić silne hasła, przypisując definicje Azure Policy, które umożliwiają inspekcję maszyn wirtualnych z systemem Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących hasła Świadomość maszyn wirtualnych w przypadku naruszenia zasad dotyczących siły haseł ułatwia podejmowanie działań naprawczych w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami.

- \[Wersja\]zapoznawcza: Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Wersja\]zapoznawcza: Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- \[Wersja\]zapoznawcza: Wdróż wymagania w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- \[Wersja\]zapoznawcza: Wdróż wymagania w celu inspekcji maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Wersja\]zapoznawcza: Wdróż wymagania w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł
- \[Wersja\]zapoznawcza: Inspekcja maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Wersja\]zapoznawcza: Inspekcja maszyn wirtualnych z systemem Windows bez maksymalnego wieku hasła 70 dni
- \[Wersja\]zapoznawcza: Inspekcja maszyn wirtualnych z systemem Windows bez minimalnego wieku hasła wynoszącego 1 dzień
- \[Wersja\]zapoznawcza: Inspekcja maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Wersja\]zapoznawcza: Inspekcja maszyn wirtualnych z systemem Windows, które zezwalają na ponowne używanie poprzednich 24 haseł

Ten plan pomaga również kontrolować dostęp do zasobów platformy Azure, przypisując definicje Azure Policy. Te zasady przeprowadzają inspekcję użycia typów zasobów i konfiguracji, które mogą zezwalać na dostęp do zasobów. Informacje o zasobach, które naruszają te zasady, mogą pomóc w podejmowaniu działań naprawczych w celu zapewnienia dostępu do zasobów platformy Azure tylko autoryzowanym użytkownikom.

- \[Wersja\]zapoznawcza: Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które mają konta bez hasła
- \[Wersja\]zapoznawcza: Wdróż wymagania, aby przeprowadzić inspekcję maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- \[Wersja\]zapoznawcza: Inspekcja maszyn wirtualnych z systemem Linux, które mają konta bez hasła
- \[Wersja\]zapoznawcza: Inspekcja maszyn wirtualnych z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager
- Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="11-external-interface-protection"></a>11 ochrona interfejsu zewnętrznego

W przypadku korzystania z więcej niż 25 zasad do odpowiedniego zarządzania bezpiecznymi użytkownikami ten plan pomaga chronić interfejsy usługi przed nieautoryzowanym dostępem, przypisując [Azure Policy](../../../policy/overview.md) definicję, która monitoruje nieograniczone konta magazynu. Konta magazynu z nieograniczonym dostępem mogą zezwalać na niezamierzony dostęp do informacji zawartych w systemie informacyjnym. Ten plan przypisuje również zasady, które umożliwiają adaptacyjne kontrole aplikacji na maszynach wirtualnych.

- Przeprowadź inspekcję nieograniczonego dostępu do kont magazynu
- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="12-secure-service-administration"></a>12 administrowanie bezpieczną usługą

Platforma Azure implementuje funkcję kontroli dostępu opartej na rolach (RBAC), aby ułatwić zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego przez przypisanie pięciu [Azure Policy](../../../policy/overview.md) definicji w celu inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub zapisu oraz kontami z uprawnieniami właściciela i/lub zapisu, które nie mają włączono uwierzytelnianie wieloskładnikowe.

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

- \[Wersja\]zapoznawcza: Inspekcja pliku/etc/passwd maszyny wirtualnej z systemem Linux jest ustawiona na 0644

## <a name="13-audit-information-for-users"></a>13 informacji o inspekcji dla użytkowników

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Przypisane zasady również przeprowadzają inspekcję, jeśli maszyny wirtualne nie wysyłają dzienników do określonego obszaru roboczego usługi log Analytics.

- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server
- Przeprowadź inspekcję ustawienia diagnostyki
- Przeprowadź inspekcję ustawień inspekcji SQL na poziomie serwera
- \[Wersja\]zapoznawcza: Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux
- \[Wersja\]zapoznawcza: Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows
- Wdrażaj zasób Network Watcher podczas tworzenia sieci wirtualnych

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli w OFICJALNych i BRYTYJSKIch planach NHS, odwiedź następujące artykuły, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Plany NHS oficjalnego i Zjednoczonego Królestwa — Omówienie](./index.md)
> [planów NHSych w Wielkiej Brytanii i brytyjskich](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

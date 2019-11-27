---
title: Przykładowe kontrolki ISO 27001
description: Sterowanie mapowaniem przykładu planu ISO 27001. Każda kontrolka jest zamapowana na co najmniej jedną zasadę platformy Azure, która pomaga w ocenie.
ms.date: 07/22/2019
ms.topic: sample
ms.openlocfilehash: effc68f3b7daa576b855fb61b39c1e47929a614e
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546672"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>Sterowanie mapowaniem przykładu planu ISO 27001

W tym artykule szczegółowo opisano, jak przykładowy plan ISO 27001 strategii platformy Azure jest mapowany na kontrolki ISO 27001. Aby uzyskać więcej informacji na temat kontrolek, zobacz [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Następujące mapowania znajdują się w kontrolkach **ISO 27001:2013** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz **\[Podgląd,\] inspekcji ISO 27001:2013 kontrolki i Wdróż określone rozszerzenia maszyn wirtualnych, aby obsługiwać zasady inspekcji** wbudowanej inicjatywy.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 Rozdzielenie obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie pozwala na nadmiarowość administracyjną. Bez względu na to, że zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć prawdopodobieństwo naruszenia za pośrednictwem konta właściciela z naruszeniem. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując dwie [Azure Policy](../../../policy/overview.md) definicje, które przeprowadzą inspekcję liczby właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji może pomóc w zaimplementowaniu odpowiedniego rozdzielenia obowiązków.

- Wersja zapoznawcza \[\]: Inspekcja minimalnej liczby właścicieli subskrypcji
- Wersja zapoznawcza \[\]: Przeprowadź inspekcję maksymalnej liczby właścicieli dla subskrypcji

## <a name="a821-classification-of-information"></a>A. 8.2.1 — Klasyfikacja informacji

[Usługa oceny luk w zabezpieczeniach](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) w systemie Azure może ułatwić odnajdywanie poufnych danych przechowywanych w bazach danych i zawiera zalecenia dotyczące klasyfikowania tych danych. Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , aby przeprowadzić inspekcję luk w zabezpieczeniach podczas skanowania za pośrednictwem programu SQL Server.

- Wersja zapoznawcza \[\]: monitoruj wyniki oceny luk w zabezpieczeniach SQL w Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2 dostęp do sieci i usług sieciowych

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC) w celu zarządzania dostępem do zasobów platformy Azure. Ten plan ułatwia kontrolowanie dostępu do zasobów platformy Azure przez przypisanie siedmiu [Azure Policy](../../../policy/overview.md) definicji. Te zasady przeprowadzają inspekcję użycia typów zasobów i konfiguracji, które mogą zezwalać na dostęp do zasobów.
Informacje o zasobach, które naruszają te zasady, mogą pomóc w podejmowaniu działań naprawczych w celu zapewnienia dostępu do zasobów platformy Azure tylko autoryzowanym użytkownikom.

- \[wersji zapoznawczej\]: Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji kont maszyn wirtualnych z systemem Linux bez hasła
- \[wersji zapoznawczej\]: Wdrażanie rozszerzenia maszyny wirtualnej w celu inspekcji maszyny wirtualnej z systemem Linux zezwalającej na połączenia zdalne z kont bez hasła
- \[Podgląd\]: Inspekcja kont maszyn wirtualnych z systemem Linux bez haseł
- \[Podgląd\]: Inspekcja maszyny wirtualnej z systemem Linux zezwalająca na połączenia zdalne z kont bez hasła
- Inspekcja użycia klasycznych kont magazynu
- Inspekcja użycia klasycznych maszyn wirtualnych
- Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 Zarządzanie prawami dostępu uprzywilejowanego

Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując cztery [Azure Policy](../../../policy/overview.md) definicje do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub zapisu oraz konta z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Platforma Azure implementuje funkcję kontroli dostępu opartej na rolach (RBAC) w celu zarządzania dostępem do zasobów platformy Azure. Ten plan przypisuje również trzy definicje Azure Policy do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. Ten plan przypisuje również definicję Azure Policy do inspekcji użycia niestandardowych reguł RBAC. Zrozumienie, gdzie są zaimplementowane niestandardowe reguły RBAC, może pomóc w sprawdzeniu potrzeby i właściwej implementacji, ponieważ niestandardowe reguły RBAC są podatne na błędy.

- \[Podgląd\]: konta inspekcji z uprawnieniami właściciela, dla których nie włączono uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Podgląd\]: konta inspekcji z uprawnieniami do zapisu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Podgląd\]: Inspekcja kont zewnętrznych z uprawnieniami właściciela w ramach subskrypcji
- \[Podgląd\]: Inspekcja kont zewnętrznych z uprawnieniami do zapisu w ramach subskrypcji
- Inspekcja aprowizacji Azure Active Directory administratora programu SQL Server
- Inspekcja użycia Azure Active Directory na potrzeby uwierzytelniania klientów w programie Service Fabric
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4 zarządzanie tajnymi informacjami o uwierzytelnianiu użytkowników

Ten plan przypisuje trzy definicje [Azure Policy](../../../policy/overview.md) do kont inspekcji, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga zapewnić bezpieczeństwo kont nawet w przypadku naruszenia zabezpieczeń jednego z informacji o uwierzytelnianiu. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone. Ten plan również przypisuje dwa definicje Azure Policy, które przeprowadzą inspekcję uprawnień pliku hasła maszyny wirtualnej z systemem Linux w celu alertu, jeśli są ustawione nieprawidłowo. Ta konfiguracja umożliwia podejmowanie działań naprawczych w celu zapewnienia, że uwierzytelniające nie zostały naruszone.

- \[Podgląd\]: konta inspekcji z uprawnieniami właściciela, dla których nie włączono uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Podgląd\]: konta inspekcji z uprawnieniami do odczytu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Podgląd\]: konta inspekcji z uprawnieniami do zapisu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[wersji zapoznawczej\]: Wdróż rozszerzenie maszyny wirtualnej w celu inspekcji uprawnień pliku haseł maszyny wirtualnej systemu Linux
- \[Podgląd\]: Inspekcja pliku/etc/passwd maszyny wirtualnej z systemem Linux jest ustawiona na 0644

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 Przegląd praw dostępu użytkownika

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), która ułatwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje cztery [Azure Policy](../../../policy/overview.md) definicje do kont inspekcji, dla których należy określić priorytety dla przeglądu, w tym konta z amortyzacją i konta zewnętrzne z podniesionymi uprawnieniami.

- \[Podgląd\]: Inspekcja przestarzałych kont w subskrypcji
- \[Podgląd\]: Inspekcja przestarzałych kont z uprawnieniami właściciela w ramach subskrypcji
- \[Podgląd\]: Inspekcja kont zewnętrznych z uprawnieniami właściciela w ramach subskrypcji
- \[Podgląd\]: Inspekcja kont zewnętrznych z uprawnieniami do zapisu w ramach subskrypcji

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 usunięcie lub dostosowanie praw dostępu

Platforma Azure implementuje funkcję [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), która ułatwia zarządzanie dostępem do zasobów na platformie Azure. Korzystając z [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) i RBAC, można aktualizować role użytkowników, aby odzwierciedlały zmiany organizacyjne. W razie potrzeby konta mogą być blokowane przed zalogowaniem (lub usunięciem), które natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie [Azure Policy](../../../policy/overview.md) definicje do inspekcji konta amortyzowanego, które należy wziąć pod uwagę podczas usuwania.

- \[Podgląd\]: Inspekcja przestarzałych kont w subskrypcji
- \[Podgląd\]: Inspekcja przestarzałych kont z uprawnieniami właściciela w ramach subskrypcji

## <a name="a942-secure-log-on-procedures"></a>A. 9.4.2 bezpieczne procedury logowania

Ten plan przypisuje trzy definicje Azure Policy do kont inspekcji, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Usługa Azure Multi-Factor Authentication zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie. Monitorowanie kont bez włączonej usługi uwierzytelnianie wieloskładnikowe umożliwia zidentyfikowanie kont, które mogą być bardziej zagrożone.

- \[Podgląd\]: konta inspekcji z uprawnieniami właściciela, dla których nie włączono uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Podgląd\]: konta inspekcji z uprawnieniami do odczytu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji
- \[Podgląd\]: konta inspekcji z uprawnieniami do zapisu, które nie obsługują uwierzytelniania wieloskładnikowego w ramach subskrypcji

## <a name="a943-password-management-system"></a>System zarządzania hasłami. 9.4.3

Ten plan pomaga wymusić silne hasła, przypisując 10 [Azure Policy](../../../policy/overview.md) definicji, które będą przeprowadzać inspekcję maszyn wirtualnych z systemem Windows, które nie wymuszają minimalnej siły i innych wymagań Świadomość maszyn wirtualnych w przypadku naruszenia zasad dotyczących siły haseł ułatwia podejmowanie działań naprawczych w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami.

- \[podglądu\]: Wdrażanie rozszerzenia maszyny wirtualnej w celu przeprowadzenia inspekcji maszyny wirtualnej z systemem Windows wymusza wymagania dotyczące złożoności haseł
- \[podglądu\]: Wdróż rozszerzenie maszyny wirtualnej w celu przeprowadzenia inspekcji maksymalnego wieku hasła dla maszyny wirtualnej z systemem Windows 70 dni
- Wersja zapoznawcza \[\]: Wdróż rozszerzenie maszyny wirtualnej w celu przeprowadzenia inspekcji dla maszyny wirtualnej z systemem Windows minimalne przedawnienie hasła
- \[podglądu\]: Wdrażanie rozszerzenia maszyny wirtualnej w celu przeprowadzenia inspekcji haseł maszyn wirtualnych z systemem Windows musi wynosić co najmniej 14 znaków
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny wirtualnej w celu przeprowadzenia inspekcji maszyny wirtualnej z systemem Windows nie powinno zezwalać na poprzednie 24 hasła
- \[podglądu\]: Inspekcja maszyny wirtualnej z systemem Windows wymusza wymagania dotyczące złożoności hasła
- Wersja zapoznawcza \[\]: okres ważności hasła maszyny wirtualnej z systemem Windows 70 dni
- Wersja zapoznawcza \[\]: okres ważności hasła dla maszyny wirtualnej z systemem Windows
- Wersja zapoznawcza \[\]: Inspekcja haseł maszyn wirtualnych z systemem Windows musi wynosić co najmniej 14 znaków
- Wersja zapoznawcza \[\]: Inspekcja maszyny wirtualnej z systemem Windows nie powinna zezwalać na poprzednie 24 hasła

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A. 10.1.1 zasady korzystania z formantów kryptograficznych

Ten plan pomaga wymusić zasady korzystania z kontrolek cryptograph przez przypisanie 13 [Azure Policy](../../../policy/overview.md) definicji, które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych.
Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Zasady przypisane przez ten plan wymagają szyfrowania dla kont usługi BLOB Storage i kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; Inspekcja braku szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych konta usługi Automation; Inspekcja niezabezpieczonych połączeń z kontami magazynu, aplikacjami funkcji, aplikacjami sieci Web, API Apps i Redis Cache; Inspekcja niesłabego szyfrowania hasła maszyny wirtualnej; i Przeprowadź inspekcję niezaszyfrowanej komunikacji Service Fabric.

- Wersja zapoznawcza \[\]: Przeprowadź inspekcję dostępu tylko do protokołu HTTPS dla aplikacja funkcji
- \[Podgląd\]: Inspekcja dostępu tylko do protokołu HTTPS dla aplikacji sieci Web
- Wersja zapoznawcza \[\]: Przeprowadź inspekcję dostępu tylko do protokołu HTTPS dla aplikacji interfejsu API
- Podgląd \[\]: Inspekcja braku szyfrowania obiektów BLOB dla kont magazynu
- \[podglądu\]: Wdrażanie rozszerzenia maszyny wirtualnej w celu przeprowadzenia inspekcji maszyny wirtualnej z systemem Windows nie powinno przechowywać haseł przy użyciu szyfrowania odwracalnego
- Podgląd \[\]: Inspekcja maszyny wirtualnej z systemem Windows nie powinna przechowywać haseł przy użyciu szyfrowania odwracalnego
- \[podglądu\]: Monitoruj nieszyfrowane dyski maszyn wirtualnych w Azure Security Center
- Inspekcja włączenia szyfrowania zmiennych konta usługi Automation
- Inspekcja włączania tylko bezpiecznych połączeń z Redis Cache
- Inspekcja bezpiecznego transferu na konta magazynu
- Przeprowadź inspekcję ustawienia właściwości ClusterProtectionLevel na EncryptAndSign w Service Fabric
- Inspekcja stanu przezroczystego szyfrowania danych
- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="a1241-event-logging"></a>A. 12.4.1 Rejestrowanie zdarzeń

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure.
Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane w ramach zasobów platformy Azure.

- \[wersji zapoznawczej\]: Inspekcja wdrożenia Agent zależności — nielista obrazów maszyn wirtualnych
- \[wersji zapoznawczej\]: monitorowanie Agent zależności wdrożenia w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[wersji zapoznawczej\]: Inspekcja wdrożenia agenta Log Analytics — nielista obrazów maszyn wirtualnych
- Wersja zapoznawcza \[\]: Przeprowadź inspekcję wdrożenia agenta Log Analytics w VMSS-VM Image (OS) na liście
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3 dzienniki administratorów i operatorów

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu Azure Policy definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane w ramach zasobów platformy Azure.

- \[wersji zapoznawczej\]: Inspekcja wdrożenia Agent zależności — nielista obrazów maszyn wirtualnych
- \[wersji zapoznawczej\]: monitorowanie Agent zależności wdrożenia w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[wersji zapoznawczej\]: Inspekcja wdrożenia agenta Log Analytics — nielista obrazów maszyn wirtualnych
- Wersja zapoznawcza \[\]: Przeprowadź inspekcję wdrożenia agenta Log Analytics w VMSS-VM Image (OS) na liście
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server

## <a name="a1244-clock-synchronization"></a>Synchronizacja zegara. 12.4.4

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu Azure Policy definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure. Dzienniki systemu Azure są oparte na zsynchronizowaniu zegarów wewnętrznych w celu utworzenia skorelowanego rekordu zdarzeń między zasobami.

- \[wersji zapoznawczej\]: Inspekcja wdrożenia Agent zależności — nielista obrazów maszyn wirtualnych
- \[wersji zapoznawczej\]: monitorowanie Agent zależności wdrożenia w programie VMSS — lista obrazów maszyn wirtualnych (OS) nie została wystawiona
- \[wersji zapoznawczej\]: Inspekcja wdrożenia agenta Log Analytics — nielista obrazów maszyn wirtualnych
- Wersja zapoznawcza \[\]: Przeprowadź inspekcję wdrożenia agenta Log Analytics w VMSS-VM Image (OS) na liście
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 Instalacja oprogramowania w systemach operacyjnych

Adaptacyjna kontrola aplikacji to rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję Azure Policy, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ta funkcja ułatwia sterowanie instalacją oprogramowania i aplikacji na maszynach wirtualnych platformy Azure.

- \[wersji zapoznawczej\]: monitorowanie możliwej listy dozwolonych aplikacji w Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 Zarządzanie lukami w zabezpieczeniach

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego przez przypisanie pięciu [Azure Policy](../../../policy/overview.md) definicji, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego i luki w zabezpieczeniach programu SQL Server w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym.

- Wersja zapoznawcza \[\]: Monitoruj brakujące Endpoint Protection w Azure Security Center
- \[podglądu\]: Monitoruj brakujące aktualizacje systemu w Azure Security Center
- \[wersji zapoznawczej\]: monitorowanie luk w zabezpieczeniach systemu operacyjnego w Azure Security Center
- Wersja zapoznawcza \[\]: monitoruj wyniki oceny luk w zabezpieczeniach SQL w Azure Security Center
- Wersja zapoznawcza \[\]: Monitoruj luki w zabezpieczeniach maszyn wirtualnych w Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A. 12.6.2 ograniczenia dotyczące instalacji oprogramowania

Adaptacyjna kontrola aplikacji to rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję Azure Policy, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ograniczenia dotyczące instalacji oprogramowania mogą pomóc w zmniejszeniu prawdopodobieństwa wprowadzenia luk w zabezpieczeniach oprogramowania.

- \[wersji zapoznawczej\]: monitorowanie możliwej listy dozwolonych aplikacji w Azure Security Center

## <a name="a1311-network-controls"></a>A. 13.1.1, kontrolki sieci

Ten plan ułatwia zarządzanie sieciami i sterowanie nimi przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , która monitoruje sieciowe grupy zabezpieczeń z regułami ograniczającymi. Reguły, które są zbyt ograniczane, mogą zezwalać na niezamierzony dostęp do sieci i powinny być przeglądane. Ten plan przypisuje również trzy definicje Azure Policy, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, i konta magazynu z nieograniczonym dostępem mogą zezwalać na nieograniczony dostęp do informacji zawartych w systemie informacyjnym.

- Wersja zapoznawcza \[\]: Monitoruj dostęp do sieci w Azure Security Center
- \[Podgląd\]: Monitoruj niechronione punkty końcowe sieci w Azure Security Center
- \[Podgląd\]: Monitoruj niechronione aplikacje sieci Web w Azure Security Center
- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1 — zasady i procedury transferu informacji

Plan pomaga zapewnić, że transfer informacji w ramach usług platformy Azure jest bezpieczny, przypisując dwie [Azure Policy](../../../policy/overview.md) definicje do inspekcji niezabezpieczonych połączeń z kontami magazynu i Redis Cache.

- Inspekcja włączania tylko bezpiecznych połączeń z Redis Cache
- Inspekcja bezpiecznego transferu na konta magazynu

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli planu ISO 27001 zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o architekturze i sposobie wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Plany iso 27001 — omówienie](./index.md)
> [ISO 27001 plan — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
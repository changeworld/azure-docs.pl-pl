---
title: Przykładowe mapowanie kontrolek — oficjalne UK-plan-
description: Mapowanie kontrolek UK OFICJALNĄ próbkę planu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2eb6e62bc891a147a89107f5e3de7c2b605bbd09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276979"
---
# <a name="control-mapping-of-the-uk-official-blueprint-sample"></a>Mapowanie kontrolek oficjalne UK próbki planu

Poniższy artykuł szczegółowo opisuje sposób mapowania oficjalnym Zjednoczone Królestwo Wielkiej Brytanii OFICJALNĄ próbkę planu.
Aby uzyskać więcej informacji na temat formantów, zobacz [oficjalne UK](https://www.gov.uk/government/publications/government-security-classifications).

Następujące mapowania są **oficjalne UK** kontrolki. Korzystanie z nawigacji po prawej stronie umożliwia przejście bezpośrednio do mapowania określonego formantu. Wiele kontrolek zamapowanego są implementowane za pomocą [usługi Azure Policy](../../../policy/overview.md) inicjatywy. Aby zapoznać się z pełną inicjatywy, otwórz **zasad** w witrynie Azure portal i wybierz pozycję **definicje** strony. Następnie znajdź i zaznacz  **[Wersja zapoznawcza] inspekcji oficjalne UK kontroluje i wdrażanie określonych rozszerzeń maszyn wirtualnych w celu spełnienia wymagań inspekcji** inicjatywa zasad wbudowanych.

## <a name="1-data-in-transit-protection"></a>1 data Protection przesyłania

Planu pomaga zagwarantować, przekazywania informacji z usługami platformy Azure jest bezpieczne, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji niezabezpieczonego połączenia konta usługi storage i Redis Cache.

- Powinien być włączony tylko bezpiecznych połączeń z usługą Redis Cache
- Bezpieczne przesyłanie danych do konta magazynu powinna być włączona.

## <a name="23-data-at-rest-protection"></a>2.3 dane ochrony rest

Ten plan ułatwia wymuszanie zasad dotyczących używania kontrolek cryptograph, przypisując 11 [usługi Azure Policy](../../../policy/overview.md) definicje, które wymuszają określonych cryptograph kontroli i inspekcji użytkowania słabe ustawień kryptograficznych.
Zrozumienie, w której zasoby platformy Azure może mieć optymalnej konfiguracji kryptograficznych mogą pomóc podjąć działania naprawcze, aby upewnić się, że zasoby są skonfigurowane zgodnie z zasadami bezpieczeństwa informacji. W szczególności zasady przypisane przez ten plan wymagane szyfrowanie dla kont usługi data lake storage; wymagają technologii transparent data encryption w bazach danych SQL; przeprowadzaj inspekcję brakującego szyfrowania na konta magazynu, baz danych SQL, dyski maszyny wirtualnej i zmienne konta usługi automation; Inspekcja niezabezpieczonego połączenia konta usługi storage i Redis Cache; przeprowadzaj inspekcję szyfrowania hasła słabe maszyny wirtualnej; i inspekcji nieszyfrowana komunikacja usługi Service Fabric.

- Monitoruj niezaszyfrowane bazy danych SQL w usłudze Azure Security Center
- Szyfrowanie dysków powinny być stosowane w przypadku maszyn wirtualnych
- Zmienne konta usługi Automation, które mają być szyfrowane
- Bezpieczne przesyłanie danych do konta magazynu powinna być włączona.
- Bezpieczne przesyłanie danych do konta magazynu powinna być włączona.
- Klastry usługi Service Fabric powinien mieć ustawioną właściwość ClusterProtectionLevel do EncryptAndSign
- Transparent Data Encryption w bazach danych SQL powinno być włączone.
- Wdrażanie bazy danych SQL przezroczyste szyfrowanie danych
- Wymagaj szyfrowania konta Data Lake Store
- Dozwolone lokalizacje (ostatecznej kodowanego "POŁUDNIOWE Zjednoczone Królestwo" i "Zachodnie Zjednoczone Królestwo")
- Dozwolone lokalizacje dla grup zasobów (ostatecznej kodowanego "POŁUDNIOWE Zjednoczone Królestwo" i "Zachodnie Zjednoczone Królestwo")

## <a name="52-vulnerability-management"></a>5.2 Zarządzanie lukami w zabezpieczeniach

Ten plan pomaga w zarządzaniu informacje dotyczą luk w zabezpieczeniach, przypisując pięciu [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują brakujący program endpoint protection, Brak aktualizacji systemu operacyjnego luki w zabezpieczeniach systemu, SQL luki w zabezpieczeniach, a maszyna wirtualna luk w zabezpieczeniach. Wglądowi w szczegółowe dane zawierają w czasie rzeczywistym informacje o stanie zabezpieczeń zasobów wdrożonych i może pomóc określić priorytety działania korygujące.

- Monitorowanie brakującej ochrony punktów końcowych w usłudze Azure Security Center
- Aktualizacje systemu powinien być zainstalowany na komputerach
- Powinny zostać skorygowane luk w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach
- Powinny zostać skorygowane luk w zabezpieczeniach w bazach danych SQL
- Luki w zabezpieczeniach powinna zostać skorygowane przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="53-protective-monitoring"></a>5.3 ochronne monitorowania

Ten plan pomaga w ochronie informacji o systemie zasoby, przypisując [usługi Azure Policy](../../../policy/overview.md) definicji, który monitoruje nieograniczone kont magazynu. Ten plan przypisuje definicja zasad platformy Azure, która monitoruje aktywność listy dozwolonych adresów.

- Przeprowadź inspekcję dostępu do sieci bez ograniczeń do kont magazynu
- Adaptacyjne kontrole aplikacji powinno być włączone na maszynach wirtualnych
- Wdrażanie wykrywanie zagrożeń na serwerach SQL
- Wdróż domyślne rozszerzenie Microsoft IaaS chroniące przed złośliwym kodem dla systemu Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 zabezpieczyć Zarządzanie użytkownikami / 10 Uwierzytelnianie i tożsamość

Azure implementuje kontroli dostępu opartej na rolach (RBAC) do pomaga, którymi zarządzasz, kto ma dostęp do zasobów na platformie Azure. W witrynie Azure portal, możesz sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnienia. Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując sześć [usługi Azure Policy](../../../policy/overview.md) definicji w celu inspekcji konta zewnętrzne z właścicielem i/lub odczytu/zapisu, uprawnień i kont z właścicielem, odczytu lub zapisu uprawnienia, które nie mają włączonego uwierzytelniania wieloskładnikowego.

- Uwierzytelnianie wieloskładnikowe powinno być włączone dla kont z uprawnieniami właściciela subskrypcji
- Uwierzytelnianie wieloskładnikowe powinny być włączone kont z uprawnieniami do zapisu w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone dla kont z uprawnieniami do odczytu w ramach subskrypcji
- Przeprowadź inspekcję konta zewnętrzne z uprawnieniami właściciela na subskrypcji
- Inspekcja kont zewnętrznych z uprawnieniami do zapisu w subskrypcji
- Przeprowadź inspekcję konta zewnętrzne z uprawnieniami do odczytu w ramach subskrypcji

Ten plan przypisuje dwie definicje usługi Azure Policy do przeprowadzania inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL i usługi Service Fabric. Za pomocą usługi Azure Active Directory uwierzytelniania umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft.

- Administrator usługi Azure Active Directory powinny zostać aprowizowane dla serwerów SQL
- Klastry usługi Service Fabric do uwierzytelniania klientów należy używać tylko usługi Azure Active Directory

Ten plan przypisuje także pięć definicji zasad platformy Azure do inspekcji kont, które powinny mieć priorytet dla przeglądu, w tym zamortyzowany kont i zewnętrznych. W razie potrzeby, kont można logowanie zablokowane (lub usunąć), które natychmiast powoduje usunięcie prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie definicje usługi Azure Policy konta amortyzowany inspekcji, które mają być uwzględniane w usuwania.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Zewnętrzne konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje definicja zasad platformy Azure, która przeprowadza inspekcję maszyn wirtualnych systemu Linux hasło pliku uprawnień do wyzwalania alertu, jeżeli są ustawione nieprawidłowo. Ten projekt umożliwia podejmowanie działań naprawczych, aby upewnić się, że nie są zagrożone wystawców uwierzytelnienia.

- [Wersja zapoznawcza]: Audit Linux VM /etc/passwd file permissions are set to 0644

Ten plan pomaga można wymuszać stosowanie silnych haseł, przypisując 10 definicji zasad platformy Azure, które Przeprowadź inspekcję maszyn wirtualnych Windows, które nie wymuszają minimalne i inne wymagania dotyczące hasła. Rozpoznawanie maszyn wirtualnych z naruszeniem zasad siły haseł pomaga podjąć działania naprawcze, aby upewnić się, że zgodne z zasadami haseł dla wszystkich kont użytkowników maszyny Wirtualnej.

- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [Wersja zapoznawcza]: Audit Windows VMs that do not have the password complexity setting enabled
- [Wersja zapoznawcza]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Wersja zapoznawcza]: Audit Windows VMs that do not have a minimum password age of 1 day
- [Wersja zapoznawcza]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Wersja zapoznawcza]: Audit Windows VMs that allow re-use of the previous 24 passwords

Również planu ułatwia kontrolowanie dostępu do zasobów platformy Azure, przypisując siedem definicji zasad platformy Azure. Te zasady przeprowadzania inspekcji użycia typów zasobów i konfiguracje, które mogą umożliwić mniej ograniczające dostęp do zasobów. Zasoby zrozumienia, naruszających może te zasady, pomoc podjąć działania naprawcze, aby zapewnić dostęp do zasobów platformy Azure jest ograniczone do autoryzowanych użytkowników.

- [Wersja zapoznawcza]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [Wersja zapoznawcza]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [Wersja zapoznawcza]: Audit Linux VMs that have accounts without passwords
- [Wersja zapoznawcza]: Audit Linux VMs that allow remote connections from accounts without passwords
- Konta magazynu powinny być migrowane do nowych zasobów usługi Azure Resource Manager
- Maszyny wirtualne powinny być migrowane do nowych zasobów usługi Azure Resource Manager
- Przeprowadzaj inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="11-external-interface-protection"></a>Ochrona 11 interfejs zewnętrzny

Inne niż odpowiedniego użytkownika bezpiecznego zarządzania, korzystając z ponad 25 zasad, ten plan pomaga w ochronie interfejsy usługi przed nieautoryzowanym dostępem, przypisując [usługi Azure Policy](../../../policy/overview.md) definicję, która monitoruje bez ograniczeń konta magazynu. Konta magazynu z nieograniczonym dostępem można zezwolić niekontrolowanego dostępu do informacji zawartych w systemie informacji. Ten plan przypisuje także zasady, które umożliwia adaptacyjne kontrole aplikacji na maszynach wirtualnych.

- Przeprowadź inspekcję dostępu do sieci bez ograniczeń do kont magazynu
- Adaptacyjne kontrole aplikacji powinno być włączone na maszynach wirtualnych

## <a name="12-secure-service-administration"></a>12 Usługa bezpiecznego administrowania

Azure implementuje kontroli dostępu opartej na rolach (RBAC) do pomaga, którymi zarządzasz, kto ma dostęp do zasobów na platformie Azure. W witrynie Azure portal, możesz sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnienia. Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując pięciu [usługi Azure Policy](../../../policy/overview.md) definicji w celu inspekcji konta zewnętrzne z właścicielem i/lub zapisać uprawnień i kont z właścicielem i/lub uprawnienia do zapisu nie mają włączonego uwierzytelniania wieloskładnikowego.

Systemy używanego do administrowania usługą w chmurze będzie ma wysoce uprzywilejowany dostęp do tej usługi. Ich naruszenia musi znaczny wpływ, w tym sposób obejścia kontroli zabezpieczeń i kradzieży i umożliwiają manipulowanie dużych ilości danych. Metody używane przez dostawcę usług administratorów do zarządzania usługi operational powinny zostać tak zaprojektowane, aby zmniejszyć ryzyko wykorzystania, który może naruszać bezpieczeństwa usługi. Jeśli zasada ta nie jest zaimplementowany, osoba atakująca może być oznacza, że do obejścia kontroli zabezpieczeń i wykradać lub manipulowania dużych ilości danych.

- Uwierzytelnianie wieloskładnikowe powinno być włączone dla kont z uprawnieniami właściciela subskrypcji
- Uwierzytelnianie wieloskładnikowe powinny być włączone kont z uprawnieniami do zapisu w ramach subskrypcji
- Zewnętrzne konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje dwie definicje usługi Azure Policy do przeprowadzania inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL i usługi Service Fabric. Za pomocą usługi Azure Active Directory uwierzytelniania umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft.

- Administrator usługi Azure Active Directory powinny zostać aprowizowane dla serwerów SQL
- Klastry usługi Service Fabric do uwierzytelniania klientów należy używać tylko usługi Azure Active Directory

Ten plan przypisuje czterech definicji usługi Azure Policy do inspekcji kont, które powinny mieć priorytet dla przeglądu, w tym zamortyzowany kont i konta zewnętrzne z podwyższonym poziomem uprawnień. W razie potrzeby, kont można logowanie zablokowane (lub usunąć), które natychmiast powoduje usunięcie prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie definicje usługi Azure Policy konta amortyzowany inspekcji, które mają być uwzględniane w usuwania.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Zewnętrzne konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje definicja zasad platformy Azure, która przeprowadza inspekcję maszyn wirtualnych systemu Linux hasło pliku uprawnień do wyzwalania alertu, jeżeli są ustawione nieprawidłowo. Ten projekt umożliwia podejmowanie działań naprawczych, aby upewnić się, że nie są zagrożone wystawców uwierzytelnienia.

- [Wersja zapoznawcza]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>Informacje o inspekcji 13 dla użytkowników

Ten plan pomaga zagwarantować, system zdarzenia są rejestrowane przez przypisanie 6 [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję ustawienia dziennika dla zasobów platformy Azure. Przypisanych zasad przeprowadza inspekcję, jeśli maszyny wirtualne nie są wysyłane dzienniki do obszaru roboczego analizy określonego dziennika.

- Monitorowanie bez inspekcji serwerów SQL w usłudze Azure Security Center
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Przeprowadź inspekcję ustawienia inspekcji poziomu serwera SQL
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Linux VMs
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Windows VMs
- Wdrażaj zasób network watcher podczas tworzenia sieci wirtualnych

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

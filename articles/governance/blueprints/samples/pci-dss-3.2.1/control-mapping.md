---
title: Mapowanie kontrolek — PCI-DSS v3.2.1 planu — przykład
description: Mapowanie kontrolki próbki Payment Card Industry Data Security Standard v3.2.1 planu usługi Azure Policy i RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540945"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapowanie kontrolek próbki planu v3.2.1 PCI-DSS

Poniższy artykuł szczegółowo opisuje sposób mapowania przykładowy plan v3.2.1 plany platformy Azure ze standardami PCI-DSS formantów v3.2.1 PCI-DSS. Aby uzyskać więcej informacji na temat formantów, zobacz [v3.2.1 PCI-DSS](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Następujące mapowania są **v3.2.1:2018 PCI-DSS** kontrolki. Korzystanie z nawigacji po prawej stronie umożliwia przejście bezpośrednio do mapowania określonego formantu. Wiele kontrolek zamapowanego są implementowane za pomocą [usługi Azure Policy](../../../policy/overview.md) inicjatywy. Aby zapoznać się z pełną inicjatywy, otwórz **zasad** w witrynie Azure portal i wybierz pozycję **definicje** strony. Następnie znajdź i zaznacz  **[Wersja zapoznawcza] v3.2.1:2018 inspekcji PCI kontroluje i wdrażanie określonych rozszerzeń maszyn wirtualnych w celu spełnienia wymagań inspekcji** inicjatywa zasad wbudowanych.

## <a name="132-and-134-boundary-protection"></a>Ochrona granic 1.3.2 i 1.3.4

Ten plan ułatwia zarządzanie i sterowanie sieci, przypisując [usługi Azure Policy](../../../policy/overview.md) definicje, które monitoruje sieciowe grupy zabezpieczeń z liberalnych regułach. Reguły, które są zbyt liberalnych mogą zezwolić na dostęp do sieci niezamierzone i powinna zostać przejrzana pod. Ten plan przypisuje definicji zasad platformy Azure, które Monitoruj niechronione punkty końcowe, aplikacji i kont magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę i kont magazynu z nieograniczonym dostępem umożliwia niekontrolowanego dostępu do informacji zawartych w systemie informacji.

- Przeprowadź inspekcję dostępu do sieci bez ograniczeń do kont magazynu
- Dostęp za pośrednictwem internetowy punkt końcowy powinien być ograniczony

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h i ppkt 6.5.3 kryptograficzna ochrona

Ten plan ułatwia wymuszanie zasad przy użyciu kontrolki cryptograph, przypisując [usługi Azure Policy](../../../policy/overview.md) definicje, które wymuszają określonych cryptograph kontroli i inspekcji użytkowania słabe ustawień kryptograficznych. Zrozumienie, w której zasoby platformy Azure może mieć optymalnej konfiguracji kryptograficznych mogą pomóc podjąć działania naprawcze, aby upewnić się, że zasoby są skonfigurowane zgodnie z zasadami bezpieczeństwa informacji. W szczególności zasady przypisane przez ten plan wymagają technologii transparent data encryption w bazach danych SQL; przeprowadzaj inspekcję brakującego szyfrowania na kontach magazynu, a zmienne konta usługi automation. Dostępne są także zasady, który adres inspekcji niezabezpieczonego połączenia konta magazynu, aplikacji funkcji, aplikacji sieci Web, aplikacje interfejsu API i pamięci podręcznej redis Cache i przeprowadzanie inspekcji nieszyfrowana komunikacja usługi Service Fabric.

- Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinny być dostępne tylko za pośrednictwem protokołu HTTPS
- Monitoruj nieszyfrowaną bazę danych SQL w usłudze Azure Security Center
- Szyfrowanie dysków powinny być stosowane w przypadku maszyn wirtualnych
- Zmienne konta usługi Automation, które mają być szyfrowane
- Powinien być włączony tylko bezpiecznych połączeń z usługą Redis Cache
- Bezpieczne przesyłanie danych do konta magazynu powinna być włączona.
- Klastry usługi Service Fabric powinien mieć ustawioną właściwość ClusterProtectionLevel do EncryptAndSign
- Transparent Data Encryption w bazach danych SQL powinno być włączone.
- Wdrażanie bazy danych SQL przezroczyste szyfrowanie danych

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1 i 6.2, 6.6 11.2.1 skanowanie luk w zabezpieczeniach i aktualizacje systemu

Ten plan pomaga w zarządzaniu informacje dotyczą luk w zabezpieczeniach, przypisując [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, wykrywania zagrożeń SQL i maszyny wirtualnej luki w zabezpieczeniach w usłudze Azure Security Center. Usługa Azure Security Center zapewnia możliwości raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń zasobów platformy Azure wdrożonych.

- Monitorowanie brakującej ochrony punktów końcowych w usłudze Azure Security Center
- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server
- Wdrażanie wykrywanie zagrożeń na serwerach SQL
- Aktualizacje systemu powinien być zainstalowany na komputerach
- Powinny zostać skorygowane luk w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach
- Powinny zostać skorygowane luk w zabezpieczeniach w bazach danych SQL
- Luki w zabezpieczeniach powinna zostać skorygowane przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 i 7.1.3 podział obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie zezwala na potrzeby administracyjne nadmiarowości. Z drugiej strony o zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć ryzyko naruszenia zabezpieczeń za pomocą konta właściciela z naruszonymi zabezpieczeniami. Ten plan ułatwia utrzymanie odpowiedniej liczby właścicieli subskrypcji platformy Azure, przypisując [usługi Azure Policy](../../../policy/overview.md) definicje, które inspekcji liczba właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji pomoże Ci zaimplementować odpowiednie rozdzielenie obowiązków.

- Powinna istnieć więcej niż jeden właściciel został przypisany do Twojej subskrypcji
- Maksymalnie 3 właścicieli należy wyznaczyć dla Twojej subskrypcji 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a i 8.3.1.b zarządzania prawami dostępu uprzywilejowanego

Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując [usługi Azure Policy](../../../policy/overview.md) definicji, aby przeprowadzić inspekcję konta zewnętrzne z właścicielem, zapisu i/lub odczytu uprawnień i kont pracowników z właścicielem i/lub zapisu uprawnienia, które nie mają włączonego uwierzytelniania wieloskładnikowego. Azure implementuje kontroli dostępu opartej na rolach (RBAC), aby zarządzać, kto ma dostęp do zasobów platformy Azure. Zrozumienie, w których Implementowanie niestandardowych zasad RBAC ułatwi sprawdzenie wymagań oraz do realizacji, zgodnie z niestandardowymi regułami RBAC są podatne. Planu przypisuje [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji korzystanie z uwierzytelniania usługi Azure Active Directory dla serwerów SQL. Za pomocą usługi Azure Active Directory authentication upraszcza zarządzanie uprawnieniami i centralizuje zarządzanie tożsamościami użytkowników bazy danych i inne firmy Microsoft  
Usługi.
 
- Zewnętrzne konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Zewnętrzne konta z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone dla kont z uprawnieniami właściciela subskrypcji
- Uwierzytelnianie wieloskładnikowe powinny być włączone kont z uprawnieniami do zapisu w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone dla kont z uprawnieniami do odczytu w ramach subskrypcji
- Administrator usługi Azure Active Directory powinny zostać aprowizowane dla serwerów SQL
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 i 8.1.5 najmniej uprawnieniami i przeglądu użytkownika prawa dostępu

Azure implementuje kontroli dostępu opartej na rolach (RBAC) do pomaga, którymi zarządzasz, kto ma dostęp do zasobów na platformie Azure. W witrynie Azure portal, możesz sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnienia. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont, które powinny mieć priorytet dla przeglądu, w tym zamortyzowany kont i konta zewnętrzne z podwyższonym poziomem uprawnień.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Zewnętrzne konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Zewnętrzne konta z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 usunięcie lub dostosowanie prawa dostępu

Azure implementuje kontroli dostępu opartej na rolach (RBAC), aby ułatwić zarządzanie, kto ma dostęp do zasobów platformy Azure. Korzystając z usługi Azure Active Directory i RBAC, można zaktualizować ról użytkownika w celu odzwierciedlenia zmian organizacyjnych. W razie potrzeby, kont można logowanie zablokowane (lub usunąć), które natychmiast powoduje usunięcie prawa dostępu do zasobów platformy Azure. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji zamortyzowany konta, które należy uwzględnić do usunięcia.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b i 8.2.5 uwierzytelniania opartego na hasłach

Ten plan pomaga można wymuszać stosowanie silnych haseł, przypisując [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję maszyn wirtualnych Windows, które nie wymuszają minimalne i inne wymagania dotyczące hasła. Rozpoznawanie maszyn wirtualnych z naruszeniem zasad siły haseł pomaga podjąć działania naprawcze, aby upewnić się, że zgodne z zasadami haseł dla wszystkich kont użytkowników maszyny Wirtualnej.

- [Wersja zapoznawcza]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Wersja zapoznawcza]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Wersja zapoznawcza]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [Wersja zapoznawcza]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>Generowanie inspekcji 10.3 i 10.5.4

Ten plan pomaga zagwarantować, system zdarzenia są rejestrowane przez przypisanie [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję ustawienia dziennika dla zasobów platformy Azure.
Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonywanych w ramach zasobów platformy Azure. Dzienniki platformy Azure zależą od wewnętrznego Zegary zsynchronizowane, aby utworzyć rekord czas skorelowanego zdarzenia w zasobach.

- Monitorowanie bez inspekcji serwerów SQL w usłudze Azure Security Center
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Przeprowadź inspekcję ustawienia inspekcji poziomu serwera SQL
- Wdrażanie inspekcji na serwerach SQL
- Konta magazynu powinny być migrowane do nowych zasobów usługi Azure Resource Manager
- Maszyny wirtualne powinny być migrowane do nowych zasobów usługi Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>Bezpieczeństwo informacji 12.3.6 i 12.3.7

Ten plan ułatwia zarządzanie i kontrolować sieć, przypisując [usługi Azure Policy](../../../policy/overview.md) definicje, które inspekcji lokalizacje sieciowe dopuszczalne i produktów firmy zatwierdzonych dozwolone dla środowiska. To są dostosowywane przez każdej firmy, za pomocą parametrów zasad w ramach każdej z tych zasad.

- Dozwolone lokalizacje
- Dozwolone lokalizacje dla grup zasobów

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy przejrzeniu mapowanie kontrolek planu v3.2.1 PCI DSS, odwiedź następujące artykuły, aby dowiedzieć się o przeglądzie oraz sposób wdrażania w tym przykładzie:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 planu — omówienie](./index.md)
> [v3.2.1 PCI-DSS planu — wdrażanie kroki](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

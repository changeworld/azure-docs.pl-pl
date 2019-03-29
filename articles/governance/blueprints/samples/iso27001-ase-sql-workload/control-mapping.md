---
title: Przykład — ISO 27001 planu — mapowanie kontrolek
description: Mapowanie kontrolek ISO 27001 przykładowy plan.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c062759938652518ac3cafff64973050554ca19d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579703"
---
# <a name="control-mapping-of-the-azure-blueprints-iso-27001-blueprint-sample"></a>Mapowanie kontrolek Azure schematy ISO 27001 próbki planu

Następujący artykuł szczegółowo opisuje sposób mapowania kontroli ISO 27001 przykładowy plan Azure schematy ISO 27001 ASE/SQL obciążenia. Aby uzyskać więcej informacji na temat formantów, zobacz [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Następujące mapowania są **ISO 27001: 2013** kontrolki. Korzystanie z nawigacji po prawej stronie umożliwia przejście bezpośrednio do mapowania określonego formantu. Wiele kontrolek zamapowanego są implementowane za pomocą [usługi Azure Policy](../../../policy/overview.md) inicjatywy. Aby zapoznać się z pełną inicjatywy, otwórz **zasad** w witrynie Azure portal i wybierz pozycję **definicje** strony. Następnie znajdź i zaznacz  **[Wersja zapoznawcza] audytu ISO 27001: 2013 kontroluje i wdrażanie określonych rozszerzeń maszyn wirtualnych w celu spełnienia wymagań inspekcji** inicjatywa zasad wbudowanych.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 podział obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie zezwala na potrzeby administracyjne nadmiarowości. Z drugiej strony o zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć ryzyko naruszenia zabezpieczeń za pomocą konta właściciela z naruszonymi zabezpieczeniami. Ten plan ułatwia utrzymanie odpowiedniej liczby właścicieli subskrypcji platformy Azure, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicje, które inspekcji liczba właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji pomoże Ci zaimplementować odpowiednie rozdzielenie obowiązków.

- [Wersja zapoznawcza]: Audit minimum number of owners for subscription
- [Wersja zapoznawcza]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 dostępu do sieci i usługami sieciowymi

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby zarządzać, kto ma dostęp do zasobów platformy Azure. Ten plan ułatwia kontrolowanie dostępu do zasobów platformy Azure, przypisując siedem [usługi Azure Policy](../../../policy/overview.md) definicje. Te zasady przeprowadzania inspekcji użycia typów zasobów i konfiguracje, które mogą umożliwić mniej ograniczające dostęp do zasobów.
Zasoby zrozumienia, naruszających może te zasady, pomoc podjąć działania naprawcze, aby zapewnić dostęp do zasobów platformy Azure jest ograniczone do autoryzowanych użytkowników.

- [Wersja zapoznawcza]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Wersja zapoznawcza]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Hasłas
- [Wersja zapoznawcza]: Audit Linux VM accounts with no passwords
- [Wersja zapoznawcza]: Audit Linux VM allowing remote connections from accounts with no passwords
- Przeprowadź inspekcję użycia klasycznych kont magazynu
- Przeprowadź inspekcję użycia klasycznych maszyn wirtualnych
- Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="a922-user-access-provisioning"></a>Inicjowanie obsługi dostępu użytkowników A.9.2.2

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby zarządzać, kto ma dostęp do zasobów platformy Azure. Ten plan przypisuje trzy [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji użytkowania [usługi Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) uwierzytelnianie dla serwerów SQL i [usługi Service Fabric](../../../../service-fabric/service-fabric-overview.md). Za pomocą usługi Azure Active Directory uwierzytelniania umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft. Ten plan przypisuje inspekcjonuj użycie niestandardowych zasad RBAC definicji zasad platformy Azure. Zrozumienie, w których Implementowanie niestandardowych zasad RBAC ułatwi sprawdzenie wymagań oraz do realizacji, zgodnie z niestandardowymi regułami RBAC są podatne.

- Przeprowadź inspekcję aprowizowania administratora usługi Azure Active Directory dla programu SQL Server
- Przeprowadź inspekcję użycia usługi Azure Active Directory na potrzeby uwierzytelnienia klientów w usłudze Service Fabric
- Przeprowadź inspekcję użycia niestandardowych reguł kontroli RBAC

## <a name="a923-management-of-privileged-access-rights"></a>Zarządzanie A.9.2.3 uprzywilejowane prawa dostępu

Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując cztery [usługi Azure Policy](../../../policy/overview.md) definicji w celu inspekcji konta zewnętrzne z właścicielem i/lub zapisać uprawnień i kont z właścicielem i/lub uprawnienia do zapisu nie mają włączonego uwierzytelniania wieloskładnikowego.

- [Wersja zapoznawcza]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Wersja zapoznawcza]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Wersja zapoznawcza]: Audit external accounts with owner permissions on a subscription
- [Wersja zapoznawcza]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Zarządzanie A.9.2.4 informacji klucza tajnego uwierzytelniania użytkowników

Ten plan przypisuje trzy [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont, które nie mają włączonego uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga bezpieczeństwo konta, nawet w przypadku naruszenia zabezpieczeń jeden zestaw informacji uwierzytelniania. Monitorując kont bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej prawdopodobne naruszenia. Ten plan przypisuje dwiema definicjami zasad platformy Azure, które inspekcji maszyny Wirtualnej systemu Linux hasło pliku uprawnień do wyzwalania alertu, jeżeli są ustawione nieprawidłowo. Ta konfiguracja umożliwia akcje naprawcze, aby upewnić się, że nie są zagrożone wystawców uwierzytelnienia.

- [Wersja zapoznawcza]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Wersja zapoznawcza]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Wersja zapoznawcza]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Wersja zapoznawcza]: Deploy VM extension to audit Linux VM passwd file permissions
- [Wersja zapoznawcza]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>Przegląd A.9.2.5 praw dostępu użytkownika

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC) do pomaga zarządzasz, kto ma dostęp do zasobów na platformie Azure. W witrynie Azure portal, możesz sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnienia. Ten plan przypisuje cztery [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont, które powinny mieć priorytet dla przeglądu, w tym zamortyzowany kont i konta zewnętrzne z podwyższonym poziomem uprawnień.

- [Wersja zapoznawcza]: Audit deprecated accounts on a subscription
- [Wersja zapoznawcza]: Audit deprecated accounts with owner permissions on a subscription
- [Wersja zapoznawcza]: Audit external accounts with owner permissions on a subscription
- [Wersja zapoznawcza]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Usunięcie A.9.2.6 lub dostosowanie prawa dostępu

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC) do pomaga zarządzasz, kto ma dostęp do zasobów na platformie Azure. Za pomocą [usługi Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) i RBAC, można zaktualizować ról użytkownika w celu odzwierciedlenia zmian organizacyjnych. W razie potrzeby, kont można logowanie zablokowane (lub usunąć), które natychmiast powoduje usunięcie prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwa [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji zamortyzowany konta, które należy uwzględnić do usunięcia.

- [Wersja zapoznawcza]: Audit deprecated accounts on a subscription
- [Wersja zapoznawcza]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>System zarządzania hasłami A.9.4.3

Ten plan pomaga można wymuszać stosowanie silnych haseł, przypisując 10 [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję maszyn wirtualnych Windows, które nie wymuszają minimalne i inne wymagania dotyczące hasła. Rozpoznawanie maszyn wirtualnych z naruszeniem zasad siły haseł pomaga podjąć działania naprawcze, aby upewnić się, że zgodne z zasadami haseł dla wszystkich kont użytkowników maszyny Wirtualnej.

- [Wersja zapoznawcza]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Wersja zapoznawcza]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Wersja zapoznawcza]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Wersja zapoznawcza]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Wersja zapoznawcza]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Wersja zapoznawcza]: Audit Windows VM enforces password complexity requirements
- [Wersja zapoznawcza]: Audit Windows VM maximum password age 70 days
- [Wersja zapoznawcza]: Audit Windows VM minimum password age 1 day
- [Wersja zapoznawcza]: Audit Windows VM passwords must be at least 14 characters
- [Wersja zapoznawcza]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Zasady A.10.1.1 przy użyciu formantów kryptograficznych

Ten plan ułatwia wymuszanie zasad dotyczących używania kontrolek cryptograph, przypisując 13 [usługi Azure Policy](../../../policy/overview.md) definicje, które wymuszają określonych cryptograph kontroli i inspekcji użytkowania słabe ustawień kryptograficznych.
Zrozumienie, w której zasoby platformy Azure może mieć optymalnej konfiguracji kryptograficznych mogą pomóc podjąć działania naprawcze, aby upewnić się, że zasoby są skonfigurowane zgodnie z zasadami bezpieczeństwa informacji. W szczególności zasady przypisane przez ten plan Wymagaj szyfrowania dla konta usługi blob storage i kont magazynu jeziora danych; wymagają technologii transparent data encryption w bazach danych SQL; przeprowadzaj inspekcję brakującego szyfrowania na konta magazynu, baz danych SQL, dyski maszyny wirtualnej i zmienne konta usługi automation; Inspekcja niezabezpieczonego połączenia z kontami magazynu, aplikacji funkcji, aplikacji sieci Web, aplikacje interfejsu API i pamięci podręcznej Redis; przeprowadzaj inspekcję szyfrowania hasła słabe maszyny wirtualnej; i inspekcji nieszyfrowana komunikacja usługi Service Fabric.

- [Wersja zapoznawcza]: Audit HTTPS only access for a Function App
- [Wersja zapoznawcza]: Audit HTTPS only access for a Web Application
- [Wersja zapoznawcza]: Audit HTTPS only access for an API App
- [Wersja zapoznawcza]: Audit missing blob encryption for storage accounts
- [Wersja zapoznawcza]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  Szyfrowanien
- [Wersja zapoznawcza]: Audit Windows VM should not store passwords using reversible encryption
- [Wersja zapoznawcza]: Monitor unencrypted SQL database in Azure Security Center
- [Wersja zapoznawcza]: Monitor unencrypted VM Disks in Azure Security Center
- Przeprowadź inspekcję włączania szyfrowania zmiennych konta usługi Automation
- Przeprowadź inspekcję włączania tylko bezpiecznych połączeń z pamięć podręczną Redis Cache
- Przeprowadź inspekcję bezpiecznego transferu do kont magazynu
- Przeprowadź inspekcję ustawienia właściwości ClusterProtectionLevel na wartość EncryptAndSign w usłudze Service Fabric
- Inspekcja stanu przezroczystego szyfrowania danych

## <a name="a1241-event-logging"></a>Rejestrowanie zdarzeń A.12.4.1

Ten plan pomaga zagwarantować, system zdarzenia są rejestrowane przez przypisanie siedem [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję ustawienia dziennika dla zasobów platformy Azure. Przypisanych zasad przeprowadza inspekcję, jeśli maszyny wirtualne nie są wysyłane dzienniki do obszaru roboczego analizy określonego dziennika.

- [Wersja zapoznawcza]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Inspekcja wdrożenie agenta zależności w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Monitor unaudited SQL database in Azure Security Center
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Przeprowadź inspekcję ustawień inspekcji SQL na poziomie serwera

## <a name="a121-management-of-technical-vulnerabilities"></a>Zarządzanie A.12.1 techniczne luk w zabezpieczeniach

Ten plan pomaga w zarządzaniu informacje dotyczą luk w zabezpieczeniach, przypisując pięciu [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, wykrywania zagrożeń SQL i maszyny wirtualnej luki w zabezpieczeniach. Wglądowi w szczegółowe dane zawierają w czasie rzeczywistym informacje o stanie zabezpieczeń zasobów wdrożonych i może pomóc określić priorytety działania korygujące.

- [Wersja zapoznawcza]: Monitor missing Endpoint Protection in Azure Security Center
- [Wersja zapoznawcza]: Monitor missing system updates in Azure Security Center
- [Wersja zapoznawcza]: Monitor OS vulnerabilities in Azure Security Center
- [Wersja zapoznawcza]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Wersja zapoznawcza]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 ograniczenia dotyczące instalacji oprogramowania

Funkcji adaptacyjnego sterowania aplikacjami to rozwiązanie w usłudze Azure Security Center pomaga kontrolować, które aplikacje można uruchamiać na maszynach wirtualnych na platformie Azure. Ten plan przypisuje definicja zasad platformy Azure, która śledzi zmiany w zestaw dozwolonych aplikacji. Ograniczenia dotyczące instalacji oprogramowania może pomóc zmniejszyć prawdopodobieństwo wprowadzenia luk w zabezpieczeniach oprogramowania.

- [Wersja zapoznawcza]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>Formanty sieciowe A.13.1.1

Ten plan ułatwia zarządzanie i sterowanie sieci, przypisując [usługi Azure Policy](../../../policy/overview.md) definicję, która monitoruje sieciowe grupy zabezpieczeń z liberalnych regułach. Reguły, które są zbyt liberalnych mogą zezwolić na dostęp do sieci niezamierzone i powinna zostać przejrzana pod.

- [Wersja zapoznawcza]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>Procedury i zasady transferu informacji A.13.2.1

Planu pomaga zagwarantować, przekazywania informacji z usługami platformy Azure jest bezpieczne, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji niezabezpieczonego połączenia konta usługi storage i Redis Cache.

- Przeprowadź inspekcję włączania tylko bezpiecznych połączeń z pamięć podręczną Redis Cache
- Przeprowadź inspekcję bezpiecznego transferu do kont magazynu

## <a name="a1413-protecting-application-services-transactions"></a>Transakcje usługi A.14.1.3 chronienie aplikacji

Ten plan pomaga chronić zasoby systemu informacji, przypisując trzy [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują niechronione punkty końcowe, aplikacji i kont magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę i kont magazynu z nieograniczonym dostępem umożliwia niekontrolowanego dostępu do informacji zawartych w systemie informacji.

- [Wersja zapoznawcza]: Monitor unprotected network endpoints in Azure Security Center
- [Wersja zapoznawcza]: Monitor unprotected web application in Azure Security Center
- Przeprowadź inspekcję nieograniczonego dostępu do kont magazynu

## <a name="a1613-reporting-information-security-weaknesses"></a>Słabe strony algorytmu A.16.1.3 raportowania informacji zabezpieczeń

Ten plan pomaga dające luki w zabezpieczeniach systemu, przypisując pięciu [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują luk w zabezpieczeniach, stanu poprawek i alerty o złośliwym oprogramowaniu w usłudze Azure Security Center. Usługa Azure Security Center zapewnia możliwości raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń zasobów platformy Azure wdrożonych.

- [Wersja zapoznawcza]: Monitor missing Endpoint Protection in Azure Security Center
- [Wersja zapoznawcza]: Monitor missing system updates in Azure Security Center
- [Wersja zapoznawcza]: Monitor OS vulnerabilities in Azure Security Center
- [Wersja zapoznawcza]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Wersja zapoznawcza]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy przejrzeniu mapowanie kontrolek próbki planu obciążenia ISO 27001 App Service środowiska/bazą danych SQL, odwiedź następujące artykuły, aby dowiedzieć się więcej o architekturze i sposobu wdrażania w tym przykładzie:

> [!div class="nextstepaction"]
> [Plan obciążenia ISO 27001 App Service środowiska/bazą danych SQL — omówienie](./index.md)
> [obciążenie bazy danych środowiska/SQL usługi ISO 27001 aplikacji planu — wdrażanie kroki](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
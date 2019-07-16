---
title: Przykładowe mapowanie kontrolek — ISO 27001 udostępnionych usług planu-
description: Mapowanie kontrolek przykładowy plan ISO 27001 udostępnionych usług do usługi Azure Policy i RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 5c225d76e1822d42500713578a7159eed0699a66
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225958"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Mapowanie kontrolek próbki planu ISO 27001 udostępnionych usług

Poniższy artykuł szczegółowo opisuje sposób usług Azure schematy ISO 27001 udostępnionych planu mapy próbki z kontrolkami ISO 27001. Aby uzyskać więcej informacji na temat formantów, zobacz [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Następujące mapowania są **ISO 27001: 2013** kontrolki. Korzystanie z nawigacji po prawej stronie umożliwia przejście bezpośrednio do mapowania określonego formantu. Wiele kontrolek zamapowanego są implementowane za pomocą [usługi Azure Policy](../../../policy/overview.md) inicjatywy. Aby zapoznać się z pełną inicjatywy, otwórz **zasad** w witrynie Azure portal i wybierz pozycję **definicje** strony. Następnie znajdź i zaznacz  **\[Podgląd\] audytu ISO 27001: 2013 kontroluje i wdrażanie określonych rozszerzeń maszyn wirtualnych w celu spełnienia wymagań inspekcji** inicjatywa zasad wbudowanych.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 podział obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie zezwala na potrzeby administracyjne nadmiarowości. Z drugiej strony o zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć ryzyko naruszenia zabezpieczeń za pomocą konta właściciela z naruszonymi zabezpieczeniami. Ten plan ułatwia utrzymanie odpowiedniej liczby właścicieli subskrypcji platformy Azure, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicje, które inspekcji liczba właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji pomoże Ci zaimplementować odpowiednie rozdzielenie obowiązków.

- \[Podgląd\]: Minimalna liczba właścicieli subskrypcji inspekcji
- \[Podgląd\]: Inspekcja maksymalną liczbę właścicieli subskrypcji

## <a name="a821-classification-of-information"></a>A.8.2.1 klasyfikacji informacji

Azure [oceny luk w zabezpieczeniach SQL service](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) może pomóc odkryć, danych poufnych przechowywanych w bazach danych i zawiera zalecenia dotyczące klasyfikowania danych. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji, że zostaną rozwiązane zidentyfikowane podczas oceny luk w zabezpieczeniach SQL skanowania pod kątem luk w zabezpieczeniach.

- \[Podgląd\]: Monitoruj wyniki oceny luk w zabezpieczeniach SQL w usłudze Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 dostępu do sieci i usługami sieciowymi

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby zarządzać, kto ma dostęp do zasobów platformy Azure. Ten plan ułatwia kontrolowanie dostępu do zasobów platformy Azure, przypisując siedem [usługi Azure Policy](../../../policy/overview.md) definicje. Te zasady przeprowadzania inspekcji użycia typów zasobów i konfiguracje, które mogą umożliwić mniej ograniczające dostęp do zasobów.
Zasoby zrozumienia, naruszających może te zasady, pomoc podjąć działania naprawcze, aby zapewnić dostęp do zasobów platformy Azure jest ograniczone do autoryzowanych użytkowników.

- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji kont maszyny Wirtualnej systemu Linux przy użyciu hasła nie
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji zezwala na połączenia zdalne z kont z żadne hasła maszyny Wirtualnej systemu Linux
- \[Podgląd\]: Inspekcja kont maszyny Wirtualnej systemu Linux przy użyciu hasła nie
- \[Podgląd\]: Inspekcja zezwala na połączenia zdalne z kont z żadne hasła maszyny Wirtualnej systemu Linux
- Inspekcja użycia klasycznych kont magazynu
- Inspekcja użycia klasycznych maszyn wirtualnych
- Przeprowadzaj inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="a923-management-of-privileged-access-rights"></a>Zarządzanie A.9.2.3 uprzywilejowane prawa dostępu

Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując cztery [usługi Azure Policy](../../../policy/overview.md) definicji w celu inspekcji konta zewnętrzne z właścicielem i/lub zapisać uprawnień i kont z właścicielem i/lub uprawnienia do zapisu nie mają włączonego uwierzytelniania wieloskładnikowego. Azure implementuje kontroli dostępu opartej na rolach (RBAC), aby zarządzać, kto ma dostęp do zasobów platformy Azure. Ten plan przypisuje także trzy definicji zasad platformy Azure w celu przeprowadzania inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL i usługi Service Fabric. Za pomocą usługi Azure Active Directory uwierzytelniania umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft. Ten plan przypisuje inspekcjonuj użycie niestandardowych zasad RBAC definicji zasad platformy Azure. Zrozumienie, w których Implementowanie niestandardowych zasad RBAC ułatwi sprawdzenie wymagań oraz do realizacji, zgodnie z niestandardowymi regułami RBAC są podatne.

- \[Podgląd\]: Inspekcja kont z uprawnieniami właściciela, którzy nie są włączone w ramach subskrypcji usługi MFA
- \[Podgląd\]: Inspekcja kont z uprawnieniami do zapisu, którzy nie są włączone w ramach subskrypcji usługi MFA
- \[Podgląd\]: Przeprowadź inspekcję konta zewnętrzne z uprawnieniami właściciela na subskrypcji
- \[Podgląd\]: Inspekcja kont zewnętrznych z uprawnieniami do zapisu w subskrypcji
- Przeprowadź inspekcję aprowizacji administrator usługi Azure Active Directory dla programu SQL server
- Inspekcja użycia usługi Azure Active Directory do uwierzytelniania klientów w usłudze Service Fabric
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Zarządzanie A.9.2.4 informacji klucza tajnego uwierzytelniania użytkowników

Ten plan przypisuje trzy [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont, które nie mają włączonego uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga bezpieczeństwo konta, nawet w przypadku naruszenia zabezpieczeń jeden zestaw informacji uwierzytelniania. Monitorując kont bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej prawdopodobne naruszenia. Ten plan przypisuje dwiema definicjami zasad platformy Azure, które inspekcji maszyny Wirtualnej systemu Linux hasło pliku uprawnień do wyzwalania alertu, jeżeli są ustawione nieprawidłowo. Ta konfiguracja umożliwia akcje naprawcze, aby upewnić się, że nie są zagrożone wystawców uwierzytelnienia.

- \[Podgląd\]: Inspekcja kont z uprawnieniami właściciela, którzy nie są włączone w ramach subskrypcji usługi MFA
- \[Podgląd\]: Inspekcja kont z uprawnieniami do odczytu, którzy nie są włączone w ramach subskrypcji usługi MFA
- \[Podgląd\]: Inspekcja kont z uprawnieniami do zapisu, którzy nie są włączone w ramach subskrypcji usługi MFA
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej, aby przeprowadzić inspekcję uprawnienia do pliku haseł maszyny Wirtualnej systemu Linux
- \[Podgląd\]: Uprawnienia do pliku/etc/passwd inspekcji maszyny Wirtualnej systemu Linux są ustawione na 0644

## <a name="a925-review-of-user-access-rights"></a>Przegląd A.9.2.5 praw dostępu użytkownika

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie, kto ma dostęp do zasobów na platformie Azure. W witrynie Azure portal, możesz sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnienia. Ten plan przypisuje cztery [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont, które powinny mieć priorytet dla przeglądu, w tym zamortyzowany kont i konta zewnętrzne z podwyższonym poziomem uprawnień.

- \[Podgląd\]: Inspekcja przestarzałe konta w ramach subskrypcji
- \[Podgląd\]: Inspekcja przestarzałe konta z uprawnieniami właściciela na subskrypcji
- \[Podgląd\]: Przeprowadź inspekcję konta zewnętrzne z uprawnieniami właściciela na subskrypcji
- \[Podgląd\]: Inspekcja kont zewnętrznych z uprawnieniami do zapisu w subskrypcji

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Usunięcie A.9.2.6 lub dostosowanie prawa dostępu

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie, kto ma dostęp do zasobów na platformie Azure. Za pomocą [usługi Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) i RBAC, można zaktualizować ról użytkownika w celu odzwierciedlenia zmian organizacyjnych. W razie potrzeby, kont można logowanie zablokowane (lub usunąć), które natychmiast powoduje usunięcie prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwa [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji zamortyzowany konta, które należy uwzględnić do usunięcia.

- \[Podgląd\]: Inspekcja przestarzałe konta w ramach subskrypcji
- \[Podgląd\]: Inspekcja przestarzałe konta z uprawnieniami właściciela na subskrypcji

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 bezpiecznego logowania procedur

Ten plan przypisuje trzech definicji usługi Azure Policy do inspekcji kont, które nie mają włączonego uwierzytelniania wieloskładnikowego. Usługa Azure Multi-Factor Authentication zapewnia dodatkowe zabezpieczenia, wymagając od drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie. Monitorując kont bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej prawdopodobne naruszenia.

- \[Podgląd\]: Inspekcja kont z uprawnieniami właściciela, którzy nie są włączone w ramach subskrypcji usługi MFA
- \[Podgląd\]: Inspekcja kont z uprawnieniami do odczytu, którzy nie są włączone w ramach subskrypcji usługi MFA
- \[Podgląd\]: Inspekcja kont z uprawnieniami do zapisu, którzy nie są włączone w ramach subskrypcji usługi MFA

## <a name="a943-password-management-system"></a>System zarządzania hasłami A.9.4.3

Ten plan pomaga można wymuszać stosowanie silnych haseł, przypisując 10 [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję maszyn wirtualnych Windows, które nie wymuszają minimalne i inne wymagania dotyczące hasła. Rozpoznawanie maszyn wirtualnych z naruszeniem zasad siły haseł pomaga podjąć działania naprawcze, aby upewnić się, że zgodne z zasadami haseł dla wszystkich kont użytkowników maszyny Wirtualnej.

- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej, aby przeprowadzić inspekcję maszyn wirtualnych Windows wymusza wymagania dotyczące złożoności hasła
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej, aby przeprowadzić inspekcję maszyn wirtualnych Windows maksymalny okres ważności hasła 70 dni
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej, aby przeprowadzić inspekcję maszyn wirtualnych Windows minimalny okres ważności hasła 1 dzień
- \[Podgląd\]: Wdrażanie maszyny Wirtualnej rozszerzenie inspekcji hasła maszyny Wirtualnej Windows musi być co najmniej 14 znaków
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej, aby przeprowadzić inspekcję maszyn wirtualnych Windows nie powinien zezwalać na poprzednich haseł 24
- \[Podgląd\]: Maszyna wirtualna Windows inspekcji wymusza wymagania dotyczące złożoności hasła
- \[Podgląd\]: Przeprowadź inspekcję maszyn wirtualnych Windows maksymalny okres ważności hasła 70 dni
- \[Podgląd\]: Przeprowadź inspekcję maszyn wirtualnych Windows minimalny okres ważności hasła 1 dzień
- \[Podgląd\]: Hasło maszyny Wirtualnej Windows inspekcji musi mieć co najmniej 14 znaków
- \[Podgląd\]: Maszyna wirtualna Windows inspekcji nie powinien zezwalać na poprzednich haseł 24

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Zasady A.10.1.1 przy użyciu formantów kryptograficznych

Ten plan ułatwia wymuszanie zasad dotyczących używania kontrolek cryptograph, przypisując 13 [usługi Azure Policy](../../../policy/overview.md) definicje, które wymuszają określonych cryptograph kontroli i inspekcji użytkowania słabe ustawień kryptograficznych.
Zrozumienie, w której zasoby platformy Azure może mieć optymalnej konfiguracji kryptograficznych mogą pomóc podjąć działania naprawcze, aby upewnić się, że zasoby są skonfigurowane zgodnie z zasadami bezpieczeństwa informacji. W szczególności zasady przypisane przez ten plan Wymagaj szyfrowania dla konta usługi blob storage i kont magazynu jeziora danych; wymagają technologii transparent data encryption w bazach danych SQL; przeprowadzaj inspekcję brakującego szyfrowania na konta magazynu, baz danych SQL, dyski maszyny wirtualnej i zmienne konta usługi automation; Inspekcja niezabezpieczonego połączenia z kontami magazynu, aplikacji funkcji, aplikacji sieci Web, aplikacje interfejsu API i pamięci podręcznej Redis; przeprowadzaj inspekcję szyfrowania hasła słabe maszyny wirtualnej; i inspekcji nieszyfrowana komunikacja usługi Service Fabric.

- \[Podgląd\]: Przeprowadź inspekcję dostępu tylko HTTPS dla aplikacji funkcji
- \[Podgląd\]: Przeprowadź inspekcję dostępu tylko HTTPS dla aplikacji sieci Web
- \[Podgląd\]: Przeprowadź inspekcję dostępu tylko HTTPS dla aplikacji interfejsu API
- \[Podgląd\]: Przeprowadź inspekcję brakującego szyfrowania elementu blob dla kont magazynu
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej, aby przeprowadzić inspekcję maszyn wirtualnych Windows nie przechowuj hasła, przy użyciu szyfrowania odwracalnego
- \[Podgląd\]: Maszyna wirtualna Windows inspekcji nie przechowuj hasła, przy użyciu szyfrowania odwracalnego
- \[Podgląd\]: Monitoruj nieszyfrowaną bazę danych SQL w usłudze Azure Security Center
- \[Podgląd\]: Monitoruj nieszyfrowane dyski maszyn wirtualnych w usłudze Azure Security Center
- Włączenie inspekcji szyfrowania zmiennych konta usługi Automation
- Przeprowadź inspekcję włączenie tylko bezpiecznych połączeń z usługą Redis Cache
- Inspekcja bezpiecznego transferu na kontach magazynu
- Przeprowadź inspekcję ustawienia właściwości ClusterProtectionLevel EncryptAndSign w usłudze Service Fabric
- Inspekcja stanu przezroczystego szyfrowania danych

## <a name="a1241-event-logging"></a>Rejestrowanie zdarzeń A.12.4.1

Ten plan pomaga zagwarantować, system zdarzenia są rejestrowane przez przypisanie siedem [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję ustawienia dziennika dla zasobów platformy Azure.
Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonywanych w ramach zasobów platformy Azure.

- \[Podgląd\]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Inspekcja wdrożenie agenta zależności w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Monitorowanie bazy danych SQL bez inspekcji w usłudze Azure Security Center
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Przeprowadź inspekcję ustawienia inspekcji poziomu serwera SQL

## <a name="a1243-administrator-and-operator-logs"></a>Dzienniki administratora A.12.4.3 and — operator

Ten plan pomaga upewnić się, że system zdarzenia są rejestrowane przez przypisanie siedem Azure definicjom zasad inspekcji ustawienia dziennika dla zasobów platformy Azure. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonywanych w ramach zasobów platformy Azure.

- \[Podgląd\]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Inspekcja wdrożenie agenta zależności w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Monitorowanie bazy danych SQL bez inspekcji w usłudze Azure Security Center
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Przeprowadź inspekcję ustawienia inspekcji poziomu serwera SQL

## <a name="a1244-clock-synchronization"></a>Synchronizacja zegara A.12.4.4

Ten plan pomaga upewnić się, że system zdarzenia są rejestrowane przez przypisanie siedem Azure definicjom zasad inspekcji ustawienia dziennika dla zasobów platformy Azure. Dzienniki platformy Azure zależą od wewnętrznego Zegary zsynchronizowane, aby utworzyć rekord czas skorelowanego zdarzenia w zasobach.

- \[Podgląd\]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Inspekcja wdrożenie agenta zależności w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- \[Podgląd\]: Monitorowanie bazy danych SQL bez inspekcji w usłudze Azure Security Center
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Przeprowadź inspekcję ustawienia inspekcji poziomu serwera SQL

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Instalacja oprogramowania w systemach operacyjnych

Funkcji adaptacyjnego sterowania aplikacjami to rozwiązanie w usłudze Azure Security Center pomaga kontrolować, które aplikacje można uruchamiać na maszynach wirtualnych na platformie Azure. Ten plan przypisuje definicja zasad platformy Azure, która śledzi zmiany w zestaw dozwolonych aplikacji. Ta możliwość ułatwia kontrolowanie instalacji oprogramowania i aplikacji na maszynach wirtualnych platformy Azure.

- \[Podgląd\]: Monitor możliwe listę dozwolonych aplikacji w usłudze Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>Zarządzanie A.12.6.1 techniczne luk w zabezpieczeniach

Ten plan pomaga w zarządzaniu informacje dotyczą luk w zabezpieczeniach, przypisując pięciu [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, wykrywania zagrożeń SQL i maszyny wirtualnej luki w zabezpieczeniach w usłudze Azure Security Center. Usługa Azure Security Center zapewnia możliwości raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń zasobów platformy Azure wdrożonych.

- \[Podgląd\]: Monitorowanie brakującej ochrony punktów końcowych w usłudze Azure Security Center
- \[Podgląd\]: Monitoruj brakujące aktualizacje systemu w usłudze Azure Security Center
- \[Podgląd\]: Monitorowanie luk w zabezpieczeniach systemu operacyjnego, w usłudze Azure Security Center
- \[Podgląd\]: Monitoruj wyniki oceny luk w zabezpieczeniach SQL w usłudze Azure Security Center
- \[Podgląd\]: Monitorowanie maszyn wirtualnych luk w zabezpieczeniach w usłudze Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 ograniczenia dotyczące instalacji oprogramowania

Funkcji adaptacyjnego sterowania aplikacjami to rozwiązanie w usłudze Azure Security Center pomaga kontrolować, które aplikacje można uruchamiać na maszynach wirtualnych na platformie Azure. Ten plan przypisuje definicja zasad platformy Azure, która śledzi zmiany w zestaw dozwolonych aplikacji. Ograniczenia dotyczące instalacji oprogramowania może pomóc zmniejszyć prawdopodobieństwo wprowadzenia luk w zabezpieczeniach oprogramowania.

- \[Podgląd\]: Monitor możliwe listę dozwolonych aplikacji w usłudze Azure Security Center

## <a name="a1311-network-controls"></a>Formanty sieciowe A.13.1.1

Ten plan ułatwia zarządzanie i sterowanie sieci, przypisując [usługi Azure Policy](../../../policy/overview.md) definicję, która monitoruje sieciowe grupy zabezpieczeń z liberalnych regułach. Reguły, które są zbyt liberalnych mogą zezwolić na dostęp do sieci niezamierzone i powinna zostać przejrzana pod. Ten plan przypisuje trzech definicji zasad platformy Azure, które monitorują niechronione punkty końcowe, aplikacji i kont magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę i kont magazynu z nieograniczonym dostępem umożliwia niekontrolowanego dostępu do informacji zawartych w systemie informacji.

- \[Podgląd\]: Monitoruj liberalny dostęp do sieci w Centrum zabezpieczeń Azure
- \[Podgląd\]: Monitoruj niechronione punkty końcowe sieci w Centrum zabezpieczeń Azure
- \[Podgląd\]: Monitoruj niechronione aplikacje internetowe w usłudze Azure Security Center
- Przeprowadź inspekcję dostępu do sieci bez ograniczeń do kont magazynu

## <a name="a1321-information-transfer-policies-and-procedures"></a>Procedury i zasady transferu informacji A.13.2.1

Planu pomaga zagwarantować, przekazywania informacji z usługami platformy Azure jest bezpieczne, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji niezabezpieczonego połączenia konta usługi storage i Redis Cache.

- Przeprowadź inspekcję włączenie tylko bezpiecznych połączeń z usługą Redis Cache
- Inspekcja bezpiecznego transferu na kontach magazynu

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy przejrzeniu mapowanie kontrolek planu ISO 27001 udostępnionych usług, odwiedź następujące artykuły, aby dowiedzieć się więcej o architekturze i sposobu wdrażania w tym przykładzie:

> [!div class="nextstepaction"]
> [ISO 27001 udostępnionych usług planu — omówienie](./index.md)
> [ISO 27001 udostępnionych usług planu — wdrażanie kroki](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

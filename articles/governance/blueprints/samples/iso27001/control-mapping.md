---
title: Przykładowe kontrole planu ISO 27001
description: Mapowanie sterowania próbki planu ISO 27001. Każdy formant jest mapowany na jedną lub więcej zasad platformy Azure, które pomagają w ocenie.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 501884491dbef85cdf8a29cb5fdcef44a68235a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920571"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>Mapowanie sterowania przykładem planu ISO 27001

W poniższym artykule opisano, jak przykładowy plan planu Azure Blueprints ISO 27001 jest mapowana na formanty ISO 27001. Aby uzyskać więcej informacji na temat kontrolek, zobacz [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Następujące mapowania są do **formantów ISO 27001:2013.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania sterowania. Wiele mapowanych formantów są implementowane za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[formanty inspekcji w wersji\] zapoznawczej ISO 27001:2013 i wdruż określone rozszerzenia maszyn wirtualnych, aby obsługiwać wbudowaną** inicjatywę zasad dotyczącą wymagań inspekcji.

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności zawiera formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Podział obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie zezwala na nadmiarowość administracyjną. Z drugiej strony posiadanie zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć ryzyko naruszenia zabezpieczeń za pośrednictwem konta właściciela, których bezpieczeństwo zostało naruszone. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując dwie definicje [zasad platformy Azure,](../../../policy/overview.md) które inspekcji liczbę właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji może pomóc w zaimplementowanie odpowiedniego rozdzielenia obowiązków.

- \[Wersja\]zapoznawcza : Inspekcja minimalnej liczby właścicieli subskrypcji
- \[Wersja\]zapoznawcza : Inspekcja maksymalnej liczby właścicieli subskrypcji

## <a name="a821-classification-of-information"></a>A.8.2.1 Klasyfikacja informacji

[Usługa oceny luk w zabezpieczeniach](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) platformy Azure sql może pomóc w odnajdywanie poufnych danych przechowywanych w bazach danych i zawiera zalecenia dotyczące klasyfikacji tych danych. Ten plan przypisuje definicję [zasad platformy Azure](../../../policy/overview.md) do inspekcji, że luki zidentyfikowane podczas skanowania oceny luk w zabezpieczeniach SQL są korygowane.

- \[Podgląd:\]Monitorowanie wyników oceny luk w zabezpieczeniach SQL w usłudze Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Dostęp do sieci i usług sieciowych

Platforma Azure implementuje [kontrolę dostępu opartą na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby zarządzać tym, kto ma dostęp do zasobów platformy Azure. Ten plan ułatwia kontrolowanie dostępu do zasobów platformy Azure przez przypisanie siedmiu definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady inspekcji użycia typów zasobów i konfiguracji, które mogą zezwalać na bardziej dopuszczalny dostęp do zasobów.
Zrozumienie zasobów, które naruszają te zasady, może pomóc w podjęcie działań naprawczych w celu zapewnienia, że dostęp do zasobów platformy Azure jest ograniczony do autoryzowanych użytkowników.

- \[Wersja\]zapoznawcza : Wdrażanie rozszerzenia maszyny Wirtualnej w celu inspekcji kont maszyn wirtualnych z systemem Linux bez haseł
- \[Wersja\]zapoznawcza : Wdrażanie rozszerzenia maszyny Wirtualnej w celu inspekcji maszyny Wirtualnej systemu Linux umożliwiającej zdalne połączenia z kont bez haseł
- \[Podgląd\]: Inspekcja kont maszyn wirtualnych z systemem Linux bez haseł
- \[Podgląd\]: Inspekcja maszyny Wirtualnej z systemem Linux umożliwiającej zdalne połączenia z kont bez haseł
- Inspekcja wykorzystania klasycznych kont magazynu
- Inspekcja wykorzystania klasycznych maszyn wirtualnych
- Inspekcja maszyn wirtualnych, które nie używają dysków zarządzanych

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Zarządzanie prawami dostępu uprzywilejowanego

Ten plan pomaga ograniczyć i kontrolować uprzywilejowane prawa dostępu, przypisując cztery definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub zapisu i kont z uprawnieniami właściciela i/lub zapisu, które nie mają włączonego uwierzytelniania wieloskładnikowego. Platforma Azure implementuje kontrolę dostępu opartą na rolach (RBAC), aby zarządzać tym, kto ma dostęp do zasobów platformy Azure. Ten plan przypisuje również trzy definicje zasad platformy Azure do inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL i sieci szkieletowej usług. Korzystanie z uwierzytelniania usługi Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami i scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. Ten plan przypisuje również definicji zasad platformy Azure do inspekcji użycia niestandardowych reguł RBAC. Zrozumienie, gdzie implementują niestandardowe reguły RBAC, może pomóc w weryfikacji potrzeb i prawidłowej implementacji, ponieważ niestandardowe reguły RBAC są podatne na błędy.

- \[Wersja\]zapoznawcza : Inspekcja kont z uprawnieniami właściciela, które nie są włączone w ucho w ramach subskrypcji
- \[Podgląd\]: Inspekcja kont z uprawnieniami do zapisu, które nie są włączone w umorzonym u subskrypcji
- \[Wersja\]zapoznawcza : Inspekcja kont zewnętrznych z uprawnieniami właściciela w ramach subskrypcji
- \[Wersja\]zapoznawcza : Inspekcja kont zewnętrznych z uprawnieniami do zapisu w ramach subskrypcji
- Inspekcja inicjowania obsługi administracyjnej administratora usługi Azure Active Directory dla serwera SQL
- Inspekcja użycia usługi Azure Active Directory do uwierzytelniania klienta w sieci szkieletowej usług
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Zarządzanie tajnymi informacjami uwierzytelniania użytkowników

Ten plan przypisuje trzy definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont, które nie mają włączonego uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga chronić konta, nawet jeśli jedna część informacji uwierzytelniania zostanie naruszona. Monitorując konta bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej narażone na zagrożenia. Ten plan przypisuje również dwie definicje zasad platformy Azure, które inspekcji uprawnień do pliku hasła maszyny Wirtualnej systemu Linux do alertów, jeśli są one ustawione niepoprawnie. Ta konfiguracja umożliwia podjęcie działań naprawczych w celu zapewnienia, że wystawcy uwierzytelnienia nie są zagrożone.

- \[Wersja\]zapoznawcza : Inspekcja kont z uprawnieniami właściciela, które nie są włączone w ucho w ramach subskrypcji
- \[Wersja\]zapoznawcza : Inspekcja kont z uprawnieniami do odczytu, które nie są włączone w ramach subskrypcji
- \[Podgląd\]: Inspekcja kont z uprawnieniami do zapisu, które nie są włączone w umorzonym u subskrypcji
- \[Wersja\]zapoznawcza : Wdrażanie rozszerzenia maszyny Wirtualnej w celu inspekcji uprawnień do pliku passwd maszyny Wirtualnej systemu Linux
- \[Podgląd\]: Inspekcja Linux VM /etc/passwd uprawnienia do plików są ustawione na 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Przegląd praw dostępu użytkowników

Platforma Azure implementuje [kontrolę dostępu opartą na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie tym, kto ma dostęp do zasobów na platformie Azure. Korzystając z witryny Azure Portal, można przejrzeć, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje cztery definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont, które powinny być traktowane priorytetowo do przeglądu, w tym zamortyzowane konta i konta zewnętrzne z podwyższonymi uprawnieniami.

- \[Wersja\]zapoznawcza : Inspekcja przestarzałych kont w ramach subskrypcji
- \[Podgląd\]: Inspekcja przestarzałych kont z uprawnieniami właściciela w ramach subskrypcji
- \[Wersja\]zapoznawcza : Inspekcja kont zewnętrznych z uprawnieniami właściciela w ramach subskrypcji
- \[Wersja\]zapoznawcza : Inspekcja kont zewnętrznych z uprawnieniami do zapisu w ramach subskrypcji

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Usunięcie lub dostosowanie praw dostępu

Platforma Azure implementuje [kontrolę dostępu opartą na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie tym, kto ma dostęp do zasobów na platformie Azure. Korzystając z [usługi Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) i RBAC, można zaktualizować role użytkowników, aby odzwierciedlić zmiany organizacyjne. W razie potrzeby konta mogą być blokowane przed logowaniem się (lub usuwaniem), co natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji konta zamortyzowanego, które należy wziąć pod uwagę do usunięcia.

- \[Wersja\]zapoznawcza : Inspekcja przestarzałych kont w ramach subskrypcji
- \[Podgląd\]: Inspekcja przestarzałych kont z uprawnieniami właściciela w ramach subskrypcji

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Bezpieczne procedury wykupu

Ten plan przypisuje trzy definicje zasad platformy Azure do inspekcji kont, które nie mają włączonego uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe platformy Azure zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie. Monitorując konta bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej narażone na zagrożenia.

- \[Wersja\]zapoznawcza : Inspekcja kont z uprawnieniami właściciela, które nie są włączone w ucho w ramach subskrypcji
- \[Wersja\]zapoznawcza : Inspekcja kont z uprawnieniami do odczytu, które nie są włączone w ramach subskrypcji
- \[Podgląd\]: Inspekcja kont z uprawnieniami do zapisu, które nie są włączone w umorzonym u subskrypcji

## <a name="a943-password-management-system"></a>A.9.4.3 System zarządzania hasłami

Ten plan pomaga wymusić silne hasła, przypisując 10 definicji [zasad platformy Azure,](../../../policy/overview.md) które są poddane inspekcji maszyn wirtualnych systemu Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących haseł. Świadomość maszyn wirtualnych z naruszeniem zasad siły hasła pomaga podjąć działania naprawcze, aby upewnić się, że hasła dla wszystkich kont użytkowników maszyn wirtualnych są zgodne z zasadami.

- \[Wersja\]zapoznawcza : Wdrażanie rozszerzenia maszyny Wirtualnej w celu inspekcji maszyny Wirtualnej systemu Windows wymusza wymagania dotyczące złożoności hasła
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej w celu inspekcji maksymalnego wieku hasła maszyny Wirtualnej systemu Windows 70 dni
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej w celu inspekcji minimalnego wieku hasła systemu Windows 1 dzień
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej w celu inspekcji haseł maszyn wirtualnych systemu Windows musi mieć co najmniej 14 znaków
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji maszyny Wirtualnej systemu Windows nie powinno zezwalać na poprzednie hasła 24
- \[Wersja\]zapoznawcza : Inspekcja maszyny Wirtualnej systemu Windows wymusza wymagania dotyczące złożoności hasła
- \[Podgląd\]: Inspekcja maksymalnego wieku hasła maszyny Wirtualnej systemu Windows 70 dni
- \[Podgląd\]: Inspekcja minimalnego wieku hasła systemu Windows 1 dzień
- \[Podgląd\]: Inspekcja haseł maszyn wirtualnych systemu Windows musi mieć co najmniej 14 znaków
- \[Podgląd\]: Inspekcja maszyny Wirtualnej systemu Windows nie powinny zezwalać na poprzednie hasła 24

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Zasady dotyczące stosowania form kontrolnych kryptograficznych

Ten plan ułatwia wymuszanie zasad dotyczących używania formantów kryptograficznych przez przypisanie 13 definicji [zasad platformy Azure,](../../../policy/overview.md) które wymuszają określone formanty kryptografów i inspekcji użycia słabych ustawień kryptograficznych.
Zrozumienie, gdzie zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podjęcie działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. W szczególności zasady przypisane przez ten plan wymagają szyfrowania dla kont magazynu obiektów blob i kont magazynu usługi data lake; wymagać przejrzystego szyfrowania danych w bazach danych SQL; inspekcja brakującego szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych kont automatyzacji; inspekcji niezabezpieczonych połączeń z kontami magazynu, aplikacjami funkcji, aplikacjami sieci Web, aplikacjami interfejsu API i pamięcią podręczną Redis; audyt słabe szyfrowanie haseł maszyny wirtualnej; i inspekcji niezaszyfrowanej sieci szkieletowej usług.

- \[Podgląd\]: Inspekcja dostępu HTTPS tylko dla aplikacji funkcyjnej
- \[Podgląd\]: Inspekcja dostępu HTTPS tylko dla aplikacji sieci Web
- \[Podgląd\]: Inspekcja dostępu https tylko dla aplikacji interfejsu API
- \[Wersja\]zapoznawcza : Inspekcja brakującego szyfrowania obiektów blob dla kont magazynu
- \[Podgląd\]: Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji maszyny Wirtualnej systemu Windows nie powinno przechowywać haseł przy użyciu szyfrowania odwracalnego
- \[Podgląd:\]Inspekcja maszyny Wirtualnej systemu Windows nie powinna przechowywać haseł przy użyciu szyfrowania odwracalnego
- \[Podgląd:\]Monitorowanie niezaszyfrowanych dysków maszyn wirtualnych w usłudze Azure Security Center
- Inspekcja włączania szyfrowania zmiennych kont automatyzacji
- Inspekcja włączanie tylko bezpiecznych połączeń z pamięcią podręczną Redis
- Inspekcja bezpiecznego transferu na konta magazynu
- Inspekcja ustawienia właściwości ClusterProtectionLevel na encryptandsign w sieci szkieletowej usług
- Inspekcja stanu przezroczystego szyfrowania danych
- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL

## <a name="a1241-event-logging"></a>Rejestrowanie zdarzeń a.12.4.1

Ten plan pomaga upewnić się, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu definicji [zasad platformy Azure,](../../../policy/overview.md) które inspekcji ustawień dziennika zasobów platformy Azure.
Dzienniki diagnostyczne zapewniają wgląd w operacje, które zostały wykonane w ramach zasobów platformy Azure.

- \[Wersja\]zapoznawcza : Wdrożenie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta zależności inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji na poziomie serwera SQL
- Inspekcja powinna być włączona w przypadku zaawansowanych ustawień zabezpieczeń danych na programie SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Dzienniki administratorów i operatorów

Ten plan pomaga upewnić się, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu definicji zasad platformy Azure, które inspekcji ustawień dziennika zasobów platformy Azure. Dzienniki diagnostyczne zapewniają wgląd w operacje, które zostały wykonane w ramach zasobów platformy Azure.

- \[Wersja\]zapoznawcza : Wdrożenie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta zależności inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji na poziomie serwera SQL
- Inspekcja powinna być włączona w przypadku zaawansowanych ustawień zabezpieczeń danych na programie SQL Server

## <a name="a1244-clock-synchronization"></a>Synchronizacja zegara A.12.4.4

Ten plan pomaga upewnić się, że zdarzenia systemowe są rejestrowane przez przypisanie siedmiu definicji zasad platformy Azure, które inspekcji ustawień dziennika zasobów platformy Azure. Dzienniki platformy Azure opierają się na zsynchronizowanych zegarach wewnętrznych, aby utworzyć skorelowany rekord zdarzeń w zasobach.

- \[Wersja\]zapoznawcza : Wdrożenie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta zależności inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji na poziomie serwera SQL
- Inspekcja powinna być włączona w przypadku zaawansowanych ustawień zabezpieczeń danych na programie SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Instalacja oprogramowania w systemach operacyjnych

Adaptacyjne sterowanie aplikacjami to rozwiązanie z usługi Azure Security Center, które pomaga kontrolować, które aplikacje mogą działać na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję zasad platformy Azure, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ta funkcja ułatwia kontrolowanie instalacji oprogramowania i aplikacji na maszynach wirtualnych platformy Azure.

- \[Podgląd:\]Monitorowanie możliwej białej listy aplikacji w usłudze Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Zarządzanie lukami technicznymi

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego, przypisując pięć definicji [zasad platformy Azure,](../../../policy/overview.md) które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, luki w zabezpieczeniach SQL i luki w zabezpieczeniach maszyny wirtualnej w usłudze Azure Security Center. Usługa Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń wdrożonych zasobów platformy Azure.

- \[Podgląd\]: Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center
- \[Wersja\]zapoznawcza : Monitorowanie brakujących aktualizacji systemu w usłudze Azure Security Center
- \[Wersja\]zapoznawcza : monitorowanie luk w zabezpieczeniach systemu operacyjnego w usłudze Azure Security Center
- \[Podgląd:\]Monitorowanie wyników oceny luk w zabezpieczeniach SQL w usłudze Azure Security Center
- \[Podgląd:\]Monitorowanie luk w zabezpieczeniach maszyny Wirtualnej w usłudze Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Ograniczenia dotyczące instalacji oprogramowania

Adaptacyjne sterowanie aplikacjami to rozwiązanie z usługi Azure Security Center, które pomaga kontrolować, które aplikacje mogą działać na maszynach wirtualnych znajdujących się na platformie Azure. Ten plan przypisuje definicję zasad platformy Azure, która monitoruje zmiany w zestawie dozwolonych aplikacji. Ograniczenia dotyczące instalacji oprogramowania mogą zmniejszyć prawdopodobieństwo wprowadzenia luk w zabezpieczeniach oprogramowania.

- \[Podgląd:\]Monitorowanie możliwej białej listy aplikacji w usłudze Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Sterowanie siecią

Ten plan ułatwia zarządzanie sieciami i kontrolowanie ich przez przypisywanie definicji [zasad platformy Azure,](../../../policy/overview.md) która monitoruje sieciowe grupy zabezpieczeń z regułami dopuszczalnymi. Reguły, które są zbyt dopuszczalne, mogą zezwalać na niezamierzony dostęp do sieci i powinny zostać sprawdzone. Ten plan przypisuje również trzy definicje zasad platformy Azure, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, oraz konta magazynu z nieograniczonym dostępem mogą zezwalać na niezamierzony dostęp do informacji zawartych w systemie informacyjnym.

- \[Wersja\]zapoznawcza : Monitorowanie dopuszczalnego dostępu do sieci w usłudze Azure Security Center
- \[Podgląd:\]Monitorowanie niechronionych punktów końcowych sieci w usłudze Azure Security Center
- \[Podgląd:\]Monitorowanie niechronionej aplikacji sieci Web w usłudze Azure Security Center
- Inspekcja nieograniczonego dostępu do kont magazynu

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Zasady i procedury przekazywania informacji

Plan pomaga zapewnić, że transfer informacji za pomocą usług platformy Azure jest bezpieczny, przypisując dwie definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji niezabezpieczonych połączeń z kontami magazynu i pamięcią podręczną Redis.

- Inspekcja włączanie tylko bezpiecznych połączeń z pamięcią podręczną Redis
- Inspekcja bezpiecznego transferu na konta magazynu

## <a name="next-steps"></a>Następne kroki

Teraz, po przejrzeniu mapowania sterowania planu ISO 27001, odwiedź następujące artykuły, aby dowiedzieć się więcej o architekturze i jak wdrożyć ten przykład:

> [!div class="nextstepaction"]
> [Plan ISO 27001 — omówienie](./index.md)
> [planu ISO 27001 — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
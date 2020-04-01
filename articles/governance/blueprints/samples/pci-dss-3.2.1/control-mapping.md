---
title: Przykładowe elementy sterujące planu PCI-DSS w wersji 3.2.1
description: Sterowanie mapowaniem przykładowego planu Standard zabezpieczeń danych dla przemysłu kart płatniczych w wersji 3.2.1 do usługi Azure Policy i RBAC.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76905634"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapowanie sterowania przykładem planu PCI-DSS w wersji 3.2.1

W poniższym artykule opisano, jak plan pci-DSS systemu azure plany v3.2.1 przykładowe mapy do formantów PCI-DSS w wersji 3.2.1. Aby uzyskać więcej informacji na temat formantów, zobacz [PCI-DSS w wersji 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Następujące mapowania są do **formantów PCI-DSS w wersji 3.2.1:2018.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania sterowania. Wiele mapowanych formantów są implementowane za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[\] formanty Inspekcji PCI w wersji 3.2.1:2018 i wdruż określone rozszerzenia maszyn wirtualnych, aby obsługiwać wbudowaną** inicjatywę zasad dotyczącą wymagań inspekcji.

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności zawiera formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 i 1.3.4 Ochrona granic

Ten plan ułatwia zarządzanie sieciami i kontrolowanie ich przez przypisywanie definicji [zasad platformy Azure,](../../../policy/overview.md) które monitorują sieciowe grupy zabezpieczeń za pomocą reguł dopuszczalnych. Reguły, które są zbyt dopuszczalne, mogą zezwalać na niezamierzony dostęp do sieci i powinny zostać sprawdzone. Ten plan przypisuje jedną definicję zasad platformy Azure, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, oraz konta magazynu z nieograniczonym dostępem mogą zezwalać na niezamierzony dostęp do informacji zawartych w systemie informacyjnym.

- Inspekcja nieograniczonego dostępu do kont magazynu
- Dostęp przez punkt końcowy skierowany do Internetu powinien być ograniczony

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h i 6.5.3 Ochrona kryptograficzna

Ten plan ułatwia wymuszanie zasad przy użyciu formantów kryptograficznych, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które wymuszają określone formanty kryptografów i inspekcji użycia słabych ustawień kryptograficznych. Zrozumienie, gdzie zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podjęcie działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. W szczególności zasady przypisane przez ten plan wymagają przezroczystego szyfrowania danych w bazach danych SQL; inspekcja brakującego szyfrowania na kontach magazynu i zmiennych konta automatyzacji. Istnieją również zasady, które dotyczą inspekcji niezabezpieczonych połączeń z kontami magazynu, aplikacjami funkcyjnymi, aplikacjami WebApp, aplikacjami interfejsu API i pamięcią podręczną Redis oraz inspekcji niezaszyfrowanej komunikacji sieci szkieletowej usług.

- Aplikacja function powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL
- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- Zmienne konta automatyzacji powinny być szyfrowane
- Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis
- Należy włączyć bezpieczny transfer na konta magazynu
- Klastry sieci szkieletowej usług powinny mieć właściwość ClusterProtectionLevel ustawiona na EncryptAndSign
- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL
- Wdrażanie przezroczystego szyfrowania danych w u źródła danych SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 i 11.2.1 Skanowanie luk w zabezpieczeniach i aktualizacje systemu

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które monitorują brakujące aktualizacje systemu, luki w systemie operacyjnym, luki w zabezpieczeniach SQL i luki w zabezpieczeniach maszyny wirtualnej w usłudze Azure Security Center. Usługa Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń wdrożonych zasobów platformy Azure.

- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center
- Wdrażanie domyślnego rozszerzenia Microsoft IaaSAntimalware dla systemu Windows Server
- Wdrażanie wykrywania zagrożeń na serwerach SQL
- Należy zainstalować aktualizacje systemu na maszynach
- Luki w zabezpieczeniach konfiguracji na komputerach powinny zostać naprawione
- Luki w bazach danych SQL powinny zostać naprawione
- Luki w zabezpieczeniach powinny zostać naprawione przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 i 7.1.3 Rozdzielenie obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie zezwala na nadmiarowość administracyjną. Z drugiej strony posiadanie zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć ryzyko naruszenia zabezpieczeń za pośrednictwem konta właściciela, których bezpieczeństwo zostało naruszone. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które inspekcji liczbę właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji może pomóc w zaimplementowanie odpowiedniego rozdzielenia obowiązków.

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Do subskrypcji należy wyznaczyć maksymalnie 3 właścicieli 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a i 8.3.1.b Zarządzanie prawami dostępu uprzywilejowanego

Ten plan pomaga ograniczyć i kontrolować uprzywilejowane prawa dostępu, przypisując definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela, zapisu i/lub odczytu oraz kontami pracowników z uprawnieniami właściciela i/lub zapisu, które nie mają włączonego uwierzytelniania wieloskładnikowego. Platforma Azure implementuje kontrolę dostępu opartą na rolach (RBAC), aby zarządzać tym, kto ma dostęp do zasobów platformy Azure. Zrozumienie, gdzie implementują niestandardowe reguły RBAC, może pomóc w weryfikacji potrzeb i prawidłowej implementacji, ponieważ niestandardowe reguły RBAC są podatne na błędy. Ten plan przypisuje również definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL. Korzystanie z uwierzytelniania usługi Azure Active Directory upraszcza zarządzanie uprawnieniami i centralizuje zarządzanie tożsamościami użytkowników baz danych i innych firm Microsoft  
services.
 
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 i 8.1.5 Najmniejsze uprawnienia i przegląd praw dostępu użytkowników

Platforma Azure implementuje kontrolę dostępu opartą na rolach (RBAC), aby pomóc w zarządzaniu, kto ma dostęp do zasobów na platformie Azure. Korzystając z witryny Azure Portal, można przejrzeć, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont, które powinny być traktowane priorytetowo do przeglądu, w tym zamortyzowane konta i konta zewnętrzne z podwyższonymi uprawnieniami.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Usunięcie lub dostosowanie praw dostępu

Platforma Azure implementuje kontrolę dostępu opartą na rolach (RBAC), aby ułatwić zarządzanie tym, kto ma dostęp do zasobów na platformie Azure. Korzystając z usługi Azure Active Directory i RBAC, można zaktualizować role użytkowników, aby odzwierciedlić zmiany organizacyjne. W razie potrzeby konta mogą być blokowane przed logowaniem się (lub usuwaniem), co natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji konta zamortyzowanego, które należy wziąć pod uwagę do usunięcia.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b i 8.2.5 Uwierzytelnianie oparte na hasłach

Ten plan pomaga wymusić silne hasła, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które są poddane inspekcji maszyn wirtualnych systemu Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących haseł. Świadomość maszyn wirtualnych z naruszeniem zasad siły hasła pomaga podjąć działania naprawcze, aby upewnić się, że hasła dla wszystkich kont użytkowników maszyn wirtualnych są zgodne z zasadami.

- \[Podgląd:\]Inspekcja maszyn wirtualnych systemu Windows, które nie mają maksymalnego wieku hasła 70 dni
- \[Wersja\]zapoznawcza : Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które nie mają maksymalnego wieku hasła 70 dni
- \[Podgląd\]: Inspekcja maszyn wirtualnych systemu Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Podgląd\]: Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Podgląd\]: Inspekcja maszyn wirtualnych systemu Windows, które umożliwiają ponowne użycie poprzednich 24 haseł
- \[Wersja\]zapoznawcza : Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które umożliwiają ponowne użycie poprzednich 24 haseł

## <a name="103-and-1054-audit-generation"></a>10.3 i 10.5.4 Generowanie audytu

Ten plan pomaga upewnić się, że zdarzenia systemowe są rejestrowane przez przypisanie definicji [usługi Azure Policy,](../../../policy/overview.md) które inspekcji ustawień dziennika zasobów platformy Azure.
Dzienniki diagnostyczne zapewniają wgląd w operacje, które zostały wykonane w ramach zasobów platformy Azure. Dzienniki platformy Azure opierają się na zsynchronizowanych zegarach wewnętrznych, aby utworzyć skorelowany rekord zdarzeń w zasobach.

- Inspekcja powinna być włączona w przypadku zaawansowanych ustawień zabezpieczeń danych na programie SQL Server
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji na poziomie serwera SQL
- Wdrażanie inspekcji na serwerach SQL
- Konta magazynu powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager
- Maszyny wirtualne powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>12.3.6 i 12.3.7 Bezpieczeństwo informacji

Ten plan ułatwia zarządzanie siecią i kontrolowanie jej przez przypisywanie definicji [zasad platformy Azure,](../../../policy/overview.md) które kontrolują dopuszczalne lokalizacje sieciowe i zatwierdzone produkty firmy dozwolone dla środowiska. Są one dostosowywane przez każdą firmę za pomocą parametrów zasad w ramach każdej z tych zasad.

- Dozwolone lokalizacje
- Dozwolone lokalizacje dla grup zasobów

## <a name="next-steps"></a>Następne kroki

Teraz, gdy przejrzano mapowanie sterowania planu PCI-DSS w wersji 3.2.1, odwiedź następujące artykuły, aby dowiedzieć się więcej o omówieniu i sposobie wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Plan PCI-DSS v3.2.1 — omówienie](./index.md)
> [planu PCI-DSS v3.2.1 — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
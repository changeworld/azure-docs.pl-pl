---
title: Uk OFFICIAL & UK NHS plan kontroli przykładowych
description: Mapowanie kontrolne próbek planu brytyjskiego OFICJALNEGO i brytyjskiego NHS. Każdy formant jest mapowany na jedną lub więcej zasad platformy Azure, które pomagają w ocenie.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851370"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mapowanie kontrolne próbek planu brytyjskiego OFICJALNEGO i brytyjskiego NHS

Poniżej dalsza część artykułu W tym artykule opisano, jak brytyjski oficjalny i brytyjski plan nhs mapuje do kontroli brytyjskiego oficjalnego i brytyjskiego NHS. Aby uzyskać więcej informacji na temat kontroli, zobacz [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Poniższe mapowania są do **brytyjskiego oficjalnego** i **brytyjskiego nhs** kontroli. Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania sterowania. Wiele mapowanych formantów są implementowane za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[inspekcję podglądu\] UK OFFICIAL i UK NHS kontroli i wdrożyć określone rozszerzenia maszyn wirtualnych do obsługi wymagań inspekcji** wbudowanej inicjatywy polityki.

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności zawiera formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Dane w ochronie tranzytu

Plan pomaga zapewnić, że transfer informacji za pomocą usług platformy Azure jest bezpieczny, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które inspekcji niezabezpieczonych połączeń do kont magazynu i pamięci podręcznej Redis.

- Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis
- Należy włączyć bezpieczny transfer na konta magazynu
- Pokaż wyniki inspekcji z serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Wdrażanie wymagań wstępnych do inspekcji serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API
- Najnowsza wersja TLS powinna być używana w aplikacji sieci Web
- Najnowsza wersja TLS powinna być używana w aplikacji funkcji

## <a name="23-data-at-rest-protection"></a>2.3 Ochrona danych w spoczynku

Ten plan ułatwia wymuszanie zasad dotyczących używania formantów kryptograficznych przez przypisanie definicji [zasad platformy Azure,](../../../policy/overview.md) które wymuszają określone formanty kryptografów i inspekcji użycia słabych ustawień kryptograficznych.
Zrozumienie, gdzie zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podjęcie działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. W szczególności zasady przypisane przez ten plan wymagają szyfrowania dla kont magazynu usługi data lake; wymagać przejrzystego szyfrowania danych w bazach danych SQL; inspekcja brakującego szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych kont automatyzacji; inspekcji niezabezpieczonych połączeń z kontami magazynu i pamięcią podręczną Redis; audyt słabe szyfrowanie haseł maszyny wirtualnej; i inspekcji niezaszyfrowanej sieci szkieletowej usług.

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- Zmienne konta automatyzacji powinny być szyfrowane
- Klastry sieci szkieletowej usług powinny mieć właściwość ClusterProtectionLevel ustawiona na EncryptAndSign
- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL
- Wdrażanie przezroczystego szyfrowania danych w u źródła danych SQL DB
- Wymagaj szyfrowania na kontach usługi Data Lake Store
- Dozwolone lokalizacje (zostały zakodowane na twardą dysk -"UK SOUTH" i "UK WEST")
- Dozwolone lokalizacje dla grup zasobów (zostały zakodowane na twardą pozycję "UK SOUTH" i "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 Zarządzanie lukami w zabezpieczeniach

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które monitorują brak ochrony punktów końcowych, brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, luki w zabezpieczeniach SQL i luki w zabezpieczeniach maszyny wirtualnej. Te szczegółowe informacje zawierają informacje w czasie rzeczywistym o stanie zabezpieczeń wdrożonych zasobów i mogą pomóc w nakreśleniu priorytetów działań naprawczych.

- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center
- Należy zainstalować aktualizacje systemu na maszynach
- Należy zainstalować aktualizacje systemu w zestawach skalowania maszyny wirtualnej
- Luki w zabezpieczeniach konfiguracji na komputerach powinny zostać naprawione
- Luki w bazach danych SQL powinny zostać naprawione
- Luki w zabezpieczeniach powinny zostać naprawione przez rozwiązanie do oceny luk w zabezpieczeniach
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych SQL
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL

## <a name="53-protective-monitoring"></a>5.3 Monitorowanie ochronne

Ten plan ułatwia ochronę zasobów systemu informacyjnego, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które zapewniają monitorowanie ochrony w zakresie nieograniczonego dostępu, zezwalaj na działanie listy i zagrożenia.

- Inspekcja nieograniczonego dostępu do kont magazynu
- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych
- Inspekcja maszyn wirtualnych bez skonfigurowania odzyskiwania po awarii
- Standard ochrony przed atakami DDoS powinien być włączony
- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystkie" w przypadku zarządzanym SQL ustawienia zaawansowanego zabezpieczenia danych
- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych serwera SQL
- Wdrażanie wykrywania zagrożeń na serwerach SQL
- Wdrażanie domyślnego rozszerzenia Microsoft IaaSAntimalware dla systemu Windows Server

## <a name="9-secure-user-management"></a>9 Bezpieczne zarządzanie użytkownikiem 

Platforma Azure implementuje kontrolę dostępu opartą na rolach (RBAC), aby pomóc w zarządzaniu, kto ma dostęp do zasobów na platformie Azure. Korzystając z witryny Azure Portal, można przejrzeć, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan pomaga ograniczyć i kontrolować prawa dostępu, przypisując definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub odczytu/zapisu i kont z uprawnieniami właściciela, odczytu i/lub zapisu, które nie mają włączonego uwierzytelniania wieloskładnikowego.

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

## <a name="10-identity-and-authentication"></a>10 Tożsamość i uwierzytelnianie

Ten plan pomaga ograniczyć i kontrolować prawa dostępu, przypisując definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub odczytu/zapisu i kont z uprawnieniami właściciela, odczytu i/lub zapisu, które nie mają włączonego uwierzytelniania wieloskładnikowego.

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

Ten plan przypisuje definicje zasad platformy Azure do inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL i sieci szkieletowej usług. Korzystanie z uwierzytelniania usługi Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami i scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft.

- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL
- Klastry sieci szkieletowej usług powinny używać tylko usługi Azure Active Directory do uwierzytelniania klienta

Ten plan przypisuje również definicje zasad platformy Azure do inspekcji kont, które powinny być traktowane priorytetowo do przeglądu, w tym zamortyzowane konta i konta zewnętrzne. W razie potrzeby konta mogą być blokowane przed logowaniem się (lub usuwaniem), co natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie definicje zasad platformy Azure do inspekcji konta zamortyzowanego, które należy wziąć pod uwagę do usunięcia.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje również definicję zasad platformy Azure, która przeprowadza inspekcje uprawnień do pliku haseł maszyny Wirtualnej systemu Linux w celu alertowania, jeśli są one ustawione niepoprawnie. Ten projekt umożliwia podjęcie działań naprawczych w celu zapewnienia, że wystawcy uwierzytelnienia nie są zagrożone.

- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Linux, które nie mają uprawnień do pliku passwd ustawionego na 0644

Ten plan pomaga wymusić silne hasła, przypisując definicje zasad platformy Azure, które są poddane inspekcji maszyn wirtualnych systemu Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących haseł. Świadomość maszyn wirtualnych z naruszeniem zasad siły hasła pomaga podjąć działania naprawcze, aby upewnić się, że hasła dla wszystkich kont użytkowników maszyn wirtualnych są zgodne z zasadami.

- \[Podgląd\]: Wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych systemu Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Podgląd\]: Wdrażanie wymagań wstępnych do inspekcji maszyn wirtualnych systemu Windows, które nie mają maksymalnego wieku hasła 70 dni
- \[Podgląd\]: Wdrażanie wymagań wstępnych do inspekcji maszyn wirtualnych systemu Windows, które nie mają minimalnego wieku hasła 1 dnia
- \[Podgląd\]: Wdrażanie wymagań wstępnych do inspekcji maszyn wirtualnych systemu Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Wersja\]zapoznawcza : Wdrażanie wymagań wstępnych do inspekcji maszyn wirtualnych systemu Windows, które umożliwiają ponowne użycie poprzednich 24 haseł
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które nie mają maksymalnego wieku hasła 70 dni
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które nie mają minimalnego wieku hasła 1 dnia
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które umożliwiają ponowne użycie poprzednich 24 haseł

Ten plan ułatwia również kontrolowanie dostępu do zasobów platformy Azure przez przypisywanie definicji zasad platformy Azure. Te zasady inspekcji użycia typów zasobów i konfiguracji, które mogą zezwalać na bardziej dopuszczalny dostęp do zasobów. Zrozumienie zasobów, które naruszają te zasady, może pomóc w podjęcie działań naprawczych w celu zapewnienia, że dostęp do zasobów platformy Azure jest ograniczony do autoryzowanych użytkowników.

- \[Wersja\]zapoznawcza : Wdrażanie wymagań w celu inspekcji maszyn wirtualnych z systemem Linux, które mają konta bez haseł
- \[Wersja\]zapoznawcza: Wdrażanie wymagań w celu inspekcji maszyn wirtualnych z systemem Linux, które zezwalają na zdalne połączenia z kont bez haseł
- \[Podgląd\]: Inspekcja maszyn wirtualnych z systemem Linux, które mają konta bez haseł
- \[Podgląd\]: Inspekcja maszyn wirtualnych z systemem Linux, które umożliwiają zdalne połączenia z kont bez haseł
- Konta magazynu powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager
- Maszyny wirtualne powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager
- Inspekcja maszyn wirtualnych, które nie używają dysków zarządzanych

## <a name="11-external-interface-protection"></a>11 Ochrona interfejsu zewnętrznego

Poza użyciem więcej niż 25 zasad do odpowiedniego bezpiecznego zarządzania użytkownikami ten plan pomaga chronić interfejsy usługi przed nieautoryzowanym dostępem, przypisując definicję [zasad platformy Azure,](../../../policy/overview.md) która monitoruje nieograniczone konta magazynu. Konta magazynu z nieograniczonym dostępem mogą umożliwiać niezamierzony dostęp do informacji zawartych w systemie informacyjnym. Ten plan przypisuje również zasady, która umożliwia adaptacyjne formantów aplikacji na maszynach wirtualnych.

- Inspekcja nieograniczonego dostępu do kont magazynu
- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych
- Zasady nsgs dla aplikacji internetowych na IaaS powinny być zaostrzone
- Dostęp przez punkt końcowy skierowany do Internetu powinien być ograniczony
- Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych z widokiem na Internet powinny zostać wzmocnione
- Rozwiązanie ochrony punktów końcowych powinno być zainstalowane w zestawach skalowania maszyny wirtualnej
- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych
- Inspekcja nieograniczonego dostępu do kont magazynu
- Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji
- Zdalne debugowanie powinno być wyłączone dla aplikacji sieci Web
- Zdalne debugowanie powinno być wyłączone dla aplikacji interfejsu API
- Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja function powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja API powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="12-secure-service-administration"></a>12 Bezpieczna administracja serwisem

Platforma Azure implementuje kontrolę dostępu opartą na rolach (RBAC), aby pomóc w zarządzaniu, kto ma dostęp do zasobów na platformie Azure. Korzystając z witryny Azure Portal, można przejrzeć, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan pomaga ograniczyć i kontrolować uprzywilejowane prawa dostępu, przypisując pięć definicji [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub zapisu i kont z właścicielem i/lub uprawnienia do zapisu, które nie mają włączonego uwierzytelniania wieloskładnikowego.

Systemy używane do administrowania usługą w chmurze będą miały wysoce uprzywilejowany dostęp do tej usługi. Ich kompromis miałby znaczący wpływ, w tym środki do ominięcia kontroli bezpieczeństwa i kradzieży lub manipulowania dużymi ilościami danych. Metody stosowane przez administratorów usługodawcy do zarządzania usługą operacyjną powinny być zaprojektowane w celu ograniczenia ryzyka wykorzystania, które mogłoby zagrozić bezpieczeństwu usługi. Jeśli ta zasada nie zostanie zaimplementowana, osoba atakująca może mieć środki do ominięcia kontroli zabezpieczeń i kradzieży lub manipulowania dużymi ilościami danych.

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje definicje zasad platformy Azure do inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL i sieci szkieletowej usług. Korzystanie z uwierzytelniania usługi Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami i scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft.

- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL
- Klastry sieci szkieletowej usług powinny używać tylko usługi Azure Active Directory do uwierzytelniania klienta

Ten plan przypisuje również definicje zasad platformy Azure do inspekcji kont, które powinny być traktowane priorytetowo do przeglądu, w tym zamortyzowane konta i konta zewnętrzne z podwyższonymi uprawnieniami. W razie potrzeby konta mogą być blokowane przed logowaniem się (lub usuwaniem), co natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje dwie definicje zasad platformy Azure do inspekcji konta zamortyzowanego, które należy wziąć pod uwagę do usunięcia.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje również definicję zasad platformy Azure, która przeprowadza inspekcje uprawnień do pliku haseł maszyny Wirtualnej systemu Linux w celu alertowania, jeśli są one ustawione niepoprawnie. Ten projekt umożliwia podjęcie działań naprawczych w celu zapewnienia, że wystawcy uwierzytelnienia nie są zagrożone.

- \[Podgląd\]: Inspekcja Linux VM /etc/passwd uprawnienia do plików są ustawione na 0644

## <a name="13-audit-information-for-users"></a>13 Informacje z audytu dla użytkowników

Ten plan pomaga upewnić się, że zdarzenia systemowe są rejestrowane przez przypisanie definicji [usługi Azure Policy,](../../../policy/overview.md) które inspekcji ustawień dziennika zasobów platformy Azure. Przypisane zasady również inspekcji, jeśli maszyny wirtualne nie wysyłają dzienniki do określonego obszaru roboczego analizy dziennika.

- Inspekcja powinna być włączona w przypadku zaawansowanych ustawień zabezpieczeń danych na programie SQL Server
- Przeprowadzanie inspekcji ustawienia diagnostyki
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows
- Wdrażanie obserwatora sieci podczas tworzenia sieci wirtualnych

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontrolnego planów BRYTYJSKIEGO OFICJALNEGO i BRYTYJSKIEGO NHS odwiedź następujące artykuły, aby dowiedzieć się więcej o przeglądzie i sposobie wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Uk OFFICIAL i UK NHS plany - Przegląd](./index.md)
> [UK OFICJALNE i UK NHS plany - Wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

---
title: Kanada Federalna PBMM plan kontroli przykładowych
description: Mapowanie kontrolne próbek planu Federalnego PBMM w Kanadzie. Każdy formant jest mapowany na jedną lub więcej zasad platformy Azure, które pomagają w ocenie.
ms.date: 09/04/2019
ms.topic: sample
ms.openlocfilehash: 943ca6849f9a55ce45b609a06cfcbb9b96b691c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545255"
---
# <a name="control-mapping-of-the-canada-federal-pbmm-blueprint-sample"></a>Mapowanie kontrolne przykładowego planu federalnego PBMM w Kanadzie

W poniższym artykule opisano, jak plan planu Azure Blueprints Canada Federal Protected B, Medium Integrity, Medium Availability (PBMM) jest mapowany na formanty kanadyjskiego federalnego pbmm. Aby uzyskać więcej informacji na temat kontroli, zobacz [Kanada Federal PBMM](https://www.canada.ca/en/government/system/digital-government/modern-emerging-technologies/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html#toc4).

Poniższe mapowania są do **Kanada Federalnej PBMM** kontroli. Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania sterowania. Wiele mapowanych formantów są implementowane za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[podgląd:\]Inspekcja Kanada Federalna PBMM kontroluje** wbudowaną inicjatywę polityki.

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności zawiera formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/canada-federal-pbmm/control-mapping.md).

## <a name="location-constraints"></a>Ograniczenia lokalizacji

Ten plan pomaga ograniczyć lokalizację wdrażania wszystkich zasobów i grup zasobów do "Canada Central" i "Canada East", przypisując następujące definicje zasad platformy Azure:

- Dozwolone lokalizacje (zostały zakodowane na twardo do "Canada Central" i "Canada East")
- Dozwolone lokalizacje dla grup zasobów (zostały zakodowane na twardą potrzeby w "Canada Central" i "Canada East")

## <a name="ac-2-account-management"></a>Zarządzanie kontem AC-2

Ten plan ułatwia przeglądanie kont, które mogą nie spełniać wymagań dotyczących zarządzania kontami organizacji. Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które inspekcji kont zewnętrznych z odczytu, zapisu i uprawnień właściciela na subskrypcji i przestarzałe konta. Przeglądając konta skontrolowane przez te zasady, można podjąć odpowiednie działania, aby upewnić się, że wymagania dotyczące zarządzania kontem są spełnione.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji


## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Zarządzanie kontem | Schematy oparte na rolach

Platforma Azure implementuje [kontrolę dostępu opartą na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie tym, kto ma dostęp do zasobów na platformie Azure. Korzystając z witryny Azure Portal, można przejrzeć, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje również definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji użycia uwierzytelniania usługi Azure Active Directory dla serwerów SQL i sieci szkieletowej usług. Korzystanie z uwierzytelniania usługi Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami i scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. Ponadto ten plan przypisuje definicji zasad platformy Azure do inspekcji użycia niestandardowych reguł RBAC. Zrozumienie, gdzie są implementowane niestandardowe reguły RBAC, może pomóc w weryfikacji potrzeb i prawidłowej implementacji, ponieważ niestandardowe reguły RBAC są podatne na błędy.

- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL
- Klastry sieci szkieletowej usług powinny używać tylko usługi Azure Active Directory do uwierzytelniania klienta

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Egzekwowanie przepływu informacji

Udostępnianie zasobów między źródłami (CORS) umożliwia żądanie zasobów usług App Services z domeny zewnętrznej. Firma Microsoft zaleca zezwalanie tylko wymaganym domenom na interakcję z interfejsem API, funkcją i aplikacjami sieci Web. Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) aby ułatwić monitorowanie ograniczeń dostępu do zasobów CORS w usłudze Azure Security Center.
Zrozumienie implementacji CORS może pomóc w weryfikacji, czy formanty przepływu informacji są implementowane.

- CORS nie powinien zezwalać każdemu zasobowi na dostęp do aplikacji sieci Web

## <a name="ac-5-separation-of-duties"></a>AC-5 Rozdzielenie ceł

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie zezwala na nadmiarowość administracyjną. Z drugiej strony posiadanie zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć ryzyko naruszenia zabezpieczeń za pośrednictwem konta właściciela, których bezpieczeństwo zostało naruszone. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które inspekcji liczbę właścicieli subskrypcji platformy Azure. Ten plan przypisuje również definicje zasad platformy Azure, które ułatwiają kontrolowanie członkostwa w grupie Administratorzy na maszynach wirtualnych systemu Windows. Zarządzanie uprawnieniami administratora właściciela subskrypcji i administratora maszyny wirtualnej może pomóc w zaimplementowanie odpowiedniego rozdzielenia obowiązków.

- Do subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Inspekcja maszyn wirtualnych systemu Windows, w których grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Inspekcja maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, w których grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdrażanie wymagań dotyczących inspekcji maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich

## <a name="ac-6-least-privilege"></a>Ac-6 Najmniejszy przywilej

Platforma Azure implementuje [kontrolę dostępu opartą na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie tym, kto ma dostęp do zasobów na platformie Azure. Korzystając z witryny Azure Portal, można przejrzeć, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje definicje [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont, które powinny być traktowane priorytetowo do przeglądu. Przeglądanie tych wskaźników konta może pomóc w zapewnieniu, że zaimplementowano kontrole najmniejszych uprawnień.

- Do subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Inspekcja maszyn wirtualnych systemu Windows, w których grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Inspekcja maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich
- Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, w których grupa Administratorzy zawiera dowolny z określonych elementów członkowskich
- Wdrażanie wymagań dotyczących inspekcji maszyn wirtualnych systemu Windows, w których grupa Administratorzy nie zawiera wszystkich określonych elementów członkowskich

## <a name="ac-7-security-attributes"></a>Atrybuty zabezpieczeń AC-7

Odnajdowanie danych i możliwości klasyfikacji zaawansowanych zabezpieczeń danych dla usługi Azure SQL Database zapewnia możliwości odnajdowania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami. Zaawansowane zabezpieczenia danych mogą pomóc w zapewnieniu informacji skojarzonych z odpowiednimi atrybutami zabezpieczeń dla organizacji. Ten plan przypisuje definicje [zasad platformy Azure](../../../policy/overview.md) do monitorowania i wymuszania korzystania z zaawansowanych zabezpieczeń danych na serwerze SQL.

- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych SQL
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL
- Wdrażanie zaawansowanych zabezpieczeń danych na serwerach SQL

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Dostęp zdalny | Automatyczne monitorowanie / sterowanie

Ten plan ułatwia monitorowanie i kontrolowanie zdalnego dostępu przez przypisanie definicji [zasad platformy Azure](../../../policy/overview.md) w celu monitorowania, że zdalne debugowanie dla aplikacji usługi Azure App Service jest wyłączone. Plan przypisuje również definicje zasad, które inspekcji maszyn wirtualnych systemu Linux, które umożliwiają połączenia zdalne z kont bez haseł. Ponadto plan przypisuje definicji zasad platformy Azure, która ułatwia monitorowanie nieograniczonego dostępu do kont magazynu. Monitorowanie tych wskaźników może pomóc w zapewnieniu zgodności metod dostępu zdalnego z zasadami zabezpieczeń.

- \[Podgląd\]: Inspekcja maszyn wirtualnych z systemem Linux, które umożliwiają zdalne połączenia z kont bez haseł
- \[Wersja\]zapoznawcza: Wdrażanie wymagań w celu inspekcji maszyn wirtualnych z systemem Linux, które zezwalają na zdalne połączenia z kont bez haseł
- Inspekcja nieograniczonego dostępu do kont magazynu
- Zdalne debugowanie powinno być wyłączone dla aplikacji interfejsu API
- Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji
- Zdalne debugowanie powinno być wyłączone dla aplikacji sieci Web

## <a name="au-3-2-content-of-audit-records"></a>AU-3 (2) Treść dokumentacji audytowej

Dane dziennika zebrane przez usługę Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics, umożliwiając scentralizowaną konfigurację i zarządzanie. Ten plan pomaga upewnić się, że zdarzenia są rejestrowane przez przypisanie definicji [zasad platformy Azure,](../../../policy/overview.md) które inspekcji i wymuszania wdrożenia agenta usługi Log Analytics na maszynach wirtualnych platformy Azure.

- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Obszar roboczy analizy dzienników inspekcji dla maszyny Wirtualnej — niezgodność raportu
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows

## <a name="au-5-response-to-audit-processing-failures"></a>Odpowiedź AU-5 na błędy przetwarzania inspekcji

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które monitorują konfiguracje inspekcji i rejestrowania zdarzeń. Monitorowanie tych konfiguracji może stanowić wskaźnik awarii lub błędnej konfiguracji systemu inspekcji i pomóc w podjęcia działań naprawczych.

- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji na poziomie serwera SQL
- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Przegląd audytu, analiza i sprawozdawczość | Centralny przegląd i analiza

Dane dziennika zebrane przez usługę Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics, umożliwiając scentralizowane raportowanie i analizę. Ten plan pomaga upewnić się, że zdarzenia są rejestrowane przez przypisanie definicji [zasad platformy Azure,](../../../policy/overview.md) które inspekcji i wymuszania wdrożenia agenta usługi Log Analytics na maszynach wirtualnych platformy Azure.

- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Obszar roboczy analizy dzienników inspekcji dla maszyny Wirtualnej — niezgodność raportu
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows

## <a name="au-12-audit-generation"></a>Generowanie audytu AU-12

Ten plan pomaga upewnić się, że zdarzenia systemowe są rejestrowane przez przypisanie definicji [usługi Azure Policy,](../../../policy/overview.md) które inspekcji ustawień dziennika zasobów platformy Azure. Te definicje zasad inspekcji i wymuszania wdrażania agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i konfiguracji ustawień inspekcji dla innych typów zasobów platformy Azure. Te definicje zasad również inspekcji konfiguracji dzienników diagnostycznych, aby zapewnić wgląd w operacje, które są wykonywane w ramach zasobów platformy Azure. Ponadto inspekcja i zaawansowane zabezpieczenia danych są konfigurowane na serwerach SQL.

- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Obszar roboczy analizy dzienników inspekcji dla maszyny Wirtualnej — niezgodność raportu
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji na poziomie serwera SQL
- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL
- Wdrażanie zaawansowanych zabezpieczeń danych na serwerach SQL
- Wdrażanie inspekcji na serwerach SQL
- Wdrażanie ustawień diagnostycznych dla grup zabezpieczeń sieci

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Najmniej funkcjonalność | Autoryzowane oprogramowanie / Biała lista

Adaptacyjna kontrola aplikacji w usłudze Azure Security Center to inteligentne, automatyczne kompleksowe rozwiązanie do umieszczania aplikacji na białej liście, które może blokować lub uniemożliwiać uruchamianie określonego oprogramowania na maszynach wirtualnych. Kontrola aplikacji ułatwia tworzenie list zatwierdzonych aplikacji dla maszyn wirtualnych. Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga monitorować maszyn wirtualnych, gdzie biała lista aplikacji jest zalecane, ale nie został jeszcze skonfigurowany.

- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych

## <a name="cm-11-user-installed-software"></a>Oprogramowanie CM-11 zainstalowane przez użytkownika

Adaptacyjna kontrola aplikacji w usłudze Azure Security Center to inteligentne, automatyczne kompleksowe rozwiązanie do umieszczania aplikacji na białej liście, które może blokować lub uniemożliwiać uruchamianie określonego oprogramowania na maszynach wirtualnych. Kontrola aplikacji może pomóc w egzekwowaniu i monitorowaniu zgodności z zasadami ograniczeń oprogramowania. Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga monitorować maszyn wirtualnych, gdzie biała lista aplikacji jest zalecane, ale nie został jeszcze skonfigurowany.

- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych

## <a name="cp-7-alternate-processing-site"></a>Cp-7 Alternatywna strona przetwarzania

Usługa Azure Site Recovery replikuje obciążenia uruchomione na maszynach wirtualnych z lokalizacji podstawowej do lokalizacji dodatkowej. Jeśli awaria występuje w lokacji głównej, obciążenie kończy się niepowodzeniem w lokalizacji pomocniczej. Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która przeprowadza inspekcje maszyn wirtualnych bez skonfigurowania odzyskiwania po awarii. Monitorowanie tego wskaźnika może pomóc w zapewnieniu niezbędnych kontroli awaryjnych.

- Inspekcja maszyn wirtualnych bez skonfigurowania odzyskiwania po awarii

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identyfikacja i uwierzytelnianie (użytkownicy organizacji) | Dostęp sieciowy do kont uprzywilejowanych

Ten plan ułatwia ograniczanie i kontrolowanie uprzywilejowanego dostępu przez przypisywanie definicji [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont z uprawnieniami właściciela i/lub zapisu, które nie mają włączonego uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga chronić konta, nawet jeśli jedna część informacji uwierzytelniania zostanie naruszona. Monitorując konta bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej narażone na zagrożenia.

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami do zapisu w ramach subskrypcji

## <a name="ia-5-authenticator-management"></a>Zarządzanie uwierzytelniaczem IA-5

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które inspekcji maszyn wirtualnych systemu Linux, które umożliwiają połączenia zdalne z kont bez haseł i/lub mają nieprawidłowe uprawnienia ustawione na plik passwd. Ten plan przypisuje również definicje zasad, które są inspekcji konfiguracji typu szyfrowania haseł dla maszyn wirtualnych systemu Windows. Monitorowanie tych wskaźników pomaga zapewnić zgodność wystawców uwierzytelniających system z zasadami identyfikacji i uwierzytelniania organizacji.

- \[Podgląd\]: Inspekcja maszyn wirtualnych z systemem Linux, które nie mają uprawnień do pliku passwd ustawionego na 0644
- \[Podgląd\]: Inspekcja maszyn wirtualnych z systemem Linux, które mają konta bez haseł
- \[Podgląd:\]Wdrażanie wymagań w celu inspekcji maszyn wirtualnych z systemem Linux, które nie mają uprawnień do pliku passwd ustawionego na 0644
- \[Wersja\]zapoznawcza : Wdrażanie wymagań w celu inspekcji maszyn wirtualnych z systemem Linux, które mają konta bez haseł

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Zarządzanie uwierzytelniaczem | Uwierzytelnianie oparte na hasłach

Ten plan pomaga wymusić silne hasła, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które są poddane inspekcji maszynom wirtualnym systemu Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących haseł. Świadomość maszyn wirtualnych z naruszeniem zasad siły hasła pomaga podjąć działania naprawcze w celu zapewnienia, że hasła dla wszystkich kont użytkowników maszyn wirtualnych są zgodne z zasadami haseł organizacji.

- \[Podgląd\]: Inspekcja maszyn wirtualnych systemu Windows, które umożliwiają ponowne użycie poprzednich 24 haseł
- \[Podgląd:\]Inspekcja maszyn wirtualnych systemu Windows, które nie mają maksymalnego wieku hasła 70 dni
- \[Podgląd:\]Inspekcja maszyn wirtualnych systemu Windows, które nie mają minimalnego wieku hasła 1 dnia
- \[Podgląd:\]Inspekcja maszyn wirtualnych systemu Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Podgląd\]: Inspekcja maszyn wirtualnych systemu Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Wersja\]zapoznawcza : Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które umożliwiają ponowne użycie poprzednich 24 haseł
- \[Wersja\]zapoznawcza : Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które nie mają maksymalnego wieku hasła 70 dni
- \[Podgląd\]: Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które nie mają minimalnego wieku hasła 1 dnia
- \[Wersja\]zapoznawcza : Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które nie mają włączonego ustawienia złożoności hasła
- \[Podgląd\]: Wdrażanie wymagań w celu inspekcji maszyn wirtualnych systemu Windows, które nie ograniczają minimalnej długości hasła do 14 znaków

## <a name="ia-8-100-identification-and-authentication-non-organizational-users--identity-and-credential-assurance-levels"></a>IA-8 (100) Identyfikacja i uwierzytelnianie (użytkownicy nieorganizowicy) | Poziomy zapewnienia tożsamości i poświadczeń

Ten plan ułatwia ograniczanie i kontrolowanie uprzywilejowanego dostępu przez przypisywanie definicji [zasad platformy Azure](../../../policy/overview.md) do inspekcji kont z uprawnieniami właściciela i/lub zapisu, które nie mają włączonego uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga chronić konta, nawet jeśli jedna część informacji uwierzytelniania zostanie naruszona. Monitorując konta bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej narażone na zagrożenia.

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami do zapisu w ramach subskrypcji

## <a name="ra-5-vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach RA-5

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które monitorują luki w zabezpieczeniach systemu operacyjnego, luki w zabezpieczeniach SQL i luki w zabezpieczeniach maszyny wirtualnej w usłudze Azure Security Center. Usługa Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń wdrożonych zasobów platformy Azure. Ten plan przypisuje również definicje zasad, które są poddane inspekcji i wymuszaniu zaawansowanego bezpieczeństwa danych na serwerach SQL. Zaawansowane zabezpieczenia danych obejmowały ocenę luk w zabezpieczeniach i zaawansowane funkcje ochrony przed zagrożeniami, które pomagają zrozumieć luki w wdrożonych zasobach.

- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL
- Wdrażanie zaawansowanych zabezpieczeń danych na serwerach SQL
- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Luki w zabezpieczeniach konfiguracji maszyn wirtualnych powinny zostać naprawione
- Luki w bazach danych SQL powinny zostać naprawione
- Luki w zabezpieczeniach powinny zostać naprawione przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="sc-5-denial-of-service-protection"></a>Ochrona przed atakami typu "odmowa usługi" w sc-5

Warstwa standardowa typu "rozproszona odmowa usługi DDoS) platformy Azure zapewnia dodatkowe funkcje i możliwości ograniczania ryzyka w podstawowej warstwie usług. Te dodatkowe funkcje obejmują integrację usługi Azure Monitor i możliwość przeglądania raportów ograniczających zagrożenie po ataku. Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która przeprowadza inspekcje, jeśli warstwa standardowa DDoS jest włączona. Zrozumienie różnicy w możliwościach między warstwami usług może pomóc w wyborze najlepszego rozwiązania do obsługi zabezpieczeń typu "odmowa usługi" dla środowiska platformy Azure.

- Standard ochrony przed atakami DDoS powinien być włączony

## <a name="sc-7-boundary-protection"></a>Ochrona granic SC-7

Ten plan ułatwia zarządzanie i kontrolowanie granic systemu przez przypisanie definicji [zasad platformy Azure,](../../../policy/overview.md) która monitoruje zalecenia dotyczące wzmacniania grup zabezpieczeń sieciowych w usłudze Azure Security Center. Usługa Azure Security Center analizuje wzorce ruchu maszyn wirtualnych z widokiem na Internet i udostępnia zalecenia dotyczące reguł grupy zabezpieczeń sieci, aby zmniejszyć potencjalną powierzchnię ataku.
Ponadto ten plan przypisuje również definicje zasad, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, oraz konta magazynu z nieograniczonym dostępem mogą zezwalać na niezamierzony dostęp do informacji zawartych w systemie informacyjnym.

- Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych z widokiem na Internet powinny zostać wzmocnione
- Dostęp przez punkt końcowy skierowany do Internetu powinien być ograniczony
- Inspekcja nieograniczonego dostępu do kont magazynu
- Zasady nsgs dla aplikacji internetowych na IaaS powinny być zaostrzone

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Ochrona granic | Punkty dostępu

Dostęp do maszyny wirtualnej just-in-time (JIT) blokuje ruch przychodzący na maszynach wirtualnych platformy Azure, zmniejszając narażenie na ataki, zapewniając jednocześnie łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Dostęp do maszyny wirtualnej JIT pomaga ograniczyć liczbę połączeń zewnętrznych do zasobów na platformie Azure. Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just-in-time, ale nie zostały jeszcze skonfigurowane.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Ochrona granic | Zewnętrzne usługi telekomunikacyjne

Dostęp do maszyny wirtualnej just-in-time (JIT) blokuje ruch przychodzący na maszynach wirtualnych platformy Azure, zmniejszając narażenie na ataki, zapewniając jednocześnie łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Dostęp do maszyny wirtualnej JIT ułatwia zarządzanie wyjątkami od zasad przepływu ruchu, ułatwiając procesy żądania dostępu i zatwierdzania. Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just-in-time, ale nie zostały jeszcze skonfigurowane.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Poufność i integralność transmisji | Kryptograficzna lub alternatywna ochrona fizyczna

Ten plan pomaga chronić poufne i integralność przesyłanych informacji, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które ułatwiają monitorowanie mechanizmu kryptograficznego zaimplementowanego dla protokołów komunikacyjnych. Zapewnienie prawidłowego szyfrowania komunikacji może pomóc w spełnieniu wymagań organizacji lub ochronie informacji przed nieautoryzowanym ujawnieniem i modyfikacją.

- Aplikacja API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Inspekcja serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Wdrażanie wymagań w celu inspekcji serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Aplikacja function powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć tylko bezpieczne połączenia z pamięcią podręczną Redis
- Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć bezpieczny transfer na konta magazynu

## <a name="sc-28-1-protection-of-information-at-rest"></a>SC-28 (1) Ochrona informacji w spoczynku

Ten plan ułatwia wymuszanie zasad dotyczących używania formantów kryptograficznych w celu ochrony informacji w stanie spoczynku, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które wymuszają określone formanty kryptografów i inspekcji użycia słabych ustawień kryptograficznych. Zrozumienie, gdzie zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podjęcie działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. W szczególności definicje zasad przypisane przez ten plan wymagają szyfrowania dla kont magazynu usługi data lake; wymagać przejrzystego szyfrowania danych w bazach danych SQL; i inspekcji brak szyfrowania w bazach danych SQL, dyskach maszyn wirtualnych i zmiennych konta automatyzacji.

- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL
- Wdrażanie zaawansowanych zabezpieczeń danych na serwerach SQL
- Wdrażanie przezroczystego szyfrowania danych w u źródła danych SQL DB
- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- Wymagaj szyfrowania na kontach usługi Data Lake Store
- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL

## <a name="si-2-flaw-remediation"></a>Usuwanie błędów SI-2

Ten plan ułatwia zarządzanie błędami systemu informacyjnego, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które monitorują brakujące aktualizacje systemu, luki w systemie operacyjnym, luki w zabezpieczeniach SQL i luki w zabezpieczeniach maszyny wirtualnej w usłudze Azure Security Center. Usługa Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń wdrożonych zasobów platformy Azure. Ten plan przypisuje również definicję zasad, która zapewnia łatanie systemu operacyjnego dla zestawów skalowania maszyny wirtualnej.

- Wymagaj automatycznego poprawiania obrazu systemu operacyjnego w zestawach skalowania maszyn wirtualnych
- Należy zainstalować aktualizacje systemu w zestawach skalowania maszyny wirtualnej
- Aktualizacje systemu powinny być zainstalowane na maszynach wirtualnych
- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Luki w zabezpieczeniach konfiguracji maszyn wirtualnych powinny zostać naprawione
- Luki w bazach danych SQL powinny zostać naprawione
- Luki w zabezpieczeniach powinny zostać naprawione przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="si-3-malicious-code-protection"></a>Ochrona przed złośliwym kodem SI-3

Ten plan ułatwia zarządzanie ochroną punktów końcowych, w tym ochronę przed złośliwym kodem, przez przypisanie definicji [zasad platformy Azure,](../../../policy/overview.md) które monitorują brak ochrony punktów końcowych na maszynach wirtualnych w usłudze Azure Security Center i wymuszają rozwiązanie ochrony przed złośliwym oprogramowaniem firmy Microsoft na maszynach wirtualnych systemu Windows.

- Wdrażanie domyślnego rozszerzenia Microsoft IaaSAntimalware dla systemu Windows Server
- Rozwiązanie ochrony punktów końcowych powinno być zainstalowane w zestawach skalowania maszyny wirtualnej
- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Ochrona przed złośliwym kodem | Centralne zarządzanie

Ten plan ułatwia zarządzanie ochroną punktów końcowych, w tym ochroną przed złośliwym kodem, przypisując definicje [zasad platformy Azure,](../../../policy/overview.md) które monitorują brak ochrony punktów końcowych na maszynach wirtualnych w usłudze Azure Security Center. Usługa Azure Security Center zapewnia scentralizowane funkcje zarządzania i raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń wdrożonych zasobów platformy Azure.

- Rozwiązanie ochrony punktów końcowych powinno być zainstalowane w zestawach skalowania maszyny wirtualnej
- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center

## <a name="si-4-information-system-monitoring"></a>Monitorowanie systemu informacyjnego SI-4

Ten plan ułatwia monitorowanie systemu przez inspekcję i wymuszanie rejestrowania i zabezpieczeń danych w zasobach platformy Azure. W szczególności zasady przypisane inspekcji i wymuszania wdrożenia agenta usługi Log Analytics i ulepszone ustawienia zabezpieczeń dla baz danych SQL, kont magazynu i zasobów sieciowych. Te funkcje mogą pomóc w wykrywaniu nietypowych zachowań i wskaźników ataków, dzięki czemu można podjąć odpowiednie działania.

- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji — obraz maszyny Wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Wdrożenie agenta analizy dziennika inspekcji w vmss - obraz maszyny wirtualnej (OS) niepubliczny
- \[Wersja\]zapoznawcza : Obszar roboczy analizy dzienników inspekcji dla maszyny Wirtualnej — niezgodność raportu
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux
- \[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows
- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL
- Wdrażanie zaawansowanych zabezpieczeń danych na serwerach SQL
- Wdrażanie zaawansowanej ochrony przed zagrożeniami na kontach magazynu
- Wdrażanie inspekcji na serwerach SQL
- Wdrażanie obserwatora sieci podczas tworzenia sieci wirtualnych
- Wdrażanie wykrywania zagrożeń na serwerach SQL

> [!NOTE]
> Dostępność określonych definicji zasad platformy Azure może się różnić w przypadku platformy Azure dla instytucji rządowych i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Przejrzano mapowanie kontroli przykładowego planu kanadyjskiego federalnego pbmm. Następnie odwiedź następujące artykuły, aby dowiedzieć się więcej o omówieniu i sposobie wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Kanada Federalny PBMM plan - Przegląd](./control-mapping.md)
> [Kanada Federal PBMM plan - Wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
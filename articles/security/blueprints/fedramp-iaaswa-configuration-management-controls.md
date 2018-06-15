---
title: Plan zarządzania konfiguracją - automatyzacji aplikacji sieci Web FedRAMP - Azure zabezpieczeń i zgodności
description: Automatyzacji aplikacji sieci Web FedRAMP — Zarządzanie konfiguracją
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 356eccac6af2780c02d1cd935d41891b5f89f1a2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206990"
---
# <a name="configuration-management-cm"></a>Zarządzanie konfiguracją (CM)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-cm-1"></a>Formant NIST 800 53 CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Konfiguracja zasad zarządzania i procedur

**CM 1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] konfiguracji zasad zarządzania, którego przeznaczenie, zakres, ról, obowiązki, zobowiązań zarządzania Współpraca między jednostki organizacyjnej i zgodności; i procedur w celu ułatwienia stosowania zasad zarządzania konfiguracji oraz możliwościami zarządzania skojarzony konfiguracji; przegląda i aktualizuje bieżące zasady zarządzania konfiguracji [przypisania: zdefiniowane przez organizację częstotliwość]; i procedury zarządzania konfiguracji [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady zarządzania konfiguracji na poziomie przedsiębiorstwa i procedur klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-2"></a>Formant NIST 800 53 CM-2

#### <a name="baseline-configuration"></a>Linii bazowej konfiguracji

**CM 2** organizacji rozwija, dokumentów i przechowuje pod kontrolą konfiguracja bieżącej podstawowej konfiguracji systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Szablony usługi Azure Resource Manager i towarzyszące zasobów, które obejmuje ten plan reprezentują linii bazowej "Konfiguracja jako kod" dla architektury wdrożone. Rozwiązanie znajduje się jednak GitHub, która może służyć do konfiguracji kontroli. Rozwiązanie zawiera linii bazowej konfiguracji żądanego stanu (DSC) dla każdej wdrożonej maszyny wirtualnej. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-2-1a"></a>Tj NIST 800 53 kontroli CM 2 (1).

#### <a name="baseline-configuration--reviews-and-updates"></a>Linii bazowej konfiguracji | Przeglądy i aktualizacje

**.A CM-2 (1)** organizacji przegląda i aktualizuje linii bazowej konfiguracji systemu informacji [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądu i aktualizowania konfiguracji bazowej wdrożonych przez klienta zasobów (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800 53 kontroli CM 2 (1) b

#### <a name="baseline-configuration--reviews-and-updates"></a>Linii bazowej konfiguracji | Przeglądy i aktualizacje

**B CM-2 (1)** organizacji przegląda i aktualizuje linii bazowej konfiguracji systemu informacji wymaganych z powodu [przypisania zdefiniowany przez organizację okoliczności].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądu i aktualizowania konfiguracji bazowej zasobów wdrożonych przez klienta, gdy jest to wymagane. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-2-1c"></a>((1) plików .c NIST 800 53 kontroli CM 2

#### <a name="baseline-configuration--reviews-and-updates"></a>Linii bazowej konfiguracji | Przeglądy i aktualizacje

**.C CM-2 (1)** organizacji przegląda i aktualizuje linii bazowej konfiguracji systemu informacji w ramach instalacji składnika systemu informacji i uaktualnień.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądu i aktualizowania konfiguracji bazowej zasobów wdrożonych przez klienta, gdy jest to wymagane. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800 53 kontroli CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Linii bazowej konfiguracji | Obsługa automatyzacji dla dokładność / waluty

**CM-2 (2)** automatyczne mechanizmy do obsługi aktualne, kompletne, dokładne i łatwo dostępne podstawowej konfiguracji systemu informacji jest stosowana w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Szablony usługi Azure Resource Manager i towarzyszące zasobów, które obejmuje ten plan reprezentują linii bazowej "Konfiguracja jako kod" dla architektury wdrożone. Rozwiązanie znajduje się jednak GitHub, która może służyć do konfiguracji kontroli. W portalu Azure skryptu automatyzacji jest dostępna dla wszystkich wdrożonych zasobów i udostępnia zawsze aktualne reprezentacja tych zasobów.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800 53 kontroli CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Linii bazowej konfiguracji | Zatrzymanie poprzedniej konfiguracji

**CM-2 (3)** Organizacja zachowuje [przypisania: zdefiniowane przez organizację poprzednie wersje linii bazowej konfiguracji systemu informacji] do obsługi wycofywania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zachowując poprzednie wersje linii bazowej konfiguracji dla zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800 53 kontrolować CM 2 .a (7)

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Linii bazowej konfiguracji | Konfigurowanie systemów, składniki i urządzenia obszarów o wysokim ryzyku

**.A CM-2 (7)** problemów organizacji [przypisania: systemów informacje zdefiniowane przez organizację, składniki systemu lub urządzenia] z [przypisania: konfiguracje zdefiniowany przez organizację] osobom podróży do lokalizacji który organizacji uzna za znaczące zagrożenie.

**Obowiązki:** `Not Applicable`

|||
|---|---|
| **Klienta** | Brak urządzeń fizycznych kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Zawartość klienta Microsoft Azure nigdy nie jest przechowywana poza Microsoft Azure, która jest fizycznie zlokalizowany w kontynentalnym Stanów Zjednoczonych. Personel firmy Microsoft Azure nie działają podczas podróży z urządzeń zawarty w magazynie Microsoft Azure. Tak ten formant nie ma zastosowania do systemu Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800 53 kontrolować CM 2 b (7)

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Linii bazowej konfiguracji | Konfigurowanie systemów, składniki i urządzenia obszarów o wysokim ryzyku

**B CM-2 (7)** organizacja stosuje [przypisania: zabezpieczeń zdefiniowany przez organizację] do urządzenia, gdy zwróci osób.

**Obowiązki:** `Not Applicable`

|||
|---|---|
| **Klienta** | Brak urządzeń fizycznych kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Nigdy nie jest przechowywana zawartość klienta Microsoft Azure poza Microsoft Azure i personel firmy Microsoft Azure nie działają podczas podróży z urządzeń zawarty w magazynie Microsoft Azure, w związku z tym nie ma zastosowania tego formantu. |


 ## <a name="nist-800-53-control-cm-3a"></a>Formant NIST 800 53 CM-3.a

#### <a name="configuration-change-control"></a>Kontroli zmian konfiguracji

**CM 3.a** organizacji określa typy zmiany w systemie informacje, które są kontrolowane przez konfigurację.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za określenie, jakie typy zmian do wdrożonych przez klienta zasobów (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych) są kontrolowane przez konfigurację. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-3b"></a>Formant NIST 800 53 CM-3.b

#### <a name="configuration-change-control"></a>Kontroli zmian konfiguracji

**CM 3.b** organizacji przegląda proponowanych zmian konfiguracji pod kontrolą systemu informacji i zatwierdza lub nie uwzględni takie zmiany, z uwzględnieniem jawne zabezpieczeń analizy wpływu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądania proponowanych zmian kontrolą konfiguracji do zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-3c"></a>Formant NIST 800 53 CM-3.c

#### <a name="configuration-change-control"></a>Kontroli zmian konfiguracji

**CM 3.c** Konfiguracja dokumentów organizacji zmiany decyzji skojarzoną z systemem informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za dokumentację zmiany kontrolą konfiguracji związanych z zasobami wdrożonych przez klienta (zobacz CM 03.b). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-3d"></a>Formant NIST 800 53 CM-3.d

#### <a name="configuration-change-control"></a>Kontroli zmian konfiguracji

**CM 3.d** organizacji implementuje zatwierdzone zmiany konfiguracji pod kontrolą systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wdrażanie zmiany kontrolą konfiguracji zatwierdzona w CM 03.b. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-3e"></a>Formant NIST 800 53 CM-3.e

#### <a name="configuration-change-control"></a>Kontroli zmian konfiguracji

**CM-3.e** Organizacja zachowuje rekordów kontrolą konfiguracji zmiany w systemie informacji dla [przypisania: okres zdefiniowany organizacji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zachowując rekord kontrolą konfiguracji zmiany w zasobach wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-3f"></a>Formant NIST 800 53 CM-3.f

#### <a name="configuration-change-control"></a>Kontroli zmian konfiguracji

**CM 3.f** organizacji inspekcji i monitoruje działania związane z kontrolą konfiguracji zmiany w systemie informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za inspekcji i przeglądanie zmian w konfiguracji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-3g"></a>Formant NIST 800 53 CM-3.g

#### <a name="configuration-change-control"></a>Kontroli zmian konfiguracji

**CM 3.g** organizacji koordynuje i zapewnia nadzoru działań kontroli zmian konfiguracji za pomocą [przypisania: element kontroli zmian konfiguracji zdefiniowane w organizacji (np. Komitetu, tablicy)] który convenes [wybór () jeden lub więcej): [przypisania: zdefiniowane przez organizację częstotliwość]; [Przypisania: warunki zmiany konfiguracji zdefiniowany przez organizację]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za koordynowanie i podając nadzoru działania kontroli zmian konfiguracji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-1a"></a>Tj. NIST 800 53 kontroli CM – 3 (1)

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Kontroli zmian konfiguracji | Automatyczne dokumentu / powiadomień / zakaz zmiany

**.A CM – 3 (1)** automatyczne mechanizmy dokumentu proponowanych zmian do systemu informacji jest stosowana w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących zautomatyzowane mechanizmy dokumentu proponowanych zmian (zobacz CM 03.b). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800 53 kontroli CM – 3 (1) b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Kontroli zmian konfiguracji | Automatyczne dokumentu / powiadomień / zakaz zmiany

**B CM – 3 (1)** organizacja stosuje automatycznych mechanizmy powiadamiania o [przypisania: zdefiniowanych zorganizowane zatwierdzania] proponowanych zmian do informacji systemu i żądania zmiany zatwierdzenia.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących zautomatyzowany mechanizm trasy i żądania zatwierdzenia dla proponowanych zmian do zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800 53 kontroli CM – 3 (1) .c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Kontroli zmian konfiguracji | Automatyczne dokumentu / powiadomień / zakaz zmiany

**.C CM – 3 (1)** organizacja stosuje automatycznych mechanizmy, aby wyróżnić proponowanych zmian w systemie informacje, które nie zostały zatwierdzone lub odrzucone przez [przypisania: okres zdefiniowany organizacji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących zautomatyzowany mechanizm Wyróżnij propozycje nieprzejrzanymi zmiany. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800 53 kontroli CM – 3 (1) .d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Kontroli zmian konfiguracji | Automatyczne dokumentu / powiadomień / zakaz zmiany

**.D CM – 3 (1)** uniemożliwiają zmiany w systemie informacje do momentu otrzymania są wyznaczone zatwierdzenia automatyczne mechanizmy jest stosowana w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących zautomatyzowany mechanizm zakazać implementacji niezatwierdzone zmiany w zasobach wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800 53 kontroli CM – 3 (1) .e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Kontroli zmian konfiguracji | Automatyczne dokumentu / powiadomień / zakaz zmiany

**CM-3 (1).e** Automatyczne mechanizmy dokumentu wszystkie zmiany do systemu informacji jest stosowana w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących zautomatyzowany mechanizm dokumentu wszystkie zmiany zaimplementowanym zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800 53 kontroli CM – 3 (1) .f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Kontroli zmian konfiguracji | Automatyczne dokumentu / powiadomień / zakaz zmiany

**.F CM – 3 (1)** organizacja stosuje automatycznych mechanizmy powiadamiania o [przypisania: zdefiniowany przez organizację personelu] ukończenia zatwierdzone zmiany do systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących zautomatyzowany mechanizm zapewnienie powiadomienia po zakończeniu zatwierdzone zmiany do zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800 53 kontroli CM – 3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Kontroli zmian konfiguracji | Test / zweryfikować / dokumentu zmiany

**CM – 3 (2)** organizacji testów, weryfikuje i dokumenty zmiany w systemie informacji przed wdrożeniem zmian na system operacyjny.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialne za testowanie, sprawdzanie poprawności i dokumentowanie zmian do wdrożonych przez klienta zasobów przed implementacją. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800 53 kontroli CM – 3 (4)

#### <a name="configuration-change-control--security-representative"></a>Kontroli zmian konfiguracji | Przedstawiciel zabezpieczeń

**CM – 3 (4)** organizacja wymaga reprezentatywny zabezpieczeń informacji ma być członkiem [przypisania: element kontroli zmian konfiguracji zdefiniowanej w organizacji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przypisywanie przedstawicielem zabezpieczeń informacji być członkiem elementu kontrolki zmiany zdefiniowane w CM 03.g. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800 53 kontroli CM – 3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Kontroli zmian konfiguracji | Zarządzanie kryptografii

**CM – 3 (6)** organizacji gwarantuje, że mechanizmów kryptograficznych używany w celu zapewnienia [przypisania: zabezpieczeń zdefiniowany przez organizację] są w obszarze Zarządzanie konfiguracją.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zapewnienie, że mechanizmów kryptograficznych są w obszarze Zarządzanie konfiguracją. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-4"></a>Formant NIST 800 53 CM-4

#### <a name="security-impact-analysis"></a>Analiza wpływu zabezpieczeń

**CM 4** organizacji analizuje zmiany w systemie informacji, aby określić potencjalny wpływ zabezpieczeń przed implementacji zmian.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za analizowanie proponowanych zmian do zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800 53 kontroli CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Analiza wpływu zabezpieczeń | Oddzielnych środowisk testowych

**CM-4 (1)** organizacji analizuje zmiany w systemie informacji w środowisku testowym oddzielne przed implementacją w środowisku operacyjnym, wyszukiwanie wpływ zabezpieczeń z powodu wady, słabe strony algorytmu, niezgodności lub zamierzone malice.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za analizowanie proponowanych zmian do wdrożonych przez klienta zasobów w środowisku testowym przed implementacją w środowisku operacyjnym. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-5"></a>Formant NIST 800 53 CM-5

#### <a name="access-restrictions-for-change"></a>Ograniczenia dostępu dla zmian

**CM 5** organizacji definiuje, dokumentów i zatwierdza oraz wymusza ograniczenia dostępu fizycznych i logicznych skojarzone z zmiany w systemie informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Azure Active Directory, konto, które uprawnienia są implementowane przy użyciu kontroli dostępu opartej na rolach, przypisując użytkowników do ról, zapewniając strict sterowania przez użytkowników, którzy mogą wyświetlać i kontroli wdrożonych zasobów. Uprawnienia konta w usłudze Active Directory są implementowane za pomocą kontroli dostępu opartej na rolach, przypisując użytkownikom dla grup zabezpieczeń. Te grupy zabezpieczeń kontrolować akcje wykonywane przez użytkowników względem konfiguracji systemu operacyjnego. Te systemy oparte na rolach można rozszerzyć przez klienta w celu spełnienia potrzeb misji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800 53 kontroli CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Dostęp do ograniczenia dotyczące zmiany | Automatyczne wymuszania dostępu / inspekcji

**CM-5 (1)** systemu informacji wymusza ograniczenia dostępu i obsługuje inspekcję akcjach wymuszania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Azure Active Directory, konto, które uprawnienia są implementowane przy użyciu kontroli dostępu opartej na rolach, przypisując użytkowników do ról, zapewniając strict sterowania przez użytkowników, którzy mogą wyświetlać i kontroli wdrożonych zasobów. Uprawnienia konta w usłudze Active Directory są implementowane za pomocą kontroli dostępu opartej na rolach, przypisując użytkownikom dla grup zabezpieczeń. Te grupy zabezpieczeń kontrolować akcje wykonywane przez użytkowników względem konfiguracji systemu operacyjnego. Wszystkie dostępu i prób dostępu są poddawane inspekcji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800 53 kontroli CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Dostęp do ograniczenia dotyczące zmiany | Przejrzyj zmiany w systemie

**CM-5 (2)** organizacji monitoruje zmiany w systemie informacji [przypisania: częstotliwość zdefiniowany przez organizację] i [przypisania: zdefiniowany przez organizację okoliczności] Aby ustalić, czy wystąpiły nieautoryzowane zmiany.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądania zmian do wdrożonych przez klienta zasoby, aby sprawdzić, czy wystąpiły nieautoryzowane zmiany. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800 53 kontroli CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Dostęp do ograniczenia dotyczące zmiany | Podpisane składniki

**CM-5 (3)** systemu informacji zapobiega instalacji [przypisania: składniki oprogramowania układowego i oprogramowania zdefiniowane przez organizację] bez weryfikacji czy składnik jest podpisany cyfrowo za pomocą certyfikatu, który jest rozpoznawany i zatwierdzone przez organizację.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan wdrożenia zasad ograniczeń oprogramowania systemu Windows, aby określić użytkowników, którzy mogą instalować i uruchamiać określone aplikacje. Dalsze wszystkich okien cyfrowo są singed aktualizacji systemu operacyjnego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800 53 kontrolować tj CM 5 (5).

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Dostęp do ograniczenia dotyczące zmiany | Limit produkcji / Operational uprawnień

**.A CM-5 (5)** organizacji ogranicza uprawnienia, aby zmienić informacji na składniki systemowe i informacje związane z systemu, w środowisku produkcyjnym lub w środowisku operacyjnym.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za ograniczanie uprawnień do wprowadzania zmian w produkcyjnym wdrożeniu klienta lub operacyjną środowisk. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800 53 kontrolować CM-5, b (5)

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Dostęp do ograniczenia dotyczące zmiany | Limit produkcji / Operational uprawnień

**B CM-5 (5)** organizacji monitoruje i reevaluates uprawnień [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądu i reevaluating uprawnienia zdefiniowane w .a CM-05 (05). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-6a"></a>Formant NIST 800 53 CM-6.a

#### <a name="configuration-settings"></a>Ustawienia konfiguracji

**CM 6.a** organizacji ustanawia i dokumenty ustawienia konfiguracji dla informacji produktów technologii stosowanych w ramach przy użyciu systemu informacji [przypisania: listy kontrolne konfiguracji zabezpieczeń zdefiniowany przez organizację] które odzwierciedlać Tryb najbardziej restrykcyjne zgodne z wymagań operacyjnych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan zawiera plan bazowy konfiguracji żądanego stanu (DSC) dla każdej wdrożonej maszyny wirtualnej. Skrypty programu PowerShell te deklaratywne zdefiniować i skonfigurować zasoby, do których są stosowane. Linia bazowa konfiguracji DSC dołączane do zasobów wykorzystywanych przez to rozwiązanie można rozszerzyć przez klienta w celu spełnienia potrzeb misji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-6b"></a>Formant NIST 800 53 CM-6.b

#### <a name="configuration-settings"></a>Ustawienia konfiguracji

**CM 6.b** Organizacja wdraża ustawienia konfiguracji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan zawiera plan bazowy konfiguracji żądanego stanu (DSC) dla każdej wdrożonej maszyny wirtualnej. Plany bazowe jest automatycznie stosowane do maszyn wirtualnych podczas wdrażania przy użyciu skryptu niestandardowego rozszerzenia maszyny wirtualnej. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-6c"></a>Formant NIST 800 53 CM-6.c

#### <a name="configuration-settings"></a>Ustawienia konfiguracji

**CM 6.c** organizacji identyfikuje, dokumentów i zatwierdza wszelkie odchylenia od ustawień konfiguracji ustalonych [przypisania: składniki systemowe zdefiniowane przez organizację informacje] na podstawie [przypisania: zdefiniowane przez organizację wymagań operacyjnych].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za identyfikowania, dokumentowania i zatwierdzania wszystkich odchyleń od ustawienia konfiguracji ustalonych zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-6d"></a>Formant NIST 800 53 CM-6.d

#### <a name="configuration-settings"></a>Ustawienia konfiguracji

**CM 6.d** monitorów organizacji i kontroli zmiany w ustawieniach konfiguracji zgodnie z zasadami organizacji i procedur.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża usługi Konfiguracja DSC automatyzacji. Automation DSC wyrównuje konfiguracje maszyny z określoną konfigurację zdefiniowany przez organizację. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800 53 kontroli CM – 6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Ustawienia konfiguracji | Zautomatyzowane zarządzanie centralnej / Application / weryfikacji

**CM – 6 (1)** organizacja stosuje automatycznych mechanizmy centralnie zarządzać, zastosuj i sprawdź ustawienia konfiguracji dla [przypisania: składniki systemowe zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża Konfiguracja DSC automatyzacji Azure. Automation DSC wyrównuje konfiguracje maszyny z określoną konfigurację zdefiniowany przez organizację i stale monitoruje zmiany. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800 53 kontroli CM – 6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Ustawienia konfiguracji | Odpowiadanie na nieautoryzowane zmiany

**CM – 6 (2)** organizacja stosuje [przypisania: zabezpieczeń zdefiniowany przez organizację] odpowiedzieć nieautoryzowane zmiany [przypisania: ustawienia konfiguracji zdefiniowany przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża Konfiguracja DSC automatyzacji Azure. Automation DSC można skonfigurować do generowania alertu lub błędy konfiguracji w przypadku wykrycia naprawienia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-7a"></a>Formant NIST 800 53 CM-7.a

#### <a name="least-functionality"></a>Co najmniej funkcji

**CM 7.a** organizacji konfiguruje system informacji, aby zapewnić tylko podstawowe możliwości.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasoby wdrożone przez ten plan są skonfigurowane do zapewnienia funkcji co najmniej ich przeznaczenia. Plan bazowy konfiguracji żądanego stanu (DSC) jest uwzględniony w każdej wdrożonej maszyny wirtualnej. Skrypty programu PowerShell te deklaratywne zdefiniować i skonfigurować zasoby, do których są stosowane. Linia bazowa konfiguracji DSC dołączane do zasobów wykorzystywanych przez to rozwiązanie można rozszerzyć przez klienta, aby jeszcze bardziej ograniczyć liczbę funkcji do potrzeb misji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-7b"></a>Od CM kontroli NIST 800 53-7.b

#### <a name="least-functionality"></a>Co najmniej funkcji

**Od 7.b CM** organizacji zabrania lub ogranicza korzystanie z funkcji następujące porty, protokołów i usług: [przypisania: zabronione lub ograniczone funkcje zdefiniowane przez organizację, portów, protokołów i/lub usług].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża brama aplikacji w usłudze Azure i siecią grup zabezpieczeń do ograniczenia użycia portów i protokołów wyłącznie do tych niezbędne. Brama aplikacji, grup zabezpieczeń sieci i linie bazowe konfiguracji DSC dla maszyn wirtualnych można dodatkowo skonfigurować przez klienta w celu ograniczenia użycia funkcji, portów, protokołów i usług, które były dostępne tylko funkcje przeznaczone. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-7-1a"></a>Tj. NIST 800 53 kontroli CM 7 (1)

#### <a name="least-functionality--periodic-review"></a>Co najmniej funkcji | Przeglądać

**.A CM-7 (1)** organizacji przegląda informacje o systemie [przypisania: zdefiniowane przez organizację częstotliwość] do identyfikowania funkcji niepotrzebnych i/lub niezabezpieczone, portów, protokołów i usług.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za recenzowania wdrożonych przez klienta zasobów (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych) do identyfikowania funkcji niepotrzebnych i/lub niezabezpieczone, portów, protokołów i usług. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800 53 kontroli CM 7 (1) b

#### <a name="least-functionality--periodic-review"></a>Co najmniej funkcji | Przeglądać

**B CM-7 (1)** wyłącza organizacji [przypisania: funkcje zdefiniowane przez organizację, portów, protokołów i usług w ramach systemu informacji uznane za niepotrzebne i/lub niezabezpieczone].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wyłączenie funkcji, portów, protokołów i usług, które zostały uznane za niepotrzebne lub niezabezpieczone. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800 53 kontroli CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Co najmniej funkcji | Zapobiegaj wykonanie programu

**CM-7 (2)** systemu informacji uniemożliwia wykonania programu zgodnie z [wybór (jeden lub więcej): [przypisania: zdefiniowany w organizacji zasad dotyczących oprogramowania program użycia i ograniczenia]; reguły autoryzacji warunki i warunki użytkowania programu software].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za uniemożliwia wykonania programu zgodnie z zasad użytkowania oprogramowania zdefiniowane przez klienta programu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800 53 kontrolować tj CM 7 (5).

#### <a name="least-functionality--authorized-software--whitelisting"></a>Co najmniej funkcji | Autoryzowany oprogramowania / listę dozwolonych podobnej

**.A CM-7 (5)** identyfikuje organizacji [przypisania: zdefiniowane przez organizację programów autoryzacji do wykonania w systemie informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiada za ustalenie autoryzowanych programów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800 53 kontrolować CM 7 b (5)

#### <a name="least-functionality--authorized-software--whitelisting"></a>Co najmniej funkcji | Autoryzowany oprogramowania / listę dozwolonych podobnej

**B CM-7 (5)** Odmów all, zezwolenia przez wyjątek zasady umożliwiające wykonywanie autoryzowanych programów w systemie informacji jest stosowana w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujące wszystkie Odmów, zezwolenia przez wyjątek zasady umożliwiają wykonywanie autoryzowanych programów zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800 53 kontrolować CM 7 .c (5)

#### <a name="least-functionality--authorized-software--whitelisting"></a>Co najmniej funkcji | Autoryzowany oprogramowania / listę dozwolonych podobnej

**.C CM-7 (5)** organizacji przegląda i aktualizuje listę autoryzowanych programów [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądu i aktualizowania listy autoryzowanych programów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-8a"></a>Formant NIST 800 53 CM-8.a

#### <a name="information-system-component-inventory"></a>Informacje o systemie składnika spisu

**CM 8.a** rozwija i dokumenty spis informacji na składniki systemowe dokładnie odzwierciedla bieżący system informacji; obejmuje wszystkie składniki w obrębie granicy autoryzacji systemu informacji; jest w organizacji poziom szczegółowości uważane za niezbędne do monitorowania i raportowania; i zawiera [przypisania: informacje zdefiniowane przez organizację uznany za należy uzyskać informacje o systemie składnika accountability].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża wszystkie zasoby grupy zasobów platformy Azure Resource Manager. Usługa Azure Resource Manager udostępnia zawsze aktualna lista wdrożonych zasobów i można dostosować do znacznika i grupy zasobów, Zarządzanie zapasami. Zasoby wdrażane przez to rozwiązanie podano tag określonych zasobów, które mogą być skojarzone z granicą systemu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-8b"></a>Formant NIST 800 53 CM-8.b

#### <a name="information-system-component-inventory"></a>Informacje o systemie składnika spisu

**CM 8.b** organizacji przegląda i aktualizuje spis składnik systemu informacji [przypisania: częstotliwość zdefiniowany przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża wszystkie zasoby grupy zasobów platformy Azure Resource Manager. Usługa Azure Resource Manager udostępnia zawsze aktualna lista wdrożonych zasobów gotowa do przeglądu w portalu Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800 53 kontroli CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Spis składnik systemu informacji | Aktualizacje podczas instalacji / usuwania

**CM-8 (1)** organizacji aktualizacji spisu informacji na składniki systemu w ramach instalacji składnika, usuwania i aktualizacji systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża wszystkie zasoby grupy zasobów platformy Azure Resource Manager. Bloku zasobów w portalu Azure zawiera listę wszystkich wdrożonych zasobów, zapewniając zawsze aktualne spis wdrożone i usunąć zasobów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800 53 kontroli CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Spis składnik systemu informacji | Automatyczne konserwacji

**CM-8 (2)** organizacja stosuje automatycznych mechanizmów Aby zachować aktualne, kompletne, dokładne i łatwo dostępne spis informacji na składniki systemu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża wszystkie zasoby grupy zasobów platformy Azure Resource Manager. Bloku zasobów w portalu Azure zawiera listę wszystkich wdrożonych zasobów, zapewniając zawsze aktualne spis wdrożone i usunąć zasobów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800 53 kontrolować tj CM 8 (3).

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Spis składnik systemu informacji | Automatyczne wykrywanie nieautoryzowanych składnika

**.A CM-8 (3)** organizacja stosuje mechanizmów automatyczne [przypisania: zdefiniowany przez organizację częstotliwość] do wykrycia obecności nieautoryzowanego sprzętu, oprogramowania i oprogramowania układowego składników w systemie informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących zautomatyzowane mechanizmy do wykrycia obecności nieautoryzowanego oprogramowania we wdrożeniu klienta zasobów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800 53 kontrolować CM 8 b (3)

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Spis składnik systemu informacji | Automatyczne wykrywanie nieautoryzowanych składnika

**B CM-8 (3)** organizacji wykonuje następujące akcje po wykryciu nieautoryzowanego składniki: [wybór (jeden lub więcej): wyłącza dostępu do sieci przez takie składniki; izoluje składniki; powiadamia [przypisania: zdefiniowane przez organizację "personel lub ról]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykonanie akcji po wykryciu nieautoryzowanego oprogramowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800 53 kontroli CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Spis składnik systemu informacji | Informacje o odpowiedzialności

**CM-8 (4)** organizacji zawiera informacje składnik spisu informacji o systemie, sposób identyfikowania przez [wybór (jeden lub więcej): Nazwa pozycji; roli], osoby odpowiedzialne/odpowiedzialny za administrowanie tych składników .

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża wszystkie zasoby grupy zasobów platformy Azure Resource Manager. Tagi zasobów platformy Azure są klucz / wartość pary, które można zastosować do klasyfikowania zasobów do celów accountability i/lub zarządzania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800 53 kontroli CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Spis składnik systemu informacji | Nie zduplikowanych ewidencjonowania aktywności składników

**CM-8 (5)** organizacji sprawdza, czy wszystkie składniki w obrębie granicy autoryzacji systemu informacje nie są duplikowane w zasobach składnik systemu innych informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża wszystkie zasoby grupy zasobów platformy Azure Resource Manager. Usługa Azure Resource Manager udostępnia zawsze aktualna lista wdrożonych zasobów. Zasoby wdrażane przez to rozwiązanie podano tag określonych zasobów, które mogą być skojarzone z granicą systemu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-9a"></a>Formant NIST 800 53 CM-9.a

#### <a name="configuration-management-plan"></a>Plan zarządzania konfiguracji

**CM 9.a** organizacji rozwija, dokumentów i implementuje planu zarządzania konfiguracji dla systemu informacje dotyczące ról, obowiązki i konfiguracji zarządzania procesy i procedury.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za tworzenie, dokumentowania i wdrażanie planu zarządzania konfiguracji zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-9b"></a>Formant NIST 800 53 CM-9.b

#### <a name="configuration-management-plan"></a>Plan zarządzania konfiguracji

**CM 9.b** organizacji rozwija, dokumentów i implementuje planu zarządzania konfiguracji ustanawia procesem cyklu identyfikujące elementów konfiguracji w całym cyklu życia rozwoju systemu i do systemu informacji Zarządzanie konfiguracją elementów konfiguracji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za tworzenie, dokumentowania i wdrażanie planu zarządzania konfiguracji zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800-53 Control CM-9.c

#### <a name="configuration-management-plan"></a>Plan zarządzania konfiguracji

**CM 9.c** organizacji rozwija, dokumentów i implementuje planu zarządzania konfiguracji systemu informacji definiujący konfigurację elementy systemu informacji i umieszcza elementy konfiguracji, w obszarze Konfiguracja Zarządzanie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za tworzenie, dokumentowania i wdrażanie planu zarządzania konfiguracji zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-9d"></a>Formant NIST 800 53 CM-9.d

#### <a name="configuration-management-plan"></a>Plan zarządzania konfiguracji

**CM 9.d** organizacji rozwija, dokumentów i implementuje planu zarządzania konfiguracji systemu informacji, który chroni plan zarządzania konfiguracji przed nieuprawnionym ujawnieniem i modyfikacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za tworzenie, dokumentowania i wdrażanie planu zarządzania konfiguracji zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-10a"></a>Formant NIST 800 53 CM-10.a

#### <a name="software-usage-restrictions"></a>Ograniczenia użycia oprogramowania

**CM 10.a** organizacja korzysta z oprogramowania i skojarzone dokumentacji zgodnie z warunkami umowy kontraktu i prawa autorskie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Licencje systemu Windows i program SQL Server są dołączone do zasobów wykorzystywanych przez ten plan. Jest to funkcja wbudowana platformy Azure. Organizacje z istniejącej umowy licencyjne oprogramowania rozważyć wdrażanie modeli alternatywnych licencji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-10b"></a>Formant NIST 800 53 CM-10.b

#### <a name="software-usage-restrictions"></a>Ograniczenia użycia oprogramowania

**CM 10.b** organizacji śledzi korzystanie z oprogramowania i skojarzone dokumentacji chronione przez liczbę licencji do kontrolowania, kopiowanie i dystrybucji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Licencje systemu Windows i program SQL Server są dołączone do zasobów wykorzystywanych przez ten plan. Użytkownik nie ma obowiązku oddzielnie śledzić użycie licencji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-10c"></a>Formant NIST 800 53 CM-10.c

#### <a name="software-usage-restrictions"></a>Ograniczenia użycia oprogramowania

**CM 10.c** organizacja kontroluje i dokumenty korzystanie z technologii, aby upewnić się, że ta funkcja nie jest używany przez nieautoryzowanych dystrybucji, wyświetlania, wydajność lub reprodukowania dziełami udostępnianie pliku peer-to-peer.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Plik nie peer-to-peer, udostępnianie możliwości wdrożone przez ten plan nie istnieje. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800 53 kontroli CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>Ograniczenia użycia oprogramowania | Oprogramowanie typu Open Source

**CM-10 (1)** organizacji ustanawia następujące ograniczenia dotyczące użycia oprogramowania typu open source: [przypisania: ograniczenia zdefiniowane przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady zarządzania konfiguracji na poziomie przedsiębiorstwa klienta może dotyczyć ograniczeń użytkowania oprogramowania typu open source. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-11a"></a>Formant NIST 800 53 CM-11.a

#### <a name="user-installed-software"></a>Oprogramowanie instalowane przez użytkownika

**CM 11.a** ustanawia organizacji [przypisania: zasady zdefiniowane przez organizację] dotyczących instalacji oprogramowania przez użytkowników.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za ustanawianie zasad regulujących instalację oprogramowania na zasoby wdrażane klienta przez użytkowników. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-11b"></a>Formant NIST 800 53 CM-11.b

#### <a name="user-installed-software"></a>Oprogramowanie instalowane przez użytkownika

**CM 11.b** organizacja wymusza zasady instalacji oprogramowania przy użyciu [przypisania: metody zdefiniowane przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za egzekwowanie zasad instalacji oprogramowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-cm-11c"></a>Formant NIST 800 53 CM-11.c

#### <a name="user-installed-software"></a>Oprogramowanie instalowane przez użytkownika

**CM 11.c** organizacji monitoruje zgodność z zasadami w [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za monitorowanie zgodności wdrożonych przez klienta zasobów za pomocą zasad określonych w CM 11.a. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800 53 kontroli CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Użytkownik zainstalował oprogramowania | Alerty dotyczące nieautoryzowanych instalacji

**CM-11 (1)** alerty systemu informacji [przypisania: techniczną zdefiniowany przez organizację lub ról] po wykryciu nieautoryzowanych instalacji oprogramowania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zapewnienie alerty po wykryciu nieautoryzowanych instalacji oprogramowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


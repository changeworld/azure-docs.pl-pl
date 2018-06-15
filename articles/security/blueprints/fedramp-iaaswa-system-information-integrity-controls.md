---
title: Zabezpieczeń platformy Azure i systemu plan - automatyzacji aplikacji sieci Web FedRAMP — zgodność i integralność informacji
description: Automatyzacja aplikacji FedRAMP sieci Web — System i integralność informacji
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 0eca3c82aea287f6582bd56574512dce5e8e86c7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206982"
---
# <a name="system-and-information-integrity-si"></a>System i integralność informacji (SI)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-si-1"></a>Formant NIST 800 53 SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Systemu i zasady integralności informacji i procedur

**SI 1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] systemowe i informacje zasady integralności, którego dotyczy cel, zakres, ról, obowiązki, zobowiązań zarządzania Współpraca między jednostki organizacyjnej i zgodności; i procedury w celu ułatwienia implementacji systemu i zasady integralności informacji i skojarzonego z nim systemu i kontroli spójności informacji; przegląda i aktualizuje bieżące zasady integralności systemu i informacji [przypisania: zdefiniowane przez organizację częstotliwość]; i procedur integralności systemu i informacji [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Przez klienta zasad integralności systemu i informacji na poziomie przedsiębiorstwa i procedur mogą być wystarczająca do adresów tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-2a"></a>NIST 800-53 Control SI-2.a

#### <a name="flaw-remediation"></a>Luka korygowania

**SI 2.a** organizacji identyfikuje, raporty i poprawia wady systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża rozwiązanie Automatyzacja i kontroli do śledzenia stanu aktualizacji dla maszyn wirtualnych systemu Windows wdrożone w ramach tej architektury. Na pulpicie nawigacyjnym kafelka zarządzania aktualizacjami Wyświetla stan korygowania luka wszystkich wdrożonych serwerów systemu Windows. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-2b"></a>NIST 800-53 Control SI-2.b

#### <a name="flaw-remediation"></a>Luka korygowania

**SI 2.b** organizacji testów aktualizacje oprogramowania związane z luka korygowania skuteczności i potencjalne efekty uboczne, przed rozpoczęciem instalacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialne za testowanie aktualizacji związanych z korygowania luka skuteczność i potencjalne efekty uboczne przed instalacją zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-2c"></a>NIST 800-53 Control SI-2.c

#### <a name="flaw-remediation"></a>Luka korygowania

**SI 2.c** organizacji instaluje związanych z zabezpieczeniami aktualizacje oprogramowania w ramach [przypisania: okres zdefiniowany organizacji] wersji aktualizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyny wirtualne systemu Windows wdrożone przez ten plan są domyślnie skonfigurowane, odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie również wdraża rozwiązanie do automatyzacji i kontroli za pośrednictwem której można utworzyć wdrożenia aktualizacji do wdrożenia poprawki na serwerach z systemem Windows w razie potrzeby. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-2d"></a>NIST 800-53 Control SI-2.d

#### <a name="flaw-remediation"></a>Luka korygowania

**SI 2.d** organizacji zawiera korygowania luka w organizacji konfiguracji procesu zarządzania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za tym korygowania luka w konfiguracji zarządzania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800 53 kontroli SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>Aktualizacja_zabezpieczeń korygowania | Centralne zarządzanie

**SI-2 (1)** organizacja zarządza centralnie luka proces korygowania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża rozwiązanie Automatyzacja i kontroli do śledzenia stanu aktualizacji dla maszyn wirtualnych systemu Windows wdrożone w ramach tej architektury. Na pulpicie nawigacyjnym kafelka zarządzania aktualizacjami Wyświetla stan korygowania luka wszystkich wdrożonych serwerów systemu Windows. Do wdrożenia poprawki na serwerach z systemem Windows w razie potrzeby można utworzyć wdrożenia aktualizacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800 53 kontroli SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Aktualizacja_zabezpieczeń korygowania | Luka automatycznego korygowania stanu

**SI-2 (2)** organizacja stosuje mechanizmów automatyczne [przypisania: zdefiniowane przez organizację częstotliwość] do ustalenia stanu składników systemowych informacji w odniesieniu do korygowania luka.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża rozwiązanie Automatyzacja i kontroli do śledzenia stanu aktualizacji dla maszyn wirtualnych systemu Windows wdrożone w ramach tej architektury. Skanowanie każdego zarządzanego komputera z systemem Windows odbywa się dwa razy dziennie. Co 15 minut wywoływany jest interfejs API systemu Windows, aby wykonać zapytanie o czas ostatniej aktualizacji w celu sprawdzenia, czy stan się zmienił, a jeśli tak, inicjowane jest skanowanie pod kątem zgodności. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800 53 kontrolować tj SI-2 (3).

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Aktualizacja_zabezpieczeń korygowania | Czas do rozwiązania wady / testy dla działania naprawcze

**.A SI-2 (3)** organizacji mierzy czas między luka identyfikowanie i korygowanie luka.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za korygując wady w zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800 53 kontrolować SI-2, b (3)

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Aktualizacja_zabezpieczeń korygowania | Czas do rozwiązania wady / testy dla działania naprawcze

**B SI-2 (3)** ustanawia organizacji [przypisania: zdefiniowane przez organizację testów porównawczych] do wykonywania działań korygujących.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient może polegać na testów porównawczych poziomie enterprise dla procesów korygowania luka. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-3a"></a>Formant NIST 800 53 SI-3.a

#### <a name="malicious-code-protection"></a>Ochrona złośliwego kodu

**SI 3.a** organizacja stosuje mechanizmy ochrony złośliwego kodu na informacji punkty wejścia i wyjścia systemu do wykrywania i zwalczania złośliwego kodu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża zabezpieczenia ochrony przed złośliwym kodem oparta na hoście dla wszystkich wdrożonych maszyn wirtualnych systemu Windows implementowane przy użyciu rozszerzenia maszyny wirtualnej Antimalware firmy Microsoft. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-3b"></a>Formant NIST 800 53 SI-3.b

#### <a name="malicious-code-protection"></a>Ochrona złośliwego kodu

**SI 3.b** organizacji aktualizuje mechanizmy ochrony złośliwy kod zawsze, gdy nowości są dostępne zgodnie z organizacyjnej konfiguracji zasad zarządzania i procedur.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża zabezpieczenia ochrony przed złośliwym kodem oparta na hoście dla wszystkich wdrożonych maszyn wirtualnych systemu Windows implementowane przy użyciu rozszerzenia maszyny wirtualnej Antimalware firmy Microsoft. To rozszerzenie jest skonfigurowany do automatycznego aktualizowania obu ochrony przed złośliwym kodem aparatu i ochrony podpisy jako wersji staną się dostępne. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-3c"></a>Formant NIST 800 53 SI-3.c

#### <a name="malicious-code-protection"></a>Ochrona złośliwego kodu

**SI 3.c** organizacja skonfiguruje mechanizmy ochrony złośliwy kod do wykonania skanowania okresowego systemu informacji [przypisania: zdefiniowane przez organizację częstotliwość] i skanowania w czasie rzeczywistym plików ze źródeł zewnętrznych z [wybór (jeden lub więcej); punkt końcowy; punkty wejścia/wyjścia sieci] jako pliki są pobierane, otwarty lub wykonywane zgodnie z zasadami zabezpieczeń organizacji; i [wybór (jeden lub więcej): blokować złośliwy kod; kwarantanny złośliwy kod; Wyślij alert, aby administrator. [Przypisania: akcji zdefiniowane organizacji]] w odpowiedzi do wykrywania złośliwego kodu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża zabezpieczenia ochrony przed złośliwym kodem oparta na hoście dla wszystkich wdrożonych maszyn wirtualnych systemu Windows implementowane przy użyciu rozszerzenia maszyny wirtualnej Antimalware firmy Microsoft. To rozszerzenie jest skonfigurowany do skanowania zarówno w czasie rzeczywistym oraz okresowe (co tydzień), automatycznie zaktualizować obu ochrony przed złośliwym kodem aparatu i ochrony sygnatury i akcje automatycznego korygowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-3d"></a>NIST 800-53 Control SI-3.d

#### <a name="malicious-code-protection"></a>Ochrona złośliwego kodu

**SI 3.d** organizacji adresów odbieranie fałszywych alarmów podczas wykrywania złośliwego kodu i zwalczania i wynikowy potencjalnego wpływu na dostępność informacji systemu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za ochrony przed złośliwym kodem. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-3-1"></a>RO kontroli NIST 800 53-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Złośliwy kod ochrony | Centralne zarządzanie

**RO-3 (1)** organizacja zarządza centralnie mechanizmy ochrony złośliwego kodu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża zabezpieczenia ochrony przed złośliwym kodem oparta na hoście dla wszystkich wdrożonych maszyn wirtualnych systemu Windows implementowane przy użyciu rozszerzenia maszyny wirtualnej Antimalware firmy Microsoft. Platforma Azure udostępnia scentralizowane możliwość sprawdź bieżący stan rozwiązania chroniące przed złośliwym kodem. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-3-2"></a>RO kontroli NIST 800 53-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Złośliwy kod ochrony | Aktualizacje automatyczne

**RO-3 (2)** system informacji automatycznie aktualizuje mechanizmy ochrony złośliwego kodu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża zabezpieczenia ochrony przed złośliwym kodem oparta na hoście dla wszystkich wdrożonych maszyn wirtualnych systemu Windows implementowane przy użyciu rozszerzenia maszyny wirtualnej Antimalware firmy Microsoft. To rozszerzenie jest skonfigurowany do automatycznego aktualizowania obu ochrony przed złośliwym kodem aparatu i ochrony podpisy jako wersji staną się dostępne. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-3-7"></a>RO kontroli NIST 800 53-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Złośliwy kod ochrony | Wykrywanie na podstawie nonsignature

**RO-3 (7)** system informacji implementuje mechanizmów wykrywanie na podstawie nonsignature złośliwego kodu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża zabezpieczenia ochrony przed złośliwym kodem oparta na hoście dla wszystkich wdrożonych maszyn wirtualnych systemu Windows implementowane przy użyciu rozszerzenia maszyny wirtualnej Antimalware firmy Microsoft. To rozszerzenie jest skonfigurowany do wykonywania heurystyczne wykrycie. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-4a"></a>NIST 800-53 Control SI-4.a

#### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**SI 4.a** organizacji monitoruje systemu informacji wykrywanie ataków i wskaźniki potencjalnych ataków, zgodnie z [przypisania: organizacji zdefiniowane przez monitorowanie celów]; i nieautoryzowanym lokalnej, sieci i zdalnego połączenia.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża analizy dzienników i rozwiązań zabezpieczeń i inspekcji. To rozwiązanie zapewnia obszerne stan zabezpieczeń, ataków i wskaźniki potencjalnych ataków. Pulpit nawigacyjny zabezpieczeń i kontrola zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów wdrożone przy użyciu danych dostępnych w rozwiązań administracyjnych wdrożonych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-4b"></a>NIST 800-53 Control SI-4.b

#### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**SI 4.b** organizacji identyfikuje nieautoryzowanym użyciem systemu informacji za pośrednictwem [przypisania: zdefiniowane przez organizację technik i metod].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża rozwiązanie zabezpieczeń i inspekcji. Identyfikowanie i dostępu do domeny udostępnia pulpit nawigacyjny przegląd stanu systemu tożsamości informacji, w tym liczby nieudanych prób logowania, a bieżąca liczba kont, które są rejestrowane w usłudze. Informacje dostępne w ten pulpit nawigacyjny może pomóc w identyfikacji potencjalnych podejrzanych działań. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-4c"></a>NIST 800-53 Control SI-4.c

#### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**SI 4.c** Organizacja wdraża monitorowania urządzeń strategicznie systemu informacji do zbierania informacji istotnych ustalić organizacji; i ad hoc lokalizacje w systemie śledzenia określonych rodzajów transakcji odsetek w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża analizy dzienników i rozwiązań zabezpieczeń i inspekcji. Bezpieczeństwo i inspekcji pulpit nawigacyjny zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów wdrożone przy użyciu dostępnych danych administracyjnych wdrożonych rozwiązania wgląd w dane monitorowania systemu operacyjnego maszyny Wirtualnej. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-4d"></a>NIST 800-53 Control SI-4.d

#### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**SI 4.d** organizacji chroni informacjami uzyskanymi z monitorowania włamań narzędzi z nieautoryzowanym dostępem, modyfikowanie i usuwanie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Formanty logicznego dostępu służą do ochrony informacji monitorowania w ramach tego planu przed nieautoryzowanym dostępem, modyfikowanie i usuwanie. Usługa Azure Active Directory wymusza zatwierdzonych logicznego dostępu przy użyciu członkostwa w grupach opartej na rolach. Możliwość wyświetlania informacji monitorowania i używać narzędzi do monitorowania może być ograniczony do użytkowników, którzy wymagają tych uprawnień. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-4e"></a>Formant NIST 800 53 SI-4.e

#### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**SI-4.e** Poziom systemu informacji monitorowania aktywności, gdy istnieje wskazanie zwiększone ryzyko operacje organizacyjne i zasoby, osobom, innych organizacji lub kraju, na podstawie informacji egzekwowania prawa, między elementami organizacji informacje dotyczące analizy lub innych wiarygodnych źródeł informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za monitorowanie zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-4f"></a>NIST 800-53 Control SI-4.f

#### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**SI 4.f** organizacji uzyskuje prawne opinii dotyczących monitorowania działania zgodnie z obowiązujących przepisów federalne, zamówienia wykonawczego, dyrektywy, zasady lub przepisy dotyczące systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za monitorowanie zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-4g"></a>NIST 800-53 Control SI-4.g

#### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**SI 4.g** zapewnia organizacji [przypisania: system informacje zdefiniowane przez organizację informacje kontrolne] do [przypisania: techniczną zdefiniowany przez organizację lub ról] [wybór (jeden lub więcej): w razie potrzeby; [Przypisania: zdefiniowane przez organizację częstotliwość]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za monitorowanie zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800 53 kontroli SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Monitorowanie systemu informacji | System wykrywania nieautoryzowanego dostępu systemowe

**RO-4 (1)** organizacji łączy i konfiguruje narzędzia do wykrywania poszczególnych nieautoryzowanego dostępu do systemu wykrywania nieautoryzowanego dostępu systemowe informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za monitorowanie zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800 53 kontroli SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Monitorowanie systemu informacji | Automatyczne narzędzia do analizy w czasie rzeczywistym

**RO-4 (2)** automatycznego narzędzia do obsługi niemal w czasie rzeczywistym analizy zdarzeń jest stosowana w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża analizy dzienników i różne rozwiązania zarządzania, w tym w zakresie zabezpieczeń i inspekcji. Analiza dzienników zapewnia niemal w czasie rzeczywistym analizy zdarzeń między wdrożonych zasobów. Rozwiązań do zarządzania zapewnia kompleksowy przegląd stan zabezpieczeń między domenami rozwiązania. Analiza dzienników zapewnia wgląd w stan zabezpieczeń wdrożonych zasobów między rozwiązaniami do zarządzania wdrożone przy użyciu dostępnych danych. Analiza dzienników można skonfigurować do generowania alertów na podstawie określonych kryteriów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800 53 kontroli SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Monitorowanie systemu informacji | Ruch przychodzący i wychodzący komunikacji

**RO-4 (4)** systemu informacji monitoruje ruch przychodzący i wychodzący komunikacji [przypisania: zdefiniowane przez organizację częstotliwość] nieautoryzowanego lub nietypowych działań lub warunków.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za monitorowanie zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800 53 kontroli SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Monitorowanie systemu informacji | Alerty wygenerowane przez system

**RO-4 (5)** alerty systemu informacji [przypisania: techniczną zdefiniowany przez organizację lub ról] kiedy występują następujące oznaczenia rzeczywiste lub potencjalne naruszenie zabezpieczeń: [przypisania: wskaźniki naruszenia zdefiniowany przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża różnych rozwiązań do zarządzania, w tym w zakresie zabezpieczeń i inspekcji. Analiza dzienników zapewnia niemal w czasie rzeczywistym analizy zdarzeń między wdrożonych zasobów. Rozwiązań do zarządzania zapewnia kompleksowy przegląd stan zabezpieczeń między domenami rozwiązania. Analiza dzienników można skonfigurować do generowania alertów na podstawie określonych kryteriów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800 53 kontroli SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Monitorowanie systemu informacji | Analizowanie anomalii ruch komunikacji

**RO-4 (11)** organizacji analizuje ruch komunikacja wychodząca w granicy zewnętrznego systemu informacji i wybrać [przypisania: wskazuje wnętrza zdefiniowany przez organizację w ramach systemu (np. podsieci, podsystemami)] do Wykryj anomalii.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za analizowanie anomalii ruch komunikacji dla zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800 53 kontroli SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Monitorowanie systemu informacji | Wykrywania nieautoryzowanego dostępu bezprzewodowego

**RO-4 (14)** organizacja wymaga prób system wykrywania nieautoryzowanego dostępu bezprzewodowego do identyfikowania urządzeń bezprzewodowych nieautoryzowanych i do wykrycia ataku i potencjalnych naruszeń/dokonywania do systemu informacji.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Nie kontrolowane przez klienta sprzęt, w tym urządzeń bezprzewodowych jest dozwolony w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure regularnie monitoruje dla nieautoryzowanych sygnały sieci bezprzewodowej, co kwartał, zgodnie z opisem w AC 18. <br /> Microsoft Azure implementuje ten formant w imieniu klientów zarówno PaaS i IaaS. |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800 53 kontroli SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Monitorowanie systemu informacji | Korelowanie informacji monitorowania

**RO-4 (16)** informacji z monitorowania narzędzi w całym systemie informacje są powiązane z organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża analizy dzienników i różne rozwiązania zarządzania, w tym w zakresie zabezpieczeń i inspekcji. Analiza dzienników zapewnia wgląd w stan zabezpieczeń wdrożonych zasobów między rozwiązaniami do zarządzania wdrożone przy użyciu dostępnych danych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800 53 kontroli SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitorowanie systemu informacji | Analiza ruchu / przekonwertować Exfiltration

**RO-4 (18)** organizacji analizuje ruch komunikacja wychodząca w granicy zewnętrznego systemu informacji (np. system obwodowej), w [przypisania: wskazuje wnętrza zdefiniowany przez organizację w ramach systemu (np. podsystemów, podsieci)] do wykrywania konwertowania exfiltration informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za analizowanie ruchu komunikacji dla zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800 53 kontroli SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Monitorowanie systemu informacji | Osoby stwarza większe zagrożenie

**RO-4 (19)** implementuje organizacji [przypisania: zdefiniowane przez organizację dodatkowe monitorowanie] osób, które zostały zidentyfikowane przez [przypisania: źródeł zdefiniowanych przez organizację] jako stwarza zwiększony poziom ryzyka.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialna za monitorowanie osób, które stanowić większe zagrożenie. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800 53 kontroli SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>Monitorowanie systemu informacji | Użytkownicy o odpowiednich uprawnieniach

**RO-4 (20)** implementuje organizacji [przypisania: zdefiniowane przez organizację dodatkowe monitorowanie] uprzywilejowanych użytkowników.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialna za monitorowanie użytkownicy o odpowiednich uprawnieniach. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800 53 kontroli SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Monitorowanie systemu informacji | Usługi sieciowe nieautoryzowanego

**RO-4 (22)** systemu informacji wykrywa usługi sieciowe, które nie zostały autoryzowane lub zatwierdzone przez [przypisania: zdefiniowany przez organizację autoryzacji lub zatwierdzenia procesy] i [wybór (jeden lub więcej): inspekcja; alertów [przypisania: "techniczną zdefiniowany przez organizację lub ról]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiada za Wykrywanie nieautoryzowanych sieci usługi. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800 53 kontroli SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Monitorowanie systemu informacji | Urządzenia z systemem hosta

**RO-4 (23)** implementuje organizacji [przypisania: zdefiniowane przez organizację oparta na hoście mechanizmów monitorowania] na [przypisania: składniki systemowe zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan zbiera dane monitorowania z wdrożonych zasobów, włącznie z danymi z możliwości monitorowania oparta na hoście. Microsoft Monitoring Agent jest zainstalowany na wszystkich maszynach wirtualnych systemu Windows do zbierania danych monitorowania używane przez usługi Analiza dzienników oraz innych rozwiązań do zarządzania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800 53 kontroli SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Monitorowanie systemu informacji | Wskaźniki naruszenia zabezpieczeń

**RO-4 (24)** systemu informacji wykrywa, zbiera dystrybuuje i używa wskaźniki naruszenia zabezpieczeń.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialna za odnajdywanie, zbierania, dystrybucji i przy użyciu wskaźniki naruszenia zabezpieczeń do zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-5a"></a>NIST 800-53 Control SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>Alerty zabezpieczeń, klasyfikatory i dyrektywy

**SI 5.a** organizacja otrzymuje alerty zabezpieczeń systemu informacji, klasyfikatory i z dyrektywami [przypisania: organizacji zdefiniowane przez organizacje zewnętrzne] w sposób ciągły.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie alertami zabezpieczeń, klasyfikatory i dyrektyw wdrożonych przez klienta zasobów (dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-5b"></a>NIST 800-53 Control SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>Alerty zabezpieczeń, klasyfikatory i dyrektywy

**RO 5.b** organizacji generuje alerty zabezpieczeń wewnętrznych, klasyfikatory i dyrektywy uznane za niezbędne.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie alertami zabezpieczeń, klasyfikatory i dyrektywy zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-5c"></a>NIST 800-53 Control SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>Alerty zabezpieczeń, klasyfikatory i dyrektywy

**SI 5.c** organizacji upowszechnia alerty zabezpieczeń, klasyfikatory i dyrektywy: [wybór (jeden lub więcej): [przypisania: techniczną zdefiniowany przez organizację lub ról]; [Przypisania: elementy zdefiniowane w organizacji w organizacji]; [Przypisania: organizacji zdefiniowane przez organizacje zewnętrzne]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie alertami zabezpieczeń, klasyfikatory i dyrektywy zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-5d"></a>NIST 800-53 Control SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>Alerty zabezpieczeń, klasyfikatory i dyrektywy

**SI 5.d** organizacji implementuje dyrektywy zabezpieczeń zgodnie z ustanowionym przedziały czasu lub powiadamia organizacji stopnia niezgodności.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie alertami zabezpieczeń, klasyfikatory i dyrektywy zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800 53 kontroli SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Alerty zabezpieczeń, klasyfikatory i dyrektywy | Automatyczne alertów i klasyfikatory

**RO-5 (1)** organizacja stosuje mechanizmów automatycznego, aby alertów i advisory informacje dostępne w całej organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie alertami zabezpieczeń, klasyfikatory i dyrektywy zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-6a"></a>NIST 800-53 Control SI-6.a

#### <a name="security-function-verification"></a>Weryfikacja — funkcja

**SI 6.a** systemu informacji sprawdza poprawnego działania [przypisania: funkcje zabezpieczeń zdefiniowany przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za weryfikację funkcji zabezpieczeń dla wdrożonych przez klienta zasobów (dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800-53 Control SI-6.b

#### <a name="security-function-verification"></a>Weryfikacja — funkcja

**SI 6.b** system informacje przeprowadza weryfikacji [wybór (jeden lub więcej): [przypisania: stany przejściowe zdefiniowany przez organizację systemu]; po polecenia przez użytkownika z uprawnieniami odpowiednie; [Przypisania: zdefiniowane przez organizację częstotliwość]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za weryfikację funkcji zabezpieczeń zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800-53 Control SI-6.c

#### <a name="security-function-verification"></a>Weryfikacja — funkcja

**SI 6.c** powiadamia systemu informacji [przypisania: techniczną zdefiniowany przez organizację lub ról] testów weryfikacji zabezpieczeń nie powiodło się.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za weryfikację funkcji zabezpieczeń zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-6d"></a>NIST 800-53 Control SI-6.d

#### <a name="security-function-verification"></a>Weryfikacja — funkcja

**SI 6.d** systemu informacji [wybór (jeden lub więcej): zamyka system informacji; ponowne uruchomienie systemu informacji; [Przypisania: zdefiniowane przez organizację alternatywnych akcje]] Po odnalezieniu anomalii.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za weryfikację funkcji zabezpieczeń zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-7"></a>Formant NIST 800 53 SI-7

#### <a name="software-firmware-and-information-integrity"></a>Oprogramowanie, oprogramowania układowego i integralność informacji

**SI 7** organizacja stosuje integralności weryfikacji narzędzia do wykrywania nieautoryzowanych zmian [przypisania: oprogramowania zdefiniowane przez organizację, oprogramowania układowego i informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System Windows udostępnia weryfikacji integralności plików w czasie rzeczywistym, ochrony i odzyskiwania podstawowej systemu plików, które są instalowane w ramach systemu Windows lub autoryzowanych aktualizacje systemu Windows za pomocą funkcji ochrony zasobów systemu Windows (WRP). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800 53 kontroli SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Oprogramowanie, oprogramowania układowego i integralność informacji | Sprawdzanie integralności

**RO-7 (1)** systemu informacje przeprowadza sprawdzanie integralności [przypisania: informacje, oprogramowania układowego i oprogramowania zdefiniowane przez organizację] [wybór (jeden lub więcej): przy uruchamianiu; w [przypisania: stany przejściowe zdefiniowany przez organizację lub zdarzeń związanych z zabezpieczeniami]; [Przypisania: zdefiniowane przez organizację częstotliwość]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System Windows udostępnia weryfikacji integralności plików w czasie rzeczywistym, ochrony i odzyskiwania podstawowej systemu plików, które są instalowane w ramach systemu Windows lub autoryzowanych aktualizacje systemu Windows za pomocą funkcji ochrony zasobów systemu Windows (WRP). WRP umożliwia sprawdzanie integralności w czasie rzeczywistym. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800 53 kontroli SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Oprogramowanie, oprogramowania układowego i integralność informacji | Zautomatyzowane powiadamianie naruszeń integralności

**RO-7 (2)** organizacja stosuje automatycznego narzędzia, które zapewniają powiadomienie [przypisania: techniczną zdefiniowany przez organizację lub ról] na wykrywanie niezgodności podczas weryfikacji integralności.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System Windows udostępnia weryfikacji integralności plików w czasie rzeczywistym, ochrony i odzyskiwania podstawowej systemu plików, które są instalowane w ramach systemu Windows lub autoryzowanych aktualizacje systemu Windows za pomocą funkcji ochrony zasobów systemu Windows (WRP).  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800 53 kontroli SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Oprogramowanie, oprogramowania układowego i integralność informacji | Automatyczne odpowiedź na naruszenie integralności

**RO-7 (5)** systemu informacji automatycznie [wybór (jeden lub więcej): zamyka system informacji; ponowne uruchomienie systemu informacji; implementuje [przypisania: zabezpieczeń zdefiniowany przez organizację]] kiedy są naruszeń integralności odnaleziony.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za automatycznie reagowanie na naruszenie integralności w zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800 53 kontroli SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Oprogramowanie, oprogramowania układowego i integralność informacji | Integracja wykrywania i odpowiedzi

**RO-7 (7)** organizacji włącza wykrywanie nieautoryzowanych [przypisania: zdefiniowany przez organizację zmiany związanych z zabezpieczeniami w systemie informacje] do możliwości organizacyjnej odpowiedzi na zdarzenia.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest wyłączną odpowiedzialność za ochronę integralności oprogramowania i informacje dla wdrożonych przez klienta zasobów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800 53 kontroli SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Oprogramowanie, oprogramowania układowego i integralność informacji | Kod wykonywalny binarnego lub maszyny

**RO-7 (14)** organizacji uniemożliwia korzystanie z kodu binarnego lub pliku wykonywalnego maszyny z źródeł z ograniczoną liczbą lub żadnych gwarancji, jak i bez kodu źródłowego; i zawiera wyjątki wymagania kodu źródłowego tylko w przypadku istotnych misji / wymagań operacyjnych i akceptacji autoryzowanie urzędnika.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy przedsiębiorstw system informacji integralności procedur i mogą ustalić wymagania, aby uzyskać kod źródłowy kodu wykonywalnego, binarne lub maszyny z niektórych źródeł. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-8a"></a>Formant NIST 800 53 SI-8.a

#### <a name="spam-protection"></a>Ochronę przed spamem

**SI 8.a** mechanizmy organizacja wymaga spamu ochrony informacji punktów wejścia i wyjścia systemu do wykrywania i wykonania akcji na niechciane komunikaty.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak serwerów poczty wdrożenia w ramach tego planu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-8b"></a>NIST 800-53 Control SI-8.b

#### <a name="spam-protection"></a>Ochronę przed spamem

**SI 8.b** organizacji aktualizacje spamu mechanizmy ochrony nowych wersji dostępnych zgodnie z organizacyjnej konfiguracji zasad zarządzania i procedur.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak serwerów poczty wdrożenia w ramach tego planu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800 53 kontroli SI-8 (1)

#### <a name="spam-protection--central-management"></a>Spamu ochrony | Centralne zarządzanie

**RO-8 (1)** organizacja zarządza centralnie mechanizmy ochrony wiadomości-śmieci.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak serwerów poczty wdrożenia w ramach tego planu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800 53 kontroli SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Spamu ochrony | Aktualizacje automatyczne

**RO-8 (2)** system informacji automatycznie aktualizuje mechanizmy ochrony wiadomości-śmieci.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak serwerów poczty wdrożenia w ramach tego planu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-10"></a>Formant NIST 800 53 SI-10

#### <a name="information-input-validation"></a>Informacje o sprawdzania poprawności danych wejściowych

**SI 10** informacji system sprawdza poprawność [przypisania: dane wejściowe zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za sprawdzania poprawności danych wejściowych informacje dla wdrożonych przez klienta zasobów (dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-11a"></a>Formant NIST 800 53 SI-11.a

#### <a name="error-handling"></a>Obsługa błędów

**SI 11.a** system informacji generuje komunikaty o błędach, które zawierają informacje niezbędne do działania naprawcze bez ujawniania informacji, które mogą być wykorzystywane przez atakujący.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasoby wdrożone przez ten plan stosować komercyjnych systemów operacyjnych i aplikacji. To oprogramowanie używa najlepsze rozwiązania w branży, aby upewnić się, że poufne informacje nie została ujawniona w komunikatach o błędach. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-11b"></a>Formant NIST 800 53 SI-11.b

#### <a name="error-handling"></a>Obsługa błędów

**SI 11.b** systemu informacji ujawnia tylko komunikaty o błędach [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasoby wdrożone przez ten plan stosować komercyjnych systemów operacyjnych i aplikacji. To oprogramowanie używa najlepsze rozwiązania w branży, aby zapewnić komunikaty o błędach, które są odpowiednie w kontekście zastosowań odbieranie komunikatu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-12"></a>Formant NIST 800 53 SI-12

#### <a name="information-handling-and-retention"></a>Obsługa informacji i przechowywania

**SI 12** organizacji obsługuje i zachowuje informacje w ramach systemu informacji i informacji wyjściowych z systemu zgodnie z obowiązującymi federalne wykonawczego zleceń, dyrektywy, zasady, przepisy, standardy, i wymagań operacyjnych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za obsługę i zachowywanie informacji w ramach wdrożonych przez klienta zasobów (Aby dołączyć aplikacji, systemów operacyjnych, oprogramowania oraz bazy danych) i danych wyjściowych informacji z tych zasobów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-si-16"></a>Formant NIST 800 53 SI-16

#### <a name="memory-protection"></a>Funkcja ochrony pamięci

**SI 16** system informacji implementuje [przypisania: zabezpieczeń zdefiniowany przez organizację] do ochrony jego pamięci pochodzący z wykonania na nieautoryzowanego kodu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System Windows ma ochrony w celu zapobiegania wykonywanie kodu w lokalizacji pamięci ograniczone: nie wykonać (NX), losowe generowanie układu przestrzeni adresów (ASLR) i funkcja Zapobieganie wykonywaniu danych (DEP). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |

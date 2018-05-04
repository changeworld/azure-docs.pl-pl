---
title: "Azure zabezpieczeń i zgodności plan - automatyzacji aplikacji sieci Web FedRAMP - identyfikacji i uwierzytelniania"
description: Automatyzacja aplikacji sieci Web FedRAMP - identyfikacji i uwierzytelniania
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2018
---
# <a name="identification-and-authentication-ia"></a>Identyfikacji i uwierzytelniania (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800 53 kontroli IA-1

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Identyfikacja oraz zasady uwierzytelniania i procedury

**IA-1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] zasad identyfikacji i uwierzytelniania, którego dotyczy cel, zakres, ról, obowiązki, zarządzania zobowiązań, koordynacji między jednostki organizacyjnej i zgodności; i procedur w celu ułatwienia wykonania nadzór nad identyfikacją i zasady uwierzytelniania i skojarzone identyfikacji i uwierzytelniania; przegląda i aktualizuje bieżące zasady identyfikacji i uwierzytelniania [przypisania: zdefiniowane przez organizację częstotliwość]; i identyfikacji i uwierzytelniania procedury [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Zasada identyfikacji i uwierzytelniania na poziomie przedsiębiorstwa i procedur klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800 53 kontroli IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Identyfikacji i uwierzytelniania (organizacyjnej użytkowników)

**IA-2** unikatowo identyfikuje system informacji i uwierzytelnia użytkowników w organizacji (lub procesów działających w imieniu użytkowników w organizacji).

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Konta utworzone przez tego planu ma unikatowych identyfikatorów. Wbudowanych kont z-unikatowe identyfikatory są wyłączone lub usunięte. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800 53 kontroli IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Dostęp sieciowy do kont uprzywilejowanych

**IA-2 (1)** system informacji implementuje uwierzytelnianie wieloskładnikowe dla dostępu do sieci do kont uprzywilejowanych.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wdrażanie uwierzytelniania wieloskładnikowego, dostępu do sieci do kont uprzywilejowanych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800 53 kontroli IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Dostęp sieciowy do konta nieuprzywilejowanego

**IA-2 (2)** system informacji implementuje uwierzytelnianie wieloskładnikowe dla dostępu do sieci, aby konta bez uprawnień.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wdrażanie uwierzytelniania wieloskładnikowego, dostępu do sieci na konta nieuprzywilejowanego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800 53 kontroli IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Lokalnego dostępu do kont uprzywilejowanych

**IA-2 (3)** system informacji implementuje uwierzytelnianie wieloskładnikowe na potrzeby dostępu lokalnego do kont uprzywilejowanych.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Odbiorca nie ma lokalnego dostępu do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na dostęp lokalny, chyba, że wymagany jest dostęp do fizycznych. Uprawnienia administratora lokalnego może być używana tylko do rozwiązywania problemów w przypadkach, gdy serwer członkowski występują problemy z siecią i uwierzytelniania domeny nie działa. <br /> Azure implementuje uwierzytelnianie wieloskładnikowe na potrzeby dostępu lokalnego za pośrednictwem mechanizmy kontroli dostępu, wymagane fizycznego dostępu do środowiska. Pokoje w centrach danych platformy Azure, która zawiera wszystkie systemy infrastruktury platformy Azure w obrębie granicy systemu są ograniczone przez różne mechanizmy zabezpieczeń fizycznych, w tym wymaganie badging dostęp do firmowej kart inteligentnych i urządzeń biometrycznych. Obu metod uwierzytelniania są wymagane do fizycznego dostępu w momencie służąca do colocations centrum danych Azure. |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800 53 kontroli IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Lokalnego dostępu do konta nieuprzywilejowanego

**IA-2 (4)** system informacji implementuje uwierzytelnianie wieloskładnikowe dla lokalnego dostępu do konta z niskimi uprawnieniami.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Odbiorca nie ma lokalnego dostępu do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure uwzględnia wszystkie konta Microsoft Azure dla instytucji rządowych, używane przez pracowników firmy Microsoft Azure dla instytucji rządowych jako uprzywilejowane. Uwierzytelnianie wieloskładnikowe zaimplementowano dla wszystkich kont pracowników firmy Microsoft Azure dla instytucji rządowych przy użyciu kart inteligentnych i numery PIN, w tym dostęp lokalny. |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800 53 kontroli IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Uwierzytelnianie grup

**IA-2 (5)** organizacja wymaga osób uwierzytelniania za pomocą poszczególnych uwierzytelniania podczas uwierzytelniania grupy jest stosowane.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Brak udostępnionego grupy kont są włączone na zasobów wykorzystywanych przez ten plan. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800 53 kontroli IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Dostęp sieciowy do kont uprzywilejowanych - odporność powtarzania

**IA-2 (8)** system informacji implementuje mechanizmy uwierzytelniania powtarzania odporne na dostęp do sieci do kont uprzywilejowanych.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Dostęp do zasobów wykorzystywanych przez ten plan jest chroniony przed atakami powtarzania wbudowanej funkcji protokołu Kerberos w usłudze Azure Active Directory, usługi Active Directory i systemu operacyjnego Windows. W uwierzytelnianiu Kerberos uwierzytelniania wysyłane przez klienta zawiera dodatkowe dane, takie jak listy IP zaszyfrowane, klienta sygnatury czasowe i okres istnienia biletu. Jeśli pakiet jest odtwarzany, zostanie sprawdzony sygnatury czasowej. Jeśli znacznik czasu jest starsza niż, lub taka sama jak poprzednie uwierzytelniania, pakiet zostanie odrzucony. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800 53 kontroli IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Dostęp sieciowy do konta nieuprzywilejowanego — odporność powtórzeń

**IA-2 (9)** system informacji implementuje mechanizmy uwierzytelniania powtarzania odporne na dostęp do sieci, aby konta bez uprawnień.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Dostęp do zasobów wykorzystywanych przez ten plan jest chroniony przed atakami powtarzania wbudowanej funkcji protokołu Kerberos w usłudze Azure Active Directory, usługi Active Directory i systemu operacyjnego Windows. W uwierzytelnianiu Kerberos uwierzytelniania wysyłane przez klienta zawiera dodatkowe dane, takie jak listy IP zaszyfrowane, klienta sygnatury czasowe i okres istnienia biletu. Jeśli pakiet jest odtwarzany, zostanie sprawdzony sygnatury czasowej. Jeśli znacznik czasu jest starsza niż, lub taka sama jak poprzednie uwierzytelniania, pakiet zostanie odrzucony. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800 53 kontroli IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Dostęp zdalny — osobne urządzenia

**IA-2 (11)** system informacji implementuje uwierzytelnianie wieloskładnikowe dla dostępu zdalnego do uprzywilejowanego i konta nieuprzywilejowanego tak, aby jednym z czynników jest udostępniana przez urządzenie oddzielona od systemu, aby uzyskać dostęp, a urządzenie spełnia [ Przypisania: zdefiniowane przez organizację siły wymagań mechanizmu].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wdrażanie uwierzytelniania wieloskładnikowego, aby zdalnie uzyskiwać dostęp do zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800 53 kontroli IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identyfikacji i uwierzytelniania (użytkowników w organizacji) | Akceptacji Piv poświadczeń

**IA-2 (12)** systemu informacji akceptuje i elektronicznie weryfikuje poświadczenia weryfikacji tożsamości osobistej (PIV).

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za akceptowanie i weryfikowania poświadczeń weryfikacji tożsamości osobistej (PIV). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800 53 kontroli IA-3

#### <a name="device-identification-and-authentication"></a>Urządzenie identyfikacji i uwierzytelniania

**IA-3** unikatowo identyfikuje system informacji i uwierzytelnia [przypisania: określonych zdefiniowany przez organizację i/lub typów urządzeń] przed nawiązaniem [wybór (jeden lub więcej): lokalny; zdalnego; sieci] połączenia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wdrażanie urządzenia identyfikacji i uwierzytelniania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 Control IA-4.a

#### <a name="identifier-management"></a>Identyfikator zarządzania

**IA 4.a** organizacja zarządza identyfikatory systemu informacji przez odebranie autoryzacji z [przypisania: techniczną zdefiniowany przez organizację lub ról] można przypisać osoby, identyfikator grupy, roli lub urządzenia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie identyfikatorów (tj. osób, grup, ról i urządzeń) dla zasobów klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 Control IA-4.b

#### <a name="identifier-management"></a>Identyfikator zarządzania

**IA-4.b** organizacja zarządza identyfikatory systemu informacji po wybraniu identyfikatora, który identyfikuje osoby, grupy, roli lub urządzenia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan monity podczas wdrażania identyfikatorów określonych klientów dla poszczególnych kont.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 Control IA-4.c

#### <a name="identifier-management"></a>Identyfikator zarządzania

**IA 4.c** organizacja zarządza informacji systemu identyfikatory, przypisując identyfikator do zamierzonego osoby, grupy, roli lub urządzenia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie identyfikatorów (tj. osób, grup, ról i urządzeń) dla zasobów klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800 53 kontroli IA-4.d

#### <a name="identifier-management"></a>Identyfikator zarządzania

**IA 4.d** organizacja zarządza identyfikatory systemu informacji przez zapobiegania ponownemu identyfikatory [przypisania: okres zdefiniowany organizacji].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Usługi Active Directory i lokalne konta systemu operacyjnego Windows są przypisywane identyfikator unikatowy zabezpieczeń (SID). Azure kont usługi Active Directory są przypisywane globalnie unikatowy identyfikator obiektu. Te unikatowe identyfikatory nie podlegają ponownego użycia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800 53 kontroli IA-4.e

#### <a name="identifier-management"></a>Identyfikator zarządzania

**IA-4.e** Organizacja zarządza identyfikatory systemu informacji przez wyłączenie identyfikator po [przypisania: organizacji zdefiniowane okresie braku aktywności].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje zaplanowanego zadania dla usługi Active Directory automatycznie wyłączyć kont po 35 dni nieaktywności. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800 53 kontroli IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>Identyfikator zarządzania | Określenie stanu użytkownika

**IA-4 (4)** organizacja zarządza pojedyncze identyfikatory, przez który unikatowo identyfikuje poszczególnych jako [przypisania: właściwości zdefiniowane przez organizację identyfikowanie poszczególnych stanu].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Azure Active Directory i obsługi usługi Active Directory określające wykonawców, dostawców i innych typów użytkownika za pomocą konwencji nazewnictwa stosowane do ich identyfikatorów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800 53 kontroli IA-5.a

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.a** organizacja zarządza wystawców uwierzytelnienia systemu informacji przy weryfikacji w ramach dystrybucji początkowego uwierzytelniania, tożsamości osoby, grupy, roli lub urządzenie odbierające wystawcy uwierzytelnienia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie wystawców uwierzytelnienia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 Control IA-5.b

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA-5.b** organizacja zarządza wystawców uwierzytelnienia systemu informacji ustanawiając zawartości początkowego uwierzytelniania dla uwierzytelnienia zdefiniowany przez organizację.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Cała zawartość początkowego uwierzytelniania dla kont utworzonych przez ten plan spełnia wymagania określone w IA-5 (1) zweryfikowane, gdy został określony przez klienta podczas wdrażania.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800 53 kontroli IA-5.c

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.c** organizacja zarządza wystawców uwierzytelnienia systemu informacji przez zapewnienie, że wystawców uwierzytelnienia mają wystarczającą długość mechanizm ich przeznaczenia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Używany przez spełnia wymagania tego planu dla siły zgodnie z wymaganiami FedRAMP wystawców uwierzytelnienia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 Control IA-5.d

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.d** organizacja zarządza wystawców uwierzytelnienia systemu informacji przez ustanawiania i wykonywania procedur administracyjnych dla dystrybucji początkowego uwierzytelniania, utracone naruszony lub uszkodzony wystawców uwierzytelnienia i cofnięcia wystawcy uwierzytelnienia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie wystawców uwierzytelnienia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800 53 kontroli IA-5.e

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA-5.e** Organizacja zarządza wystawców uwierzytelnienia systemu informacji przez zmianę domyślnej zawartości wystawców uwierzytelnienia przed instalacją systemu informacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Wartości domyślne zostały zmienione wszystkich wystawców uwierzytelnienia dla składników tego planu. Wystawców uwierzytelnienia są określone przez klienta podczas wdrażania tego rozwiązania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800 53 kontroli IA-5.f

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.f** organizacja zarządza wystawców uwierzytelnienia systemu informacji ustanawiając minimalny i maksymalny okres istnienia ograniczenia i warunki ponownemu wystawców uwierzytelnienia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zarządzanie wystawców uwierzytelnienia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800 53 kontroli IA-5.g

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.g** organizacja zarządza wystawców uwierzytelnienia systemu informacji odświeżając zmiana/wystawców uwierzytelnienia [przypisania: organizacji zdefiniowane przez okres czasu według typu uwierzytelniania].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasad grupy jest ustanowić i skonfigurowany tak, aby zaimplementować ograniczenia okres istnienia haseł (60 dni). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800 53 kontroli IA-5.h

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.h** organizacja zarządza wystawców uwierzytelnienia systemu informacje, aby chronić zawartość uwierzytelniania z nieuprawnionym ujawnieniem i modyfikacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje Key Vault w celu ochrony zawartości uwierzytelniania przed nieuprawnionym ujawnieniem i modyfikacji. Następujące wystawców uwierzytelnienia są przechowywane w magazynie kluczy: Azure hasło dla konta Wdróż, hasło administratora maszyny wirtualnej, hasło konta usługi programu SQL Server. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 Control IA-5.i

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.i** organizacja zarządza wystawców uwierzytelnienia systemu informacji wymagając od osób do wykonania i o urządzeniach wdrożenia, określonych zabezpieczeń i ochrony do ochrony wystawców uwierzytelnienia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje Key Vault w celu ochrony zawartości uwierzytelniania przed nieuprawnionym ujawnieniem i modyfikacji. Następujące wystawców uwierzytelnienia są przechowywane w magazynie kluczy: Azure hasło dla konta Wdróż, hasło administratora maszyny wirtualnej, hasło konta usługi programu SQL Server. Magazyn kluczy szyfruje klucze i klucze tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych i hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 Control IA-5.j

#### <a name="authenticator-management"></a>Wystawca uwierzytelnienia zarządzania

**IA 5.j** organizacja zarządza wystawców uwierzytelnienia systemu informacji zmieniając wystawców uwierzytelnienia dla konta grupy/roli, kiedy członkostwo tych kont zmiany.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Brak udostępnionego grupy kont są włączone na zasobów wykorzystywanych przez ten plan. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-1a"></a>Tj NIST 800 53 kontroli IA-5 (1).

#### <a name="authenticator-management--password-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie oparte na hasłach

**.A IA-5 (1)** system informacji dla uwierzytelniania opartego na hasłach wymusza złożoności hasła [przypisania: mieszać zdefiniowany przez organizację wymagania dotyczącą uwzględniania wielkości liter, liczba znaków, wielkie litery, małe litery, cyfry i znaki specjalne, w tym minimalne wymagania dla każdego typu].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasad grupy jest ustanowić i skonfigurowany tak, aby wymusić wymagania dotyczące złożoności hasła dla kont lokalnych maszyn wirtualnych i kont usługi AD.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800 53 b kontroli IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie oparte na hasłach

**B IA-5 (1)** system informacji dla uwierzytelniania opartego na hasłach wymusza co najmniej następującą liczbę znaków zmienione podczas tworzenia nowego hasła: [przypisania: liczba zdefiniowanych przez organizację].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących uwierzytelniania opartego na hasłach w zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800 53 .c kontroli IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie oparte na hasłach

**.C IA-5 (1)** system informacji dla uwierzytelniania opartego na hasłach przechowuje i przesyła tylko chronione kryptograficznie hasła.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Usługi Azure Directory służy do zapewnienia wszystkie hasła kryptograficznie ochrony podczas przechowywania i transmisji. Hasła przechowywane przez usługę Active Directory i lokalnie na wdrożenia maszyn wirtualnych systemu Windows są automatycznie wartość skrótu w ramach funkcji zabezpieczeń. Sesje uwierzytelniania pulpitu zdalnego są zabezpieczone, upewnij się, że wystawców uwierzytelnienia są chronione podczas przesyłania przy użyciu protokołu TLS. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800 53 .d kontroli IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie oparte na hasłach

**.D IA-5 (1)** system informacji dla uwierzytelniania opartego na hasłach wymusza stosowanie hasła minimalny i maksymalny okres istnienia ograniczenia [przypisania: zdefiniowane przez organizację numerów okres istnienia minimalny, okres istnienia maksymalną].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasady grupy są połączenia i skonfigurowany tak, aby wymusić ograniczenia dotyczące haseł, które wymuszają minimalna (1 dzień) i (60 dni) maksymalny okres istnienia ograniczeń dla kont lokalnych i kont usługi AD. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800 53 .e kontroli IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie oparte na hasłach

**IA-5 (1).e** Informacje, do uwierzytelniania opartego na hasłach zabronione ponownemu użyciu hasła dla [przypisania: liczba zdefiniowanych przez organizację] generacji.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasad grupy jest ustanowić i skonfigurowany tak, aby wymusić ograniczeń na warunki ponownemu (24 haseł) dla kont lokalnych i kont usługi AD. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800 53 .f kontroli IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie oparte na hasłach

**.F IA-5 (1)** system informacji dla uwierzytelniania opartego na hasłach zezwala na korzystanie z tymczasowego hasła dla logowania systemu za pomocą natychmiastowe zmiany hasła stałe.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Usługa Azure Active Directory służy do zarządzania kontroli dostępu do systemu informacji. Zawsze, gdy konto został początkowo utworzony lub tymczasowego hasła jest generowany, Azure Active Directory jest zastosować wymaganie, że użytkownik zmienić hasło przy następnym logowaniu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-2a"></a>Tj NIST 800 53 kontroli IA-5 (2).

#### <a name="authenticator-management--pki-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie za pomocą infrastruktury kluczy publicznych

**.A IA-5 (2)** system informacji, do uwierzytelniania na podstawie infrastruktury kluczy publicznych weryfikuje certyfikaty, tworząc i weryfikowanie ścieżki certyfikacji do kotwicy zaufania zaakceptowane tym sprawdzanie informacji o stanie certyfikatów.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących uwierzytelnianie za pomocą infrastruktury kluczy publicznych w ramach zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800 53 b kontroli IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie za pomocą infrastruktury kluczy publicznych

**(2) IA-5, b** systemu informacji dla uwierzytelniania opartego na infrastruktury kluczy publicznych wymusza uprawnienia dostępu do odpowiedniego klucza prywatnego.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących uwierzytelnianie za pomocą infrastruktury kluczy publicznych w ramach zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800 53 .c kontroli IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie za pomocą infrastruktury kluczy publicznych

**.C IA-5 (2)** systemu informacje, do uwierzytelniania na podstawie infrastruktury kluczy publicznych mapuje uwierzytelniona tożsamość konta użytkownika lub grupy.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących uwierzytelnianie za pomocą infrastruktury kluczy publicznych w ramach zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800 53 .d kontroli IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie za pomocą infrastruktury kluczy publicznych

**.D IA-5 (2)** systemu informacje, do uwierzytelniania na podstawie infrastruktury kluczy publicznych implementuje lokalnej pamięci podręcznej danych odwołania do obsługi odnajdywania ścieżki i weryfikacja w przypadku brakiem dostępu do informacji odwołania za pośrednictwem sieci.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących uwierzytelnianie za pomocą infrastruktury kluczy publicznych w ramach zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800 53 kontroli IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Wystawca uwierzytelnienia zarządzania | w osoby lub zaufanego rejestracji innych firm

**IA-5 (3)** organizacja wymaga rejestracji procesu odbierania [przypisania: typy zdefiniowane w organizacji i/lub określonych wystawców uwierzytelnienia] prowadzone [zaznaczenia: osobiście; w innej zaufanej] przed [przypisania: Urząd rejestrowania zdefiniowanych przez organizację] o autoryzacji przez [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za rejestrację wystawców uwierzytelnienia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800 53 kontroli IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Wystawca uwierzytelnienia zarządzania | Obsługa automatycznego ustalania siły hasła

**IA-5 (4)** organizacja stosuje automatycznego narzędzia do ustalenia, czy hasło wystawców uwierzytelnienia są wystarczająco silne do zaspokojenia [przypisania: wymagania zdefiniowane w organizacji].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Konta użytkowników z tego planu obejmują AD i kont użytkowników lokalnych. Oba te zapewniają mechanizmy, które Wymuszanie zgodności z wymaganiami ustanowionych haseł, aby można było utworzyć hasła początkowego i podczas zmiany hasła. Azure Active Directory jest automatyczne narzędzie w celu ustalenia, czy hasło wystawców uwierzytelnienia są wystarczająco silne do zaspokojenia długość hasła, złożoność, obracanie i okres istnienia ograniczenia ustalone w IA-5 (1). Usługi Azure Active Directory zapewnia, że siły uwierzytelniania hasła podczas tworzenia spełnia tymi standardami. Określony klienta hasła używane do wdrażania tego rozwiązania są sprawdzane w celu spełnienia wymagań odnośnie siły hasła. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800 53 kontroli IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Wystawca uwierzytelnienia zarządzania | Ochrona wystawców uwierzytelnienia

**IA-5 (6)** organizacji chroni wystawców uwierzytelnienia proporcjonalnie do tej kategorii informacji, do którego wystawca uwierzytelnienia pozwala dostępu.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zabezpieczenie wystawców uwierzytelnienia. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800 53 kontroli IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Wystawca uwierzytelnienia zarządzania | Nie osadzonych niezaszyfrowane uwierzytelnienia statyczne

**IA-5 (7)** organizacji zapewnia nie osadzone w aplikacji i skryptów dostępu lub przechowywane na klawiszy funkcyjnych niezaszyfrowane statycznych wystawców uwierzytelnienia.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Nie jest używana z niezaszyfrowane statycznych wystawców uwierzytelnienia osadzone w aplikacji, dostęp do skryptów lub wdrożone przez ten plan klawisze funkcyjne. Dowolny skrypt lub aplikację, która używa uwierzytelniania nawiązuje połączenie się z kontenerem usługi Azure Key Vault przed każdym użyciu. Dostęp do usługi Azure Key Vault kontenery podlega inspekcji, co umożliwia wykrywanie naruszeń zakaz, jeżeli używane konto usługi dostępu do systemu bez odpowiedniego wywołania do kontenera usługi Azure Key Vault. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800 53 kontroli IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Wystawca uwierzytelnienia zarządzania | Wiele kont systemu informacji

**IA-5 (8)** implementuje organizacji [przypisania: zabezpieczeń zdefiniowany przez organizację] ryzyko ujawnienia z powodu osoby posiadające kont w wielu systemach informacji zarządzania.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient może polegać na zabezpieczenia zabezpieczenia na poziomie przedsiębiorstwa do zarządzania ryzykiem związanym z osoby posiadające kont w wielu systemach. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800 53 kontroli IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Wystawca uwierzytelnienia zarządzania | Uwierzytelnianie na podstawie tokenu sprzętu

**IA-5 (11)** system informacji uwierzytelniania tokenów sprzętu wykorzystuje mechanizmy, które spełniają [przypisania: wymagania zdefiniowane w organizacji tokenu jakości].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujące mechanizmy spełniają wymagania jakości uwierzytelniania tokenów sprzętu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800 53 kontroli IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Wystawca uwierzytelnienia zarządzania | Czas wygaśnięcia pamięci podręcznej wystawców uwierzytelnienia

**IA-5 [13]** informacji zabronione użycie pamięci podręcznej wystawców uwierzytelnienia po [przypisania: okres zdefiniowany organizacji].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Nie wdrożono przez ten plan zasobów są skonfigurowane do korzystania z pamięci podręcznej wystawców uwierzytelnienia. Uwierzytelnianie wdrożonych maszyn wirtualnych wymaga wprowadzenia wystawcy uwierzytelnienia podczas uwierzytelniania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800 53 kontroli IA-6

#### <a name="authenticator-feedback"></a>Wystawca uwierzytelnienia opinii

**IA-6** systemu informacji ukrywa opinii informacje dotyczące uwierzytelniania podczas procesu uwierzytelniania, aby chronić informacje z eksploatacji/wykorzystanie przez nieautoryzowane osoby.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Dostęp do zasobów wykorzystywanych przez ten plan jest za pośrednictwem pulpitu zdalnego i korzysta z uwierzytelniania systemu Windows. Domyślne zachowanie sesji uwierzytelniania systemu Windows maski hasła, gdy dane wejściowe podczas sesji uwierzytelniania.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800 53 kontroli IA-7

#### <a name="cryptographic-module-authentication"></a>Uwierzytelnianie modułu kryptograficznego

**IA-7** system informacji implementuje mechanizmy do uwierzytelniania modułu kryptograficznego, które spełniają wymagania obowiązujących przepisów federalne, zamówienia wykonawczego, dyrektywy, zasad, przepisów, standardów i wskazówki dotyczące takich uwierzytelnianie.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Uwierzytelnianie systemu Windows, pulpitu zdalnego i funkcji BitLocker są zatrudnieni przez ten plan. Te składniki można skonfigurować polegać na FIPS 140 sprawdzania poprawności modułów kryptograficznych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800 53 kontroli IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Identyfikacji i uwierzytelniania (z systemem innym niż organizacyjnej użytkowników)

**IA-8** systemu informacji unikatowo identyfikuje i uwierzytelnia użytkowników w organizacji z systemem innym niż (lub procesów działających w imieniu użytkowników w organizacji z systemem innym niż).

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za identyfikacji i uwierzytelniania-organizacyjnej użytkowników uzyskujących dostęp do zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800 53 kontroli IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identyfikacji i uwierzytelniania (z systemem innym niż organizacyjnej użytkowników) | Akceptacji Piv poświadczenia z innych agencji

**IA-8 (1)** systemu informacji akceptuje i elektronicznie weryfikuje poświadczenia weryfikacji tożsamości osobistej (PIV) z innymi agencje federalne.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za akceptowanie i weryfikowania poświadczeń weryfikacji tożsamości osobistej (PIV) wydanego przez inne agencje federalne. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800 53 kontroli IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identyfikacji i uwierzytelniania (z systemem innym niż organizacyjnej użytkowników) | Akceptacji poświadczeń innych firm

**IA-8 (2)** system informacji akceptuje tylko zatwierdzone FICAM poświadczeń innych firm.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za akceptuje tylko innych poświadczeń, które zostały zatwierdzone przez inicjatywy federalne tożsamości, poświadczeń i Solutions Framework zaufania dostępu do zarządzania (FICAM). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800 53 kontroli IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identyfikacji i uwierzytelniania (z systemem innym niż organizacyjnej użytkowników) | Użyj zatwierdzone Ficam produktów

**IA-8 (3)** organizacji jest stosowana tylko składniki systemowe zatwierdzone FICAM informacji w [przypisania: systemów informacje zdefiniowane przez organizację] do akceptowania poświadczeń innych firm.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wykorzystujących tylko federalne tożsamości, poświadczeń, i inicjatywy Solutions Framework zaufania dostępu do zarządzania (FICAM) zatwierdzonych zasobów do akceptowania poświadczeń innych firm. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800 53 kontroli IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identyfikacji i uwierzytelniania (z systemem innym niż organizacyjnej użytkowników) | Korzystanie z profilów wystawiony Ficam

**IA-8 (4)** systemu informacji odpowiada wystawiony FICAM profilów.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zgodnych z profilów wystawiony przez inicjatywy federalne tożsamości, poświadczeń i Solutions Framework zaufania dostępu do zarządzania (FICAM). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |

---
title: Plan zabezpieczeń platformy Azure i zgodność — automatyzacji aplikacji sieci Web FedRAMP — kontrola dostępu
description: Automatyzacja aplikacji sieci Web FedRAMP — kontroli dostępu
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 3d30f889c4d7ed0dafcf8559e8987090c03f4d5e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421501"
---
# <a name="access-control-ac"></a>Kontrola dostępu (AC)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-ac-1"></a>Formant NIST 800 53 AC-1

#### <a name="access-control-policy-and-procedures"></a>Zasady kontroli dostępu i procedur

**AC 1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] zasady kontroli dostępu, którego dotyczy cel, zakres, ról, obowiązki zobowiązań zarządzania, koordynacji wśród jednostki organizacyjnej i zgodności; i procedury w celu ułatwienia implementacji zasad kontroli dostępu i kontroli dostępu skojarzone; przegląda i aktualizuje bieżące zasady kontroli dostępu [przypisania: zdefiniowane przez organizację częstotliwość]; i procedur kontroli dostępu [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa i procedur klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2a"></a>Formant NIST 800 53 AC-2.a

#### <a name="account-management"></a>Zarządzanie kontami

**Ak 2.a** identyfikuje i wybiera następujące typy informacji kont systemowych do obsługi funkcji organizacji misji/biznesowe organizacji: [przypisania: typy kont systemu zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan opiera się na i implementuje następujące typy kont systemu: użytkowników usługi Azure Active Directory (używany do wdrożenia rozwiązania oraz zarządzanie dostępem do zasobów platformy Azure), użytkownicy systemu Windows (zarządzane przez usługę Active Directory), konto usługi programu SQL Server. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2b"></a>Formant NIST 800 53 AC-2.b

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.b** organizacji przypisuje konta menedżerów kont systemowych informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przypisywanie menedżerów kontom objęci AC 02.a. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2c"></a>Formant NIST 800 53 AC-2.c

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.c** organizacji ustanawia warunki członkostwa grup i ról.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za ustanawianie kryteria członkostwa ról i grup dla typów konta kontrolowane przez klienta (zobacz AC 02.a). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2d"></a>Formant NIST 800 53 AC-2.d

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.d** organizacji określa autoryzowanych użytkowników systemu informacji, członkostwo grup i ról i dostępu autoryzacje (tj. uprawnienia) i innych atrybutów (zgodnie z wymaganiami) dla poszczególnych kont.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy mogą polegać na ustalonych konta przedsiębiorstw procesu autoryzacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2e"></a>Formant NIST 800 53 AC-2.e

#### <a name="account-management"></a>Zarządzanie kontami

**AC-2.e** Organizacja wymaga zatwierdzenia przez [przypisania: techniczną zdefiniowany przez organizację lub ról] dla żądań kierowanych do utworzenia informacji kont systemowych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy mogą polegać na ustalonych konta przedsiębiorstw procesu autoryzacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2f"></a>Formant NIST 800 53 AC-2.f

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.f** organizacji tworzy, umożliwia modyfikuje, wyłącza i usuwania kont systemu informacji zgodnie z [przypisania: procedury zdefiniowane przez organizację lub warunków].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy mogą polegać na proces zarządzania ustalonych konta poziomie przedsiębiorstwa. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2g"></a>Formant NIST 800 53 AC-2.g

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.g** organizacji monitoruje informacji systemu kont.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania zabezpieczeń i kontrola tożsamości i dostępu do pulpitu nawigacyjnego. Ten pulpit nawigacyjny umożliwia menedżerów konta do monitorowania informacji systemu kont. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2h"></a>Formant NIST 800 53 AC-2.h

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.h** organizacji powiadamia menedżerowie kont, gdy konta nie są już wymagane; gdy użytkownicy są zakończone lub przeniesione; i gdy obciążenie systemu poszczególnych informacji lub do ograniczonego zmiany.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć procesu, aby powiadomić menedżera odpowiedniego konta, gdy konto nie jest już potrzebne. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2i"></a>Formant NIST 800 53 AC-2.i

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.i** organizacji zezwala na dostęp do systemu informacji, w oparciu o autoryzacji dostępu prawidłowe; system zamierzone użycie; i inne atrybuty zgodnie z wymaganiami organizacji lub funkcje skojarzone misji/biznesowych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć procesu autoryzacji dostępu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2j"></a>Formant NIST 800 53 AC-2.j

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.j** organizacji monitoruje konta dla zgodności z wymaganiami zarządzania konta [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za recenzowania kont kontrolowane przez klienta z częstotliwością wymagane do określenia, czy konta są zgodne z wszystkich wymagań organizacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-2k"></a>Formant NIST 800 53 AC-2.k

#### <a name="account-management"></a>Zarządzanie kontami

**AC 2.k** organizacji ustanawia proces ponownego wystawienia poświadczeń konta udostępnionego grupy (jeśli je wdrożono) po usunięciu osób z grupy.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć procesu dla zarządzania poświadczeniami konta grupy. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-1"></a>Ak kontroli NIST 800 53-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Zarządzanie kontami | Automatyczne zarządzanie kontami systemu

**Ak-2 (1)** automatyczne mechanizmy do obsługi zarządzania kont systemowych informacji jest stosowana w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania zabezpieczeń i kontrola tożsamości i dostępu do pulpitu nawigacyjnego. Ten pulpit nawigacyjny umożliwia menedżerów konta do monitorowania informacji systemu kont. Może być skonfigurowana do wysyłania alertów, gdy podejrzewa się nietypowe działania lub występują inne zdarzenia, wstępnie zdefiniowanych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-2"></a>Ak kontroli NIST 800 53-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Zarządzanie kontami | Usunięcie konta tymczasowe / awaryjnego

**Ak-2 (2)** systemu informacji automatycznie [zaznaczenia: usuwa; wyłącza] tymczasowe i awaryjnego konta po [przypisania: organizacji zdefiniowane przez okres czasu dla każdego typu konta].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan nie powoduje wdrożenia tymczasowe lub awaryjnego kont. Ręcznie nie jest wyłączona, kontroler domeny wdrożone automatycznie wyłącza wszystkie nieaktywne konta po 35 dni. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-3"></a>Ak kontroli NIST 800 53-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Zarządzanie kontami | Wyłącz nieaktywne konta

**Ak-2 (3)** system informacji automatycznie wyłącza nieaktywne konta po [przypisania: okres zdefiniowany organizacji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Kontroler domeny wdrożone przez ten plan jest skonfigurowany do wyłączyć wszystkich kont użytkowników po 35 dni nieaktywności. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-4"></a>Ak kontroli NIST 800 53-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Zarządzanie kontami | Akcje automatyczne inspekcji

**Ak-2 (4)** systemu informacji automatycznie kontroli konta tworzenie, modyfikowanie, włączanie, wyłączenie i usuwania działań i powiadamia, [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje następujące typy kont systemu: użytkowników usługi Azure Active Directory, użytkowników systemu Windows, konto usługi programu SQL Server. Akcje zarządzania konta w usłudze Azure Active Directory generują zdarzenie w dzienniku aktywności platformy Azure; Poziom systemu operacyjnego konta akcji zarządzania generują zdarzenie w dzienniku systemu. Te dzienniki są zbierane przez analizy dzienników i przechowywane w obszarze roboczym analizy dzienników. Analiza dzienników można skonfigurować do wysyłania alertów w przypadku wystąpienia zdarzeń wstępnie zdefiniowane.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-5"></a>Ak kontroli NIST 800 53-2 (5)

#### <a name="account-management--inactivity-logout"></a>Zarządzanie kontami | Wyloguj aktywności

**Ak-2 (5)** organizacja wymaga, że limit podczas logowania użytkowników [przypisania: zdefiniowane przez organizację okres aktywności oczekiwano lub podczas logowania opis].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć zasady, które użytkownicy Wyloguj się, gdy będą one są nieaktywne na pewien czas (lub innych czynników). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800 53 kontrolować tj AC-2 (7).

#### <a name="account-management--role-based-schemes"></a>Zarządzanie kontami | Schematy oparte na rolach

**Ak-2 (7) .a** ustanawia i zarządza kontami użytkowników uprzywilejowanych zgodnie z schemat dostępu opartej na rolach, który organizuje informacji dozwolony dostęp do systemu oraz uprawnień do ról w organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje następujące typy kont systemu: użytkowników usługi Azure Active Directory, użytkowników systemu Windows, konto usługi programu SQL Server. Uprawnienia konta usługi Azure Active Directory są implementowane przy użyciu kontroli dostępu opartej na rolach, przypisując użytkowników do ról; Uprawnienia konta w usłudze Active Directory są implementowane za pomocą kontroli dostępu opartej na rolach, przypisując użytkownikom dla grup zabezpieczeń. Te systemy oparte na rolach można rozszerzyć przez klienta w celu spełnienia potrzeb misji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800 53 kontrolować AC-2, b (7)

#### <a name="account-management--role-based-schemes"></a>Zarządzanie kontami | Schematy oparte na rolach

**Ak-2 (7) b** organizacji monitoruje przypisań ról uprzywilejowanych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje zabezpieczeń i inspekcji rozwiązania tożsamości i dostępu do pulpitu nawigacyjnego. Ten pulpit nawigacyjny umożliwia menedżerów konta do monitorowania informacji systemu kont. To rozwiązanie może być badana zgłoszenia przypisań ról uprzywilejowanych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800 53 kontrolować AC 2 .c (7)

#### <a name="account-management--role-based-schemes"></a>Zarządzanie kontami | Schematy oparte na rolach

**.C AC-2 (7)** przyjmuje organizacji [przypisania: akcje zdefiniowane przez organizację] Jeśli przypisań ról uprzywilejowanych nie są już odpowiednie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za podejmowania działań na kontach kontrolowane przez klienta, gdy przypisań ról uprzywilejowanych nie są już odpowiednie. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-9"></a>Ak kontroli NIST 800 53-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Zarządzanie kontami | Ograniczenia dotyczące korzystania z udostępnionych / grupy kont

**Ak-2 (9)** organizacji tylko zezwala na używanie kont udostępnionych grupy, które spełniają [przypisania: warunków zdefiniowanych przez organizację tworzenia kont udostępnionych grup].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak udostępnionego grupy kont są włączone na zasobów wykorzystywanych przez ten plan. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-10"></a>Ak kontroli NIST 800 53-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Zarządzanie kontami | Zakończenie poświadczeń konta udostępnionego / grupy

**Ak-2 (10)** systemu informacji kończy poświadczenia konta udostępnionego grupy po członków pozostaw grupę.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak udostępnionego grupy kont są włączone na zasobów wykorzystywanych przez ten plan. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-11"></a>Ak kontroli NIST 800 53-2 (11)

#### <a name="account-management--usage-conditions"></a>Zarządzanie kontami | Warunki użytkowania

**AC-2 (11)** wymusza systemu informacji [przypisania: okoliczności zdefiniowany przez organizację i/lub warunków użytkowania] do [przypisania: kont systemowych informacje zdefiniowane przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasady grupy w usłudze Active Directory i będzie skonfigurowany tak, aby zaimplementować ograniczenia czasu dnia lub inne warunki użytkowania konta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800 53 kontrolować tj AC-2 (12).

#### <a name="account-management--account-monitoring--atypical-usage"></a>Zarządzanie kontami | Konto monitorowania / nietypowe użycia

**Ak-2 (12), tj.** organizacji monitoruje kont systemowych informacji dla [przypisania: zdefiniowane przez organizację nietypowe użycia].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania zabezpieczeń i kontrola tożsamości i dostępu do pulpitu nawigacyjnego. Ten pulpit nawigacyjny umożliwia menedżerów konta na potrzeby monitorowania prób dostępu przed wdrożonych zasobów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800 53 kontrolować AC-2, b (12)

#### <a name="account-management--account-monitoring--atypical-usage"></a>Zarządzanie kontami | Konto monitorowania / nietypowe użycia

**B AC-2 (12)** organizacji raporty nietypowe użycie kont systemowych informacji do [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje rozwiązania zabezpieczeń i kontrola tożsamości i dostępu do pulpitu nawigacyjnego. Ten pulpit nawigacyjny umożliwia menedżerów konta na potrzeby monitorowania prób dostępu przed wdrożonych zasobów. To rozwiązanie można skonfigurować do wysyłania alertów, gdy podejrzewa się nietypowe działania lub występują inne zdarzenia, wstępnie zdefiniowanych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800 53 kontroli AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Zarządzanie kontami | Wyłączanie kont dla użytkowników indywidualnych o wysokim ryzyku

**Ak-2 (13)** organizacji wyłącza konta użytkowników stwarza duże ryzyko w [przypisania: organizacji zdefiniowane przez okres] odnajdowania ryzyka.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa klienta i procedury może utworzyć warunki wyłączanie kont użytkowników stwarza duże ryzyko w organizacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-3"></a>Formant NIST 800 53 AC-3

#### <a name="access-enforcement"></a>Wymuszanie dostępu

**Ak 3** systemu informacji wymusza zatwierdzonych zezwolenia na logicznego dostępu do informacji i zasobów systemowych zgodnie z zasad kontroli dostępu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wymusza autoryzacje logicznego dostępu za pomocą kontroli dostępu opartej na rolach, wymuszane przez usługę Azure Active Directory przez przypisywania użytkowników do ról, usługi Active Directory, przypisując użytkownikom dla grup zabezpieczeń i formanty poziomu systemu operacyjnego Windows. Azure Active Directory role przypisane do użytkowników lub grup sterować logicznego dostępu do zasobów w obrębie platformy Azure na poziomie zasobu, grupy lub subskrypcji. Grup zabezpieczeń usługi Active Directory kontrolować logicznego dostępu do zasobów na poziomie systemu operacyjnego i funkcji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-4"></a>Formant NIST 800 53 AC-4

#### <a name="information-flow-enforcement"></a>Wymuszanie przepływ informacji

**AC 4** systemu informacji wymusza zatwierdzonych zezwolenia na sterowanie przepływem informacji w ramach systemu i między systemami połączonych na podstawie [przypisania: zasad sterowania przepływem informacji zdefiniowanych przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wymusza ograniczenia przepływu informacji przy użyciu grup zabezpieczeń sieci zastosować do podsieci, w których są wdrażane zasoby, bramy aplikacji i usługi równoważenia obciążenia. Grup zabezpieczeń sieci upewnij się, że przepływ informacji jest kontrolowany między zasobami na podstawie reguł zatwierdzone. Moduł równoważenia bramy i obciążenia aplikacji dynamicznie kierować ruchem do określonych zasobów na podstawie zatwierdzonych ról. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-4-8"></a>Ak kontroli NIST 800 53-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Wymuszanie przepływ informacji | Filtry zasad zabezpieczeń

**Ak-4 (8)** wymusza systemu informacji za pomocą sterowania przepływem informacji [przypisania: filtry zasad zabezpieczeń zdefiniowany przez organizację] jako podstawę decyzje dotyczące kontroli przepływu dla [przypisania: przepływów informacje zdefiniowane przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wymuszania sterowanie przepływem informacji w zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-4-21"></a>Ak kontroli NIST 800 53-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Wymuszanie przepływ informacji | Rozdzielenie fizycznego / logicznej przepływ informacji

**Ak-4 (21)** systemu informacji oddziela przepływ informacji logicznie lub fizycznie przy użyciu [przypisania: mechanizmów zdefiniowany przez organizację i/lub techniki] celu [przypisania: zdefiniowany organizacji wymagane rozbarwień przez typy informacje o].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za oddzielanie przepływ informacji w zasobów wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-5a"></a>Formant NIST 800 53 AC-5.a

#### <a name="separation-of-duties"></a>Podział obowiązków

**AC 5.a** oddziela organizacji [przypisania: zdefiniowane przez organizację obowiązków osób].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za podział obowiązków między kontami kontrolowane przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-5b"></a>Formant NIST 800 53 AC-5.b

#### <a name="separation-of-duties"></a>Podział obowiązków

**Ak 5.b** organizacji dokumentów podział obowiązków osób.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za dokumentację podział obowiązków między kontami kontrolowane przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-5c"></a>Formant NIST 800 53 AC-5.c

#### <a name="separation-of-duties"></a>Podział obowiązków

**AC 5.c** organizacji definiuje informacje zezwolenia na dostęp do systemu do obsługi rozdzielenia obowiązków.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje kontroli dostępu opartej na rolach, które można skonfigurować w celu rozdzielenia obowiązków zgodnie z wymaganiami organizacji. Uprawnienia konta usługi Azure Active Directory są implementowane przy użyciu kontroli dostępu opartej na rolach, przypisując użytkowników do ról; Uprawnienia konta w usłudze Active Directory są implementowane za pomocą kontroli dostępu opartej na rolach, przypisując użytkownikom dla grup zabezpieczeń. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-6"></a>Formant NIST 800 53 AC-6

#### <a name="least-privilege"></a>Najniższe uprawnienia

**AC 6** organizacji jest stosowana zasada minimalnych uprawnień, dzięki czemu tylko autoryzowani uzyskuje dostęp do użytkowników (lub procesów działających w imieniu użytkowników), które są niezbędne do wykonania zadania zgodnie z organizacyjnej misji i znaczenie biznesowe.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje kontroli dostępu opartej na rolach do Ogranicz użytkowników do tylko te uprawnienia, które jawnie przypisane. Uprawnienia konta usługi Azure Active Directory są implementowane przy użyciu kontroli dostępu opartej na rolach, przypisując użytkowników do ról; Uprawnienia konta w usłudze Active Directory są implementowane za pomocą kontroli dostępu opartej na rolach, przypisując użytkownikom dla grup zabezpieczeń.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800 53 kontroli AC – 6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Najniższe uprawnienia | Autoryzacja dostępu do funkcji zabezpieczeń

**Ak – 6 (1)** organizacji jawnie zezwala na dostęp do [przypisania: funkcje zdefiniowane przez organizację zabezpieczeń (wdrożenie w sprzętu, oprogramowania i oprogramowanie układowe) i informacji związanych z zabezpieczeniami].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć procesu autoryzacji dostępu, który obejmuje dostęp do funkcji zabezpieczeń. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800 53 kontroli AC – 6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Najniższe uprawnienia | Niewymagających uprzywilejowanego dostępu dla funkcji niezwiązana z zabezpieczeniami

**AC – 6 (2)** organizacja wymaga, aby użytkowników kont systemu informacje lub role mają dostęp do [przypisania: funkcje zabezpieczeń zdefiniowany przez organizację lub informacji związanych z zabezpieczeniami], użyj konta nieuprzywilejowanego lub ról, gdy Uzyskiwanie dostępu do funkcji niezwiązana z zabezpieczeniami.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa klienta może wymagać użytkownikom używanie konta nieuprzywilejowanego podczas uzyskiwania dostępu do funkcji niezwiązana z zabezpieczeniami. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800 53 kontroli AC – 6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Najniższe uprawnienia | Dostęp sieciowy do uprzywilejowanych poleceń

**Ak – 6 (3)** organizacji zezwala na dostęp do sieci [przypisania: zdefiniowany przez organizację uprzywilejowanych polecenia] tylko w przypadku [przypisania: organizacji zdefiniowanych ważnych wymagań operacyjnych] i dokumenty uzasadnienie takiego dostępu w Planowanie zabezpieczeń systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą określić uprzywilejowanych poleceń, które mogą być udostępniane za pośrednictwem sieci. Uwaga: Klienci nie mają fizycznego dostępu do infrastruktury platformy Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800 53 kontroli AC – 6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Najniższe uprawnienia | Uprzywilejowane konta

**Ak – 6 (5)** organizacji ogranicza uprzywilejowanych kont w systemie informacji [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą określić ograniczenia wykorzystania kont uprzywilejowanych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800 53 kontrolować AC 6 .a (7)

#### <a name="least-privilege--review-of-user-privileges"></a>Najniższe uprawnienia | Sprawdź uprawnienia użytkownika

**Ak – 6 (7), tj.** przeglądy organizacji [przypisania: częstotliwość zdefiniowany przez organizację] uprawnienia przypisane do [przypisania: zdefiniowany przez organizację ról lub grup użytkowników] do sprawdzania poprawności potrzebę takich uprawnień.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za przeglądania uprawnień użytkowników kont kontrolowane przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800 53 kontrolować AC 6, b (7)

#### <a name="least-privilege--review-of-user-privileges"></a>Najniższe uprawnienia | Sprawdź uprawnienia użytkownika

**Ak – 6 (7) b** organizacji następuje zmiana przypisania lub usuwa uprawnienia, jeśli to konieczne poprawnie odzwierciedlić potrzeb organizacji misji/biznesowych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za ponowne przypisywanie lub usuwanie uprawnień dla kont kontrolowane przez klienta, gdy jest to konieczne. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800 53 kontroli AC – 6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Najniższe uprawnienia | Poziomy uprawnień do wykonania kodu

**Ak – 6 (8)** uniemożliwia systemu informacji [przypisania: oprogramowania zdefiniowane przez organizację] wykonywanie na wyższym poziomie uprawnień niż użytkowników wykonywania oprogramowania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje kontroli dostępu opartej na rolach do Ogranicz użytkowników do tylko te uprawnienia, które jawnie przypisane. Zabezpieczenia systemu operacyjnego na poziomie maszyny wirtualnej nie zezwalają na oprogramowanie ma zostać wykonane na wyższym poziomie uprawnień niż użytkowników wykonywania oprogramowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800 53 kontroli AC – 6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Najniższe uprawnienia | Inspekcja użycia funkcji uprzywilejowanych

**Ak – 6 (9)** systemu informacji inspekcji wykonywania uprzywilejowanych funkcji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje usługę Analiza dzienników. Wdrożone maszyny wirtualne i usługi Azure kont magazynu diagnostyki są połączonych źródeł do analizy dzienników zapewnienie, że wykonanie funkcji uprzywilejowanych podlega inspekcji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800 53 kontroli AC – 6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Najniższe uprawnienia | Zabrania wykonywania uprzywilejowanych funkcji nieuprzywilejowanych użytkowników

**Ak – 6 (10)** systemu informacji uniemożliwia użytkownikom bez uprawnień wykonywania uprzywilejowanych funkcji to wyłączenie obejścia, lub zmienianie zaimplementowana zabezpieczenia zabezpieczeń metodach przeciwdziałania im.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan implementuje kontroli dostępu opartej na rolach do Ogranicz użytkowników do tylko te uprawnienia, które jawnie przypisane.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-7a"></a>Formant NIST 800 53 AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>Nieudanych logowań

**AC 7.a** systemu informacji wymusza limit [przypisania: liczba zdefiniowanych przez organizację] kolejnych nieprawidłowe próby logowania przez użytkownika podczas [przypisania: organizacji zdefiniowane przez okres].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Limity portalu Azure kolejnych nieprawidłowe próby logowania przez użytkowników. Zasady grupy są stosowane na poziomie systemu operacyjnego dla wszystkich maszyn wirtualnych wdrożonych przez tego planu. Zasady ogranicza kolejnych nieudanych prób logowania przez użytkowników do nie więcej niż trzech w ciągu 15 minut. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-7b"></a>Od AC kontroli NIST 800 53-7.b

#### <a name="unsuccessful-logon-attempts"></a>Nieudanych logowań

**Od 7.b AC** systemu informacji automatycznie [zaznaczenia: blokuje konta/węzeł [przypisania: okres zdefiniowany organizacji]; blokady konta/węzła do czasu zwolnienia przez administratora; opóźnienia dalej monit logowania zgodnie z [ Przypisania: algorytm zdefiniowany przez organizację opóźnienia]] Jeśli Przekroczono maksymalną liczbę nieudanych prób.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Azure portal blokady konta po kolejnych nieprawidłowe próby logowania przez użytkowników. Zasady grupy są stosowane na poziomie systemu operacyjnego dla wszystkich maszyn wirtualnych wdrożonych przez tego planu. Zasady blokowania kont do trzech godzin po trzech kolejnych nieudanych prób logowania przez użytkowników. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-7-2"></a>Ak kontroli NIST 800 53-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Nieudanych logowań | Przeczyść / czyszczenia urządzenia przenośnego

**Ak-7 (2)** systemu informacji powoduje usunięcie/czyszczenia informacji z [przypisania: zdefiniowane przez organizację urządzeń przenośnych] na podstawie [przypisania: organizacji zdefiniowane wymagania/techniki przeczyszczanie/czyszczenie] po [przypisania: próby logowania kolejnych nieudanych urządzeń zdefiniowany przez organizację numer].

**Obowiązki:** `Not Applicable`

|||
|---|---|
| **Klienta** | Urządzenia przenośne nie są w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na urządzenia przenośne w obrębie granicy platformy Azure. Tak ten formant nie ma zastosowania do systemu Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>Formant NIST 800 53 AC-8.a

#### <a name="system-use-notification"></a>Powiadomienie systemowe użycia

**AC 8.a** Wyświetla systemu informacje dla użytkowników [przypisania: system zdefiniowane w organizacji używać powiadomienie lub transparentu] przed udzieleniem im dostępu do sieci, który zapewnia spójne z odpowiednich federalne powiadomienia prywatności i bezpieczeństwa ustawowych, zamówienia wykonawczego, dyrektywy, zasady, wykonawczych, standardy i wskazówki i stanów, że użytkownicy uzyskują dostęp do stany USA Rządowych systemu informacji; użycie systemu informacji może być monitorowany, zarejestrowane i może ulec inspekcji; nieautoryzowanym użyciem systemu informacji jest zabronione i może ulec karne oraz cywilnego kary; i korzystanie z informacji oznacza zgodę na monitorowanie i rejestrowanie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasady grupy implementuje powiadomienie Użyj systemu, który będzie wyświetlany użytkownikom przed logowania. Uwaga: Ten plan implementuje Użyj powiadomienie systemowe przykład. Klienta należy edytować ten tekst ma spełnia organizacji i/lub wymagania dotyczące przepisów treści. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-8b"></a>Formant NIST 800 53 AC-8.b

#### <a name="system-use-notification"></a>Powiadomienie systemowe użycia

**AC 8.b** informacji system ma zachowywać powiadomienie lub banera na ekranie, dopóki użytkownicy potwierdzić warunki użytkowania i podjęcie odpowiednich działań jawne Zaloguj się do lub dalsze dostęp do systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasady grupy implementuje powiadomienie Użyj systemu, który będzie wyświetlany użytkownikom przed logowania. Użytkownik musi potwierdzić powiadomień, aby zalogować się. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-8c"></a>Formant NIST 800 53 AC-8.c

#### <a name="system-use-notification"></a>Powiadomienie systemowe użycia

**AC 8.c** systemu informacji dla systemów publicznie Wyświetla informacje o systemie Użyj [przypisania: warunki zdefiniowane w organizacji], zanim zostanie przyznany dostęp dalsze; Wyświetla odwołania do monitorowania, rejestrowania, lub inspekcji, które są zgodne z dostosowania prywatności dla tych systemów, które zazwyczaj Zabroń używania tych działań; i zawiera opis autoryzowanych używa systemu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za wyświetlanie powiadomienie systemowe Użyj wszystkich zasobów publicznie wdrożonych przez klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-10"></a>Formant NIST 800 53 AC-10

#### <a name="concurrent-session-control"></a>Formant równoczesnych sesji

**AC 10** systemu informacji ogranicza liczbę jednoczesnych sesji dla każdego [przypisania: konto zdefiniowane przez organizację i/lub typ konta] do [przypisania: liczba zdefiniowanych przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady systemu operacyjnego został zaimplementowany dla maszyn wirtualnych wdrożonych przez tego planu. Zasady implementuje ograniczenia równoczesnych sesji (dwa sesji). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-11a"></a>Formant NIST 800 53 AC-11.a

#### <a name="session-lock"></a>Blokady sesji

**AC 11.a** systemu informacji uniemożliwia dalsze dostęp do systemu inicjując blokady sesji po [przypisania: okres zdefiniowany organizacji] czas braku aktywności lub po odebraniu żądania od użytkownika.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasady grupy implementuje blokady nieaktywności sesji protokołu RDP. Użytkownicy mogą ręcznie inicjować blokady. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-11b"></a>Formant NIST 800 53 AC-11.b

#### <a name="session-lock"></a>Blokady sesji

**AC 11.b** system informacji ma zachowywać blokady sesji, dopóki użytkownik ustanawia ponownie dostęp przy użyciu określone procedury identyfikacji i uwierzytelniania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasady grupy implementuje blokady nieaktywności sesji protokołu RDP. Użytkownicy muszą ponownie uwierzytelniać do odblokowania sesji.  |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-11-1"></a>Ak kontroli NIST 800 53-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Blokady sesji | Wyświetla ukrywanie wzorca

**Ak-11 (1)** systemu informacji za pośrednictwem blokady sesji zawiera informacje o poprzednio widoczne na ekranie z obrazem publicznie dostępnej.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania kontrolera domeny, do której są przyłączone do wszystkich wdrożonych maszyn wirtualnych. Zasady grupy implementuje blokady nieaktywności sesji protokołu RDP. Blokady sesji zawiera informacje widoczne wcześniej. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-12"></a>Formant NIST 800 53 AC-12

#### <a name="session-termination"></a>Zakończenie sesji

**AC 12** systemu informacji automatycznie kończy sesję użytkownika po [przypisania: zdefiniowane przez organizację warunki lub zdarzenia wyzwalacza wymagające rozłączenia sesji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Konfiguracja hosta sesji usług pulpitu zdalnego dla maszyn wirtualnych systemu Windows wdrożone przez ten plan można skonfigurować do zakończenia sesji wymagań organizacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-12-1a"></a>((1) tj. NIST 800 53 kontroli AC 12

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Zakończenie sesji | Użytkownik zainicjował Logouts / wyświetla wiadomości

**Ak-12 (1), tj.** systemu informacji udostępnia funkcję wylogowania komunikacji zainicjowane przez użytkownika sesji zawsze, gdy jest używane uwierzytelnianie w celu uzyskania dostępu do [przypisania: zasoby zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Azure portal maszyny wirtualnej systemów operacyjnych i wdrożone przez ten plan Włącz używane do inicjowania wylogowania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800 53 kontroli AC 12 (1) b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Zakończenie sesji | Użytkownik zainicjował Logouts / wyświetla wiadomości

**Ak-12 (1) b** systemu informacji zostanie wyświetlony komunikat jawne wylogowania użytkownikom wskazujący przerwanie niezawodnej sesji, uwierzytelnionym komunikacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Azure portal maszyny wirtualnej systemów operacyjnych i wdrożone przez ten plan Włącz używane do inicjowania wylogowania. Proces wylogowywania zawiera wskazanie użytkownikom zakończenia sesji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-14a"></a>Formant NIST 800 53 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Dozwolone akcje bez identyfikacji i uwierzytelniania

**AC 14.a** identyfikuje organizacji [przypisania: akcje zdefiniowane przez organizację użytkownika] który można wykonać bez identyfikacji i uwierzytelniania zgodne z organizacyjnej misji/firm funkcje w systemie informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiada za ustalenie akcje, które mogą być wykonywane na zasoby wdrażane klienta bez uwierzytelniania (np. na przykład wyświetlanie publicznie strony sieci web) lub identyfikator. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-14b"></a>Formant NIST 800 53 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Dozwolone akcje bez identyfikacji i uwierzytelniania

**AC 14.b** organizacji dokumentów i zapewnia obsługi uzasadnienie w planie ochrony informacji w systemie nie wymaga uwierzytelniania lub identyfikator akcji użytkownika.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zapewnienie dokumentacji nie wymaga uwierzytelniania na zasobach wdrożonych przez klienta lub identyfikator akcji użytkownika. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-17a"></a>Formant NIST 800 53 AC-17.a

#### <a name="remote-access"></a>Dostęp zdalny

**AC 17.a** organizacji ustanawia i dokumenty ograniczenia użycia, wymagań dotyczących konfiguracji/połączenia i wskazówki dotyczące implementacji dla każdego typu dostępu zdalnego są dozwolone.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą określić ograniczenia użycia dostępu zdalnego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-17b"></a>Formant NIST 800 53 AC-17.b

#### <a name="remote-access"></a>Dostęp zdalny

**AC 17.b** organizacji zezwala na zdalny dostęp do systemu informacji przed przyznaniem takich połączeń.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć procesu autoryzacji dostępu zdalnego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-17-1"></a>Ak kontroli NIST 800 53-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Dostęp zdalny | Automatyczne monitorowania / sterowania

**Ak-17 (1)** systemu informacji monitoruje i kontroluje metody dostępu zdalnego.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan zapewnia dostęp zdalny do systemu informacji za pośrednictwem portalu Azure za pomocą połączeń usług pulpitu zdalnego za pośrednictwem jumpbox i za pośrednictwem aplikacji sieci web zaimplementowana przez klienta. Dostęp za pośrednictwem portalu Azure i sesji pulpitu zdalnego są poddawane inspekcji i monitorować za pomocą analizy dzienników. Klient musi implementować kontroli dostępu zdalnego, w razie potrzeby do aplikacji sieci web. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-17-2"></a>Ak kontroli NIST 800 53-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Dostęp zdalny | Ochrona poufności / integralności przy użyciu szyfrowania

**Ak-17 (2)** system informacji implementuje mechanizmów kryptograficznych ochrony poufności i integralności sesji dostępu zdalnego.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Dostęp zdalny do zasobów wykorzystywanych przez ten schemat, w tym portalu Azure, połączeń usług pulpitu zdalnego i brama aplikacji w sieci web, są chronione przy użyciu protokołu TLS. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-17-3"></a>Ak kontroli NIST 800 53-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Dostęp zdalny | Zarządzane punkty kontroli dostępu

**Ak-17 (3)** systemu informacji kieruje wszystkie dostępu zdalnego za pośrednictwem [przypisania: liczba zdefiniowanych przez organizację] zarządzane punkty kontroli dostępu do sieci.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zdalny dostęp do aplikacji sieci web zakładaną wdrożone przez ten plan jest za pośrednictwem bramy aplikacji. Dostęp zdalny do innych zasobów jest za pośrednictwem jumpbox. Nie ma żadnych innych publicznie punktów końcowych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800 53 kontrolować AC 17 .a (4)

#### <a name="remote-access--privileged-commands--access"></a>Dostęp zdalny | Uprzywilejowane polecenia / dostęp

**Ak-17 (4), tj.** organizacji zezwala na wykonanie polecenia uprzywilejowanych i dostęp do informacji związanych z zabezpieczeniami za pośrednictwem zdalnego dostępu tylko dla [przypisania: organizacji zdefiniowanych wymaga].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą określić uprzywilejowanych polecenia, które mogą być do niej zdalny dostęp i obejmują uzasadnienie. Uwaga: Klienci nie mają sieci bezpośredniego dostępu do infrastruktury platformy Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800 53 kontrolować AC 17 b (4)

#### <a name="remote-access--privileged-commands--access"></a>Dostęp zdalny | Uprzywilejowane polecenia / dostęp

**B AC-17 (4)** organizacji dokumentów uzasadnienie takiego dostępu w sekcji Planowanie zabezpieczeń systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą określić uprzywilejowanych polecenia, które mogą być do niej zdalny dostęp i obejmują uzasadnienie. Uwaga: Klienci nie mają sieci bezpośredniego dostępu do infrastruktury platformy Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-17-9"></a>Ak kontroli NIST 800 53-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Dostęp zdalny | Odłącz / wyłączyć dostęp

**Ak-17 (9)** organizacji zapewnia możliwość szybkiego odłączyć i wyłączyć dostępu zdalnego do systemu informacji w [przypisania: okres zdefiniowany organizacji].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan zapewnia dostęp zdalny do systemu informacji za pośrednictwem portalu Azure, za pomocą połączeń usług pulpitu zdalnego za pośrednictwem jumpbox i aplikacji sieci web. Jeśli konta usługi Azure Active Directory jest wyłączony lub usunięty, dostępu do portalu Azure jest odłączony od razu. Podobnie jeśli konto na poziomie systemu operacyjnego maszyny wirtualnej jest wyłączone lub usunięte, pulpitu dostępu zdalnego za pośrednictwem jumpbox jest odłączony od razu. Klienci muszą implementować dostępu zdalnego rozłączyć dla aplikacji sieci web. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-18a"></a>Formant NIST 800 53 AC-18.a

#### <a name="wireless-access"></a>Dostęp bezprzewodowy

**AC 18.a** organizacji ustanawia ograniczenia użycia, wymagań dotyczących konfiguracji/połączenia i wykonania wskazówki dotyczące dostępu bezprzewodowego.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych dostępu bezprzewodowego w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure ustanawia ograniczenia użycia, wymagań dotyczących konfiguracji/połączenia i wykonania wskazówki dotyczące dostępu bezprzewodowego za pośrednictwem sieci Security Standard, która jawnie zabrania stosowania sieci bezprzewodowej w środowisku Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-18b"></a>Formant NIST 800 53 AC-18.b

#### <a name="wireless-access"></a>Dostęp bezprzewodowy

**AC 18.b** organizacji zezwala na dostęp bezprzewodowy do systemu informacji przed przyznaniem takich połączeń.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych dostępu bezprzewodowego w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na dostęp bezprzewodowy w centrach danych Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-1"></a>Ak kontroli NIST 800 53-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Bezprzewodowego dostępu | Uwierzytelnianie i szyfrowanie

**Ak-18 (1)** system informacji chroni dostępu bezprzewodowego do sieci przy użyciu uwierzytelniania [wybór (jeden lub więcej): użytkownicy; urządzenia] i szyfrowania.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych dostępu bezprzewodowego w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na dostęp bezprzewodowy w środowisku Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-3"></a>Ak kontroli NIST 800 53-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Bezprzewodowego dostępu | Wyłącz sieci bezprzewodowej

**Ak-18 (3)** wyłącza organizacji, gdy nie jest przeznaczony do użycia, bezprzewodowej sieci możliwości wewnętrznie osadzone w składnikach systemu informacji przed wystawiania i wdrażania.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych dostępu bezprzewodowego w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na dostęp bezprzewodowy w środowisku Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-4"></a>Ak kontroli NIST 800 53-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Bezprzewodowego dostępu | Ogranicz konfiguracje przez użytkowników

**Ak-18 (4)** organizacji identyfikuje i jawnie zezwala na użytkowników, którzy mogą niezależnej konfiguracji funkcji sieci bezprzewodowej.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych dostępu bezprzewodowego w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na dostęp bezprzewodowy w środowisku Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-5"></a>Ak kontroli NIST 800 53-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Bezprzewodowego dostępu | Anten / poziomy transmisji zasilania

**Ak-18 (5)** organizacji wybiera anten radiowych i kalibruje transmisji poziomu Aby zmniejszyć prawdopodobieństwo, że można używać sygnały mogą być odbierane poza granice kontrolowane przez organizację.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych dostępu bezprzewodowego w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na dostęp bezprzewodowy w środowisku Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19a"></a>Formant NIST 800 53 AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Kontrola dostępu dla urządzeń przenośnych

**AC 19.a** organizacji ustanawia ograniczenia użycia, wymagania dotyczące konfiguracji, wymagania dotyczące połączenia i wskazówki dotyczące implementacji dla urządzeń przenośnych kontrolowane przez organizację.

**Obowiązki:** `Not Applicable`

|||
|---|---|
| **Klienta** | Brak urządzeń przenośnych kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na urządzenia przenośne w obrębie granicy platformy Azure. Tak ten formant nie ma zastosowania do systemu Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>Formant NIST 800 53 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Kontrola dostępu dla urządzeń przenośnych

**AC 19.b** organizacji zezwala na połączenia z urządzeniami przenośnymi z systemami informacji organizacyjnych.

**Obowiązki:** `Not Applicable`

|||
|---|---|
| **Klienta** | Brak urządzeń przenośnych kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na urządzenia przenośne w obrębie granicy platformy Azure. Tak ten formant nie ma zastosowania do systemu Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>Ak kontroli NIST 800 53-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Kontrola dostępu dla urządzeń przenośnych | Pełne urządzenia / na podstawie kontenera szyfrowania

**AC-19 [5]** organizacja stosuje [zaznaczenia: szyfrowanie urządzenia pełnej; szyfrowania kontenera] do ochrony poufności i integralności informacji na [przypisania: zdefiniowany przez organizację urządzeń przenośnych].

**Obowiązki:** `Not Applicable`

|||
|---|---|
| **Klienta** | Brak urządzeń przenośnych kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nie zezwala na urządzenia przenośne w obrębie granicy platformy Azure. Tak ten formant nie ma zastosowania do systemu Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>Formant NIST 800 53 AC-20.a

#### <a name="use-of-external-information-systems"></a>Korzystanie z systemów zewnętrznych informacji

**AC 20.a** organizacji ustanawia warunków i postanowień, zgodne z bez relacji zaufania nawiązać z innych organizacji będącej właścicielem, działają, i/lub utrzymanie systemów informacyjnych zewnętrznego, dzięki czemu autoryzowane osoby dostęp do systemu informacji z systemów zewnętrznych informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą obejmować postanowienia dotyczące wykorzystania oferty usług w chmurze w obszarze FedRAMP. Azure udzielono tymczasowego autoryzacji do działania (P-ATO) przez FedRAMP wspólnego autoryzacji tablicy (JAB) umożliwiające przejęcie korzystanie z usług w chmurze Azure agencji rządowych Stanów Zjednoczonych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-20b"></a>Formant NIST 800 53 AC-20.b

#### <a name="use-of-external-information-systems"></a>Korzystanie z systemów zewnętrznych informacji

**AC 20.b** organizacji ustanawia warunków i postanowień, zgodne z bez relacji zaufania nawiązać z innych organizacji będącej właścicielem, działają, i/lub utrzymanie systemów informacyjnych zewnętrznego, dzięki czemu autoryzowane osoby do przetwarzania, przechowywania lub przesyłać informacje kontrolowane organizacji korzystających z systemów zewnętrznych informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą obejmować postanowienia dotyczące wykorzystania oferty usług w chmurze w obszarze FedRAMP. Azure udzielono tymczasowego autoryzacji do działania (P-ATO) przez FedRAMP wspólnego autoryzacji tablicy (JAB) umożliwiające przejęcie korzystanie z usług w chmurze Azure agencji rządowych Stanów Zjednoczonych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-20-1"></a>Ak kontroli NIST 800 53-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Korzystanie z systemów zewnętrznych informacji | Limity autoryzowane użycie

**Ak-20 (1)** uprawnionych osób na potrzeby systemu zewnętrznego informacji do uzyskania dostępu do systemu informacje lub przetwarzania, przechowywania lub przesyłać informacje kontrolowane organizacji, tylko wtedy, gdy organizacja sprawdza pozwala organizacji Implementacja kontroli zabezpieczeń wymagane w systemie zewnętrznym, jak określono w zasadami zabezpieczeń informacji w organizacji oraz plan ochrony; lub zachowuje umowy połączenie lub przetwarzania systemu zatwierdzonych informacji z jednostką organizacyjną hosting systemu zewnętrznego informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Grupy technologii informacji na poziomie przedsiębiorstwa odbiorcy mogą oceny zgodności dostawcy usług chmury z wymaganiami zabezpieczeń informacji w organizacji i uznania całego przedsiębiorstwa do użycia oferty usług skojarzonej chmury. Azure udzielono tymczasowego autoryzacji do działania (P-ATO) przez FedRAMP wspólnego autoryzacji tablicy (JAB). Azure jest oceniane przez organizację oceny zatwierdzone FedRAMP innych firm (3PAO) do oceny zgodności z FedRAMP kontrolę zabezpieczeń i inne wymagania. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-ac-20-2"></a>Ak kontroli NIST 800 53-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Korzystanie z systemów zewnętrznych informacji | Urządzenia przenośnego urządzenia pamięci masowej

**Ak-20 (2)** organizacji [zaznaczenia: ogranicza; zabrania] korzystanie z urządzenia przenośnego urządzenia pamięci masowej kontrolowane przez organizację według uprawnionych osób w systemach zewnętrznych informacji.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft nie zezwala na urządzenia przenośnego urządzenia pamięci masowej kontrolowane przez klienta w środowisku Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-21a"></a>Formant NIST 800 53 AC-21.a

#### <a name="information-sharing"></a>Udostępnianie informacji

**AC 21.a** organizacji ułatwia udostępnianie przez włączenie autoryzowanych użytkowników w celu ustalenia, czy przypisane do udostępniania partnera autoryzacji dostępu są takie same ograniczenia dostępu do informacji dla informacji [przypisania: zdefiniowany przez organizację udostępniania informacji okoliczności, w którym wymagany jest uznania użytkownika].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady kontroli dostępu na poziomie przedsiębiorstwa odbiorcy mogą obejmować przepisy dotyczące udostępniania informacji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-21b"></a>Formant NIST 800 53 AC-21.b

#### <a name="information-sharing"></a>Udostępnianie informacji

**AC 21.b** organizacja stosuje [przypisania: zdefiniowane przez organizację automatycznego mechanizmów lub procesów ręcznych] do świadczenia użytkownikom pomocy w informacji udostępnianie współpracy podejmowaniu decyzji co do.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient może polegać na udostępniania możliwości obsługi decyzji informacji korporacyjnych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-22a"></a>Formant NIST 800 53 AC-22.a

#### <a name="publicly-accessible-content"></a>Dostępny publicznie element zawartości

**AC 22.a** organizacji oznacza osoby upoważnione do publikowania informacji o systemie publicznie dostępnych informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może wyznaczyć osoby upoważnione do publikowania informacji publicznie. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-22b"></a>Formant NIST 800 53 AC-22.b

#### <a name="publicly-accessible-content"></a>Dostępny publicznie element zawartości

**AC 22.b** organizacji przygotowuje uprawnionych osób, aby upewnić się, publicznie dostępnych informacji nie zawiera niepubliczne informacje.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy mogą polegać na poziomie przedsiębiorstwa szkoleń dla użytkowników indywidualnych uprawnień do publikowania informacji publicznie. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-22c"></a>Formant NIST 800 53 AC-22.c

#### <a name="publicly-accessible-content"></a>Dostępny publicznie element zawartości

**AC 22.c** organizacji przegląda proponowanych zawartość informacji przed publikowanie do systemu publicznie dostępnych informacji, aby upewnić się, że niepubliczne informacje nie są uwzględniane.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć procesu przeglądu zawartości proponowane zaksięgowania publicznie systemu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-ac-22d"></a>Formant NIST 800 53 AC-22.d

#### <a name="publicly-accessible-content"></a>Dostępny publicznie element zawartości

**AC 22.d** organizacji przegląda zawartość w systemie publicznie dostępnych informacji dla niepublicznych informacji [przypisania: zdefiniowany przez organizację częstotliwość] i usuwa takie informacje, jeśli odnaleziony.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Procedury kontroli dostępu na poziomie przedsiębiorstwa klienta może utworzyć procesu przeglądu okresowe zawartość przesłaną do systemów publicznie. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


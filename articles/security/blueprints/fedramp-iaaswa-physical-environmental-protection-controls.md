---
title: "Plan ochrony fizyczne i środowiskowe - automatyzacji aplikacji sieci Web FedRAMP - Azure zabezpieczeń i zgodności"
description: "Automatyzacji aplikacji sieci Web FedRAMP - ochrony fizyczne i środowiskowe"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 792b9da0f4e5ec73c39f56a6e4805cf3c37133c4
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2018
---
# <a name="physical-and-environmental-protection-pe"></a>Fizyczne i środowiskowe ochrony (PE)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-pe-1"></a>Formant PE NIST 800 53-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Zasady ochrony fizyczne i środowiskowe i procedur

**PE 1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] zasady ochrony fizyczne i środowiskowe, które dotyczy cel, zakres, ról, obowiązki, zarządzania zobowiązań, koordynacji między jednostki organizacyjnej i zgodności; i procedury w celu ułatwienia implementacji zasad ochrony fizyczne i środowiskowe i kontrolek skojarzone fizyczne i środowiskowe ochrony; przegląda i aktualizuje bieżące zasady ochrony fizyczne i środowiskowe [przypisania: zdefiniowane przez organizację częstotliwość]; i procedur ochrony fizyczne i środowiskowe [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Zasady ochrony na poziomie przedsiębiorstwa fizyczne i środowiskowe klienta i procedur mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-pe-2a"></a>Formant PE NIST 800 53-2.a

#### <a name="physical-access-authorizations"></a>Zezwolenia na dostęp fizyczny

**PE 2.a** organizacji rozwija zatwierdza i przechowuje listę osób autoryzowany dostęp do funkcji, w której znajduje się system informacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Fizyczny dostęp do centrum danych firmy Microsoft musi być zatwierdzony przez zespół zarządzania Centrum danych (DCM) przy użyciu narzędzia dostępu do centrum danych. Przydziały dostępu wymagają datę zakończenia, po upływie którego dostęp zostanie automatycznie usunięta i musi zostać ponownie uznany. Ponadto, gdy dostęp nie jest już wymagane, funkcjonariusze centrum danych lub zarządzania ręcznego zażądania przerwanie dostępu. |


 ## <a name="nist-800-53-control-pe-2b"></a>Formant PE NIST 800 53-2.b

#### <a name="physical-access-authorizations"></a>Zezwolenia na dostęp fizyczny

**PE 2.b** poświadczenia autoryzacji dostępu zaimplementowanych wystawia organizacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Narzędzia dostępu do centrum danych jest wiarygodne źródło wyświetlanie listy wszystkich pracowników z autoryzowanego dostępu do określonego centrum danych. Narzędzie jest połączony z urządzenia kontroli dostępu zabezpieczeń fizycznych centrum danych i autoryzuje dostępu oparte na poziomy dostępu, które zostały zatwierdzone przez zespół DCM. Poziomy dostępu są przypisane w narzędziu do firmy Microsoft albo użytkownika wystawiony plakietkę lub wskaźnika na tymczasowy dostęp przypisane w centrum danych przez przełożonego pomieszczeniu sterowania (CRS). Poziomy dostępu zostały zatwierdzone przez zespół DCM. Oprócz poświadczeń przypisany do fizycznej identyfikatory niektóre obszary Centrum danych wymaga rejestracji danych biometrycznych użytkownika (Geometria dłoni lub linii papilarnych). |


 ## <a name="nist-800-53-control-pe-2c"></a>Formant PE NIST 800 53-2.c

#### <a name="physical-access-authorizations"></a>Zezwolenia na dostęp fizyczny

**PE 2.c** organizacji sprawdza listy dostępu określających dostępu zaimplementowanych autoryzowanych osób [przypisania: zdefiniowany przez organizację częstotliwość].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Oprócz odwołania praw dostępu opisano w części w Microsoft Azure przeglądy autoryzacji dostępu listami centrach danych platformy Azure co 90 dni celu indywidualnego dostępu w razie potrzeby usuń/aktualizacji. |


 ## <a name="nist-800-53-control-pe-2d"></a>Formant PE NIST 800 53-2.d

#### <a name="physical-access-authorizations"></a>Zezwolenia na dostęp fizyczny

**PE 2.d** organizacji usuwa osób z listy dostępu zaimplementowanych po dostępu nie jest już wymagane.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure automatycznie usuwa dostępu, po osiągnięciu Data zakończenia przypisania dostępu. Gdy dostęp nie jest już wymagane, funkcjonariusze centrum danych lub zarządzania ręcznie zażąda przerwanie dostępu w narzędziu dostępu do centrum danych. Ponadto Microsoft Azure usunie wszystkie zezwolenia na dostęp do niepotrzebnych odnalezione wyniku Przegląd listy dostępu opisano w części c. |


 ## <a name="nist-800-53-control-pe-3a"></a>Formant PE NIST 800 53-3.a

#### <a name="physical-access-control"></a>Kontrola dostępu fizycznego

**PE 3.a** organizacja wymusza zezwolenia na dostęp do fizycznych w [przypisania: zdefiniowane przez organizację wejścia/wyjścia wskazuje funkcji którym znajduje się system informacji] weryfikując zezwolenia na dostęp do poszczególnych przed udzieleniem im dostęp do obiektu; i kontrolowanie wejście/wyjście do zaimplementowanych przy użyciu [wybór (jeden lub więcej): [przypisania: urządzenia systemów sterujące zdefiniowane w organizacji dostęp fizyczny]; osłony].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure wymusza zezwolenia na dostęp do fizycznego dla wszystkich punktów fizyczny dostęp do centrach danych platformy Azure przy użyciu personel 24 x 7, alarmy, monitorowania wideo, uwierzytelnianie wieloskładnikowe i pułapki man urządzenia portalu. |


 ## <a name="nist-800-53-control-pe-3b"></a>Formant PE NIST 800 53-3.b

#### <a name="physical-access-control"></a>Kontrola dostępu fizycznego

**PE 3.b** Organizacja zachowuje dzienniki inspekcji fizyczny dostęp do [przypisania: punkty wejścia/wyjścia zdefiniowany przez organizację].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Pełny dostęp do infrastruktury centrum danych Azure są rejestrowane i inspekcji. |


 ## <a name="nist-800-53-control-pe-3c"></a>Formant PE NIST 800 53-3.c

#### <a name="physical-access-control"></a>Kontrola dostępu fizycznego

**PE 3.c** zapewnia organizacji [przypisania: zabezpieczeń zdefiniowany przez organizację] Aby kontrolować dostęp do obszarów w obrębie obiektu oficjalnie wyznaczony jako dostępny publicznie.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Centrach danych platformy Azure nie zawierają obszarów, które są oznaczone jako dostępny publicznie. |


 ## <a name="nist-800-53-control-pe-3d"></a>Formant PE NIST 800 53-3.d

#### <a name="physical-access-control"></a>Kontrola dostępu fizycznego

**PE 3.d** eskorty odwiedzających organizacji i monitoruje aktywność odwiedzający [przypisania: zdefiniowane przez organizację okoliczności wymagające eskorty dla gości i monitorowania].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Wszystkich odwiedzających, które zostały zatwierdzone dostęp do centrum danych (zobacz PE-2) są wyznaczone jako 'Tylko konwojowanie' na ich identyfikatory lub za pośrednictwem innych wizualnie (np. identyfikatory kolorowe) i są wymagane do ich eskorty przez cały czas. Bez odwiedzających nie ma żadnych poziomów dostępu przyznanych im i mogą być przesyłane wyłącznie na dostęp do ich eskort. Eskorty monitorować wszystkie działania ich odwiedzającego znajduje się w centrum danych. |


 ## <a name="nist-800-53-control-pe-3e"></a>Formant PE NIST 800 53-3.e

#### <a name="physical-access-control"></a>Kontrola dostępu fizycznego

**PE-3.e** Organizacja zabezpiecza klucze, kombinacje i inne urządzenia fizycznego dostępu.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Klucze fizycznych i identyfikatory tymczasowego dostępu są chronione w ramach operacji Centrum zabezpieczeń (SOC). Funkcjonariusze są obsługą 24 x 7. Klucze są wyewidencjonowane do określonego personelu przez dopasowanie osoby wskaźnika dostępu do fizycznego klucza. Spisów klucza są wykonywane podczas każdej zmiany, a klucze nie mogą być umieszczone poza siedzibą. |


 ## <a name="nist-800-53-control-pe-3f"></a>Formant PE NIST 800 53-3.f

#### <a name="physical-access-control"></a>Kontrola dostępu fizycznego

**PE 3.f** spisów organizacji [przypisania: zdefiniowane przez organizację fizyczny dostęp urządzeń] co [przypisania: częstotliwość zdefiniowany przez organizację].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Fizyczny dostęp urządzeń znajdujących się w centrach danych platformy Azure są spisanych na co najmniej corocznego. Klucze i identyfikatory tymczasowego dostępu do uwzględnienia w spisie wiele razy dziennie podczas każdej zmiany. Czytniki dostępu i podobne urządzenia dostępu są połączone z systemu zabezpieczeń fizycznych, których stan jest stale reprezentowane. |


 ## <a name="nist-800-53-control-pe-3g"></a>Formant PE NIST 800 53-3.g

#### <a name="physical-access-control"></a>Kontrola dostępu fizycznego

**PE 3.g** organizacji zmienia kombinacji i klucze [przypisania: zdefiniowane przez organizację częstotliwość] i/lub gdy klucze zostaną utracone, kombinacje są uszkodzone lub osób są przekazywane lub zakończenia.  

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Procedury wdrażania w przypadkach, gdy wskaźnika dostępu ma centrów danych Microsoft Azure lub klucz zostało utracone lub zakończony lub przekazywane przez osobę. W przypadku rozwiązania lub przeniesienia dostęp osoby zostanie natychmiast usunięty z systemu i ich wskaźnika dostępu usunięte. |


 ### <a name="nist-800-53-control-pe-3-1"></a>NIST 800 53 PE kontroli-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Kontrola dostępu fizycznego | Dostęp do informacji o systemie

**Pe3 (1)** organizacja wymusza autoryzacje fizyczny dostęp do systemu informacji oprócz fizyczne metody kontroli dostępu dla funkcji w [przypisania: zdefiniowane przez organizację spacje fizycznego zawierający jeden lub więcej składników system informacje].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure dodatkowo ogranicza obszary w centrach danych firmy Microsoft, która zawiera krytyczne systemy (np. colocations, środowiska krytyczne, pokoje MDF itp.) przy użyciu różnych mechanizmów zabezpieczeń, takich jak kontrola dostępu elektronicznych urządzeń biometrycznych, i Formanty Anti-passback. Dostęp do platformy Azure colocations są przyznawane jako osobne, wyższy poziom dostępu DCAT niż innych obszarach dostęp do centrum danych. Ponadto wszystkie Azure ekwiwalentu pełnego wymiaru czasu w i dostawców, którzy mają dostęp do colocations Azure dla instytucji rządowych są wymagane do oficjalnie podlegają kontroli chmury firmy Microsoft i USA możliwości dostępnych weryfikacji przed jej uprawnienia dostępu do środowiska. Zobacz sekcję PS-03, aby uzyskać dodatkowe szczegóły dotyczące wykrywania chmury colocations Azure dla instytucji rządowych. |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800 53 PE kontroli-4

#### <a name="access-control-for-transmission-medium"></a>Kontrola dostępu do nośnika transmisyjnego

**PE 4** organizacja kontroluje dostęp fizyczny do [przypisania: informacje zdefiniowane przez organizację systemu dystrybucji i transmisji linii] w obrębie organizacji urządzeń przy użyciu [przypisania: zabezpieczeń zdefiniowany przez organizację] .

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure została zaimplementowana kontroli dostępu do nośnika transmisyjnego do projektowania i tworzenia pomieszczeń ramki dystrybucji Main (MDF) i colocations do ochrony przed przypadkowym uszkodzeniem systemu informacji o dystrybucji i transmisji linii przerw w działaniu i dostępem fizycznym. Dostęp do MDF pomieszczenia i colocations wymagają uwierzytelniania (wskaźnika dostępu i dane biometryczne) dwuskładnikowego. Dzięki temu, że dostęp jest ograniczony do wyłącznie autoryzowanego personelu (zobacz PE-2, PE 3). W ramach MDF przekazywania i rozpowszechniania wiersze są chronione przed szkody przypadkowe, przerw w działaniu i dostępem fizycznym przy użyciu przewody kompletnego stanu, stojakami zablokowane, klatki lub kabla zasobników. |


 ## <a name="nist-800-53-control-pe-5"></a>Formant PE NIST 800 53-5

#### <a name="access-control-for-output-devices"></a>Kontrola dostępu dla urządzenia wyjściowe

**PE 5** organizacja kontroluje fizyczny dostęp do informacji systemu wyjściowego urządzeń uniemożliwienie nieautoryzowane uzyskania danych wyjściowych.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Centrów danych Microsoft Azure nie ma danych wyjściowych (monitory, drukarki, urządzenia audio, itp.) trwale dołączone do urządzenia Azure zasoby lub zasoby współużytkowane Azure. Oprócz nie ma urządzeń wyjściowych, funkcjonariusze wykonywać fizycznych wskazówki dotyczące instrumentu wiele razy na shift sprawdzanie elementy, takie jak Trwa blokowanie drzwi i stojakami są zabezpieczone. Dostęp do centrum danych jest ograniczona do osób, które zostały zatwierdzone autoryzacji dostępu. Colocations wymagają uwierzytelniania (wskaźnika dostępu i dane biometryczne) do uzyskania dostępu dwuskładnikowego. |


 ## <a name="nist-800-53-control-pe-6a"></a>Formant PE NIST 800 53-6.a

#### <a name="monitoring-physical-access"></a>Monitorowania fizycznego dostępu

**PE 6.a** organizacji monitoruje fizyczny dostęp do funkcji, których system informacji znajduje się wykrywanie i reagowanie na zdarzenia zabezpieczeń fizycznych.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Fizyczny dostęp jest monitorowany przez wykonanie urządzeń zabezpieczeń i procesów w centrach danych. Przykładami 24 x 7 elektronicznych monitorowania kontroli dostępu, alarm i systemy wideo, a także 24 x 7 w lokacji patroli placówki i przyczyn. Przełożonego pomieszczeniu sterowania znajduje się w SOC przez cały czas w celu zapewnienia monitorowania fizycznego dostępu w centrum danych. |


 ## <a name="nist-800-53-control-pe-6b"></a>Formant PE NIST 800 53-6.b

#### <a name="monitoring-physical-access"></a>Monitorowania fizycznego dostępu

**PE 6.b** organizacji przegląda dzienników fizycznego dostępu [przypisania: zdefiniowane przez organizację częstotliwość] i po wystąpieniu [przypisania: zdarzeń zdefiniowanych przez organizację lub potencjalne oznaczenia zdarzeń].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Dzienników fizycznego dostępu są stale sprawdzone i utrzymywana dochodzeniowych weryfikację. |


 ## <a name="nist-800-53-control-pe-6c"></a>Formant PE NIST 800 53-6.c

#### <a name="monitoring-physical-access"></a>Monitorowania fizycznego dostępu

**PE 6.c** organizacji koordynuje wyniki przeglądów i badania możliwości organizacyjnej odpowiedzi na zdarzenia. 

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Zdarzenia zabezpieczeń, które występują w centrum danych są udokumentowane przez zespół ds. Zespół ds tworzy raporty, które przechwytują szczegóły zdarzenia zabezpieczeń po wystąpieniu zdarzenia. <br /> Dla zdarzeń wymagających powiadomień dla instytucji rządowych zespołu ochrony Microsoft Azure będzie koordynować z dostawcą aplikacji głównej (np. usługi Office 365) do powiadamiania klienta agencji rządowych Stanów Zjednoczonych, nam certyfikatu i FedRAMP zgodnie z wytycznymi US-CERT (patrz IR 6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>NIST 800 53 PE kontroli – 6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitorowania fizycznego dostępu | Alarmy nieautoryzowanego dostępu / sprzętu nadzoru

**PE — 6 (1)** organizacji monitoruje alarmy fizycznych nieautoryzowanego dostępu i kontroli urządzeń.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Oprócz zabezpieczeń siedzibie 24 x 7 centrów danych Microsoft Azure, (Wydzierżawionym i pełni zarządzane) również korzystać z monitorowania systemów i systemu telewizji przemysłowej alarm. Alarmy są monitorowane i odpowiedział przełożonego pomieszczeniu sterowania stacjonujących 24 x 7 w SOC. W przypadku odpowiedzi przełożonego pomieszczeniu sterowania korzysta z kamer w obszarze zdarzenia badana umożliwiają udzielenia informacji w czasie rzeczywistym. |


 ### <a name="nist-800-53-control-pe-6-4"></a>NIST 800 53 PE kontroli – 6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitorowania fizycznego dostępu | Monitorowanie fizyczny dostęp do systemów informacyjnych

**PE — 6 (4)** organizacji monitoruje fizyczny dostęp do systemu informacje oprócz monitorowania fizycznego dostępu ze strony jako [przypisania: zdefiniowane przez organizację spacje fizycznego zawierający co najmniej jednego składnika informacji System]

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure monitoruje fizyczny dostęp do urządzenia, a także systemów informacji w centrach danych. Wszystkie witryny Microsoft online services urządzeń znajduje się w lokalizacjach w centrach danych, gdzie dostęp fizyczny jest monitorowany. Wszystkie wspólnej lokalizacji i pomieszczenia MDF są chronione przez kontroli dostępu, alarmy i wideo. |


 ## <a name="nist-800-53-control-pe-8a"></a>Formant PE NIST 800 53-8.a

#### <a name="visitor-access-records"></a>Rekordy dostępu dla gości

**PE 8.a** organizacji przechowuje rekordy dostępu dla gości w obiekcie, w której znajduje się system informacji dla [przypisania: okres zdefiniowany organizacji].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Rejestruje dostęp do centrum danych są obsługiwane w narzędzia dostępu do centrum danych w formularzu żądania zatwierdzone. Zgodnie z opisem w PE 3, gości są wymagane konwojować cały czas. Konwojowanie dostępu w centrum danych jest rejestrowana i jeśli to konieczne może zostać skorelowane z obiekt odwiedzający do przyszłych przeglądu. |


 ## <a name="nist-800-53-control-pe-8b"></a>Formant PE NIST 800 53-8.b

#### <a name="visitor-access-records"></a>Rekordy dostępu dla gości

**PE 8.b** organizacji przegląda rekordy dostępu dla gości [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Osoby odwiedzające z żądaniem zatwierdzonych dostępu będzie miał ich rekordów dostępu sprawdzone w momencie ich identyfikacji zostaną sprawdzone pod kątem formularza dla instytucji rządowych wystawiony identyfikator lub firma Microsoft opublikowała wskaźnika. Zgodnie z opisem w PE 3, gości są eskortowanych przez cały czas znajduje się w centrum danych. |


 ### <a name="nist-800-53-control-pe-8-1"></a>NIST 800 53 PE kontroli-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Rekordy dostępu dla gości | Automatyczne konserwacji rekordów / przeglądu

**PE — 8 (1)** automatyczne mechanizmów ułatwia prowadzenie i przegląd rekordy dostępu osoby odwiedzającej jest stosowana w organizacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure obsługuje rekordów dostęp do centrum danych w DCAT w formularzu żądania DCAT zatwierdzone. DCAT żądania mogą być tylko zatwierdzone przez zespół DCM. Poziomy dostępu w centrum danych są przypisane i zarządzane w ramach DCAT. Dostęp do centrum danych jest przeglądowi co kwartał. Dostęp do centrach danych platformy Azure jest rejestrowana w DCAT i jest dostępny do przyszłych dochodzenia możliwe. Gości są wymagane konwojować cały czas. Konwojowanie dostępu w centrum danych jest rejestrowany w alarm monitorowania systemu, a jeśli to konieczne może zostać skorelowane z obiekt odwiedzający do przyszłych przeglądu. Dostępu osoby odwiedzającej jest przeglądany stale za pomocą przypisanej konwojowanie i przełożonego pomieszczeniu sterowania za pośrednictwem systemu telewizji przemysłowej i alarm monitorowania systemu. Gości nie są dostarczane z dostępem i musi towarzyszyć ich eskorty przez cały czas. |


 ## <a name="nist-800-53-control-pe-9"></a>Formant PE NIST 800 53-9

#### <a name="power-equipment-and-cabling"></a>Sprzęt zasilania i okablowanie

**PE 9** organizacji chroni zasilania sprzętu i zasilania okablowania systemu informacji z uszkodzenia i zniszczenia.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure udostępnia ochronnych spacje i odpowiednie etykietowania kabli. Microsoft Azure infrastruktury urządzeń, na przykład kable, elektrycznego wierszy i tworzenia kopii zapasowej generatory muszą być umieszczone w środowiskach, które zostały zaprojektowane do ochrony przed zagrożeniami środowiska, takich jak kradzież, fire, wybuchowych, dymu wody, pyłu, wibrację, trzęsienia ziemi, szkodliwe chemikaliów, elektrycznego zakłócenia, awarie zasilania i zakłóceń elektrycznego (nagłego). Wszystkie zasoby przenośnej usług online (np. stojakami, serwerów, urządzeń sieciowych) musi być zablokowany lub podłączony w miejscu w celu zapewnienia ochrony przed kradzieżą lub przeniesienia uszkodzenia. Zasilania i informacje w dowolnym środowisku Microsoft Azure są odpowiednio oznaczone i chronione przed przechwyceniem lub uszkodzenia. Kable systemu zasilania i informacje są oddzielone od siebie we wszystkich punktach w danym środowisku, aby uniknąć zakłóceń. |


 ## <a name="nist-800-53-control-pe-10a"></a>Formant PE NIST 800 53-10.a

#### <a name="emergency-shutoff"></a>Bliskie awaryjnego

**PE 10.a** organizacji zapewnia możliwość z wyłączeniem zasilania systemu informacje lub system poszczególnych składników sytuacji awaryjnych.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure został zainstalowany przycisków awaryjnego Power Off (EPO) w lokalizacji w centrum danych, co jest wymagane przez kod lokalnej. W niektórych centrach danych Microsoft Azure zarządzanych projekt centrum danych nie wymaga już EPO przycisków. |


 ## <a name="nist-800-53-control-pe-10b"></a>Formant PE NIST 800 53-10.b

#### <a name="emergency-shutoff"></a>Bliskie awaryjnego

**PE 10.b** organizacji umieszcza przełączniki awaryjnego bliskie lub urządzeń w [przypisania: zdefiniowane przez organizację lokalizacji systemu informacje lub składnik systemu] do ułatwienia bezpiecznego i łatwego dostępu dla personelu.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Przyciski EPO strategicznie są umieszczane zezwalająca na potrzeby aktywacji sytuacji awaryjnych. Przyciski EPO można umieścić w colocations, obsługiwane urządzenia centrów operacji (FOCs) lub jako wymagane przez kod lokalnej. |


 ## <a name="nist-800-53-control-pe-10c"></a>Formant PE NIST 800 53-10.c

#### <a name="emergency-shutoff"></a>Bliskie awaryjnego

**PE 10.c** organizacji chroni zdolność bliskie awaryjnego zasilania przed nieautoryzowanym aktywacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Aby zapobiec przypadkowemu aktywacji, EPO przyciski może mieć ochronnych obudowa wymagają aktywacji podwójną i wykorzystywać alarmu jako ostrzeżenie przed aktywacji. Ponadto przyciski EPO są pod nadzorem wideo. |


 ## <a name="nist-800-53-control-pe-11"></a>Formant PE NIST 800 53-11

#### <a name="emergency-power"></a>Awaryjnego zasilania

**PE 11** organizacji zapewnia krótkoterminowej zasilacz ułatwiające [wybór (jeden lub więcej): prawidłowe zamknięcie systemu informacji; przejścia systemu informacji do długoterminowego zasilania] w przypadku utraty źródła zasilania podstawowego.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure została zaimplementowana nadzwyczajne uprawnienia, aby chronić urządzenia centrum danych i scalone z awaryjny Podaj systemu (UPS), która zapewnia krótkoterminowej zasilacz dostarczania energii, dopóki generatory nie są możliwe do trybu online. |


 ### <a name="nist-800-53-control-pe-11-1"></a>Formant PE NIST 800 53-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Nadzwyczajne uprawnienia | Długoterminowe alternatywny zasilacz - minimalnego możliwości operacyjne

**PE — 11 (1)** organizacji zapewnia długoterminowej alternatywny zasilacz wymagany system informacji, umożliwiającej utrzymania minimalny zestaw możliwości operacyjnych w przypadku rozszerzonej utraty zasilania podstawowego.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure została zaimplementowana długoterminowych dostaw zasilania systemu informacji, który jest w stanie z zachowaniem minimalna wymagana występuje operacyjne możliwości podczas rozszerzonej utraty zasilania podstawowego. Zasilania nie powiedzie się lub spadnie do poziomu napięcia nie do przyjęcia, systemy awaryjny (UPS) natychmiast zaczną działać i przejęcia obciążenia zasilania. Zapewnia wystarczająco dużo mocy korzystającej z serwerów, dopóki generatory może zająć ponad. Awaryjnych podłączać kopii zapasowych dla dłuższych przestojów i planowanej konserwacji i mogą pracować ze rezerw na miejscu paliwa w przypadku klęski żywiołowej centrum danych. Azure obsługuje Diesla generatora wiele naszych centrach danych. Generatory kopii zapasowej są używane, gdy jest to niezbędne do pomagają w utrzymaniu stabilności siatki lub naprawy nadzwyczajne i sytuacjach konserwacji, które wymagają nam podjęcie naszych centrach danych poza siatkę zasilania. |


 ## <a name="nist-800-53-control-pe-12"></a>Formant PE NIST 800 53-12

#### <a name="emergency-lighting"></a>Oświetlenie

**PE — 12** organizacja stosuje i obsługuje automatyczne awaryjnego oświetlenia dla systemu informacji, który uaktywnia w przypadku awarii zasilania lub przerw w działaniu i który obejmuje awaryjnego kończy działanie oraz ewakuacji w obrębie obiektu.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Centrów danych Microsoft Azure, (Wydzierżawionym i zarządzane w pełni) implementuje oświetlenia awaryjnego w formie koszty oświetlenia awaryjnego na dedykowany obwody kopii zapasowej przez systemy generator (patrz PE-11) i UPS. Automatyczne oświetlenie zaimplementowano wzdłuż wszystkich ewakuacji, wyjściach, a wewnątrz colocations zgodnie z Fire krajowych i kod bezpieczeństwa życia ochrony skojarzenia (NFPA). W przypadku, gdy zasilania zostały utracone, oświetlenia awaryjnego zostanie automatycznie przełączona do potęgi pochodzącymi z systemami UPS i generator. Systemów oświetlenia awaryjnego w centrach danych Microsoft Azure przechodzą rutynowej konserwacji, aby upewnić się, że pozostają w stanie właściwe. |


 ## <a name="nist-800-53-control-pe-13"></a>Formant PE NIST 800 53-13

#### <a name="fire-protection"></a>Uruchomienie ochrony

**PE — 13** organizacja stosuje i przechowuje fire pomijanie wykrywania urządzeń/systemy i systemu informacje są obsługiwane przez źródło energii niezależne.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure została zaimplementowana ochrona fire, instalując systemów fire i pomijania w centrach danych Microsoft Azure. <br /> Microsoft Azure w centrach danych implementuje fire niezawodną mechanizmy wykrywania. Podejście ochrony fire Microsoft Azure obejmuje użycie próbkowania fotoelektrycznego detektory dymu zainstalowane poniżej piętro i na limitu, które są zintegrowane z systemu zraszającego fire ochrony. Ponadto istnieją systemy VESDA Xtralis (bardzo wczesne dymu wykrywania urządzenia) w każdym kolokacji monitorować udziału użytkownika. Jednostki VESDA to systemy próbkowania lotniczego bardzo ważne zainstalowany w całym wiele spacji wysokiej wartości. Jednostki VESDA umożliwiają dochodzeniowych odpowiedzi przed rzeczywistej alarmowego wykrywania. <br /> Pola "Pull stacji" alarmowego są instalowane w centrach danych ręczne alarmowego powiadomienia. Gaśnice znajdują się w centrach danych są poprawnie inspekcji, obsługiwane i oznakowane co roku. Personel zabezpieczeń patrols wszystkich budynków wielokrotnie co shift. Personel centrum danych wykonywać codzienne przewodnika lokacji sprawdzeniu wszystkich obserwować fire spełnienie wymagań. <br /> Obszary zawierające poufne maszyn (colocations, MDFs, itp.) są chronione przez systemów zraszaczy podwójne blokada akcji przed (suchej potoku). Zraszacze suche są systemu akcji przed dwuetapowa, który wymaga zarówno zraszaczy aktywacji head (z powodu interakcji) jak i wykrywania dymu zwolnienia limitu górnego. Aktywacja head zraszaczy zwalnia wykorzystania lotniczego w potoków, dzięki czemu potoków wypełnić limitu górnego. Limitu górnego jest zwalniany, kiedy detektora dym lub cieplna również jest aktywowany. <br /> Oświetlenie systemów i Fire wykrywania/pomijania przewodowe do datacenter zasilacz UPS i systemów generatora źródła nadmiarowy. |


 ### <a name="nist-800-53-control-pe-13-1"></a>NIST 800 53 PE kontroli-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Uruchomienie ochrony | Wykrywanie urządzeń / systemy

**PE — 13 (1)** wymaga organizacji wyzwalać wykrywania urządzeń/komputerach systemu informacji o aktywacji automatycznie i powiadom [przypisania: techniczną zdefiniowany przez organizację lub ról] i [przypisania: awaryjnego zdefiniowany przez organizację obiekty odpowiadające] w przypadku fire.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure wymaga wyzwalać wykrywania urządzeń/komputerach systemu informacji o aktywacji automatycznie i powiadom personelu Centrum danych oraz awaryjnego obiektów odpowiadających w przypadku fire. W przypadku, gdy z mechanizmów wykrywania fire aktywowaniu w dowolnym wspólnej lokalizacji lokalnej działu fire jest powiadamiany automatycznie za pośrednictwem systemu alarmowego. Ponadto systemy wykrywania ochrony i fire fire są również powiązane w systemie zabezpieczeń powiadamiania personelu lokalnego, jak i zabezpieczeń. |


 ### <a name="nist-800-53-control-pe-13-2"></a>NIST 800 53 PE kontroli-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Uruchomienie ochrony | Pomijanie urządzenia / systemy

**PE — 13 (2)** wymaga organizacji wyzwalać pomijania urządzenia/systemy dla systemu informacje, które zapewniają automatyczne powiadomienia o dowolnym aktywacji do [przypisania: techniczną zdefiniowany przez organizację lub ról] i [przypisania: zdefiniowany przez organizację awaryjnego obiektów odpowiadających].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. W przypadku, gdy jeden z systemów pomijania fire aktywowaniu w centrum danych z lokalnego działu fire jest powiadamiany automatycznie za pośrednictwem systemu alarmowego. Ponadto systemy wykrywania ochrony i fire fire są również powiązane w systemie zabezpieczeń powiadamiania personelu lokalnego, jak i zabezpieczeń. |


 ### <a name="nist-800-53-control-pe-13-3"></a>NIST 800 53 PE kontroli-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Uruchomienie ochrony | Pomijanie Fire automatyczne

**PE — 13 (3)** możliwości pomijania automatycznej fire systemu informacji jest stosowana w organizacji, podczas funkcji nie pracownicy w sposób ciągły.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure w centrach danych są obsługą 24 x 7. Systemy pomijania Fire Uwzględnij automatycznie bez konieczności ręcznej interwencji po wykryciu sytuacji alarmowego. |


 ## <a name="nist-800-53-control-pe-14a"></a>Formant PE NIST 800 53-14.a

#### <a name="temperature-and-humidity-controls"></a>Temperatury i wilgotności formantów

**PE 14.a** organizacji przechowuje temperatury i wilgotności poziomy w obrębie obiektu, w którym system informacji znajduje się w [przypisania: zdefiniowane przez organizację akceptowalnego poziomu].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure obsługuje poziomy temperatury i wilgotności, zgodnie z wytycznymi American społeczeństwa ogrzewania Refrigerating i inżynierów klimatyzacja (symbole). Poziomy temperatury i wilgotności są stale monitorowane przez biuletynie centrum danych budynku zarządzania systemem (BMS). |


 ## <a name="nist-800-53-control-pe-14b"></a>Formant PE NIST 800 53-14.b

#### <a name="temperature-and-humidity-controls"></a>Temperatury i wilgotności formantów

**PE 14.b** organizacji monitoruje poziom temperatury i wilgotności [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. W centrach danych Microsoft Azure poziomy temperatury i wilgotności monitorowane są stale przez System zarządzania budynku biuletynie (BMS). Personel centrum danych monitora biuletynie BMS z Centrum operacje urządzenia (FOC), tak, aby przed punktów alarm zostaną przekroczone mogą zarządzać temperatury i wilgotności w centrum danych. |


 ### <a name="nist-800-53-control-pe-14-2"></a>NIST 800 53 PE kontroli-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Temperatury i wilgotności kontrolki | Monitorowanie za pomocą alarmy / powiadomienia

**PE — 14 (2)** organizacja stosuje temperatury i wilgotności monitorowania, który pozwala alarm lub powiadomienia o zmianach potencjalnie szkodliwe personelu lub urządzeń.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. W centrach danych Microsoft Azure poziomy temperatury i wilgotności monitorowane są stale przez System zarządzania budynku biuletynie (BMS). Personel centrum danych monitora biuletynie BMS z Centrum operacje urządzenia (FOC), tak, aby przed punktów alarm zostaną przekroczone mogą zarządzać temperatury i wilgotności w centrum danych. |


 ## <a name="nist-800-53-control-pe-15"></a>NIST 800 53 PE kontroli-15

#### <a name="water-damage-protection"></a>Ochrona uszkodzenia limitu górnego

**PE 15** organizacji chroni system informacji z szkody wynikające z wyciek wody, zapewniając wzorca zawory bliskie lub izolacji, które są dostępne, działa prawidłowo i znanych do personelu klucza.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure udostępnia wykrywania limitu górnego/przecieków w obszarach o ryzyka wycieku wody (np. jednostek obsługi lotniczego). Systemy pomijania Fire również mieć przeciek alarmy wykrywania, które są monitorowane. System wykrywania limitu górnego/przeciek jest zintegrowany z systemem alarmu i powiadomień zakładzie. Gaśnicze w centrach danych są podzielone na strefy. Procedury awaryjnego wymagających zaworów bliskie limitu górnego oraz ich lokalizacje znają personelu Centrum danych. Riser zraszaczy mają możliwość wyłączone pojedynczo lub grupowo za pośrednictwem zawory bramy. Wszystkie zraszacze przestrzeni krytyczne są zraszacze typ akcji przed blokada dwa razy, wymagające dwóch metod aktywacji, zanim zainicjowaniu przepływu. Wykorzystanie systemu zraszającego jest monitorowane i alarmed przed wyciekiem limitu górnego. |


 ### <a name="nist-800-53-control-pe-15-1"></a>NIST 800 53 PE kontroli – 15 (1)

#### <a name="water-damage-protection--automation-support"></a>Ochrona uszkodzenia wody | Obsługa automatyzacji

**PE – 15 (1)** organizacja stosuje mechanizmy automatycznego do wykrywania obecności wody w pobliżu informacji systemu i alerty [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure wykorzystuje mechanizmy automatycznego do wykrywania obecności wody w centrach danych i alertów personelu Centrum danych. Platforma Azure udostępnia wykrywania limitu górnego/przecieków w obszarach o ryzyka wycieku wody (np. jednostek obsługi lotniczego). Systemy pomijania Fire również mieć przeciek alarmy wykrywania, które są monitorowane. System wykrywania limitu górnego/przeciek jest zintegrowany z systemem alarmu i powiadomień zakładzie. Wykorzystanie systemu zraszającego jest monitorowane i alarmed przed wyciekiem limitu górnego. |


 ## <a name="nist-800-53-control-pe-16"></a>Formant PE NIST 800 53-16

#### <a name="delivery-and-removal"></a>Dostarczania i usuwania

**PE 16** organizacji autoryzuje, monitoruje i kontroluje [przypisania: zdefiniowane przez organizację typy informacji na składniki systemu] wprowadzanie i zamykanie funkcji i obsługuje rekordy z tych elementów.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure implementuje ten wymóg w imieniu klientów. Microsoft Azure implementuje wymuszone stosowanie dozwolonych i wyjścia centrum danych. Wszystkie składniki/zasoby systemu są śledzone w bazie danych zasobów narzędzia do zarządzania. |


 ## <a name="nist-800-53-control-pe-17a"></a>Formant PE NIST 800 53-17.a

#### <a name="alternate-work-site"></a>Alternatywne pracy witryny

**PE 17.a** organizacja stosuje [przypisania: kontroli zabezpieczeń zdefiniowany przez organizację] w miejscach pracy alternatywny.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Przepisy lokacji przedsiębiorstw alternatywny pracy klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-pe-17b"></a>Formant PE NIST 800 53-17.b

#### <a name="alternate-work-site"></a>Alternatywne pracy witryny

**PE 17.b** organizacji ocenia jak to możliwe, skuteczności zabezpieczeń formanty w miejscach pracy alternatywny.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Przepisy lokacji przedsiębiorstw alternatywny pracy klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-pe-17c"></a>NIST 800-53 Control PE-17.c

#### <a name="alternate-work-site"></a>Alternatywne pracy witryny

**PE 17.c** organizacji umożliwia pracownikom do komunikowania się z personelem zabezpieczeń informacji w przypadku problemów lub zdarzeń zabezpieczeń.

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Przepisy lokacji przedsiębiorstw alternatywny pracy klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-pe-18"></a>Formant PE NIST 800 53-18

#### <a name="location-of-information-system-components"></a>Lokalizacja informacji na składniki systemu

**PE 18** organizacji umieszcza składników systemu informacji w obrębie obiektu, aby zminimalizować potencjalne szkody z [przypisania: zdefiniowane przez organizację fizyczne i środowiskowe zagrożenia] i minimalizuje możliwość wystąpienia dla nieautoryzowany dostęp.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Klienci nie mieć fizyczny dostęp do zasobów systemu w centrach danych platformy Azure. |
| **Dostawca (Microsoft Azure)** | Azure implementuje podejście do projektowania strategicznych centrum danych do zaspokojenia lokalizacji formantu składników systemu informacji. Wszystkie witryny Microsoft online services urządzeń znajduje się w lokalizacjach, które zostały zaprojektowane do ochrony przed zagrożeniami środowiska, takich jak kradzież, fire, wybuchowych, dymu, limitu górnego, pyłu, wibrację, trzęsienie ziemi, szkodliwe chemikaliów, elektrycznego zakłócenia, awarie zasilania elektrycznego zakłóceń (nagłego) i promieniowania. Funkcje i infrastruktury wdrożono sejsmiczna tężników do ochrony przed zagrożeniami środowiska. Wszystkie wspólnej lokalizacji i pomieszczenia MDF są chronione przez kontroli dostępu, alarmy i wideo. Funkcji jest również patrolowane przez właściwych zabezpieczeń 24 x 7. Wszystkie zasoby Azure przenośnych są zablokowane lub podłączony w miejscu w celu zapewnienia ochrony przed kradzieżą lub przeniesienia uszkodzenia. |



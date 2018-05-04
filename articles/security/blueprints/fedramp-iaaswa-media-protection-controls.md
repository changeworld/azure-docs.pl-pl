---
title: "Plan ochrony Media - automatyzacji aplikacji sieci Web FedRAMP - Azure zabezpieczeń i zgodności"
description: "Automatyzacja aplikacji FedRAMP Web — ochrony nośnika"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 37812c2f7ee79685f9014a7999b4355e649ca6e1
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2018
---
# <a name="media-protection-mp"></a>Ochrona nośnika (MP)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-mp-1"></a>Formant NIST 800 53 MP-1

#### <a name="media-protection-policy-and-procedures"></a>Zasady ochrony nośników i procedur

**MP-1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] zasady ochrony nośnika, którego dotyczy cel, zakres, ról, obowiązki zobowiązań zarządzania, koordynacji wśród jednostki organizacyjnej i zgodności; i procedury w celu ułatwienia implementacji zasad ochrony nośników i kontrolek ochrony skojarzonych z nimi nośników; przegląda i aktualizuje bieżące zasady ochrony nośnika [przypisania: zdefiniowane przez organizację częstotliwość]; i procedur ochrony nośnika [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:**`Customer Only`

|||
|---|---|
| **Klienta** | Zasady ochrony nośnika na poziomie przedsiębiorstwa i procedur klienta mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-mp-2"></a>Formant NIST 800 53 MP-2

#### <a name="media-access"></a>Dostępu do nośnika

**MP-2** organizacji, ogranicza dostęp do [przypisania: zdefiniowane przez organizację typów nośników cyfrowych i/lub -cyfrowe] do [przypisania: techniczną zdefiniowany przez organizację lub ról].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure został zaimplementowany dostępu do nośnika poprzez wdrożenie zasady zabezpieczeń firmy Microsoft. Logicznego dostępu do nośnika cyfrowego jest kontrolowany przez Active Directory obiekty zasad grupy (GPO AD) i grupy zabezpieczeń. Fizyczny dostęp do wszystkich nośników jest ograniczone przez proces dostęp do centrum danych. Dostęp jest ograniczony do osób, które cel prawem firmę do uzyskiwania dostępu do danych. Zapoznaj się pe3, fizyczną kontrolę dostępu, uzyskać więcej informacji dotyczących kontroli dostępu do centrum danych. Standardowa ochrony zasobów definiuje zabezpieczeń wymaganych do ochrony poufności, integralności i dostępności zasobów informacyjnych w centrach danych Microsoft Azure. |


 ## <a name="nist-800-53-control-mp-3a"></a>Formant NIST 800 53 MP-3.a

#### <a name="media-marking"></a>Oznaczanie nośnika

**Pakiet administracyjny 3.a** organizacji oznacza nośnika systemu informacje wskazujące ograniczenia dystrybucji, obsługa ostrzeżenia i oznaczenia ochrony (jeśli istnieją) informacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure oznacza zasoby w centrach danych firmy Microsoft o dużym znaczeniu Biznesowym, MBI lub LBI oznaczenia (wysoki, umiarkowany lub firm niskim wpływie na środowisko), co wymaga różnych poziomów zabezpieczeń i obsługi środki ostrożności. Właściciele zawartości są wymagane do klasyfikowania ich zasoby, które są przechowywane w centrum danych firmy Microsoft. Aby uzyskać więcej informacji, zapoznaj się Standardowa Klasyfikacja zasobów i zasobów ochrony Standard. |


 ## <a name="nist-800-53-control-mp-3b"></a>Formant NIST 800 53 MP-3.b

#### <a name="media-marking"></a>Oznaczanie nośnika

**MP 3.b** wyłącza organizacji [przypisania: zdefiniowany przez organizację typy nośnika systemu informacje] oznaczyć tak długo, jak nośnik pozostają w [przypisania: zdefiniowane przez organizację kontrolowane obszarów].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure wymaga właścicieli zasobów, można przypisać ich zasobów z klasyfikacją zasobów i żadne zasoby nie podlegają to wymaganie. W środowisku centrum danych firmy Microsoft zasoby odnosi się do serwerów, urządzeń sieciowych i taśmy magnetyczne. Innego nośnika cyfrowego, takich jak USB flash/thumb dysków, zewnętrzne/wymienne dyski twarde, lub dysk CD/DVD nie są używane. Non cyfrowy nośnika nie jest używany w centrum danych. |


 ## <a name="nist-800-53-control-mp-4a"></a>Formant NIST 800 53 MP-4.a

#### <a name="media-storage"></a>Media Storage

**MP 4.a** organizacja kontroluje fizycznie i są bezpiecznie przechowywane [przypisania: zdefiniowane przez organizację typów nośników cyfrowych i/lub -cyfrowe] w [przypisania: zdefiniowane przez organizację kontrolowane obszarów].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nośnika cyfrowego zasoby są fizycznie i bezpiecznie przechowywane w ramach pomieszczenia kolokacji centrum danych. Centrach danych firmy Microsoft ma wiele warstw fizyczne metody kontroli dostępu (wskaźnika dostępu, biometria; Zobacz PE 3, aby uzyskać szczegółowe informacje na fizyczne metody kontroli dostępu) i nadzoru wideo w celu zapewnienia bezpiecznego magazynu. Cyfrowe dla nośnikami serwerów, urządzeń sieciowych i taśmy magnetyczne używany do utworzenia kopii zapasowej. Nośniki non cyfrowy nie są używane w środowisku centrum danych. |


 ## <a name="nist-800-53-control-mp-4b"></a>Formant NIST 800 53 MP-4.b

#### <a name="media-storage"></a>Media Storage

**Pakiet administracyjny 4.b** organizacji chroni informacji nośnika systemu, dopóki nie zostaną zniszczone nośnika lub oczyszczony przy użyciu zatwierdzonych sprzętu, technik i procedur.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nośnika cyfrowego zasoby są chronione w colocations centrum danych firmy Microsoft za pośrednictwem fizyczne metody kontroli dostępu (pe3) i kontroli dostępu logiczne (IA-2) przez czas ich istnienia zasobu. Zasoby Microsoft Azure są wyczyszczone, wyczyszczone lub niszczona razem z metody zgodne z dodatkiem SP NIST 800 88 przed usunięciem zasoby. Zniszczenie zasobów Microsoft Azure korzysta z usługi zniszczenie zasobów w siedzibie organizacji. |


 ## <a name="nist-800-53-control-mp-5a"></a>Formant NIST 800 53 MP-5.a

#### <a name="media-transport"></a>Nośnik transportu

**MP 5.a** organizacji chroni i kontroluje [przypisania: zdefiniowane przez organizację typów nośnika systemu informacje] podczas transportu poza kontrolowanym przy użyciu [przypisania: zabezpieczeń zdefiniowany przez organizację].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure nośnika cyfrowego w centrach danych firmy Microsoft składają się z serwerów, urządzeń sieciowych i magnetycznego kopii zapasowej taśmy i dyski, gdzie jest to odpowiednie. Centrach danych firmy Microsoft nie należy używać nośników-cyfrowego. Microsoft korzysta z trzech metod w celu ochrony nośnika, który jest transportowane poza centrum danych: 1) bezpiecznego transportu, 2) szyfrowania 3) czyszczenia, Przeczyść lub zniszczenia. |


 ## <a name="nist-800-53-control-mp-5b"></a>Formant NIST 800 53 MP-5.b

#### <a name="media-transport"></a>Nośnik transportu

**Pakiet administracyjny 5.b** organizacji utrzymuje accountability dla nośnika systemu informacji podczas transportu poza kontrolowanym.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure obsługuje accountability zasobów, pozostawiając centrum danych przy użyciu wskazówek z SP NIST 800 88: spójne czyszczenia/przeczyszczanie, zniszczenie zasobów szyfrowania, tworzenia spisu dokładne, śledzenia i ochrony łańcuch nadzoru podczas transportu. |


 ## <a name="nist-800-53-control-mp-5c"></a>Formant NIST 800 53 MP-5.c

#### <a name="media-transport"></a>Nośnik transportu

**Pakiet administracyjny 5.c** organizacji dokumentów czynnościami związanymi z transportem nośnika systemu informacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure obsługuje rekordów spisu przed transportu, śledzenia i ochrony łańcuch nadzoru podczas transportu, czyszczenia/przeczyszczanie zasobów, zasobów zniszczenie, otrzymania zasoby i sprawdzania poprawności magazynu po transportu. |


 ## <a name="nist-800-53-control-mp-5d"></a>Formant NIST 800 53 MP-5.d

#### <a name="media-transport"></a>Nośnik transportu

**Pakiet administracyjny 5.d** organizacji ogranicza działania związane z transportem nośnika systemu informacji do autoryzowanego personelu.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure ogranicza działania transportu zasobów do autoryzowanego personelu ochrona łańcuch nadzoru. Użycie blokad, umożliwiające potwierdzenia zamknięcia, a wymagające weryfikacji spisów zasobów zapewnia, że tylko do autoryzowanego personelu są zaangażowane w transporcie zasobów. |


 ### <a name="nist-800-53-control-mp-5-4"></a>NIST 800 53 kontroli MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Nośnik transportu | Ochrona usług kryptograficznych

**MP-5 (4)** system informacji implementuje mechanizmów kryptograficznych, aby chronić prywatność i integralność informacji przechowywanych na nośnika cyfrowego podczas transportu poza kontrolowanym.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure wymaga danych ochrony usługi (punktu dystrybucji) do zarządzania kryptograficzne klucze za pomocą FIPS 140-2 zweryfikowane 3: szyfrowanie na poziomie modułu (cert #1694) i sprzętowego modułu zabezpieczeń (cert #1178), aby zabezpieczyć dane zaszyfrowane AES 256-bitowy na taśmy magnetyczne. |


 ## <a name="nist-800-53-control-mp-6a"></a>Formant NIST 800 53 MP-6.a

#### <a name="media-sanitization"></a>Ich oczyszczania nośnika

**MP 6.a** oczyszcza organizacji [przypisania: nośnika systemu informacje zdefiniowane przez organizację] przed usunięciem, spoza organizacji formantu, czy zwykłe wydanie ponownemu użyciu [przypisania: techniki zdefiniowany przez organizację ich oczyszczania i procedur] zgodnie z odpowiednich standardów federalnych i organizacyjne i zasady.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure wymaga nośnika cyfrowego w środowisku centrum danych Microsoft Azure zostaną oczyszczone/usunięte przy użyciu narzędzia Microsoft Azure zatwierdzone i w sposób zgodny z dodatkiem SP NIST 800-88 wytyczne dla nośnika ich oczyszczania, przed są ponownie używane lub usunięte . Nośniki non cyfrowy nie są używane przez system Microsoft Azure w środowisku centrum danych. |


 ## <a name="nist-800-53-control-mp-6b"></a>Formant NIST 800 53 MP-6.b

#### <a name="media-sanitization"></a>Ich oczyszczania nośnika

**Pakiet administracyjny 6.b** mechanizmów ich oczyszczania z siły i integralności proporcjonalnie do kategorii lub klasyfikacji informacji jest stosowana w organizacji.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure używa jednostki usunięcia danych i procesów do czyszczenia/czyszczenie danych w sposób zgodny z dodatkiem SP NIST 800 88 i które są proporcjonalnie do środka trwałego klasyfikacji zasobów Microsoft Azure. Wymaganie zniszczenie zasobów, w Microsoft Azure korzysta z usługi zniszczenie zasobów w siedzibie organizacji. |


 ### <a name="nist-800-53-control-mp-6-1"></a>NIST 800 53 kontroli MP – 6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Nośnik ich oczyszczania | Przejrzyj / zatwierdzanie / śledzić / dokumentu / Verify

**MP-6 (1)** organizacji przegląda, zatwierdza śledzi, dokumentów i weryfikuje działań nośnika ich oczyszczania i usuwania.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure została zaimplementowana procedur ich oczyszczania nośnika zgodnie z instrukcjami podanymi w SP NIST 800 88 Standardowa Klasyfikacja zasobów i standardowe ochrony zasobów. Wszystkie nośniki magnetycznego lub elektronicznego jest oczyszczone/przeczyszczane przy następujących specyfikacji 800 88 NIST SP zgodnie z jego klasyfikacja zasobów platformy Azure. Azure korzysta z jednostki usunięcia danych z najwyższą protokołu rozwiązania (EPS). EPS oprogramowania obsługuje NIST SP wymagania 800 88 czyszczenia i przeczyszczanie bezpiecznego usunięcia. |


 ### <a name="nist-800-53-control-mp-6-2"></a>NIST 800 53 kontroli MP – 6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Nośnik ich oczyszczania | Testowanie sprzętu

**MP-6 (2)** organizacji testów ich oczyszczania sprzętu i procedur [przypisania: zdefiniowany przez organizację częstotliwość] można zweryfikować, że zamierzone ich oczyszczania zostanie osiągnięty.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure używa jednostki usunięcia danych i procesów do czyszczenia/czyszczenie danych w sposób zgodny z dodatkiem SP NIST 800-88. Raz na 180 dni, operacje kontrolery domeny testy jednostek usunięcia danych Microsoft Azure i proces usuwania. W teście operacje kontrolerów domeny sprawdza, czy zamierzone ich oczyszczania jest osiągany za pomocą analizy śledczej przetestowany dysków twardych, aby upewnić się, że dane zostały oczyszczony przez jednostki usunięcia danych |


 ### <a name="nist-800-53-control-mp-6-3"></a>NIST 800 53 kontroli MP – 6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>Nośnik ich oczyszczania | Techniki bezpieczne

**MP-6 (3)** organizacji dotyczy techniki ich bezpieczne oczyszczania urządzenia przenośnego urządzenia pamięci masowej przed łączyć tych urządzeń z systemem informacje w następujących okolicznościach: [przypisania: zdefiniowane przez organizację okoliczności wymagające ich oczyszczania urządzenia przenośnego urządzenia pamięci masowej].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure zapewnia, że centrach danych platformy Azure wykonaj zapobiec zainfekowaniu środowiska dla instytucji rządowych przez złośliwe oprogramowanie na urządzeniach przenośnych magazynu narzędzia i wymiennego nośnika procedury bezpieczeństwa w książce Uruchamianie usług centrum danych. Procedura określa podjęcie następujących akcji z dysków USB przed użyciem w środowisku dla instytucji rządowych: <br /> (1) Formatuj dyski USB, gdy dyski są najpierw zakupionego od producenta lub dostawcy, przed pierwszym użyciem lub używane ponownie dla innego narzędzia. <br /> (2) skanowania dowolnego dysku USB do użycia w obszarze wyznaczonego dla instytucji rządowych złośliwego oprogramowania, przed zmianą na dysku do obszaru. <br /> (3) po użyciu dysku w obszarze wyznaczonego dla instytucji rządowych, formatowania dysku przed opuszczeniem obszaru. <br /> Narzędzia i procedury bezpieczeństwa nośnik wymienny również wymaga wszystkich thumb utracone, odrzuconych, kradzieży lub niewłaściwie dysków nigdy nie zostać wprowadzone ponownie w centrach danych platformy Azure, ale aby być zamiast tego skatalogować i zniszczone. |


 ## <a name="nist-800-53-control-mp-7"></a>Formant NIST 800 53 MP-7

#### <a name="media-use"></a>Użyj nośnika

**MP-7** organizacji [zaznaczenia: ogranicza; zabrania] użycie [przypisania: zdefiniowany przez organizację typy nośnika systemu informacji] na [przypisania: systemów informacje zdefiniowane przez organizację lub składników systemu] przy użyciu [przypisania: zdefiniowany przez organizację zabezpieczającymi].

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Platforma Microsoft Azure wymaga właścicieli zasobów, można przypisać ich zasobów z klasyfikacją zasobów, a żadne zasoby nie podlegają to wymaganie. W środowisku centrum danych Microsoft Azure zasoby odnosi się do serwerów i urządzeń sieciowych. Innego nośnika cyfrowego, takich jak dyski flash/pamięci przenośnej USB są zarządzane przez określone zasady i procedury dotyczące sposobu zarządzania tymi urządzeniami. Dysk CD/DVD nie są używane. Non cyfrowy nośnika nie jest używany w centrum danych. Użycie nośnika cyfrowego w środowisku centrum danych Microsoft Azure jest monitorowana 24 x 7 za pośrednictwem systemu telewizji przemysłowej pokrycia. Aby uzyskać więcej informacji, zobacz PE 06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>NIST 800 53 kontroli MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Użyj nośnika | Zabroń używania bez właściciela

**MP-7 (1)** organizacji zabrania używania urządzenia przenośnego urządzenia pamięci masowej w systemach informacje organizacyjne, wówczas, gdy takich urządzeń do zidentyfikowania właściciela.

**Obowiązki:**`Azure Only`

|||
|---|---|
| **Klienta** | Nie ma nośnika kontrolowane przez klienta w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft ogranicza użycie zapisywalny, wymiennego nośnika do nośnika, który został jawnie zatwierdzony przez zarządzanie Centrum danych za pomocą narzędzia kontrolerów domeny i procedury nośnika wymiennego. Nośnik, który jest właścicielem osobiście lub nie ma do zidentyfikowania ono właściciela jest zabronione w dowolnym obszarze produkcji, zgodnie z opisem w dokumencie programu Microsoft Datacenter pracy regułami i przepisami. |

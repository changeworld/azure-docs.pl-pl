---
title: Plan Azure przetwarzania płatności — wymagania dotyczące tożsamości
description: Wymaganie PCI DSS 8
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a83040a6b5174307ea73e5473165835458d217f6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące tożsamości dla środowisk standardem PCI DSS 
## <a name="pci-dss-requirement-8"></a>Wymaganie PCI DSS 8

**Identyfikację i uwierzytelnienie dostęp do składników systemu**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Przypisywanie Unikatowy identyfikator (ID), aby każda osoba z dostępem zapewnia, że każda osoba, jest unikatowo odpowiedzialne za swoje działania. Takie accountability znajduje się w miejscu, działania podjęte w danych o kluczowym znaczeniu i systemy są wykonywane przez i mogą być śledzone do znanych i autoryzowanych użytkowników i procesów.
Skuteczność hasła przede wszystkim jest określany przez projekt i implementację systemu uwierzytelniania — szczególnie, jak często próby podania hasła może się osoba atakująca i metody zabezpieczeń do ochrony haseł użytkownika w punkcie wejścia, podczas transmisji i znajduje się w magazynie.

> [!NOTE]
> Te wymagania są stosowane do wszystkich kont, w tym punkcie sprzedaży konta, z możliwości administracyjne i wszystkie konta używane do przeglądania lub dostępu posiadacza danych lub systemów dostępu z danymi imię. Dotyczy to również kont używanych przez dostawców i osoby trzecie (na przykład dla pomocy technicznej lub konserwacji). Te wymagania nie dotyczą konta używane przez użytkowników (np. posiadacze kart mający). Jednak wymagania 8.1.1, 8.2, 8.5, 8.2.3 za pośrednictwem 8.2.5 i 8.1.6 za pośrednictwem 8.1.8 nie są przeznaczone do zastosowania do kont użytkowników w punkcie sprzedaży płatności aplikacji, które miało dostęp tylko do jednego numeru karty w czasie w celu umożliwienia (jednej transakcji przykład kasjer kont).
 
## <a name="pci-dss-requirement-81"></a>Wymaganie PCI DSS 8.1

**8.1** Zdefiniuj i zaimplementuj zasad i procedur w celu zapewnienia prawidłowego użytkownika zarządzania identyfikacji — konsument użytkowników i administratorów na wszystkich składników systemu w następujący sposób.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Przypadek użycia i opis odpowiednie użycie administratorów zapewnia magazynu sieci Web firmy Contoso Przykładowe wdrożenie.|



### <a name="pci-dss-requirement-811"></a>Wymaganie PCI DSS 8.1.1

**8.1.1** przypisać unikatowy identyfikator wszystkich użytkowników przed zezwoleniem na dostęp do składników systemu lub posiadacza danych.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso implementuje usługi Azure Active Directory i kontroli dostępu Azure Active Directory Role-Based (RBAC) do zapewnienia wszystkich użytkowników mają unikatowego identyfikatora. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>Wymaganie PCI DSS 8.1.2

**8.1.2** kontrolować dodawanie, usuwanie i zmianę nazwy użytkowników, poświadczeń i innych obiektów identyfikator.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso implementuje usługi Azure Active Directory i kontroli dostępu Azure Active Directory Role-Based (RBAC) do zapewnienia wszystkich użytkowników mają unikatowego identyfikatora. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>Wymaganie PCI DSS 8.1.3

**8.1.3** natychmiast odwołanie dostępu dla dowolnego zakończone użytkowników.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Active Directory do zarządzania użytkownikami. Odwołania użytkowników może odbywać się w usłudze Active Directory.|



### <a name="pci-dss-requirement-814"></a>Wymaganie PCI DSS 8.1.4

**8.1.4** usunąć lub wyłączyć nieaktywne konta w ciągu 90 dni.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Active Directory do zarządzania użytkownikami. `-enableADDomainPasswordPolicy` Można ustawić opcję zapewnienie hasła wygasną w ciągu 90 dni.|



### <a name="pci-dss-requirement-815"></a>Wymaganie PCI DSS 8.1.5

**8.1.5** obsługuje zarządzanie identyfikatory używane przez osoby trzecie, aby uzyskać dostęp, lub Obsługa składników systemu za pośrednictwem zdalnego dostępu w następujący sposób:
- Włączone tylko na czas trwania okresu potrzebne i wyłączone nieużywane.
- Monitorowane w użyciu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure przyjęła zasad firmy i ochrony, w tym zasady zabezpieczeń informacji. Zasady zostały zatwierdzone, opublikowana i przekazywane do systemu Microsoft Azure. Zasady zabezpieczeń informacji wymaga dostępu do zasobów firmy Microsoft Azure, aby otrzymać oparte na biznesowego wyjaśnienia, z autoryzacji właściciela zasobów i ograniczona na podstawie zasad "do ograniczonego" i "uprawnień". Ponadto zasady dotyczy również wymagania dotyczące cyklu życia zarządzania dostęp do takich jak dostęp do inicjowania obsługi administracyjnej, uwierzytelniania, autoryzacji dostępu, usuwanie praw dostępu i okresowe dostęp recenzji. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Wersja demonstracyjna magazynu sieci Web firmy Contoso zaimplementowała Azure Active Directory i kontroli dostępu Azure Active Directory Role-Based zarządzanie dostępem użytkowników do instalacji. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>Wymaganie PCI DSS 8.1.6

**8.1.6** limit powtarzany podejmowane próby uzyskania dostępu przez zablokowania identyfikator użytkownika po nie więcej niż sześciu prób.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso Webstore zaimplementowała przejrzysty podział obowiązków (DARNI) dla wszystkich użytkowników pokaz. Aby uzyskać więcej informacji, zobacz ""Azure Active Directory Identity Protection"w [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>Wymaganie PCI DSS 8.1.7

**8.1.7** ustawić czas trwania blokady do co najmniej 30 minut lub dopóki administrator identyfikator użytkownika.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia, wymuszania i monitorowania zasad haseł, zgodne z wymaganiami PCI DSS.|



### <a name="pci-dss-requirement-818"></a>Wymaganie PCI DSS 8.1.8

**8.1.8** , gdy sesja ma bezczynny więcej niż 15 minut, wymaga ponownego uwierzytelnienia do ponownego aktywowania terminal lub sesji użytkownika.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do tworzenia, wymuszania i monitorowania zasad haseł, zgodne z wymaganiami PCI DSS.|



## <a name="pci-dss-requirement-82"></a>Wymaganie PCI DSS 8.2

**8.2** przypisywać unikatowy identyfikator, sprawdź oprócz zarządzania właściwe uwierzytelnienie użytkownika — konsument użytkowników i administratorów na wszystkich składników systemu przez zastosowanie co najmniej jeden z następujących metod uwierzytelniania wszystkich użytkowników:
- Coś, co wiesz, takie jak hasła lub hasło
- Coś masz, takie jak token urządzeń lub kart inteligentnych
- Coś się, takich jak biometryczne

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Implementacja magazynu sieci Web firmy Contoso w usłudze Multi-Factor authentication została wyłączona, aby zapewnić łatwość użycia dla pokaz. Uwierzytelnianie wieloskładnikowe można zaimplementować przy użyciu [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>Wymaganie PCI DSS 8.2.1

**8.2.1** przy użyciu silnej kryptografii renderowania wszystkie poświadczenia uwierzytelniania (np. hasła/wyrażenia) nie można go odczytać podczas transmisji i przechowywania na wszystkie składniki systemowe.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure opracowała procedury zarządzania kluczami zarządzać kluczy kryptograficznych w całym cyklu ich życia (np. generacji, dystrybucji odwołania). Microsoft Azure korzysta z firmowej infrastruktury PKI firmy Microsoft. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso wymusza stosowanie silnych haseł, udokumentowane w przewodniku wdrażania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>Wymaganie PCI DSS 8.2.2

**8.2.2** zweryfikować tożsamość użytkownika przed zmodyfikowaniem żadnego poświadczenia uwierzytelniania — na przykład wykonywania resetowanie haseł, inicjowania obsługi administracyjnej nowe tokeny lub generowania nowych kluczy.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure opracowała procedury zarządzania kluczami zarządzać kluczy kryptograficznych w całym cyklu ich życia (np. generacji, dystrybucji odwołania). Microsoft Azure korzysta z firmowej infrastruktury PKI firmy Microsoft. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso wymusza stosowanie silnych haseł, udokumentowane w przewodniku wdrażania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>Wymaganie PCI DSS 8.2.3

**8.2.3** haseł/hasła muszą spełniać następujące:
- Wymagaj minimalnej długości co najmniej 7 znaków.
- Zawierają zarówno liczbowych i alfabetyczne znaków.
Alternatywnie haseł/hasła musi mieć złożoność i siły równoważne parametry wymienione powyżej.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso wymusza stosowanie silnych haseł, udokumentowane w przewodniku wdrażania.|



### <a name="pci-dss-requirement-824"></a>Wymaganie PCI DSS 8.2.4

**8.2.4** zmiany hasła użytkownika/hasła co najmniej raz na 90 dni.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Active Directory do zarządzania użytkownikami. `-enableADDomainPasswordPolicy` Można ustawić opcji upewnij się, co najmniej raz na 90 dni wygaśnięcia hasła.|



### <a name="pci-dss-requirement-825"></a>Wymaganie PCI DSS 8.2.5

**8.2.5** nie zezwalaj użytkownikom przesłać nowe hasło, które, która jest taka sama jak dowolne cztery ostatnie haseł/hasła użytkownik został użyty.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso wymusza stosowanie silnych haseł, udokumentowane w przewodniku wdrażania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>Wymaganie PCI DSS 8.2.6

**8.2.6** ustawioną dla pierwszego użycia i na Resetowanie hasła/hasła unikatową wartość dla każdego użytkownika, a następnie zmień natychmiast po pierwszym użyciu.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso wymusza stosowanie silnych haseł, udokumentowane w przewodniku wdrażania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>Wymaganie PCI DSS 8.3

**8.3** zabezpieczenia wszystkich poszczególnych z konsoli dostępu administracyjnego i wszystkie dostępu zdalnego w środowisku danych posiadacza (CRP) przy użyciu usługi Multi-Factor authentication.

> [!NOTE]
> Uwierzytelnianie wieloskładnikowe wymaga, aby co najmniej dwa z trzech metod (patrz wymagania 8.2 opisy metod uwierzytelniania) używany do uwierzytelniania. Dwa razy (na przykład za pomocą dwóch oddzielnych haseł) przy użyciu jeden składnik nie jest uważana za usługi Multi-Factor authentication.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Administratorzy Azure są wymagane na potrzeby dostępu podczas wykonywania konserwacji i administracji do systemu Azure i serwery usługi Multi-Factor authentication. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web tworzy trzy konta podczas wdrażania: administrator, sqladmin i edna (domyślny użytkownik zalogowany do aplikacji sieci web podczas wykonywania demo). Uwierzytelnianie wieloskładnikowe nie został zaimplementowany dla pokaz.|



### <a name="pci-dss-requirement-831"></a>Wymaganie PCI DSS 8.3.1

**8.3.1** wprowadzają uwierzytelnianie wieloskładnikowe dla każdego dostępu z konsoli do CDE dla pracowników z dostępem administracyjnym.

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Administratorzy Azure są wymagane na potrzeby dostępu podczas wykonywania konserwacji i administracji do systemu Azure i serwery usługi Multi-Factor authentication. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web tworzy trzy konta podczas wdrażania: administrator, sqladmin i edna (domyślny użytkownik zalogowany do aplikacji sieci web podczas wykonywania demo). Uwierzytelnianie wieloskładnikowe nie został zaimplementowany dla pokaz.|



### <a name="pci-dss-requirement-832"></a>Wymaganie PCI DSS 8.3.2

**8.3.2** wprowadzają uwierzytelnianie wieloskładnikowe dla wszystkich dostępu zdalnego do sieci (zarówno użytkownika i administratora oraz tym innej firmy do uzyskiwania dostępu do pomocy technicznej lub konserwacji) pochodzący spoza sieci jednostki.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Administratorzy Azure są wymagane na potrzeby dostępu podczas wykonywania konserwacji i administracji do systemu Azure i serwery usługi Multi-Factor authentication. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web tworzy trzy konta podczas wdrażania: administrator, sqladmin i edna (domyślny użytkownik zalogowany do aplikacji sieci web podczas wykonywania demo). Uwierzytelnianie wieloskładnikowe nie został zaimplementowany dla pokaz.|



## <a name="pci-dss-requirement-84"></a>Wymaganie PCI DSS 8.4

**8.4** dokumentu i komunikować się zasady uwierzytelniania i procedury dla wszystkich użytkowników, w tym:
- Wskazówki dotyczące wybierania poświadczeń silnego uwierzytelniania
- Wskazówki dotyczące sposobu użytkowników należy chronić swoje poświadczenia uwierzytelniania
- Instrukcje, aby nie używać ponownie wcześniej utworzonych haseł
- Instrukcje dotyczące zmiany hasła w przypadku braku jakiegokolwiek podejrzenia hasło można złamać.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do następujących wytycznych i dokumentowanie i komunikacji procedury uwierzytelniania i zasady dla wszystkich użytkowników.|



## <a name="pci-dss-requirement-85"></a>Wymaganie PCI DSS 8.5

**8.5** nie używaj grupa, współużytkowanych lub identyfikatory ogólny, hasła lub innych metod uwierzytelniania, w następujący sposób:
- Identyfikatory ogólnego użytkownika są wyłączone lub usunięte.
- Do administrowania systemem i innych ważnych funkcji nie istnieją identyfikatorów użytkownika udostępnionego.
- Udostępnione i rodzajowy nazwy użytkownika nie są używane do administrowania żadnych składników systemu.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web tworzy trzy konta podczas wdrażania: administrator, sqladmin i edna (domyślny użytkownik zalogowany do aplikacji sieci web podczas wykonywania demo). Uwierzytelnianie wieloskładnikowe nie został zaimplementowany dla pokaz.|



### <a name="pci-dss-requirement-851"></a>Wymaganie PCI DSS 8.5.1

**8.5.1** **dodatkowych wymogów dotyczących tylko dostawców usług:** dostawców usługi Dostęp zdalny do lokalnego klienta (na przykład do obsługi systemów POS lub serwerów), należy użyć (poświadczenia uwierzytelniania unikatowy takie jak hasła/frazy) dla każdego klienta. 

> [!NOTE]
> To wymaganie nie jest przeznaczony do zastosowania do udostępnionego dostawców usług hostingu, uzyskiwanie dostępu do ich własnych środowisko macierzyste, gdzie hostowane są wiele środowisk klienta.

**Obowiązki:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy klientów Microsoft Azure. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy klientów Microsoft Azure.|



## <a name="pci-dss-requirement-86"></a>Wymaganie PCI DSS 8.6

**8.6** w przypadku innych mechanizmów uwierzytelniania (na przykład tokeny zabezpieczające fizyczne lub logiczne, karty inteligentne, certyfikaty, itp.), użyj tych mechanizmów można przypisać w następujący sposób:
- Mechanizmy uwierzytelniania musi być przypisany do indywidualnych kont i nie są współużytkowane przez wiele kont.
- Formanty fizycznych i/lub logiczne muszą być w celu zapewnienia, że tylko konta mogą używać mechanizmu w celu uzyskania dostępu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web tworzy trzy konta podczas wdrażania: administrator, sqladmin i edna (domyślny użytkownik zalogowany do aplikacji sieci web podczas wykonywania demo). Uwierzytelnianie wieloskładnikowe nie został zaimplementowany dla pokaz. Dostęp jest zarządzana za pośrednictwem [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/), która pomaga w zabezpieczaniu kluczy kryptograficznych i kluczy tajnych używanych przez usługi i aplikacje w chmurze. |



## <a name="pci-dss-requirement-87"></a>Wymaganie PCI DSS 8.7

**8.7** dostęp do dowolnej bazy danych zawierające dane posiadacza (w tym dostępu aplikacji, Administratorzy i innych użytkowników) ma następujące ograniczenia:
- Wszystkie dostęp użytkownika do, zapytań użytkownika i działań użytkownika w bazach danych są za pośrednictwem metod programistycznych.
- Tylko administratorzy bazy danych mają możliwość uzyskania bezpośredniego dostępu do lub zapytanie bazy danych.
- Identyfikatory aplikacji dla aplikacji baz danych można tylko przez aplikacje (a nie przez poszczególnych użytkowników lub inne procesy aplikacji z systemem innym niż).

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso chroni wszystkie dane posiadacza z usługi Azure Key Vault, a szyfrowanie rekordów jest opisane w dokumentacji wdrożenia. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>Wymaganie PCI DSS 8.8

**8.8** upewnij się, że zasady zabezpieczeń i procedury działania w celu ich identyfikacji i uwierzytelniania są udokumentowane w użyciu i znane wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do zapewnienia, że zasady zabezpieczeń i procedury działania w celu ich identyfikacji i uwierzytelniania są udokumentowane w użyciu oraz znane wszystkich zainteresowanych podmiotów.|





---
title: Plan Azure przetwarzania płatności — wymagania dotyczące zapory
description: PCI DSS wymaganie 1
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6f82c828b7cd878cc9ed9f42a4bf63d0d7aedb16
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące zapory dla środowisk standardem PCI DSS 
## <a name="pci-dss-requirement-1"></a>PCI DSS wymaganie 1

**Zainstalowanie i konserwowanie konfigurację zapory, aby chronić dane posiadacza**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Zaporach urządzeń, które kontrolują ruch komputera między sieciami jednostki (wewnętrzne) i niezaufanych sieci (zewnętrzne), jak również ruch do i z bardziej poufnego obszarów w ramach jednostki wewnętrzny zaufanej sieci. Środowisko danych posiadacza jest przykładem bardziej poufnego obszarze jednostki zaufanej sieci.
Zapora sprawdza, czy cały ruch sieciowy i blokuje tych transmisji nie spełniających kryteria określonego zabezpieczeń.
Wszystkie systemy muszą być chronione przed nieautoryzowanym dostępem z niezaufanych sieci, czy wejścia do systemu za pośrednictwem Internetu, jako handlu elektronicznego, pracownika dostęp do Internetu za pośrednictwem przeglądarek komputerowych, dostęp do poczty e-mail pracownika, dedykowanych połączeń, takich jak Business-to-business połączeń, za pośrednictwem sieci bezprzewodowych lub za pośrednictwem innych źródeł. Często pozornie nieznaczne ścieżki do i z niezaufanymi sieciami zapewniają niechronionych ścieżek do systemów klucza. Zapory są mechanizm ochrony klucza dla każdej sieci na komputerze.
Innymi składnikami systemu może udostępnić funkcji zapory tak długo, jak spełniają minimalne wymagania dotyczące zapór, zgodnie z definicją w wymaganie 1. W przypadku, gdy innymi składnikami systemu są używane w środowisku danych posiadacza umożliwiają korzystanie z funkcji zapory, te urządzenia musi być zawarty w zakresie i ocena wymaganie 1.

## <a name="pci-dss-requirement-11"></a>Wymaganie PCI DSS 1.1

**1.1** Ustanów wdrożenie zapory programowej i routera konfiguracji standardy i które obejmują następujące (zobacz 1.1.1 za pośrednictwem 1.1.7).


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Udostępnia magazynu sieci Web firmy Contoso zapory CDE z izolacją PaaS i implementacja interfejsu środowiska usługi aplikacji zapewnia CRP przychodzące i wychodzące danych jest chroniona.<br /><br />[Środowiska usługi aplikacji (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) jest planu usług Premium używane dla zachowania zgodności. Aby uzyskać więcej informacji na formanty ASE oraz konfiguracji, zobacz [wskazówki PCI - środowiska usługi aplikacji](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>Wymaganie PCI DSS 1.1.1

**1.1.1** formalny proces zatwierdzania i testowanie wszystkich połączeń sieciowych i zmian do konfiguracji zapory i router


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Wystąpienie magazynu sieci Web firmy Contoso ustanawia modelu elementu konfiguracji/CD DevOps za zapewnienie, że wszystkie zmiany są poprawnie zarządzane. Analiza dzienników zapewnia szczegółowe rejestrowanie zmian. Zmiany można przejrzeć i sprawdzić dokładność. Aby uzyskać bardziej szczegółowe wskazówki, zobacz [wskazówki PCI - usługi Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />[Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) zapewnia scentralizowane widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Jeden rzut oka należy sprawdzić, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowany i można szybko zidentyfikować wszystkie zasoby, które wymagają uwagi.|



### <a name="pci-dss-requirement-112"></a>Wymaganie PCI DSS 1.1.2

**1.1.2** bieżącego diagram sieciowy identyfikujący wszystkich połączeń między środowiskiem danych posiadacza i innych sieciach, w tym żadnej sieci bezprzewodowej

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Zajrzyj do dokumentacji architektury i projektu odwołanie magazynu sieci Web firmy Contoso w ramach instalacji wzorca rozwiązania.|



### <a name="pci-dss-requirement-113"></a>Wymaganie PCI DSS 1.1.3

**1.1.3** bieżącego diagramu, który zawiera wszystkie dane posiadacza przepływa między systemów i sieci

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Zobacz magazynu sieci Web firmy Contoso DPD i modelu zagrożeń.|



### <a name="pci-dss-requirement-114"></a>Wymaganie PCI DSS 1.1.4

**1.1.4** wymagania dotyczące zapory na każdym połączenie internetowe oraz między żadnych siecią obwodową i sieci wewnętrznej strefy

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure wykorzystuje granic ochrony urządzeń, takich jak bramy, listy kontroli dostępu w sieci i zapory aplikacji do obsługi komunikacji sterowania na granicach wewnętrznych i zewnętrznych na poziomie platformy. Klient skonfiguruje je do ich specyfikacjach i wymaganiach. Microsoft Azure filtruje komunikacji po wznowieniu na platformie. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso zapewnia sieć obwodową z izolacją PaaS i implementacja interfejsu środowiska usługi aplikacji zapewnia CRP przychodzące i wychodzące danych jest chroniona.<br /><br />[Środowiska usługi aplikacji (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) jest planu usług Premium używane dla zachowania zgodności. Aby uzyskać więcej informacji na formanty ASE oraz konfiguracji, zobacz [wskazówki PCI - środowiska usługi aplikacji](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>Wymaganie PCI DSS 1.1.5

**1.1.5** opis grup, ról i obowiązków zarządzania składników sieciowych

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Używa magazynu sieci Web firmy Contoso [based kontroli dostępu (RBAC)](/azure/role-based-access-control/role-assignments-portal) do izolowania ról użytkownika. RBAC umożliwia precyzyjne zarządzanie dostępem ukierunkowanych na platformie Azure. Konfiguracja istnieje dla subskrypcji dostępu i dostępu do usługi Azure Key Vault.|



### <a name="pci-dss-requirement-116"></a>Wymaganie PCI DSS 1.1.6

**1.1.6** dokumentację biznesowego wyjaśnienia i zatwierdzenie wszystkich usług, protokoły i porty dozwolone, łącznie z dokumentacją funkcje zabezpieczeń zaimplementowana dla nich uważane za niebezpieczne.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso otwiera tylko porty i protokoły wymagane w całej projektowania urzędu Rejestrowania. Szczegółowe informacje o przepływ danych są widoczne w modelu DPD i zagrożeń.|



### <a name="pci-dss-requirement-117"></a>Wymaganie PCI DSS 1.1.7

**1.1.7** wymaganie, aby przejrzeć reguły zapory programowej i routera ustawia co najmniej co sześć miesięcy

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | W Contoso Webstore zestawów reguł zapory są kontrolowane w celu zapewnienia, że żadne niepotrzebne lub nieużywane reguły są dołączone. Zgodnie z projektem pokaz jest wdrażana z najniższych uprawnień najmniejszy wpływ ścieżki.|



## <a name="pci-dss-requirement-12"></a>Wymaganie PCI DSS 1.2

**1.2** zapory programowej i routera konfiguracje, które ograniczają połączeń między niezaufanych sieci i wszystkie składniki systemowe w środowisku danych posiadacza kompilacji. 

> [!NOTE]
> "Niezaufanej sieci" jest żadną siecią, która może być zewnętrzne sieci należących do jednostki w ramach przeglądu i/lub która jest poza jednostki możliwość kontrolowania lub zarządzać.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | CRP Contoso Webstore jest zdefiniowany w dokumentacji urząd Rejestrowania i wdrażania. Niezaufanych sieci są odrzucane przez projekt.|



### <a name="pci-dss-requirement-121"></a>Wymaganie PCI DSS 1.2.1

**1.2.1** Ograniczanie ruchu przychodzącego i wychodzącego do tej, która jest niezbędna dla środowiska danych posiadacza i jawnie odmówiono wszystkich pozostałych rodzajów ruchu.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | CRP Contoso Webstore jest zdefiniowany w dokumentacji urząd Rejestrowania i wdrażania. Niezaufanych sieci są odrzucane przez projekt. Wersja demonstracyjna magazynu sieci Web firmy Contoso konfiguruje zaporę aplikacji Microsoft Azure, aby umożliwić tylko określonych zakresów adresów IP dostępu do usług Microsoft Azure. Magazynu sieci Web firmy Contoso zawiera Odmów wszelkie zapory na wszystkich granic CRP. Wszystkie konfiguracje odbywa się podczas początkowej konfiguracji wdrożenia.

> [!NOTE]
> Środowisko usługi aplikacji (ASE) służy w tym rozwiązaniu do izolacji CRP jednak jest niezbędne, że Twoje kwalifikowaną doświadczeniem zabezpieczeń (QSA) daje w wyniku tego rozwiązania, ponieważ ASE implementuje izolacji sieci obwodowej, który zezwala na połączenia wychodzące przez ASE. PCI-DSS wymaga wszystkich połączeń przychodzących i wychodzących, które nie są wymagane musi być zablokowany. Dla ASE działał poprawnie, ASE będzie nawiązywać wychodzące połączenia jako potrzebne zgodnie z definicją w ["Zagadnienia dotyczące sieci dla środowiska usługi App Service"](/azure/app-service/app-service-environment/network-info). Klienci powinni sprawdzić połączenia wychodzące z Twojej QSA przed wdrożeniem rozwiązania w środowisku produkcyjnym w celu zapewnienia, że będzie spełniać wymagania. |



### <a name="pci-dss-requirement-122"></a>Wymaganie PCI DSS 1.2.2

**1.2.2** bezpiecznego i synchronizować pliki konfiguracji routera.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web zawiera konfiguracje synchronizowane dla formantów natywnego sieci Microsoft Azure.|



### <a name="pci-dss-requirement-123"></a>Wymaganie PCI DSS 1.2.3

**1.2.3** zainstalować zapory między wszystkich sieci bezprzewodowej i środowiska danych posiadacza i skonfigurować te zapór, Odmów, lub jeśli ruch jest niezbędne do celów biznesowych zezwala na tylko autoryzowane ruchu między sieci bezprzewodowej środowisko i środowisko danych imię.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie ma żadnych rozwiązań sieci bezprzewodowej lub włączony.|



## <a name="pci-dss-requirement-13"></a>Wymaganie PCI DSS 1.3

**1.3** zakazać bezpośredniego dostępu publicznego między Internet i każdego składnika systemu w środowisku danych imię.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure wykorzystuje granic sieci i oparta na hoście ochrony urządzeń, takich jak zapory, moduły równoważenia obciążenia i listy kontroli dostępu. Te urządzenia używają mechanizmów, takich jak izolacji sieci VLAN, NAT i filtrowanie ruchu oddzielne klientów z Internetu i ruch związany z zarządzaniem pakietów. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso udostępnia w czasie wdrażania, konfiguracji zapory aplikacji Azure umożliwia tylko określonych zakresów adresów IP, aby uzyskać dostęp do witryny, Dołącz bastionu maszynach wirtualnych platformy Azure w ich CRP.|



### <a name="pci-dss-requirement-131"></a>Wymaganie PCI DSS 1.3.1

**1.3.1** zaimplementować sieć obwodową, aby ograniczyć ruch przychodzący tylko składników systemu, które zapewniają autoryzowanym usługom publicznie, protokoły i porty.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Implementacja magazynu sieci Web firmy Contoso jego sieć obwodowa gwarantuje, że tylko autoryzowanym usługom mogą łączyć się z CDE.|



### <a name="pci-dss-requirement-132"></a>Wymaganie PCI DSS 1.3.2

**1.3.2** limit ruchu przychodzącego ruchu internetowego do adresów IP w sieci obwodowej.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Implementacja magazynu sieci Web firmy Contoso jego sieć obwodowa gwarantuje, że tylko autoryzowanym usługom mogą łączyć się z CDE.|



### <a name="pci-dss-requirement-133"></a>Wymaganie PCI DSS 1.3.3

**1.3.3** wdrożenie środków ochrony przed fałszowaniem do wykrywania i blokowania sfałszowane adresy IP źródła przed wejściem do sieci. (Na przykład blokować ruchu pochodzącego z Internetu przy użyciu adresu źródłowego wewnętrznego).

**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje filtrowanie, aby zapobiec fałszywych ruchu i ograniczania ruchu przychodzącego i wychodzącego do TPM składników sieciowych. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-134"></a>Wymaganie PCI DSS 1.3.4

**1.3.4** uniemożliwia nieautoryzowany ruch wychodzący z posiadacza środowiska dane z Internetem.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Architektura magazynu sieci Web firmy Contoso uniemożliwia nieautoryzowany ruch wychodzący z poziomu środowiska w zakresie z Internetem. Jest to osiągane przez konfigurowanie ruchu wychodzącego listy ACL dla zatwierdzonych portów i protokołów w Microsoft Azure. Należą do nich dostęp do CDE w bazie danych programu SQL Server. <br /><br />Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>Wymaganie PCI DSS 1.3.5

**1.3.5** zezwolenia tylko "ustanowić" połączeń do sieci.


**Obowiązki:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementuje filtrowanie, aby zapobiec fałszywych ruchu i ograniczania ruchu przychodzącego i wychodzącego do TPM składników sieciowych. Sieci Microsoft Azure jest rozdzielone do rozdzielania ruchu klientów z ruch związany z zarządzaniem. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy.|



### <a name="pci-dss-requirement-136"></a>Wymaganie PCI DSS 1.3.6

**1.3.6** składników systemu miejscu przechowujących dane posiadacza (na przykład w bazie danych) w strefie sieci wewnętrznej, rozdzielone w sieci obwodowej i innych niezaufanych sieci.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure używa podział sieci i translacji adresów Sieciowych do rozdzielania ruchu klientów z ruch związany z zarządzaniem. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Architektura magazynu sieci Web firmy Contoso uniemożliwia nieautoryzowany ruch wychodzący z poziomu środowiska w zakresie z Internetem. Jest to osiągane przez konfigurowanie ruchu wychodzącego listy ACL dla zatwierdzonych portów i protokołów w Microsoft Azure. Należą do nich dostęp do CDE w bazie danych programu SQL Server. <br /><br />Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>Wymaganie PCI DSS 1.3.7

**1.3.7** nie ujawniają prywatnych adresów IP i informacji o routingu dla osób niepowołanych. 

> [!NOTE]
> Metody przesłonić adresy IP mogą obejmować, ale nie są ograniczone do:
> - Translator adresów sieciowych (NAT).
> - Umieszczenie serwerów zawierających dane posiadacza serwerów proxy/zapory.
> - Usunięcie lub filtrowanie anonsów tras dla sieci prywatnych, korzystających z zarejestrowanych adresowania.
> - Użycie wewnętrznego przestrzeni adresowej RFC1918 zamiast adresy zarejestrowane.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure używa translatora adresów sieciowych (NAT) i podział sieci do rozdzielania ruchu klientów z ruch związany z zarządzaniem. Urządzenia platformy Azure są identyfikowane przez ich UUID i uwierzytelniania przy użyciu protokołu Kerberos. Azure zarządzane sieci, które urządzenia są identyfikowane za pomocą IP RFC 1918 problemu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso umieszcza wszystkie dane posiadacza serwerów proxy/zapory i przestrzeń adresową RFC1918 jest używane wewnętrznie.|



## <a name="pci-dss-requirement-14"></a>Wymaganie PCI DSS 1.4

**1.4** zainstalować zapory osobistej funkcji oprogramowania lub równoważnej na dowolnym przenośnych urządzeń komputerowych (łącznie z firmy i/lub należące do pracowników) który połączyć się z Internetem, gdy komputer znajduje się poza siecią (na przykład komputery przenośne używane przez pracowników), a które są również używane do uzyskania dostępu CDE. Konfiguracje zapory (lub równoważnego) obejmują:-zdefiniowano określonych ustawień konfiguracji.
- Aktywnie działa osobiste zapory (lub równoważne funkcje).
- Osobiste zapory (lub równoważne funkcje) nie jest może przez użytkowników urządzeń przenośnych obliczeniowych.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie zapewnia ochrony użytkownik końcowy urządzenia. [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) może służyć do zarządzania urządzeniami przenośnymi pracowników używa dostępu do danych firmowych.|



## <a name="pci-dss-requirement-15"></a>Wymaganie PCI DSS 1.5

**1.5** upewnij się, że zasady zabezpieczeń i procedur operacyjnych zarządzania zapory są udokumentowane w użyciu i znane wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso udostępnia w czasie wdrażania, konfiguracji zapory aplikacji Azure umożliwia tylko określonych zakresów adresów IP, aby uzyskać dostęp do witryny, Dołącz bastionu maszynach wirtualnych platformy Azure w ich CRP.|





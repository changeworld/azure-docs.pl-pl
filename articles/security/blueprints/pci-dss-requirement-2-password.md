---
title: Plan Azure przetwarzania płatności — wymagania dotyczące hasła
description: PCI DSS wymaganie 2
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3ca83294702a138713f31e34b0e94b1ccb87be0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące hasła dla środowisk standardem PCI DSS 
## <a name="pci-dss-requirement-2"></a>PCI DSS wymaganie 2

**Nie używaj dostarczonego przez dostawcę wartości domyślnych dla systemu hasła i inne parametry zabezpieczeń**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Złośliwe osoby (zewnętrzne i wewnętrzne na jednostkę) często używane do naruszenia bezpieczeństwa systemów dostawcy domyślnego hasła i inne ustawienia domyślnego dostawcy. Te hasła i ustawienia są dobrze znane przez hakera społeczności i łatwo są określane za pomocą informacje publiczne.

## <a name="pci-dss-requirement-21"></a>Wymaganie PCI DSS 2.1
 
**2.1** zawsze zmienić dostarczonego przez dostawcę wartości domyślne i usunąć lub wyłączyć niepotrzebne domyślne konta **przed** instalowania systemu w sieci.
Dotyczy to wszystkich hasła domyślny, w tym między innymi do tych używanych w systemach operacyjnych, oprogramowania, która zapewnia usługi zabezpieczeń, aplikacji i systemu kont, terminale punktach sprzedaży (POS), społeczności zarządzania protokołu SNMP (Simple Network) ciągi, itp.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Wymagania dotyczące zasad haseł Microsoft Azure Active Directory są wymuszane dla nowych haseł dostarczonych przez klientów w portalu AADUX. Zmiany hasła samoobsługi inicjowane przez klienta wymaga weryfikacji poprzednich haseł. Administrator resetowania hasła jest wymagane można zmienić na kolejne logowania. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso wymaga od użytkowników użyć zestawu silnych haseł dla wszystkich użytkowników. Brak kont przykładowa lub Gość są włączone w pokaz.<br /><br />W rozwiązaniu nie zostały zaimplementowane sieci bezprzewodowej i SNMP.|



### <a name="pci-dss-requirement-211"></a>Wymaganie PCI DSS 2.1.1

**2.1.1** w środowiskach sieci bezprzewodowej, podłączone do środowiska danych posiadacza lub przekazywania danych posiadacza, Zmień wszystkie wartości domyślne dostawcy sieci bezprzewodowej instalacji, w tym między innymi klucze szyfrowania bezprzewodowej domyślne, hasła, a Ciągi identyfikacyjny protokołu SNMP.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | W rozwiązaniu nie zostały zaimplementowane sieci bezprzewodowej i SNMP.|



## <a name="pci-dss-requirement-22"></a>Wymaganie PCI DSS 2.2

**2.2** opracowanie standardami konfiguracji wszystkich składników systemu. Mieć pewność, że tymi standardami adresów wszystkie znane luki w zabezpieczeniach i są zgodne z systemu zaakceptowane branży ograniczenie funkcjonalności standardów.
Źródeł systemu zaakceptowane branży ograniczenie funkcjonalności standardów może obejmować, ale nie są ograniczone do:
- Centrum zabezpieczeń Internetu (ci)
- Międzynarodowej Organizacji Normalizacyjnej (ISO)
- Administratora systemu inspekcji sieci (SAN) Instytut
- National Institute of Standards technologii (NIST)

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zespołu OSSC technicznej zabezpieczeń rozwija standardami konfiguracji zabezpieczeń dla systemów w środowisku Microsoft Azure, które są zgodne z branży akceptowane przez ograniczenie funkcjonalności standardów. Te konfiguracje są udokumentowane w linii bazowych systemu i istotne zmiany są przekazywane do ryzyko zespoły (np. zespół IPAK). Procedury są wdrażane do monitorowania zgodności ze standardami konfiguracji zabezpieczeń. Standardami konfiguracji zabezpieczeń dla systemów w środowisku Microsoft Azure są zgodne ze standardami zaakceptowane branży ograniczania funkcjonalności i są kontrolowane w co najmniej raz. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web udostępnia ograniczania funkcjonalności wszystkich usług w zakresie środowiska danych posiadacza (CRP). <br /><br />Ponadto wdraża magazynu sieci Web firmy Contoso [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/), który zapewnia scentralizowane widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Jeden rzut oka należy sprawdzić, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowany i można szybko zidentyfikować wszystkie zasoby, które wymagają uwagi.<br /><br />Magazynu sieci Web firmy Contoso korzysta z usługi Operations Management Suite do rejestrowania wszystkich zmian w systemie. Analiza dzienników zapewnia szczegółowe rejestrowanie zmian. Zmiany można przejrzeć i sprawdzić dokładność. Aby uzyskać bardziej szczegółowe wskazówki, zobacz [wskazówki PCI - usługi Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-221"></a>Wymaganie PCI DSS 2.2.1

**2.2.1** wdrożenie tylko jednej funkcji podstawowego na serwer, aby zapobiec funkcji, które wymagają różnych poziomów ochrony z tej samej istniejących na tym samym serwerze. (Na przykład serwerów sieci web, serwerów baz danych i DNS powinny zostać wdrożone na różnych serwerach.) 

> [!NOTE]
> Technologii wirtualizacji w przypadku użycia, wdrożenie tylko jednej funkcji głównej na składnik systemu wirtualnego.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Usługi magazynu sieci Web firmy Contoso są wdrażane jako PaaS usług. Wszystkie usługi są izolowane oraz segmentowanych zastosowanie segmentacji sieci.<br /><br />Także używa magazynu sieci Web firmy Contoso [środowiska usługi aplikacji (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) do rozwiązania kluczy wymuszania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - środowiska usługi aplikacji](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-222"></a>Wymaganie PCI DSS 2.2.2

**2.2.2** włączyć tylko niezbędne usługi, protokołów, demonów itp., co jest wymagane dla funkcji systemu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure konfiguracjach sprzętu i oprogramowania zostaną zweryfikowane co najmniej raz na kwartał do identyfikowania i usunąć niepotrzebne funkcje, portów, protokołów i usług. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Usługi magazynu sieci Web firmy Contoso są wdrażane jako PaaS usług. Wszystkie usługi są izolowane oraz segmentowanych zastosowanie segmentacji sieci.<br /><br />Także używa magazynu sieci Web firmy Contoso [środowiska usługi aplikacji (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) do rozwiązania kluczy wymuszania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - środowiska usługi aplikacji](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-223"></a>Wymaganie PCI DSS 2.2.3

**2.2.3** wdrożyć dodatkowe funkcje zabezpieczeń wszystkie wymagane usługi, protokoły lub demonów, które są uważane za niebezpieczne. 

> [!NOTE]
> W przypadku protokołu TLS/SSL wczesne wymagania w dodatku A2 muszą być wypełnione.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Usługi magazynu sieci Web firmy Contoso są wdrażane jako PaaS usług. Wszystkie usługi są izolowane oraz segmentowanych zastosowanie segmentacji sieci. Wdrożenie zawiera również Ograniczanie funkcjonalności wszystkich usług w zakresie CDE. <br /><br />Ponadto wdraża magazynu sieci Web firmy Contoso [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/), który zapewnia scentralizowane widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Jeden rzut oka należy sprawdzić, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowany i można szybko zidentyfikować wszystkie zasoby, które wymagają uwagi.<br /><br />Także używa magazynu sieci Web firmy Contoso [środowiska usługi aplikacji (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) do rozwiązania kluczy wymuszania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - środowiska usługi aplikacji](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-224"></a>Wymaganie PCI DSS 2.2.4

**2.2.4** konfigurować parametry zabezpieczeń systemu, aby uniemożliwić nadużycia.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure gwarantuje, że tylko do autoryzowanego personelu będą mogli jej konfigurować opcje zabezpieczeń platformy Azure, przy użyciu kontroli dostępu w usłudze Multi-Factor i potrzeby biznesowe udokumentowane. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso korzysta z usługi AAD i RBAC AD do zarządzania parametrami zabezpieczeń są poprawnie wdrożone. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-225"></a>Wymaganie PCI DSS 2.2.5

**2.2.5** usunąć wszystkie niepotrzebne funkcje, takie jak skrypty, sterowniki, funkcje, podsystemów, systemy plików i serwerów sieci web niepotrzebne.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Jak granice są ustalane magazynu sieci Web firmy Contoso zawiera dokumentację. Model zagrożeń firmy Contoso i diagram przepływu danych ilustrują wszystkich usług używanych i formanty włączone.|



## <a name="pci-dss-requirement-23"></a>Wymaganie PCI DSS 2.3

**2.3** zaszyfrować cały dostęp administracyjny konsoli z systemem innym niż przy użyciu silnej kryptografii. 

> [!NOTE]
> W przypadku protokołu TLS/SSL wczesne wymagania w dodatku A2 muszą być wypełnione.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zapewnia, że korzystać z silnej kryptografii jest wymuszana podczas uzyskiwania dostępu do infrastruktury funkcji hypervisor. Microsoft Azure gwarantuje również, że klientów za pomocą portalu zarządzania Microsoft Azure mogą uzyskiwać dostęp ich konsoli usługi/IaaS z silnej kryptografii. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso pokazuje, jak silne hasła można zaimplementować w rozwiązaniu; Ponadto do zweryfikowania, że szyfrowanie jest zaimplementowana w całym rozwiązaniu można wykonać wszystkie testy.<br /><br />Także używa magazynu sieci Web firmy Contoso [środowiska usługi aplikacji (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) do rozwiązania kluczy wymuszania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - środowiska usługi aplikacji](payment-processing-blueprint.md#app-service-environment).|



## <a name="pci-dss-requirement-24"></a>Wymaganie PCI DSS 2.4

**2.4** Prowadź rejestr składników systemu, które znajdują się w zakresie PCI DSS.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | W dokumentacji podana można wyświetlić spis rozwiązanie typu PaaS pokaz magazynu sieci Web firmy Contoso. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rozwiązania do zarządzania preinstalowany](payment-processing-blueprint.md#management-solutions).|



## <a name="pci-dss-requirement-25"></a>Wymaganie PCI DSS 2.5

**2.5** upewnij się, że zasady zabezpieczeń i procedury działania zarządzania dostawcy wartości domyślne i inne parametry zabezpieczeń są udokumentowane w użyciu i znanych wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Dokumentacji, która zapewnia wgląd w parametry zabezpieczeń zawiera magazynu sieci Web firmy Contoso i dokumentów usługi elementów. |



## <a name="pci-dss-requirement-26"></a>Wymaganie PCI DSS 2.6

**2.6** dostawców usług hostingu udostępnione musi ochrony hostowanej danych środowiska i posiadacza każdej jednostki. Ci dostawcy muszą spełniać określone wymagania zgodnie z opisem w *dodatek A: dodatkowe PCI DSS wymagania dotyczące dostawców hostingu udostępnionych.*

**Obowiązki:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. Microsoft Azure nie jest udostępnionej przez dostawcę hostingu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Nie dotyczy. Microsoft Azure nie jest udostępnionej przez dostawcę hostingu.|





---
title: Azure plan przetwarzania płatności — wymagania dotyczące monitorowania
description: Wymaganie PCI DSS 10
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3d26d31c592c34d1e51c3e2c2d6501edefa8dce
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące monitorowania dla środowisk standardem PCI DSS 
## <a name="pci-dss-requirement-10"></a>Wymaganie PCI DSS 10

**Śledzenie i monitorowanie dostęp do zasobów sieciowych i dane posiadacza**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Mechanizmy rejestrowania i możliwość śledzenia działań użytkownika są niezbędne w zapobieganie, wykrywanie lub zmniejszenia wpływu naruszenia zabezpieczeń danych. Obecność dzienniki we wszystkich środowiskach zezwala dokładnego śledzenia, alerty i analizy, gdy coś się problemów. Ustalania przyczyny naruszenia zabezpieczeń jest bardzo trudne, jeśli jest to niemożliwe, bez Dzienniki aktywności systemu.

## <a name="pci-dss-requirement-101"></a>Wymaganie PCI DSS 10.1

**10.1** zapisy inspekcji wdrożenie do odesłania dostęp do składników systemowych dla każdego użytkownika.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ogranicza dostęp do narzędzi administracyjnych i diagnostycznych do autoryzowanego personelu odpowiada odpowiedniego zadania. Microsoft Azure ogranicza uprzywilejowany dostęp do narzędzia używane w środowisku produkcyjnym, oparta na zasadach uprawnienia co najmniej. Microsoft Azure rejestruje i przechowuje dziennik użytkownikowi dostęp do składników systemu Microsoft Azure w środowisku platformy.<br /><br />Składniki platformy Microsoft Azure (w tym systemu operacyjnego, CloudNet sieci szkieletowej i tak dalej) są skonfigurowane do logowania i zbieranie zdarzeń zabezpieczeń. Działania administratora na platformie Microsoft Azure jest rejestrowane. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso ma szczegółowe rejestrowanie wszystkich systemu i aktywność użytkowników (w tym CHD rejestrowanie). Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rejestrowanie](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>Wymaganie PCI DSS 10.2

**10.2** wdrożenie automatycznego wykonywania audytu dla wszystkich składników systemu odtworzyć następujące zdarzenia:
- **10.2.1** pełny dostęp użytkownika do posiadacza danych
- **10.2.2** wszystkie akcje wykonywane przez osobę z katalogu głównego lub uprawnienia administracyjne
- **10.2.3** dostęp do wszystkich zapisy inspekcji
- **10.2.4** prób nieprawidłowy logicznego dostępu
- **10.2.5** użycie i zmiany do identyfikacji i uwierzytelniania mechanizmów — między innymi, ale nie jest ograniczona do tworzenia nowych kont i podniesienia uprawnień — i wszystkie zmiany, dodawania ani usuwania kont z certyfikatem głównym lub administracyjnych uprawnienia
- **10.2.6** inicjowania, zatrzymywanie oraz wstrzymywanie dzienników inspekcji
- **10.2.7** tworzenie i usuwanie obiektów na poziomie systemu

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ogranicza dostęp do narzędzi administracyjnych i diagnostycznych do autoryzowanego personelu odpowiada odpowiedniego zadania. Microsoft Azure ogranicza uprzywilejowany dostęp do narzędzia używane w środowisku produkcyjnym, oparta na zasadach uprawnienia co najmniej. Microsoft Azure rejestruje i przechowuje dziennik użytkownikowi dostęp do składników systemu Microsoft Azure w środowisku platformy.<br /><br />Składniki platformy Microsoft Azure (w tym systemu operacyjnego, CloudNet sieci szkieletowej i tak dalej) są skonfigurowane do logowania i zbieranie zdarzeń zabezpieczeń. Działania administratora na platformie Microsoft Azure jest rejestrowane. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso ma szczegółowe rejestrowanie całą aktywność systemu i użytkownika, w tym CHD rejestrowania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rejestrowanie](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>Wymaganie PCI DSS 10.3

**10.3** zarejestrować co najmniej następujące wpisów dziennika inspekcji dla wszystkich składników systemowych dla każdego zdarzenia:
- **10.3.1** identyfikacji użytkownika
- **10.3.2** typu zdarzenia
- **10.3.3** daty i godziny
- **10.3.4** wskazania powodzenia lub niepowodzenia
- **10.3.5** utworzenia zdarzenia
- **10.3.6** tożsamości lub nazwę zasobu dane były lub składnik systemu

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure opracowała procedury, aby zsynchronizować serwerów i urządzeń sieciowych w środowisku Microsoft Azure z serwerów czasu NTP warstwy 1 zsynchronizowane do globalnego pozycjonowanie systemu GPS satelity. Synchronizacja jest realizowane automatycznie co pięć minut. Microsoft Azure jest odpowiedzialny za egzekwowanie hosty usługi prawidłowo czas synchronizacji. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso rejestruje identyfikacji użytkownika, typu zdarzenia, sygnatury czasowej, zdarzeń pomyślnego nie powiodło się, źródło zdarzeń i nazwa zasobu, co jest wymagane przez 10.3 kontroli.|



## <a name="pci-dss-requirement-104"></a>Wymaganie PCI DSS 10.4

**10.4** przy użyciu technologii synchronizację czasu synchronizować wszystkie zegarami systemowymi krytyczne i godziny i upewnij się, wykonanie następujących nabywania, dystrybucji i czasu przechowywania. 
> [!NOTE]
> Przykładem technologii synchronizacja czasu jest protokołu NTP (Network Time).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure opracowała procedury, aby zsynchronizować serwerów i urządzeń sieciowych w środowisku Microsoft Azure z serwerów czasu NTP warstwy 1 zsynchronizowane do globalnego pozycjonowanie systemu GPS satelity. Synchronizacja jest realizowane automatycznie co pięć minut. Microsoft Azure jest odpowiedzialny za egzekwowanie hosty usługi prawidłowo czas synchronizacji. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Synchronizacja czasu usługi PaaS jest wykonywane przez platformę Azure.|



### <a name="pci-dss-requirement-1041"></a>Wymaganie PCI DSS 10.4.1

**10.4.1** krytyczne systemy mają poprawny i spójne czasu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.4 wymaganie](#pci-dss-requirement-10-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Synchronizacja czasu usługi PaaS jest wykonywane przez platformę Azure.|



### <a name="pci-dss-requirement-1042"></a>Wymaganie PCI DSS 10.4.2

**10.4.2** czasu dane są chronione.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.4 wymaganie](#pci-dss-requirement-10-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Synchronizacja czasu usługi PaaS jest wykonywane przez platformę Azure.|



### <a name="pci-dss-requirement-1043"></a>Wymaganie PCI DSS 10.4.3

**10.4.3** ustawienia czasu są odbierane z źródeł zaakceptowane branży czasu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.4 wymaganie](#pci-dss-requirement-10-4). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Synchronizacja czasu usługi PaaS jest wykonywane przez platformę Azure.|



## <a name="pci-dss-requirement-105"></a>Wymaganie PCI DSS 10.5

**10.5** bezpiecznego inspekcji śladów, więc nie można zmienić.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM i narzędzia Identyfikatory są implementowane w środowisku Microsoft Azure. Microsoft Azure wykorzystuje EWS umożliwiających analizę w czasie rzeczywistym zdarzeń w jego środowisku operacyjnym. Niemal w czasie rzeczywistym alertów dotyczących zdarzeń, które potencjalnie może naruszyć bezpieczeństwo systemu generowane mAs i cele. <br /><br />Rejestrowanie usługi, użytkownika i zabezpieczeń zdarzeń (dzienniki serwera sieci web, dzienniki serwera FTP itd.) jest włączona i przechowywane w centralnie. Azure ogranicza dostęp do dziennika inspekcji w celu autoryzowanego personelu w oparciu o obowiązków. Dzienniki zdarzeń zostaną zarchiwizowane w infrastrukturze Azure archiwizacji bezpiecznego i są przechowywane przez 180 dni. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Udostępnia magazynu sieci Web firmy Contoso inspekcji wszystkich elementów na platformie Azure. Wykonywanie kopii zapasowych na zewnętrzne źródło może być wykonane przez [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>Wymaganie PCI DSS 10.5.1

**10.5.1** limit wyświetlania inspekcji śladów do tych potrzeb związanych z pracą.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.5 wymaganie](#pci-dss-requirement-10-5). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Udostępnia magazynu sieci Web firmy Contoso inspekcji wszystkich elementów na platformie Azure. Wykonywanie kopii zapasowych na zewnętrzne źródło może być wykonane przez [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>Wymaganie PCI DSS 10.5.2

**10.5.2** Chroń pliki za dziennik inspekcji z nieautoryzowanym modyfikacjom.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.5 wymaganie](#pci-dss-requirement-10-5). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Udostępnia magazynu sieci Web firmy Contoso inspekcji wszystkich elementów na platformie Azure. Wykonywanie kopii zapasowych na zewnętrzne źródło może być wykonane przez [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>Wymaganie PCI DSS 10.5.3

**10.5.3** natychmiast wykonać kopię zapasową plików dziennika inspekcji serwera scentralizowane dziennika lub nośnika, który jest trudne do zmiany.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.5 wymaganie](#pci-dss-requirement-10-5). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Udostępnia magazynu sieci Web firmy Contoso inspekcji wszystkich elementów na platformie Azure. Wykonywanie kopii zapasowych na zewnętrzne źródło może być wykonane przez [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>Wymaganie PCI DSS 10.5.4

**10.5.4** zapisywanie dzienników dla technologii dołączonej do Internetu do bezpiecznego, scentralizowane, wewnętrzny dziennika serwera lub urządzenia multimedialnego.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.5 wymaganie](#pci-dss-requirement-10-5). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Udostępnia magazynu sieci Web firmy Contoso inspekcji wszystkich elementów na platformie Azure. Wykonywanie kopii zapasowych na zewnętrzne źródło może być wykonane przez [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>Wymaganie PCI DSS 10.5.5

**10.5.5** integralność pliku monitorowania lub wykrywania zmian do oprogramowanie na dzienników upewnij się, że istniejące dane dziennika nie można zmienić bez generowania alertów (chociaż nowych danych dodawane nie powinno powodować alertu).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.5 wymaganie](#pci-dss-requirement-10-5). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Udostępnia magazynu sieci Web firmy Contoso inspekcji wszystkich elementów na platformie Azure. Wykonywanie kopii zapasowych na zewnętrzne źródło może być wykonane przez [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>Wymaganie PCI DSS 10.6

**10.6** Przejrzyj dzienniki i zdarzeń zabezpieczeń dla wszystkie składniki systemowe zidentyfikować podejrzane działanie lub nieprawidłowości.
 
> [!NOTE]
> Dziennik zbierania, analizy i alerty, mogą być używane narzędzia, aby spełnić to wymaganie.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM i narzędzia Identyfikatory są implementowane w środowisku Microsoft Azure. Microsoft Azure wykorzystuje EWS umożliwiających analizę w czasie rzeczywistym zdarzeń w jego środowisku operacyjnym. Niemal w czasie rzeczywistym alertów dotyczących zdarzeń, które potencjalnie może naruszyć bezpieczeństwo systemu generowane mAs i cele. <br /><br />Rejestrowanie usługi, użytkownika i zabezpieczeń zdarzeń (dzienniki serwera sieci web, dzienniki serwera FTP itd.) jest włączona i przechowywane w centralnie. Azure ogranicza dostęp do dziennika inspekcji w celu autoryzowanego personelu w oparciu o obowiązków. Dzienniki zdarzeń zostaną zarchiwizowane w infrastrukturze Azure archiwizacji bezpiecznego i są przechowywane przez 180 dni. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Używa magazynu sieci Web firmy Contoso [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) do monitorowania, raport i zapobiec anomalii. [Klasyfikator Azure](/azure/advisor/advisor-security-recommendations) zapewnia spójne, skonsolidowanego widoku zaleceń dla wszystkich zasobów na platformie Azure.|



### <a name="pci-dss-requirement-1061"></a>Wymaganie PCI DSS 10.6.1

**10.6.1** co najmniej raz dziennie sprawdź następujące kwestie:
- Wszystkie zdarzenia zabezpieczeń
- Dzienniki wszystkie składniki systemowe, które przechowywania, przetwarzania lub przesyłania CHD i/lub SAD
- Dzienniki wszystkich składników systemu
- Dzienniki wszystkich serwerów i składników systemu, które wykonują funkcje zabezpieczeń (na przykład zapory, systemów systemów/nieautoryzowanego dostępu zapobiegania wykrywania nieautoryzowanego dostępu (Identyfikatory/adresów IP), serwery uwierzytelniania, serwery przekierowania handlu elektronicznego i tak dalej).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.6 wymaganie](#pci-dss-requirement-10-6). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Używa magazynu sieci Web firmy Contoso [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) do monitorowania, raport i zapobiec anomalii. [Klasyfikator Azure](/azure/advisor/advisor-security-recommendations) zapewnia spójne, skonsolidowanego widoku zaleceń dla wszystkich zasobów na platformie Azure.|



### <a name="pci-dss-requirement-1062"></a>Wymaganie PCI DSS 10.6.2

**10.6.2** Przejrzyj dzienniki innymi składnikami systemu okresowo na podstawie w organizacji zasad i ryzyka strategii zarządzania, zgodnie z ustaleniami roczne oceny ryzyka organizacji.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.6 wymaganie](#pci-dss-requirement-10-6). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Używa magazynu sieci Web firmy Contoso [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) do monitorowania, raport i zapobiec anomalii. [Klasyfikator Azure](/azure/advisor/advisor-security-recommendations) zapewnia spójne, skonsolidowanego widoku zaleceń dla wszystkich zasobów na platformie Azure.|



### <a name="pci-dss-requirement-1063"></a>Wymaganie PCI DSS 10.6.3

**10.6.3** uzupełniającego wyjątki i anomalie zidentyfikowanego w trakcie procesu przeglądu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zobacz sekcję "Microsoft Azure" dla [10.6 wymaganie](#pci-dss-requirement-10-6). |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Używa magazynu sieci Web firmy Contoso [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) do monitorowania, raport i zapobiec anomalii. [Klasyfikator Azure](/azure/advisor/advisor-security-recommendations) zapewnia spójne, skonsolidowanego widoku zaleceń dla wszystkich zasobów na platformie Azure.|



## <a name="pci-dss-requirement-107"></a>Wymaganie PCI DSS 10,7

**10,7** Zachowaj historię dziennik inspekcji dla co najmniej jednego roku z co najmniej trzech miesięcy od razu dostępne do analizy (na przykład w trybie online, zarchiwizowane lub umożliwiająca przywrócenie z kopii zapasowej).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zachowuje dzienniki inspekcji przez jeden rok z ostatnich miesięcy 3 natychmiast dostępna za pośrednictwem ich wewnętrzny portalu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso ma szczegółowe rejestrowanie wszystkich systemu i aktywność użytkowników (w tym CHD rejestrowanie). Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rejestrowanie](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>Wymaganie PCI DSS 10.8

**10.8** **dodatkowych wymogów dotyczących tylko dostawców usług:** należy wdrożyć proces w celu szybkiego wykrycia i raportowania błędów krytycznych systemów kontroli, w tym między innymi awarii:
- Zapory
- IDENTYFIKATORY/ADRESÓW IP
- FIM
- Oprogramowanie antywirusowe
- Fizyczne metody kontroli dostępu
- Formanty logicznego dostępu
- Mechanizmy rejestrowania inspekcji
- Formanty segmentacji (jeśli jest używany) 

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.



**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure wykorzystuje EWS umożliwiających analizę w czasie rzeczywistym zdarzeń w jego środowisku operacyjnym. Niemal w czasie rzeczywistym alertów dotyczących zdarzeń, które potencjalnie może naruszyć bezpieczeństwo systemu generowane mAs i cele. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso ma szczegółowe rejestrowanie wszystkich systemu i aktywność użytkowników (w tym CHD rejestrowanie). Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rejestrowanie](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>Wymaganie PCI DSS 10.8.1

**10.8.1** **dodatkowych wymogów dotyczących tylko dostawców usług:** odpowiadanie na awarie kontroli krytycznych w odpowiednim czasie. Procesy w odpowiedzi na błędy w kontrolkach zabezpieczeń musi zawierać:
- Przywracanie funkcje zabezpieczeń
- Identyfikowanie i dokumentowanie czas trwania (daty i godziny początku do końca) błędu zabezpieczeń
- Identyfikowanie i dokumentowanie przyczyny(yn) awarii, w tym główną przyczynę i dokumentowanie korygowania wymagane do adresu głównej przyczyny
- Identyfikowanie i adresowanie wystąpiły problemy z zabezpieczeniami, które powstały podczas awarii
- Zostanie wykonana oceny ryzyka, aby określić, czy są wymagane w wyniku awarii zabezpieczeń dalsze akcje
- Wdrożenie kontroli, aby zapobiec przyczynę błędu na podstawie pojawiał - wznawianie monitorowania kontroli zabezpieczeń 

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure wykorzystuje EWS umożliwiających analizę w czasie rzeczywistym zdarzeń w jego środowisku operacyjnym. Niemal w czasie rzeczywistym alertów dotyczących zdarzeń, które potencjalnie może naruszyć bezpieczeństwo systemu generowane mAs i cele. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso ma szczegółowe rejestrowanie wszystkich systemu i aktywność użytkowników (w tym CHD rejestrowanie). Aby uzyskać więcej informacji, zobacz [wskazówki PCI - rejestrowanie](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>Wymaganie PCI DSS 10.9

**10.9** upewnij się, że zasady zabezpieczeń i operacyjne procedury monitorowania dostęp do zasobów sieciowych i dane posiadacza są udokumentowane w użyciu i znanych wszystkich zainteresowanych podmiotów.


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web udostępnia przypadek użycia i opis sposób zarządzania i chronione CHD.|





---
title: Plan Azure przetwarzania płatności — wymagania dotyczące dostępu
description: Wymaganie PCI DSS 7
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: fb16a7973022421525e13313f53f615120caa38a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące dostępu dla środowisk standardem PCI DSS 
## <a name="pci-dss-requirement-7"></a>Wymaganie PCI DSS 7

**Ograniczanie dostępu do danych posiadacza według potrzeb biznesowych należy wiedzieć**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Aby upewnić się, że kluczowe dane są dostępne wyłącznie przez autoryzowanego personelu, systemy i procesy musi być w celu ograniczenia dostępu na podstawie musi znać i zgodnie z obowiązków.

"Musi znać" jest w przypadku prawa dostępu są przyznawane tylko minimalnej ilości danych i uprawnień niezbędnych do wykonywania zadania.

## <a name="pci-dss-requirement-71"></a>Wymaganie PCI DSS 7.1

**7.1** ograniczyć dostęp do składników systemowych i danych posiadacza tylko osoby, których zadania wymaga takiego dostępu.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure wymusza istniejące zasady ISMS dotyczące personelu Azure na dostęp do składników systemu Azure, weryfikacji efektywność kontroli dostępu, zapewniając Just In Time dostępu administracyjnego, odwoływanie dostępu, gdy nie jest już potrzebne i zapewnienie pracowników Uzyskiwanie dostępu do środowiska platformy Azure ma firma potrzeby. Azure dostęp do środowiska klienta jest bardzo ograniczona i dozwolony tylko za zgodą klienta.<br /><br />Aby ograniczyć fizyczny dostęp do centrum danych do autoryzowanych pracowników, dostawców wykonawców i gości zostały określone procedury. Weryfikacji zabezpieczeń i ewidencjonowania są wymagane dla personelu wymagające tymczasowy dostęp do infrastruktury centrum danych wewnętrznych. Dzienników fizycznego dostępu zostaną zweryfikowane co kwartał przez zespoły platformy Azure. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do ograniczenia dostępu do składników systemu oraz danych posiadacza tylko osoby, których zadania wymaga takiego dostępu. Dotyczy to również ograniczenie i ograniczanie dostępu do portalu zarządzania Azure oraz określania konta lub role z uprawnieniami do tworzenia, modyfikowania lub usuwania usług PaaS.|



### <a name="pci-dss-requirement-711"></a>Wymaganie PCI DSS 7.1.1

**7.1.1** access Definiuj musi dla każdej roli, w tym:
- Składniki i dane zasobów systemowych każdej roli musi mieć dostęp do ich funkcję zadania
- Poziom uprawnień wymagany (na przykład użytkownik, administrator, itp.) do uzyskiwania dostępu do zasobów

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do definiowania i dokumentowanie procesu zatwierdzania identyfikator użytkownika, definiowanie najniższych uprawnień, ograniczanie dostępu do danych posiadacza, przy użyciu unikatowych identyfikatorów, zapewniając podział obowiązków i odwoływanie dostępu użytkownika, gdy nie jest już potrzebna.|



### <a name="pci-dss-requirement-712"></a>Wymaganie PCI DSS 7.1.2

**7.1.2** ograniczanie dostępu do uprzywilejowanych identyfikatorów użytkownikowi najniższych uprawnień niezbędnych do wykonywania obowiązków.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure przyjęła zasad firmy i ochrony, w tym zasady zabezpieczeń informacji. Zasady zostały zatwierdzone, publikowane i przekazywane w systemie Windows Azure. Zasady zabezpieczeń systemu Microsoft Azure informacji wymaga dostępu do zasobów firmy Microsoft Azure można przyznanych oparte na biznesowego wyjaśnienia, z autoryzacji właściciela zasobów i na podstawie "do ograniczonego" i "najniższych uprawnieniach" zasady. Zasady dotyczy również wymagania dotyczące dostępu do obsługi autoryzacji dostępu, usunięcie uwierzytelniania praw dostępu, w tym cyklu życia zarządzania dostępu i monitoruje okresowe dostępu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web tworzy trzy konta podczas wdrażania: administrator, sqladmin i edna (domyślny użytkownik zalogowany do aplikacji sieci web podczas wykonywania demo). Role użytkownika są ograniczone do obowiązków oparte na scenariuszu pokaz udokumentowane.|



### <a name="pci-dss-requirement-713"></a>Wymaganie PCI DSS 7.1.3

**7.1.3** przypisywanie dostępu na podstawie klasyfikacji zadania i funkcji poszczególnych pracowników.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Contoso magazynu sieci Web tworzy trzy konta podczas wdrażania: administrator, sqladmin i edna (domyślny użytkownik zalogowany do aplikacji sieci web podczas wykonywania demo). Role użytkownika są ograniczone do obowiązków oparte na scenariuszu pokaz udokumentowane.|



### <a name="pci-dss-requirement-714"></a>Wymaganie PCI DSS 7.1.4

**7.1.4** wymagają udokumentowane zatwierdzenie przez autoryzowanego strony określenie wymagane uprawnienia.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do ograniczenia dostępu do składników systemu oraz danych posiadacza tylko osoby, których zadania wymaga takiego dostępu. Dotyczy to również ograniczenie i ograniczanie dostępu do portalu zarządzania Azure oraz określania konta lub role z uprawnieniami do tworzenia, modyfikowania lub usuwania usług PaaS.|



## <a name="pci-dss-requirement-72"></a>Wymaganie PCI DSS 7.2

**7.2** ustanowić system kontroli dostępu dla składników systemów, które ograniczają dostęp na podstawie użytkownika, trzeba znać, a jest ustawiona na "Odmów wszystkich", chyba że wyraźnie dozwolone.
Ten system kontroli dostępu musi zawierać następujące czynności:
- 7.2.1 pokrycie wszystkie składniki systemowe.
- 7.2.2 przypisania uprawnień do osób, na podstawie klasyfikacji zadania i funkcji.
- 7.2.3 domyślne ustawienie "Odmów all".

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Active Directory, aby ograniczyć dostęp tylko wyznaczonym użytkownikom. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - Identity Management](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>Wymaganie PCI DSS 7.3

**7.3** upewnij się, że zasady zabezpieczeń i procedury działania w celu ograniczania dostępu do danych posiadacza są udokumentowane w użyciu i znanych wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Przypadek użycia i opis dotyczące używający CHD i używania CHD znaleźć w dokumentacji magazynu sieci Web firmy Contoso.|





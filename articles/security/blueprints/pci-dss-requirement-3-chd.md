---
title: Plan Azure przetwarzania płatności — wymagania CHD
description: PCI DSS wymaganie 3
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 7ff8a412b16025afe2640b73b51d9b2a2fdca354
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895456"
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące CHD dla środowisk standardem PCI DSS
## <a name="pci-dss-requirement-3"></a>PCI DSS wymaganie 3

**Chronić dane przechowywane posiadacza**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Metody ochrony, takie jak szyfrowanie, obcięcie maskowania, i tworzenia skrótów są krytycznych składników posiadacza ochrony danych. Jeśli intruz prowadzenia inne kontrole dotyczące zabezpieczenia i uzyskuje dostęp do zaszyfrowanych danych, bez prawidłowego kluczy kryptograficznych, dane są niemożliwe do odczytania i nie można użyć do tej osoby. Należy również rozważyć inne metody skuteczną ochronę danych przechowywanych jako możliwości ograniczenia ryzyka. Na przykład metody zminimalizowanie ryzyka to nie przechowywania danych posiadacza, chyba że bezwzględnie konieczne, obcinanie posiadacza danych czy KADROWAĆ pełnej nie jest potrzebna, a nie wysyłania Miednice niechronionej za pomocą technologii obsługi wiadomości przez użytkownika końcowego, takie jak wiadomości e-mail i natychmiastowe do obsługi komunikatów.
Zapoznaj się PCI DSS i PA-DSS terminów, skrótów i akronimów dla definicji "silna Kryptografia" i inne postanowienia PCI DSS.

## <a name="pci-dss-requirement-31"></a>Wymaganie PCI DSS 3.1

**3.1** pamięci masowej posiadacza keep do minimum implementując zasady przechowywania i usuwania danych, procedury i procesy, które obejmują co najmniej dla wszystkich magazynów danych (CHD) posiadacza karty:
- Ograniczanie danych magazynu kwota i przechowywania czasu wymaganego do wymagań prawnych, prawne i biznesowe
- Wymagania dotyczące przechowywania określonych dla danych posiadacza
- Procesy do usunięcia bezpiecznych danych, gdy nie są już potrzebne
- Co kwartał proces identyfikacji i bezpieczne usuwanie przechowywane dane posiadacza przekracza zdefiniowanych przechowywania.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure jest odpowiedzialny za zapewnienie, że są bezpiecznie wycofany z eksploatacji przy użyciu NIST 800 88 protokołów zgodne określone w zasadach jego bezpiecznego usuwania danych klienta oznaczonego do usunięcia. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie usuwać ani zniszczyć wszystkie przechowywane CHD. Jednak wszystkie dane są zaszyfrowane i nie podstawowego konta numer (PRZESUWANIE) dane są przechowywane.<br /><br />|



## <a name="pci-dss-requirement-32"></a>Wymaganie PCI DSS 3.2

**3.2** nie należy przechowywać dane poufne uwierzytelniania po autoryzacji (nawet jeśli szyfrowane). Odebranie dane poufne uwierzytelniania, że wszystkie dane nieodwracalny po zakończeniu procesu autoryzacji. 
- Brak biznesowego wyjaśnienia, i 
- Dane są bezpiecznie przechowywane.
Dane poufne uwierzytelniania obejmuje dane jako cytowane w 3.2.1 za pośrednictwem 3.2.3 następujące wymagania:


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie usuwać ani zniszczyć wszystkie przechowywane CHD; Przykładowe dane są przechowywane dla celów demonstracyjnych tylko. Jednak wszystkie dane są zaszyfrowane i nie głównej konto, które są przechowywane dane numer (przesunięcie).<br /><br />|



### <a name="pci-dss-requirement-321"></a>Wymaganie PCI DSS 3.2.1

**3.2.1** nie należy przechowywać całą zawartość dowolną ścieżkę (od magnetycznych znajdujących się na tylnej karty, odpowiednik danych zawartych w układzie lub w innym miejscu) po autoryzacji. Tych danych można również jest nazywany pełnej ścieżki, śledzenie, śledzenie 1, śledzić 2 i dane magnetycznych —. 

> [!NOTE]
> W trakcie normalnego przebiegu biznesowych może być konieczne następujące elementy danych z magnetycznych ma zostać zachowana: 
> - Imię i nazwisko 
> - Numer konta podstawowego (przesunięcie) 
> - Data wygaśnięcia 
> - Usługa kodu 
>
> Aby zminimalizować ryzyko, należy przechowywać tylko te elementy danych zgodnie z potrzebami firmy.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie przechowuje żadnych CHD pełnej zawartości.|



### <a name="pci-dss-requirement-322"></a>Wymaganie PCI DSS 3.2.2

**3.2.2** nie należy przechowywać kod weryfikacyjny karty lub wartość (trzy- lub czterocyfrowy numer drukowane z przodu lub z tyłu karty płatności używany do weryfikowania karty nie zaprezentowanie transakcji) po autoryzacji.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso szyfruje wszystkie dane w tym przykłady CVV.|



### <a name="pci-dss-requirement-323"></a>Wymaganie PCI DSS 3.2.3

**3.2.3** nie należy przechowywać osobisty numer identyfikacyjny (PIN) lub zaszyfrowanego bloku kodu PIN po autoryzacji.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie przechowuje informacji o numerach PIN.|



## <a name="pci-dss-requirement-33"></a>Wymaganie PCI DSS 3.3

**3.3** maska KADROWAĆ podczas wyświetlania (maksymalną liczbę cyfr, które mają być wyświetlane są pierwszy sześciu i cztery ostatnie cyfry), tak, aby tylko pracowników z prawem firmę muszą można zobaczyć pełny sieci PAN. 

> [!NOTE]
> To wymaganie nie zastępuje bardziej rygorystycznych wymagań w przypadku wyświetla posiadacza danych — na przykład działem prawnym lub płatności brand wymagania dotyczące karty punktach sprzedaży (POS) potwierdzenia.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso maski numer podstawowego konta (PRZESUWANIE) przy użyciu przezroczystego szyfrowania danych, zawsze zaszyfrowanych kolumn i maskowania danych dynamicznych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-34"></a>Wymaganie PCI DSS 3.4

**3.4** renderowania PRZESUWANIE Nieodczytywalny dowolnego miejsca (w tym na przenośny nośnika cyfrowego nośnik kopii zapasowej, a w dziennikach) jest przechowywany przy użyciu dowolnej z następujących metod:
- Skróty jednokierunkowe oparte na kryptografii silne (skrót musi być z całego PRZESUWANIE)
- Obcięcie (wyznaczania wartości skrótu nie można zastąpić skróconą segment PRZESUWANIE)
- Tokeny indeksu i tablety (podkładki muszą być bezpiecznie przechowywane)
- Silna Kryptografia skojarzonych z nimi procesów zarządzania kluczami i procedur. 

> [!NOTE]
> Jest stosunkowo prosta nakładu pracy dla intruzowi odtworzenie oryginalnych danych PRZESUWANIE, jeśli mają dostęp do obcięty i skrótu wersji sieci PAN. W przypadku, gdy mieszany i obcięty wersji tego samego PRZESUWANIE znajdują się w środowisku jednostki, należy dodatkowych funkcji kontroli w celu zapewnienia, że wersje skrótu i skróconą nie zostanie skorelowana odtworzenie oryginalnej sieci PAN.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso szyfruje wszystkie dane karty kredytowej, a następnie używa usługi Azure Key Vault zarządzać kluczami, co uniemożliwia pobieranie CHD.<br /><br />Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-341"></a>Wymaganie PCI DSS 3.4.1

**3.4.1** czy (a nie do szyfrowania na poziomie pliku lub kolumny bazy danych) jest używane szyfrowanie dysku, logicznego dostępu musi być zarządzane oddzielnie i niezależnie od uwierzytelniania macierzystego systemu operacyjnego i mechanizmy kontroli dostępu (na przykład przez nie Korzystanie z bazy danych kont użytkowników lokalnych lub poświadczenia logowania ogólny sieci). Klucze odszyfrowujące nie może być skojarzony z kontami użytkowników. 

> [!NOTE]
> To wymaganie dotyczy oprócz innych PCI DSS szyfrowania i wymagania dotyczące zarządzania kluczami.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso wszystkie przechowywane dane są szyfrowane i dzieli ruch, aby zapobiec uprzywilejowanych podniesienia uprawnień funkcji DevOps.<br /><br />Środowiska usługi aplikacji jest zabezpieczona i zablokowane, musi istnieć mechanizm umożliwia wersjach opracowywania oprogramowania lub zmiany, które mogą być wymagane, takie jak możliwość monitorowania aplikacji sieci Web przy użyciu Kudu.<br /><br />Maszyna wirtualna zostanie ustanowiony jako jumpbox (host bastionu) z następujących konfiguracji:<br /><br /><ul><li>[Rozszerzenie ochrony przed złośliwym oprogramowaniem](/azure/security/azure-security-antimalware)</li><li>[Rozszerzenie monitorowania analizy dzienników](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Rozszerzenia diagnostyki maszyny Wirtualnej](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Dysku zaszyfrowanego funkcją BitLocker](/azure/security/azure-security-disk-encryption)</li></ul>Za pomocą usługi Azure Key Vault wyrównana z Azure dla instytucji rządowych, wymagania dotyczące HIPAA i PCI DSS.|



## <a name="pci-dss-requirement-35"></a>Wymaganie PCI DSS 3.5

**3.5** dokument i wdrożenie procedury ochrony kluczy, używany do zabezpieczania posiadacza przechowywanych danych przed ujawnieniem i nieprawidłowego użycia. 

> [!NOTE]
> To wymaganie dotyczy klucze służące do szyfrowania danych przechowywanych posiadacza i odnosi się także do szyfrowania klucza klucze służące do ochrony kluczy szyfrowania danych — te klucze szyfrowania klucza musi być przynajmniej taka sama, jak klucz szyfrowania danych.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Microsoft Azure gwarantuje, że magazynów kluczy klienta są logicznie odizolowane od siebie i logicznie odizolowane od płaszczyzny zarządzania w usłudze Key Vault. Key Vault jest zaprojektowana tak, aby firmy Microsoft nie ma stały dostęp do magazynu kluczy klienta. <br /><br />Klucze są chronione przez Microsoft Azure, przy użyciu branżowych standardów dotyczących algorytmów, długości kluczy i sprzętowych modułów zabezpieczeń (HSM).<br /><br />Klucza przechowywanego w Microsoft Azure Key Vault może służyć do ochrony inny klucz. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso zawiera dokumentację w celu przedstawienia i wdrażać chronionego klucza rozwiązania do ochrony pokaz CHD.|



### <a name="pci-dss-requirement-351"></a>Wymaganie PCI DSS 3.5.1

**3.5.1** *dodatkowych wymogów dotyczących tylko dostawców usług:* Obsługa udokumentowane opis architektury usług kryptograficznych, który zawiera:
- Szczegóły dotyczące algorytmów, protokołów i klucze służące do ochrony danych posiadacza, łącznie z klucza siły i Data wygaśnięcia
- Opis elementu użycia klucza dla każdego klucza
- Wszystkie moduły HSM i innych SCDs używany do zarządzania kluczami 

> [!NOTE]
> To wymaganie jest najlepszym rozwiązaniem do 31 stycznia 2018, po którym staje się on wymagania.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Microsoft Azure gwarantuje, że magazynów kluczy klienta są logicznie odizolowane od siebie i logicznie odizolowane od płaszczyzny zarządzania w usłudze Key Vault. Key Vault jest zaprojektowana tak, aby firmy Microsoft nie ma stały dostęp do magazynu kluczy klienta. <br /><br />Klucze są chronione przez Microsoft Azure, przy użyciu branżowych standardów dotyczących algorytmów, długości kluczy i sprzętowych modułów zabezpieczeń (HSM).<br /><br />Klucza przechowywanego w Microsoft Azure Key Vault może służyć do ochrony inny klucz. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso zawiera dokumentację w celu przedstawienia i wdrażać chronionego klucza rozwiązania do ochrony pokaz CHD.|



### <a name="pci-dss-requirement-352"></a>Wymaganie PCI DSS 3.5.2

**3.5.2** ograniczanie dostępu do kluczy kryptograficznych, jak najmniejszej liczby nadzorców niezbędne.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Magazyn kluczy obsługuje zasady dostępu szczegółowego, umożliwiające właściciela usługi Key Vault udzielić dostępu do określonych funkcji do wykonywania określonych operacji do konkretnych obiektów. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Zarządzanie kluczami magazynu sieci Web firmy Contoso dotyczy tylko jedno konto użytkownika (admin ##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>Wymaganie PCI DSS 3.5.3

**3.5.3** przechowywać klucze tajne i prywatne używane do szyfrowania/odszyfrowywania danych posiadacza w jednym (lub więcej) z następujących form przez cały czas:
- Zaszyfrowane za pomocą klucza szyfrowania klucz jest przynajmniej taka sama, jak klucz szyfrowania danych i jest przechowywana oddzielnie z klucza szyfrowania danych
- W ramach bezpiecznego urządzenia kryptograficznych (na przykład (hosta) sprzętowego modułu zabezpieczeń (HSM) lub zatwierdzone PTS punktu z interakcji urządzenia)
- Co najmniej dwa składniki: pełnej długości klucza lub klucza udziałów zgodnie z branży - zaakceptowane — metoda 

> [!NOTE]
> Nie jest wymagane, że w jednym z tych formularzy można przechowywać kluczy publicznych.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Klucze są przechowywane w określonych magazynów kluczy zidentyfikowane przez klienta.<br /><br />Magazyn kluczy mogą uzyskiwać równocześnie i globalnie wielu aplikacji, co zmniejsza potrzebę skopiować klucz i przechowywane w wielu lokalizacjach. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-354"></a>Wymaganie PCI DSS 3.5.4

**3.5.4** przechowywania kluczy kryptograficznych w lokalizacjach najmniejsze możliwe.


**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Klucze są przechowywane w określonych magazynów kluczy zidentyfikowane przez klienta. <br /><br />Magazyn kluczy mogą uzyskiwać równocześnie i globalnie wielu aplikacji, co zmniejsza potrzebę skopiować klucz i przechowywane w wielu lokalizacjach. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



## <a name="pci-dss-requirement-36"></a>Wymaganie PCI DSS 3,6

**3,6** pełni dokumentu i wdrożenie wszystkich procesów zarządzania kluczami i klucze kryptograficzne używane do szyfrowania danych posiadacza, łącznie z następujących procedur. 

> [!NOTE]
> Wiele standardy branżowe zarządzania kluczami są dostępne w różnych zasobów, w tym NIST, który znajduje się w temacie http://csrc.nist.gov.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-361"></a>Wymaganie PCI DSS 3.6.1

**3.6.1** generowania silnych kluczy kryptograficznych

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:** <br /><br />Podczas generowania kluczy w magazynie kluczy, Azure jest odpowiedzialny za Generowanie kluczy dla specyfikacji klienta. Klucze są generowane przy użyciu modułu HSM. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-362"></a>Wymaganie PCI DSS 3.6.2

**3.6.2** secure dystrybucji kluczy kryptograficznych

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Użyj narzędzia do własnego klucza (BYOK) hermetyzuje klucz klienta i elementów docelowych w magazynie zabezpieczeń, który jest powiązany z określonej subskrypcji platformy Azure. Klucz można importować tylko do określonych subskrypcji magazynu kluczy w wybranym regionie. Ten proces wykorzystuje procedury szyfrowania dostarczone przez producenta sprzętu. Klienci otrzymywać powiadomienie o pomyślnym transferu. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-363"></a>Wymaganie PCI DSS 3.6.3

**3.6.3** bezpiecznego magazynu kluczy kryptograficznych

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Klucze są przechowywane w modułach HSM i są chronione przy użyciu zabezpieczeń kryptograficznych producenta sprzętu. Metadane kluczy są przechowywane w usłudze Azure Storage w stanie zaszyfrowany, co jest unikatowy dla każdego magazynu kluczy. <br /><br /> |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-364"></a>Wymaganie PCI DSS 3.6.4

**3.6.4** kryptograficznych zmiany klucza dla kluczy, które osiągnęły koniec cryptoperiod ich (na przykład po upływie zdefiniowanego czasu i/lub po określonym tekst zaszyfrowany została opracowana przez dany klucz), zgodnie z definicją w skojarzonych dostawcy aplikacji lub właściciela klucza oraz z branży na podstawie najlepszych rozwiązań i wytycznych (na przykład NIST Special Publication 800-57).

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Magazyn kluczy obsługuje funkcje do uaktualniania lub przywracania kluczy, który jest zdefiniowany przez klienta. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-365"></a>Wymaganie PCI DSS 3.6.5

**3.6.5** wycofania lub zastąpienia (na przykład archiwizacji, stosowanie i niszczenie i/lub odwołania) klucze uznane za niezbędne podczas integralność klucza ma został obniżony (na przykład wyjścia pracownika z znajomości klucza zwykłego tekstu składnik) lub klucze są podejrzane bezpieczeństwa. 

> [!NOTE]
> Jeśli wycofywane lub wymieniane kluczy kryptograficznych muszą zostać zachowane, te klucze można bezpiecznie archiwizowane (na przykład przy użyciu klucza szyfrowania klucza). Zarchiwizowanych kluczy kryptograficznych należy używać tylko na potrzeby weryfikacji/odszyfrowywania.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Magazyn kluczy obsługuje funkcje do wycofania lub zastąpić, która jest zdefiniowana przez klienta. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-366"></a>Wymaganie PCI DSS 3.6.6

**3.6.6** użycie ręczne operacji zarządzania kluczami kryptograficznych zwykłym tekstem tych operacji musi być zarządzane przy użyciu wiedzy podziału i kontroli dwa. 

> [!NOTE]
> Ręczne zarządzanie kluczami operacje obejmują, ale nie są ograniczone do: klucz generacji, transmisji ładowania, magazynu i zniszczenia.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-367"></a>Wymaganie PCI DSS 3.6.7

**3.6.7** zapobiegania nieautoryzowanego zastąpienia kluczy kryptograficznych.

**Obowiązki:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **Użytkownicy usługi Key Vault:**<br /><br />Klucz magazynów logicznie są rozdzielone i nie obsługują autoryzacji między katalogu. W związku z tym nie będzie mógł nieautoryzowanego podstawienia. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-368"></a>Wymaganie PCI DSS 3.6.8

**3.6.8** wymagania dotyczące szyfrowania klucza nadzorców formalnie potwierdzić zrozumieć i zaakceptować obowiązków niejawnego klucza.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Zarządzanie kluczami magazynu sieci Web firmy Contoso dotyczy tylko jedno konto użytkownika (admin ##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>Wymaganie PCI DSS 3.7

**3.7** upewnij się, że zasady zabezpieczeń i procedury działania ochrony danych przechowywanych posiadacza są udokumentowane w użyciu i znane wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso używa usługi Azure Key Vault wszystkich zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - szyfrowania](payment-processing-blueprint.md#encryption-and-secrets-management).|





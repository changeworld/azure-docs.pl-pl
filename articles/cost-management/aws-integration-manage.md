---
title: Zarządzanie kosztami i użyciem AWS w Azure Cost Management
description: Ten artykuł pomaga zrozumieć, jak korzystać z analizy kosztów i budżetów w Cost Management, aby zarządzać kosztami i użyciem AWS.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 6767644e037c6abb3e3044c8707e923a9137a01c
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019544"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Zarządzanie kosztami i użyciem AWS na platformie Azure

Po skonfigurowaniu i skonfigurowaniu integracji raportów o kosztach i użyciu AWS dla Azure Cost Management można rozpocząć zarządzanie kosztami AWS i użyciem. Ten artykuł pomaga zrozumieć, jak korzystać z analizy kosztów i budżetów w Cost Management, aby zarządzać kosztami i użyciem AWS.

Jeśli integracja nie została jeszcze skonfigurowana, zapoznaj się z tematem [Konfigurowanie i Konfigurowanie integracji raportów użycia AWS](aws-integration-set-up-configure.md).

_Przed rozpoczęciem_: Jeśli nie znasz analiz kosztów, zobacz temat [eksplorowanie i analizowanie kosztów za pomocą analizy kosztów](quick-acm-cost-analysis.md) — Szybki Start. Jeśli nie znasz już budżetów na platformie Azure, zobacz samouczek [Tworzenie i zarządzanie budżetami platformy Azure](tutorial-acm-create-budgets.md) .

## <a name="view-aws-costs-in-cost-analysis"></a>Wyświetlanie kosztów AWS w analizie kosztów

Koszty AWS są dostępne w analizie kosztów w następujących zakresach:

- AWS połączone konta w grupie zarządzania
- AWS połączone konta
- AWS skonsolidowane koszty kont

W następnych sekcjach opisano, jak korzystać z zakresów, aby zobaczyć dane dotyczące kosztów i użycia dla każdej z nich.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Wyświetl połączone konta AWS w grupie zarządzania

Wyświetlanie kosztów przy użyciu zakresu grupy zarządzania jest jedynym sposobem wyświetlania zagregowanych kosztów pochodzących z różnych subskrypcji i połączonych kont. Korzystanie z grupy zarządzania zapewnia widok między chmurą.

W obszarze Analiza kosztów Otwórz selektor zakresu i wybierz grupę zarządzania, która zawiera połączone konta AWS. Oto przykład obrazu w Azure Portal:

![Przykład widoku wyboru zakresu](./media/aws-integration-manage/select-scope01.png)



Oto przykład wyświetlania kosztu grupy zarządzania w analizie kosztów pogrupowanym według dostawcy (platformy Azure i AWS).

![Przykład pokazujący koszty platformy Azure i AWS na kwartał w analizie kosztów](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Wyświetlanie kosztów połączonych kont AWS

Aby wyświetlić AWS konta łączy, Otwórz selektor zakresu i wybierz połączone konto AWS. Należy pamiętać, że połączone konta są skojarzone z grupą zarządzania, zgodnie z definicją w łączniku AWS.

Oto przykład, który pokazuje wybór zakresu AWS połączonego konta.

![Przykład widoku wyboru zakresu](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Wyświetlanie kosztów skonsolidowanego konta AWS

Aby wyświetlić AWS skonsolidowanych kont, Otwórz selektor zakresu i wybierz skonsolidowane konto AWS. Oto przykład, który pokazuje wybór zakresu skonsolidowanego konta AWS.

![Przykład widoku wyboru zakresu](./media/aws-integration-manage/select-scope03.png)



Ten zakres zapewnia Zagregowany widok wszystkich połączonych kont AWS skojarzonych z kontem skonsolidowanym AWS. Oto przykład przedstawiający koszty skonsolidowanego konta AWS pogrupowane według nazwy usługi.

![Przykład przedstawiający AWS skonsolidowanych kosztów w analizie kosztów](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Wymiary dostępne do filtrowania i grupowania

W poniższej tabeli opisano wymiary dostępne do grupowania i filtrowania według analizy kosztów.

| Wymiar | Nagłówek usługi Amazon — CUR | Zakresy | Komentarze |
| --- | --- | --- | --- |
| Strefa dostępności | lineitem/AvailabilityZone | Wszyscy |   |
| Location | produkt/region | Wszyscy |   |
| Zmierz |   | Wszyscy |   |
| Kategoria miernika | lineItem/ProductCode | Wszyscy |   |
| Podkategoria miernika | lineitem/UsageType | Wszyscy |   |
| Operacja | lineItem/Operation | Wszyscy |   |
| Resource | lineItem/ResourceId | Wszyscy |   |
| Typ zasobu | product/instanceType | Wszyscy | Jeśli produkt/instanceType ma wartość null, używany jest lineItem/UsageType. |
| ResourceGuid | ND | Wszyscy | Identyfikator GUID licznika platformy Azure. |
| Nazwa usługi | produkt/ProductName | Wszyscy | Jeśli produkt/ProductName ma wartość null, używany jest lineItem/ProductCode. |
| Warstwa usług |   |   |   |
| Identyfikator subskrypcji | lineItem/UsageAccountId | Skonsolidowane konto i Grupa zarządzania |   |
| Nazwa subskrypcji | ND | Skonsolidowane konto i Grupa zarządzania | Nazwy kont są zbierane przy użyciu interfejsu API organizacji AWS. |
| Tag | resourceTags/\* | Wszyscy | _Użytkownik:_ prefiks jest usuwany ze znaczników zdefiniowanych przez użytkownika, aby zezwolić na używanie tagów międzychmurowych. _AWS:_ prefiks pozostaje nienaruszony. |
| Identyfikator konta bilingowego | rachunek/PayerAccountId | Grupa zarządzania |   |
| Nazwa konta rozliczeniowego | ND | Grupa zarządzania | Nazwy kont są zbierane przy użyciu interfejsu API organizacji AWS. |
| Dostawca | ND | Grupa zarządzania | AWS lub Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Ustawianie budżetów w zakresach AWS

Używaj budżetów, aby aktywnie zarządzać kosztami i korzystać z odpowiedzialności w organizacji. Budżety są ustawiane w AWS skonsolidowanego konta i AWS połączonych kont. Oto przykład budżetu dla konta skonsolidowanego AWS pokazanego w Cost Management:

![Przykład przedstawiający budżety dla konta skonsolidowanego AWS](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Proces zbierania danych AWS

Po skonfigurowaniu łącznika AWS rozpocznie się proces zbierania i odnajdywania danych. Zebranie wszystkich danych użycia może potrwać kilka godzin. Czas trwania zależy od:

- Czas wymagany do przetworzenia bieżących plików, które znajdują się w zasobniku AWS S3.
- Czas wymagany do utworzenia skonsolidowanego konta AWS i zakresów połączonych kont AWS.
- Czas i częstotliwość AWS są zapisywane w zasobniku S3 plików raportów o kosztach i użyciu

## <a name="aws-integration-pricing"></a>Ceny integracji AWS

Każdy Łącznik AWS otrzymuje 90 dni bezpłatnej wersji próbnej. W publicznej wersji zapoznawczej nie jest naliczana opłata.

Cennik wynosi 1% kosztów miesięcznych AWS. W każdym miesiącu opłata jest naliczana na podstawie kosztów zafakturowanych w poprzednim miesiącu.

Dostęp do interfejsów API AWS może pociągnąć za sobą dodatkowe koszty.

## <a name="aws-integration-limitations"></a>Ograniczenia integracji AWS

- Cost Management nie obsługuje raportów kosztów zawierających wiele typów walut. Komunikat o błędzie jest wyświetlany w przypadku wybrania zakresu, który ma wiele walut.
- Łączniki chmury nie obsługują AWS GovCloud (US), AWS gov ani AWS.
- Cost Management przedstawia tylko _koszty użycia_ AWS. Podatki, pomoc techniczna, zwroty, RI, kredyty lub inne typy opłat nie są jeszcze obsługiwane.

## <a name="troubleshooting-aws-integration"></a>Rozwiązywanie problemów z integracją AWS

Skorzystaj z poniższych informacji dotyczących rozwiązywania problemów, aby rozwiązać typowe problemy.

### <a name="no-permission-to-aws-linked-accounts"></a>Brak uprawnień do AWS połączonych kont

**Kod błędu:** _Próby_

Istnieją dwa sposoby uzyskania uprawnień dostępu do AWS połączonych kont:

- Uzyskaj dostęp do grupy zarządzania, która ma połączone konta AWS.
- Ktoś może udzielić użytkownikowi uprawnienia do połączonego konta AWS.

Domyślnie twórca łącznika AWS jest właścicielem wszystkich obiektów utworzonych przez łącznik. Dotyczy to również skonsolidowanego konta AWS i połączonego konta AWS.

Aby możliwe było zweryfikowanie ustawień łączników, musisz mieć co najmniej rolę współautor, czytnik nie może zweryfikować ustawień łącznika

### <a name="collection-failed-with-assumerole"></a>Zbieranie nie powiodło się z AssumeRole

**Kod błędu:** _FailedToAssumeRole_

Ten błąd oznacza, że Cost Management nie może wywołać interfejsu API AssumeRole AWS. Przyczyną tego problemu może być problem z definicją roli. Sprawdź, czy są spełnione następujące warunki:

- Identyfikator zewnętrzny jest taki sam jak w definicji roli i definicji łącznika.
- Typ roli jest ustawiany na **inne konto AWS należące do Ciebie lub innej firmy.**
- Wybór opcji **Wymagaj uwierzytelniania MFA** jest wyczyszczony.
- Zaufanym kontem AWS w roli AWS jest _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Zbieranie nie powiodło się z powodu odmowy dostępu — CUR definicje raportów

**Kod błędu:** _AccessDeniedReportDefinitions_ 

Ten błąd oznacza, że Cost Management nie może wyświetlić definicji raportów kosztów i użycia. To uprawnienie służy do sprawdzania, czy waluta jest zdefiniowana zgodnie z oczekiwaniami Azure Cost Management. Zobacz [Tworzenie raportu o kosztach i użyciu w AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Zbieranie nie powiodło się z powodu odmowy dostępu — raporty listy

**Kod błędu:** _AccessDeniedListReports_ 

Ten błąd oznacza, że Cost Management nie może wyświetlić listy obiektów w zasobniku S3, w których znajduje się bieżący. Zasady AWS w usłudze IAM wymagają uprawnienia do zasobnika oraz obiektów w zasobniku. Zobacz [Tworzenie roli i zasad w AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Zbieranie nie powiodło się z powodu odmowy dostępu — Pobierz raport 

**Kod błędu:** _AccessDeniedDownloadReport_ 

Ten błąd oznacza, że Cost Management nie jest w stanie uzyskać dostępu do wszystkich plików przechowywanych w zasobniku usługi Amazon S3 i pobrać ich. Upewnij się, że zasady JSON AWS dołączone do roli przypominają przykład przedstawiony w dolnej części sekcji [Tworzenie roli i zasad w AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Zbieranie nie powiodło się, ponieważ nie znaleziono raportu o kosztach i użyciu

**Kod błędu:** _FailedToFindReport_

Ten błąd oznacza, że Cost Management nie może znaleźć raportu kosztów i użycia zdefiniowanego w łączniku. Upewnij się, że nie jest on usunięty i że zasady AWS JSON dołączone do roli przypominają przykład wyświetlany w dolnej części sekcji [Tworzenie roli i zasad w AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Nie można utworzyć lub zweryfikować łącznika ze względu na niezgodność definicji raportów o kosztach i użyciu

**Kod błędu:** _ReportIsNotValid_

Ten błąd jest związany z definicją raportu o kosztach i użyciu AWS, ale wymagamy określonych ustawień dla tego raportu, zobacz temat wymagania w temacie [Tworzenie kosztów i użycie w AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>Następne kroki

- Jeśli środowisko platformy Azure nie zostało jeszcze skonfigurowane w grupach zarządzania, zobacz [początkowa konfiguracja grup zarządzania](../governance/management-groups/index.md#initial-setup-of-management-groups).

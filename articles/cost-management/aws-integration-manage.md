---
title: Zarządzanie kosztami AWS i użycia w usłudze Azure Cost Management
description: Ten artykuł pomoże Ci zrozumieć, jak do analizy kosztów i budżetów kosztów zarządzania Zarządzanie koszty usług AWS i użycia.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870315"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Zarządzanie kosztami AWS i użycia na platformie Azure

Po użytkownik została przygotowana i skonfigurowana AWS kosztów i użycia integracji raportu dla usługi Azure Cost Management, możesz rozpocząć zarządzanie swoje koszty usług AWS i użycia. Ten artykuł pomoże Ci zrozumieć, jak do analizy kosztów i budżetów kosztów zarządzania Zarządzanie koszty usług AWS i użycia.

Jeśli użytkownik jeszcze nie skonfigurowano integracji, zobacz [zestawu i konfigurowaniu integracji raportu użycia usług AWS](aws-integration-set-up-configure.md).

_Przed rozpoczęciem_: Jeśli jesteś zaznajomiony z analizy kosztów, zobacz [badanie i analizowanie kosztów za pomocą analizy kosztów](quick-acm-cost-analysis.md) Szybki Start. I, jeśli znasz budżet na platformie Azure, zobacz [tworzenie i zarządzanie Azure budżetów](tutorial-acm-create-budgets.md) samouczka.

## <a name="view-aws-costs-in-cost-analysis"></a>Koszty usługi AWS widoku w analizy kosztów

Koszty usług AWS są dostępne w analizę kosztów w następujących zakresów:

- Kont platformy AWS połączone w ramach grupy zarządzania
- Koszty usług AWS połączone
- Koszty usługi AWS skonsolidowane

W kolejnych sekcjach opisano sposób użycia zakresów umożliwiającego wyświetlanie danych kosztów i użycia dla każdego z nich.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Wyświetl AWS połączone konta w ramach grupy zarządzania

Wyświetlanie kosztów przy użyciu danego zakresu grupy zarządzania jest jedynym sposobem, aby zobaczyć koszty zagregowane pochodzących z różnych subskrypcji i połączone konta. Za pomocą grupy zarządzania zawiera widok wielu chmur.

Analizy kosztów Otwórz selektor zakresu, a następnie wybierz grupę zarządzania, która zawiera konta usług AWS połączone. Oto przykładowa ilustracja w witrynie Azure portal:

![Przykładowy Widok wybierz zakres](./media/aws-integration-manage/select-scope01.png)



Oto przykład pokazujący koszt grupy zarządzania w analizy kosztów, pogrupowane według dostawca (Azure i AWS).

![Przykład przedstawiający koszty platformy Azure i AWS analizy kosztów w kwartału](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Wyświetlanie usług AWS, połączone konta kosztów

Aby wyświetlić koszty usług AWS łącze, otworzyć selektor zakresu i wybrać AWS połączone konta. Należy pamiętać, że połączonych kontach są skojarzone z grupą zarządzania zgodnie z definicją w łączniku usługi AWS.

Oto przykład, że przedstawia Wybieranie usług AWS połączone konto zakresu.

![Przykładowy Widok wybierz zakres](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Wyświetl AWS skonsolidowane koszty konta

Aby wyświetlić AWS konta skonsolidowane koszty, Otwórz selektor zakresu, a następnie wybierz usługi AWS skonsolidowane konta. Oto przykład, że przedstawia Wybieranie usług AWS skonsolidowane w zakresie konta.

![Przykładowy Widok wybierz zakres](./media/aws-integration-manage/select-scope03.png)



Ten zakres zawiera zagregowany widok AWS wszystkie połączone konta skojarzone z kontem usług AWS skonsolidowane. Oto przykład pokazujący, że koszty usług AWS skonsolidowane kont, zgrupowane według nazwy usługi.

![Przykład przedstawiający AWS skonsolidowane koszty w analizy kosztów](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Wymiarów, filtrowanie i grupowanie

W poniższej tabeli opisano wymiary dostępne do grupy i Filtruj według analizy kosztów.

| Wymiar | Powtórz Amazon nagłówka | Zakresy | Komentarze |
| --- | --- | --- | --- |
| Strefa dostępności | lineitem/AvailabilityZone | Wszyscy |   |
| Lokalizacja | produkt/Region | Wszyscy |   |
| Jednostka |   | Wszyscy |   |
| Kategoria miernika | lineItem/ProductCode | Wszyscy |   |
| Podkategoria miernika | lineitem/UsageType | Wszyscy |   |
| Operacja | lineItem/Operation | Wszyscy |   |
| Resource | lineItem/ResourceId | Wszyscy |   |
| Typ zasobu | product/instanceType | Wszyscy | Jeśli produkt/instanceType ma wartość null, używana jest lineItem/UsageType. |
| ResourceGuid | ND | Wszyscy | Identyfikator GUID liczników platformy Azure. |
| Nazwa usługi | product/ProductName | Wszyscy | Jeśli produkt/ProductName ma wartość null, używana jest lineItem/kod produktu. |
| Warstwa usług |   |   |   |
| Identyfikator subskrypcji | lineItem/UsageAccountId | Skonsolidowane grupy konta i zarządzanie |   |
| Nazwa subskrypcji | ND | Skonsolidowane grupy konta i zarządzanie | Nazwy kont są zbierane przy użyciu interfejsu API organizacji usługi AWS. |
| Tag | resourceTags/\* | Wszyscy | _Użytkownika:_ prefiks jest usuwany z zdefiniowanych przez użytkownika tagów, aby umożliwić wielu chmur tagów. _Aws:_ prefiks pozostaje niezmienione. |
| Identyfikator konta rozliczeń | bill/PayerAccountId | Grupa zarządzania |   |
| Nazwa konta rozliczeń | ND | Grupa zarządzania | Nazwy kont są zbierane przy użyciu interfejsu API organizacji usługi AWS. |
| Dostawca | ND | Grupa zarządzania | Usługi AWS i Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Zestaw budżet na zakresy usług AWS

Użyj budżety, aby aktywnie zarządzać kosztów i dysku odpowiedzialność w Twojej organizacji. Budżetów są ustawiane na konta usługi AWS skonsolidowane i AWS zakresy połączonego konta usługi. Oto przykład budżetów dla konta usługi AWS skonsolidowane objętego Cost Management:

![Przykład przedstawiający budżetów usług AWS skonsolidowane konta](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Proces zbierania danych usługi AWS

Po skonfigurowaniu łącznika usługi AWS, zbieranie danych i procesów odnajdywania należy uruchomić. Może upłynąć kilka godzin w celu gromadzenia wszystkich danych użycia. Czas trwania zależy od:

- Czas potrzebny do przetwarzania plików WALUTA, które znajdują się w zasobniku AWS S3.
- Czas potrzebny na tworzenie konta usługi AWS skonsolidowane i AWS połączonych zakresów konta.
- Czas i częstotliwość AWS są zapis plików kosztów i użycia raportu w przedział S3

## <a name="aws-integration-pricing"></a>Cennik integracji usług AWS

Każdy łącznik usługi AWS pobiera 90 dni do bezpłatnej wersji próbnej. W publicznej wersji zapoznawczej nie ma opłat.

Cena listy jest 1% miesięczne koszty używania usług AWS. Co miesiąc, naliczana na podstawie koszty invoiced z poprzedniego miesiąca.

Uzyskiwanie dostępu do interfejsów API usługi AWS może pociągnąć za sobą dodatkowe koszty.

## <a name="aws-integration-limitations"></a>Ograniczenia dotyczące integracji usług AWS

- Cost Management nie obsługuje raporty kosztów, które zawierają wiele typów waluty. Komunikat o błędzie jest wyświetlany w przypadku wybrania zakres z wieloma walutami.
- Łączniki chmury nie obsługują usług AWS GovCloud (USA), Administracja usług AWS lub chińska wersja platformy AWS.
- Usługa Cost Management zawiera AWS _koszty użytkowania_ tylko. Podatku, pomocy technicznej, zwroty, wystąpień Zarezerwowanych, środki na korzystanie z lub inne typy opłaty nie są jeszcze obsługiwane.

## <a name="troubleshooting-aws-integration"></a>Rozwiązywanie problemów z integracją usługi AWS

Aby rozwiązać typowe problemy, użyj następujące informacje dotyczące rozwiązywania problemów.

### <a name="no-permission-to-aws-linked-accounts"></a>Brak uprawnień do konta usługi AWS połączone

Istnieją dwa sposoby uzyskania uprawnień do usług AWS, połączone konta koszty dostępu do:

- Uzyskaj dostęp do grupy zarządzania, który zawiera konta usług AWS połączone.
- Poproś kogoś, aby zezwolić na konto usługi AWS połączone.

Domyślnie twórca łącznika usługi AWS jest właścicielem wszystkich obiektów utworzonych przez łącznik. Tym AWS skonsolidowany konta i AWS połączone konta.

### <a name="collection-failed-with-assumerole"></a>Zbieranie nie powiodło się z AssumeRole

Ten błąd oznacza, że Cost Management jest w stanie wywołać interfejs API AssumeRole AWS. Ten problem może się zdarzyć z powodu problemu z definicji roli. Sprawdź, czy są spełnione następujące warunki:

- Identyfikator zewnętrzny jest taka sama jak w definicji roli i definicji łącznika.
- Typ roli jest ustawiona na **AWS innego konta należące do Ciebie lub innych firm 3.**
- **Wymagają usługi MFA** wyboru jest wyczyszczone.
- Zaufane konto usługi AWS w roli usługi AWS jest _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Zbieranie nie powiodło się z odmowa dostępu

Ten komunikat o błędzie oznacza, że Cost Management jest w stanie uzyskać dostęp do plików WALUTA, przechowywane w zasobniku Amazon S3. Upewnij się, że zasady JSON usługi AWS, dołączone do roli podobny przykład pokazany w dolnej części [tworzenie ról i zasad w usłudze AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) sekcji.

### <a name="connector-error-with-failedtofindreport"></a>Błąd łącznika z FailedToFindReport

Ten błąd oznacza, że Cost Management, nie można odnaleźć raportu kosztów i użycia, która została zdefiniowana w łączniku. Upewnij się, nie jest on usunięty i że zasad usług AWS JSON, dołączony do roli podobny przykład pokazany w dolnej części [tworzenie ról i zasad w usłudze AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) sekcji.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli użytkownik jeszcze nie skonfigurowano środowisko platformy Azure przy użyciu grup zarządzania, zobacz [początkowej instalacji grupy zarządzania](../governance/management-groups/index.md#initial-setup-of-management-groups).

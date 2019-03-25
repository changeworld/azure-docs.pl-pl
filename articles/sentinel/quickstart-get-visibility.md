---
title: Szybki Start Azure wartownik — wprowadzenie do platformy Azure przez wartownika w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Szybki Start Azure wartownik — wprowadzenie do platformy Azure przez wartownika
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: a80c4db1b81dd2bc0b223a2781e28ccb1f5ba68e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402885"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Szybki start: Rozpoczynanie pracy z usługą Azure przez wartownika w wersji zapoznawczej

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


W tym przewodniku Szybki Start dowiesz się, jak szybko można wyświetlać i monitorować, co się dzieje w całym środowisku przy użyciu platformy Azure przez wartownika. Po połączeniu swoich źródeł danych na platformie Azure przez wartownika uzyskasz natychmiastowy wizualizacji i analizy danych, dzięki czemu będzie wiadomo, co dzieje się we wszystkich źródłach danych połączonych. Wartownik platformy Azure udostępnia pulpity nawigacyjne, które udostępniają pełnych możliwości narzędzi dostępnych w programie Azure, jak również tabele i wykresy, które są wbudowane w platformę umożliwiają analizy dzienników i zapytań. Możesz użyć wbudowanych pulpitów nawigacyjnych lub utworzyć nowy pulpit nawigacyjny, od początku lub w oparciu o istniejący pulpit nawigacyjny. 

## <a name="get-visualization"></a>Pobieranie wizualizacji

Wizualizowanie i analizę, co się dzieje w środowisku, najpierw Przyjrzyj się pulpit nawigacyjny przeglądu, aby poznać stan zabezpieczeń swojej organizacji. Kliknięcie każdego elementu z tych kafelków, aby przejść do szczegółów danych pierwotnych, z którego są tworzone. Ułatwiające zmniejszenia szumu i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, Azure przez wartownika stosowana jest metoda fusion służące do skorelowania alerty w przypadkach. **Przypadków** grup powiązanych alertów, tworzone zdarzenie informacje z możliwością działania, które można zbadać i rozwiązać.

- W witrynie Azure portal wybierz pozycję Azure przez wartownika, a następnie wybierz obszar roboczy, który chcesz monitorować.

  ![Omówienie usługi Azure wartownik](./media/qs-get-visibility/overview.png)

- Pasek narzędzi w górnej części informujący o tym ile zdarzeń stało się w przedziale czasu wybrany i porównuje go do poprzednich 24 godzin. Pasek narzędzi informujący o tym z tych zdarzeń, alertów, które zostały wyzwolone (mała liczba reprezentuje zmiany w ciągu ostatnich 24 godzin), a następnie określa dla tych zdarzeń, ile jest otwarty, w toku i zakończonych. Zaznacz, aby zobaczyć, że nie ma znacznemu zwiększeniu lub drop w liczbie zdarzeń. W przypadku zrzutu, może to być, że połączenie raportowanie zatrzymane do platformy Azure przez wartownika. W przypadku zwiększenia podejrzane może mieć wystąpił problem. Sprawdź, czy masz nowe alerty.

   ![Usługa Azure lejka wartownik](./media/qs-get-visibility/funnel.png)

Głównej części strony Przegląd w skrócie zapewnia wgląd w stan zabezpieczeń obszaru roboczego:

- **Zdarzenia i alerty w czasie**: Wyświetla liczbę zdarzeń i ile alertów zostało utworzonych z tych zdarzeń. Jeśli widzisz kolekcji, będącego nietypowe, powinien zostać wyświetlony alerty dla niego — jeśli coś nietypowego gdy istnieje skok w zdarzeniach, ale nie widać alerty, może być zaniepokoić.

- **Potencjalnie złośliwy zdarzeń**: Po wykryciu ze źródeł, które są znane jako złośliwego ruchu przez wartownika Azure ostrzega użytkownika, na mapie. Jeśli widzisz pomarańczowy, jest ruch przychodzący: ktoś próbuje uzyskać dostęp do Twojej organizacji z znanego złośliwego adresu IP. Jeśli widzisz wychodzącego działania (czerwony), oznacza to, czy strumieniowego przesyłania danych z sieci użytkownika spoza Twojej organizacji do znanego złośliwego adresu IP.

   ![Mapy wartownik platformy Azure](./media/qs-get-visibility/map.png)


- **Ostatnie sprawy**: Aby wyświetlić swoje ostatnio przypadki, ich ważność i liczbę alertów skojarzonych z przypadkiem. Jeśli zostanie wyświetlony jako nagłe szczytowe w określonym typie alertu, może to oznaczać, że istnieje aktywny atak aktualnie uruchomione. Na przykład w przypadku nagłego szczytu 20 zdarzeń typu Pass--hash z narzędzia Azure ATP jest możliwe, czy inna obecnie próbuje ataków można.

- **Źródła danych anomalie**: Analitycy danych firmy Microsoft utworzone modele, które stale wyszukiwać dane ze swoich źródeł danych w poszukiwaniu anomalii. Jeśli nie ma żadnych anomalie, będą wyświetlane żadne informacje. W przypadku wykrycia anomalii, wykonaj następujące czynności głębokiego omówi je, aby zobaczyć, co się stało. Na przykład kliknij wzrost aktywności platformy Azure. Możesz kliknąć **wykresu** Aby wyświetlić podczas się stało z kolekcji, a następnie przefiltruj pod kątem działań, które wystąpiły w tym okresie czasu, aby zobaczyć, co spowodowało kolekcji.

   ![Mapy wartownik platformy Azure](./media/qs-get-visibility/anomolies.png)

## Używaj wbudowanych pulpitów nawigacyjnych<a name="dashboards"></a>

Wbudowane pulpity nawigacyjne zawierają zintegrowane dane z połączonych źródeł danych pozwala głębokiego Poznaj zdarzenia wygenerowane w tych usługach. Wbudowane pulpity nawigacyjne zawierają identyfikator platformy Azure, zdarzenia aktywności platformy Azure i środowisko lokalne, które mogą zawierać dane ze zdarzeń Windows z serwerów z pierwszym alertów innych firm, z żadnym podmiotom trzecim, w tym dzienniki ruchu zapory, usługi Office 365 i niezabezpieczonych protokołów oparte na Windows zdarzenia.

1. W obszarze **ustawienia**, wybierz opcję **pulpity nawigacyjne**. W obszarze **zainstalowane**, możesz zobaczyć wszystkie pulpity nawigacyjne zainstalowane. W obszarze **wszystkich** widać całą galerię wbudowane pulpity nawigacyjne, które są dostępne do zainstalowania. 
2. Wyszukiwanie określonego pulpitu nawigacyjnego wyświetlić całą listę i opisy co każdej oferty. 
3. Zakładając, że możesz używać usługi Azure AD, aby rozpocząć pracę i uruchamiana za pomocą platformy Azure przez wartownika, zaleca się zainstalowanie co najmniej poniższe pulpity nawigacyjne:
   - **Usługa Azure AD**: Użyj jednego lub obu z następujących czynności:
       - **Usługa Azure AD operacji logowania** analizuje logowania wraz z upływem czasu, aby zobaczyć, czy istnieją anomalii. Ten pulpit nawigacyjny zawiera Nieudane logowania przez aplikacje, urządzenia i lokalizacji, dzięki czemu można okaże się, w skrócie, jeśli się coś nietypowego dzieje. Należy zwrócić uwagę na wielu nieudanych logowań. 
       - **Dzienniki inspekcji usługi Azure AD** analizuje działania administracyjne, takie jak zmiany w obszarze Użytkownicy (Dodawanie, usuwanie, itp.), tworzenie grupy i modyfikacji.  

   - Dodaj pulpit nawigacyjny dla zapory. Na przykład dodać pulpit nawigacyjny Palo Alto. Pulpit nawigacyjny analizuje ruch sieciowy zapory, zapewniając korelacji między swoje dane i zagrożeniami zdarzenia zapory i wyróżnienie podejrzanych zdarzeń między jednostkami. Pulpity nawigacyjne dostarcza informacji na temat trendów w ruchu i pozwala przejść do szczegółów i wyfiltrować wyniki. 

      ![Pulpit nawigacyjny Alto PAL](./media/qs-get-visibility/palo-alto-week-query.png)


Pulpity nawigacyjne można dostosować przez edycję główne zapytanie ![przycisk](./media/qs-get-visibility/edit-query-button.png). Możesz kliknąć przycisk ![przycisk](./media/qs-get-visibility/go-to-la-button.png) można przejść do [Log Analytics, aby edytować zapytanie ma](../azure-monitor/log-query/get-started-portal.md), i można wybrać wielokropek (...) i wybierz **Dostosuj dane kafelka**, co pozwala Aby edytować filtr czasu głównego lub usuń określone Kafelki na pulpicie nawigacyjnym.

Aby uzyskać więcej informacji na temat pracy z zapytaniami, zobacz [samouczka: Danych wizualnych w usłudze Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Dodaj nowy Kafelek

Jeśli chcesz dodać nowy Kafelek, możesz dodać go do istniejącego pulpitu nawigacyjnego, taki, który tworzysz lub przez wartownika Azure wbudowany pulpit nawigacyjny. 
1. W usłudze Log Analytics należy utworzyć Kafelek za pomocą instrukcji zawartych w [samouczka: Danych wizualnych w usłudze Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Gdy Kafelek zostanie utworzona, w obszarze **numeru Pin**, wybierz pulpit nawigacyjny, w którym chcesz Kafelek, aby są wyświetlane.

## <a name="create-new-dashboards"></a>Utwórz nowe pulpity nawigacyjne
Można utworzyć nowy pulpit nawigacyjny od podstaw lub użyć wbudowany pulpit nawigacyjny jako podstawy dla nowego pulpitu nawigacyjnego.

1. Aby utworzyć nowy pulpit nawigacyjny od podstaw, wybierz **pulpity nawigacyjne** i następnie **+ nowy pulpit nawigacyjny**.
2. Wybierz subskrypcję, której pulpit nawigacyjny jest tworzony i nadaj mu nazwę opisową. Każdy pulpit nawigacyjny jest zasobem platformy Azure, takich jak każdy inny, i umożliwi jej przypisanie ról (RBAC), aby zdefiniować i Ogranicz, kto może uzyskiwać dostęp do. 
3. Aby włączyć, aby pojawiło się w celu przypinania wizualizacji do pulpitów nawigacyjnych, należy go udostępniać. Kliknij przycisk **udziału** i następnie **Zarządzanie użytkownikami**. 
 
1. Użyj **Sprawdź dostęp** i **przypisań ról** podobnie jak w przypadku innych zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [pulpitów nawigacyjnych platformy Azure udziału, przy użyciu RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Nowe przykłady pulpitu nawigacyjnego

Następujące przykładowe zapytanie można porównać trendy ruchu między tygodni. Można łatwo przełączać, które urządzenia dostawcy i źródła danych, uruchom zapytanie w. W tym przykładzie użyto SecurityEvent z Windows, możesz przełączyć go w AzureActivity lub CommonSecurityLog inne zapory.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Można utworzyć kwerendę, która zawiera dane ze źródeł wielokrotności. Można utworzyć kwerendę, która analizuje dzienniki inspekcji usługi Azure Active Directory dla nowych użytkowników, które właśnie zostały utworzone, a następnie sprawdza dzienniki subskrypcji platformy Azure, aby zobaczyć, jeśli użytkownik rozpoczął wprowadzania zmiany przypisania roli w ciągu 24 godzin tworzenia. Podejrzane działania będą wyświetlane na tym pulpicie nawigacyjnym:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Można utworzyć różnych pulpitów nawigacyjnych na podstawie roli osoby spojrzenie na dane i ich wyszukiwanie. Na przykład można utworzyć pulpit nawigacyjny administratorowi sieci, zawierająca dane zapory. Można również tworzyć pulpity nawigacyjne oparte na jak często chcesz przyjrzeć się im, czy istnieją czynności, które chcesz przejrzeć codziennie i inne elementy, które chcesz sprawdzić godzinę, na przykład możesz chcieć Przyjrzyj się logowań usługi Azure AD co godzinę do wyszukania anomali ES. 

## <a name="create-new-detections"></a>Utwórz nowe funkcje wykrywania

Generowanie wykrywania na [źródeł danych, które są podłączone do platformy Azure przez wartownika](connect-data-sources.md) do badanie zagrożeń w Twojej organizacji.

Kiedy tworzysz nowe wykrycie, wykorzystaj wbudowane funkcje wykrywania specjalnie przez badaczy zabezpieczeń firmy Microsoft, dostosowanych do źródeł danych, z którym nawiązano.

1. [W społeczności usługi GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) przejdź do **wykrywania** folder, a następnie wybierz odpowiednie foldery.
   ![odpowiednie foldery](./media/qs-get-visibility/detection-folders.png)
 
3.  Przejdź do **Analytics** kartę, a następnie wybierz pozycję **Dodaj**.
   ![Tworzenie reguły w usłudze Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Skopiuj wszystkie parametry reguły, a następnie kliknij przycisk **Utwórz**.
   ![Utwórz regułę alertu](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start przedstawiono sposób rozpocząć korzystanie z platformy Azure przez wartownika. Przejdź do samouczka dotyczącego [jak wykrywać zagrożenia](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Wykrywanie zagrożeń](tutorial-detect-threats.md) do automatyzowania odpowiedzi na zagrożenia.


---
title: Przewodnik Szybki Start dotyczący platformy Azure — wprowadzenie do wersji zapoznawczej platformy Azure Microsoft Docs
description: Przewodnik Szybki Start platformy Azure — wprowadzenie do platformy Azure
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: c6434b4f5808c349d5e0ab04dafc5233f6ec63ac
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780484"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Szybki start: Wprowadzenie do wersji zapoznawczej platformy Azure — Wersja zapoznawcza

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


W tym przewodniku szybki start dowiesz się, jak szybko przeglądać i monitorować działania wykonywane w danym środowisku za pomocą platformy Azure. Po nawiązaniu połączenia ze źródłami danych na platformie Azure wskażesz błyskawiczną wizualizację i analizę danych, aby dowiedzieć się, co się dzieje między wszystkimi połączonymi źródłami danych. Wskaźnik na platformie Azure to pulpity nawigacyjne zapewniające pełną moc narzędzi już dostępnych na platformie Azure, a także tabele i wykresy, które są wbudowane w celu zapewnienia analiz dla dzienników i zapytań. Możesz użyć wbudowanych pulpitów nawigacyjnych lub łatwo utworzyć nowy pulpit nawigacyjny, od podstaw lub na podstawie istniejącego pulpitu nawigacyjnego. 

## <a name="get-visualization"></a>Pobierz wizualizację

Aby wizualizować i uzyskać analizę tego, co dzieje się w środowisku, najpierw zapoznaj się z omówieniem pulpitu nawigacyjnego, aby uzyskać pomysł dotyczący zabezpieczeń stan organizacji. Możesz kliknąć każdy element tych kafelków, aby przejść do danych pierwotnych, z których zostały utworzone. Aby ułatwić zredukowanie szumu i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, wskaźnik platformy Azure używa techniki Fusion do skorelowania alertów w zdarzenia. **zdarzenia** to grupy powiązanych alertów, które razem tworzą incydent do działania, który można zbadać i rozwiązać.

- W Azure Portal wybierz pozycję wskaźnik kontroli platformy Azure, a następnie wybierz obszar roboczy, który chcesz monitorować.

  ![Przegląd wskaźnikowego platformy Azure](./media/qs-get-visibility/overview.png)

- Pasek narzędzi na górze informuje o liczbie zdarzeń w wybranym okresie i porównuje go z poprzednimi 24 godzinami. Pasek narzędzi informuje o tych zdarzeniach, które zostały wyzwolone (niewielka liczba reprezentuje zmianę w ciągu ostatnich 24 godzin), a następnie informuje o tych zdarzeniach, o liczbie otwartych, w toku i zamknięciu. Sprawdź, czy liczba zdarzeń nie ma znaczącego wzrostu ani porzucenia. Jeśli jest to możliwe, może to oznaczać, że połączenie zostało zatrzymane na potrzeby raportowania na platformie Azure. Jeśli wystąpi wzrost, wystąpił problem podejrzany. Sprawdź, czy masz nowe alerty.

   ![Lejki wskaźnikowe platformy Azure](./media/qs-get-visibility/funnel.png)

Główna treść strony Przegląd zapewnia wgląd w informacje o stanie zabezpieczeń obszaru roboczego:

- **Zdarzenia i alerty w czasie**: Wyświetla listę liczby zdarzeń oraz liczbę alertów, które zostały utworzone na podstawie tych zdarzeń. Jeśli widzisz, że najprawdopodobniej wystąpił nietypowy sposób, zobaczysz dla niego alerty — Jeśli coś się nie dzieje w przypadku wystąpienia w zdarzeniach, ale nie widzisz alertów, może to być przyczyną problemu.

- **Potencjalne złośliwe zdarzenia**: W przypadku wykrycia ruchu ze źródeł, które są znane jako złośliwe, wskaźnik platformy Azure będzie ostrzegał o mapie. Jeśli widzisz kolor pomarańczowy, jest to ruch przychodzący: ktoś próbuje uzyskać dostęp do organizacji przy użyciu znanego złośliwego adresu IP. Jeśli zostanie wyświetlone działanie wychodzące (czerwone), oznacza to, że dane z sieci są przesyłane strumieniowo z organizacji do znanego złośliwego adresu IP.

   ![Mapa wskaźnikowa platformy Azure](./media/qs-get-visibility/map.png)


- **Ostatnie zdarzenia**: Wyświetlanie ostatnich zdarzeń, ich ważności i liczby alertów skojarzonych ze zdarzeniem. Jeśli widzisz jako nagły szczyt w określonym typie alertu, może to oznaczać, że jest aktualnie uruchomiony aktywny atak. Na przykład w przypadku nagłego szczytu 20 zdarzeń typu Pass-the-hash z usługi Azure ATP istnieje możliwość, że ktoś aktualnie próbuje się zaatakować.

- **Anomalie źródła danych**: Analitycy danych firmy Microsoft tworzą modele, które stale przeszukają dane ze źródeł danych pod kątem anomalii. Jeśli nie ma żadnych anomalii, nic nie zostanie wyświetlone. W przypadku wykrycia anomalii należy szczegółowo szczegółowe je, aby zobaczyć, co się stało. Na przykład kliknij pozycję skok w działaniu platformy Azure. Możesz kliknąć **Wykres** , aby zobaczyć, kiedy nastąpiło przekroczenie, a następnie odfiltrować działania, które wystąpiły w tym okresie, aby zobaczyć, co spowodowało skok.

   ![Mapa wskaźnikowa platformy Azure](./media/qs-get-visibility/anomolies.png)

## Korzystanie z wbudowanych pulpitów nawigacyjnych<a name="dashboards"></a>

Wbudowane pulpity nawigacyjne zapewniają zintegrowane dane z połączonych źródeł danych, aby umożliwić głębokie szczegółowe zdarzeń generowanych w ramach tych usług. Wbudowane pulpity nawigacyjne obejmują identyfikator platformy Azure, zdarzenia aktywności platformy Azure i lokalne, które mogą być danymi ze zdarzeń systemu Windows z serwerów, od alertów z pierwszej strony, w tym dzienników ruchu zapory, pakietu Office 365 i niezabezpieczonych protokołów opartych na systemie Windows wydarzeniach.

1. W obszarze **Ustawienia**wybierz pozycję **pulpity nawigacyjne**. W obszarze **zainstalowane**są widoczne wszystkie zainstalowane pulpity nawigacyjne. W obszarze **wszystko** można zobaczyć całą galerię wbudowanych pulpitów nawigacyjnych, które są dostępne do zainstalowania. 
2. Wyszukaj konkretny pulpit nawigacyjny, aby wyświetlić całą listę i opis każdej oferty. 
3. Przy założeniu, że używasz usługi Azure AD, aby rozpocząć korzystanie z platformy Azure, zalecamy zainstalowanie co najmniej następujących pulpitów nawigacyjnych:
   - **Usługa Azure AD**: Użyj jednego lub obu następujących elementów:
       - **Logowania za pomocą usługi Azure AD** analizują logowania w czasie, aby sprawdzić, czy występują anomalie. Ten pulpit nawigacyjny zawiera nieudane logowania w aplikacjach, urządzeniach i lokalizacjach, dzięki czemu można natychmiast zauważyć, jeśli coś się nie zdarza. Zwróć uwagę na wiele nieudanych logowań. 
       - **Dzienniki inspekcji usługi Azure AD** analizują działania administracyjne, takie jak zmiany użytkowników (Dodawanie, usuwanie itp.), tworzenie grup i modyfikacje.  

   - Dodaj pulpit nawigacyjny dla zapory. Na przykład Dodaj pulpit nawigacyjny Palo Alto. Pulpit nawigacyjny analizuje ruch związany z zaporą, zapewniając korelacje między danymi zapory i zdarzeniami zagrożeń oraz wyróżnia podejrzane zdarzenia w różnych jednostkach. Pulpity nawigacyjne zapewniają informacje o trendach w ruchu i umożliwiają przechodzenie do szczegółów i filtrowanie wyników. 

      ![PAL Alto Dashboard](./media/qs-get-visibility/palo-alto-week-query.png)


Możesz dostosować pulpity nawigacyjne, edytując przycisk ![](./media/qs-get-visibility/edit-query-button.png)głównej kwerendy. Możesz ![kliknąć przycisk](./media/qs-get-visibility/go-to-la-button.png) przycisk, aby przejść do log Analytics, [Aby edytować zapytanie](../azure-monitor/log-query/get-started-portal.md)w tym miejscu i można wybrać wielokropek (...) i wybrać opcję **Dostosuj dane kafelka**, co umożliwia edytowanie filtru czasu głównego lub usuwanie określone kafelki z pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na temat pracy z zapytaniami, zobacz [samouczek: Dane wizualne w Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Dodawanie nowego kafelka

Jeśli chcesz dodać nowy kafelek, możesz dodać go do istniejącego pulpitu nawigacyjnego, jednego z nich lub wbudowanego pulpitu nawigacyjnego platformy Azure. 
1. W log Analytics Utwórz kafelek przy użyciu instrukcji znalezionych w [samouczku: Dane wizualne w Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Po utworzeniu kafelka w obszarze **Przypnij**wybierz Pulpit nawigacyjny, w którym ma być wyświetlany kafelek.

## <a name="create-new-dashboards"></a>Utwórz nowe pulpity nawigacyjne
Możesz utworzyć nowy pulpit nawigacyjny od podstaw lub użyć wbudowanego pulpitu nawigacyjnego jako podstawy dla nowego pulpitu nawigacyjnego.

1. Aby utworzyć nowy pulpit nawigacyjny od podstaw, wybierz pulpity nawigacyjne, a następnie pozycję **+ nowy pulpit nawigacyjny**.
2. Wybierz subskrypcję, w której jest tworzony pulpit nawigacyjny, i nadaj jej nazwę opisową. Każdy pulpit nawigacyjny jest zasobem platformy Azure, podobnie jak inne, i można przypisać role IT (RBAC) w celu zdefiniowania i ograniczenia dostępu do nich. 
3. Aby umożliwić jej wyświetlanie na pulpitach nawigacyjnych w celu przypinania wizualizacji do, należy ją udostępnić. Kliknij przycisk **Udostępnij** , a następnie **Zarządzaj użytkownikami**. 
 
1. Użyj **uprawnień Sprawdź dostęp** i **rola** , tak jak w przypadku innych zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Przykłady nowych pulpitów nawigacyjnych

Poniższe przykładowe zapytanie umożliwia porównanie trendów ruchu w tygodniach. Można łatwo przełączać dostawcę urządzenia i źródło danych, na których uruchomiono zapytanie. W tym przykładzie używamy SecurityEvent z systemu Windows, możesz przełączyć go do uruchamiania w usłudze Azure lub CommonSecurityLog na dowolnej innej zaporze.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Możesz chcieć utworzyć zapytanie, które zawiera dane z wielu źródeł. Można utworzyć zapytanie, które przeszukuje Azure Active Directory dzienniki inspekcji dla nowo utworzonych użytkowników, a następnie sprawdza dzienniki platformy Azure, aby sprawdzić, czy użytkownik rozpoczął wprowadzanie zmian przypisań ról w ciągu 24 godzin od utworzenia. Podejrzane działanie będzie widoczne na tym pulpicie nawigacyjnym:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Możesz tworzyć różne pulpity nawigacyjne w oparciu o rolę osoby, która przegląda dane i czego szukają. Na przykład możesz utworzyć pulpit nawigacyjny dla administratora sieci, który zawiera dane zapory. Możesz również tworzyć pulpity nawigacyjne w zależności od tego, jak często chcesz je przeglądać, czy istnieją jakieś rzeczy, które chcesz przejrzeć codziennie, i inne elementy, które chcesz sprawdzić po godzinie, na przykład możesz chcieć zajrzeć na logowanie do usługi Azure AD co godzinę, aby wyszukać anomali AK. 

## <a name="create-new-detections"></a>Utwórz nowe wykrycia

Generuj wykrycia dla [źródeł danych połączonych z platformą Azure wskaźnikiem,](connect-data-sources.md) aby zbadać zagrożenia w organizacji.

Podczas tworzenia nowego wykrywania należy skorzystać z wbudowanych wykryć spreparowanych przez badaczy zabezpieczeń firmy Microsoft, które są dostosowane do połączonych źródeł danych.

1. [W społeczności usługi GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) przejdź do folderu **Detects** , a następnie wybierz odpowiednie foldery.
   ![odpowiednie foldery](./media/qs-get-visibility/detection-folders.png)
 
3.  Przejdź do karty **Analiza** i wybierz pozycję **Dodaj**.
   ![Utwórz regułę w Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Skopiuj wszystkie parametry do reguły, a następnie kliknij przycisk **Utwórz**.
   ![Utwórz regułę alertu](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start pokazano, jak rozpocząć korzystanie z platformy Azure. Przejdź do samouczka dotyczącego [sposobu wykrywania zagrożeń](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Wykrywaj zagrożenia](tutorial-detect-threats.md) , aby zautomatyzować swoje odpowiedzi na zagrożenia.


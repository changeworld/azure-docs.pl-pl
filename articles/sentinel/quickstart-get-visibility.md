---
title: 'Szybki start: wprowadzenie do usługi Azure Sentinel'
description: Szybki start usługi Azure Sentinel — wprowadzenie do usługi Azure Sentinel
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 95dcc135593c566eb1319ed52df3df6c1ada6609
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067680"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Szybki start: wprowadzenie do usługi Azure Sentinel




W tym przewodniku Szybki start dowiesz się, jak szybko wyświetlać i monitorować, co dzieje się w twoim środowisku za pomocą usługi Azure Sentinel. Po połączeniu źródeł danych z usługą Azure Sentinel otrzymujesz natychmiastową wizualizację i analizę danych, dzięki czemu możesz wiedzieć, co dzieje się we wszystkich połączonych źródłach danych. Usługa Azure Sentinel udostępnia skoroszyty, które zapewniają pełną moc narzędzi już dostępnych na platformie Azure, a także tabele i wykresy, które są wbudowane, aby zapewnić ci analizę dzienników i zapytań. Możesz użyć wbudowanych skoroszytów lub łatwo utworzyć nowy skoroszyt od podstaw lub na podstawie istniejącego skoroszytu. 

## <a name="get-visualization"></a>Uzyskaj wizualizację

Aby wizualizować i uzyskać analizę tego, co dzieje się w środowisku, najpierw zapoznaj się z pulpitem nawigacyjnym przeglądu, aby zorientować się w postawie bezpieczeństwa w organizacji. Możesz kliknąć na każdy element tych kafelków, aby przejść do szczegółów do nieprzetworzonych danych, z których są tworzone. Aby zmniejszyć hałas i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, usługa Azure Sentinel używa techniki fuzji w celu skorelowania alertów z zdarzeniami. **zdarzenia** to grupy powiązanych alertów, które razem tworzą zdarzenie, które można zbadać i rozwiązać.

- W witrynie Azure portal wybierz pozycję Azure Sentinel, a następnie wybierz obszar roboczy, który chcesz monitorować.

  ![Omówienie usługi Azure Sentinel](./media/qs-get-visibility/overview.png)

- Pasek narzędzi u góry informuje, ile zdarzeń masz w wybranym okresie czasu i porównuje je z poprzednimi 24 godzinami. Pasek narzędzi informuje o tych zdarzeniach, alerty, które zostały wyzwolone (mała liczba reprezentuje zmiany w ciągu ostatnich 24 godzin), a następnie informuje o tych zdarzeniach, ile są otwarte, w toku i zamknięte. Sprawdź, czy nie ma dramatycznego wzrostu lub spadku liczby zdarzeń. Jeśli występuje spadek, może to być, że połączenie przestał raportowania do usługi Azure Sentinel. Jeśli nastąpi wzrost, mogło dojść do czegoś podejrzanego. Sprawdź, czy masz nowe alerty.

   ![Lejek Wartownik azure](./media/qs-get-visibility/funnel.png)

Główna treść strony przeglądu daje wgląd w stan zabezpieczeń obszaru roboczego:

- **Zdarzenia i alerty w czasie:** Wyświetla liczbę zdarzeń i liczbę alertów utworzonych z tych zdarzeń. Jeśli widzisz skok, który jest nietypowy, powinieneś zobaczyć alerty dla niego - jeśli jest coś niezwykłego, gdzie jest skok zdarzeń, ale nie widzisz alertów, może to być powodem do niepokoju.

- **Potencjalne szkodliwe zdarzenia:** Po wykryciu ruchu ze źródeł, o których wiadomo, że są złośliwe, usługa Azure Sentinel ostrzega cię na mapie. Jeśli widzisz pomarańczowy, jest to ruch przychodzący: ktoś próbuje uzyskać dostęp do twojej organizacji ze znanego złośliwego adresu IP. Jeśli widzisz działanie wychodzące (czerwone), oznacza to, że dane z sieci są przesyłane strumieniowo z organizacji do znanego złośliwego adresu IP.

   ![Mapa azure sentinel](./media/qs-get-visibility/map.png)


- **Ostatnie zdarzenia**: Aby wyświetlić ostatnie zdarzenia, ich dotkliwość i liczbę alertów związanych z incydentem. Jeśli widzisz jako nagły szczyt w określonym typie alertu, może to oznaczać, że jest obecnie uruchomiony aktywny atak. Na przykład jeśli masz nagły szczyt 20 pass-the-hash zdarzeń z usługi Azure ATP, jest możliwe, że ktoś jest obecnie próbuje zaatakować.

- **Anomalie źródła danych:** analitycy danych firmy Microsoft stworzyli modele, które stale przeszukują dane ze źródeł danych w poszukiwaniu anomalii. Jeśli nie ma żadnych anomalii, nic nie jest wyświetlane. Jeśli zostaną wykryte anomalie, należy głęboko zagłębić się w nich, aby zobaczyć, co się stało. Na przykład kliknij na skok w usłudze Azure Activity. Możesz kliknąć **na wykresie,** aby zobaczyć, kiedy nastąpił skok, a następnie filtrować działania, które wystąpiły w tym okresie, aby zobaczyć, co spowodowało skok.

   ![Mapa azure sentinel](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Używanie wbudowanych skoroszytów<a name="dashboards"></a>

Wbudowane skoroszyty zapewniają zintegrowane dane z połączonych źródeł danych, aby umożliwić głębokie zagłębienie się w zdarzenia generowane w tych usługach. Wbudowane skoroszyty obejmują usługę Azure AD, zdarzenia aktywności platformy Azure i lokalne, które mogą być danymi z zdarzeń systemu Windows z serwerów, alertów innych firm, z dowolnego stron trzecich, w tym dzienników ruchu zapory, usługi Office 365 i niezabezpieczonych protokołów opartych na systemie Windows Zdarzenia. Skoroszyty są oparte na skoroszytach usługi Azure Monitor, aby zapewnić ci większą elastyczność dostosowywania i elastyczność w projektowaniu własnego skoroszytu. Aby uzyskać więcej informacji, zobacz [Skoroszyty](../azure-monitor/app/usage-workbooks.md).

1. W obszarze **Ustawienia**wybierz pozycję **Skoroszyty**. W obszarze **Zainstalowane**można wyświetlić cały zainstalowany skoroszyt. W obszarze **Wszystkie**można wyświetlić całą galerię wbudowanych skoroszytów, które są dostępne do zainstalowania. 
2. Wyszukaj określony skoroszyt, aby wyświetlić całą listę i opis tego, co oferuje każdy z nich. 
3. Zakładając, że używasz usługi Azure AD, aby rozpocząć pracę z usługą Azure Sentinel, zaleca się zainstalowanie co najmniej następujących skoroszytów:
   - **Usługa Azure AD:** Użyj jednej lub obu następujących czynności:
       - **Logowania usługi Azure AD** analizuje logowania w czasie, aby sprawdzić, czy istnieją anomalie. Ten skoroszyt udostępnia nieudane logowania przez aplikacje, urządzenia i lokalizacje, dzięki czemu można zauważyć, na pierwszy rzut oka, jeśli dzieje się coś niezwykłego. Należy zwrócić uwagę na wiele nieudanych logów. 
       - **Dzienniki inspekcji usługi Azure AD** analizuje działania administratora, takie jak zmiany w użytkownikach (dodawanie, usuwanie itp.), tworzenie grup i modyfikacje.  

   - Dodawanie skoroszytu zapory. Na przykład dodaj skoroszyt Palo Alto. Skoroszyt analizuje ruch zapory, zapewniając korelacje między danymi zapory a zdarzeniami zagrożeń i wyróżnia podejrzane zdarzenia między jednostkami. Skoroszyty dostarczają informacji o trendach w ruchu drogowym oraz umożliwiają przechodzenie do szczegółów i filtrowanie wyników. 

      ![Panel pal alto](./media/qs-get-visibility/palo-alto-week-query.png)


Skoroszyty można dostosować, edytując przycisk ![](./media/qs-get-visibility/edit-query-button.png)zapytania głównego . Możesz kliknąć ![przycisk,](./media/qs-get-visibility/go-to-la-button.png) aby przejść do [usługi Log Analytics, aby edytować kwerendę,](../azure-monitor/log-query/get-started-portal.md)a następnie wybrać wielokropek (...) i wybrać opcję Dostosuj dane **kafelka**, który umożliwia edycję głównego filtru czasu lub usunięcie określonych kafelków ze skoroszytu.

Aby uzyskać więcej informacji na temat pracy z kwerendami, zobacz [Samouczek: Dane wizualne w usłudze Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Dodawanie nowego kafelka

Jeśli chcesz dodać nowy kafelek, możesz dodać go do istniejącego skoroszytu, który tworzysz lub do wbudowanego skoroszytu Usługi Azure Sentinel. 
1. W usłudze Log Analytics utwórz kafelek, korzystając z instrukcji znalezionych w [samouczku: Dane wizualne w usłudze Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Po utworzeniu kafelka w obszarze **Przypnij**zaznacz skoroszyt, w którym ma się pojawić kafelek.

## <a name="create-new-workbooks"></a>Tworzenie nowych skoroszytów
Możesz utworzyć nowy skoroszyt od podstaw lub użyć wbudowanego skoroszytu jako podstawy nowego skoroszytu.

1. Aby utworzyć nowy skoroszyt od podstaw, wybierz **skoroszyty,** a następnie **+Nowy skoroszyt**.
2. Wybierz subskrypcję, w jakiej skoroszyt jest tworzony, i nadaj mu opisową nazwę. Każdy skoroszyt jest zasobem platformy Azure, jak każdy inny i można przypisać go role (RBAC) do definiowania i ograniczania, kto może uzyskać dostęp. 
3. Aby włączyć wyświetlanie go w skoroszytach, do których można przypiąć wizualizacje, musisz je udostępnić. Kliknij **pozycję Udostępnij,** a następnie **pozycję Zarządzaj użytkownikami**. 
 
1. Użyj **sprawdź przydziałów dostępu** i **roli,** jak w przypadku innych zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [Udostępnianie skoroszytów platformy Azure przy użyciu funkcji RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Przykłady nowych skoroszytów

Poniższa przykładowa kwerenda umożliwia porównywanie trendów ruchu w ciągu kilku tygodni. Można łatwo przełączyć dostawcę urządzenia i źródło danych, na którym uruchamiasz kwerendę. W tym przykładzie użyto SecurityEvent z systemu Windows, można przełączyć go do uruchomienia na AzureActivity lub CommonSecurityLog na dowolnej innej zaporze.

     |where DeviceVendor == "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Można utworzyć kwerendę, która zawiera dane ze źródeł wielokrotności. Można utworzyć kwerendę, która analizuje dzienniki inspekcji usługi Azure Active Directory dla nowych użytkowników, które właśnie zostały utworzone, a następnie sprawdza dzienniki platformy Azure, aby sprawdzić, czy użytkownik rozpoczął wprowadzanie zmian przypisania ról w ciągu 24 godzin od utworzenia. Ta podejrzana aktywność pojawi się na tym pulpicie nawigacyjnym:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Możesz tworzyć różne skoroszyty na podstawie roli osoby patrzącej na dane i tego, czego szukają. Można na przykład utworzyć skoroszyt dla administratora sieci zawierający dane zapory. Można również tworzyć skoroszyty na podstawie częstotliwości, jak często chcesz na nie patrzeć, czy są rzeczy, które chcesz codziennie przeglądać, oraz inne elementy, które chcesz sprawdzić raz na godzinę, na przykład możesz chcieć przeglądać logowania usługi Azure AD co godzinę, aby wyszukać anomalie . 

## <a name="create-new-detections"></a>Tworzenie nowych wykrywania

Generowanie wykrywania w [źródłach danych połączonych z usługą Azure Sentinel](connect-data-sources.md) w celu zbadania zagrożeń w organizacji.

Podczas tworzenia nowego wykrywania należy korzystać z wbudowanych wykrywania spreparowanych przez badaczy zabezpieczeń firmy Microsoft, które są dostosowane do połączonych źródeł danych.

Aby wyświetlić wszystkie out-of-the-box wykrywania, przejdź do **Analytics,** a następnie **reguły szablonów**. Ta karta zawiera wszystkie wbudowane reguły usługi Azure Sentinel.

   ![Używanie wbudowanych funkcji wykrywania w celu znajdowania zagrożeń za pomocą usługi Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Aby uzyskać więcej informacji na temat wykrywania out-of-the-box, zobacz [Samouczek: Uzyskiwanie wbudowanej analizy](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start dowiesz się, jak rozpocząć korzystanie z usługi Azure Sentinel. Przejdź do [samouczka, jak wykryć zagrożenia](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Utwórz niestandardowe reguły wykrywania zagrożeń,](tutorial-detect-threats-custom.md) aby zautomatyzować reagowanie na zagrożenia.


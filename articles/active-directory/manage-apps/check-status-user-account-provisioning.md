---
title: Raporty dotyczące usługi Azure Active Directory użytkownika automatyczne Inicjowanie obsługi administracyjnej konta do aplikacji SaaS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić stan inicjowania obsługi zadań konta użytkowników i jak rozwiązywać problemy z aprowizacji poszczególnych użytkowników.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70ca1e2f4fd831619cc3cd443d98018a35f4e1ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963077"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Samouczek: Raporty dotyczące aprowizacja kont użytkowników


Usługa Azure Active Directory obejmuje [konta użytkownika usługi aprowizacji](user-provisioning.md) który ułatwia automatyzację inicjowania obsługi anulowania obsługi administracyjnej kont użytkowników w aplikacji SaaS i innych systemów, na potrzeby cyklu życia tożsamości end-to-end Zarządzanie. Usługa Azure AD obsługuje wstępnie zintegrowanych użytkownika aprowizacji łączniki dla wszystkich aplikacji i systemów, w sekcji "Proponowany" [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

W tym artykule opisano, jak sprawdzić stan zadań inicjowania obsługi administracyjnej po zostało skonfigurowane i jak rozwiązywać problemy z inicjowania obsługi poszczególnych użytkowników i grup.

## <a name="overview"></a>Omówienie

Inicjowania obsługi administracyjnej łączniki są, konfigurowanie i skonfigurować za pomocą [witryny Azure portal](https://portal.azure.com), postępując zgodnie z [podano dokumentację](../saas-apps/tutorial-list.md) dla obsługiwanych aplikacji. Po skonfigurowaniu i uruchomiona, inicjowanie obsługi administracyjnej zadania mogą zostać zgłoszone przy użyciu jednej z dwóch metod:

* **Portal zarządzania systemu Azure** — w tym artykule opisano przede wszystkim, trwa pobieranie informacji w raporcie z [witryny Azure portal](https://portal.azure.com), co zapewnia zarówno aprowizacji raportu podsumowania jak i udostępniania szczegółowych dzienniki inspekcji dla danej aplikacji.

* **Interfejs API inspekcji** — Azure Active Directory oferuje także inspekcji interfejsu API, który umożliwia programowe pobieranie szczegółowe dzienniki inspekcji inicjowania obsługi administracyjnej. Zobacz [inspekcji usługi Azure Active Directory, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) dokumentacji dotyczące korzystania z tego interfejsu API. Chociaż w tym artykule nie szczegółowo opisano, jak za pomocą interfejsu API, szczegółowo opisują rodzaje inicjowania obsługi zdarzeń, które są rejestrowane w dzienniku inspekcji.

### <a name="definitions"></a>Definicje

W tym artykule używane następujące terminy, określonych poniżej:

* **Źródłowy System** — repozytorium użytkowników, którzy inicjowania obsługi administracyjnej usługi AD Azure synchronizuje z. Usługa Azure Active Directory jest system źródłowy dla większości wstępnie zintegrowanych łączników inicjowania obsługi administracyjnej, jednak istnieją pewne wyjątki (przykład: WORKDAY synchronizacji ruchu przychodzącego).

* **Docelowy System** — repozytorium użytkowników, którzy synchronizuje usługi aprowizacji usługi Azure AD. Zazwyczaj jest to aplikacja SaaS (przykłady: SalesForce, ServiceNow, usługi G Suite, usługa Dropbox dla firm), ale w niektórych przypadkach może być systemu lokalnego, takich jak usługi Active Directory (przykład: WORKDAY synchronizacji ruchu przychodzącego do usługi Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Wprowadzenie udostępniania raportów z portalu zarządzania systemu Azure

Pobierz zainicjowanie obsługi informacji w raporcie dla danej aplikacji, należy uruchomić, uruchamiając [portalu zarządzania systemu Azure](https://portal.azure.com) i przechodząc do aplikacji przedsiębiorstwa, dla której skonfigurowano usługę aprowizacji. Na przykład w przypadku udostępniania użytkownikom do wyniesienia rozgrywek LinkedIn jest ścieżka nawigacji, aby szczegóły aplikacji:

**Usługa Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje > LinkedIn podnieść poziom**

W tym miejscu są dostępne raport z podsumowaniem aprowizacji i inicjowania obsługi dzienników inspekcji, oba opisane poniżej.


## <a name="provisioning-summary-report"></a>Inicjowanie obsługi administracyjnej raport z podsumowaniem

Raport z podsumowaniem inicjowania obsługi administracyjnej jest widoczna w **aprowizacji** kartę dla danej aplikacji. Znajduje się w **szczegóły synchronizacji** sekcji poniżej **ustawienia**i zawiera następujące informacje:

* Całkowita liczba użytkowników i / grup, zostały zsynchronizowane i są obecnie dostępne w zakresie aprowizacji między systemem źródłowego i docelowego systemu.

* Czas ostatniej synchronizacji zostało uruchomione. Zazwyczaj odbyła się co 20 – 40 minut, po [początkowej synchronizacji](user-provisioning.md#what-happens-during-provisioning) zostało zakończone.

* Określa, czy [początkowej synchronizacji](user-provisioning.md#what-happens-during-provisioning) zostało ukończone.

* Określa, czy Proces inicjowania obsługi został umieszczony w kwarantannie, a Przyczyna stanu kwarantanny jest (na przykład błąd komunikacji z systemu docelowego, z powodu poświadczeń Nieprawidłowa wartość pola administrator).

Raport z podsumowaniem inicjowania obsługi administracyjnej powinna być pierwsze spojrzenie Administratorzy miejsca, aby sprawdzić kondycję operacyjną zadanie inicjowania obsługi administracyjnej.

 ![Raport z podsumowaniem](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Dzienniki inspekcji inicjowania obsługi administracyjnej
Wszystkie działania wykonywane przez usługę aprowizacji są rejestrowane w dziennikach inspekcji usługi Azure AD, które mogą być wyświetlane w **dzienniki inspekcji** karcie **Inicjowanie obsługi administracyjnej konta** kategorii. Typy zdarzeń zarejestrowanych działań:

* **Importuj zdarzenia** — "import" zdarzenie jest rejestrowane każdorazowo inicjowania obsługi administracyjnej usługi AD Azure pobiera informacje o poszczególnych użytkowników i grup z systemu źródłowego lub docelowego systemu. Podczas synchronizacji użytkownicy są pobierane z systemu źródłowego najpierw z wynikami rejestrowane jako "Importuj" zdarzenia. Zgodne identyfikatory użytkowników pobrane następnie są wyszukiwane względem systemu docelowego, aby sprawdzić, czy istnieją one z wynikami zarejestrowany jako "Importuj" zdarzenia. Te zdarzenia rejestrować wszystkie atrybuty zamapowanych użytkowników i ich wartości, które były widoczne dla usługi Azure AD usługi aprowizacji w momencie wystąpienia zdarzenia. 

* **Zdarzenia reguły synchronizacji** — te zdarzenia sporządzić raport na temat wyników są reguły mapowania atrybutów i filtrów określania zakresu dowolne skonfigurowane po użytkownika dane zostały zaimportowane i wykonywane począwszy od systemów źródłowych i docelowych. Na przykład jeśli użytkownik w systemie źródłowym jest uważana za będącą w zakresie udostępniania i uważa się, że nie istnieje w systemie docelowym, a następnie rejestruje tego zdarzenia, które użytkownik będzie gotowa w systemie docelowym. 

* **Eksportuj zdarzenia** — "export" zdarzenie jest rejestrowane każdorazowo usługi aprowizacji usługi Azure AD zapisuje obiekt konta lub grupy użytkowników systemu docelowego. Te zdarzenia rejestruje wszystkie atrybuty użytkownika i ich wartości, które zostały napisane przez usługę Azure AD usługi aprowizacji w czasie zdarzenia. Jeśli wystąpił błąd podczas zapisywania obiektu konta lub grupy użytkowników do systemu docelowego, która będzie wyświetlana w tym miejscu.

* **Przetwarzanie depozytu zdarzeń** -Escrow procesu — wystąpienia usługi aprowizacji napotka błąd podczas próby wykonania operacji, gdy rozpoczyna się ponowić próbę wykonania operacji na interwał wycofywania czasu. Zdarzenie "depozytu" jest rejestrowana w każdym razem, gdy prób ponownego wykonania operacji inicjowania obsługi administracyjnej.

Po wyświetleniu inicjowania obsługi zdarzeń dla poszczególnych użytkowników, zdarzenia są zwykle zachodzą w następującej kolejności:

1. Importuj zdarzenia: Użytkownik jest pobierana z systemu źródłowego.

2. Importuj zdarzenia: System docelowy zostaje przesłane zapytanie pod kątem istnienia pobrane użytkownika.

3. Zdarzenia reguły synchronizacji: Dane użytkownika z systemów źródłowych i docelowych są sprawdzane pod kątem skonfigurowanych reguł Mapowanie atrybutów i filtrów określania zakresu, aby określić, jaką akcję, należy wykonać.

4. Eksportuj zdarzenia: Jeśli określone zdarzenia reguły synchronizacji, czy akcja powinna być wykonana (Add, Update, Delete), a następnie wyniki akcji są rejestrowane w zdarzeniu eksportu.

![Tworzenie użytkownika testowego usługi Azure AD](./media/check-status-user-account-provisioning/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Trwa wyszukiwanie inicjowania obsługi zdarzeń dla określonego użytkownika

To najbardziej typowy przypadek użycia, w przypadku inicjowania obsługi dzienników inspekcji do sprawdzania stanu obsługi administracyjnej konta użytkownika. Aby wyszukać ostatniego zdarzenia inicjowania obsługi administracyjnej dla określonego użytkownika:

1. Przejdź do **dzienniki inspekcji** sekcji.

2. Z **kategorii** menu, wybierz opcję **Inicjowanie obsługi administracyjnej konta**.

3. W **zakres dat** menu, wybierz zakres dat, który chcesz wyszukać.

4. W **wyszukiwania** paska, wprowadź identyfikator użytkownika chcesz wyszukać. Format wartości Identyfikator powinien być zgodny w dowolnie wybrany jako podstawowy identyfikator dopasowania w konfiguracji mapowanie atrybutu (na przykład, userPrincipalName lub pracowniku numer identyfikacyjny). Wartość Identyfikatora, wymagane będzie widoczny w kolumnie cele.

5. Naciśnij klawisz Enter, aby wyszukać. Najpierw zostanie zwrócony najnowszych zdarzeń, inicjowania obsługi administracyjnej.

6. Jeśli zdarzenia są zwracane, należy pamiętać, typy działań oraz tego, czy zakończonych powodzeniem lub niepowodzeniem. Jeśli żadne wyniki nie zostaną zwrócone, następnie oznacza użytkownik nie istnieje albo nie jeszcze został wykryty w procesie aprowizacji w przypadku pełnej synchronizacji nie zostało jeszcze zakończone.

7. Kliknij poszczególne zdarzenia, aby wyświetlić szczegółowe, w tym wszystkich właściwości użytkownika, które zostały pobrane, ocenione lub zapisywane jako część zdarzenia.

Aby uzyskać pokaz dotyczący sposobu użycia dzienniki inspekcji Zobacz poniższy klip wideo. Dzienniki inspekcji są prezentowane w całym 5:30 oznaczyć:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Porady dotyczące wyświetlania inicjowania obsługi dzienników inspekcji

Najlepsze czytelności w witrynie Azure portal, wybierz **kolumn** przycisk, a następnie wybierz następujące kolumny:

* **Data** — Pokazuje datę, wystąpiło zdarzenie.
* **Obiekty docelowe** — pokazuje aplikacji nazwy i Identyfikatora użytkownika, są tematy zdarzenia.
* **Działanie** — typ działania, zgodnie z wcześniejszym opisem.
* **Stan** — czy wydarzenie zakończyło się pomyślnie, czy nie.
* **Przyczyna stanu** — podsumowanie co wydarzyło się w przypadku inicjowania obsługi administracyjnej.


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Inicjowania obsługi administracyjnej podsumowania raport i dzienniki inspekcji odgrywają kluczową rolę pomaga rozwiązać problemy dotyczące aprowizacji różne konta administratorów.

Na podstawie scenariusza wskazówki na temat rozwiązywania problemów automatycznej aprowizacji użytkowników w temacie [problemy z konfigurowaniem i aprowizowaniem użytkowników z aplikacją](application-provisioning-config-problem.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](what-is-single-sign-on.md)

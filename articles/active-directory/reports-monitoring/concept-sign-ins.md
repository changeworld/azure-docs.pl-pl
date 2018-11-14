---
title: Raporty dotyczące logowań w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących logowań w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ae962cba5e3d08661eb1c93edfc2feb221a9367e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623794"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Architektura raportowania w usłudze Azure Active Directory (Azure AD) obejmuje następujące składniki:

- **Działanie** 
    - **Operacje logowania** — informacje na temat użycia zarządzanych aplikacji i działań logowania użytkownika.
    - **Dzienniki inspekcji** - [dzienniki inspekcji](concept-audit-logs.md) Podaj informacje o użytkowników i grupy zarządzania, zarządzane aplikacje i działania dotyczące katalogu aktywności systemu.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — [ryzykowne logowania](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania, które mogły zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani w związku z ryzykiem** — [ryzykowny użytkownik](concept-user-at-risk.md) jest wskaźnikiem konta użytkownika, który może być zabezpieczenia mogły zostać naruszone.

Ten temat zawiera omówienie raportów logowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w roli administratora zabezpieczeń, Czytelnik zabezpieczeń i czytelnika raportów
* Administratorzy globalni
* Ponadto każdy użytkownik (niebędący administratorem) będą mogli ich własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?
* Dzierżawca musi mieć skojarzoną licencję usługi Azure AD w wersji Premium, aby wyświetlić pełny raport o wszystkich operacjach logowania

## <a name="sign-ins-report"></a>Raport logowań

Raport logowań użytkownika zawiera odpowiedzi na następujące pytania:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

Dostęp do raportów logowania, wybierając **logowania** w **działania** części **usługi Azure Active Directory** bloku [witrynyAzureportal](https://portal.azure.com).

![Działania związane z logowaniem](./media/concept-sign-ins/61.png "Działania związane z logowaniem")

Domyślny widok listy dziennika logowań pokazuje następujące dane:

- Data logowania
- Powiązany użytkownik
- Aplikacja, do której zalogował się użytkownik
- Stan logowania
- Stan wykrywania ryzyka
- Stan wymagania dotyczącego uwierzytelniania wieloskładnikowego (MFA)

![Działania związane z logowaniem](./media/concept-sign-ins/01.png "Działania związane z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Działania związane z logowaniem](./media/concept-sign-ins/19.png "Działania związane z logowaniem")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Działania związane z logowaniem](./media/concept-sign-ins/02.png "Działania związane z logowaniem")

Wybierz element w widoku listy, aby uzyskać więcej szczegółowych informacji.

![Działania związane z logowaniem](./media/concept-sign-ins/03.png "Działania związane z logowaniem")

> [!NOTE]
> Klienci, teraz można rozwiązywać problemy z zasad dostępu warunkowego za pomocą wszystkich raportów logowania. Klikając **dostępu warunkowego** kartę rekordu logowania, klientów można przeglądać stanu dostępu warunkowego i dokładniej omówić szczegółowe informacje o zasadach które są stosowane do logowania i wyników dla każdej zasady.
> Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące informacji urzędu certyfikacji w wszystkich logowań](reports-faq.md#conditional-access).

![Działania związane z logowaniem](./media/concept-sign-ins/ConditionalAccess.png "Działania związane z logowaniem")


## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz przefiltrować dane logowania przy użyciu następujących pól domyślnych:

- Użytkownik
- Aplikacja
- Stan logowania
- Dostęp warunkowy
- Date

![Działania związane z logowaniem](./media/concept-sign-ins/04.png "Działania związane z logowaniem")

Filtr **Użytkownik** umożliwia określenie nazwy lub głównej nazwy wybranego użytkownika (nazwy UPN).

Filtr **Aplikacja** umożliwia określenie nazwy wybranej aplikacji.

Filtr **Stan logowania** umożliwia wybranie jednej z następujących wartości:

- Wszyscy
- Powodzenie
- Niepowodzenie

**Dostępu warunkowego** filtr umożliwia wybranie urzędu certyfikacji zasad stan logowania:

- Wszyscy
- Nie zastosowano
- Powodzenie
- Niepowodzenie

Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 24 godziny
- Niestandardowy zakres czasu

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Jeśli dodasz kolejne pola do widoku logowań, te pola zostaną automatycznie dodane do listy filtrów. Na przykład dodanie pola **Aplikacja kliencka** do listy powoduje udostępnienie kolejnej opcji filtru, która umożliwia ustawienie następujących filtrów:

- Przeglądarka      
- Exchange ActiveSync (obsługiwane)               
- Exchange ActiveSync (nieobsługiwane)
- Inni klienci               
    - IMAP
    - MAPI
    - Starsi klienci pakietu Office
    - POP
    - SMTP


![Działania związane z logowaniem](./media/concept-sign-ins/12.png "Działania związane z logowaniem")


## <a name="download-sign-in-activities"></a>Pobieranie działań związanych z logowaniem

Możesz [Pobierz dane logowania](quickstart-download-sign-in-report.md) Jeśli chcesz pracować z nim poza witryny Azure portal. Kliknięcie przycisku **Pobierz** spowoduje utworzenie pliku CSV zawierającego 5000 najnowszych rekordów.  Oprócz przycisk pobierania, witryny Azure portal zapewnia także możliwość [generowania skryptu w celu pobrania danych](tutorial-signin-logs-download-script.md).  

![Pobierz](./media/concept-sign-ins/71.png "Pobierz")

Jeśli potrzebujesz bardziej elastycznych opcji, możesz użyć rozwiązania do tworzenia skryptu. Klikając **skryptu** tworzy skrypt środowiska PowerShell, który zawiera wszystkie filtry, które zostały ustawione. Pobierz i uruchom ten skrypt **z trybu administratora** można wygenerować pliku CSV. 

> [!IMPORTANT]
> Liczba rekordów, które można pobrać jest ograniczony przez [zasady przechowywania raportów usługi Azure Active Directory](reference-reports-data-retention.md).  

### <a name="running-the-script-on-a-windows-10-machine"></a>Uruchomienie skryptu na komputerze z systemem Windows 10

Jeśli chcesz uruchomić skrypt **systemu Windows 10** maszyny, musisz najpierw wykonać kilka dodatkowych kroków. 

1. Zainstaluj [modułu AzureRM](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. Zaimportuj moduł, otwierając wiersz polecenia programu PowerShell i uruchamiając polecenie **AzureRM Import-Module**.
3. Uruchom **Set-ExecutionPolicy unrestricted** i wybierz polecenie **tak na wszystko**. 
4. Teraz możesz uruchamiać pobranego skryptu programu PowerShell w trybie administratora, aby wygenerować plik CSV.

## <a name="sign-ins-data-shortcuts"></a>Skróty danych logowania

Oprócz usługi Azure AD witryny Azure portal oferuje dodatkowe punkty wejścia do danych logowania:

- Omówienie ochrony zabezpieczeń tożsamości
- Użytkownicy
- Grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dane logowania użytkowników w ochrony zabezpieczeń tożsamości

Wykres logowania użytkownika w **ochrony zabezpieczeń tożsamości** strony Przegląd przedstawia tygodniowe agregacje logowań dla wszystkich użytkowników w danym okresie czasu. Domyślny okres to 30 dni.

![Działania związane z logowaniem](./media/concept-sign-ins/06.png "Działania związane z logowaniem")

Po kliknięciu dnia na wykresie logowań zostanie wyświetlony przegląd działań logowania dla tego dnia.

Każdy wiersz na liście działań logowania określa następujące informacje:

* Kto się zalogował?
* Do której aplikacji się logowano?
* Jaki jest stan logowania?
* Jaki jest stan uwierzytelniania wieloskładnikowego dla logowania?

Klikając pozycję, można uzyskać więcej szczegółowych informacji na temat operacji logowania:

- Identyfikator użytkownika
- Użytkownik
- Nazwa użytkownika
- Identyfikator aplikacji
- Aplikacja
- Klient
- Lokalizacja
- Adres IP
- Date
- Wymagane uwierzytelnianie wieloskładnikowe
- Stan logowania
 
Na stronie **Użytkownicy** znajduje się pełny przegląd wszystkich logowań użytkowników dostępny po kliknięciu pozycji **Logowania** w sekcji **Działanie**.

![Działania związane z logowaniem](./media/concept-sign-ins/08.png "Działania związane z logowaniem")

## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Jakie są 3 najczęściej używane aplikacje w organizacji?
* Ostatnio została wdrożona aplikacja. W jaki sposób działa?

Punktem wyjścia do tych danych są 3 najczęściej używane aplikacje w organizacji uwzględnione w raporcie z ostatnich 30 dni znajdującym się w sekcji **Przegląd** w obszarze **Aplikacje dla przedsiębiorstw**.

![Działania związane z logowaniem](./media/concept-sign-ins/10.png "Działania związane z logowaniem")

Wykres użycia aplikacji przedstawia tygodniowe agregacje logowań 3 najczęściej używanych aplikacji w danym czasie. Domyślny okres to 30 dni.

![Działania związane z logowaniem](./media/concept-sign-ins/47.png "Działania związane z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.

![Raportowanie](./media/concept-sign-ins/single_spp_usage_graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.

Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

![Działania związane z logowaniem](./media/concept-sign-ins/11.png "Działania związane z logowaniem")

## <a name="next-steps"></a>Kolejne kroki

* [Kody błędów raport aktywności logowania](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych w usłudze Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów w usłudze Azure AD](reference-reports-latencies.md)


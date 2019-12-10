---
title: Raporty dotyczące logowań w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących logowań w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/09/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc996c7b5d8a63834f548689c83f7a72685120d2
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951177"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Architektura raportowania w Azure Active Directory (Azure AD) składa się z następujących składników:

- **Działanie** 
    - **Logowania** — informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - **Dzienniki inspekcji** - [dzienniki inspekcji](concept-audit-logs.md) zapewniają informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzane aplikacje i działania w katalogu.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — [ryzykowne logowanie](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani do ryzyka** — [ryzykowny użytkownik](concept-user-at-risk.md) jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Ten artykuł zawiera omówienie raportu logowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?

* Użytkownicy w roli administrator zabezpieczeń, czytelnik zabezpieczeń, czytelnik globalny i role czytelnika raportu
* Administratorzy globalni
* Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

* Do wyświetlenia podsumowującego raportu aktywności związanej z logowaniem wymagane jest skojarzenie dzierżawy z licencją usługi Azure AD Premium. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Wyświetlenie danych w raportach po przeprowadzeniu uaktualnienia do licencji Premium bez działań związanych z danymi przed uaktualnieniem zajmie kilka dni.

## <a name="sign-ins-report"></a>Raport dotyczący logowań

Raport logowania użytkownika zawiera odpowiedzi na następujące pytania:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

Zacznij od [Azure Portal](https://portal.azure.com). Aby uzyskać dostęp do raportu logowania, wybierz pozycję **logowania**, a następnie przejdź do **monitorowania.** Wyświetlanie rekordów logowania w portalu może potrwać do dwóch godzin.

![Aktywność logowania](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Aktywność związana z logowaniem")

> [!IMPORTANT]
> Raport logowania zawiera tylko logowania **interaktywne** , czyli logowania, w przypadku których użytkownik ręcznie loguje się przy użyciu nazwy użytkownika i hasła. Nieinteraktywne logowania, takie jak uwierzytelnianie między usługami, nie są wyświetlane w raporcie logowania. 

Domyślny widok listy dziennika logowań pokazuje następujące dane:

- Data logowania
- Powiązany użytkownik
- Aplikacja, do której użytkownik zalogował się
- Stan logowania
- Stan wykrywania ryzyka
- Stan wymagania dotyczącego uwierzytelniania wieloskładnikowego (MFA)

![Aktywność logowania](./media/concept-sign-ins/sign-in-activity.png "Aktywność związana z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Aktywność logowania](./media/concept-sign-ins/19.png "Aktywność związana z logowaniem")

Okno dialogowe **kolumny** umożliwia dostęp do atrybutów, które można wybrać. W raporcie logowania można mieć tylko atrybuty, które nie mają relacji jeden-do-wielu z innymi atrybutami jako kolumna.

![Aktywność logowania](./media/concept-sign-ins/columns.png "Aktywność związana z logowaniem")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Aktywność logowania](./media/concept-sign-ins/basic-sign-in.png "Aktywność związana z logowaniem")

> [!NOTE]
> Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego przez wszystkie raporty logowania. Klikając kartę **dostęp warunkowy** dla rekordu logowania, klienci mogą sprawdzić stan dostępu warunkowego i szczegółowe w celu uzyskania szczegółowych informacji dotyczących zasad, które zastosowały się do logowania, oraz wyników dla każdej zasady.
> Aby uzyskać więcej informacji, zapoznaj [się z często zadawanymi pytaniami dotyczącymi informacji o urzędzie certyfikacji we wszystkich logowaniach](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Najpierw Zawężanie danych raportowanych do poziomu, który się do Ciebie sprawdza. Następnie należy odfiltrować dane logowania przy użyciu pola daty jako domyślnego filtru. Usługa Azure AD udostępnia szeroką gamę dodatkowych filtrów, które można ustawić.

![Aktywność logowania](./media/concept-sign-ins/04.png "Aktywność związana z logowaniem")

Filtr **Użytkownik** umożliwia określenie nazwy lub głównej nazwy wybranego użytkownika (nazwy UPN).

Filtr **Aplikacja** umożliwia określenie nazwy wybranej aplikacji.

Filtr **Stan logowania** umożliwia wybranie jednej z następujących wartości:

- Wszystko
- Powodzenie
- Niepowodzenie

Filtr **dostępu warunkowego** umożliwia wybranie stanu zasad urzędu certyfikacji dla logowania:

- Wszystko
- Nie zastosowano
- Powodzenie
- Niepowodzenie

Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- Jeden miesiąc
- 7 dni
- 24 godziny
- Niestandardowy zakres czasu

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Jeśli dodasz kolejne pola do widoku logowań, te pola zostaną automatycznie dodane do listy filtrów. Na przykład dodanie pola **Aplikacja kliencka** do listy powoduje udostępnienie kolejnej opcji filtru, która umożliwia ustawienie następujących filtrów:  
![Aktywność logowania](./media/concept-sign-ins/12.png "Aktywność związana z logowaniem")

- **Przeglądarka**  
    Ten filtr przedstawia wszystkie zdarzenia, dla których podjęto próbę zalogowania przy użyciu przepływów przeglądarki.
- **Exchange ActiveSync (obsługiwane)**  
    Ten filtr przedstawia wszystkie próby logowania, w przypadku których podjęto próbę wykonania protokołu Exchange ActiveSync (EAS) z obsługiwanych platform, takich jak iOS, Android i Windows Phone.
- **Exchange ActiveSync (nieobsługiwane)**  
    Ten filtr przedstawia wszystkie próby logowania, w przypadku których podjęto próbę użycia protokołu EAS z nieobsługiwanych platform, takich jak Linux dystrybucje.
- **Klienci Mobile Apps i komputery stacjonarne** Filtr pokazuje wszystkie próby logowania, które nie były używane przez przepływy przeglądarki. Na przykład aplikacje mobilne z dowolnej platformy przy użyciu dowolnego protokołu lub aplikacji klienckich dla komputerów stacjonarnych, takich jak pakiet Office w systemie Windows lub MacOS.
  
- **Inni klienci**
    - **PROTOKOŁU**  
        Starsza wersja klienta poczty używającej protokołu IMAP do pobierania poczty e-mail.
    - **MAPI**  
        Pakiet Office 2013, gdzie Biblioteka ADAL jest włączona i używa interfejsu MAPI.
    - **Stara klienci pakietu Office**  
        Pakiet Office 2013 w konfiguracji domyślnej, gdzie Biblioteka ADAL nie jest włączona i używa interfejsu MAPI lub pakietu Office 2016, gdzie ADAL została wyłączona.
    - **POP**  
        Starsza wersja klienta poczty używającej protokołu POP3 do pobierania poczty e-mail.
    - **SMTP**  
        Starszego klienta poczty e-mail korzystającego z protokołu SMTP do wysyłania wiadomości.

## <a name="download-sign-in-activities"></a>Pobieranie działań związanych z logowaniem

Kliknij opcję **Pobierz** , aby utworzyć plik CSV lub kod JSON z najnowszych rekordów 250 000. Rozpocznij od [pobrania danych logowania,](quickstart-download-sign-in-report.md) Jeśli chcesz korzystać z nich poza Azure Portal.  

![Pobieranie](./media/concept-sign-ins/71.png "Pobierz")

> [!IMPORTANT]
> Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania raportów Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Skróty danych logowania

Usługa Azure AD i Azure Portal udostępniają dodatkowe punkty wejścia do danych logowania:

- Omówienie ochrony tożsamości
- Użytkownicy
- Grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Użytkownicy logują się do danych w usłudze Identity Security Protection

Na stronie Omówienie usługi **Identity Security Protection** Graf logowania użytkownika przedstawia tygodniowe agregacje logowania. Wartością domyślną okresu jest 30 dni.

![Aktywność logowania](./media/concept-sign-ins/06.png "Aktywność związana z logowaniem")

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
- Data
- Wymagane uwierzytelnianie wieloskładnikowe
- Stan logowania

> [!NOTE]
> Adresy IP są wystawiane w taki sposób, że nie istnieje ostateczne połączenie między adresem IP i lokalizacją, w której komputer z tym adresem jest fizycznie zlokalizowany. Mapowanie adresów IP jest skomplikowane przez fakt, że dostawcy urządzeń przenośnych i sieci VPN wystawiają adresy IP z pul centralnych, które często są bardzo daleko od miejsca użycia urządzenia klienckiego. Obecnie w raportach usługi Azure AD konwertowanie adresu IP na lokalizację fizyczną jest najlepszym nakładem pracy na podstawie śladów, danych rejestru, wyszukiwania wstecznego i innych informacji.

Na stronie **Użytkownicy** znajduje się pełny przegląd wszystkich logowań użytkowników dostępny po kliknięciu pozycji **Logowania** w sekcji **Działanie**.

![Aktywność logowania](./media/concept-sign-ins/08.png "Aktywność związana z logowaniem")

## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Co to są trzy najpopularniejsze aplikacje w organizacji?
* Jak działa moja najnowsza aplikacja?

Punkt wejścia do tych danych to trzy pierwsze aplikacje w organizacji. Dane są zawarte w raporcie z ostatnich 30 dni w sekcji **Przegląd** w obszarze **aplikacje dla przedsiębiorstw**.

![Aktywność logowania](./media/concept-sign-ins/10.png "Aktywność związana z logowaniem")

Wykresy użycia aplikacji dotyczą tygodniowego agregacji logowań dla najpopularniejszych trzech aplikacji w danym okresie. Domyślny okres to 30 dni.

![Aktywność logowania](./media/concept-sign-ins/graph-chart.png "Aktywność związana z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.

![Raportowanie](./media/concept-sign-ins/single-app-usage-graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.

Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

## <a name="office-365-activity-logs"></a>Dzienniki aktywności pakietu Office 365

Dzienniki aktywności pakietu Office 365 można wyświetlić w [centrum administracyjnym Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Należy wziąć pod uwagę, że usługa Office 365 i dzienniki aktywności usługi Azure AD dzielą znaczną liczbę zasobów katalogu. Tylko Microsoft 365 centrum administracyjnego zapewnia pełny wgląd w dzienniki aktywności pakietu Office 365. 

Możesz również programowo uzyskać dostęp do dzienników aktywności pakietu Office 365 przy użyciu [interfejsów API zarządzania pakietu office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Następne kroki

* [Kody błędów raportów działań związanych z logowaniem](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych usługi Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów usługi Azure AD](reference-reports-latencies.md)


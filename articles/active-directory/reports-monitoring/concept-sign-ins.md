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
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6121ca6c1636c8839110712310a1b94fe7fada49
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619255"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Architektura raportowania w Azure Active Directory (Azure AD) składa się z następujących składników:

- **Aktywność** 
    - **Logowania** — informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - Dzienniki inspekcji dzienników inspekcji zapewniają informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzane aplikacje i działania w katalogu.[](concept-audit-logs.md)  - 
- **Zabezpieczenia** 
    - **Ryzykowne logowania** — [ryzykowne logowanie](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani do ryzyka** — [ryzykowny użytkownik](concept-user-at-risk.md) jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Ten temat zawiera omówienie raportu logowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w rolach administrator zabezpieczeń, czytelnik zabezpieczeń i czytelnik raportów
* Administratorzy globalni
* Ponadto wszyscy użytkownicy (inni niż administratorzy) mogą uzyskać dostęp do własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?
* Dzierżawca musi mieć skojarzoną licencję Azure AD — wersja Premium, aby wyświetlić raport dotyczący wszystkich działań związanych z logowaniem. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach.

## <a name="sign-ins-report"></a>Raport dotyczący logowań

Raport logowania użytkownika zawiera odpowiedzi na następujące pytania:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

Możesz uzyskać dostęp do raportu logowania, wybierając pozycję **logowania** w sekcji **działanie** w bloku **Azure Active Directory** w [Azure Portal](https://portal.azure.com). Należy pamiętać, że w przypadku niektórych rekordów logowania w portalu może upłynąć do dwóch godzin.

![Działania związane z logowaniem](./media/concept-sign-ins/61.png "Działania związane z logowaniem")

> [!IMPORTANT]
> Raport logowania zawiera tylko logowania **interaktywne** , czyli logowania, w przypadku których użytkownik ręcznie loguje się przy użyciu nazwy użytkownika i hasła. Nieinteraktywne logowania, takie jak uwierzytelnianie między usługami, nie są wyświetlane w raporcie logowania. 

Domyślny widok listy dziennika logowań pokazuje następujące dane:

- Data logowania
- Powiązany użytkownik
- Aplikacja, do której użytkownik zalogował się
- Stan logowania
- Stan wykrywania ryzyka
- Stan wymagania dotyczącego uwierzytelniania wieloskładnikowego (MFA)

![Działania związane z logowaniem](./media/concept-sign-ins/01.png "Działania związane z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Działania związane z logowaniem](./media/concept-sign-ins/19.png "Działania związane z logowaniem")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Działania związane z logowaniem](./media/concept-sign-ins/02.png "Działania związane z logowaniem")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Działania związane z logowaniem](./media/concept-sign-ins/03.png "Działania związane z logowaniem")

> [!NOTE]
> Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego przez wszystkie raporty logowania. Klikając kartę **dostęp warunkowy** dla rekordu logowania, klienci mogą sprawdzić stan dostępu warunkowego i szczegółowe w celu uzyskania szczegółowych informacji dotyczących zasad, które zastosowały się do logowania, oraz wyników dla każdej zasady.
> Aby uzyskać więcej informacji, zapoznaj [się z często zadawanymi pytaniami dotyczącymi informacji o urzędzie certyfikacji we wszystkich logowaniach](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Aby zawęzić zgłoszone dane do poziomu, który działa dla Ciebie, można filtrować dane logowania przy użyciu pola daty jako domyślnego filtru. Ponadto usługa Azure AD udostępnia szeroką gamę dodatkowych filtrów, które można ustawić.

![Działania związane z logowaniem](./media/concept-sign-ins/04.png "Działania związane z logowaniem")

Filtr **Użytkownik** umożliwia określenie nazwy lub głównej nazwy wybranego użytkownika (nazwy UPN).

Filtr **Aplikacja** umożliwia określenie nazwy wybranej aplikacji.

Filtr **Stan logowania** umożliwia wybranie jednej z następujących wartości:

- Wszyscy
- Powodzenie
- Niepowodzenie

Filtr **dostępu warunkowego** umożliwia wybranie stanu zasad urzędu certyfikacji dla logowania:

- Wszyscy
- Nie zastosowano
- Powodzenie
- Niepowodzenie

Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości to:

- 1 miesiąc
- 7 dni
- 24 godz.
- Niestandardowy zakres czasu

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Jeśli dodasz kolejne pola do widoku logowań, te pola zostaną automatycznie dodane do listy filtrów. Na przykład dodanie pola **Aplikacja kliencka** do listy powoduje udostępnienie kolejnej opcji filtru, która umożliwia ustawienie następujących filtrów:  
![Działania związane z logowaniem](./media/concept-sign-ins/12.png "Działania związane z logowaniem")

- **Przeglądarka**  
    Ten filtr przedstawia wszystkie zdarzenia, w przypadku których próby logowania zostały wykonane przy użyciu przepływów przeglądarki.
- **Exchange ActiveSync (obsługiwane)**  
    Ten filtr przedstawia wszystkie próby logowania, w przypadku których podjęto próbę wykonania protokołu Exchange ActiveSync (EAS) z obsługiwanych platform, takich jak iOS, Android i Windows Phone.
- **Exchange ActiveSync (nieobsługiwane)**  
    Ten filtr przedstawia wszystkie próby logowania, w przypadku których podjęto próbę użycia protokołu EAS z nieobsługiwanych platform, takich jak Linux dystrybucje.
- **Klienci Mobile Apps i komputery stacjonarne** Ten filtr pokazuje wszystkie próby logowania, które nie były używane przez przepływy przeglądarki. Mogą to być aplikacje mobilne z dowolnej platformy przy użyciu dowolnego protokołu lub aplikacji klienckich dla komputerów stacjonarnych, takich jak pakiet Office w systemie Windows lub MacOS.
  
- **Inni klienci**
    - **PROTOKOŁU**  
        Starsza wersja klienta poczty używającej protokołu IMAP do pobierania poczty e-mail.
    - **OBSŁUGUJĄC**  
        Pakiet Office 2013, gdzie Biblioteka ADAL jest włączona i używa interfejsu MAPI.
    - **Starsi klienci pakietu Office**  
        Pakiet Office 2013 w konfiguracji domyślnej, gdzie Biblioteka ADAL nie jest włączona i używa interfejsu MAPI lub pakietu Office 2016, gdzie ADAL została wyłączona.
    - **POP**  
        Starsza wersja klienta poczty używającej protokołu POP3 do pobierania poczty e-mail.
    - **SMTP**  
        Starszego klienta poczty e-mail korzystającego z protokołu SMTP do wysyłania wiadomości.

## <a name="download-sign-in-activities"></a>Pobieranie działań związanych z logowaniem

Możesz [pobrać dane logowania,](quickstart-download-sign-in-report.md) Jeśli chcesz korzystać z nich poza Azure Portal. Kliknięcie przycisku **Pobierz** umożliwia utworzenie pliku CSV lub JSON dla najnowszych rekordów 250 000.  

![Pobierz](./media/concept-sign-ins/71.png "Pobierz")

> [!IMPORTANT]
> Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania raportów Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Skróty danych logowania

Oprócz usługi Azure AD Azure Portal zapewnia dodatkowe punkty wejścia do danych logowania:

- Omówienie ochrony tożsamości
- Użytkownicy
- Grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Użytkownicy logują się do danych w usłudze Identity Security Protection

Na stronie Omówienie usługi **Identity Security Protection** Graf logowania użytkownika przedstawia tygodniowe agregacje logowań dla wszystkich użytkowników w danym okresie czasu. Domyślny okres to 30 dni.

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
- Location
- Adres IP
- Date
- Wymagana usługa MFA
- Stan logowania

> [!NOTE]
> Adresy IP są wystawiane w taki sposób, że nie istnieje ostateczne połączenie między adresem IP i lokalizacją, w której komputer z tym adresem jest fizycznie zlokalizowany. Mapowanie adresów IP jest skomplikowane przez fakt, że dostawcy urządzeń przenośnych i sieci VPN wystawiają adresy IP z pul centralnych, które często są bardzo daleko od miejsca użycia urządzenia klienckiego. Obecnie w raportach usługi Azure AD konwertowanie adresu IP na lokalizację fizyczną jest najlepszym nakładem pracy na podstawie śladów, danych rejestru, wyszukiwania wstecznego i innych informacji.

Na stronie **Użytkownicy** znajduje się pełny przegląd wszystkich logowań użytkowników dostępny po kliknięciu pozycji **Logowania** w sekcji **Działanie**.

![Działania związane z logowaniem](./media/concept-sign-ins/08.png "Działania związane z logowaniem")

## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Jakie są 3 najczęściej używane aplikacje w organizacji?
* Ostatnio została wdrożona aplikacja. W jaki sposób działa?

Punktem wyjścia do tych danych są 3 najczęściej używane aplikacje w organizacji uwzględnione w raporcie z ostatnich 30 dni znajdującym się w sekcji **Przegląd** w obszarze **Aplikacje dla przedsiębiorstw**.

![Działania związane z logowaniem](./media/concept-sign-ins/10.png "Działania związane z logowaniem")

Wykres użycia aplikacji tygodniowe agregacje logowań dla pierwszych 3 aplikacji w danym okresie. Domyślny okres to 30 dni.

![Działania związane z logowaniem](./media/concept-sign-ins/47.png "Działania związane z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.

![Raportowanie](./media/concept-sign-ins/single_spp_usage_graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.

Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

![Działania związane z logowaniem](./media/concept-sign-ins/11.png "Działania związane z logowaniem")

## <a name="office-365-activity-logs"></a>Dzienniki aktywności pakietu Office 365

Dzienniki aktywności pakietu Office 365 można wyświetlić w [centrum administracyjnym Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Mimo że działania pakietu Office 365 i dzienniki aktywności usługi Azure AD współdzielą wiele zasobów katalogu, tylko Microsoft 365 centrum administracyjnym zapewnia pełny wgląd w dzienniki aktywności pakietu Office 365. 

Możesz również programowo uzyskać dostęp do dzienników aktywności pakietu Office 365 przy użyciu [interfejsów API zarządzania pakietu office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Następne kroki

* [Kody błędów raportów działań związanych z logowaniem](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych usługi Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów usługi Azure AD](reference-reports-latencies.md)


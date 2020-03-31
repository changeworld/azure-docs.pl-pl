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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246521"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Architektura raportowania w usłudze Azure Active Directory (Azure AD) składa się z następujących składników:

- **Działanie** 
    - **Logowania** — informacje o użyciu zarządzanych aplikacji i działań logowania użytkownika.
    - **Dzienniki inspekcji Dzienniki** - [inspekcji](concept-audit-logs.md) zawierają informacje o aktywności systemowej o użytkownikach i zarządzaniu grupami, zarządzanych aplikacjach i działaniach katalogowych.
- **Zabezpieczenia** 
    - **Ryzykowne logowania** — ryzykowne [logowanie](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania przez osobę, która nie jest prawowitym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani ze względu na ryzyko** — ryzykowny [użytkownik](concept-user-at-risk.md) jest wskaźnikiem dla konta użytkownika, które mogło zostać naruszone.

Z tego artykułu dowiesz się, że raport logowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?

* Użytkownicy w rolach Administrator zabezpieczeń, Czytnik zabezpieczeń, Czytnik globalny i Czytnik raportów
* Administratorzy globalni
* Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

- Raport aktywności logowania jest dostępny we [wszystkich wersjach usługi Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data).

- Jeśli chcesz uzyskać dostęp do danych logowania przy użyciu interfejsu API, dzierżawa musi mieć skojarzoną z nią licencję [usługi Azure Active Directory Premium.](../fundamentals/active-directory-get-started-premium.md)



## <a name="sign-ins-report"></a>Raport dotyczący logowań

Raport logowania użytkownika zawiera odpowiedzi na następujące pytania:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

W menu [portalu Azure](https://portal.azure.com) wybierz pozycję Azure **Active Directory**lub wyszukaj i wybierz **usługę Azure Active Directory** z dowolnej strony.

![Wybieranie usługi Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Usługa Azure Active Directory")

W obszarze **Monitorowanie**wybierz **pozycję Logowanie,** aby otworzyć [raport logowania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Aktywność logowania](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aktywność związana z logowaniem")

Może upłynąć do dwóch godzin, aby niektóre rekordy logowania, aby pokazać się w portalu.

> [!IMPORTANT]
> Raport logowania wyświetla tylko **interaktywne** logowania, czyli logowania, w których użytkownik ręcznie loguje się przy użyciu swojej nazwy użytkownika i hasła. Nieinterakcyjne logowania, takie jak uwierzytelnianie między usługami, nie są wyświetlane w raporcie logowania. 

Domyślny widok listy dziennika logowań pokazuje następujące dane:

- Data logowania
- Powiązany użytkownik
- Aplikacja, do wylogowała się użytkownik
- Stan logowania
- Stan wykrywania ryzyka
- Stan wymagania dotyczącego uwierzytelniania wieloskładnikowego (MFA)

![Aktywność logowania](./media/concept-sign-ins/sign-in-activity.png "Aktywność związana z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Aktywność logowania](./media/concept-sign-ins/19.png "Aktywność związana z logowaniem")

Okno dialogowe **Kolumny** daje dostęp do atrybutów do wyboru. W raporcie logowania nie można mieć pól, które mają więcej niż jedną wartość dla danego żądania logowania jako kolumny. Dotyczy to na przykład szczegółów uwierzytelniania, danych dostępu warunkowego i lokalizacji sieciowej.   

![Aktywność logowania](./media/concept-sign-ins/columns.png "Aktywność związana z logowaniem")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Aktywność logowania](./media/concept-sign-ins/basic-sign-in.png "Aktywność związana z logowaniem")

> [!NOTE]
> Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego za pośrednictwem wszystkich raportów logowania. Klikając kartę **Dostęp warunkowy** dla rekordu logowania, klienci mogą przejrzeć stan dostępu warunkowego i zapoznać się ze szczegółami zasad zastosowanych do logowania i wynikiem dla każdej zasady.
> Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące informacji o ucho we wszystkich logowaniach](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Po pierwsze, zawężenie zgłoszonych danych do poziomu, który działa dla Ciebie. Po drugie, filtrowanie danych logowania przy użyciu pola daty jako filtru domyślnego. Usługa Azure AD zapewnia szeroką gamę dodatkowych filtrów, które można ustawić:

![Aktywność logowania](./media/concept-sign-ins/04.png "Aktywność związana z logowaniem")

**Identyfikator żądania** — identyfikator żądania, na której Ci zależy.

**Użytkownik** — nazwa lub główna nazwa użytkownika (UPN) użytkownika, na których Ci zależy.

**Aplikacja** — nazwa aplikacji docelowej.
 
**Status** — stan logowania, na który Ci zależy:

- Powodzenie

- Niepowodzenie

- Przerwane


**Adres IP** — adres IP urządzenia używanego do łączenia się z dzierżawą.

**Lokalizacja** — lokalizacja, z wyz.:

- Miasto

- Stan / Prowincja

- Kraj/region


**Zasób** — nazwa usługi używanej do logowania.


**Identyfikator zasobu** — identyfikator usługi używanej do logowania.


**Aplikacja kliencka** — typ aplikacji klienckiej używanej do łączenia się z dzierżawą:

![Filtr aplikacji klienckiej](./media/concept-sign-ins/client-app-filter.png)


|Nazwa|Nowoczesne uwierzytelnianie|Opis|
|---|:-:|---|
|Uwierzytelniona protokół SMTP| |Używane przez klientów POP i IMAP do wysyłania wiadomości e-mail.|
|Automatycznego wykrywania| |Używane przez klientów programu Outlook i EAS do znajdowania skrzynek pocztowych i łączenia się z nią w usłudze Exchange Online.|
|Exchange ActiveSync| |Ten filtr pokazuje wszystkie próby logowania, w których podjęto próbę protokołu EAS.|
|Przeglądarka|![Zaznacz](./media/concept-sign-ins/check.png)|Pokazuje wszystkie próby logowania od użytkowników korzystających z przeglądarek internetowych|
|Exchange ActiveSync| | Pokazuje wszystkie próby logowania od użytkowników z aplikacjami klienckimi przy użyciu programu Exchange ActiceSync w celu połączenia się z usługą Exchange Online|
|Exchange Online PowerShell| |Służy do łączenia się z usługą Exchange Online za pomocą zdalnego programu PowerShell. Jeśli zablokujesz uwierzytelnianie podstawowe dla programu Exchange Online PowerShell, musisz użyć modułu Programu PowerShell usługi Exchange Online do nawiązania połączenia. Aby uzyskać instrukcje, zobacz [Łączenie się z programem Exchange Online PowerShell przy użyciu uwierzytelniania wieloskładnikowego](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange Web Services| |Interfejs programowania używany przez program Outlook, Outlook dla komputerów Mac i aplikacje innych firm.|
|IMAP4| |Starszy klient poczty korzystający z protokołu IMAP do pobierania wiadomości e-mail.|
|MAPI przez HTTP| |Używane przez program Outlook 2010 i nowsze.|
|Aplikacje mobilne i klienci komputerowi|![Zaznacz](./media/concept-sign-ins/check.png)|Pokazuje wszystkie próby logowania od użytkowników korzystających z aplikacji mobilnych i klientów klasycznych.|
|Książka adresowa trybu offline| |Kopia kolekcji list adresów, które są pobierane i używane przez program Outlook.|
|Outlook Anywhere (RPC przez HTTP)| |Używany przez program Outlook 2016 i wcześniejszych.|
|Usługa programu Outlook| |Używany przez aplikację Poczta i Kalendarz dla systemu Windows 10.|
|POP3| |Starszy klient poczty korzystający z protokołu POP3 do pobierania wiadomości e-mail.|
|Raportowanie usług sieci Web| |Służy do pobierania danych raportu w usłudze Exchange Online.|
|Inni klienci| |Pokazuje wszystkie próby logowania od użytkowników, w których aplikacja kliencka nie jest uwzględniona lub nieznana.|



**System operacyjny** — system operacyjny uruchomiony na urządzeniu używane logowania do dzierżawy. 


**Przeglądarka urządzeń** — jeśli połączenie zostało zainicjowane z przeglądarki, to pole umożliwia filtrowanie według nazwy przeglądarki.


**Identyfikator korelacji** — identyfikator korelacji działania.




**Dostęp warunkowy** — stan zastosowanych reguł dostępu warunkowego

- **Nieuwzdłe:** Podczas logowania nie zastosowano żadnych zasad do użytkownika i aplikacji.

- **Powodzenie:** Co najmniej jedna zasady dostępu warunkowego stosowane do użytkownika i aplikacji (ale niekoniecznie inne warunki) podczas logowania. 

- **Błąd:** Jedna lub więcej zasad dostępu warunkowego zastosowano i nie zostało spełnione podczas logowania.









## <a name="download-sign-in-activities"></a>Pobieranie działań związanych z logowaniem

Kliknij opcję **Pobierz,** aby utworzyć plik CSV lub JSON z ostatnich 250 000 rekordów. Zacznij od [pobrania danych logowania,](quickstart-download-sign-in-report.md) jeśli chcesz pracować z nim poza portalem Azure.  

![Pobierz](./media/concept-sign-ins/71.png "Pobierz")

> [!IMPORTANT]
> Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania raportów usługi Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Skróty do danych logowania

Usługa Azure AD i portal Azure zapewniają dodatkowe punkty wejścia do danych logowania:

- Omówienie ochrony zabezpieczeń tożsamości
- Użytkownicy
- Grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dane logowania użytkowników w ochronie zabezpieczeń tożsamości

Wykres logowania użytkownika na stronie Przegląd **ochrony zabezpieczeń tożsamości** pokazuje cotygodniowe agregacje logowania. Wartość domyślna dla okresu wynosi 30 dni.

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
> Adresy IP są wydawane w taki sposób, że nie ma ostatecznego połączenia między adresem IP a miejscem, w którym fizycznie znajduje się komputer z tym adresem. Mapowanie adresów IP komplikuje fakt, że operatorzy komórkowi i sieci VPN wydają adresy IP z centralnych pul, które często są bardzo daleko od miejsca, w którym urządzenie klienckie jest faktycznie używane. Obecnie w raportach usługi Azure AD konwertowanie adresu IP na lokalizację fizyczną jest najlepszym rozwiązaniem na podstawie śladów, danych rejestru, odwrotnego wyszukiwania i innych informacji.

Na stronie **Użytkownicy** znajduje się pełny przegląd wszystkich logowań użytkowników dostępny po kliknięciu pozycji **Logowania** w sekcji **Działanie**.

![Aktywność logowania](./media/concept-sign-ins/08.png "Aktywność związana z logowaniem")

## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Jakie są trzy najlepsze aplikacje w twojej organizacji?
* Jak działa moja najnowsza aplikacja?

Punkt wejścia do tych danych jest trzy najlepsze aplikacje w organizacji. Dane są zawarte w ciągu ostatnich 30 dni raportu w sekcji **Przegląd** w **obszarze Aplikacje przedsiębiorstwa**.

![Aktywność logowania](./media/concept-sign-ins/10.png "Aktywność związana z logowaniem")

Użycie aplikacji przedstawia cotygodniowe agregacje logowania dla trzech najlepszych aplikacji w danym okresie. Domyślny okres to 30 dni.

![Aktywność logowania](./media/concept-sign-ins/graph-chart.png "Aktywność związana z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.

![Raportowanie](./media/concept-sign-ins/single-app-usage-graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.

Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

## <a name="office-365-activity-logs"></a>Dzienniki aktywności usługi Office 365

Dzienniki aktywności usługi Office 365 można wyświetlać w [centrum administracyjnym usługi Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Należy wziąć pod uwagę punkt, że działania usługi Office 365 i dzienniki aktywności usługi Azure AD współużytkują znaczną liczbę zasobów katalogu. Tylko centrum administracyjne usługi Microsoft 365 zapewnia pełny widok dzienników aktywności usługi Office 365. 

Dzienniki aktywności usługi Office 365 można również uzyskać programowo, korzystając z [interfejsów API zarządzania usługą Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Następne kroki

* [Kody błędów raportu aktywności logowania](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych usługi Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów usługi Azure AD](reference-reports-latencies.md)


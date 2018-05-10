---
title: Rozwiązanie do zarządzania usługi Office 365 na platformie Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje o konfiguracji i korzystanie z rozwiązania usługi Office 365 na platformie Azure.  Zawiera szczegółowy opis rekordów usługi Office 365 utworzone w analizy dzienników.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: 8797e08ad942687b7d2defd765f4fe3f9765812f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="office-365-management-solution-in-azure-preview"></a>Rozwiązanie do zarządzania usługi Office 365 na platformie Azure (wersja zapoznawcza)

![Logo usługi Office 365](media/oms-solution-office-365/icon.png)

Rozwiązanie do zarządzania usługi Office 365 służy do monitorowania środowiska usługi Office 365 w analizy dzienników.

- Monitorowanie aktywności użytkowników na swoje konta usługi Office 365, aby analizować wzorce użycia, a także behawioralnej trendów. Na przykład można wyodrębnić scenariusze użycia określonego, takich jak pliki, które są udostępniane poza organizację lub najbardziej popularnych witryn programu SharePoint.
- Monitorować działania administratora, aby śledzić zmiany w konfiguracji lub operacji wysokiego poziomu uprawnień.
- Wykrywanie i zbadaj zachowanie niechciane użytkownika, które można dostosować do własnych potrzeb organizacji.
- Prezentacja inspekcji i zgodności. Na przykład można monitorować operacji uzyskiwania dostępu do pliku na poufne pliki, które ułatwiają wykonywanie procesu inspekcji i zgodności.
- Rozwiązywania problemów operacyjnych za pomocą [dziennika wyszukiwania](../log-analytics/log-analytics-log-search.md) na podstawie danych działania usługi Office 365 w organizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej znajduje się przed to rozwiązanie jest zainstalowana i skonfigurowana.

- Organizacyjnej subskrypcji usługi Office 365.
- Poświadczenia dla konta użytkownika, który jest administratorem globalnym.
- Aby odbierać dane inspekcji, musisz [należy skonfigurować inspekcję](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) w ramach subskrypcji usługi Office 365.  Należy pamiętać, że [inspekcji skrzynki pocztowej](https://technet.microsoft.com/library/dn879651.aspx) skonfigurowano oddzielnie.  Można nadal zainstalować rozwiązania i zbierać innych danych, jeśli inspekcja nie jest skonfigurowany.
 


## <a name="management-packs"></a>Pakiety administracyjne
To rozwiązanie nie instalować żadnych pakietów administracyjnych w podłączonych grup zarządzania.
  

## <a name="configuration"></a>Konfigurowanie
Gdy [dodanych do subskrypcji usługi Office 365 rozwiązania](../log-analytics/log-analytics-add-solutions.md), należy połączyć go z subskrypcji usługi Office 365.

1. Dodaj rozwiązanie zarządzania alertami do obszaru roboczego analizy dzienników przy użyciu procesu opisanego w [dodać rozwiązania](../log-analytics/log-analytics-add-solutions.md).
2. Przejdź do **ustawienia** w portalu OMS.
3. W obszarze **połączonych źródeł**, wybierz pozycję **usługi Office 365**.
4. Polecenie **połączenie usługi Office 365**.<br>![Konstruktor usługi Office 365](media/oms-solution-office-365/configure.png)
5. Zaloguj się do usługi Office 365 przy użyciu konta administratora globalnego dla Twojej subskrypcji. 
6. Subskrypcja będzie wyświetlane z obciążeniami, które rozwiązanie będzie monitorował.<br>![Konstruktor usługi Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Zbieranie danych
### <a name="supported-agents"></a>Obsługiwani agenci
Rozwiązanie Office 365 nie pobierania danych z dowolnej [agentów OMS](../log-analytics/log-analytics-data-sources.md).  Pobiera on dane bezpośrednio z usługi Office 365.

### <a name="collection-frequency"></a>Częstotliwość zbierania
Usługi Office 365, wysyła [powiadomienia elementu webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) z szczegółowych danych do analizy dzienników zawsze jest tworzony rekord.

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu rozwiązanie Office 365 do obszaru roboczego analizy dzienników **usługi Office 365** kafelka zostaną dodane do pulpitu nawigacyjnego. Ten kafelek zawiera liczbę oraz graficzną reprezentację liczby komputerów w środowisku wraz z informacjami o ich zgodności aktualizacji.<br><br>
![Kafelek podsumowania usługi Office 365](media/oms-solution-office-365/tile.png)  

Polecenie **usługi Office 365** Kafelek, aby otworzyć **usługi Office 365** pulpitu nawigacyjnego.

![Pulpit nawigacyjny usługi Office 365](media/oms-solution-office-365/dashboard.png)  

Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera listę top dziesięciu alertów według liczby spełniających kryteria tej kolumny. dla określonego zakresu i zakres czasu. Można uruchomić wyszukiwania dziennika, która zawiera całą listę, klikając Zobacz wszystkie w dolnej części kolumny lub przez kliknięcie nagłówka kolumny.

| Kolumna | Opis |
|:--|:--|
| Operacje | Zawiera informacje o aktywnych użytkowników z subskrypcji usługi Office 365 wszystkie monitorowane. Ponadto można zobaczyć liczbę działań, które się tak zdarzyć w czasie.
| Exchange | Pokazuje podział działania serwera Exchange, takie jak uprawnienie Add-Mailbox lub Set-Mailbox. |
| Sharepoint | Pokazuje top działania czy użytkownicy wykonywać na dokumentów programu SharePoint. Gdy przechodzenie z tego kafelka strony wyszukiwania zawiera szczegółowe informacje o tych działań, takich jak dokument docelowy i lokalizację tego działania. Na przykład dla zdarzenia dostępu do pliku, będzie mógł wyświetlić dokument, który jest dostępny, jego nazwa skojarzone konto i adres IP. |
| Usługa Azure Active Directory | Zawiera użytkownika najwyższego działań, takich jak zresetować hasło użytkownika i prób logowania. Gdy możesz przejść do szczegółów, można wyświetlić szczegóły tych działań, takich jak stan wyniku. Najczęściej jest to przydatne, jeśli chcesz monitorować podejrzanych działań w usłudze Azure Active Directory. |




## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Wszystkie rekordy w obszarze roboczym analizy dzienników przy użyciu rozwiązania usługi Office 365 mają **typu** z **OfficeActivity**.  **OfficeWorkload** właściwość określa usługi Office 365, która rekord odwołuje się do - programu Exchange, usługi AzureActiveDirectory, SharePoint lub usługi OneDrive.  **RecordType** właściwość określa typ operacji.  Właściwości będą się różnić dla poszczególnych typów operacji i przedstawiono w poniższych tabelach.

### <a name="common-properties"></a>Wspólne właściwości
Następujące właściwości są wspólne dla wszystkich rekordów usługi Office 365.

| Właściwość | Opis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| ClientIP | Adres IP urządzenia, która była używana, gdy działanie zostało zarejestrowane. Adres IP jest wyświetlany w formacie adres IPv4 lub IPv6. |
| OfficeWorkload | Usługa Office 365, odnoszący się do rekordu.<br><br>Usługi AzureActiveDirectory<br>Exchange<br>Sharepoint|
| Operacja | Nazwa użytkownika lub administratora aktywności.  |
| Identyfikatora organizacji | Identyfikator GUID dzierżawy usługi Office 365 w organizacji. Ta wartość zawsze będzie taka sama dla organizacji, niezależnie od tego, usługę Office 365, w którym występuje. |
| recordType | Typ działania wykonywane. |
| ResultStatus | Wskazuje, czy akcja (określony we właściwości operacji) zakończyła się powodzeniem. Możliwe wartości to zakończyło się pomyślnie, PartiallySucceded lub nie powiodło się. Aktywność administratora programu Exchange, wartość jest wartość PRAWDA lub FAŁSZ. |
| Nazwa użytkownika | Nazwa UPN (główna nazwa użytkownika) użytkownika, który wykonał akcję, która spowodowała rekord rejestrowany; na przykład my_name@my_domain_name. Należy pamiętać, że rejestruje działania wykonywane przez kont systemowych (np. SHAREPOINT\system lub NTAUTHORITY\SYSTEM) dostępne są również. | 
| UserKey | Alternatywny identyfikator użytkownika określonego we właściwości identyfikatora użytkownika.  Na przykład ta właściwość jest wypełniana passport Unikatowy identyfikator (PUID) zdarzenia wykonywane przez użytkowników w programie SharePoint, usłudze OneDrive dla firm i Exchange. Ta właściwość może także określić tę samą wartość jako wartość właściwości identyfikator użytkownika dla zdarzenia występujące w innych usługach i wydarzeniach wykonywane przez kont systemu|
| UserType | Typ użytkownika, który wykonać operację.<br><br>Jednostka administracyjna<br>Aplikacja<br>DcAdmin<br>Regularne<br>Zarezerwowane<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory base
Następujące właściwości są wspólne dla wszystkich rekordów w usłudze Azure Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi AzureActiveDirectory |
| recordType     | Usługi AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ zdarzenia usługi Azure AD. |
| właściwości rozszerzone | Rozszerzone właściwości zdarzeń usługi Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Azure logowania konto usługi Active Directory
Te rekordy są tworzone, gdy próbuje zalogować użytkownika usługi Active Directory.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi AzureActiveDirectory |
| recordType     | AzureActiveDirectoryAccountLogon |
| Aplikacja | Aplikacja, która wyzwala zdarzenia logowania, konta, takie jak pakietu Office 15. |
| Klient | Szczegółowe informacje o kliencie urządzenia, systemu operacyjnego urządzenia i przeglądarki urządzenia, które było używane dla zdarzenia logowania konta. |
| stanu logowania | Ta właściwość jest bezpośrednio z OrgIdLogon.LoginStatus. Mapowania różnych interesujące niepowodzeń logowania może być wykonywane przez alerty algorytmów. |
| UserDomain | Informacje tożsamości dzierżawy (TII). | 


### <a name="azure-active-directory"></a>Usługa Azure Active Directory
Te rekordy są tworzone podczas dodatkami lub zmiany zostały wprowadzone do usługi Azure Active Directory obiektów.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Usługi AzureActiveDirectory |
| recordType     | Usługi AzureActiveDirectory |
| AADTarget | Użytkownik wykonanej akcji (określone przez właściwość operacji). |
| Aktor | Nazwa użytkownika lub nazwy głównej usługi, który wykonał akcję. |
| ActorContextId | Identyfikator GUID aktora należącej do organizacji. |
| ActorIpAddress | Adres IP aktora w formacie adres IPV4 lub IPV6. |
| InterSystemsId | Identyfikator GUID śledzić akcje dotyczące składników w usłudze Office 365. |
| IntraSystemId |   Identyfikator GUID jest generowany przez usługę Azure Active Directory do śledzenia akcji. |
| SupportTicketId | Klient obsługuje identyfikator biletu działań w sytuacjach "act na imieniu of". |
| TargetContextId | Identyfikator GUID wybrany użytkownik należy do organizacji. |


### <a name="data-center-security"></a>Zabezpieczeń w centrum danych
Te rekordy są tworzone na podstawie danych inspekcji zabezpieczeń w centrum danych.  

| Właściwość | Opis |
|:--- |:--- |
| EffectiveOrganization | Nazwa dzierżawy, która była celem podniesienia uprawnień/polecenia cmdlet. |
| ElevationApprovedTime | Znacznik czasu po została zatwierdzona podniesienia uprawnień. |
| ElevationApprover | Nazwa menedżera firmy Microsoft. |
| ElevationDuration | Czas trwania, dla którego była aktywna podniesienia uprawnień. |
| ElevationRequestId |  Unikatowy identyfikator dla żądania podniesienia uprawnień. |
| ElevationRole | Zażądano roli podniesienia uprawnień. |
| ElevationTime | Godzina rozpoczęcia podniesienia uprawnień. |
| Godzina_rozpoczęcia | Godzina rozpoczęcia wykonywania poleceń cmdlet. |


### <a name="exchange-admin"></a>Administrator programu Exchange
Te rekordy są tworzone podczas wprowadzania zmian do konfiguracji programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeAdmin |
| ExternalAccess |  Określa, czy polecenie cmdlet zostało uruchomione przez użytkowników w organizacji przez personel centrum danych firmy Microsoft lub konta usługi Centrum danych lub administratora delegowanego. Wartość False wskazuje, czy polecenie cmdlet zostało uruchomione przez osobę będącą w Twojej organizacji. Wartość True wskazuje, że polecenie cmdlet zostało uruchomione przez personel centrum danych, konto usługi Centrum danych lub administratora delegowanego. |
| ModifiedObjectResolvedName |  Jest to przyjazny dla użytkownika nazwa obiektu, który został zmodyfikowany przez polecenie cmdlet. To jest rejestrowane tylko wtedy, gdy polecenie cmdlet modyfikuje obiektu. |
| Nazwa_organizacji | Nazwa dzierżawcy. |
| OriginatingServer | Nazwa serwera, z którego wykonano polecenia cmdlet. |
| Parametry | Nazwa i wartość dla wszystkich parametrów, które były używane z poleceniem cmdlet identyfikowany we właściwości operacji. |


### <a name="exchange-mailbox"></a>Skrzynek pocztowych programu Exchange
Te rekordy są tworzone podczas zmiany lub dodatki zostały wprowadzone do skrzynek pocztowych programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| ClientInfoString | Informacje o kliencie poczty e-mail, którego użyto do wykonania tej operacji, takich jak wersja przeglądarki, wersja programu Outlook i informacje o urządzeniach przenośnych. |
| Client_IPAddress | Adres IP urządzenia, który został użyty podczas operacji został zapisany w dzienniku. Adres IP jest wyświetlany w formacie adres IPv4 lub IPv6. |
| ClientMachineName | Nazwa komputera hostującego klienta programu Outlook. |
| ClientProcessName | Klient poczty e-mail, który został użyty do uzyskania dostępu do skrzynki pocztowej. |
| ClientVersion | Wersja klienta poczty e-mail. |
| InternalLogonType | Zarezerwowany do użytku wewnętrznego. |
| Logon_Type | Wskazuje typ użytkownika, który dostęp do skrzynki pocztowej i wykonać operację, której został zapisany w dzienniku. |
| LogonUserDisplayName |    Przyjazna dla użytkownika nazwa użytkownika, który wykonał działanie. |
| LogonUserSid | Identyfikator SID użytkownika, który wykonał działanie. |
| MailboxGuid | Identyfikator GUID programu Exchange skrzynki pocztowej, którego próbował uzyskać dostęp. |
| MailboxOwnerMasterAccountSid | Skrzynka pocztowa właściciela konta głównego identyfikatora SID konta. |
| MailboxOwnerSid | Identyfikator SID właściciela skrzynki pocztowej. |
| MailboxOwnerUPN | Adres e-mail osoby, która jest właścicielem skrzynki pocztowej, którego próbował uzyskać dostęp. |


### <a name="exchange-mailbox-audit"></a>Inspekcji skrzynki pocztowej programu Exchange
Te rekordy są tworzone po utworzeniu wpisu inspekcji skrzynki pocztowej.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| Element | Reprezentuje element, na którym została wykonana operacja | 
| SendAsUserMailboxGuid | Skrzynka pocztowa, do którego próbował uzyskać dostęp do wysyłania wiadomości e-mail jako identyfikator GUID programu Exchange. |
| SendAsUserSmtp | Adresu SMTP użytkownika, który jest Personifikowany. |
| SendonBehalfOfUserMailboxGuid | Skrzynki pocztowej, którego próbował uzyskać dostęp do wysyłania wiadomości e-mail dla identyfikatora GUID programu Exchange. |
| SendOnBehalfOfUserSmtp | Adresu SMTP użytkownika, w imieniu którego są wysyłane wiadomości e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Grupy inspekcji skrzynki pocztowej programu Exchange
Te rekordy są tworzone podczas zmiany lub dodatki zostały wprowadzone do grupy programu Exchange.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informacje o każdym elemencie w grupie. |
| CrossMailboxOperations | Wskazuje, czy operacja związane z więcej niż jedną skrzynkę pocztową. |
| DestMailboxId | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa skrzynki pocztowej docelowy identyfikator GUID. |
| DestMailboxOwnerMasterAccountSid | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa identyfikator SID dla konta głównego identyfikator SID właściciela skrzynki pocztowej docelowej. |
| DestMailboxOwnerSid | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa identyfikator SID skrzynki pocztowej docelowej. |
| DestMailboxOwnerUPN | Ustawiona tylko wtedy, gdy parametr CrossMailboxOperations ma wartość PRAWDA. Określa nazwę UPN właściciela skrzynki pocztowej docelowej. |
| DestFolder | Folder docelowy dla operacji, takich jak przeniesienie. |
| Folder | Folder, w której znajduje się grupa elementów. |
| Foldery |     Informacje o folderach źródła uwzględnionego w operacji; na przykład, jeśli foldery są zaznaczone, a następnie usuwany. |


### <a name="sharepoint-base"></a>Podstawa programu SharePoint
Te właściwości są wspólne dla wszystkich rekordów z programu SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| Źródła zdarzeń | Określa, czy wystąpiło zdarzenie w programie SharePoint. Możliwe wartości to SharePoint lub ObjectModel. |
| ItemType | Typ obiektu, który został dostępne lub zmodyfikowany. Poniższa tabela ItemType szczegółowe na typach obiektów. |
| MachineDomainInfo | Informacje o operacji synchronizacji urządzenia. Te informacje są dostarczane tylko wtedy, gdy znajduje się on na żądanie. |
| MachineId |   Informacje o operacji synchronizacji urządzenia. Te informacje są dostarczane tylko wtedy, gdy znajduje się on na żądanie. |
| Site_ | Identyfikator GUID lokacji, w którym znajduje się plik lub folder, dostępne dla użytkownika. |
| Source_Name | Obiekt, który wywołał operację inspekcji. Możliwe wartości to SharePoint lub ObjectModel. |
| UserAgent | Informacje dotyczące klienta lub przeglądarki użytkownika. Informacje te są dostarczane przez klienta lub przeglądarki. |


### <a name="sharepoint-schema"></a>Schemat programu SharePoint
Te rekordy są tworzone podczas zmiany konfiguracji zostały wprowadzone w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | Sharepoint |
| CustomEvent | Opcjonalny ciąg dla niestandardowych zdarzeń. |
| Event_Data |  Opcjonalne ładunek dla niestandardowych zdarzeń. |
| ModifiedProperties | Właściwość jest uwzględniony w zdarzenia administrator, takie jak dodanie użytkownika jako członka lokację lub grupy administracyjnej kolekcji witryn. Właściwość zawiera nazwę właściwości, która została zmodyfikowana (na przykład grupy administratora witryny), nowa wartość zmodyfikowanej właściwości (takie użytkownik, który został dodany jako administratora witryny), a wartość poprzedniego zmodyfikowanego obiektu. |


### <a name="sharepoint-file-operations"></a>Operacje na plikach programu SharePoint
Te rekordy są tworzone w odpowiedzi na operacje na plikach w programie SharePoint.

| Właściwość | Opis |
|:--- |:--- |
| OfficeWorkload | Sharepoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Rozszerzenie pliku, który jest kopiowany lub przenieść. Ta właściwość jest wyświetlany tylko w przypadku zdarzeń FileCopied i FileMoved. |
| Parametr DestinationFileName | Nazwa pliku, który jest skopiowany lub przeniesiony. Ta właściwość jest wyświetlany tylko w przypadku zdarzeń FileCopied i FileMoved. |
| DestinationRelativeUrl | Adres URL folderu docelowego, w którym skopiować lub przenieść pliku. Kombinacja wartości parametrów SiteURL DestinationRelativeURL i parametr DestinationFileName jest taka sama jak wartość właściwości identyfikator obiektu, który jest pełna nazwa ścieżki do pliku, który został skopiowany. Ta właściwość jest wyświetlany tylko w przypadku zdarzeń FileCopied i FileMoved. |
| SharingType | Typ uprawnienia, które zostały przypisane do użytkownika, który został udostępniony zasób udostępniania. Ten użytkownik jest identyfikowany przez parametr UserSharedWith. |
| Site_Url | Adres URL witryny, w którym znajduje się plik lub folder, dostępne dla użytkownika. |
| SourceFileExtension | Rozszerzenie pliku, którego użytkownik próbował uzyskać dostęp. Ta właściwość jest pusta, jeśli obiekt, który uzyskano jest folderem. |
| SourceFileName |  Nazwa pliku lub folderu dostępne dla użytkownika. |
| SourceRelativeUrl | Adres URL folderu, który zawiera plik dostępne dla użytkownika. Kombinacja wartości parametrów SiteURL, SourceRelativeURL i SourceFileName jest taka sama jak wartość właściwości identyfikator obiektu jest pełna nazwa ścieżki dla pliku dostępne dla użytkownika. |
| UserSharedWith |  Użytkownik, który został udostępniony zasób. |




## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
Poniższa tabela zawiera przykładowe wyszukiwania w dzienniku dotyczące rekordów aktualizacji zbieranych przez to rozwiązanie.

| Zapytanie | Opis |
| --- | --- |
|Liczba wszystkich operacji w ramach subskrypcji usługi Office 365 |Typ = OfficeActivity &#124; miar count() przez operację |
|Użycie witryny programu SharePoint|Typ = OfficeActivity OfficeWorkload = sharepoint &#124; miar count() jako liczba wg SiteUrl &#124; sortowania asc liczba|
|Operacje na plikach dostępu przez użytkownika typu|Typ = OfficeActivity OfficeWorkload = sharepoint operacji = FileAccessed &#124; miar count() przez UserType|
|Wyszukiwanie z określone słowo kluczowe|Typ = OfficeActivity OfficeWorkload = usługi azureactivedirectory "Test"|
|Monitor działań zewnętrznych na serwerze Exchange|Typ = OfficeActivity OfficeWorkload = exchange ExternalAccess = true|



## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli rozwiązania usługi Office 365 nie zbiera danych zgodnie z oczekiwaniami, sprawdź jego stan w portalu OMS pod adresem **ustawienia** -> **połączonych źródeł** -> **usługi Office 365**. W poniższej tabeli opisano każdy stan.

| Stan | Opis |
|:--|:--|
| Aktywne | Subskrypcja usługi Office 365 jest aktywna i obciążenie połączył się z obszaru roboczego analizy dzienników. |
| Oczekujące | Subskrypcja usługi Office 365 jest aktywna, ale obciążenie nie jest jeszcze podłączony do obszaru roboczego analizy dzienników pomyślnie. Przy pierwszym połączeniu subskrypcji usługi Office 365 wszystkich obciążeń w tym stanie dopóki będą pomyślnym nawiązaniu połączenia. Zezwól na 24 godziny dla wszystkich obciążeń przełączyć się na aktywny. |
| Nieaktywna | Subskrypcja usługi Office 365 jest nieaktywny. Sprawdź stronę administracyjne usługi Office 365, aby uzyskać szczegółowe informacje. Po aktywowaniu subskrypcji usługi Office 365, odłączyć go z obszaru roboczego analizy dzienników, a następnie połącz go ponownie, aby rozpocząć odbieranie danych. |



## <a name="next-steps"></a>Kolejne kroki
* Korzystanie z wyszukiwania w dzienniku usługi [Log Analytics](../log-analytics/log-analytics-log-searches.md) w celu wyświetlania szczegółowych danych aktualizacji.
* [Tworzenie własnych pulpity nawigacyjne](../log-analytics/log-analytics-dashboards.md) Aby wyświetlić ulubione zapytania wyszukiwania usługi Office 365.
* [Tworzenie alertów](../log-analytics/log-analytics-alerts.md) aktywnego powiadamianych ważne działań usługi Office 365.  

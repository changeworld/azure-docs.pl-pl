---
title: Dokumentacja ustawień dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Omówienie obsługiwanych ustawień w zasadach dostępu warunkowego usługi Azure Active Directory.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 300367ee4e4bdb412bf4e5f25ba6cea067e18ed3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Informacje dotyczące ustawień dostępu warunkowego w usłudze Azure Active Directory

Można użyć [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) do kontrolowania sposobu autoryzowani użytkownicy mogą uzyskać dostępu do zasobów.   

Ten artykuł zawiera informacje o pomocy technicznej dla opcji konfiguracji zasad dostępu warunkowego: 

- Przydziały aplikacji w chmurze

- Warunek platformy urządzenia 

- Stan aplikacji klienta

- Wymagania aplikacji zatwierdzonych klienta


Jeśli nie jest to informacje, które użytkownik chce się dowiedzieć, zostaw komentarz na końcu tego artykułu.

## <a name="cloud-apps-assignments"></a>Przydziały aplikacji w chmurze

Za pomocą zasad dostępu warunkowego, można kontrolować, jak użytkownicy uzyskują dostęp do Twojego [aplikacji w chmurze](active-directory-conditional-access-conditions.md#cloud-apps). Po skonfigurowaniu zasad dostępu warunkowego, musisz wybrać co najmniej jednej aplikacji w chmurze. 

![Wybierz zasady aplikacji w chmurze](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplikacje w chmurze firmy Microsoft

Zasady dostępu warunkowego można przypisać do następujących aplikacji w chmurze firmy Microsoft:

- Usługa Azure Information Protection - [Dowiedz się więcej](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Yammer pakietu Microsoft Office 365

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (w tym usługi OneDrive dla firm i Project Online)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Inne aplikacje 

Oprócz aplikacji firmy Microsoft w chmurze można przypisać zasady dostępu warunkowego do następujących typów aplikacji w chmurze:

- Azure AD połączonych aplikacji

- Wstępnie zintegrowanych federacyjnych aplikacja oprogramowania jako usługa (SaaS)

- Aplikacje używające hasło rejestracji jednokrotnej (SSO)

- -Aplikacji biznesowych

- Aplikacje, które używają serwera Proxy aplikacji usługi Azure AD


## <a name="device-platform-condition"></a>Warunek platformy urządzenia

W zasadach dostępu warunkowego można skonfigurować warunek platformy urządzenia powiązać zasady systemu operacyjnego na komputerze klienckim. Dostęp warunkowy usługi Azure AD obsługuje następujące platformy urządzeń:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Powiązanie zasady dostępu do systemu operacyjnego klienta](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Stan aplikacji klienta 

W zasadach dostępu warunkowego, można skonfigurować [aplikacjach klienckich](active-directory-conditional-access-conditions.md#client-apps) warunku powiązać zasady aplikacji klienta, który zainicjował próba dostępu. Ustaw klienta warunku aplikacji, aby przydzielić lub blokowanie dostępu, gdy podejmowana jest próba dostępu z następujących typów aplikacji klienta:

- Przeglądarka
- Aplikacje mobilne i aplikacje komputerowe

![Kontrolowanie dostępu do aplikacji klienta](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Obsługiwane przeglądarki 

Zasady dostępu warunkowego, można wybrać **przeglądarki** jako aplikację klienta.

![Kontrolowanie dostępu do obsługiwanych przeglądarek](./media/active-directory-conditional-access-technical-reference/05.png)

To ustawienie działa z przeglądarki. Jednak do zaspokojenia zasad urządzenia, takie jak wymóg urządzenie zgodne z następujących systemów operacyjnych i przeglądarek są obsługiwane:


| System operacyjny                     | Przeglądarki                            | Pomoc techniczna     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Edge, Chrome     | ![Zaznacz][1] |
| Windows 8 / 8.1        | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| Windows 7              | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Zaznacz][1] |
| Android                | Chrome, Intune Managed Browser      | ![Zaznacz][1] |
| Windows Phone          | Program Internet Explorer, krawędzi             | ![Zaznacz][1] |
| Windows Server 2016    | Program Internet Explorer, krawędzi             | ![Zaznacz][1] |
| Windows Server 2016    | Chrome                              | Wkrótce |
| Windows Server 2012 R2 | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| Windows Server 2008 R2 | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| macOS                  | Chrome, Safari                      | ![Zaznacz][1] |



#### <a name="chrome-support"></a>Obsługa programu Chrome

Chrome działu pomocy technicznej w **systemu Windows 10 twórców aktualizację (wersja 1703)** lub później, zainstaluj [to rozszerzenie](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Chrome działu pomocy technicznej w **7 i Windows 8.1**, utwórz następujący klucz rejestru:

|    |    |
|--- | ---|
|Ścieżka | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|Name (Nazwa) | 1 |
|Typ | REG_SZ (ciąg) |
|Dane | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Te przeglądarki obsługuje uwierzytelnianie urządzenia, umożliwiając urządzenia można zidentyfikować ani weryfikowana pod kątem zasad. Sprawdzanie urządzenia kończy się niepowodzeniem, jeśli przeglądarka jest uruchomiony w trybie prywatnym. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Obsługiwane klienci pulpitów i aplikacji mobilnych

Zasady dostępu warunkowego, można wybrać **aplikacji mobilnych i klasycznych klientów** jako aplikację klienta.


![Kontrolowanie dostępu do obsługiwanych aplikacji mobilnych lub klienci usług pulpitu](./media/active-directory-conditional-access-technical-reference/06.png)


To ustawienie ma wpływ na prób dostępu z następujących aplikacji mobilnych i będących klientami pulpitu: 


|Aplikacje klienckie|Usługa docelowa|Platforma|
|---|---|---|
|Usługa Azure RemoteApp|Usługa Azure RemoteApp|Windows 10, Windows 8.1, Windows 7, iOS, Android i Mac OS X|
|Aplikacji programu Dynamics CRM|Dynamics CRM|Windows 10, Windows 8.1, iOS i Android|
|Osoby-mail/kalendarza aplikacji Outlook 2016 Outlook 2013 (z nowoczesnego uwierzytelniania)|Office 365 Exchange Online|Windows 10|
|Zasady MFA i lokalizację dla aplikacji. Urządzenia, na podstawie zasad nie są obsługiwane. |Moje aplikacje usługi aplikacji|Android i iOS|
|Kontroluje usługi zespoły firmy Microsoft — do wszystkich usług, które obsługują Teams firmy Microsoft i wszystkie jego aplikacjach klienckich - pulpitu systemu Windows, iOS, Android, WP i klienta sieci web|Microsoft Teams|Windows 10, Windows 8.1, Windows 7, iOS, Android i macOS |
|Aplikacje pakietu Office 2016, Office 2013 (z nowoczesnego uwierzytelniania), usługi OneDrive Synchronizowanie klienta (zobacz [uwagi](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Usługi Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Aplikacje pakietu Office 2016, Office uniwersalnych aplikacji, Office 2013 (z nowoczesnego uwierzytelniania), klient synchronizacji usługi OneDrive (zobacz [uwagi](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), obsługa grupy Office jest planowane w przyszłości, obsługi aplikacji programu SharePoint jest planowane w przyszłości|Usługi Office 365 SharePoint Online|Windows 10|
|Pakiet Office 2016 dla macOS (Word, Excel, PowerPoint, tylko w programie OneNote). OneDrive dla firm Obsługa planowanych w przyszłości|Usługi Office 365 SharePoint Online|Mac OS X|
|Aplikacje mobilne pakietu Office|Usługi Office 365 SharePoint Online|Android, iOS|
|Aplikacja usługi Yammer pakietu Office|Yammer usługi Office 365|Windows 10, iOS, Android|
|Outlook 2016 (Urząd macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013 (z nowoczesnego uwierzytelniania), usługi Skype dla firm (z nowoczesnego uwierzytelniania)|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Outlook aplikacji mobilnej|Office 365 Exchange Online|Android, iOS|
|Aplikacji Power BI|Usługa Power BI|Windows 10, Windows 8.1, Windows 7, Android i iOS|
|Skype dla firm|Office 365 Exchange Online|Android, IOS |
|Visual Studio Team Services aplikacji|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS i Android|



## <a name="approved-client-app-requirement"></a>Wymagania aplikacji zatwierdzonych klienta 

Zasady dostępu warunkowego można wymagać, czy dostęp jest próba aplikacji w wybranej chmurze potrzeb z aplikacji klienckiej zatwierdzone. 

![Kontroli dostępu dla aplikacji klienckich zatwierdzone](./media/active-directory-conditional-access-technical-reference/21.png)

To ustawienie dotyczy następujących aplikacji klienta:


- Usługa Microsoft Intune Managed Browser
- Microsoft PowerBI
- Microsoft Invoicing
- Microsoft Launcher
- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype dla firm
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio
- Microsoft Word



**Uwagi**

- Aplikacje klienta zatwierdzonych obsługuje funkcja zarządzania aplikacjami mobilnymi usługi Intune.

- **Wymaga aplikacji klienta zatwierdzonych** wymaganie:

    - Obsługuje tylko systemy iOS i Android dla [warunku platformy urządzenia](#device-platforms-condition).


## <a name="next-steps"></a>Kolejne kroki

- Omówienie dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego w środowisku, zobacz [zalecane wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



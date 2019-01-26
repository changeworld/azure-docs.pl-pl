---
title: Dokumentacja ustawień dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zapoznaj się z omówieniem obsługiwanych ustawień w zasadach dostępu warunkowego usługi Azure Active Directory.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: dbb8417975d051b4a13d6f94fca76fd3bf321a35
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55074247"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Informacje dotyczące ustawień dostępu warunkowego w usłudze Azure Active Directory

Możesz użyć [dostępu warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) do kontrolowania sposobu autoryzowani użytkownicy mogą uzyskiwać dostęp do zasobów usługi.   

Ten artykuł zawiera informacje dotyczące pomocy technicznej dla opcji konfiguracji zasad dostępu warunkowego: 

- Przypisania aplikacji w chmurze

- Warunek platformy urządzeń 

- Stan aplikacji klienta

- Wymagania aplikacji zatwierdzone klienta


Jeśli nie jest to informacje, których szukasz, pozostaw komentarz na końcu tego artykułu.

## <a name="cloud-apps-assignments"></a>Przypisania aplikacji w chmurze

Za pomocą zasad dostępu warunkowego, możesz kontrolować dostęp użytkowników Twojej [aplikacje w chmurze](conditions.md#cloud-apps). Po skonfigurowaniu zasad dostępu warunkowego, musisz wybrać co najmniej jedną aplikację w chmurze. 

![Wybierz aplikacje w chmurze dla zasad](./media/technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplikacje w chmurze firmy Microsoft

Zasady dostępu warunkowego można przypisać do następujących aplikacji w chmurze firmy Microsoft:

- Usługa Azure Information Protection — [Dowiedz się więcej](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Usługa Azure SQL Database — [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)

- Microsoft Dynamics 365

- Microsoft Office 365 w usłudze Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (w tym usługi OneDrive dla firm i usługi Project Online)

- Microsoft Power BI 

- Azure DevOps

- Microsoft Teams


### <a name="other-applications"></a>Inne aplikacje 

Oprócz aplikacji w chmurze firmy Microsoft można przypisać zasady dostępu warunkowego do następujących typów aplikacji w chmurze:

- Aplikacje połączone usługi AD systemu Azure

- Wstępnie zintegrowane federacyjnego aplikacja oprogramowania jako usługi (SaaS)

- Aplikacje, które używają hasła logowania jednokrotnego (SSO)

- Line-of-business aplikacji

- Aplikacje, które używają serwera Proxy aplikacji usługi Azure AD


## <a name="device-platform-condition"></a>Warunek platformy urządzeń

W zasadach dostępu warunkowego można skonfigurować warunek platformy urządzenia, z którego chcesz powiązać zasady dla systemu operacyjnego na komputerze klienckim. Dostęp warunkowy usługi Azure AD obsługuje następujące platformy urządzeń:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Powiązać zasady dostępu do systemu operacyjnego klienta](./media/technical-reference/41.png)





## <a name="client-apps-condition"></a>Stan aplikacji klienta 

W zasadach dostępu warunkowego można skonfigurować [aplikacje klienckie](conditions.md#client-apps) warunek, aby powiązać zasady do aplikacji klienckiej, który zainicjował próba dostępu. Warunek klienta aplikacji można udzielić lub blokowanie dostępu, gdy podejmowana jest próba dostępu z następujących typów aplikacji klienckich:

- Przeglądarka
- Aplikacje mobilne i aplikacje komputerowe

![Kontrola dostępu dla aplikacji klienckich](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Obsługiwane przeglądarki 

Zasady dostępu warunkowego, można wybrać **przeglądarek** jako aplikację kliencką.

![Kontrola dostępu do obsługiwanych przeglądarek](./media/technical-reference/05.png)

To ustawienie działa ze wszystkimi przeglądarkami. Jednak do zasad urządzenia, takie jak wymaganie zgodnego urządzenia spełniają następujące systemy operacyjne i przeglądarki są obsługiwane:


| System operacyjny                     | Przeglądarki                            | Pomoc techniczna     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     | ![Zaznacz][1] |
| Windows 8 / 8.1        | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| Windows 7              | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| iOS                    | Safari, usługa Intune Managed Browser      | ![Zaznacz][1] |
| Android                | Chrome, usługa Intune Managed Browser      | ![Zaznacz][1] |
| Windows Phone          | Internet Explorer, Microsoft Edge             | ![Zaznacz][1] |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             | ![Zaznacz][1] |
| Windows Server 2016    | Chrome                              | Wkrótce |
| Windows Server 2012 R2 | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| Windows Server 2008 R2 | Program Internet Explorer, Chrome           | ![Zaznacz][1] |
| macOS                  | Chrome, Safari                      | ![Zaznacz][1] |



#### <a name="chrome-support"></a>Pomocy technicznej dla programu Chrome

Dla programu Chrome obsługi w programie **systemu Windows 10 dla kreatywnych (wersja 1703)** lub nowszej, zainstaluj [to rozszerzenie](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Aby automatycznie wdrożyć to rozszerzenie przeglądarki Chrome, utwórz następujący klucz rejestru:

|    |    |
|--- | ---|
|Ścieżka | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|Name (Nazwa) | 1 |
|Typ | REG_SZ (ciąg) |
|Dane | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx

Dla programu Chrome obsługi w programie **7 i Windows 8.1**, utwórz następujący klucz rejestru:

|    |    |
|--- | ---|
|Ścieżka | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|Name (Nazwa) | 1 |
|Typ | REG_SZ (ciąg) |
|Dane | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Te przeglądarki obsługują uwierzytelnianie urządzeń, umożliwiając można zidentyfikowane i zweryfikować względem zasad. Sprawdzanie urządzenia kończy się niepowodzeniem, jeśli przeglądarka działa w trybie prywatnym. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Obsługiwane aplikacje mobilne i klienci stacjonarni

Zasady dostępu warunkowego, można wybrać **aplikacje mobilne i klienci stacjonarni** jako aplikację kliencką.


![Kontrola dostępu dla obsługiwane aplikacje mobilne i klienci stacjonarni](./media/technical-reference/06.png)


To ustawienie ma wpływ na dostęp podjętych następujące aplikacje mobilne i klienci stacjonarni: 


|Aplikacje klienckie|Usługa docelowa|Platforma|
|---|---|---|
|Usługa Azure RemoteApp|Usługa Azure RemoteApp|Windows 10, Windows 8.1, Windows 7, iOS, Android i Mac OS X|
|Aplikacja CRM Dynamics|Dynamics CRM|Windows 10, Windows 8.1, iOS i Android|
|Aplikacja poczty/kalendarz/osób, Outlook 2016 Outlook 2013 |Office 365 Exchange Online|Windows 10|
|Zasady uwierzytelniania Wieloskładnikowego i lokalizacji dla aplikacji. Urządzenia, na podstawie zasad nie są obsługiwane. |Wszystkie usługi Moje aplikacje usługi app service|Android i iOS|
|Usługi Microsoft Teams — w ten sposób kontroluje wszystkie usługi, które obsługują Microsoft Teams i wszystkie jej aplikacje klient — Windows Desktop z systemem iOS, Android, WP i klienta sieci web|Microsoft Teams|Windows 10, Windows 8.1, Windows 7, iOS, Android i macOS |
|Klient synchronizacji OneDrive aplikacje pakietu Office 2013, pakiet Office 2016 (zobacz [uwagi](https://support.office.com/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Aplikacje pakietu Office 2016, aplikacje uniwersalne pakietu Office, pakietu Office 2013, klient synchronizacji OneDrive (zobacz [uwagi](https://support.office.com/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), w przyszłości planujemy obsługę grup usługi Office, w przyszłości planujemy obsługę aplikacji programu SharePoint|Office 365 SharePoint Online|Windows 10|
|Pakiety Office 2016 dla systemu macOS (Word, Excel, PowerPoint, OneNote tylko). W przyszłości planujemy usługi OneDrive dla firm pomocy technicznej|Office 365 SharePoint Online|Mac OS X|
|Aplikacje mobilne pakietu Office|Office 365 SharePoint Online|Android, iOS|
|Aplikacja usługi Yammer pakietu Office|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (pakiet Office dla systemu macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013, Skype dla firm|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Aplikacja mobilna Outlook|Office 365 Exchange Online|Android, iOS|
|Aplikacja usługi Power BI|Usługa Power BI|Windows 10, Windows 8.1, Windows 7, Android i iOS|
|Skype dla firm|Office 365 Exchange Online|Android, IOS |
|Aplikacja Azure DevOps|Azure DevOps|Windows 10, Windows 8.1, Windows 7, iOS i Android|


## <a name="support-for-legacy-authentication"></a>Obsługa starszych uwierzytelniania

Wybierając **inni klienci**, można określić warunek, który wpływa na aplikacje używające uwierzytelniania podstawowego przy użyciu protokołów poczty, takich jak IMAP, MAPI, POP, SMTP i starsze aplikacje pakietu Office, które nie korzystają z nowoczesnego uwierzytelniania.  

![Inni klienci](./media/technical-reference/11.png)

Aby uzyskać więcej informacji, zobacz [aplikacje klienckie](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Wymaganie aplikacji zatwierdzone klienta 

W zasadach dostępu warunkowego można wymagać, że dostęp jest próba aplikacji w wybranej chmurze musi zostać wykonane z zatwierdzonej aplikacji klienckiej. 

![Kontrola dostępu dla zatwierdzonych aplikacji klienckich](./media/technical-reference/21.png)

To ustawienie dotyczy następujących aplikacji klienta:


- Microsoft Intune Managed Browser
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
- Microsoft To-Do
- Microsoft Stream
- Microsoft Edge



**Uwagi**

- Aplikacje zatwierdzone klienta obsługuje funkcja zarządzania aplikacjami mobilnymi usługi Intune.

- **Wymagaj zatwierdzonej aplikacji klienckiej** wymagania:

    - Obsługuje tylko systemy iOS i Android, aby uzyskać [warunek platformy urządzenia](#device-platforms-condition).


## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd dostępu warunkowego, zobacz [co to jest dostęp warunkowy w usłudze Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego w danym środowisku, zobacz [zalecane rozwiązania w zakresie dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).



<!--Image references-->
[1]: ./media/technical-reference/01.png



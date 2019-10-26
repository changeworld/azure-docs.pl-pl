---
title: Azure Active Directory informacje o ustawieniach dostępu warunkowego | Microsoft Docs
description: Zapoznaj się z omówieniem obsługiwanych ustawień w zasadach dostępu warunkowego Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: reference
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a3518dfcad3678dc298ba8529e731d48ec1d195
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893472"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory informacje o ustawieniach dostępu warunkowego

Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) można kontrolować sposób, w jaki autoryzowani użytkownicy mogą uzyskiwać dostęp do zasobów.

Ten artykuł zawiera informacje o pomocy technicznej dotyczące następujących opcji konfiguracji w zasadach dostępu warunkowego:

- Przypisania aplikacji w chmurze
- Warunek platformy urządzenia
- Warunek aplikacji klienckich
- Wymagania dotyczące zatwierdzonej aplikacji klienckiej

Jeśli nie są to informacje, których szukasz, pozostaw komentarz na końcu tego artykułu.

## <a name="cloud-apps-assignments"></a>Przypisania aplikacji w chmurze

Korzystając z zasad dostępu warunkowego, można kontrolować sposób, w jaki użytkownicy uzyskują dostęp do [aplikacji w chmurze](conditions.md#cloud-apps-and-actions). Konfigurując zasady dostępu warunkowego, należy wybrać co najmniej jedną aplikację w chmurze. 

![Wybierz aplikacje w chmurze dla zasad](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Aplikacje w chmurze firmy Microsoft

Zasady dostępu warunkowego można przypisać do następujących aplikacji w chmurze firmy Microsoft:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database i magazyn danych — [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Analiza Application Insights firmy Microsoft
- Microsoft Azure Information Protection — [Dowiedz się więcej](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Zarządzanie Microsoft Azureami — [Dowiedz się więcej](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure zarządzanie subskrypcjami
- Microsoft Cloud App Security
- Portal Access Control Microsoft Commerce Tools
- Usługa uwierzytelniania Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Rejestracja Microsoft Intune
- Microsoft Planner
- Microsoft PowerApps
- Wyszukiwanie w usłudze Bing firmy Microsoft
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Pakiet Office 365 Exchange Online
- Pakiet Office 365 SharePoint Online
- Usługa Yammer pakietu Office 365
- Office Delve
- Pakiet Office Sway
- Outlook Groups
- Usługa Power BI
- Project Online
- Skype dla firm Online
- Wirtualna sieć prywatna (VPN)
- Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender

### <a name="other-applications"></a>Inne aplikacje

Oprócz aplikacji w chmurze firmy Microsoft można przypisać zasady dostępu warunkowego do następujących typów aplikacji w chmurze:

- Aplikacje połączone z usługą Azure AD
- Wstępnie zintegrowana aplikacja federacyjna oprogramowania jako usługi (SaaS)
- Aplikacje korzystające z logowania jednokrotnego (SSO) hasła
- Aplikacje biznesowe
- Aplikacje korzystające z usługi Azure serwer proxy aplikacji usługi Azure AD

## <a name="device-platform-condition"></a>Warunek platformy urządzenia

W zasadach dostępu warunkowego można skonfigurować warunek platformy urządzenia, aby powiązać zasady z systemem operacyjnym na kliencie. Dostęp warunkowy usługi Azure AD obsługuje następujące platformy urządzeń:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Powiązanie zasad dostępu z systemem operacyjnym klienta](./media/technical-reference/41.png)

W przypadku zablokowania starszego uwierzytelniania przy użyciu warunku **other clients** można również ustawić warunek platformy urządzenia.

## <a name="client-apps-condition"></a>Warunek aplikacji klienckich

W zasadach dostępu warunkowego można skonfigurować warunek [aplikacje klienckie](conditions.md#client-apps) , aby powiązać zasady z aplikacją kliencką, która zainicjowała próbę dostępu. Ustaw warunek aplikacje klienckie na udzielenie lub zablokowanie dostępu podczas próby dostępu z następujących typów aplikacji klienckich:

- Przeglądarka
- Aplikacje mobilne i aplikacje klasyczne

![Kontrola dostępu do aplikacji klienckich](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Obsługiwane przeglądarki

W zasadach dostępu warunkowego można wybrać **przeglądarki** jako aplikację kliencką.

![Kontrola dostępu do obsługiwanych przeglądarek](./media/technical-reference/05.png)

To ustawienie działa ze wszystkimi przeglądarkami. Jednak w celu spełnienia zasad dotyczących urządzeń, takich jak zgodne wymagania dotyczące urządzeń, obsługiwane są następujące systemy operacyjne i przeglądarki:

| System operacyjny                     | Browser                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8/8,1        | Internet Explorer, program Chrome                       |
| Windows 7              | Internet Explorer, program Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Dlaczego widzę monit dotyczący certyfikatu w przeglądarce

W systemach Windows 7, iOS, Android i macOS usługa Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest inicjowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD.  Gdy użytkownik po raz pierwszy zaloguje się za pomocą przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik musi wybrać ten certyfikat przed użyciem przeglądarki.

#### <a name="chrome-support"></a>Obsługa programu Chrome

Aby uzyskać pomoc techniczną dla programu Chrome w **aktualizacji systemu Windows 10 dla twórców (wersja 1703)** lub nowszą, zainstaluj [rozszerzenie konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). To rozszerzenie jest wymagane, gdy zasady dostępu warunkowego wymagają szczegółowych informacji o urządzeniu.

Aby automatycznie wdrożyć to rozszerzenie w przeglądarkach programu Chrome, Utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Nazwa | 1 |
| Typ | REG_SZ (ciąg) |
| Dane | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

Aby uzyskać pomoc techniczną dla programu Chrome w **Windows 8.1 i 7**, Utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Nazwa | 1 |
| Typ | REG_SZ (ciąg) |
| Dane | {"wzorzec": "https://device.login.microsoftonline.com", "filter": {"WYSTAWCa": {"CN": "MS-Organization-Access"}}} |

Te przeglądarki obsługują uwierzytelnianie urządzeń, co pozwala na identyfikację i weryfikację urządzenia względem zasad. Sprawdzenie urządzenia kończy się niepowodzeniem, jeśli przeglądarka działa w trybie prywatnym.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Obsługiwane aplikacje mobilne i klienci stacjonarni

W zasadach dostępu warunkowego można wybrać opcję **aplikacje mobilne i klienci stacjonarni** jako aplikacja kliencka.

![Kontrola dostępu do obsługiwanych aplikacji mobilnych lub klientów klasycznych](./media/technical-reference/06.png)

To ustawienie ma wpływ na próby dostępu wykonane z następujących aplikacji mobilnych i klientów stacjonarnych:

| Aplikacje klienckie | Usługa docelowa | Platforma |
| --- | --- | --- |
| Aplikacja Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS i Android |
| Aplikacja poczty/kalendarza/osoby, Outlook 2016, Outlook 2013 (z nowoczesnego uwierzytelniania)| Pakiet Office 365 Exchange Online | Windows 10 |
| Zasady usługi MFA i lokalizacji dla aplikacji. Zasady oparte na urządzeniach nie są obsługiwane.| Wszystkie aplikacje my App Service| Systemy Android i iOS |
| Usługi Microsoft Teams — kontroluje wszystkie usługi obsługujące Microsoft Teams i wszystkie jej aplikacje klienckie — Windows Desktop, iOS, Android, WP i klient sieci Web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android i macOS |
| Aplikacje pakietu Office 2016, pakiet Office 2013 (z nowoczesnego uwierzytelniania), klient synchronizacji usługi OneDrive (zobacz [uwagi](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Pakiet Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikacje pakietu Office 2016, uniwersalne aplikacje biurowe, pakiet Office 2013 (z nowoczesnego uwierzytelniania), klient synchronizacji usługi OneDrive (zobacz [uwagi](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), Obsługa grup pakietu Office jest planowana w przyszłości, obsługa aplikacji programu SharePoint jest planowana w przyszłości. | Pakiet Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, tylko OneNote). Obsługa usługi OneDrive dla firm zaplanowana w przyszłości| Pakiet Office 365 SharePoint Online| macOS|
| Pakiet Office 2019| Pakiet Office 365 SharePoint Online | Windows 10, macOS |
| Aplikacje mobilne pakietu Office | Pakiet Office 365 SharePoint Online | Android, iOS |
| Aplikacja Yammera pakietu Office | Usługa Yammer pakietu Office 365 | Windows 10, iOS, Android |
| Outlook 2019 | Pakiet Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office for macOS) | Pakiet Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (z nowoczesnego uwierzytelniania), Skype dla firm (z nowoczesnego uwierzytelniania) | Pakiet Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Aplikacja mobilna Outlook | Pakiet Office 365 Exchange Online | Android, iOS |
| Aplikacja Power BI | usługa Power BI | Windows 10, Windows 8.1, Windows 7, Android i iOS |
| Skype dla firm | Pakiet Office 365 Exchange Online| Android, IOS |
| Aplikacja Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS i Android |

## <a name="support-for-legacy-authentication"></a>Obsługa starszego uwierzytelniania

Wybierając **innych klientów**, można określić warunek, który ma wpływ na aplikacje używające uwierzytelniania podstawowego z protokołami pocztowymi, takimi jak IMAP, MAPI, pop, SMTP i starsze aplikacje pakietu Office, które nie używają nowoczesnego uwierzytelniania.  

![Inni klienci](./media/technical-reference/11.png)

Aby uzyskać więcej informacji, zobacz [aplikacje klienckie](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Wymagania dotyczące zatwierdzonej aplikacji klienckiej

W zasadach dostępu warunkowego można wymagać od zatwierdzonej aplikacji klienckiej próby dostępu do wybranych aplikacji w chmurze. 

![Kontrola dostępu do zatwierdzonych aplikacji klienckich](./media/technical-reference/21.png)

To ustawienie dotyczy następujących aplikacji klienckich:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype dla firm
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Uwagi**

- Zatwierdzone aplikacje klienckie obsługują funkcję zarządzania aplikacjami mobilnymi w usłudze Intune.
- Wymagane wymagania dotyczące **zatwierdzonej aplikacji klienckiej** :
   - Obsługuje tylko [warunek platformy](#device-platform-condition)dla systemów iOS i Android.

## <a name="app-protection-policy-requirement"></a>Wymaganie dotyczące zasad ochrony aplikacji 

W zasadach dostępu warunkowego można wymagać, aby zasady ochrony aplikacji były obecne w aplikacji klienckiej przed udostępnieniem dostępu do wybranych aplikacji w chmurze. 

![Kontrola dostępu przy użyciu zasad ochrony aplikacji](./media/technical-reference/22.png)

To ustawienie dotyczy następujących aplikacji klienckich:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Uwagi**

- Aplikacje dla zasad ochrony aplikacji obsługują funkcję zarządzania aplikacjami mobilnymi w usłudze Intune z ochroną zasad.
- Wymagania dotyczące wymagań **zasad ochrony aplikacji** :
    - Obsługuje tylko [warunek platformy](#device-platform-condition)dla systemów iOS i Android.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem dostępu warunkowego, zobacz [co to jest dostęp warunkowy w Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Aby skonfigurować zasady dostępu warunkowego w danym środowisku, zapoznaj się z [zaleceniami zalecanymi dla dostępu warunkowego w Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png

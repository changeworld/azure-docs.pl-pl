---
title: Informacje dotyczące ustawień usługi Azure Active Directory dostępu warunkowego | Dokumentacja firmy Microsoft
description: Zapoznaj się z omówieniem obsługiwanych ustawień w zasadach usługi Azure Active Directory dostępu warunkowego.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5919eebccad8d7f9e048ae07be296eaaaf8428eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112107"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Informacje dotyczące ustawień usługi Azure Active Directory dostępu warunkowego

Możesz użyć [dostępu warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) do kontrolowania sposobu autoryzowani użytkownicy mogą uzyskiwać dostęp do zasobów usługi.

Ten artykuł zawiera informacje dotyczące pomocy technicznej dla opcji konfiguracji zasad dostępu warunkowego:

- Przypisania aplikacji w chmurze
- Warunek platformy urządzeń
- Stan aplikacji klienta
- Wymagania aplikacji zatwierdzone klienta

Jeśli nie jest to informacje, których szukasz, pozostaw komentarz na końcu tego artykułu.

## <a name="cloud-apps-assignments"></a>Przypisania aplikacji w chmurze

Za pomocą zasad dostępu warunkowego, możesz kontrolować dostęp użytkowników Twojej [aplikacje w chmurze](conditions.md#cloud-apps-and-actions). Po skonfigurowaniu zasad dostępu warunkowego, musisz wybrać co najmniej jedną aplikację w chmurze. 

![Wybierz aplikacje w chmurze dla zasad](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Aplikacje w chmurze firmy Microsoft

Zasady dostępu warunkowego można przypisać do następujących aplikacji w chmurze firmy Microsoft:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database i Data Warehouse — [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights — analiza
- Microsoft Azure Information Protection — [Dowiedz się więcej](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Interfejs Microsoft Azure Management - [Dowiedz się więcej](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Zarządzanie subskrypcją platformy Microsoft Azure
- Microsoft Cloud App Security
- Microsoft Commerce narzędzi portalu kontroli dostępu
- Usługa uwierzytelniania narzędzia handlowych firmy Microsoft
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Rejestracja w usłudze Microsoft Intune
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- Microsoft wyszukiwania w usłudze Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Project Online
- Skype dla firm Online
- Virtual Private Network (VPN)
- Visual Studio App Center
- Usługa Windows Defender ATP

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

| System operacyjny                     | Przeglądarki                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8 / 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Microsoft Edge, Intune Managed Browser |
| Android                | Chrome, Microsoft Edge, Intune Managed Browser |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Dlaczego widzą monit w przeglądarce certyfikat

W Windows 7, iOS, Android i macOS usługi Azure AD identyfikuje urządzenia przy użyciu certyfikatu klienta, którego zainicjowano, gdy urządzenie jest zarejestrowane w usłudze Azure AD.  Gdy użytkownik najpierw loguje się za pośrednictwem przeglądarki użytkownik jest monitowany o wybierz certyfikat. Użytkownik musi wybrać ten certyfikat przed rozpoczęciem korzystania z przeglądarki.

#### <a name="chrome-support"></a>Pomocy technicznej dla programu Chrome

Dla programu Chrome obsługi w programie **systemu Windows 10 dla kreatywnych (wersja 1703)** lub nowszej, zainstaluj [to rozszerzenie](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Aby automatycznie wdrożyć to rozszerzenie przeglądarki Chrome, utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (ciąg) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Dla programu Chrome obsługi w programie **7 i Windows 8.1**, utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (ciąg) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Te przeglądarki obsługują uwierzytelnianie urządzeń, umożliwiając można zidentyfikowane i zweryfikować względem zasad. Sprawdzanie urządzenia kończy się niepowodzeniem, jeśli przeglądarka działa w trybie prywatnym.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Obsługiwane aplikacje mobilne i klienci stacjonarni

Zasady dostępu warunkowego, można wybrać **aplikacje mobilne i klienci stacjonarni** jako aplikację kliencką.

![Kontrola dostępu dla obsługiwane aplikacje mobilne i klienci stacjonarni](./media/technical-reference/06.png)

To ustawienie ma wpływ na dostęp podjętych następujące aplikacje mobilne i klienci stacjonarni:

| Aplikacje klienckie | Usługa docelowa | Platforma |
| --- | --- | --- |
| Aplikacja CRM Dynamics | Dynamics CRM | Windows 10, Windows 8.1, iOS i Android |
| Poczta/kalendarz/osób aplikacji Outlook 2016, Outlook 2013 (z nowoczesnego uwierzytelniania)| Office 365 Exchange Online | Windows 10 |
| Zasady uwierzytelniania Wieloskładnikowego i lokalizacji dla aplikacji. Urządzenia, na podstawie zasad nie są obsługiwane.| Wszystkie usługi Moje aplikacje usługi app service| Systemy android i iOS |
| Usługi Microsoft Teams — w ten sposób kontroluje wszystkie usługi, które obsługują Microsoft Teams i wszystkie jej aplikacje klient — Windows Desktop z systemem iOS, Android, WP i klienta sieci web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android i macOS |
| Aplikacje pakietu Office 2016, Office 2013 (z nowoczesnego uwierzytelniania), usługi OneDrive synchronizacji klienta (zobacz [uwagi](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikacje pakietu Office 2016, aplikacje uniwersalne pakietu Office, pakietu Office 2013 (z nowoczesnego uwierzytelniania), klient synchronizacji OneDrive (zobacz [uwagi](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), w przyszłości planujemy obsługę grup usługi Office, w przyszłości planujemy obsługę aplikacji programu SharePoint | Office 365 SharePoint Online | Windows 10 |
| Pakiety Office 2016 (Word, Excel, PowerPoint i OneNote tylko). W przyszłości planujemy usługi OneDrive dla firm pomocy technicznej| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | System Windows 10 w systemie macOS |
| Aplikacje mobilne pakietu Office | Office 365 SharePoint Online | Android, iOS |
| Aplikacja usługi Yammer pakietu Office | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | System Windows 10 w systemie macOS |
| Outlook 2016 (pakiet Office dla systemu macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (z nowoczesnego uwierzytelniania), usługi Skype dla firm (z nowoczesnego uwierzytelniania) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Aplikacja mobilna Outlook | Office 365 Exchange Online | Android, iOS |
| Aplikacja Power BI | Usługa Power BI | Windows 10, Windows 8.1, Windows 7, Android i iOS |
| Skype dla firm | Office 365 Exchange Online| Android, IOS |
| Visual Studio Team Services aplikacji | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS i Android |

## <a name="support-for-legacy-authentication"></a>Obsługa starszych uwierzytelniania

Wybierając **inni klienci**, można określić warunek, który wpływa na aplikacje używające uwierzytelniania podstawowego przy użyciu protokołów poczty, takich jak IMAP, MAPI, POP, SMTP i starsze aplikacje pakietu Office, które nie korzystają z nowoczesnego uwierzytelniania.  

![Inni klienci](./media/technical-reference/11.png)

Aby uzyskać więcej informacji, zobacz [aplikacje klienckie](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Wymaganie aplikacji zatwierdzone klienta

W zasadach dostępu warunkowego można wymagać, że dostęp jest próba aplikacji w wybranej chmurze musi zostać wykonane z zatwierdzonej aplikacji klienckiej. 

![Kontrola dostępu dla zatwierdzonych aplikacji klienckich](./media/technical-reference/21.png)

To ustawienie dotyczy następujących aplikacji klienta:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Edge
- Program Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Program Microsoft Outlook
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

- Aplikacje zatwierdzone klienta obsługuje funkcja zarządzania aplikacjami mobilnymi usługi Intune.
- **Wymagaj zatwierdzonej aplikacji klienckiej** wymagania:
   - Obsługuje tylko systemy iOS i Android, aby uzyskać [warunek platformy urządzenia](#device-platform-condition).

## <a name="app-protection-policy-requirement"></a>Wymaganie dotyczące zasad ochrony aplikacji 

W zasadach dostępu warunkowego można wymagać, że zasady ochrony aplikacji znajdować się na aplikację kliencką, zanim możliwy jest dostęp do aplikacji w wybranej chmurze. 

![Kontroluj dostęp za pomocą zasad ochrony aplikacji](./media/technical-reference/22.png)

To ustawienie dotyczy następujących aplikacji klienta:

- Microsoft OneDrive
- Program Microsoft Outlook

**Uwagi**

- Aplikacje dla zasad ochrony aplikacji obsługują funkcji zarządzania aplikacjami mobilnymi usługi Intune przy użyciu zasad ochrony.
- **Wymagają zasad ochrony aplikacji** wymagania:
    - Obsługuje tylko systemy iOS i Android, aby uzyskać [warunek platformy urządzenia](#device-platform-condition).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd dostępu warunkowego, zobacz [co to jest dostęp warunkowy w usłudze Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego w danym środowisku, zobacz [zalecane praktyki dla dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png

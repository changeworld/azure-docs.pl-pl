---
title: Warunki w zasadach dostępu warunkowego — usługa Azure Active Directory
description: Jakie są warunki w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295332"
---
# <a name="conditional-access-conditions"></a>Dostęp warunkowy: Warunki

W ramach zasad dostępu warunkowego administrator może używać sygnałów z warunków, takich jak ryzyko, platforma urządzenia lub lokalizacja, aby usprawnić swoje decyzje dotyczące zasad. 

![Definiowanie zasad dostępu warunkowego i określanie warunków](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Wiele warunków można łączyć w celu utworzenia szczegółowych i określonych zasad dostępu warunkowego.

Na przykład podczas uzyskiwania dostępu do poufnych aplikacji administrator może uwzględniać informacje o ryzyku logowania z ochrony tożsamości i lokalizacji w decyzji o dostępie oprócz innych formantów, takich jak uwierzytelnianie wieloskładnikowe.

## <a name="sign-in-risk"></a>Ryzyko logowania

W przypadku klientów z [dostępem](../identity-protection/overview-identity-protection.md)do ochrony tożsamości ryzyko logowania można ocenić jako część zasad dostępu warunkowego. Ryzyko logowania reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie jest autoryzowane przez właściciela tożsamości. Więcej informacji na temat ryzyka logowania można znaleźć w artykułach: [Co to jest ryzyko](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) i [jak: Konfigurowanie i włączanie zasad ryzyka](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Platformy urządzeń

Platforma urządzenia charakteryzuje się systemem operacyjnym, który działa na urządzeniu. Usługa Azure AD identyfikuje platformę przy użyciu informacji dostarczonych przez urządzenie, takich jak ciągi agenta użytkownika. Ponieważ ciągi agenta użytkownika mogą być modyfikowane, te informacje są niezweryfikowane. Platforma urządzenia powinna być używana w porozumieniu z zasadami zgodności urządzeń usługi Microsoft Intune lub jako część instrukcji bloku. Domyślnie ma być stosowany do wszystkich platform urządzeń.

Usługa Azure AD Dostęp warunkowy obsługuje następujące platformy urządzeń:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Jeśli zablokujesz starsze uwierzytelnianie przy użyciu warunku **Inne klientów,** możesz również ustawić warunek platformy urządzenia.

## <a name="locations"></a>Lokalizacje

Podczas konfigurowania lokalizacji jako warunku organizacje mogą zdecydować się na uwzględnienie lub wykluczenie lokalizacji. Te nazwane lokalizacje mogą obejmować publiczne informacje o sieci IPv4, kraj lub region, a nawet nieznane obszary, które nie są mapowane do określonych krajów lub regionów. Tylko zakresy adresów IP mogą być oznaczone jako zaufana lokalizacja.

W przypadku **uwzględnienia dowolnej lokalizacji**ta opcja obejmuje dowolny adres IP w Internecie, a nie tylko skonfigurowane nazwane lokalizacje. Wybierając **dowolną lokalizację,** administratorzy mogą wybrać opcję **wykluczenia wszystkich zaufanych** lub **wybranych lokalizacji.**

Na przykład niektóre organizacje mogą nie wymagać uwierzytelniania wieloskładnikowego, gdy ich użytkownicy są połączeni z siecią w zaufanej lokalizacji, takiej jak ich fizyczna siedziba główna. Administratorzy mogą utworzyć zasadę, która zawiera dowolną lokalizację, ale wyklucza wybrane lokalizacje dla swoich sieci centrali.

Więcej informacji o lokalizacjach można znaleźć w artykule: [Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy](location-condition.md).

## <a name="client-apps-preview"></a>Aplikacje klienckie (wersja zapoznawcza)

Zasady dostępu warunkowego domyślnie mają zastosowanie do aplikacji i aplikacji opartych na przeglądarce, które wykorzystują nowoczesne protokoły uwierzytelniania. Oprócz tych aplikacji administratorzy mogą dołączyć klientów programu Exchange ActiveSync i innych klientów korzystających ze starszych protokołów.

- Przeglądarka
   - Należą do nich aplikacje internetowe, które używają protokołów, takich jak SAML, Federacja WS, OpenID Connect lub usługi zarejestrowane jako klient poufny OAuth.
- Aplikacje mobilne i klienci komputerowi
   - Nowoczesne klienty uwierzytelniania
      - Ta opcja obejmuje aplikacje, takie jak aplikacje stacjonarne pakietu Office i aplikacje na telefon.
   - Klienci programu Exchange ActiveSync
      - Domyślnie obejmuje to wszystkie użycie protokołu Exchange ActiveSync (EAS). Wybranie **opcji Zastosuj zasady tylko do obsługiwanych platform** ograniczy się do obsługiwanych platform, takich jak iOS, Android i Windows.
      - Gdy zasady zablokują użycie programu Exchange ActiveSync, użytkownik, którego dotyczy problem, otrzyma jedną wiadomość e-mail z kwarantanną. Ten e-mail zawiera informacje o tym, dlaczego są one zablokowane i zawierają instrukcje korygowania, jeśli to możliwe.
   - Inni klienci
      - Ta opcja obejmuje klientów korzystających z podstawowych/starszych protokołów uwierzytelniania, które nie obsługują nowoczesnego uwierzytelniania.
         - Uwierzytelniony protokół SMTP — używany przez klientów POP i IMAP do wysyłania wiadomości e-mail.
         - Automatyczne wykrywanie — używane przez klientów programu Outlook i EAS do znajdowania skrzynek pocztowych i łączenia się z nią w usłudze Exchange Online.
         - Program Exchange Online PowerShell — służy do łączenia się z usługą Exchange Online za pomocą zdalnego programu PowerShell. Jeśli zablokujesz uwierzytelnianie podstawowe dla programu Exchange Online PowerShell, musisz użyć modułu programu Exchange Online PowerShell, aby się połączyć. Aby uzyskać instrukcje, zobacz [Łączenie się z programem Exchange Online PowerShell przy użyciu uwierzytelniania wieloskładnikowego](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS) — interfejs programowania używany przez program Outlook, Outlook dla komputerów Mac i aplikacje innych firm.
         - IMAP4 — używany przez klientów poczty e-mail IMAP.
         - MAPI za pośrednictwem protokołu HTTP (MAPI/HTTP) — używane przez program Outlook 2010 i nowsze.
         - Książka adresowa trybu offline (OAB) — kopia kolekcji list adresów, które są pobierane i używane przez program Outlook.
         - Outlook Anywhere (RPC over HTTP) — używany przez program Outlook 2016 i starsze.
         - Usługa programu Outlook — używana przez aplikację Poczta i Kalendarz dla systemu Windows 10.
         - POP3 — używany przez klientów poczty pop.
         - Raportowanie usług sieci Web — służy do pobierania danych raportu w usłudze Exchange Online.

Te warunki są powszechnie używane, gdy wymaga się urządzenia zarządzanego, blokuje starsze uwierzytelnianie i blokuje aplikacje internetowe, ale zezwala na aplikacje mobilne lub klasyczne.

### <a name="supported-browsers"></a>Obsługiwane przeglądarki

To ustawienie działa ze wszystkimi przeglądarkami. Jednak aby spełnić zasady urządzenia, takie jak wymagania dotyczące urządzeń zgodnych, obsługiwane są następujące systemy operacyjne i przeglądarki:

| System operacyjny | Przeglądarki |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Przeglądarka zarządzana usługi Intune, Safari |
| Android | Microsoft Edge, Przeglądarka zarządzana usługi Intune, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Dlaczego w przeglądarce jest wyświetlany monit o certyfikat

W systemach Windows 7, iOS, Android i macOS Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest aprowidyfikowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD.  Gdy użytkownik po raz pierwszy loguje się za pośrednictwem przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik musi wybrać ten certyfikat przed użyciem przeglądarki.

#### <a name="chrome-support"></a>Obsługa Chrome

Aby uzyskać pomoc techniczną chrome w **systemie Windows 10 Creators Update (wersja 1703)** lub nowsza, zainstaluj [rozszerzenie Konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). To rozszerzenie jest wymagane, gdy zasady dostępu warunkowego wymaga szczegółów określonych urządzenia.

Aby automatycznie wdrożyć to rozszerzenie w przeglądarkach Chrome, utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE\Oprogramowanie\Zasady\Google\Chrome\ExtensionInstallForcelist |
| Nazwa | 1 |
| Typ | REG_SZ (ciąg) |
| Dane | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

W przypadku obsługi Chrome w **systemach Windows 8.1 i 7**utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE\SOFTWARE\Zasady\Google\Chrome\AutoSelectCertificateForUrls |
| Nazwa | 1 |
| Typ | REG_SZ (ciąg) |
| Dane | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Te przeglądarki obsługują uwierzytelnianie urządzeń, umożliwiając identyfikację urządzenia i sprawdzanie poprawności zgodnie z zasadami. Sprawdzanie urządzenia kończy się niepowodzeniem, jeśli przeglądarka jest uruchomiona w trybie prywatnym.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Obsługiwane aplikacje mobilne i klienci komputerowi

Organizacje mogą wybierać **aplikacje mobilne i klientów klasycznych** jako aplikację kliencką.

To ustawienie ma wpływ na próby dostępu podejmowane przez następujące aplikacje mobilne i klientów klasycznych:

| Aplikacje klienckie | Usługa docelowa | Platforma |
| --- | --- | --- |
| Aplikacja Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS i Android |
| Aplikacja Poczta/Kalendarz/Kontakty, Outlook 2016, Outlook 2013 (z nowoczesnym uwierzytelnianiem)| Office 365 Exchange Online | Windows 10 |
| Usługi MFA i zasady dotyczące lokalizacji aplikacji. Zasady oparte na urządzeniach nie są obsługiwane.| Dowolna usługa aplikacji Moje aplikacje | Android i iOS |
| Usługi Microsoft Teams — ta kontrola wszystkich usług obsługujących usługi Microsoft Teams i wszystkie jego aplikacje klienckie — Windows Desktop, iOS, Android, WP i klienta sieci Web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android i macOS |
| Aplikacje pakietu Office 2016, Office 2013 (z nowoczesnym uwierzytelnianiem), [klient synchronizacji usługi OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikacje pakietu Office 2016, aplikacje pakietu Uniwersalnego pakietu Office, pakiet Office 2013 (z nowoczesnym uwierzytelnianiem), [klient synchronizacji usługi OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (tylko programy Word, Excel, PowerPoint, OneNote). | Office 365 SharePoint Online | macOS |
| Pakiet Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Aplikacje pakietu Office dla urządzeń przenośnych | Office 365 SharePoint Online | Android, iOS |
| Aplikacja Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Program Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Pakiet Office dla systemu macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (z nowoczesnym uwierzytelnianiem), Skype dla firm (z nowoczesnym uwierzytelnianiem) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Aplikacja mobilna Outlook | Office 365 Exchange Online | Android, iOS |
| Aplikacja Power BI | Usługa Power BI | Windows 10, Windows 8.1, Windows 7, Android i iOS |
| Skype dla firm | Office 365 Exchange Online| Android, iOS |
| Aplikacja Usługi teamu programu Visual Studio | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS i Android |

### <a name="exchange-activesync-clients"></a>Klienci programu Exchange ActiveSync

- Organizacje mogą wybierać tylko klientów programu Exchange ActiveSync podczas przypisywania zasad użytkownikom lub grupom. Wybranie **opcji Wszyscy użytkownicy**, **Wszyscy użytkownicy-goście i użytkownicy zewnętrzni**lub **Role katalogu** spowoduje zablokowanie wszystkich użytkowników.
- Podczas tworzenia zasad przypisanych do klientów programu Exchange ActiveSync **usługa Office 365 Exchange Online** powinna być jedyną aplikacją w chmurze przypisaną do zasad. 
- Organizacje mogą zawęzić zakres tej zasady do określonych platform przy użyciu warunku **Platformy urządzenia.**

Jeśli kontrola dostępu przypisana do zasad używa **wymagaj zatwierdzonej aplikacji klienckiej,** użytkownik jest kierowany do zainstalowania i korzystania z klienta mobilnego programu Outlook. W przypadku, gdy wymagane jest **uwierzytelnianie wieloskładnikowe,** użytkownicy, których dotyczy problem, są blokowani, ponieważ uwierzytelnianie podstawowe nie obsługuje uwierzytelniania wieloskładnikowego.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Blokowanie uwierzytelniania starszej wersji za pomocą dostępu warunkowego](block-legacy-authentication.md)
- [Wymaganie zatwierdzonych aplikacji klienckich z dostępem warunkowym](app-based-conditional-access.md)

### <a name="other-clients"></a>Inni klienci

Wybierając **pozycję Inni klienci,** można określić warunek, który ma wpływ na aplikacje korzystające z uwierzytelniania podstawowego z protokołami pocztowymi, takimi jak IMAP, MAPI, POP, SMTP i starsze aplikacje pakietu Office, które nie używają nowoczesnego uwierzytelniania.

## <a name="device-state-preview"></a>Stan urządzenia (wersja zapoznawcza)

Warunek stanu urządzenia może służyć do wykluczania urządzeń, które są hybrydowe usługi Azure AD przyłączone i/lub urządzeń oznaczonych jako zgodne z zasadami zgodności usługi Microsoft Intune z zasad dostępu warunkowego organizacji.

Na przykład *wszyscy użytkownicy* uzyskujący dostęp do aplikacji w chmurze *usługi Microsoft Azure Management,* w tym wszystkie stany **urządzenia** z wyłączeniem **sprzężenia usługi Azure AD z hybrydową usługą device** i urządzenie oznaczone **jako zgodne** oraz *formanty dostępu*, **Blokuj**. 
   - W tym przykładzie można utworzyć zasadę, która umożliwia dostęp do usługi Microsoft Azure Management tylko z urządzeń, które są hybrydowe usługi Azure AD przyłączone i/lub urządzeń oznaczonych jako zgodne.

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: Dotacja](concept-conditional-access-grant.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

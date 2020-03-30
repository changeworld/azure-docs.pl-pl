---
title: Blokowanie starszych protokołów uwierzytelniania w usłudze Azure AD
description: Dowiedz się, jak i dlaczego organizacje powinny blokować starsze protokoły uwierzytelniania
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63271567e70955f6dfb0b10a5c882b6dce9545ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932491"
---
# <a name="blocking-legacy-authentication"></a>Blokowanie uwierzytelniania starszego
 
Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure Active Directory (Azure AD) obsługuje szeroką gamę protokołów uwierzytelniania, w tym uwierzytelnianie starsze. Uwierzytelnianie starsze to termin, który odnosi się do żądania uwierzytelnienia złożonego przez:

- Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klient pakietu Office 2010)
- Każdy klient korzystający ze starszych protokołów poczty, takich jak IMAP/SMTP/POP3

Obecnie większość wszystkich prób logowania kompromitujących pochodzi ze starszego uwierzytelniania. Uwierzytelnianie starsze nie obsługuje uwierzytelniania wieloskładnikowego (MFA). Nawet jeśli masz włączone zasady usługi MFA w katalogu, zły aktor może uwierzytelniać się przy użyciu starszego protokołu i pominąć uwierzytelnianie usługi MFA. Najlepszym sposobem ochrony konta przed złośliwymi żądaniami uwierzytelniania złożonymi przez starsze protokoły jest całkowite zablokowanie tych prób.

## <a name="identify-legacy-authentication-use"></a>Identyfikowanie użycia uwierzytelniania starszego

Aby zablokować starsze uwierzytelnianie w katalogu, należy najpierw zrozumieć, czy użytkownicy mają aplikacje korzystające ze starszego uwierzytelniania i jak wpływa na ogólny katalog. Dzienniki logowania usługi Azure AD mogą służyć do zrozumienia, jeśli używasz uwierzytelniania starszego.

1. Przejdź do witryny Azure portal > azure active directory > logowania.
1. Dodaj kolumnę Aplikacja kliencka, jeśli nie jest wyświetlana, klikając kolumna > aplikacji klienta.
1. Filtruj według aplikacji klienta, > sprawdź wszystkie prezentowane opcje innych klientów i kliknij przycisk Zastosuj.
1. Filtruj według stanu > sukces i kliknij przycisk Zastosuj. 
1. W razie potrzeby rozszerz zakres dat za pomocą filtru Data.

Filtrowanie będzie wyświetlać tylko pomyślne próby logowania, które zostały wykonane przez wybrane starsze protokoły uwierzytelniania. Kliknięcie każdej indywidualnej próby logowania spowoduje wyświetlenie dodatkowych informacji. Kolumna Aplikacja kliencka lub pole Aplikacja kliencka na karcie Informacje podstawowe po wybraniu pojedynczego wiersza danych wskaże, który starszy protokół uwierzytelniania został użyty. Te dzienniki wskażą, którzy użytkownicy są nadal zależni od starszego uwierzytelniania i które aplikacje używają starszych protokołów do żądania uwierzytelniania. W przypadku użytkowników, którzy nie są wyświetlane w tych dziennikach i są potwierdzone, że nie używają uwierzytelniania starszego, należy zaimplementować zasady dostępu warunkowego lub włączyć zasady planu bazowego: blokuj starsze uwierzytelnianie tylko dla tych użytkowników.

## <a name="moving-away-from-legacy-authentication"></a>Odejście od uwierzytelniania starszego 

Gdy masz lepsze pojęcie o tym, kto używa starszego uwierzytelniania w katalogu i które aplikacje od niego zależą, następnym krokiem jest uaktualnienie użytkowników do korzystania z nowoczesnego uwierzytelniania. Nowoczesne uwierzytelnianie to metoda zarządzania tożsamościami, która oferuje bezpieczniejsze uwierzytelnianie i autoryzację użytkowników. Jeśli masz zasady usługi MFA w miejscu w katalogu, nowoczesne uwierzytelnianie zapewnia, że użytkownik jest monitowany o uwierzytelnianie wieloskładnikowe, gdy jest to wymagane. Jest to bezpieczniejsza alternatywa dla starszych protokołów uwierzytelniania.

W tej sekcji przedstawiono omówienie krok po kroku dotyczące aktualizowania środowiska do nowoczesnego uwierzytelniania. Zapoznaj się z poniższymi krokami przed włączeniem starszych zasad blokowania uwierzytelniania w organizacji.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Krok 1: Włącz nowoczesne uwierzytelnianie w katalogu

Pierwszym krokiem w włączaniu nowoczesnego uwierzytelniania jest upewnienie się, że katalog obsługuje nowoczesne uwierzytelnianie. Nowoczesne uwierzytelnianie jest domyślnie włączone dla katalogów utworzonych 1 sierpnia 2017 r. lub później. Jeśli katalog został utworzony przed tą datą, musisz ręcznie włączyć nowoczesne uwierzytelnianie dla katalogu, wykonując następujące czynności:

1. Sprawdź, czy katalog obsługuje już nowoczesne `Get-CsOAuthConfiguration` uwierzytelnianie, uruchamiając [moduł programu Skype dla firm Online PowerShell](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Jeśli polecenie zwraca `OAuthServers` pustą właściwość, nowoczesne uwierzytelnianie jest wyłączone. Zaktualizuj to `Set-CsOAuthConfiguration`ustawienie, aby włączyć nowoczesne uwierzytelnianie przy użyciu programu . Jeśli `OAuthServers` twoja nieruchomość zawiera wpis, jesteś dobry, aby przejść.

Pamiętaj, aby wykonać ten krok przed przejściem do przodu. Bardzo ważne jest, aby najpierw zmienić konfiguracje katalogów, ponieważ określają one, który protokół będzie używany przez wszystkich klientów pakietu Office. Nawet jeśli używasz klientów pakietu Office, które obsługują nowoczesne uwierzytelnianie, będą one domyślnie przy użyciu starszych protokołów, jeśli nowoczesne uwierzytelnianie jest wyłączone w katalogu.

### <a name="step-2-office-applications"></a>Krok 2: Aplikacje pakietu Office

Po włączeniu nowoczesnego uwierzytelniania w katalogu można rozpocząć aktualizowanie aplikacji, włączając nowoczesne uwierzytelnianie dla klientów pakietu Office. Klienci pakietu Office 2016 lub nowszego domyślnie obsługują nowoczesne uwierzytelnianie. Nie są wymagane żadne dodatkowe kroki.

Jeśli korzystasz z klientów pakietu Office 2013 dla systemu Windows lub starszych, zalecamy uaktualnienie do pakietu Office 2016 lub nowszego. Nawet po wykonaniu poprzedniego kroku włączania nowoczesnego uwierzytelniania w katalogu starsze aplikacje pakietu Office będą nadal używać starszych protokołów uwierzytelniania. Jeśli korzystasz z klientów pakietu Office 2013 i nie możesz natychmiast uaktualnić do pakietu Office 2016 lub nowszego, wykonaj czynności opisane w poniższym artykule, aby [włączyć nowoczesne uwierzytelnianie dla pakietu Office 2013 na urządzeniach z systemem Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Aby chronić swoje konto podczas korzystania ze starszego uwierzytelniania, zalecamy używanie silnych haseł w katalogu. Zapoznaj się z [ochroną hasłem](../authentication/concept-password-ban-bad.md) usługi Azure AD, aby zablokować słabe hasła w katalogu.

Pakiet Office 2010 nie obsługuje nowoczesnego uwierzytelniania. Należy uaktualnić wszystkich użytkowników korzystających z pakietu Office 2010 do nowszej wersji pakietu Office. Zalecamy uaktualnienie do pakietu Office 2016 lub nowszego, ponieważ domyślnie blokuje on starsze uwierzytelnianie.

Jeśli używasz systemu MacOS, zalecamy uaktualnienie do pakietu Office 2016 lub nowszego. Jeśli korzystasz z natywnego klienta poczty, musisz mieć system MacOS w wersji 10.14 lub nowszej na wszystkich urządzeniach.

### <a name="step-3-exchange-and-sharepoint"></a>Krok 3: Program Exchange i sharepoint

Aby klienci programu Outlook z systemem Windows mogli korzystać z nowoczesnego uwierzytelniania, usługa Exchange Online musi być również nowoczesna. Jeśli nowoczesne uwierzytelnianie jest wyłączone w usłudze Exchange Online, klienci programu Outlook z systemem Windows obsługującym nowoczesne uwierzytelnianie (Outlook 2013 lub nowsze) będą używać uwierzytelniania podstawowego do łączenia się ze skrzynkami pocztowymi usługi Exchange Online.

Program SharePoint Online jest włączony dla nowoczesnego uwierzytelniania domyślnego. W przypadku katalogów utworzonych po 1 sierpnia 2017 r. nowoczesne uwierzytelnianie jest domyślnie włączone w usłudze Exchange Online. Jeśli jednak wcześniej wyłączono nowoczesne uwierzytelnianie lub katalog utworzony przed tą datą, wykonaj kroki opisane w poniższym artykule, aby [włączyć nowoczesne uwierzytelnianie w usłudze Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Krok 4: Skype dla firm

Aby zapobiec starszym żądaniom uwierzytelniania składanych przez program Skype dla firm, konieczne jest włączenie nowoczesnego uwierzytelniania dla usługi Skype dla firm Online. W przypadku katalogów utworzonych po 1 sierpnia 2017 r. nowoczesne uwierzytelnianie dla programu Skype dla firm jest domyślnie włączone.

Sugerujemy przejście do usługi Microsoft Teams, która domyślnie obsługuje nowoczesne uwierzytelnianie. Jeśli jednak obecnie nie można przeprowadzić migracji, musisz włączyć nowoczesne uwierzytelnianie dla usługi Skype dla firm Online, aby klienci programu Skype dla firm zaczęli korzystać z nowoczesnego uwierzytelniania. Wykonaj kroki opisane w tym artykule [Topologie programu Skype dla firm obsługiwane za pomocą nowoczesnego uwierzytelniania](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), aby włączyć nowoczesne uwierzytelnianie dla programu Skype dla firm.

Oprócz włączania nowoczesnego uwierzytelniania w usłudze Skype dla firm Online zalecamy włączenie nowoczesnego uwierzytelniania dla usługi Exchange Online podczas włączania nowoczesnego uwierzytelniania dla programu Skype dla firm. Ten proces pomoże zsynchronizować stan nowoczesnego uwierzytelniania w usłudze Exchange Online i w usłudze Skype dla firm online i uniemożliwi wiele monitów logowania dla klientów programu Skype dla firm.

### <a name="step-5-using-mobile-devices"></a>Krok 5: Korzystanie z urządzeń mobilnych

Aplikacje na urządzeniu przenośnym muszą również blokować starsze uwierzytelnianie. Zalecamy korzystanie z programu Outlook dla urządzeń przenośnych. Program Outlook dla urządzeń przenośnych domyślnie obsługuje nowoczesne uwierzytelnianie i spełnia inne zasady ochrony bazowej usługi MFA.

Aby korzystać z natywnego klienta poczty systemu iOS, musisz mieć uruchomiony system iOS w wersji 11.0 lub nowszej, aby upewnić się, że klient poczty został zaktualizowany w celu zablokowania uwierzytelniania starszego.

### <a name="step-6-on-premises-clients"></a>Krok 6: Klienci lokalni

Jeśli jesteś klientem hybrydowym korzystającym z lokalnego programu Exchange Server i programu Skype dla firm lokalnie, obie usługi będą musiały zostać zaktualizowane, aby umożliwić nowoczesne uwierzytelnianie. Podczas korzystania z nowoczesnego uwierzytelniania w środowisku hybrydowym, nadal uwierzytelniasz użytkowników lokalnie. Zmienia się historia autoryzowania ich dostępu do zasobów (plików lub wiadomości e-mail).

Zanim zaczniesz włączać nowoczesne uwierzytelnianie lokalnie, upewnij się, że spełniasz wymagania wstępne. Teraz możesz przystąpić do włączania nowoczesnego uwierzytelniania lokalnie.

Kroki włączania nowoczesnego uwierzytelniania można znaleźć w następujących artykułach:

* [Jak skonfigurować program Exchange Server lokalnie do używania nowoczesnego uwierzytelniania hybrydowego](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Jak korzystać z nowoczesnego uwierzytelniania (ADAL) w programie Skype dla firm](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować program Exchange Server lokalnie do używania nowoczesnego uwierzytelniania hybrydowego](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Jak korzystać z nowoczesnego uwierzytelniania (ADAL) w programie Skype dla firm](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Blokowanie starszego uwierzytelniania](../conditional-access/block-legacy-authentication.md)

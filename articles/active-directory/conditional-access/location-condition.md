---
title: Co to jest warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego? | Microsoft Docs
description: Dowiedz się, jak używać warunek lokalizacji do kontrolowania dostępu do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 886118614427bea61f745e1ded28824b60225919
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112294"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Co to jest warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego? 

Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Warunek lokalizacji zasad dostępu warunkowego można powiązać ustawienia kontroli dostępu do lokalizacji sieciowych użytkowników.

Ten artykuł zawiera informacje, które należy skonfigurować warunek lokalizacji.

## <a name="locations"></a>Lokalizacje

Usługa Azure AD umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca w publicznym Internecie. Dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika można kontrolować z warunkiem lokalizacji. Typowe przypadki użycia dla warunku lokalizacji są następujące:

- Wymaganie uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi, gdy znajdują się poza siecią firmową.
- Blokuje dostęp dla użytkowników uzyskujących dostęp do usługi z określonych krajów lub regionów.

Lokalizacja jest etykietę dla lokalizacji sieciowej albo reprezentuje lokalizację o nazwie lub uwierzytelnianie wieloskładnikowe zaufanych adresów IP.

## <a name="named-locations"></a>Nazwane lokalizacje

Nazwane lokalizacje umożliwia tworzenie logiczne grupowanie zakresów adresów IP lub innych krajów i regionów.

Możesz uzyskać dostęp nazwane lokalizacje w **Zarządzaj** sekcji na stronie dostępu warunkowego.

![Nazwane lokalizacje dostępu warunkowego](./media/location-condition/02.png)

Lokalizacja o nazwie zawiera następujące składniki:

![Utwórz nową nazwę lokalizacji](./media/location-condition/42.png)

- **Nazwa** — Nazwa wyświetlana nazwanych lokalizacji.
- **Zakresy adresów IP** — jeden lub więcej zakresów adresów IPv4 w formacie CIDR. Określenie zakresu adresów IPv6 nie jest obsługiwana.

   > [!NOTE]
   > Rangess adres IPv6 obecnie nie można uwzględnić w nazwanych lokalizacji. Zakresy adresów IPv6 w tym measn nie można wykluczyć z zasad dostępu warunkowego.

- **Oznacz jako zaufaną lokalizację** -flagę można ustawić dla nazwanych lokalizacji do wskazania zaufanej lokalizacji. Zazwyczaj zaufanych lokalizacji są obszarów sieci, które są kontrolowane przez dział IT. Oprócz dostępu warunkowego, Zaufane lokalizacje nazwane są również używane przez raporty dotyczące zabezpieczeń usługi Azure Identity Protection i usługą Azure AD do zmniejszenia [wyników fałszywie dodatnich](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Kraje/regiony** — ta opcja umożliwia wybranie co najmniej jeden kraj lub region, aby zdefiniować nazwanych lokalizacji.
- **Uwzględnij nieznane obszary** — adresy IP niektóre nie są zamapowane na konkretnym kraju lub regionu. Ta opcja pozwala wybrać, czy te adresy IP powinien być uwzględniony w lokalizacji o nazwie. Użyj tego ustawienia podczas stosowania zasad za pomocą nazwanych lokalizacji dla nieznanych lokalizacjach.

Rozmiar obiektu pokrewnego ograniczają liczbę nazwane lokalizacje, które można skonfigurować w usłudze Azure AD. Organizacje można skonfigurować maksymalnie 90 nazwane lokalizacje, każdy skonfigurowany z maksymalnie 1200 zakresów adresów IP.

Zasady dostępu warunkowego dotyczy ruchu IPv4 i IPv6. Obecnie nazwane lokalizacje są dozwolone zakresy adresów IPv6, należy skonfigurować. To ograniczenie powoduje, że następujące sytuacje:

- Zasady dostępu warunkowego nie będzie można wykonywać na określone zakresy adresów IPv6
- Zasady dostępu warunkowego nie można wykluczyć określone zakresy adresów IPV6

Jeśli zasady są skonfigurowane do zastosowania "Dowolne miejsce", będą dotyczyć ruchu IPv4 i IPv6. Nazwane lokalizacje skonfigurowane dla określonej kraje i regiony obsługują tylko adresy IPv4. Ruch IPv6 jest tylko włączone, gdy wybrano opcję "Uwzględnij nieznane obszary".

## <a name="trusted-ips"></a>Zaufane adresy IP

Możesz również skonfigurować zakresy adresów IP, reprezentujący lokalny intranet w organizacji w [ustawienia usługi Multi-Factor authentication](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ta funkcja umożliwia konfigurowanie maksymalnie 50 zakresów adresów IP. Zakresy adresów IP są w formacie CIDR. Aby uzyskać więcej informacji, zobacz [zaufane adresy IP](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Jeśli masz zaufane adresy IP skonfigurowane, są one wyświetlane jako **zaufane adresy IP usługi MFA** na liście lokalizacje warunek lokalizacji.

### <a name="skipping-multi-factor-authentication"></a>Pomijanie usługi Multi-Factor authentication

Na stronie Ustawienia usługi uwierzytelnianie wieloskładnikowe, można zidentyfikować użytkowników firmowej sieci intranet, wybierając **Pomiń uwierzytelnianie Multi-Factor Authentication w przypadku żądań od użytkowników federacyjnych w moim intranecie**. To ustawienie wskazuje, że wewnątrz firmowych sieciowe oświadczenia, który jest wydane przez usługi AD FS, powinien być zaufany i używany do identyfikowania użytkownika jako znajdujące się w sieci firmowej. Aby uzyskać więcej informacji, zobacz [włączyć funkcję zaufane adresy IP przy użyciu dostępu warunkowego](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaznaczeniu tej opcji, w tym nazwanych lokalizacji **zaufane adresy IP usługi MFA** dotyczą jakiekolwiek zasady w przypadku wybrania tej opcji.

Dla aplikacji mobilnych i klasycznych, które długo mieszkałem okresy istnienia sesji, dostęp warunkowy jest okresowo ponownie oceniane. Wartość domyślna to raz godzinę. Gdy wewnątrz tylko wystawiane jest oświadczenie sieci firmowej w czasie początkowe uwierzytelnianie, usługa Azure AD nie może mieć listy zaufanych zakresów adresów IP. W tym przypadku jest trudne do ustalenia, czy użytkownik jest nadal w sieci firmowej:

1. Sprawdź, czy adres IP użytkownika w jednym z zaufanych zakresów adresów IP.
2. Sprawdź, czy pierwsze trzy oktety adresu IP użytkownika są zgodne z pierwsze trzy oktety adresu IP początkowego uwierzytelniania. Adres IP jest porównywany ze wstępnego uwierzytelniania wewnątrz firmowych sieci oświadczenia pierwotnie został wystawiony i lokalizację użytkownika została zweryfikowana.

Jeśli oba kroki zakończy się niepowodzeniem, użytkownik uznaje się już znajdować się w zaufanych adresów IP.

## <a name="location-condition-configuration"></a>Konfiguracja warunków lokalizacji

Po skonfigurowaniu warunek lokalizacji, masz możliwość rozróżnienia:

- Dowolne miejsce
- Wszystkie zaufane lokalizacje
- Wybrane lokalizacje

![Konfiguracja warunków lokalizacji](./media/location-condition/01.png)

### <a name="any-location"></a>Dowolne miejsce

Domyślnie wybranie **dowolnej lokalizacji** powoduje, że zasady mają być stosowane do wszystkich adresów IP, co oznacza każdy adres w Internecie. To ustawienie nie jest ograniczona do adresów IP, które zostały skonfigurowane jako lokalizację o nazwie. Po wybraniu **dowolnej lokalizacji**, nadal można wykluczyć określone lokalizacje z zasad. Na przykład można zastosować zasady do wszystkich lokalizacji, z wyjątkiem zaufane lokalizacje, aby ustawić zakres do wszystkich lokalizacji, z wyjątkiem sieci firmowej.

### <a name="all-trusted-locations"></a>Wszystkie zaufane lokalizacje

Ta opcja ma zastosowanie do:

- Wszystkie lokalizacje, które zostały oznaczone jako zaufaną lokalizację
- **Zaufane adresy IP usługi MFA** (jeśli jest skonfigurowane)

### <a name="selected-locations"></a>Wybrane lokalizacje

Po wybraniu tej opcji można wybrać jedną lub więcej nazwanych lokalizacji. Zasady do zastosowania tego ustawienia użytkownik musi nawiązać połączenie z dowolną z wybranej lokalizacji. Po kliknięciu **wybierz** otwiera kontrolka wyboru o nazwie sieci, która pokazuje listę o nazwie sieci. Na liście wyświetlane, jeśli lokalizacja sieciowa została oznaczona jako zaufane. Nazwanych lokalizacji o nazwie **zaufane adresy IP usługi MFA** jest używana do włączenia ustawienia protokołu IP, które można skonfigurować na stronie Ustawienia usługi Multi-Factor authentication.

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="when-is-a-location-evaluated"></a>Po ocenieniu lokalizacji

Zasady dostępu warunkowego są oceniane po:

- Użytkownik początkowo loguje się do aplikacji sieci web app, telefon komórkowy lub pulpitu.
- Aplikacji mobilnej lub klasycznej, które używają nowoczesnego uwierzytelniania używa tokenu odświeżania można uzyskać nowy token dostępu. Domyślnie ten test jest raz godzinę.

Tego wyboru oznacza, że dla urządzeń przenośnych i aplikacji klasycznych korzystających z nowoczesnego uwierzytelniania, zmiany w lokalizacji można wykryć w ciągu godziny zmiany lokalizacji sieciowej. Dla aplikacji mobilnych i klasycznych, które nie korzystają z nowoczesnego uwierzytelniania zasady są stosowane na każde żądanie tokenu. Częstotliwość żądania może się różnić w zależności od aplikacji. Podobnie dla aplikacji sieci web, zasada jest stosowana podczas początkowego logowania i jest dobra dla okresu istnienia sesji w aplikacji sieci web. Z powodu różnic w okresy istnienia sesji w aplikacjach różnią się także czas między oceny zasad. Każdorazowo, aplikacja żąda nowego tokenu logowania, zasady są stosowane.

Domyślnie usługa Azure AD wystawia token w systemie godzinowym. Po przeniesieniu poza siecią firmową, w ciągu godziny zasady są wymuszane w przypadku aplikacji korzystających z nowoczesnego uwierzytelniania.

### <a name="user-ip-address"></a>Adres IP użytkownika

Adres IP, który jest używany podczas oceny zasad jest publiczny adres IP użytkownika. W przypadku urządzeń w sieci prywatnej ten adres IP nie jest adres IP klienta urządzenia użytkownika w sieci intranet, jest to adres używanej przez sieć, aby nawiązać połączenie z publicznej sieci internet.

> [!WARNING]
> Jeśli urządzenie ma adres IPv6, konfigurowanie warunek lokalizacji nie jest obsługiwana.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Zbiorcze przekazywanie i pobieranie nazwane lokalizacje

Podczas tworzenia lub aktualizacji o nazwie lokalizacje, aktualizacji zbiorczych można przekazać lub pobrać plik CSV z zakresami adresów IP. Przekazywanie zastępuje zakresy adresów IP na liście z tymi z pliku. Każdy wiersz w pliku zawiera jeden zakres adresów IP w formacie CIDR.

### <a name="cloud-proxies-and-vpns"></a>Serwery proxy w chmurze i sieci VPN

Gdy używasz serwera proxy hostowane w chmurze lub rozwiązanie sieci VPN, adres IP usługi Azure AD używa podczas oceny zasad jest adres IP serwera proxy. Nagłówek X-Forwarded-For (XFF), która zawiera publiczny adres IP użytkownika nie jest używana, ponieważ nie ma możliwości weryfikacji, który pochodzi z zaufanego źródła, więc przedstawiałoby metodę faking adresu IP.

Gdy serwer proxy w chmurze jest w miejscu, zasady, które służy do Wymagaj urządzenia połączonego z domeną mogą być używane lub wewnątrz sieci firmowej oświadczeń z usług AD FS.

### <a name="api-support-and-powershell"></a>Obsługa interfejsu API i programu PowerShell

Interfejs API i programu PowerShell nie jest jeszcze obsługiwana dla nazwanych lokalizacji lub dla zasad dostępu warunkowego.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).

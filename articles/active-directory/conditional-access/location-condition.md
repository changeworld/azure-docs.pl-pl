---
title: Warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy
description: Dowiedz się, jak używać warunku lokalizacji do kontrolowania dostępu do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263233"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy? 

Dzięki [dostępowi warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)można kontrolować sposób uzyskiwania dostępu do aplikacji w chmurze przez autoryzowanych użytkowników. Warunek lokalizacji zasad dostępu warunkowego umożliwia powiązanie ustawień kontroli dostępu z lokalizacjami sieciowymi użytkowników.

Ten artykuł zawiera informacje potrzebne do skonfigurowania warunku lokalizacji.

## <a name="locations"></a>Lokalizacje

Usługa Azure AD umożliwia logowanie jednokrotne na urządzeniach, aplikacjach i usługach z dowolnego miejsca w publicznym Internecie. W warunkach lokalizacji można kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika. Typowe przypadki użycia dla warunku lokalizacji to:

- Wymagania uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi, gdy są poza siecią firmową.
- Blokowanie dostępu dla użytkowników uzyskujących dostęp do usługi z określonych krajów lub regionów.

Lokalizacja to etykieta lokalizacji sieciowej reprezentująca nazwaną lokalizację lub zaufane usługi IP uwierzytelniania wieloskładnikowego.

## <a name="named-locations"></a>Nazwane lokalizacje

W nazwanych lokalizacjach można tworzyć logiczne grupy zakresów adresów IP lub krajów i regionów.

Dostęp do nazwanych lokalizacji można uzyskać w sekcji **Zarządzanie** na stronie Dostęp warunkowy.

![Nazwane lokalizacje w programie "Dostęp warunkowy"](./media/location-condition/02.png)

Nazwana lokalizacja ma następujące składniki:

![Tworzenie nowej nazwanej lokalizacji](./media/location-condition/42.png)

- **Nazwa** — nazwa wyświetlana nazwanej lokalizacji.
- **Zakresy adresów IP** — co najmniej jeden zakres adresów IPv4 w formacie CIDR. Określanie zakresu adresów IPv6 nie jest obsługiwane.

   > [!NOTE]
   > Zakresy adresów IPv6 nie mogą być obecnie uwzględniane w nazwanej lokalizacji. Oznacza to, że zakresy IPv6 nie mogą być wykluczone z zasad dostępu warunkowego.

- **Oznacz jako zaufaną lokalizację** — flagę można ustawić dla nazwanej lokalizacji w celu wskazania zaufanej lokalizacji. Zazwyczaj zaufane lokalizacje to obszary sieciowe kontrolowane przez dział IT. Oprócz dostępu warunkowego zaufane nazwane lokalizacje są również używane przez usługi Azure Identity Protection i raporty zabezpieczeń usługi Azure AD w celu zmniejszenia [liczby fałszywych alarmów.](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)
- **Kraje/regiony** — ta opcja umożliwia wybranie jednego lub większej liczby krajów lub regionów w celu zdefiniowania nazwanej lokalizacji.
- **Uwzględnij nieznane obszary** — niektóre adresy IP nie są mapowane na określony kraj lub region. Ta opcja umożliwia wybranie, czy te adresy IP powinny być uwzględnione w nazwanej lokalizacji. Użyj tego ustawienia, gdy zasady przy użyciu nazwanej lokalizacji powinny mieć zastosowanie do nieznanych lokalizacji.

Liczba nazwanych lokalizacji, które można skonfigurować jest ograniczona przez rozmiar obiektu pokrewnego w usłudze Azure AD. Lokalizacje można konfigurować na podstawie następujących ograniczeń:

- Jedna nazwana lokalizacja z maksymalnie 1200 zakresami adresów IP.
- Maksymalnie 90 nazwanych lokalizacji z przypisanym do każdego z nich jednym zakresem IP.

Zasady dostępu warunkowego dotyczą ruchu IPv4 i IPv6. Obecnie nazwane lokalizacje nie zezwalają na konfigurowanie zakresów IPv6. To ograniczenie powoduje następujące sytuacje:

- Zasady dostępu warunkowego nie mogą być kierowane do określonych zakresów IPv6
- Zasady dostępu warunkowego nie mogą wykluczać określonych zakresów protokołu IPV6

Jeśli zasada jest skonfigurowana do zastosowania do "Dowolnej lokalizacji", będzie stosowana do ruchu IPv4 i IPv6. Nazwane lokalizacje skonfigurowane dla określonych krajów i regionów obsługują tylko adresy IPv4. Ruch IPv6 jest uwzględniany tylko wtedy, gdy wybrano opcję "uwzględnij nieznane obszary".

## <a name="trusted-ips"></a>Zaufane adresy IP

Zakresy adresów IP reprezentujące lokalny intranet organizacji można również skonfigurować w [ustawieniach usługi uwierzytelniania wieloskładnikowego](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ta funkcja umożliwia skonfigurowanie do 50 zakresów adresów IP. Zakresy adresów IP są w formacie CIDR. Aby uzyskać więcej informacji, zobacz [Zaufane usługi IP](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Jeśli masz skonfigurowane zaufane usługi IP, są one wyświetlane jako **zaufany identyfikator IPS usługi MFA** na liście lokalizacji dla warunku lokalizacji.

### <a name="skipping-multi-factor-authentication"></a>Pomijanie uwierzytelniania wieloskładnikowego

Na stronie ustawienia usługi uwierzytelniania wieloskładnikowego można zidentyfikować użytkowników intranetu firmowego, wybierając **pozycję Pomiń uwierzytelnianie wieloskładnikowe dla żądań od użytkowników federacyjnych w moim intranecie**. To ustawienie wskazuje, że oświadczenie sieci wewnętrznej firmy, które jest wystawiane przez usługi AD FS, powinno być zaufane i używane do identyfikowania użytkownika jako znajdującego się w sieci firmowej. Aby uzyskać więcej informacji, zobacz [Włączanie funkcji Zaufane punkty kontaktowe przy użyciu funkcji Dostęp warunkowy](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaznaczeniu tej opcji, w tym nazwana lokalizacja **zaufanego usługi IPS usługi MFA** będzie stosowana do wszystkich zasad z wybraną opcją.

W przypadku aplikacji mobilnych i klasycznych, które mają długotrwałe okresy istnienia sesji, dostęp warunkowy jest okresowo ponownie wycenywany. Wartość domyślna to raz na godzinę. Gdy oświadczenie sieci wewnętrznej firmy jest wystawiane tylko w czasie początkowego uwierzytelniania, usługa Azure AD może nie mieć listy zaufanych zakresów adresów IP. W takim przypadku trudniej jest ustalić, czy użytkownik nadal jest w sieci firmowej:

1. Sprawdź, czy adres IP użytkownika znajduje się w jednym z zaufanych zakresów adresów IP.
2. Sprawdź, czy pierwsze trzy oktety adresu IP użytkownika są zgodne z trzema pierwszymi oktetami adresu IP początkowego uwierzytelniania. Adres IP jest porównywany z początkowym uwierzytelnianiem, gdy pierwotnie wystawiono oświadczenie sieci wewnętrznej i potwierdzono lokalizację użytkownika.

Jeśli oba kroki nie powiodą się, użytkownik nie jest już na zaufanym ip.

## <a name="location-condition-configuration"></a>Konfiguracja warunku lokalizacji

Podczas konfigurowania warunku lokalizacji można rozróżnić:

- Dowolnej aplikacji.
- Wszystkie zaufane lokalizacje
- Wybrane lokalizacje

![Konfiguracja warunku lokalizacji](./media/location-condition/01.png)

### <a name="any-location"></a>Dowolnej aplikacji.

Domyślnie **wybranie opcji Dowolna lokalizacja** powoduje, że zasada ma być stosowana do wszystkich adresów IP, co oznacza dowolny adres w Internecie. To ustawienie nie jest ograniczone do adresów IP skonfigurowanych jako nazwana lokalizacja. Po **wybraniu opcji Dowolna lokalizacja**nadal można wykluczyć określone lokalizacje z zasad. Na przykład można zastosować zasady do wszystkich lokalizacji z wyjątkiem zaufanych lokalizacji, aby ustawić zakres na wszystkie lokalizacje, z wyjątkiem sieci firmowej.

### <a name="all-trusted-locations"></a>Wszystkie zaufane lokalizacje

Ta opcja dotyczy:

- Wszystkie lokalizacje, które zostały oznaczone jako zaufana lokalizacja
- **Zaufany ips usługi MFA** (jeśli jest skonfigurowany)

### <a name="selected-locations"></a>Wybrane lokalizacje

Za pomocą tej opcji można wybrać jedną lub więcej nazwanych lokalizacji. Aby zasady z tym ustawieniem zostały zastosowane, użytkownik musi połączyć się z dowolnej z wybranych lokalizacji. Po kliknięciu przycisku **Wybierz** nazwany formant wyboru sieci, który pokazuje listę nazwanych sieci zostanie otwarta. Lista pokazuje również, czy lokalizacja sieciowa została oznaczona jako zaufana. Nazwana lokalizacja o nazwie **Zaufane adresy IP usługi MFA** jest używana do uwzględnienia ustawień IP, które można skonfigurować na stronie ustawień usługi uwierzytelniania wieloskładnikowego.

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="when-is-a-location-evaluated"></a>Kiedy oceniana jest lokalizacja?

Zasady dostępu warunkowego są oceniane, gdy:

- Użytkownik początkowo loguje się do aplikacji internetowej, aplikacji mobilnej lub klasycznej.
- Aplikacja mobilna lub klasyczna, która używa nowoczesnego uwierzytelniania, używa tokenu odświeżania, aby uzyskać nowy token dostępu. Domyślnie ten czek jest raz na godzinę.

To sprawdzenie oznacza, że w przypadku aplikacji mobilnych i klasycznych korzystających z nowoczesnego uwierzytelniania zmiana lokalizacji zostanie wykryta w ciągu godziny od zmiany lokalizacji sieciowej. W przypadku aplikacji mobilnych i klasycznych, które nie używają nowoczesnego uwierzytelniania, zasady są stosowane dla każdego żądania tokenu. Częstotliwość żądania może się różnić w zależności od aplikacji. Podobnie w przypadku aplikacji sieci web zasady są stosowane podczas początkowego logowania i jest dobre dla okresu istnienia sesji w aplikacji sieci web. Ze względu na różnice w okresach istnienia sesji w aplikacjach czas między oceną zasad będzie się również różnić. Za każdym razem, gdy aplikacja żąda nowego tokenu logowania, zasady są stosowane.

Domyślnie usługa Azure AD wystawia token na podstawie godzinowej. Po przejściu z sieci firmowej w ciągu godziny zasady są wymuszane dla aplikacji korzystających z nowoczesnego uwierzytelniania.

### <a name="user-ip-address"></a>Adres IP użytkownika

Adres IP używany w ocenie zasad jest publiczny adres IP użytkownika. W przypadku urządzeń w sieci prywatnej ten adres IP nie jest adresem IP klienta urządzenia użytkownika w intranecie, ale adresem używanym przez sieć do łączenia się z publicznym Internetem.

> [!WARNING]
> Jeśli urządzenie ma tylko adres IPv6, konfigurowanie warunku lokalizacji nie jest obsługiwane.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Zbiorcze przesyłanie i pobieranie nazwanych lokalizacji

Podczas tworzenia lub aktualizowania nazwanych lokalizacji w przypadku aktualizacji zbiorczych można przekazać lub pobrać plik CSV z zakresami adresów IP. Przekazywanie zastępuje zakresy adresów IP na liście zakresami z pliku. Każdy wiersz pliku zawiera jeden zakres adresów IP w formacie CIDR.

### <a name="cloud-proxies-and-vpns"></a>Serwery proxy i VPN w chmurze

Podczas korzystania z serwera proxy hostowanego w chmurze lub rozwiązania sieci VPN adres IP używany przez usługę Azure AD podczas oceny zasad jest adresem IP serwera proxy. Nagłówek X-Forwarded-For (XFF), który zawiera publiczny adres IP użytkownika, nie jest używany, ponieważ nie ma sprawdzania poprawności, że pochodzi z zaufanego źródła, więc przedstawia metodę fałszowania adresu IP.

Gdy serwer proxy w chmurze jest na miejscu, można użyć zasad, które są używane do żądania urządzenia przyłączonego do domeny lub oświadczenia wewnętrznego corpnet z usług AD FS.

### <a name="api-support-and-powershell"></a>Obsługa interfejsu API i programu PowerShell

Interfejs API i program PowerShell nie są jeszcze obsługiwane dla nazwanych lokalizacji ani zasad dostępu warunkowego.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymaganie usługi MFA dla określonych aplikacji z dostępem warunkowym usługi Azure Active Directory](app-based-mfa.md).
- Jeśli chcesz skonfigurować zasady dostępu warunkowego dla swojego środowiska, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).

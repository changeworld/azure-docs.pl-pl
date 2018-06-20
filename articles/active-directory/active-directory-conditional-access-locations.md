---
title: Co to jest lokalizacji warunki dostępu warunkowego w usłudze Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak użyć warunku lokalizacji, aby kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: e5924262d9965c4e6732f8c23031cd575dc7958d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232478"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Co to jest warunek lokalizację w usłudze Azure Active Directory dostępu warunkowego? 

Z [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Warunek lokalizacji zasady dostępu warunkowego umożliwia powiązanie ustawienia kontroli dostępu do lokalizacji sieciowych użytkowników.

Ten artykuł zawiera informacje potrzebne do skonfigurowania warunku lokalizacji. 

## <a name="locations"></a>Lokalizacje

Usługi Azure AD włącza funkcji logowania jednokrotnego do urządzeń, aplikacji i usług z dowolnego miejsca w publicznej sieci internet. Dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika można kontrolować z warunkiem lokalizacji. Dostępne są następujące typowe przypadki użycia dla warunku lokalizacji:

- Wymagania uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi, gdy znajdują się poza siecią firmową  

- Blokuje dostęp dla użytkowników uzyskujących dostęp do usługi z określonego krajach lub regionach. 

Lokalizacja jest etykietę dla lokalizacji sieciowej albo reprezentuje nazwanego lokalizacji czy uwierzytelnianie wieloskładnikowe zaufanych adresów IP.


## <a name="named-locations"></a>Lokalizacje nazwane 

Nazwane lokalizacje umożliwia tworzenie logiczne grupy zakresów adresów IP, innych krajów i regionów. 

Są dostępne w lokalizacji o nazwie **Zarządzaj** sekcji strony dostępu warunkowego.

![Lokalizacje](./media/active-directory-conditional-access-locations/02.png)

 


Nazwane lokalizacji zawiera następujące składniki:

![Lokalizacje](./media/active-directory-conditional-access-locations/42.png)

- **Nazwa** — Nazwa wyświetlana nazwanego lokalizacji.

- **Zakresy adresów IP** — co najmniej jeden adres IP z zakresów w formacie CIDR.

- **Oznacz jako zaufanej lokalizacji** -flagę można ustawić dla nazwanego lokalizacji wskazać zaufanej lokalizacji. Zazwyczaj zaufanych lokalizacji są obszarów sieci, które są kontrolowane przez dział IT. Oprócz dostępu warunkowego, zaufanych lokalizacji o nazwie są również używane przez raporty dotyczące zabezpieczeń Azure ochrony tożsamości i usługi Azure AD do zmniejszenia [fałszywych alarmów](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Kraj / regiony** — ta opcja umożliwia wybranie co najmniej jeden kraj lub region, aby zdefiniować lokalizacji o nazwie. 

- **Obejmują obszary nieznany** — niektórych adresów nie są mapowane do określonego kraju. Tę opcję można wybrać, jeśli te adresy IP powinny znajdować się w lokalizacji o nazwie. Gdy zasady za pomocą lokalizacji o nazwie stosuje się do nieznanej lokalizacji może być wyboru.

Liczba nazwane lokalizacje, które można skonfigurować jest ograniczona przez rozmiar obiektu pokrewnego w usłudze Azure AD. Można skonfigurować:

- Jedną nazwę lokalizacji z zakresów adresów IP do 1200.

- Maksymalnie 90 lokalizacji o nazwie jeden zakres adresów IP przypisanych do każdego z nich.




## <a name="trusted-ips"></a>Zaufane adresy IP

Można również skonfigurować zakresy adresów IP reprezentujący lokalny intranet organizacji w [ustawień usługi Multi-Factor authentication](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ta funkcja umożliwia konfigurowanie zakresów adresów IP do 50. Zakresy adresów IP są w formacie CIDR. Aby uzyskać więcej informacji, zobacz [zaufanych adresów IP](authentication/howto-mfa-mfasettings.md#trusted-ips).  

Jeśli mają zaufany skonfigurowane adresy IP, są wyświetlane jako **listę zaufanych adresów IP MFA** na liście lokalizacji dla warunku lokalizacji.   

### <a name="skipping-multi-factor-authentication"></a>Pomijanie usługi Multi-Factor authentication

Na stronie ustawień usługi uwierzytelniania wieloskładnikowego można określić użytkownicy intranetu, wybierając **Pomiń uwierzytelnianie Multi-Factor authentication dla żądań od użytkowników federacyjnych w moim intranecie**. To ustawienie wskazuje, że wewnątrz firmowych sieciowe oświadczeń, który jest wystawiany przez usługi AD FS, powinny być zaufane i używane do identyfikowania użytkownika jako znajdujące się w sieci firmowej. Aby uzyskać więcej informacji, zobacz [włączyć funkcję zaufanych adresów IP przy użyciu dostępu warunkowego](authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaznaczeniu tej opcji, w tym o nazwie lokalizacji **adresów IP z usługami MFA zaufane** będzie dotyczyć wszystkie zasady z tą wybrane.

Dla aplikacji mobilnych i klasycznych, które mają tam długo okresy istnienia sesji, dostęp warunkowy jest okresowo ponowna ocena. Wartość domyślna to raz godzinę. Gdy wewnątrz sieci firmowej oświadczenia i tylko wystawione podczas uwierzytelniania, usługi Azure AD nie może mieć listy zaufanych zakresów adresów IP. W tym przypadku jest trudne do ustalenia, czy użytkownik jest nadal w sieci firmowej:

1. Sprawdź, czy adres IP użytkownika jest w jednym z zaufanych zakresów IP.

2. Sprawdź, czy pierwsze trzy oktety adresu IP użytkownika odpowiada pierwsze 3 oktety adresu początkowego uwierzytelniania. Adres IP jest porównywany z uwierzytelniania, ponieważ jest to, kiedy wewnątrz sieci firmowej oświadczeń było początkowo wydane i lokalizację użytkownika została zweryfikowana.

Jeśli obu czynności zakończą się niepowodzeniem, użytkownik jest uznawany za już na zaufanego adresu IP.



## <a name="location-condition-configuration"></a>Stan konfiguracji

Po skonfigurowaniu warunku lokalizacji, masz możliwość rozróżnienia:

- Dowolna lokalizacja 
- Wszystkie zaufane lokalizacje
- Wybrane lokalizacje

![Lokalizacje](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>Dowolna lokalizacja

Domyślnie zaznaczenie **dowolnej lokalizacji** powoduje, że zasady do zastosowania dla wszystkich adresów IP, która oznacza każdy adres w Internecie. To ustawienie nie jest ograniczona do adresów IP, które zostały skonfigurowane jako lokalizacji o nazwie. Po wybraniu **dowolnej lokalizacji**, nadal można wykluczyć określone lokalizacje z zasad. Na przykład można zastosować zasady do wszystkich lokalizacji, z wyjątkiem zaufanych lokalizacji, aby ustawić zakres do wszystkich lokalizacji, z wyjątkiem sieci firmowej.

### <a name="all-trusted-locations"></a>Wszystkie zaufane lokalizacje

Ta opcja ma zastosowanie do:

- Wszystkie lokalizacje, które zostały oznaczone jako zaufanej lokalizacji
- **Adresy IP MFA zaufane** (jeśli jest skonfigurowane)


### <a name="selected-locations"></a>Wybrane lokalizacje

Po wybraniu tej opcji można wybrać co najmniej jedna lokalizacja nazwanego. To ustawienie, aby zastosować zasady użytkownik musi połączyć za pomocą dowolnego z wybranej lokalizacji. Po kliknięciu **wybierz** otwiera formant wyboru nazwanej sieci z listą o nazwie sieci. Lista zawiera, jeśli lokalizacja sieciowa została oznaczona jako zaufane. Nazwane lokalizacji o nazwie **adresów IP z usługami MFA zaufane** jest używana do włączenia ustawienia protokołu IP, które można skonfigurować na stronie Ustawienia usługi Multi-Factor authentication.

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="when-is-a-location-evaluated"></a>Podczas oceny lokalizację?

Zasady dostępu warunkowego są oceniane po: 

- Początkowo zalogowaniu użytkownika do aplikacji sieci web app, telefon komórkowy lub pulpitu. 

- Aplikacji mobilnych lub komputerowych, który używa nowoczesnego uwierzytelniania, używa token odświeżania, aby uzyskać nowy token dostępu. Domyślnie jest to raz godzinę. 

Oznacza to, że dla urządzeń przenośnych i aplikacji korzystających z nowoczesnego uwierzytelniania zmiana lokalizacji można wykryć w ciągu godziny zmiany lokalizacji sieciowej. Dla aplikacji mobilnych i klasycznych, które nie używają nowoczesnego uwierzytelniania zasady są stosowane na każde żądanie tokenu. Częstotliwość żądania mogą się różnić w zależności od aplikacji. Podobnie dla aplikacji sieci web, zasada zostanie zastosowane w początkowej logowania i dotyczy okresu istnienia sesji w aplikacji sieci web. Z powodu różnic w okresy istnienia sesji w aplikacjach różnią się również czas między oceny zasad. Za każdym żądaniem aplikacji nowy token logowania, zasady są stosowane.


Domyślnie program Azure AD wystawia token co godzinę. Po przeniesieniu poza siecią firmową, w ciągu godziny zasady są wymuszane dla aplikacji korzystających z nowoczesnego uwierzytelniania.


### <a name="user-ip-address"></a>Adres IP użytkownika

Adres IP, który jest używany podczas oceny zasad jest publiczny adres IP użytkownika. Dla urządzeń w sieci prywatnej to nie jest adres IP klienta urządzenia użytkownika w sieci intranet, jest to adres używanej przez sieć do nawiązania połączenia z Internetem. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Zbiorcze, przekazywanie i pobieranie lokalizacji o nazwie

Podczas tworzenia lub aktualizacji o nazwie lokalizacje aktualizacje zbiorcze, możesz przesłać lub pobrać plik CSV z zakresu adresów IP. Przekazanie zastępuje zakresu adresów IP na liście z pliku. Każdy wiersz w pliku zawiera jeden zakres adresów IP w formacie CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Serwery proxy chmury i sieci VPN 

Jeśli używasz serwera proxy w chmurze hostowanej lub rozwiązanie sieci VPN, adres IP usługi Azure AD używa podczas oceny zasad jest adres IP serwera proxy. Nagłówek X-Forwarded-For (XFF), która zawiera użytkowników, publiczny adres IP nie jest używana, ponieważ nie istnieje żadne weryfikacji, który pochodzi z zaufanego źródła, dlatego przedstawia metodę faking adresu IP. 

Po serwera proxy w chmurze w miejscu, zasady, które służy do wymagają urządzenia dołączonego do domeny mogą służyć lub wewnątrz corpnet oświadczenia usług AD FS.



### <a name="api-support-and-powershell"></a>Obsługa interfejsu API i programu PowerShell 

Interfejs API i programu PowerShell nie jest jeszcze obsługiwana w przypadku lokalizacji o nazwie lub dla zasad dostępu warunkowego.





## <a name="next-steps"></a>Kolejne kroki

- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 

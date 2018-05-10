---
title: Skonfiguruj uwierzytelnianie wieloskładnikowe platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania ustawień usługi Azure Multi-Factor Authentication w portalu Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 509df5274a5e44342390036511689fa8f8146807
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurowanie ustawień usługi Azure Multi-Factor Authentication

Ten artykuł pomaga w zarządzaniu Azure Multi-Factor Authentication, teraz, gdy jest uruchomiona. Obejmuje ona różnych tematów, które ułatwiają maksymalne wykorzystanie usługi Azure Multi-Factor Authentication. Nie wszystkie funkcje są dostępne w każdym [wersja Azure Multi-Factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

| Cecha | Opis | 
|:--- |:--- |
| [Blokować i odblokowywać użytkowników](#block-and-unblock-users) |Użyj funkcji Użytkownicy Zablokuj/Odblokuj, aby uniemożliwić użytkownikom odbieranie żądań uwierzytelniania. |
| [Alert o oszustwie](#fraud-alert) |Skonfiguruj funkcja alertów oszustwa, dzięki czemu użytkownicy mogą raportować fałszywych próbuje uzyskać dostęp do ich zasobów. |
| [Jednorazowe obejście](#one-time-bypass) |Użyj funkcji jednorazowe obejście, aby umożliwić użytkownikom uwierzytelnianie jeden raz przez _pomijanie_ usługi Multi-Factor Authentication. |
| [Niestandardowe wiadomości głosowe](#custom-voice-messages) |Można użyć własnych nagrań lub pozdrowienia przy użyciu uwierzytelniania wieloskładnikowego, należy użyć funkcji komunikatów niestandardowych głosu. |
| [Buforowanie](#caching-in-azure-multi-factor-authentication) |Funkcja buforowania można ustawić w określonym przedziale czasu, dzięki czemu kolejne próby uwierzytelniania powiedzie się automatycznie. |
| [Listę zaufanych adresów IP](#trusted-ips) |Administratorzy dzierżawy zarządzane lub federacyjnych funkcja zaufanych adresów IP do pominięcia weryfikacji dwuetapowej dla użytkowników, którzy zalogować się w intranecie firmy. |
| [Hasła aplikacji](#app-passwords) |Włącz obejście usługi Multi-Factor Authentication i kontynuować pracę w aplikacji za pomocą funkcji hasła aplikacji. |
| [Należy pamiętać, uwierzytelnianie wieloskładnikowe dla zaufanych urządzeń i przeglądarki](#remember-multi-factor-authentication-for-trusted-devices) |Ta funkcja służy do zapamiętania zaufanych urządzeń i przeglądarki przez liczbę dni, po użytkownik został pomyślnie zalogowany przy użyciu usługi Multi-Factor Authentication. |
| [Metody wyboru weryfikacji](#selectable-verification-methods) |Użyj tej funkcji do listy metod uwierzytelniania, które użytkownicy będą mogli używać wyboru. |

## <a name="block-and-unblock-users"></a>Blokować i odblokowywać użytkowników

Użyj _blokować i odblokowywać użytkowników_ funkcji, aby uniemożliwić użytkownikom odbieranie żądań uwierzytelniania. Wszelkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy zostaną zablokowane przez 90 dni od czasu, który zostały zablokowane.

### <a name="block-a-user"></a>Zablokuj użytkownika

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera usługi MFA** > **Zablokuj/Odblokuj użytkowników**.
3. Wybierz **Dodaj** aby uniemożliwić użytkownikowi.
4. Wybierz **grupy replikacji**. Wprowadź nazwę użytkownika dla zablokowanych użytkowników jako **username<span></span>@domain.com**. Wprowadź komentarz w **Przyczyna** pola.
5. Wybierz **Dodaj** na zakończenie zablokowania użytkownika.

### <a name="unblock-a-user"></a>Odblokuj użytkownika

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera usługi MFA** > **Zablokuj/Odblokuj użytkowników**.
3. Wybierz **Odblokuj** w **akcji** kolumnę obok użytkownika do odblokowania.
4. Wprowadź komentarz w **Przyczyna odblokowania** pola.
5. Wybierz **Odblokuj** na zakończenie odblokowania użytkownika.

## <a name="fraud-alert"></a>Alert dotyczący wykrycia oszustwa

Skonfiguruj _oszustwa_ funkcji, dzięki czemu użytkownicy mogą raportować fałszywych próbuje uzyskać dostęp do swoich zasobów. Użytkownicy mogą raportować oszustwa prób przy użyciu aplikacji mobilnej lub przez telefon.

### <a name="turn-on-fraud-alerts"></a>Włącz alertów oszustwa

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera usługi MFA** > **oszustwa**.

   ![Włącz alertów oszustwa](./media/howto-mfa-mfasettings/fraudalert.png)

3. Ustaw **Zezwalaj użytkownikom na przesłanie alertów oszustwa** ustawienie **na**.
4. Wybierz pozycję **Zapisz**.

### <a name="configuration-options"></a>Opcje konfiguracji

- **Blokuj użytkownika, gdy zostaje zgłoszone oszustwo**: Jeśli użytkownik zgłasza oszustwa, ich konto zostało zablokowane przez 90 dni lub dopóki administrator odblokowuje swojego konta. Administrator może przejrzeć logowania za pomocą raportu logowania i podjąć odpowiednie działania w celu zapobiegania oszustwom przyszłych. Administrator może następnie [odblokować](#unblock-a-user) konta użytkownika.
- **Kod zgłoszenia oszustwa w trakcie początkowego pozdrowienia**: gdy użytkownik odbierze połączenie telefoniczne do przeprowadzenia weryfikacji dwuetapowej, zwykle naciśnięciu **#** o potwierdzenie ich logowania. Zgłoszenia oszustwa użytkownik musi wprowadzić kod przed naciskając klawisz **#**. Ten kod jest **0** domyślnie, ale można go dostosować.

  >[!NOTE]
  >Domyślne pozdrowienia głosowe firmy Microsoft poinstruować użytkowników, aby naciśnij **0#** przesłać alert o oszustwie. Jeśli chcesz użyć kodu innego niż **0**, rejestrowania i przekazać swoje własne niestandardowe pozdrowienia głosowe odpowiednie instrukcje dla użytkowników.
  >

### <a name="view-fraud-reports"></a>Wyświetl raporty dotyczące oszustwa

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **logowania**. Raport oszustwa jest obecnie częścią raportu standardowego logowania usługi AD platformy Azure.

## <a name="one-time-bypass"></a>Jednokrotne obejście

_Jednorazowe obejście_ funkcja pozwala na uwierzytelnianie jeden raz bez przeprowadzania weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po upływie określonej liczby sekund. W sytuacjach, w którym aplikacji mobilnej lub telefon nie odbiera powiadomień lub połączeń telefonicznych można zezwolić jednorazowe obejście, więc użytkownik ma dostęp do żądanego zasobu.

### <a name="create-a-one-time-bypass"></a>Utwórz jednorazowe obejście

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera usługi MFA** > **jednorazowe obejście**.

   ![Utwórz jednorazowe obejście](./media/howto-mfa-mfasettings/onetimebypass.png)

3. Wybierz pozycję **Dodaj**.
4. Jeśli to konieczne, wybierz grupę replikacji obejście.
5. Wprowadź nazwę użytkownika jako **username<span></span>@domain.com**. Wprowadź liczbę sekund, które powinno trwać obejście. Wprowadź przyczynę obejścia. 
6. Wybierz pozycję **Dodaj**. Limit czasu przechodzi w stan obowiązywać natychmiast. Użytkownik musi się zalogować przed wygaśnięciem jednorazowego obejścia. 

### <a name="view-the-one-time-bypass-report"></a>Wyświetl raport jednorazowe obejście

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Active Directory** > **serwera usługi MFA** > **jednorazowe obejście**.

## <a name="custom-voice-messages"></a>Niestandardowe wiadomości głosowe
Można użyć własnych nagrań lub pozdrowienia na potrzeby weryfikacji dwuetapowej z _niestandardowe wiadomości głosowe_ funkcji. Te komunikaty można ponadto aby lub zastąpić nagrań firmy Microsoft.

Przed rozpoczęciem należy pamiętać o następujących ograniczeniach:

* Obsługiwane formaty plików są .wav lub .mp3.
* Limit rozmiaru plików to 5 MB.
* Uwierzytelnianie wiadomości powinien być krótszy niż 20 sekund. Komunikaty, które są dłuższe niż 20 sekund może spowodować niepowodzenie weryfikacji. Użytkownik może nie odpowiadać przed zakończeniem wiadomości i weryfikacja upłynie limit czasu.

### <a name="set-up-a-custom-message"></a>Konfigurowanie niestandardowych komunikatów

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera usługi MFA** > **ustawienia połączeń telefonicznych**.

   ![Rekord phone niestandardowych komunikatów](./media/howto-mfa-mfasettings/phonecallsettings.png)

3. Wybierz **pozdrowienia Dodaj**.
4. Wybierz typ pozdrowienie. Wybierz język.
5. Wybierz plik dźwiękowy MP3 lub WAV do przekazania.
6. Wybierz pozycję **Dodaj**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Buforowanie w uwierzytelnianie wieloskładnikowe platformy Azure

Można ustawić okres czasu, aby umożliwić prób uwierzytelnienia, po uwierzytelnieniu użytkownika za pomocą _buforowanie_ funkcji. Kolejne próby uwierzytelniania dla użytkowników w określonym przedziale czasu powiodło się automatycznie. Buforowanie jest używany głównie systemów lokalnych, takich jak sieć VPN, wysyłania wielu żądań weryfikacji podczas pierwszego żądania jest nadal w toku. Ta funkcja umożliwia kolejnych żądań do pomyślnego automatycznie po użytkownika powiedzie się pierwszy Weryfikacja w toku. 

>[!NOTE]
>Funkcja buforowania nie mają służyć do logowania do usługi Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Konfigurowanie pamięci podręcznej 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **serwera usługi MFA** > **buforowanie reguły**.

   ![Konfigurowanie reguł buforowania](./media/howto-mfa-mfasettings/cachingrules.png)

3. Wybierz pozycję **Dodaj**.
4. Wybierz **typ pamięci podręcznej** z listy rozwijanej. Wprowadź maksymalną liczbę **sekund buforowania**. 
5. Jeśli to konieczne, wybierz typ uwierzytelniania i określ aplikacji. 
6. Wybierz pozycję **Dodaj**.

## <a name="trusted-ips"></a>Zaufane adresy IP

_Zaufanych adresów IP_ funkcji Azure Multi-Factor Authentication jest używana przez administratorów dzierżawy zarządzane lub federacyjnych. Funkcja Pomija weryfikację dwuetapową dla użytkowników, którzy zalogować się w intranecie firmy. Funkcja jest dostępna z pełną wersję programu Azure Multi-Factor Authentication, a nie bezpłatną wersję dla administratorów. Aby uzyskać szczegółowe informacje dotyczące sposobu uzyskania pełną wersję programu Azure Multi-Factor Authentication, zobacz [Azure Multi-Factor Authentication](multi-factor-authentication.md).

Jeśli Twoja organizacja wdraża rozszerzenia serwera NPS zapewnienie MFA do lokalnych aplikacji Uwaga źródłowy adres IP zawsze będą poprzez prób uwierzytelniania serwera NPS.

| Typ dzierżawy usługi Azure AD | Opcji zaufanych adresów IP |
|:--- |:--- |
| Zarządzane |**Określonego zakresu adresów IP**: Administratorzy określić zakres adresów IP, które można pominąć weryfikacji dwuetapowej dla użytkowników, którzy zalogować się w intranecie firmy.|
| Federacyjne |**Wszystkich użytkowników federacyjnych**: wszystkie użytkownicy federacyjni, którzy zalogować się w ramach organizacji można pominąć weryfikacji dwuetapowej. Użytkownicy pominąć weryfikacji za pomocą oświadczeń, wystawiany przez usługi Active Directory Federation Services (AD FS).<br/>**Określonego zakresu adresów IP**: Administratorzy określić zakres adresów IP, które można pominąć weryfikacji dwuetapowej dla użytkowników, którzy zalogować się w intranecie firmy. |

Adresy IP zaufanych obejścia działa tylko z wewnątrz sieci intranet firmy. W przypadku wybrania **wszystkich użytkowników federacyjnych** opcja i użytkownik zaloguje się z poza firmową siecią intranet, użytkownik musi uwierzytelnić przy użyciu weryfikacji dwuetapowej. Proces jest taki sam, nawet wtedy, gdy użytkownik przedstawia oświadczenia usług AD FS. 

**Środowisko użytkownika końcowego wewnątrz corpnet**

Po wyłączeniu funkcji zaufanych adresów IP, weryfikacja dwuetapowa jest wymagany dla przepływów przeglądarki. Hasła aplikacji są wymagane dla starszych aplikacji wzbogaconego klienta. 

Po włączeniu funkcji zaufanych adresów IP, weryfikacja dwuetapowa to *nie* wymagane dla przepływów przeglądarki. Hasła aplikacji są *nie* wymagane w przypadku starszych aplikacji wzbogaconego klienta pod warunkiem, że użytkownik nie utworzyła hasła aplikacji. Gdy hasło aplikacji jest w użyciu, hasło pozostaje wymagane. 

**Środowisko użytkownika końcowego poza siecią corpnet**

Niezależnie od tego, czy jest włączona funkcja zaufanych adresów IP Weryfikacja dwuetapowa jest wymagane dla przepływów przeglądarki. Hasła aplikacji są wymagane dla starszych aplikacji wzbogaconego klienta. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Włącz nazwane lokalizacje przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **dostępu warunkowego** > **o nazwie lokalizacje**.
3. Wybierz **nową lokalizację**.
4. Wprowadź nazwę lokalizacji.
5. Wybierz **Oznacz jako zaufanej lokalizacji**.
6. Wprowadź zakres adresów IP w notacji CIDR, takich jak **192.168.1.1/24**.
7. Wybierz pozycję **Utwórz**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Włącz funkcję zaufanych adresów IP przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **dostępu warunkowego** > **o nazwie lokalizacje**.
3. Wybierz **skonfigurować uwierzytelnianie wieloskładnikowe zaufanych adresów IP**.
4. Na **ustawień usługi** w obszarze **zaufanych adresów IP**, wybierz jedną z poniższych dwóch opcji:
   
   * **Żądania od użytkowników federacyjnych pochodzące z moim intranecie**: Wybierz tę opcję, zaznacz pole wyboru. Federacyjna wszystkich użytkowników, którzy logowania z sieci firmowej pominąć weryfikacji dwuetapowej za pomocą oświadczeń, wystawiony przez usługi AD FS. Upewnij się, że usługi AD FS ma zasadę, aby dodawać oświadczenia intranet na odpowiedni ruch. Jeśli nie istnieje reguła, w usługach AD FS należy utworzyć następującą regułę:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Dla żądań z określonego zakresu publicznych adresów IP**: Wybierz tę opcję, wprowadź adresy IP w polu tekstowym za pomocą notacji CIDR.
   
     * Adresy IP, które znajdują się w xxx.xxx.xxx.1 zakresu za pośrednictwem xxx.xxx.xxx.254, można użyć w notacji, takich jak **xxx.xxx.xxx.0/24**.
     * Dla jednego adresu IP, użyj notacji, takich jak **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy, którzy zalogować się w tych adresów IP pominąć weryfikacji dwuetapowej.

5. Wybierz pozycję **Zapisz**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Włącz funkcję zaufanych adresów IP za pomocą ustawień usługi

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze usługi Multi-Factor Authentication, zaznacz **ustawienia usługi**.
5. Na **ustawień usługi** w obszarze **zaufanych adresów IP**, wybierz jedną z poniższych dwóch opcji:
   
   * **Żądania od użytkowników federacyjnych w moim intranecie**: Wybierz tę opcję, zaznacz pole wyboru. Federacyjna wszystkich użytkowników, którzy logowania z sieci firmowej pominąć weryfikacji dwuetapowej za pomocą oświadczeń, wystawiony przez usługi AD FS. Upewnij się, że usługi AD FS ma zasadę, aby dodawać oświadczenia intranet na odpowiedni ruch. Jeśli nie istnieje reguła, w usługach AD FS należy utworzyć następującą regułę:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Dla żądań z podsieci adresów określonego zakresu adresów IP**: Wybierz tę opcję, wprowadź adresy IP w polu tekstowym za pomocą notacji CIDR. 
     
     * Adresy IP, które znajdują się w xxx.xxx.xxx.1 zakresu za pośrednictwem xxx.xxx.xxx.254, można użyć w notacji, takich jak **xxx.xxx.xxx.0/24**.
     * Dla jednego adresu IP, użyj notacji, takich jak **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy, którzy zalogować się w tych adresów IP pominąć weryfikacji dwuetapowej.

6. Wybierz pozycję **Zapisz**.

![Włączyć zaufanych adresów IP za pomocą ustawień usługi](./media/howto-mfa-mfasettings/trustedips3.png)

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje, takie jak pakiet Office 2010 lub starszy i Apple Mail nie obsługują weryfikacji dwuetapowej. Aplikacje nie są skonfigurowane do akceptowania drugi weryfikacji. Aby korzystać z tych aplikacji, korzystać z _hasła aplikacji_ funkcji. Hasło aplikacji zamiast hasła tradycyjnych umożliwia Zezwalaj aplikacji na pominięcie weryfikacji dwuetapowej i kontynuować pracę.

Nowoczesne uwierzytelnianie jest obsługiwana dla klientów programu Microsoft Office 2013 lub nowszym. Pakiet Office 2013 klientów, w tym programu Outlook, obsługuje protokoły nowoczesnego uwierzytelniania i można ją włączyć do pracy z weryfikacji dwuetapowej. Po włączeniu klienta hasła aplikacji nie są wymagane dla klienta.

### <a name="considerations-about-app-passwords"></a>Zagadnienia dotyczące haseł aplikacji

Podczas korzystania z haseł aplikacji, należy wziąć pod uwagę następujące istotne kwestie:

* Hasła aplikacji tylko są wprowadzane raz na aplikację. Użytkownicy nie musieli śledzić hasła i wprowadzić je za każdym razem.
* Rzeczywiste hasło jest generowane automatycznie i nie jest dostarczony przez użytkownika. Hasło generowane automatycznie jest trudniej odgadnąć osobie atakującej i większe bezpieczeństwo.
* Istnieje limit 40 haseł na użytkownika. 
* Aplikacje, których buforowanie haseł i używać ich w scenariuszach lokalnych mogą kończą się niepowodzeniem, ponieważ hasła aplikacji nie jest znany poza konta firmowego lub szkolnego. Przykładem tego scenariusza jest wiadomości e-mail programu Exchange, które są lokalne, ale poczta jest archiwizowana w chmurze. W tym scenariuszu nie działa to samo hasło.
* Po włączeniu uwierzytelniania wieloskładnikowego na koncie użytkownika hasła aplikacji można użyć z klientów najbardziej korzystających z przeglądarki, takich jak Outlook i Microsoft Skype dla firm. Nie można wykonać działania administracyjne przy użyciu hasła aplikacji za pomocą aplikacji korzystających z przeglądarki, takich jak środowisko Windows PowerShell. Nie można wykonać akcje, nawet wtedy, gdy użytkownik ma konto administratora. Aby uruchamiać skrypty programu PowerShell, Utwórz konto usługi z silnym hasłem i nie włączaj konta na potrzeby weryfikacji dwuetapowej.

>[!WARNING]
>Hasła aplikacji nie działają w środowiskach hybrydowych, w którym klienci komunikować się z lokalnie i w chmurze punkty końcowe wykrywania automatycznego. Hasła domeny są wymagane do uwierzytelniania lokalnego. Hasła aplikacji są wymagane do uwierzytelniania w chmurze.
>

### <a name="guidance-for-app-password-names"></a>Wskazówki dotyczące nazwy haseł aplikacji

Nazwy haseł aplikacji odzwierciedlały urządzenie, na którym są używane. Jeśli użytkownik ma komputer przenośny z zainstalowanymi aplikacji korzystających z przeglądarki, takich jak Outlook, Word i Excel, należy utworzyć hasło aplikacji o nazwie **Laptop** dla tych aplikacji. Utwórz innego hasła aplikacji o nazwie **pulpitu** dla tych samych aplikacji, które są uruchamiane na komputerze stacjonarnym. 

>[!NOTE]
>Zaleca się utworzenie hasła jednej aplikacji na urządzeniu, zamiast hasła jednej aplikacji na aplikację.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federacyjnych lub pojedynczy znak na haseł aplikacji

Usługi Azure AD obsługuje Federacji lub rejestracji jednokrotnej (SSO) z lokalnego systemu Windows serwera usług domenowych Active Directory (AD DS). Jeśli Twoja organizacja jest Sfederowane przy użyciu usługi Azure AD i korzystania z usługi Azure Multi-Factor Authentication, należy wziąć pod uwagę następujące kwestie dotyczące haseł aplikacji.

>[!NOTE]
>Tylko klientom federacyjnych (SSO) mają zastosowanie następujące kwestie.

* Hasła aplikacji są weryfikowane przez usługę Azure AD i w związku z tym obejście federacji. Federacyjnej jest aktywnie używany tylko podczas konfiguracji hasła aplikacji.
* Dostawcy tożsamości (IdP) nie skontaktować się z dla użytkowników federacyjnych (SSO), w przeciwieństwie do przepływu pasywnego. Hasła aplikacji są przechowywane w ramach konta firmowego lub szkolnego. Gdy użytkownik opuści firmę, informacje o użytkowniku przechodzi do konta firmowego lub szkolnego, za pomocą **DirSync** w czasie rzeczywistym. Wyłączenie/usunięcie konta może potrwać do trzech godzin, aby zsynchronizować, które można opóźnienie wyłączenia/usunięcia hasła aplikacji w usłudze Azure AD.
* Ustawienia kontroli dostępu klienta lokalnego nie są uznawane za pomocą funkcji hasła aplikacji.
* Nie lokalnymi możliwości rejestrowania/inspekcji jest uwierzytelnienie dostępne do użycia z funkcją hasła aplikacji.
* Niektóre zaawansowane architektury wymagają kombinację poświadczenia na potrzeby weryfikacji dwuetapowej z klientami. Te poświadczenia mogą obejmować służbowy lub nazwa użytkownika konta służbowego i hasła i haseł aplikacji. Wymagania zależą od sposobu uwierzytelniania. Dla klientów, którzy uwierzytelniania względem infrastruktury lokalnej służbowego konta użytkownika i hasło wymaganą. Dla klientów, którzy uwierzytelniania usługi Azure AD wymagane jest hasło aplikacji.

  Na przykład załóżmy, że masz następujące architektura:

  * Z lokalnym wystąpieniem usługi Active Directory jest Sfederowane przy użyciu usługi Azure AD.
  * Używasz usługi Exchange online.
  * Używasz programu Skype dla firm lokalnego.
  * Używasz usługi Azure Multi-Factor Authentication.

  ![Korzystanie z haseł aplikacji w organizacji federacyjnych](./media/howto-mfa-mfasettings/federated.png)

  W tym scenariuszu można użyć następujących poświadczeń:

  * Aby zalogować się do usługi Skype dla firm, należy użyć pracy lub szkoły konta nazwę użytkownika i hasło.
  * Aby uzyskać dostęp do książki adresowej na kliencie programu Outlook, która łączy się z usługi Exchange online, należy użyć hasła aplikacji.

### <a name="allow-users-to-create-app-passwords"></a>Zezwalaj użytkownikom na tworzenie haseł aplikacji

Domyślnie użytkownicy nie mogą tworzyć hasła aplikacji. Musi być włączona funkcja hasła aplikacji. Aby udostępnić użytkownikom tworzenie haseł aplikacji, użyj następującej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze usługi Multi-Factor Authentication, zaznacz **ustawienia usługi**.
5. Na **ustawień usługi** wybierz pozycję **Zezwalaj użytkownikom na tworzenie haseł aplikacji do logowania do aplikacji niekorzystających z przeglądarki** opcji.

   ![Zezwalaj użytkownikom na tworzenie haseł aplikacji](./media/howto-mfa-mfasettings/trustedips3.png)

### <a name="create-app-passwords"></a>Tworzenie haseł aplikacji

Użytkownicy mogą tworzyć hasła aplikacji podczas ich początkowe rejestracyjny. Użytkownik może tworzyć hasła aplikacji na końcu procesu rejestracji.

Użytkownicy mogą także tworzyć hasła aplikacji po rejestracji. Hasła aplikacji można zmienić za pomocą ustawień w portalu Azure lub w portalu usługi Office 365. Aby uzyskać więcej informacji i uzyskać szczegółowe instrukcje dla użytkowników, zobacz [co to są hasła aplikacji w usłudze Azure Multi-Factor Authentication?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Należy pamiętać, uwierzytelniania wieloskładnikowego na zaufanych urządzeniach
_Pamiętaj uwierzytelnianie wieloskładnikowe_ funkcji urządzeń i przeglądarek, które są zaufane przez użytkownika jest bezpłatna funkcji dla wszystkich użytkowników usługi Multi-Factor Authentication. Użytkownicy mogą omijać późniejszych weryfikacji określoną liczbę dni, po ich został pomyślnie zalogowany do urządzenia za pomocą usługi Multi-Factor Authentication. Funkcja podnosi poziom użyteczność, minimalizując liczbę razy użytkownik ma do przeprowadzenia weryfikacji dwuetapowej na tym samym urządzeniu.

>[!IMPORTANT]
>W przypadku złamania zabezpieczeń konta lub urządzenia, pamiętaj o tym uwierzytelniania wieloskładnikowego na zaufanych urządzeniach może wpłynąć na bezpieczeństwo. Jeśli zostanie naruszone bezpieczeństwo konta firmowego lub zaufanego urządzenia utraty lub kradzieży, [Przywróć uwierzytelnianie wieloskładnikowe na wszystkich urządzeniach](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Akcja przywracania odwołuje zaufanego stanu ze wszystkich urządzeń, a użytkownik jest wymagane do przeprowadzenia weryfikacji dwuetapowej ponownie. Można również Poinstruuj użytkowników, aby przywrócić usługi Multi-Factor Authentication na urządzeniach z instrukcjami w [zarządzać ustawieniami na potrzeby weryfikacji dwuetapowej](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Jak działa funkcja

Funkcja uwierzytelnianie wieloskładnikowe Zapamiętaj ustawia trwały plik cookie w przeglądarce, gdy użytkownik wybierze **nie pytaj ponownie o X dni** opcja podczas logowania. Użytkownik nie jest monitowany ponownie uwierzytelnianie wieloskładnikowe z tej samej przeglądarki do momentu wygaśnięcia pliku cookie. Jeśli użytkownik otwiera innej przeglądarki, w tym samym urządzeniu lub czyści ich pliki cookie, są monitowani ponownie można zweryfikować. 

**Nie pytaj ponownie o X dni** opcja nie jest wyświetlana w przypadku aplikacji korzystających z przeglądarki, niezależnie od tego, czy aplikacja obsługuje nowoczesnego uwierzytelniania. Użyj tych aplikacji _tokenów odświeżania_ które zapewniają nowe tokeny dostępu co godzinę. Podczas sprawdzania poprawności tokenu odświeżania usługi Azure AD sprawdza ostatniej weryfikacji dwuetapowej, które wystąpiły w ciągu określonej liczby dni. 

Funkcja zmniejsza liczbę uwierzytelnień aplikacji sieci web, które zwykle monit zawsze. Funkcja zwiększa liczbę uwierzytelnień dla klientów nowoczesnego uwierzytelniania, które zwykle Monituj co 90 dni.

>[!IMPORTANT]
>**Pamiętaj uwierzytelnianie wieloskładnikowe** funkcja nie jest zgodny z **wylogowuj mnie** funkcji usług AD FS, kiedy użytkownicy wykonać weryfikację dwuetapową dla usług AD FS za pomocą usługi Azure Multi-Factor Uwierzytelnianie serwera lub rozwiązania innych firm usługi Multi-Factor authentication.
>
>Jeśli użytkownicy wybierają **wylogowuj mnie** na usług AD FS i również znak swoje urządzenia jako zaufane dla usługi Multi-Factor Authentication, użytkownik nie jest automatycznie zweryfikowane po **Pamiętaj uwierzytelnianie wieloskładnikowe**przekroczeniu limitu dni. Weryfikacja dwuetapowa świeże żądań usługi Azure AD, ale usług AD FS zwraca token z oryginalnego oświadczenia uwierzytelniania wieloskładnikowego i daty, a nie zostanie wykonana ponownie weryfikacji dwuetapowej. Ustawia tej reakcji poza pętlę weryfikacji między Azure AD i usług AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Należy pamiętać, Włącz uwierzytelnianie wieloskładnikowe

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze usługi Multi-Factor Authentication, zaznacz **ustawienia usługi**.
5. Na **ustawień usługi** w obszarze **Zarządzanie Pamiętaj uwierzytelnianie wieloskładnikowe**, wybierz pozycję **Zezwalaj użytkownikom na zapamiętywanie danych uwierzytelniania wieloskładnikowego na urządzeniach ufają**opcji.

   ![Należy pamiętać, uwierzytelniania wieloskładnikowego na zaufanych urządzeniach](./media/howto-mfa-mfasettings/remember.png)

6. Ustaw liczbę dni na zaufanych urządzeniach można pominąć weryfikacji dwuetapowej. Wartość domyślna to 14 dni.
7. Wybierz pozycję **Zapisz**.

### <a name="mark-a-device-as-trusted"></a>Oznacz jako zaufanego urządzenia

Po włączeniu funkcji uwierzytelniania wieloskładnikowego Zapamiętaj użytkowników można oznaczyć jako zaufane, gdy urządzenie zalogują się po wybraniu **nie pytaj ponownie**.

![Wybierz opcję "Nie pytaj ponownie" dla zaufanych urządzeń](./media/howto-mfa-mfasettings/trusted.png)

## <a name="selectable-verification-methods"></a>Metody wyboru weryfikacji

Można wybrać metody weryfikacji, które są dostępne dla użytkowników za pomocą _metody weryfikacji počítačů, které_ funkcji. Poniższa tabela zawiera krótki przegląd metod.

Gdy użytkownicy rejestrują swoje konta dla usługi Azure Multi-Factor Authentication, ich metodę weryfikacji preferowanego one wybierz opcje, które mają włączone. Wskazówki dotyczące procesu rejestracji użytkownika znajduje się w [Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md).

| Metoda | Opis |
|:--- |:--- |
| Połączenie z telefonem |Umieszcza wykonywane automatyczne połączenie głosowe. Użytkownik odbierze połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. Numer telefonu nie jest zsynchronizowany do lokalnej usługi Active Directory. |
| SMS na telefon |Wysyła wiadomość tekstową zawierającą kod weryfikacyjny. Użytkownik jest monitowany o wprowadzenie kodu weryfikacyjnego w interfejsie logowania. Ten proces jest nazywany jednokierunkowe programu SMS. Dwukierunkowe SMS oznacza, że użytkownik musi ponownie tekstu określonego kodu. Dwukierunkowe programu SMS jest przestarzały i nie jest obsługiwana po 14 listopada 2018. Użytkownicy, którzy są skonfigurowane do obsługi dwukierunkowe SMS są automatycznie przełączona na _połączenie numerem telefonu_ weryfikacji w tym czasie.|
| Powiadomienie przez aplikację mobilną |Wysyła powiadomienia wypychanego na telefonie lub zarejestrowanym urządzeniem. Użytkownik wyświetla powiadomienie i wybiera **Sprawdź** aby ukończyć weryfikację. Jest dostępna dla aplikacji Microsoft Authenticator [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), i [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Kod weryfikacyjny z aplikacji mobilnej |Aplikacja Microsoft Authenticator generuje nowy kod OATH weryfikacji co 30 sekund. Użytkownik wprowadza kod weryfikacyjny w interfejsie logowania. Jest dostępna dla aplikacji Microsoft Authenticator [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), i [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Włączanie i wyłączanie metody weryfikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
4. W obszarze usługi Multi-Factor Authentication, zaznacz **ustawienia usługi**.
5. Na **ustawień usługi** w obszarze **opcje weryfikacji**, wybrać/anulować wybór metody, aby zapewnić użytkownikom.

   ![Wybierz metodę weryfikacji](./media/howto-mfa-mfasettings/authmethods.png)

6. Kliknij pozycję **Zapisz**.

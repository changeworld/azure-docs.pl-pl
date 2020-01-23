---
title: Wykonywanie wdrożenia uwierzytelniania bez hasła przy użyciu usługi Azure AD
description: Wykonaj Azure Active Directory wdrożenie uwierzytelniania bezhasło
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8323333f378f95f0a640313524f198bdd00dc340
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512576"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Ukończ wdrażanie uwierzytelniania przy użyciu hasła

Większość cyberattacks rozpoczyna się od nazwy użytkownika i hasła skradzionego od kogoś w organizacji. 

Organizacje próbują wyeliminować zagrożenie, wymagając od użytkowników użycia:

- Długie hasła
- Złożone hasła
- Częste zmiany hasła
- Multi-Factor Authentication (MFA)

Badania firmy Microsoft [pokazują](https://aka.ms/passwordguidance) , że te wysiłki annoy użytkowników i zwiększą koszty pomocy technicznej. Aby uzyskać więcej informacji, zobacz artykuł [PA $ $Word nie ma znaczenia](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

Wdrażanie uwierzytelniania bezhaseł zapewnia następujące korzyści:

- Zwiększone zabezpieczenia. Zmniejszenie ryzyka związanego z phishingiem i atakami polegającymi na wyłudzaniu hasła przez usunięcie haseł jako obszaru ataku.
- Lepsze środowisko użytkownika. Zapewnij użytkownikom wygodny sposób uzyskiwania dostępu do danych z dowolnego miejsca i zapewnij łatwy dostęp do programu Outlook, usługi OneDrive, pakietu Office i nie tylko w przypadku urządzeń przenośnych.
- Niezawodne wgląd w szczegółowe dane. Uzyskaj wgląd w działanie bez hasła użytkowników z niezawodnym rejestrowaniem i inspekcją.

Hasło jest zamieniane na coś, co Ci się podoba. Na przykład w usłudze Windows Hello dla firm gest biometryczny, taki jak znak lub odcisk palca, lub numer PIN specyficzny dla urządzenia, który nie jest przesyłany przez sieć.

Firma Microsoft oferuje trzy opcje uwierzytelniania bezhasło, które obejmują wiele scenariuszy. Metody te mogą być używane wspólnie. 

- Funkcja [Windows Hello dla firm](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) jest Najlepsza dla użytkowników na ich dedykowanych komputerach z systemem Windows.
- Klucz zabezpieczeń Logowanie przy użyciu [kluczy zabezpieczeń FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) jest szczególnie przydatny w przypadku użytkowników logujących się na maszynach udostępnionych, takich jak kioski, sytuacje, w których korzystanie z telefonów jest ograniczone, oraz dla tożsamości o wysokim poziomie uprawnień.
- Logowanie za pomocą telefonu przy użyciu [aplikacji Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) jest przydatne w przypadku udostępniania użytkownikom urządzeń przenośnych opcji bez hasła. Powoduje to włączenie dowolnego telefonu z systemem iOS lub Android w celu uzyskania silnych poświadczeń bez hasła przez umożliwienie użytkownikom zalogowania się do dowolnej platformy lub przeglądarki. Użytkownicy logują się, uzyskując powiadomienie na telefonie, dopasowując liczbę wyświetlaną na ekranie na telefonie, a następnie używając danych biometrycznych lub numerów PIN do potwierdzenia.

## <a name="compare-passwordless-authentication-methods"></a>Porównanie metod uwierzytelniania bezhaseł

Metody uwierzytelniania bezhaseł firmy Microsoft umożliwiają korzystanie z różnych scenariuszy. Aby wybrać strategię uwierzytelniania bezhasło, należy wziąć pod uwagę potrzeby organizacyjne, wymagania wstępne i możliwości poszczególnych metod uwierzytelniania. Zalecamy, aby każda organizacja korzystająca z urządzeń z systemem Windows 10 korzystała z usługi Windows Hello dla firm. Następnie Dodaj do dodatkowych scenariuszy opcję logowania za pomocą telefonu (z aplikacją Microsoft Authenticator) lub kluczami zabezpieczeń.

### <a name="passwordless-authentication-scenarios"></a>Scenariusze uwierzytelniania bezhasło

| Scenariusz | Uwierzytelnianie na telefonie | Klucze zabezpieczeń | Windows Hello dla firm |
| --- | --- | --- | --- |
| **Logowanie do komputera**: <br> Z przypisanego urządzenia z systemem Windows 10 | **Nie** | **Tak** <br> Przy użyciu biometrycznych, PIN | **Tak**<br>z rozpoznawaniem biometrycznym i lub numerem PIN |
| **Logowanie do komputera**: <br> Z udostępnionego urządzenia z systemem Windows 10 | **Nie** | **Tak** <br> Przy użyciu biometrycznych, PIN  | **Nie** |
| **Logowanie do aplikacji sieci Web**: <br>z komputera dedykowanego przez użytkownika | **Tak** | **Tak** <br> Udostępnione Logowanie jednokrotne do aplikacji jest włączane przez logowanie komputera | **Tak**<br> Udostępnione Logowanie jednokrotne do aplikacji jest włączane przez logowanie komputera |
| **Logowanie do aplikacji sieci Web**: <br> z urządzenia przenośnego lub z systemem innym niż Windows | **Tak** | **Nie** | **Nie** |
| **Logowanie do komputera**: <br> Komputer z systemem innym niż Windows | **Nie** | **Nie** | **Nie** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Zagadnienia techniczne dotyczące aplikacji Microsoft Authenticator

**Integracja AD FS** — gdy użytkownik włączy Microsoft Authenticator poświadczenia bezhasła, uwierzytelnianie dla tego użytkownika jest domyślnie wysyłane powiadomienie o zatwierdzeniu. Użytkownicy w dzierżawie hybrydowej nie mogą być kierowani do usług AD FS w celu zalogowania się, chyba że wybierze opcję "Użyj hasła zamiast". Ten proces pomija również wszystkie lokalne zasady dostępu warunkowego i przepływy uwierzytelniania przekazywane. Jeśli jednak określono login_hint, użytkownik zostanie przekierowany do usług AD FS i pominięto opcję użycia poświadczeń bezhasła.

**Serwer usługi Azure MFA** — użytkownicy końcowi korzystający z lokalnego serwera usługi Azure MFA w organizacji mogą nadal tworzyć i używać jednego poświadczenia logowania jednokrotnego bez hasła. Jeśli użytkownik próbuje uaktualnić wiele instalacji (5 +) Microsoft Authenticator przy użyciu poświadczenia, ta zmiana może spowodować wystąpienie błędu.

**Rejestracja urządzenia** — aby użyć aplikacji Authenticator do uwierzytelniania bezserwerowego, urządzenie musi być zarejestrowane w dzierżawie usługi Azure AD i nie może być urządzeniem udostępnionym. Urządzenie można zarejestrować tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe jest obsługiwane w przypadku logowania za pomocą telefonu przy użyciu aplikacji uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wdrażania bezhaseł, organizacje muszą spełniać następujące wymagania wstępne.

| Warunek wstępny | Aplikacja Authenticator | FIDO2 klucze zabezpieczeń |
| --- | --- | --- |
| [Połączona Rejestracja w ramach usługi Azure MFA i samoobsługowego resetowania hasła (SSPR)](howto-registration-mfa-sspr-combined.md) jest włączona (funkcja w wersji zapoznawczej) | √ | √ |
| [Użytkownicy mogą korzystać z usługi Azure MFA](howto-mfa-getstarted.md) | √ | √ |
| [Użytkownicy zostali zarejestrowani do usługi Azure MFA i SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Użytkownicy zarejestrowali swoje urządzenia przenośne w usłudze Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 w wersji 1809 lub nowszej przy użyciu obsługiwanej przeglądarki, takiej jak Microsoft Edge lub Mozilla Firefox <br> (wersja 67 lub nowsza). <br> *Firma Microsoft zaleca korzystanie z wersji 1903 lub nowszej na potrzeby obsługi natywnej*. |   | √ |
| Zgodne FIDO2 klucze zabezpieczeń. Upewnij się, że korzystasz z urządzenia zabezpieczeń [przetestowanego przez firmę Microsoft i zweryfikowane](howto-authentication-passwordless-enable.md) urządzenie z systemem FIDO2. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Wymagania wstępne dotyczące usługi Windows Hello dla firm

Wymagania wstępne dotyczące usługi Windows Hello są wysoce zależne od tego, czy są wdrażane w konfiguracji lokalnej, hybrydowej czy w chmurze. Aby uzyskać więcej informacji, zobacz [pełną listę wymagań wstępnych dotyczących usługi Windows Hello dla firm](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Usługa Azure Multi-Factor Authentication

Użytkownicy rejestrują metodę bezhaseł w ramach przepływu rejestracji usługi Azure MFA. Uwierzytelnianie wieloskładnikowe przy użyciu nazwy użytkownika i hasła wraz z inną zarejestrowanej metody może być używane jako rezerwa w przypadku, gdy nie mogą korzystać z swojego telefonu ani klucza zabezpieczeń w niektórych scenariuszach.

### <a name="security-key-lifecycle"></a>Cykl życia klucza zabezpieczeń

Klucze zabezpieczeń umożliwiają dostęp do zasobów i należy zaplanować zarządzanie tymi urządzeniami fizycznymi.

1. Dystrybucja kluczy: Zaplanuj, w jaki sposób będziesz udostępniać klucze w organizacji. Może istnieć scentralizowany proces aprowizacji lub umożliwienie użytkownikom końcowym zakupu kluczy zgodnych z systemem FIDO 2,0.
1. Aktywacja klucza: użytkownicy końcowi muszą samoczynnie aktywować klucz zabezpieczeń. Użytkownicy końcowi rejestrują swoje klucze zabezpieczeń w [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) i włączają drugi współczynnik (PIN lub biometryczne) przy pierwszym użyciu.
1. Wyłączanie klucza: podczas gdy funkcje klucza zabezpieczeń są dostępne na etapie wersji zapoznawczej, nie ma możliwości, aby administrator usunął klucz z konta użytkownika. Użytkownik musi je usunąć. Jeśli klucz zostanie zgubiony lub skradziony:
   1. Usuń użytkownika z dowolnej grupy z włączoną obsługą uwierzytelniania bez hasła.
   1. Sprawdź, czy klucz został usunięty jako metoda uwierzytelniania.
   1. Wygeneruj nowy klucz.
1. Wymiana klucza: użytkownicy mogą jednocześnie włączyć dwa klucze zabezpieczeń. Podczas wymiany klucza zabezpieczeń upewnij się, że użytkownik usunął również klucz, który został zastąpiony.

### <a name="enable-windows-10-support"></a>Włącz obsługę systemu Windows 10

Włączenie logowania systemu Windows 10 przy użyciu kluczy zabezpieczeń FIDO2 wymaga włączenia funkcji dostawcy poświadczeń w systemie Windows 10. Włącz ją na jeden z dwóch sposobów:

- [Włączanie dostawcy poświadczeń przy użyciu usługi Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Zalecanym rozwiązaniem jest wdrożenie usługi Intune.
- [Włączanie dostawcy poświadczeń z pakietem aprowizacji](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Jeśli wdrożenie usługi Intune nie jest możliwe, Administratorzy muszą wdrożyć pakiet na każdej maszynie, aby włączyć funkcję dostawcy poświadczeń. Instalację pakietu można przeprowadzić za pomocą jednej z następujących opcji:
      - zasady grupy lub Configuration Manager
      - Instalacja lokalna na komputerze z systemem Windows 10

### <a name="register-security-keys"></a>Rejestrowanie kluczy zabezpieczeń

Użytkownicy muszą zarejestrować swój klucz zabezpieczeń na każdej Azure Active Directory przyłączonych do komputerów z systemem Windows 10.

Aby uzyskać więcej informacji, zobacz [Rejestrowanie użytkowników i zarządzanie kluczami zabezpieczeń FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).

### <a name="licensing-for-passwordless-authentication"></a>Licencjonowanie do uwierzytelniania bezhasła

Nie ma dodatkowych kosztów uwierzytelniania bez hasła, chociaż niektóre wymagania wstępne mogą wymagać subskrypcji Premium. Zobacz szczegółowe informacje dotyczące funkcji i licencjonowania na [stronie licencjonowania Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Opracowywanie planu

Należy wziąć pod uwagę potrzeby biznesowe i przypadki użycia dla każdej metody uwierzytelniania. Następnie wybierz metodę, która najlepiej odpowiada Twoim potrzebom.

### <a name="use-cases"></a>Przypadki zastosowań

W poniższej tabeli przedstawiono przypadki użycia do wdrożenia w ramach tego projektu.

| Obszar | Opis |
| --- | --- |
| **Dostęp** | Logowanie bez hasła jest dostępne z urządzenia firmowego lub osobistego w sieci firmowej lub poza nią. |
| **Inspekcja** | Dane użycia są dostępne dla administratorów w celu przeprowadzania inspekcji niemal w czasie rzeczywistym. <br> Dane użycia są pobierane do systemów firmowych co najmniej co 29 dni lub używane jest narzędzie SIEM. |
| **Zarządzanie** | Cykl życia przypisań użytkowników do odpowiedniej metody uwierzytelniania i skojarzonych grup jest zdefiniowany i monitorowany. |
| **Bezpieczeństwo** | Dostęp do odpowiedniej metody uwierzytelniania jest kontrolowany za pośrednictwem przypisań użytkowników i grup. <br> Tylko autoryzowani użytkownicy mogą korzystać z logowania bezhasłem. |
| **Wydajność** | Osie czasu propagacji przydziału dostępu są udokumentowane i monitorowane. <br> Czasy logowania są mierzone w celu ułatwienia użycia. |
| **Środowisko użytkownika** | Użytkownicy są świadomi zgodności urządzeń przenośnych. <br> Użytkownicy mogą konfigurować logowanie bezhasłem aplikacji uwierzytelniania. |
| **Pomoc techniczna** | Użytkownicy wiedzą, jak znaleźć pomoc techniczną w przypadku problemów z logowaniem bezhasłem. |

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

W przypadku niepowodzenia projektów technologicznych zazwyczaj wynika to z niezgodności zaoczekiwań, wyników i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że interesują](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) Cię odpowiednie osoby zainteresowane i że role udziałowców w projekcie są dobrze zrozumiałe.

### <a name="organization-communications"></a>Komunikacja organizacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Aktywnie Komunikuj się, w jaki sposób środowisko użytkownika zostanie zmienione, gdy zostanie zmienione, i jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

Komunikacja z użytkownikami końcowymi będzie musiała obejmować:

- [Włączanie korzystania ze połączonej rejestracji zabezpieczeń](howto-authentication-passwordless-phone.md)
- [Pobieranie aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Rejestrowanie w aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Logowanie za pomocą telefonu](../user-help/user-help-auth-app-sign-in.md)

Firma Microsoft udostępnia [Szablony komunikacji](https://aka.ms/mfatemplates)MFA, [Szablony komunikacji](https://www.microsoft.com/download/details.aspx?id=56768)samoobsługowego resetowania haseł (SSPR) oraz [dokumentację użytkowników końcowych](../user-help/security-info-setup-signin.md) , która ułatwia przygotowanie komunikacji. Możesz wysyłać użytkowników, aby [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) rejestrować się bezpośrednio, wybierając linki do informacji zabezpieczających na tej stronie.

### <a name="testing-passwordless"></a>Testowanie bezhasła

Na każdym etapie wdrożenia upewnij się, że testy zostały przeprowadzone zgodnie z oczekiwaniami.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testowanie aplikacji Microsoft Authenticator

Poniżej przedstawiono przykładowe przypadki testowe dotyczące uwierzytelniania bezhasła przy użyciu aplikacji Microsoft Authenticator

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować aplikację Microsoft Authenticator | Użytkownik może zarejestrować aplikację z aka.ms/mysecurityinfo |
| Użytkownik może włączyć logowanie za telefonem | Logowanie do konta służbowego zostało skonfigurowane |
| Użytkownik może uzyskać dostęp do aplikacji przy użyciu logowania za pomocą telefonu | Użytkownik przechodzi przez konto służbowe w usłudze Flow i osiągnie wydaną aplikację. |
| Testowanie wycofywania logowania za pomocą telefonu przez wyłączenie Microsoft Authenticator logowania bezterminowego na ekranie metod uwierzytelniania w portalu Azure Active Directory | Wcześniej włączeni użytkownicy nie mogą korzystać z logowania bezhasłem z Microsoft Authenticator. |
| Usuwanie logowania za pomocą telefonu z aplikacji Microsoft Authenticator | Konto służbowe nie jest już dostępne na Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testowanie kluczy zabezpieczeń

Poniżej przedstawiono przykładowe przypadki testowe dotyczące uwierzytelniania bez hasła z kluczami zabezpieczeń.

**Logowanie do FIDO urządzeń Azure Active Directory z systemem Windows 10 z dołączonymi hasłami**

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować urządzenie FIDO2 (1809) | Użytkownik może zarejestrować urządzenie FIDO2 przy użyciu usługi at Settings > konta > Opcje logowania > klucz zabezpieczeń |
| Użytkownik może zresetować urządzenie FIDO2 (1809) | Użytkownik może zresetować urządzenie FIDO2 za pomocą oprogramowania producenta |
| Użytkownik może zalogować się przy użyciu urządzenia FIDO2 (1809) | Użytkownik może wybrać klucz zabezpieczeń z okna logowania i pomyślnie się zalogować. |
| Użytkownik może zarejestrować urządzenie FIDO2 (1903) | Użytkownik może zarejestrować urządzenie FIDO2 w ustawieniach > kont > Opcje logowania > klucz zabezpieczeń |
| Użytkownik może zresetować urządzenie FIDO2 (1903) | Użytkownik może zresetować urządzenie FIDO2 w ustawieniach > kont > Opcje logowania > klucz zabezpieczeń |
| Użytkownik może zalogować się przy użyciu urządzenia FIDO2 (1903) | Użytkownik może wybrać klucz zabezpieczeń z okna logowania i pomyślnie się zalogować. |

**Logowanie w usłudze Azure AD Web Apps bezFIDOowego hasła**

| Scenariusz | Oczekiwane wyniki |
| --- | --- |
| Użytkownik może zarejestrować urządzenie FIDO2 w usłudze aka.ms/mysecurityinfo przy użyciu przeglądarki Microsoft Edge | Rejestracja powinna zakończyć się powodzeniem |
| Użytkownik może zarejestrować urządzenie FIDO2 w usłudze aka.ms/mysecurityinfo przy użyciu przeglądarki Firefox | Rejestracja powinna zakończyć się powodzeniem |
| Użytkownik może zalogować się do usługi OneDrive online przy użyciu urządzenia FIDO2 przy użyciu przeglądarki Microsoft Edge | Logowanie powinno zakończyć się pomyślnie |
| Użytkownik może zalogować się do usługi OneDrive online przy użyciu urządzenia FIDO2 przy użyciu przeglądarki Firefox | Logowanie powinno zakończyć się pomyślnie |
| Testowanie wycofywania rejestracji urządzeń FIDO2 przez wyłączenie kluczy zabezpieczeń FIDO2 w bloku metody uwierzytelniania w portalu Azure Active Directory | Użytkownicy otrzymają monit o zalogowanie się przy użyciu klucza zabezpieczeń, pomyślnie zarejestrowali je w usłudze i zostanie wyświetlony komunikat o błędzie: "zasady firmy wymagają użycia innej metody do zalogowania się". Użytkownicy powinni wtedy móc wybrać inną metodę i pomyślnie zalogować się. Zamknij okno i zaloguj się ponownie, aby sprawdzić, czy nie widzi tego samego komunikatu o błędzie. |

### <a name="auditing-passwordless"></a>Inspekcja bezhasła

Usługa Azure AD zawiera raporty, które udostępniają szczegółowe informacje techniczne i biznesowe. Czy właściciele aplikacji firmowych i technicznych zakładają własność i wykorzystują te raporty zgodnie z wymaganiami organizacji.

Sekcja metody uwierzytelniania w portalu Azure Active Directory jest miejscem, w którym Administratorzy mogą włączać i zarządzać ustawieniami poświadczeń bezterminowych.

Usługa Azure AD dodaje wpisy do dzienników inspekcji, gdy:

- Administrator wprowadza zmiany w sekcji metody uwierzytelniania.
- Użytkownik dokonuje dowolnego rodzaju zmiany poświadczeń w Azure Active Directory.

W poniższej tabeli przedstawiono kilka przykładów typowych scenariuszy raportowania.

|   | Zarządzanie ryzykiem | Zwiększenie produktywności | Zarządzanie i zgodność |
| --- | --- | --- | --- |
| **Typy raportów** | Metody uwierzytelniania — Użytkownicy zarejestrowani do rejestracji zabezpieczeń połączonych | Metody uwierzytelniania — Użytkownicy zarejestrowani do powiadomień aplikacji | Logowania: Sprawdzanie, kto uzyskuje dostęp do dzierżawy i jak |
| **Potencjalni akcje** | Użytkownicy docelowi nie są jeszcze zarejestrowani | Wdrażanie stacji Microsoft Authenticator aplikacji lub kluczy zabezpieczeń | Odwoływanie dostępu lub wymuszanie dodatkowych zasad zabezpieczeń dla administratorów |

**Usługa Azure AD zachowuje większość danych inspekcji przez 30 dni** i udostępnia dane za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API w celu pobrania ich do systemów analizy. Jeśli Twoja organizacja wymaga dłuższego przechowywania, dzienniki muszą zostać wyeksportowane i zużyte do narzędzia SIEM, takiego jak [Azure](../../sentinel/connect-azure-active-directory.md), Splunk lub Sumo Logic. [Dowiedz się więcej o wyświetlaniu raportów dotyczących dostępu i użycia](../reports-monitoring/overview-reports.md).

Użytkownicy mogą rejestrować swoje poświadczenia i zarządzać nimi, przechodząc do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Ten link kieruje użytkowników do środowiska zarządzania poświadczeniami użytkowników końcowych, które zostały włączone za pośrednictwem połączonego środowiska rejestracji SSPR/MFA. Każda rejestracja urządzeń zabezpieczeń FIDO2 lub zmian metod uwierzytelniania przez użytkownika zostanie zarejestrowana w dziennikach inspekcji Azure Active Directory.

Gdy użytkownicy włączają lub wyłączają konto w kluczu zabezpieczeń lub zresetują drugi współczynnik dla klucza zabezpieczeń na komputerach z systemem Windows 10, do dziennika zabezpieczeń zostaje dodany wpis o następujących identyfikatorach zdarzeń: 4670, 5382.

### <a name="plan-for-rollback"></a>Planowanie wycofywania

Chociaż uwierzytelnianie bezhasła jest lekkim elementem z minimalnym wpływem na użytkowników końcowych, może być konieczne wycofanie.

Wycofanie wymaga, aby administrator zalogować się do portalu Azure Active Directory, wybrać odpowiednie metody silnego uwierzytelniania i zmienić opcję Włącz na wartość "nie". Ten proces spowoduje wyłączenie funkcji bezhasło dla wszystkich użytkowników.

Użytkownicy, którzy już zarejestrowali urządzenia zabezpieczeń FIDO2, otrzymają monit o użycie urządzenia zabezpieczeń podczas następnego logowania, a następnie zobaczy następujący błąd:

![Wybierz inny sposób logowania](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Planowanie pilotażu

W przypadku wdrażania uwierzytelniania bezhaseł należy najpierw włączyć co najmniej jedną grupę pilotażową. Do tego celu można [utworzyć grupy](../fundamentals/active-directory-groups-create-azure-portal.md) . Dodaj użytkowników, którzy będą uczestniczyć w pilotażu do grup. Następnie Włącz nowe metody uwierzytelniania bezhasło dla wybranych grup.

Grupy można synchronizować z katalogu lokalnego lub z usługi Azure AD. Gdy będziesz zadowolony z wyników pilotażu, możesz przełączyć się na uwierzytelnianie bez hasła dla wszystkich użytkowników.

Zapoznaj [się z najlepszymi rozwiązaniami dla pilotażu](https://aka.ms/deploymentplans) na stronie plany wdrożenia.

## <a name="deploy-passwordless-authentication"></a>Wdróż uwierzytelnianie bezhasła

Wykonaj kroki wyrównane do wybranej metody poniżej.

### <a name="required-administrative-roles"></a>Wymagane role administracyjne

| Rola usługi Azure AD | Opis |
| --- | --- |
| Administrator uwierzytelniania | Najmniejsza rola uprzywilejowana może implementować metody uwierzytelniania i zarządzać nimi |
| Użytkownik | Najmniej uprzywilejowana rola w celu skonfigurowania aplikacji uwierzytelniania na urządzeniu lub zarejestrowania urządzenia z kluczem zabezpieczeń na potrzeby logowania do sieci Web lub systemu Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Wdróż logowanie za pomocą telefonu przy użyciu aplikacji Microsoft Authenticator

Wykonaj kroki opisane w artykule, [Włącz logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md) , aby włączyć aplikację Microsoft Authenticator jako metodę uwierzytelniania bez hasła w organizacji.

### <a name="deploy-fido2-security-key-sign-in"></a>Wdróż klucz zabezpieczeń FIDO2

Wykonaj kroki opisane w artykule, aby [włączyć klucz zabezpieczeń bezhasło dla usługi Azure AD](howto-authentication-passwordless-security-key.md) , aby włączyć klucze zabezpieczeń FIDO2 jako metody uwierzytelniania bezhasło w organizacji.

### <a name="troubleshoot-phone-sign-in"></a>Rozwiązywanie problemów z logowaniem do telefonu

| Scenariusz | Rozwiązanie |
| --- | --- |
| Użytkownik nie może wykonać rejestracji połączonej | Upewnij się, że [rejestracja łączona](concept-registration-mfa-sspr-combined.md) jest włączona. |
| Użytkownik nie może włączyć aplikacji uwierzytelniania przy użyciu telefonu | Upewnij się, że użytkownik znajduje się w zakresie wdrożenia |
| Użytkownik nie należy do zakresu uwierzytelniania bez hasła, ale jest przedstawiany przy użyciu opcji logowania bez hasła, która nie może zostać ukończona. | Ten scenariusz występuje, gdy użytkownik włączył logowanie za pomocą telefonu n aplikacji przed utworzeniem zasad. <br> Aby włączyć logowanie: należy dodać użytkownika do zakresu użytkowników z włączonym logowaniem bezhasłem. <br> Aby zablokować Logowanie: użytkownik powinien usunąć swój formularz poświadczeń z tej aplikacji. |

### <a name="troubleshoot-security-key-sign-in"></a>Rozwiązywanie problemów z logowaniem klucza zabezpieczeń

| Scenariusz | Rozwiązanie |
| --- | --- |
| Użytkownik nie może wykonać rejestracji połączonej | Upewnij się, że [rejestracja łączona](concept-registration-mfa-sspr-combined.md) jest włączona. |
| Użytkownik nie może dodać klucza zabezpieczeń w [ustawieniach zabezpieczeń](https://aka.ms/mysecurityinfo) | Upewnij się, że [klucze zabezpieczeń](howto-authentication-passwordless-security-key.md) są włączone. |
| Użytkownik nie może dodać klucza zabezpieczeń w opcjach logowania systemu Windows 10 | [Upewnij się, że klucze zabezpieczeń logowania systemu Windows](howto-authentication-passwordless-enable.md) |
| **Komunikat o błędzie**: wykryto, że ta przeglądarka lub system operacyjny nie obsługuje kluczy zabezpieczeń FIDO2. | Urządzenia zabezpieczeń FIDO2 bezhasło mogą być rejestrowane tylko w obsługiwanych przeglądarkach (Microsoft Edge, Firefox w wersji 67) w systemie Windows 10 w wersji 1809 lub nowszej. |
| **Komunikat o błędzie**: zasady firmy wymagają, aby można było zalogować się przy użyciu innej metody. | Brak pewności, że klucze zabezpieczeń są włączone w dzierżawie. |
| Użytkownik nie może zarządzać moim kluczem zabezpieczeń w systemie Windows 10 w wersji 1809 | Wersja 1809 wymaga użycia oprogramowania do zarządzania kluczami zabezpieczeń dostarczonym przez dostawcę klucza FIDO2. Skontaktuj się z dostawcą, aby uzyskać pomoc techniczną. |
| Myślę, że klucz zabezpieczeń FIDO2 może być wadliwy — jak można go przetestować | Przejdź do [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), wprowadź poświadczenia dla konta testowego, podłącz podejrzany klucz zabezpieczeń, kliknij przycisk "+" w prawym górnym rogu ekranu, kliknij polecenie Utwórz i przejdź przez proces tworzenia. Jeśli ten scenariusz nie powiedzie się, urządzenie może być uszkodzone. |

## <a name="next-steps"></a>Następne kroki

- [Włącz klucze zabezpieczeń bezhasło do logowania się w usłudze Azure AD](howto-authentication-passwordless-security-key.md)
- [Włączanie logowania bez hasła przy użyciu aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Dowiedz się więcej na temat metod uwierzytelniania użycie & szczegółowych informacji](howto-authentication-methods-usage-insights.md)

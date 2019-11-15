---
title: Plan wdrożenia samoobsługowego resetowania hasła — Azure Active Directory
description: Strategia pomyślnej implementacji resetowania hasła w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b648d6f914b5e3004ea3b62019bbec33e5a4871d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081519"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Wdrażanie samoobsługowego resetowania haseł w usłudze Azure AD

> [!NOTE]
> W tym przewodniku wyjaśniono sposób samoobsługowego resetowania hasła i sposobu jego wdrożenia. Jeśli szukasz narzędzia do samoobsługowego resetowania hasła, aby wrócić do swojego konta, przejdź do obszaru [https://aka.ms/sspr](https://aka.ms/sspr). 

Samoobsługowe resetowanie hasła (SSPR) to funkcja Azure Active Directory, która umożliwia pracownikom Resetowanie swoich haseł bez konieczności kontaktowania się z pracownikami działu IT. Przed skorzystaniem z usługi pracownicy muszą zarejestrować się w usłudze lub zostać zarejestrowany do samoobsługowego resetowania hasła. Podczas rejestracji pracownik wybiera jedną lub więcej metod uwierzytelniania włączonych przez ich organizację.

SSPR umożliwia pracownikom szybkie uzyskanie odblokowania i kontynuowanie pracy niezależnie od miejsca, w którym są lub pory dnia. Dzięki umożliwieniu użytkownikom samodzielnego odblokowania, organizacja może ograniczyć czas nieproduktywny i wysokie koszty obsługi typowych problemów związanych z hasłami.

Pomóż użytkownikom szybko rejestrować się, wdrażając SSPR obok innej aplikacji lub usługi w organizacji. Ta akcja spowoduje wygenerowanie dużej ilości logowań i umożliwi rejestrację dysku.

Przed wdrożeniem SSPR organizacje mogą chcieć określić liczbę wywołań pomocy technicznej związanych z resetowaniem haseł w czasie i średni koszt każdego wywołania. Mogą one używać tego wdrożenia danych, aby pokazać, że wartość SSPR jest dołączana do organizacji.  

## <a name="how-sspr-works"></a>Jak działa SSPR

1. Gdy użytkownik spróbuje zresetować hasło, musi sprawdzić, czy wcześniej zarejestrowano metodę lub metody uwierzytelniania, aby potwierdzić swoją tożsamość.
1. Następnie użytkownik wprowadzi nowe hasło.
   1. W przypadku użytkowników korzystających tylko z chmury nowe hasło jest przechowywane w Azure Active Directory. Aby uzyskać więcej informacji, zobacz artykuł [jak działa SSPR](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. W przypadku użytkowników hybrydowych hasło jest zapisywane z powrotem do Active Directory lokalnego za pośrednictwem usługi Azure AD Connect. Aby uzyskać więcej informacji, zobacz artykuł [co to jest zapisywanie zwrotne haseł](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Zagadnienia dotyczące licencjonowania

Azure Active Directory jest licencjonowana przez użytkownika, co oznacza, że każdy użytkownik musi mieć odpowiednią licencję dla funkcji, których używają.

Więcej informacji na temat licencjonowania można znaleźć na [stronie cennika Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Włącz rejestrację połączoną dla SSPR i usługi MFA

Firma Microsoft zaleca, aby organizacje umożliwiały łączenie się ze sobą przy użyciu usługi SSPR i uwierzytelniania wieloskładnikowego. Po włączeniu tego połączonego środowiska rejestracji użytkownicy będą musieli tylko wybrać informacje o rejestracji, aby włączyć obie funkcje.

![Rejestracja informacji o zabezpieczeniach](./media/howto-sspr-deployment/combined-security-info.png)

Połączone środowisko rejestracji nie wymaga, aby organizacje mogły korzystać z usług SSPR i Azure Multi-Factor Authentication. Połączone środowisko rejestracji zapewnia organizacjom lepszy komfort pracy użytkowników w porównaniu z tradycyjnymi indywidualnymi składnikami. Więcej informacji o połączeniu z rejestracją i sposobach ich włączania znajduje się w artykule dotyczącego [łączenia informacji o zabezpieczeniach (wersja zapoznawcza).](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Planowanie konfiguracji

Następujące ustawienia są wymagane do włączenia SSPR oraz zalecanych wartości.

| Obszar | Ustawienie | Wartość |
| --- | --- | --- |
| **Właściwości SSPR** | Włączono Samoobsługowe resetowanie hasła | **Wybrana** Grupa dla pilotażu/ **wszystko** dla środowiska produkcyjnego |
| **Metody uwierzytelniania** | Metody uwierzytelniania wymagane do zarejestrowania | Zawsze 1 więcej niż wymagane do zresetowania |
|   | Metody uwierzytelniania wymagane do zresetowania | Jeden lub dwa |
| **Rejestracja** | Czy wymagać od użytkowników rejestrowania się podczas logowania? | Yes |
|   | Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania | 90 – 180 dni |
| **Powiadomienia** | Czy powiadamiać użytkowników o resetowaniu hasła? | Yes |
|   | Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło? | Yes |
| **Customization** | Dostosuj link do pomocy technicznej | Yes |
|   | Niestandardowa wiadomość e-mail lub adres URL pomocy technicznej | Witryna lub adres e-mail pomocy technicznej |
| **Integracja lokalna** | Zapisuj hasła do lokalnej usługi AD | Yes |
|   | Zezwalaj użytkownikom na Odblokowywanie konta bez resetowania hasła | Yes |

### <a name="sspr-properties-recommendations"></a>Zalecenia dotyczące właściwości SSPR

Podczas włączania funkcji samoobsługowego resetowania hasła wybierz grupę zabezpieczeń, która ma być używana podczas pilotażu.

Planując uruchomienie usługi w szerszym stopniu, zalecamy użycie opcji all w celu wymuszenia SSPR dla wszystkich użytkowników w organizacji. Jeśli nie można ustawić opcji na wszystkie, wybierz odpowiednią grupę zabezpieczeń usługi Azure AD lub grupę usługi AD zsynchronizowaną z usługą Azure AD.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Ustaw metody uwierzytelniania wymagane do zarejestrowania na co najmniej jednej liczbie wymaganej do zresetowania. Umożliwienie wielu użytkownikom swobody ich resetowania.

Ustaw **liczbę metod wymaganych do zresetowania** do poziomu właściwego dla Twojej organizacji. Jeden z nich wymaga najmniejszego tarcia, a dwa mogą zwiększyć stan zabezpieczeń.

Zobacz, [co to są metody uwierzytelniania](concept-authentication-methods.md) , aby uzyskać szczegółowe informacje na temat metod uwierzytelniania dostępnych dla SSPR, wstępnie zdefiniowanych pytań zabezpieczających i sposobu tworzenia niestandardowych pytań zabezpieczających.

### <a name="registration-settings"></a>Ustawienia rejestracji

Ustaw opcję **Wymagaj, aby użytkownicy rejestrowali się podczas logowania** do programu **tak**. To ustawienie oznacza, że użytkownicy są zmuszeni do zarejestrowania się podczas logowania, co gwarantuje, że wszyscy użytkownicy są chronieni.

Ustaw **liczbę dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji o uwierzytelnianiu** do zakresu od **90** do **180** dni, chyba że organizacja ma potrzebę w krótszym czasie.

### <a name="notifications-settings"></a>Ustawienia powiadomień

Skonfiguruj zarówno opcję **Powiadamiaj użytkowników o** resetowaniu hasła, jak i **Powiadom wszystkich administratorów, gdy inni administratorzy zresetują swoje hasła** na **wartość tak**. Wybranie opcji **tak** powoduje zwiększenie bezpieczeństwa dzięki zapewnieniu, że użytkownicy wiedzą, kiedy hasło zostało zresetowane, i że wszyscy administratorzy są świadomi hasła. Jeśli użytkownicy lub Administratorzy otrzymają takie powiadomienie i nie zainicjowano zmiany, mogą natychmiast zgłosić potencjalne naruszenie zabezpieczeń.

### <a name="customization"></a>Dostosowywanie

**W celu zapewnienia** użytkownikom, którzy napotykają problemy, można szybko uzyskać pomoc. Ustaw tę opcję na wspólny adres e-mail pomocy technicznej lub stronę internetową, z którą znają użytkownicy.

### <a name="on-premises-integration"></a>Integracja lokalna

Jeśli masz środowisko hybrydowe, upewnij się, że **zapis z powrotem hasła do lokalnej usługi AD** ma wartość **tak**. Ustaw również opcję Zezwól użytkownikom na Odblokowywanie konta bez resetowania hasła do wartości tak, ponieważ zapewnia to większą elastyczność.

### <a name="changingresetting-passwords-of-administrators"></a>Zmiana/resetowanie haseł administratorów

Konta administratorów są kontami specjalnymi z podniesionymi uprawnieniami. Aby je zabezpieczyć, należy zastosować następujące ograniczenia dotyczące zmieniania haseł administratorów:

- Lokalni administratorzy przedsiębiorstwa lub Administratorzy domeny nie mogą resetować hasła za poorednictwem SSPR. Mogą jedynie zmienić swoje hasło w środowisku lokalnym. Dlatego nie zaleca się synchronizowania premium kont administratorów usługi AD z usługą Azure AD.
- Administrator nie może użyć tajnych pytań & odpowiedzi jako metody resetowania hasła.

### <a name="environments-with-multiple-identity-management-systems"></a>Środowiska z wieloma systemami zarządzania tożsamościami

Jeśli w środowisku istnieje wiele systemów zarządzania tożsamościami, takich jak lokalne Menedżery tożsamości, takie jak Oracle AM, SiteMinder lub inne systemy, hasła wpisywane do Active Directory mogą wymagać synchronizacji z innymi systemami przy użyciu narzędzia, takiego jak Usługa powiadamiania o zmianie hasła (PCNS) z Microsoft Identity Manager (MIM). Aby uzyskać informacje na temat bardziej złożonej scenariusza, zobacz artykuł [wdrażanie usługi powiadamiania o zmianie hasła w programie MIM na kontrolerze domeny](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Planowanie wdrażania i obsługi SSPR

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnością oczekiwań, rezultatów i obowiązków. Aby uniknąć tych pułapek, należy się upewnić, że angażuje się w odpowiednie osoby zainteresowane i że role udziałowców w projekcie są zrozumiałe w celu udokumentowania uczestników projektu oraz ich wprowadzania i odpowiedzialności.

### <a name="communications-plan"></a>Plan komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Aktywnie Komunikuj się z użytkownikami, jak korzystać z usługi i co można zrobić, aby uzyskać pomoc, jeśli coś nie działa zgodnie z oczekiwaniami. Zapoznaj się z [materiałami wdrożeniowymi samoobsługowego resetowania hasła w centrum pobierania Microsoft,](https://www.microsoft.com/download/details.aspx?id=56768) aby zapoznać się z pomysłami dotyczącymi planowania strategii komunikacji użytkowników końcowych.

### <a name="testing-plan"></a>Plan testowania

Aby upewnić się, że wdrożenie działa zgodnie z oczekiwaniami, należy zaplanować zestaw przypadków testowych, które będą używane do sprawdzania poprawności implementacji. Poniższa tabela zawiera kilka przydatnych scenariuszy testowych, których można użyć do udokumentowania oczekiwanych wyników organizacji na podstawie zasad.

| Analiza biznesowa | Oczekiwany wynik |
| --- | --- |
| Portal SSPR jest dostępny z poziomu sieci firmowej | Określone przez organizację |
| Portal SSPR jest dostępny spoza sieci firmowej | Określone przez organizację |
| Zresetuj hasło użytkownika z przeglądarki, gdy użytkownik nie ma włączonej obsługi resetowania hasła | Użytkownik nie może uzyskać dostępu do przepływu resetowania hasła |
| Zresetuj hasło użytkownika z przeglądarki, gdy użytkownik nie jest zarejestrowany do resetowania hasła | Użytkownik nie może uzyskać dostępu do przepływu resetowania hasła |
| Użytkownik loguje się, gdy wymuszana jest rejestracja resetowania hasła | Użytkownik jest monitowany o zarejestrowanie informacji o zabezpieczeniach |
| Użytkownik loguje się, gdy rejestracja resetowania haseł została zakończona | Użytkownik nie jest monitowany o zarejestrowanie informacji o zabezpieczeniach |
| Portal SSPR jest dostępny, gdy użytkownik nie ma licencji | Jest dostępny |
| Zresetuj hasło użytkownika z ekranu blokady urządzenia z systemem Windows 10 AADJ lub H + AADJ po zarejestrowaniu użytkownika | Użytkownik może zresetować hasło |
| SSPR dane rejestracji i użycia są dostępne dla administratorów w czasie niemal rzeczywistym | Jest dostępny za pośrednictwem dzienników inspekcji |

### <a name="support-plan"></a>Plan pomocy technicznej

Mimo że SSPR nie tworzy żadnych problemów użytkowników, ważne jest, aby personel pomocy technicznej mógł zaradzić sobie z problemami, które mogą wystąpić.

Administrator może zmienić lub zresetować hasło dla użytkowników końcowych za pośrednictwem portalu usługi Azure AD, dlatego lepiej jest rozwiązać ten problem za pośrednictwem samoobsługowego procesu obsługi.

W sekcji Przewodnik operacyjny tego dokumentu Utwórz listę przypadków pomocy technicznej i ich ewentualne przyczyny i Utwórz Przewodnik do rozwiązywania problemów.

### <a name="auditing-and-reporting"></a>Inspekcja i raportowanie

Po wdrożeniu wiele organizacji chce wiedzieć, jak i w przypadku użycia funkcji samoobsługowego resetowania hasła (SSPR). Funkcja raportowania, która Azure Active Directory (Azure AD) zapewnia odpowiedzi na pytania przy użyciu prekompilowanych raportów.

Dzienniki inspekcji na potrzeby rejestracji i resetowania haseł są dostępne przez 30 dni. W związku z tym, jeśli Inspekcja zabezpieczeń w ramach firmy wymaga dłuższego przechowywania, dzienniki muszą zostać wyeksportowane i zużyte do narzędzia SIEM, takiego jak [Azure](../../sentinel/connect-azure-active-directory.md), Splunk lub ArcSight.

W tabeli, podobnej do przedstawionej poniżej, udokumentowanie harmonogramu tworzenia kopii zapasowych, systemu i stron odpowiedzialnych. Może nie być konieczne oddzielne inspekcje i raportowanie kopii zapasowych, ale należy mieć oddzielną kopię zapasową, z której można odzyskać problemy.

|   | Częstotliwość pobierania | System docelowy | Osoba odpowiedzialna |
| --- | --- | --- | --- |
| Inspekcja kopii zapasowej |   |   |   |
| Tworzenie kopii zapasowej |   |   |   |
| Kopia zapasowa odzyskiwania po awarii |   |   |   |

## <a name="implementation"></a>Wdrażanie

Implementacja występuje w trzech etapach:

- Konfigurowanie użytkowników i licencji
- Konfigurowanie usługi Azure AD SSPR na potrzeby rejestracji i samoobsługi
- Konfigurowanie Azure AD Connect do zapisywania zwrotnego haseł

### <a name="communicate-the-change"></a>Przekaż zmianę

Rozpocznij implementację planu komunikacji opracowanego w fazie planowania.

### <a name="ensure-groups-are-created-and-populated"></a>Upewnij się, że grupy zostały utworzone i wypełnione

Należy zapoznać się z sekcją Planowanie metod uwierzytelniania hasła i upewnić się, że grupy dla implementacji pilotażowej lub produkcyjnej są dostępne, a wszyscy użytkownicy są dodawani do grup.

### <a name="apply-licenses"></a>Zastosuj licencje

Grupy, które mają zostać zaimplementowane, muszą mieć przypisaną licencję usługi Azure AD Premium. Licencje można przypisywać bezpośrednio do grupy lub korzystać z istniejących zasad licencji, takich jak środowisko PowerShell lub Licencjonowanie oparte na grupach.

Informacje o przypisywaniu licencji do grup użytkowników można znaleźć w artykule, [przypisywać licencje użytkownikom według członkostwa w grupie w Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Konfigurowanie SSPR

#### <a name="enable-groups-for-sspr"></a>Włącz grupy dla SSPR

1. Uzyskaj dostęp do Azure Portal przy użyciu konta administratora.
1. Wybierz pozycję Wszystkie usługi, a następnie w polu Filtr wpisz Azure Active Directory, a następnie wybierz pozycję Azure Active Directory.
1. W bloku Active Directory wybierz pozycję Resetowanie hasła.
1. W okienku właściwości wybierz pozycję wybrane. Jeśli chcesz, aby wszyscy użytkownicy włączyli, zaznacz opcję Wszystkie.
1. W bloku domyślne zasady resetowania hasła wpisz nazwę pierwszej grupy, zaznacz ją, a następnie kliknij przycisk Wybierz w dolnej części ekranu, a następnie wybierz pozycję Zapisz w górnej części ekranu.
1. Powtórz ten proces dla każdej grupy.

#### <a name="configure-the-authentication-methods"></a>Konfigurowanie metod uwierzytelniania

Zaplanuj się z sekcją Planowanie przy użyciu metod uwierzytelniania haseł tego dokumentu.

1. Wybierz pozycję rejestracja, w obszarze Wymagaj od użytkownika zarejestrowania się podczas logowania wybierz pozycję tak, a następnie ustaw liczbę dni przed wygaśnięciem, a następnie wybierz pozycję Zapisz.
1. Wybierz pozycję powiadomienie i skonfiguruj zgodnie z planem, a następnie wybierz pozycję Zapisz.
1. Wybierz pozycję Dostosowywanie i skonfiguruj zgodnie z planem, a następnie wybierz pozycję Zapisz.
1. Wybierz pozycję integracja lokalna i skonfiguruj zgodnie z planem, a następnie wybierz pozycję Zapisz.

### <a name="enable-sspr-in-windows"></a>Włącz SSPR w systemie Windows

Urządzenia z systemem Windows 10 w wersji 1803 lub nowszej, które są przyłączone do usługi Azure AD lub dołączone do hybrydowej usługi Azure AD, mogą resetować swoje hasła na ekranie logowania systemu Windows. Informacje i kroki konfigurowania tej funkcji można znaleźć w artykule [Resetowanie hasła usługi Azure AD z ekranu logowania](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Konfigurowanie zapisywania zwrotnego haseł

Procedurę konfigurowania funkcji zapisywania zwrotnego haseł dla organizacji można znaleźć w artykule [How to: Configure Password zapisywania zwrotnego](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Zarządzanie SSPR

Role wymagane do zarządzania funkcjami związanymi z funkcją samoobsługowego resetowania hasła.

| Rola biznesowa/osoba | Rola usługi Azure AD (w razie potrzeby) |
| :---: | :---: |
| Pomoc techniczna na poziomie 1 | Administrator haseł |
| Pomoc techniczna poziomu 2 | Administrator użytkowników |
| SSPR administrator | Administrator globalny |

### <a name="support-scenarios"></a>Scenariusze pomocy technicznej

Aby umożliwić sukces zespołu pomocy technicznej, możesz utworzyć często zadawane pytania na podstawie pytań otrzymywanych od użytkowników. Poniższa tabela zawiera typowe scenariusze obsługi.

| Scenariusze | Opis |
| --- | --- |
| Użytkownik nie ma dostępnych żadnych zarejestrowanych metod uwierzytelniania | Użytkownik próbuje zresetować swoje hasło, ale nie ma żadnej z metod uwierzytelniania, które zostały zarejestrowane (przykład: pozostawiły swój telefon komórkowy w domu i nie może uzyskać dostępu do poczty e-mail) |
| Użytkownik nie otrzymuje tekstu ani nie dzwoni do swojego biura lub telefonu komórkowego | Użytkownik próbuje zweryfikować swoją tożsamość za pośrednictwem tekstu lub wywołania, ale nie otrzymuje tekstu/wywołania. |
| Użytkownik nie może uzyskać dostępu do portalu resetowania haseł | Użytkownik chce zresetować swoje hasło, ale nie jest włączony do resetowania hasła i w związku z tym nie może uzyskać dostępu do strony w celu zaktualizowania haseł. |
| Użytkownik nie może ustawić nowego hasła | Użytkownik kończy weryfikację podczas przepływu resetowania hasła, ale nie może ustawić nowego hasła. |
| Użytkownik nie widzi linku resetowania hasła na urządzeniu z systemem Windows 10 | Użytkownik próbuje zresetować hasło na ekranie blokady systemu Windows 10, ale urządzenie nie jest przyłączone do usługi Azure AD lub nie jest włączone zasady dotyczące urządzeń w usłudze Intune |

Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów, można również dołączyć do nich takie dane, jak następujące.

- Które grupy są włączone dla SSPR.
- Które metody uwierzytelniania są skonfigurowane.
- Zasady dostępu powiązane z systemem lub w sieci firmowej.
- Kroki rozwiązywania problemów dla typowych scenariuszy.

Możesz również zapoznać się z naszą dokumentacją online dotyczącą rozwiązywania problemów z samoobsługowym resetowaniem haseł, aby poznać ogólne kroki rozwiązywania problemów dla najbardziej typowych scenariuszy SSPR.

## <a name="next-steps"></a>Następne kroki

- [Rozważ zaimplementowanie ochrony hasłem usługi Azure AD](concept-password-ban-bad.md)

- [Rozważ zaimplementowanie inteligentnej blokady usługi Azure AD](howto-password-smart-lockout.md)

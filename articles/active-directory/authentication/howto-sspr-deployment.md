---
title: Samoobsługowe resetowanie haseł planu wdrożenia — usługi Azure Active Directory
description: Resetuj strategii dla pomyślnego wdrożenia haseł usługi Azure AD
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
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440950"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Wdrażanie usługi Azure AD samoobsługowego resetowania haseł

Samoobsługowe resetowanie haseł (SSPR) to funkcja usługi Azure Active Directory, która umożliwia użytkownikom na resetowanie haseł bez konieczności skontaktuj się z pracownikami działu IT. Pracownicy musi zarejestrować na potrzeby lub być zarejestrowany, samodzielnie resetując hasło przed rozpoczęciem korzystania z usługi. Podczas rejestracji pracownik wybiera jeden lub więcej metod uwierzytelniania, włączony przez organizację.

Samoobsługowe Resetowanie HASEŁ umożliwia pracownikom szybko uzyskać odblokowane i kontynuować pracę, niezależnie od tego, gdzie się znajdują lub porze dnia. Pozwalając użytkownikom na odblokowywanie samodzielnie, Twoja organizacja może zmniejszyć czas przestojów i kosztów obsługi wysokiej najbardziej typowe problemy związane z hasłami.

Pomóż użytkownikom, zarejestruj się szybko przez wdrożenie funkcji samoobsługowego resetowania HASEŁ, wraz z innej aplikacji lub usługi w Twojej organizacji. Ta akcja spowoduje wygenerowanie dużej liczby operacji logowania i będą miały rejestracji.

Przed wdrożeniem funkcji samoobsługowego resetowania HASEŁ, organizacje mogą chcieć określić, ile resetowania hasła powiązane pomocy technicznej możliwe za pośrednictwem czasu i średni koszt każdego wywołania. Używają tego dane po wdrożeniu Aby wyświetlić wartość, że chodzi o przeniesienie samoobsługowego resetowania HASEŁ dla Twojej organizacji.  

## <a name="how-sspr-works"></a>Sposób działania funkcji samoobsługowego resetowania HASEŁ

1. Gdy użytkownik próbuje zresetować hasło, należy sprawdzić ich wcześniej zarejestrowanego metodę lub metody uwierzytelniania potwierdzenie swojej tożsamości.
1. Użytkownik musi następnie wprowadzić nowe hasło.
   1. Dla użytkowników tylko w chmurze nowe hasło jest przechowywane w usłudze Azure Active Directory. Aby uzyskać więcej informacji, zobacz artykuł [działa jak SSPR](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Dla użytkowników hybrydowych hasło jest zapisywane z lokalną usługą Active Directory za pośrednictwem usługi Azure AD Connect. Aby uzyskać więcej informacji, zobacz artykuł [co to jest funkcja zapisywania zwrotnego haseł](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Uwagi dotyczące licencjonowania

Usługa Azure Active Directory jest licencji na użytkownika znaczenie, każdy użytkownik musi mieć odpowiednią licencję dla funkcji, które skorzystają.

- Samoobsługowe resetowanie haseł dla użytkowników tylko w chmurze jest dostępna, za pomocą usługi Azure AD podstawowa lub nowszej.
- Samoobsługowe resetowanie haseł za pomocą funkcji zapisywania zwrotnego w środowisku lokalnym dla środowisk hybrydowych wymaga usługi Azure AD Premium P1 lub nowszej.

Więcej informacji na temat licencjonowania można znaleźć na [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Zezwolenie na zarejestrowanie połączone dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania Wieloskładnikowego

Firma Microsoft zaleca się, że organizacje włączyć rejestrację połączone środowisko samoobsługowego resetowania HASEŁ i uwierzytelniania wieloskładnikowego. Po włączeniu tego środowiska połączonego rejestracji użytkowników musi jedynie wybrać raz swoje informacje rejestracyjne umożliwiające obie funkcje.

![Połączone zabezpieczeń informacji o rejestracji](./media/howto-sspr-deployment/combined-security-info.png)

Proces rejestracji połączone nie wymaga organizacji, aby włączyć funkcji samoobsługowego resetowania HASEŁ i usługi Azure Multi-Factor Authentication do użycia. Proces rejestracji połączone zapewnia organizacjom lepsze środowisko użytkownika w porównaniu do tradycyjnych poszczególne składniki. Więcej informacji na temat rejestracji połączone i jak włączyć, można znaleźć w artykule [połączone rejestracji informacje zabezpieczeń (wersja zapoznawcza)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Zaplanowanie konfiguracji

Następujące ustawienia są wymagane do włączenia funkcji samoobsługowego resetowania HASEŁ oraz zalecane wartości.

| Obszar | Ustawienie | Wartość |
| --- | --- | --- |
| **Właściwości funkcji samoobsługowego resetowania HASEŁ** | Włączono Samoobsługowe Resetowanie hasła | **Wybrane** grupy pilotażu / **wszystkich** w środowisku produkcyjnym |
| **Metody uwierzytelniania** | Metody uwierzytelniania, wymaganych do zarejestrowania | Zawsze 1 więcej niż jest to wymagane do resetowania |
|   | Metody uwierzytelniania, wymaganych do zresetowania | Jeden lub dwa |
| **Rejestracja** | Czy wymagać od użytkowników rejestrowania się podczas logowania? | Tak |
|   | Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania | 90-180 dni |
| **Powiadomienia** | Czy powiadamiać użytkowników o resetowaniu hasła? | Yes |
|   | Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło? | Yes |
| **Dostosowywanie** | Dostosuj link do pomocy technicznej | Tak |
|   | Niestandardowe pomocy poczty e-mail lub adres URL | Adres witryny lub adres e-mail pomocy technicznej |
| **Integracja lokalna** | Zapisywać zwrotnie hasła do lokalnej usługi AD | Yes |
|   | Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła | Yes |

### <a name="sspr-properties-recommendations"></a>Zalecenia dotyczące właściwości funkcji samoobsługowego resetowania HASEŁ

Podczas włączania obsługi funkcji samoobsługowego resetowania hasła, wybierz grupę zabezpieczeń do użycia podczas programu pilotażowego.

Podczas planowania szerzej Uruchom usługę, zaleca się wymuszanie samoobsługowego resetowania HASEŁ dla wszystkich użytkowników w organizacji za pomocą wszystkich opcji. Jeśli nie można ustawić wszystkie grupy zabezpieczeń wybierz odpowiednie usługi Azure AD lub AD synchronizowana z usługą Azure AD.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Ustaw metody uwierzytelniania wymagany do zarejestrowania do co najmniej jeden więcej niż liczba wymaganych do zresetowania. Zezwalanie wielu zapewnia użytkownikom elastyczność kiedy ich potrzebują zresetować.

Ustaw **liczba metod wymaganych do zresetowania** do odpowiedniego poziomu właściwych dla danej organizacji. Zajmowania się co najmniej, jeden wymaga, a dwa może być zwiększenie poziomu bezpieczeństwa.

Zobacz [metody uwierzytelniania](concept-authentication-methods.md) Aby uzyskać szczegółowe informacje na uwierzytelniania, który dostępnych metod w celu samoobsługowego resetowania HASEŁ, pytań zabezpieczających wstępnie zdefiniowane oraz tworzenie pytań zabezpieczających dostosowane.

### <a name="registration-settings"></a>Ustawienia rejestracji

Ustaw **wymagać od użytkowników rejestrowania się podczas logowania** do **tak**. To ustawienie oznacza, że użytkownicy muszą rejestrować się podczas logowania, zapewnienie, że wszyscy użytkownicy są chronione.

Ustaw **liczbę dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** do między **90** i **180** dni, chyba, że Twoja organizacja ma biznesowych potrzeb Aby uzyskać krótszy horyzont czasowy.

### <a name="notifications-settings"></a>Ustawienia powiadomień

Skonfigurować zarówno **powiadamiania użytkowników resetowaniu hasła** i **czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło** do **tak**. Wybieranie **tak** zarówno zwiększa bezpieczeństwo, zapewniając, że użytkownicy zapoznali się po ich hasło zostało zresetowane i że wszyscy administratorzy zostali powiadomieni o tym, gdy administrator zmieni hasło. Jeśli użytkownicy lub Administratorzy otrzymywać takie powiadomienie, a nie zainicjowano zmiany, mogą natychmiast zgłosić potencjalne naruszenia zabezpieczeń.

### <a name="customization"></a>Dostosowywanie

Koniecznie dostosować **pomocy poczty e-mail lub adres URL** aby upewnić się, użytkownicy, którzy napotkali problemy można szybko uzyskać pomoc. Ustaw tę opcję do wspólnego adres e-mail pomocy technicznej lub strony sieci web, który zna użytkowników.

### <a name="on-premises-integration"></a>Integracja lokalna

Jeśli masz w środowisku hybrydowym, upewnij się, że **zapisywać zwrotnie hasła do lokalnej usługi AD** jest ustawiona na **tak**. Również ustawić Zezwalaj użytkownikom na odblokowywanie konta bez resetowania hasła Yes (tak), ponieważ umożliwia ona większą elastyczność.

### <a name="changingresetting-passwords-of-administrators"></a>Zmienianie/resetowanie haseł administratorów

Konta administratorów to specjalne konta z podwyższonym poziomem uprawnień. Aby je zabezpieczyć, zmienianie haseł administratorów stosowane następujące ograniczenia:

- W środowisku lokalnym, Administratorzy przedsiębiorstwa lub Administratorzy domeny nie można zresetować swoje hasło, za pomocą funkcji samoobsługowego resetowania HASEŁ. Można zmienić tylko hasła w ich środowisku lokalnym. W związku z tym zaleca się nie synchronizacji lokalnego konta administratora usługi AD do usługi Azure AD.
- Administrator nie można użyć klucza tajnego pytania i odpowiedzi jako metodę do zresetowania hasła.

### <a name="environments-with-multiple-identity-management-systems"></a>W środowiskach z wielu systemów zarządzania tożsamościami

W przypadku wielu tożsamości systemy zarządzania w środowisku, np. menedżerom tożsamości lokalnych, takich jak Oracle AM, mają być synchronizowane z innymi systemami za pomocą narzędzia, takiego jak może być konieczne SiteMinder, lub innych systemów, a następnie hasła zapisane w usłudze Active Directory Powiadomienia usługi o zmianie hasła (PCNS) za pomocą programu Microsoft Identity Manager (MIM). Aby znaleźć informacji na temat tego scenariusza bardziej złożonych, zobacz artykuł [wdrażanie usługi powiadamiania o zmianę hasła programu MIM, na kontrolerze domeny](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Planowanie wdrożenia i obsługę funkcji samoobsługowego resetowania HASEŁ

### <a name="engage-the-right-stakeholders"></a>Współpraca z prawej zainteresowanymi stronami

W przypadku projektów technologii kończy się niepowodzeniem, zazwyczaj to zrobią z powodu niezgodnej oczekiwania na wpływ, wyniki i odpowiedzialności. Aby uniknąć tych problemów, upewnij się, że są interesujące prawo uczestnikom projektu i czy role biorący udział w projekcie w projekcie są dobrze zrozumiałe dokumentowanie zainteresowane strony i ich dane wejściowe projektu i odpowiedzialność.

### <a name="communications-plan"></a>Plan komunikacji

Komunikacja jest bardzo istotny dla sukcesu wszelkie nowe usługi. Aktywnie komunikują się ze swoimi użytkownikami sposobu korzystania z usługi i co można zrobić, aby uzyskać pomoc, jeśli coś nie działa zgodnie z oczekiwaniami. Przegląd [samoobsługowego resetowania haseł materiałów wdrożenia w Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) zapoznać się z pomysłami na temat sposobu planowania strategii komunikacji przez użytkownika końcowego.

### <a name="testing-plan"></a>Testowanie planu

Aby upewnić się, czy wdrożenie działa zgodnie z oczekiwaniami, należy zaplanować się zestaw przypadków testowych, które będą używane do sprawdzania poprawności wdrożenia. Poniższa tabela zawiera niektóre scenariusze przydatne testów, używanych do dokumentu w Twojej organizacji oczekiwane wyniki, w oparciu o zasady.

| Analiza biznesowa | Oczekiwany wynik |
| --- | --- |
| Portal samoobsługowego resetowania HASEŁ jest dostępny z sieci firmowej | Określany przez Twoją organizację |
| Portal samoobsługowego resetowania HASEŁ jest dostępne spoza sieci firmowej | Określany przez Twoją organizację |
| Resetowanie hasła użytkownika, za pomocą przeglądarki, gdy użytkownik nie jest włączone do resetowania hasła | Użytkownik nie jest mogli korzystać z usługi flow resetowania hasła |
| Resetowanie hasła użytkownika, za pomocą przeglądarki, gdy użytkownik nie jest zarejestrowany do resetowania hasła | Użytkownik nie jest mogli korzystać z usługi flow resetowania hasła |
| Loguje użytkownika podczas rejestracji resetowania haseł jest wymuszany. | Użytkownik jest monitowany o zarejestrowanie informacji o zabezpieczeniach |
| Loguje użytkownika podczas rejestracji resetowania haseł zostało ukończone. | Użytkownik nie jest monitowany o zarejestrowanie informacji o zabezpieczeniach |
| Portal samoobsługowego resetowania HASEŁ jest dostępny, gdy użytkownik nie ma licencji | Jest dostępny |
| Zresetuj hasło użytkownika z systemu Windows 10 AADJ lub H + AADJ ekranu blokady urządzenia po użytkownik został zarejestrowany | Użytkownik może zresetować hasło |
| Samoobsługowe Resetowanie HASEŁ rejestracji i dane użycia są dostępne dla administratorów w czasie zbliżonym do rzeczywistego | Jest dostępna za pośrednictwem dzienników inspekcji |

### <a name="support-plan"></a>Plan pomocy technicznej

Podczas samoobsługowego resetowania HASEŁ nie tworzy zwykle problemy użytkownika, ważne jest zapewnienie wsparcia podmiotu trzeciego personelu przygotowana, aby poradzić sobie z problemami, które mogą wystąpić.

Gdy administrator może zmienić lub zresetować hasła dla użytkowników końcowych za pośrednictwem portalu usługi Azure AD, lepiej jest przydatne w rozwiązywaniu problemu za pośrednictwem procesu samoobsługowa pomoc techniczna.

W Przewodnik operacyjny po części tego dokumentu Utwórz listę sprawy pomocy technicznej i ich prawdopodobne przyczyny i utworzyć linię do rozpoznawania.

### <a name="auditing-and-reporting"></a>Inspekcja i raportowanie

Po wdrożeniu w wielu organizacjach chcą wiedzieć, jak elementy są używane lub jeśli samoobsługowego resetowania haseł (SSPR). Funkcja raportowania, która zapewnia usługi Azure Active Directory (Azure AD) pomaga w uzyskaniu odpowiedzi na pytania za pomocą wbudowanych raportów.

Dzienniki inspekcji na potrzeby rejestracji i resetowaniem hasła są dostępne przez 30 dni. W związku z tym, jeśli inspekcji zabezpieczeń w firmie wymaga dłuższy okres przechowywania, dzienniki muszą być wyeksportowane i używane do narzędzia SIEM, takich jak [Azure przez wartownika](../../sentinel/connect-azure-active-directory.md), Splunk lub ArcSight.

W tabeli, jak poniżej dokumentu harmonogram tworzenia kopii zapasowych, system i odpowiedzialne strony. Nie potrzebujesz oddzielnych inspekcji i raportowania tworzenia kopii zapasowych, ale powinien mieć osobną kopię zapasową, z którego można odzyskać z problemu.

|   | Częstotliwość pobierania | System docelowy | Odpowiada innych firm |
| --- | --- | --- | --- |
| Kopia zapasowa inspekcji |   |   |   |
| Raportowanie kopii zapasowej |   |   |   |
| Kopii zapasowej do odzyskiwania po awarii |   |   |   |

## <a name="implementation"></a>Wdrażanie

Implementacja odbywa się w trzech etapach:

- Konfigurowanie użytkowników i licencji
- Konfigurowanie usługi Azure AD SSPR poniesione na rejestrację i samoobsługi
- Skonfiguruj program Azure AD Connect do zapisywania zwrotnego haseł

### <a name="communicate-the-change"></a>Zmiana komunikacji

Rozpocznij realizacji planu komunikacji, który opracował w fazie planowania.

### <a name="ensure-groups-are-created-and-populated"></a>Upewnij się, grupy są tworzone i wypełniane

W sekcji Planowanie hasło uwierzytelniania metod i upewnij się, dostępnych grup wdrożenia pilotażowego i produkcyjnego oraz wszystkich odpowiednich użytkowników są dodawane do grupy.

### <a name="apply-licenses"></a>Użycie licencji

Grupy są przesyłane do zaimplementowania musi mieć usługi Azure AD przypisaną licencję premium. Możesz przypisać licencje bezpośrednio do grupy, lub możesz użyć istniejących zasad takich jak licencji za pośrednictwem programu PowerShell lub Licencjonowanie oparte na grupach.

Informacje dotyczące przypisywania licencji do grup użytkowników można znaleźć w artykule [przypisywanie licencji do użytkowników, członkostwa w grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Konfigurowanie samoobsługowego resetowania HASEŁ

#### <a name="enable-groups-for-sspr"></a>Włącz grupy dla usługi SSPR

1. Dostęp do witryny Azure portal przy użyciu konta administratora.
1. Wybierz wszystkie usługi, a w polu filtru wpisz usługi Azure Active Directory, a następnie wybierz usługę Azure Active Directory.
1. W bloku usługi Active Directory wybierz resetowania hasła.
1. W okienku właściwości wybierz wybrane. Jeśli chcesz, aby wszyscy użytkownicy włączone, zaznacz wszystko.
1. W domyślne hasło zresetować blok zasad, wpisz nazwę pierwszej grupy, zaznacz go, a następnie kliknij pozycję Wybierz, w dolnej części ekranu i wybierz pozycję Zapisz w górnej części ekranu.
1. Powtórz ten proces dla każdej grupy.

#### <a name="configure-the-authentication-methods"></a>Konfigurowanie metod uwierzytelniania

Odwoływać się do planowania w sekcji Planowanie metod uwierzytelniania hasła w tym dokumencie.

1. Wybierz rejestracji, w obszarze zdecydować, użytkownik musi zarejestrować podczas logowania, wybierz opcję Tak, a następnie ustaw liczbę dni przed wygaśnięciem, a następnie wybierz przycisk Zapisz.
1. Wybierz powiadomienie, skonfiguruj na plan i następnie wybierz przycisk Zapisz.
1. Dostosowanie, wybierz i skonfiguruj na planie, a następnie wybierz Zapisz.
1. Integracja środowiska lokalnego, wybierz i skonfiguruj na planie, a następnie wybierz Zapisz.

### <a name="enable-sspr-in-windows"></a>Włączanie funkcji samoobsługowego resetowania HASEŁ w Windows

Systemu Windows 10 systemem w wersji 1803 lub nowszej, które są albo przyłączony Azure AD lub hybrydowe przyłączone do usługi Azure AD mogą resetować swoje hasła na ekranie logowania Windows. Informacje i instrukcje konfiguracji tej funkcji można znaleźć w artykule [haseł usługi Azure AD z ekranu logowania](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Konfigurowanie zapisywania zwrotnego haseł

Kroki, aby skonfigurować funkcję zapisywania zwrotnego haseł dla swojej organizacji można znaleźć w artykule [porad: Konfigurowanie zapisywania zwrotnego haseł](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Zarządzanie funkcji samoobsługowego resetowania HASEŁ

Wymagane role do zarządzania funkcjami skojarzone z samoobsługowego resetowania haseł.

| Business roli/osoby | Rolę na platformie Azure AD (w razie potrzeby) |
| :---: | :---: |
| Pomoc techniczna poziomu 1 | Administrator haseł |
| Pomoc techniczna na poziomie 2 | Administrator użytkowników |
| Administrator funkcji samoobsługowego resetowania HASEŁ | Administrator globalny |

### <a name="support-scenarios"></a>Obsługiwane scenariusze

Aby włączyć sukcesu zespołu pomocy technicznej, można utworzyć oparty na pytania, otrzymanymi od użytkowników często zadawanych PYTAŃ. Poniższa tabela zawiera typowe scenariusze pomocy technicznej.

| Scenariusze | Opis |
| --- | --- |
| Użytkownik nie ma żadnych dostępnych metod uwierzytelniania zarejestrowane | Użytkownik próbuje zresetować swoje hasło, ale nie ma żadnych metod uwierzytelniania one zarejestrowane dostępne (przykład: left ich telefonu komórkowego w domu, a nie dostęp do poczty e-mail) |
| Użytkownik nie jest odbierania tekstu lub wywołać na telefon komórkowy lub pakietu office | Użytkownik próbuje zweryfikować swoją tożsamość za pomocą wiadomość SMS lub połączenie, ale nie odbiera tekst/wywołania. |
| Użytkownik nie może uzyskać dostęp do portalu resetowania haseł | Użytkownik chce, aby zresetować swoje hasło, ale nie jest włączona w celu zresetowania hasła i w związku z tym nie może uzyskać dostęp do strony, można zaktualizować hasła. |
| Użytkownik nie może ustawić nowe hasło | Użytkownik kończy weryfikacji podczas przepływu resetowania haseł, ale nie można ustawić nowe hasło. |
| Użytkownik nie widzi link resetowania hasła na urządzeniu z systemem Windows 10 | Użytkownik próbuje zresetować hasło z ekranu blokady systemu Windows 10, ale urządzenia albo nie jest częścią usługi Azure AD lub nie włączono zasad urządzenia usługi Intune |

Można również zawierać informacje, takie jak następujące dodatkowe procedury rozwiązywania problemów.

- Grupy, które są włączone dla funkcji samoobsługowego resetowania HASEŁ.
- Metody uwierzytelniania, które są skonfigurowane.
- Zasady dostępu związane z na lub z siecią firmową.
- Kroki rozwiązywania problemów dla typowych scenariuszy.

Może również odnosić się do dokumentacji online na temat rozwiązywania problemów samoobsługowego resetowania hasła, aby poznać ogólne kroki rozwiązywania problemów dla typowych scenariuszy funkcji samoobsługowego resetowania HASEŁ.

## <a name="next-steps"></a>Kolejne kroki

- [Rozważ zaimplementowanie ochrona za pomocą hasła usługi Azure AD](concept-password-ban-bad.md)

- [Rozważ zaimplementowanie usługi Azure AD inteligentnej blokady](howto-password-smart-lockout.md)

---
title: Jak zarządzać kontem komercyjnych portalu Marketplace w Centrum partnerskim
description: Dowiedz się, jak zarządzać kontem komercyjnych portalu Marketplace w Centrum partnerskim.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: 5cb4caa6f0f8098e68d693be6cc2f33b5ccbeb32
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752827"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Jak zarządzać kontem komercyjnych portalu Marketplace w Centrum partnerskim 

Po [utworzone konto w Centrum partnerskim](./create-account.md), można zarządzać, Twoje konto i ofert przy użyciu [pulpit nawigacyjny handlowe Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

W tym artykule będzie przejdziemy do sposobu zarządzania kontem Centrum partnerskiego, np.: 

- [Dostęp do ustawień konta Centrum partnerskiego](#access-your-account-settings)
- [Znajdź identyfikator wydawcy, sprzedawcy identyfikator, nazwę użytkownika i dzierżaw usługi Azure AD](#account-details)
- [Zaktualizuj informacje kontaktowe](#contact-info)
- [Zarządzanie szczegółami finansowe (informacje dotyczące wypłat konto, podatku profilu, stan wstrzymania informacje dotyczące wypłat)](#financial-details)
- [Konfigurowanie śledzenia identyfikatorów GUID do monitorowania użycia przez klientów](#tracking-guids)
- [Użytkownicy Menedżera](#manage-users)
- [Menedżer grupy](#manage-groups)
- [Aplikacji Menedżer usłudze Azure AD](#manage-azure-ad-applications)
- [Definiowanie ról użytkowników i uprawnień](#define-user-roles-and-permissions)
- [Zarządzanie dzierżaw usługi Azure AD (kont służbowych)](#manage-tenants)
- [Centrum partnerskie Menedżera umowy](#agreements)


## <a name="access-your-account-settings"></a>Dostęp do ustawień konta

Jeśli jeszcze tego nie zrobiono, użytkownik (lub administratorem organizacji) powinien uzyskiwać dostęp do [ustawienia konta](https://partner.microsoft.com/dashboard/account/management) dla Twojego konta w Centrum partnerskim w celu:
- Sprawdź stan weryfikacji konta w firmie
- Upewnij się, Twoje identyfikator sprzedawcy, identyfikator MPN, identyfikator wydawcy i informacje kontaktowe, w tym kontakt osoba zatwierdzająca i sprzedawcy firmy
- Skonfiguruj szczegóły finansowych w firmie, w tym zwolnienia z podatku, jeśli są one
- Tworzenie kont użytkowników dla każdego, kto będzie używać swojego konta firmowego Centrum partnerskiego

### <a name="open-developer-settings"></a>Otwórz ustawienia dla deweloperów

Ustawienia konta znajduje się w prawym górnym rogu usługi [pulpit nawigacyjny handlowe Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace) Centrum partnerskiego. Wybierz ikonę koła zębatego (w pobliżu prawego górnego rogu pulpitu nawigacyjnego), a następnie wybierz pozycję **ustawienia dewelopera**. 

![Menu Ustawienia konta w Centrum partnerskiego](./media/dashboard-developer-settings.png)

Wewnątrz **ustawienia konta**, będzie można wyświetlić swoje:
- **Ekranu szczegóły konta**: Typ konta i stan konta
- **Identyfikatory wydawcy**: Identyfikator sprzedawcy identyfikator użytkownika, identyfikator wydawcy, dzierżaw usługi Azure AD, itp.
- **Informacje kontaktowe**: Nazwa wyświetlana wydawcy, sprzedawcy i nazwisko osoby kontaktowej, poczty e-mail, telefon i adres
- **Informacje finansowe**: Informacje dotyczące wypłat konta, podatku profil i informacje dotyczące wypłat stan blokady
- **urządzenia**: Wszystkie urządzenia testowania skojarzonych z Twoim kontem
- **Śledzenie identyfikatorów GUID**: Wszystkie identyfikatory GUID skojarzenia z Twoim kontem śledzenia

### <a name="account-details"></a>Szczegóły konta

W sekcji szczegółów konta można zobaczyć podstawowe informacje, takie jak Twoje **typ konta** (firmy lub osoby) i **stanu weryfikacji** Twojego konta. Podczas procesu weryfikacji konta usługi te ustawienia będą wyświetlane każdy krok jest wymagany, m.in. Weryfikacja adresu e-mail, weryfikacja zatrudnienia i weryfikacji biznesowych. Można również zaktualizować tutaj swój adres e-mail i Wyślij ponownie weryfikacji w razie potrzeby. 

### <a name="publisher-ids"></a>Identyfikatory wydawcy

W sekcji identyfikatory wydawcy, możesz zobaczyć swoje **identyfikator sprzedawcy**, **identyfikator MPN**, i **Identyfikatora wydawcy**. Te wartości są przypisane przez firmę Microsoft do unikatowego identyfikowania konta dewelopera i nie można go edytować.

### <a name="contact-info"></a>Informacje kontaktowe

W sekcji informacji o kontaktu, możesz zobaczyć swoje **Nazwa wyświetlana wydawcy**, **informacje kontaktowe sprzedawcy** (nazwisko osoby kontaktowej, poczty e-mail, numer telefonu i adres sprzedawcy firmy) i **firmy Osoba zatwierdzająca** (nazwa, adres e-mail i numer telefonu osoby z uprawnienia do zatwierdzania decyzji dotyczących firmy). 

### <a name="financial-details"></a>Informacje finansowe

W sekcji danych finansowych można zapewnić lub zaktualizować swoje informacje finansowe, jeśli opublikujesz płatne aplikacje, dodatki lub usług. 

Jeśli planujesz listę bezpłatnych ofert, nie trzeba skonfigurować konto informacje dotyczące wypłat lub wypełnianie formularzy wszelkich podatków. Jeśli później zmieni zdanie, a później zdecydujesz, że chcesz sprzedawać za pośrednictwem firmy Microsoft, możesz skonfigurować konto informacje dotyczące wypłat i wypełnianie formularzy podatku w danym momencie. 

#### <a name="payout-account"></a>Informacje dotyczące wypłat konta

Konto informacje dotyczące wypłat to koncie bankowym, do którego bieżący postęp wykonywania są wysyłane ze sprzedaży. To konto bank musi być w tym samym kraju, w którym zarejestrowane konto w Centrum partnerskim.

Aby skonfigurować informacje dotyczące wypłat konta, musisz **skojarzyć Account Microsoft**:
1. W **ustawienia konta**w obszarze **danych finansowych** zaznacz **skojarzyć Account Microsoft**. 
2. Po wyświetleniu monitu zaloguj się przy użyciu Twojego konta Microsoft (MSA). To konto nie może być już skojarzona z innym kontem Centrum partnerskiego. 
3. Aby ukończyć konfigurowanie konta informacje dotyczące wypłat wylogować całkowicie Centrum partnerskiego, a następnie zaloguj się ponownie przy użyciu Account Microsoft (a nie swojego konta służbowego). 

Teraz, gdy Account Microsoft jest skojarzony, aby dodać konto informacje dotyczące wypłat należy:
- **Wybierz formę płatności**: Konta bankowego lub PayPal
- **Dodaj informacje o płatności**: Może to obejmować Wybieranie typu konta (sprawdzanie oszczędności "lub"), wprowadź nazwę właściciela konta, numer konta i routing numer, nazwisko, adres, numer telefonu lub adres e-mail PayPal. * Aby uzyskać więcej informacji o używaniu PayPal, jako metody płatności konta i Dowiedz się, czy jest obsługiwany w Twoim regionie na rynku, zobacz [informacje o płatności PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Zmiana typu konta informacje dotyczące wypłat można opóźnić płatności za do jednego cyklu płatności. To opóźnienie występuje, ponieważ będziemy musieli zweryfikować zmiany konta, tak jak podczas pierwszej konfiguracji konta informacje dotyczące wypłat. Będziesz nadal opłacania cała kwota po zweryfikowaniu konta; płatności powodu dla bieżącej płatności cykl zostanie dodany do następnej.  

#### <a name="tax-profile"></a>Profil podatku

Przejrzyj bieżący stan profilu podatku, potwierdzenie poprawny **typu jednostki** i **informacje o certyfikacie podatku** jest wyświetlana. Wybierz **Edytuj** zaktualizować lub wykonaj dowolny wymagane formularze.

Aby możliwe było nawiązanie swój status podatku, należy określić kraj w miejscu zamieszkania użytkownika oraz zaawansowanych możliwości dostępnych i wypełni formularzy podatkowych odpowiednich skojarzone z kraju/regionu.

Niezależnie od tego, w Twoim kraju zamieszkania lub obywatelstwa musi wypełniać formularze podatku Stanów Zjednoczonych do sprzedaży ofert przez firmę Microsoft. Partnerzy, którzy spełniają określone wymagania dotyczące rezydencji Stanów Zjednoczonych, musisz wypełnić formularz IRS W-9. Innymi partnerami poza Stanami Zjednoczonymi, musisz wypełnić formularz IRS W-8. Możesz też wypełnić te formularze online po zakończeniu profilu podatku.

Numer identyfikacji podatkowej poszczególnych Stanów Zjednoczonych (lub ITIN) nie jest wymagany, aby otrzymywać od firmy Microsoft lub oświadczenia korzyści Traktat podatku.

Można wypełnić oraz przesłać formularze podatku elektronicznie w Centrum partnerskim; w większości przypadków nie trzeba wydrukować i formularzy do obsługi poczty.

Różnych krajów i regionów mają wymagania inny podatek. Dokładne kwotę podatków — w tym musisz zapłacić zależy od innych krajów i regionów, w której sprzedajesz swoje oferty. Microsoft przekazuje sprzedaży i Użyj podatku w Twoim imieniu w niektórych krajach. Te kraje zostaną zidentyfikowane w trakcie wyświetlania listy oferty. W innych krajach, w zależności od tego, gdzie zostały zarejestrowane może być konieczne wpłaca sprzedaży, a następnie użyj podatkowych dla sprzedaży bezpośrednio do lokalnych odpowiedniemu urzędowi. Ponadto przychodów ze sprzedaży, które otrzymujesz, może być opodatkowaniu jako przychodu. Zdecydowanie zachęcamy do kontaktowania się z urzędu odpowiednie dla danego kraju lub regionu, który najlepiej może pomóc w określeniu informacji podatkowych odpowiednie dla transakcji sprzedaży firmy Microsoft.

##### <a name="withholding-rates"></a>Wstrzymanie stawki
Informacje przesyłane w formularzach podatku określa odpowiednie podatku, wstrzymanie szybkości. Wstrzymania stawka jest stosowana tylko do sprzedaży, które wprowadzasz w Stanach Zjednoczonych. Sprzedaż do lokalizacji poza USA nie są obciążane wstrzymania. Stawki wstrzymania różnić się od, ale dla większości deweloperów rejestrowanie poza Stanami Zjednoczonymi, szybkość domyślna to 30%. Masz możliwość zmniejszenia tego kursu, jeśli Twoim kraju zgodziła się Traktat podatkowych ze Stanami Zjednoczonymi.

##### <a name="tax-treaty-benefits"></a>Korzyści Traktat podatku
Jeśli jesteś spoza Stanów Zjednoczonych, można korzystać z zalet podatku Traktat korzyści. Korzyści te różnią się od kraju i umożliwiają skrócenie podatków, które withholds firmy Microsoft. Podatek Traktat korzyści może przyjąć, wykonując część II W 8BEN formularzu. Zaleca się komunikować się za pomocą odpowiednich zasobach w swoim kraju lub regionie w celu określenia, czy te korzyści odnoszą się do Ciebie.

[Więcej informacji na temat informacji podatkowych dla deweloperów aplikacji/gry Windows i wydawcom portalu Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Informacje dotyczące wypłat stan wstrzymania

Domyślnie firma Microsoft wysyła płatności, co miesiąc. Jednak istnieje możliwość zawiesić użytkownika wypłaty, co uniemożliwi Wysyłanie płatności na koncie. Jeśli zdecydujesz się zawiesić wypłaty usługi, będziemy zarejestrować przychód zdobycia i zawierają szczegółowe informacje w swojej **Podsumowanie informacje dotyczące wypłat**. Jednak nie będzie wysyłamy płatności na koncie czasu usunięcia blokady. 

Aby umieścić płatności wstrzymany, przejdź do **ustawienia konta**. W obszarze **danych finansowych**w **stan blokady informacje dotyczące wypłat** sekcji, suwak, aby przełączyć **na**. Możesz zmienić swój stan wstrzymania informacje dotyczące wypłat w dowolnym momencie, ale należy pamiętać, że decyzji będzie miało wpływ na następny informacje dotyczące wypłat miesięcznych. Na przykład, jeśli chcesz przechowywać informacje dotyczące wypłat w kwietniu, upewnij się, że ustawienia stanu wstrzymania informacje dotyczące wypłat **na** przed końcem marca.

Po ustawieniu swoje informacje dotyczące wypłat przechowywania stanu **na**, wszystkie wypłaty będzie zablokowany do momentu ustaw suwak przełączania do **poza**. Jeśli tak zrobisz, będziesz mieć dołączone podczas następnego cyklu miesięcznego informacje dotyczące wypłat (pod warunkiem któryś z progów płatności zostały spełnione). Na przykład, jeśli wystąpiły swoje wypłaty wstrzymane, ale będzie przykład aby uzyskać informacje dotyczące wypłat, generowane w czerwcu upewnij się przełączyć wypłata stan blokady do **poza** przed końcem maja.

> [!NOTE]
> Twoje **stan blokady informacje dotyczące wypłat** dotyczy wyboru **wszystkie** źródła przychodów, które są wypłacane przy użyciu Center partnera firmy Microsoft, w tym portalu Azure Marketplace — usługi AppSource, Microsoft Store, anonsowanie itp.). Nie można wybrać blokady różne stany dla każdego źródła przychodów.

### <a name="devices"></a>Urządzenia

Ustawienia zarządzania urządzeniami dotyczą tylko publikowania platformy uniwersalnej systemu Windows. [Dowiedz się więcej](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Identyfikatory GUID śledzenia

Globalnie unikatowe identyfikatory (GUID) są unikatowe numery (przy użyciu 32 cyfry szesnastkowe), który może służyć do śledzenia użycia platformy Azure. 

Aby utworzyć identyfikatorów GUID dla śledzenia, należy użyć GUID generator. Zespół usługi Azure Storage został utworzony [formie generator GUID](https://aka.ms/StoragePartners) , otrzymasz wiadomość e-mail identyfikatora GUID w poprawnym formacie i może być ponownie używane w systemach różnych śledzenia.

Firma Microsoft zaleca się utworzenie Unikatowy identyfikator GUID dla każdego kanału oferty i dystrybucji dla każdego produktu. Możesz zdecydować się na użycie jednego identyfikatora GUID wielu kanałach dystrybucji produktu, jeśli nie chcesz, raportowanie do podzielenia.

Jeśli wdrożenie produktu za pomocą szablonu i jest dostępna zarówno portalu Azure Marketplace i w witrynie GitHub, można tworzyć i zarejestruj 2 unikatowych identyfikatorów GUID:

*   Produkt w witrynie Azure Marketplace
*   Produkt A w witrynie GitHub

Raportowanie będzie odbywać się przez wartość partnera (identyfikator partnera firmy Microsoft) i identyfikatory GUID. Można także śledzić identyfikatorów GUID na bardziej szczegółowym poziomie, co oznacza każdy plan w ramach oferty.

Aby uzyskać więcej informacji, zobacz [Azure śledzenia użycia przez klientów przy użyciu identyfikatorów GUID — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Zarządzanie kontami wielu użytkowników

Centrum partnerskie wykorzystuje [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) dostęp użytkowników z wieloma kontami i zarządzania. Twoja organizacja usługi Azure AD jest automatycznie kojarzony z konta w Centrum partnerskim w ramach procesu rejestracji. 

## <a name="manage-users"></a>Zarządzanie użytkownikami

**Użytkowników** sekcji Centrum partnerskiego (w obszarze **ustawienia konta**) użyjemy usługi Azure AD do zarządzania użytkownikami, grupami i aplikacji usługi Azure AD, które mają dostęp do konta w Centrum partnerskim. Należy pamiętać, że aby zarządzać użytkownikami, użytkownik musi być zalogowany przy użyciu usługi [konto służbowe](./company-work-accounts.md) (dzierżawy skojarzone usługi Azure AD). Zarządzanie użytkownikami w ramach konta pracy / dzierżawy, musisz się wylogować, a następnie zaloguj się ponownie jako użytkownik z **Menedżera** uprawnienia, konto służbowe / dzierżawy. 

Po zalogowaniu się za pomocą swojego konta służbowego (dzierżawy usługi Azure AD), możesz wykonywać następujące czynności:
- [Dodawanie lub usuwanie użytkowników](#add-or-remove-users)
- [Zmień hasło użytkownika](#change-a-user-password)
- [Dodawanie lub usuwanie grup](#add-or-remove-users)
- [Dodawanie lub usuwanie aplikacji usługi Azure AD](#add-new-azure-ad-applications)
- [Zarządzanie kluczami dla aplikacji usługi Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definiowanie ról użytkowników i uprawnień](#define-user-roles-and-permissions)


Należy pamiętać, że wszyscy użytkownicy Centrum partnerskiego (w tym grupom i aplikacjom usługi Azure AD) musi mieć konto aktywną pracą w [dzierżawy usługi Azure AD](#manage-tenants) skojarzonym z kontem Centrum partnerskiego. 

### <a name="add-or-remove-users"></a>Dodawanie lub usuwanie użytkowników

Twoje konto musi mieć [ **poziom Manager** ](#define-user-roles-and-permissions) uprawnienia dla [(dzierżawy usługi Azure AD) konto służbowe](./company-work-accounts.md) , w której chcesz dodać lub edytować użytkowników.

#### <a name="add-existing-users"></a>Dodawanie istniejących użytkowników

Aby dodać użytkowników do Twojego konta w Centrum partnerskim, który już istnieje w Twojej firmie [(dzierżawy usługi Azure AD) konto służbowe](./company-work-accounts.md):

1. Przejdź do **użytkowników** (w obszarze **ustawienia konta**) i wybierz **dodawania użytkowników**.
2. Wybierz co najmniej jednego użytkownika z wyświetlonej listy. Pole wyszukiwania umożliwia wyszukiwanie określonych użytkowników.
* Jeśli wybierzesz więcej niż jednego użytkownika, aby dodać do konta w Centrum partnerskim, należy im przypisać tego samego rolę lub zestaw uprawnień niestandardowych. Aby dodać wielu użytkowników z różnych ról lub wymagane uprawnienia, powtórz te kroki dla poszczególnych ról lub ustaw uprawnienia niestandardowe.
3.  Po wybraniu użytkowników, kliknij przycisk **Dodaj wybrane**.
5.  W **role** sekcji, określ ról lub uprawnień niestandardowych dla wybranych użytkowników.
6.  Wybierz pozycję **Zapisz**.

#### <a name="create-new-users"></a>Utwórz nowych użytkowników

Aby utworzyć zupełnie nowych kont użytkowników, musi mieć konto z [ **administratora globalnego** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) uprawnienia. 

1. Przejdź do **użytkowników** (w obszarze **ustawienia konta**), wybierz opcję **dodawania użytkowników**, następnie wybierz **tworzenia nowych użytkowników**.
1. Wprowadź imię, nazwisko i nazwę użytkownika dla każdego nowego użytkownika. 
1. Jeśli chcesz, aby nowy użytkownik musi mieć konto administratora globalnego w katalogu organizacji, sprawdź pole o nazwie **wyznaczenia tego użytkownika administratora globalnego w usłudze Azure AD z pełną kontrolę nad wszystkimi zasobami katalogu**. Zapewni to użytkownik pełny dostęp do wszystkich funkcji administracyjnych w Twojej firmie w usłudze Azure AD. One będziesz mieć możliwość dodawania użytkowników i zarządzać nimi w Twojej organizacji konta służbowego (dzierżawy usługi Azure AD), chociaż Centrum partnerskiego nie, chyba że Przyznaj kontu odpowiednie/uprawnienia roli. 
1. Gdy zaznaczono pole, aby **wyznaczenia tego użytkownika administratora globalnego**, musisz podać **hasła odzyskiwania w wiadomości e-mail** dla użytkownika do odzyskania hasła, jeśli to konieczne.
1. W **członkostwo w grupie** wybierz wszystkie grupy, do których ma się należeć nowemu użytkownikowi.
1. W **role** sekcji, określ ról lub uprawnień niestandardowych dla użytkownika.
1. Wybierz pozycję **Zapisz**.

Tworzenie nowego użytkownika w Centrum partnerskim utworzy konto dla tego użytkownika konta służbowego (dzierżawy usługi Azure AD), do którego użytkownik jest zalogowany. Wprowadzanie zmian do nazwy użytkownika w Centrum partnerskim będzie wprowadzenie identycznych zmian w Twojej organizacji konta służbowego (dzierżawy usługi Azure AD).

#### <a name="invite-new-users-by-email"></a>Zapraszać nowych użytkowników za pośrednictwem poczty e-mail

Aby zaprosić użytkowników, którzy nie znajdują się w ramach swojego konta służbowego firmy (dzierżawy usługi Azure AD) za pośrednictwem poczty e-mail, konieczne jest posiadanie konta z [ **administratora globalnego** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) uprawnienia. 

1. Przejdź do **użytkowników** (w obszarze **ustawienia konta**), wybierz opcję **dodawania użytkowników**, następnie wybierz **zaprosić użytkowników za pośrednictwem poczty e-mail**.
2. Wprowadź co najmniej jeden adres e-mail adresów (maksymalnie 10), rozdzielonych przecinkami lub średnikami.
3. W **role** sekcji, określ ról lub uprawnień niestandardowych dla użytkownika.
4. Wybierz pozycję **Zapisz**.

Zaproszeni użytkownicy otrzymają wiadomość e-mail z zaproszeniem do dołączenia do konta w Centrum partnerskim. Nowe konto użytkownika gościa zostanie utworzony w swojego konta służbowego (dzierżawy usługi Azure AD). Każdy użytkownik należy do akceptowania zaproszenia przed uzyskaniem dostępu do konta.

Jeśli potrzebujesz ponownie wysłać zaproszenia, odwiedź stronę **użytkowników** strony, Znajdź zaproszenia na liście użytkowników, wybierz swój adres e-mail (lub tekst, który jest wyświetlany komunikat *zaproszenie oczekujące*). W dolnej części strony wybierz **Wyślij ponownie zaproszenie**.
 

> [!NOTE]
> Jeśli Twoja organizacja używa [integracji katalogu](https://go.microsoft.com/fwlink/p/?LinkID=724033) do synchronizacji usługi katalogowej w środowisku lokalnym za pomocą usługi Azure AD, nie można utworzyć nowych użytkowników, grup lub aplikacji usługi Azure AD w Centrum partnerskim. Użytkownik (lub inny administrator w katalogu lokalnego) należy utworzyć je bezpośrednio w katalogu lokalnym, zanim będzie mógł zobaczyć i dodać je do Centrum partnerskiego.

#### <a name="remove-a-user"></a>Usuwanie użytkownika

Aby usunąć użytkownika ze swojego konta służbowego (dzierżawy usługi Azure AD), przejdź do **użytkowników** (w obszarze **ustawienia konta**), wybierz użytkownika, który chcesz usunąć za pomocą pola wyboru w prawej kolumnie, a następnie wybierz  **Usuń** z dostępnych akcji. Okno podręczne pojawi się na potwierdzenie, że chcesz usunąć wybranych użytkowników.

#### <a name="change-a-user-password"></a>Zmień hasło użytkownika

Jeśli jeden z użytkowników wymaga zmiany hasła, mogą to zrobić samodzielnie, jeśli podano **hasła odzyskiwania w wiadomości e-mail** podczas tworzenia konta użytkownika. Można również zaktualizować hasło użytkownika, wykonując poniższe kroki. Aby zmienić hasło użytkownika w swojego konta służbowego firmy (dzierżawy usługi Azure AD), użytkownik musi być zalogowany przy użyciu konta z [ **administratora globalnego** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) uprawnienia. Należy pamiętać, że spowoduje to zmianę hasła użytkownika w dzierżawie usługi Azure AD wraz z hasłem umożliwia dostęp do Centrum partnerskiego.

1.  Z **użytkowników** strony (w obszarze **ustawienia konta**), wybierz nazwę konta użytkownika, który chcesz edytować.
2.  Wybierz **Resetuj hasło** znajdujący się u dołu strony.
3.  Wyświetlanie danych logowania dotyczących użytkownika, w tym hasło tymczasowe zostanie wyświetlona strona potwierdzenia. Pamiętaj wydrukować lub skopiuj te informacje i przekazać go do użytkownika, ponieważ nie będzie można uzyskać dostępu do tymczasowego hasła, po opuścisz tę stronę.


## <a name="manage-groups"></a>Zarządzanie grupami

Grupy umożliwiają kontrolowanie wielu ról użytkowników i uprawnień razem.

#### <a name="add-an-existing-group"></a>Dodaj istniejącą grupę

Aby dodać grupę oznacza już istnieje w Twojej organizacji konta służbowego (dzierżawy usługi Azure AD) do konta w Centrum partnerskim: 

1.  Z **użytkowników** strony (w obszarze **ustawienia konta**), wybierz opcję **Dodawanie grup**.
2.  Wybierz co najmniej jedną grupę z wyświetlonej listy. Aby wyszukać określone grupy, można użyć pola wyszukiwania.
Jeśli wybierzesz więcej niż jednej grupy, aby dodać do konta w Centrum partnerskim, należy im przypisać tego samego rolę lub zestaw uprawnień niestandardowych. Aby dodać wiele grup o różnych ról lub wymagane uprawnienia, powtórz te kroki dla poszczególnych ról lub ustaw uprawnienia niestandardowe.
3.  Po wybraniu grup kliknij **Dodaj wybrane**.
4.  W **role** sekcji, określ ról lub uprawnień niestandardowych dla wybranych grup. Wszyscy członkowie grupy będą mogli korzystać z Centrum partnerskiego konta z uprawnieniami odnoszą się do tej grupy, niezależnie od tego, czy role i uprawnienia związane z ich indywidualne konto.
5.  Wybierz pozycję **Zapisz**.

Po dodaniu istniejącą grupę, każdy użytkownik, który jest członkiem tej grupy będzie dostępu do Twojego konta w Centrum partnerskim przy użyciu uprawnień skojarzonych z przypisaną rolę grupy.

#### <a name="add-a-new-group"></a>Dodaj nową grupę

Aby dodać grupę dobór z kontem Centrum partnerskiego: 

1.  Z **użytkowników** strony (w obszarze **ustawienia konta**), wybierz opcję **Dodawanie grup**.
2.  Na następnej stronie wybierz **nową grupę**.
3.  Wprowadź nazwę wyświetlaną dla nowej grupy.
4.  Określ role lub niestandardowe uprawnienia dla grupy. Wszyscy członkowie grupy będą mogli uzyskać dostępu do konta Centrum partnerskie z uprawnieniami, że możesz zgłosić się tutaj, niezależnie od ról uprawnień skojarzonych z ich indywidualne konto.
5.  Wybierz użytkowników dla nowej grupy z wyświetlonej listy. Pole wyszukiwania umożliwia wyszukiwanie określonych użytkowników.
6.  Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **Dodaj wybrane** Aby dodać je do nowej grupy.
7.  Wybierz pozycję **Zapisz**.

Należy pamiętać, że ta nowa grupa zostanie utworzona w Twojej organizacji konta służbowego (dzierżawy usługi Azure AD) także nie tylko na Twoim koncie Centrum partnerskiego.

#### <a name="remove-a-group"></a>Usuwanie grupy

Aby usunąć grupę z Twojego konta służbowego (dzierżawy usługi Azure AD), przejdź do **użytkowników** (w obszarze **ustawienia konta**), wybierz grupę, którą chcesz usunąć za pomocą pola wyboru w prawej kolumnie, a następnie wybierz  **Usuń** z dostępnych akcji. Okno podręczne pojawi się na potwierdzenie, że chcesz usunąć wybrane grupy.

## <a name="manage-azure-ad-applications"></a>Zarządzanie aplikacjami usługi Azure AD

Możesz zezwolić aplikacji lub usług należących do firmy w usłudze Azure AD w celu uzyskania dostępu do konta Centrum partnerskiego. 

#### <a name="add-existing-azure-ad-applications"></a>Dodawanie istniejących aplikacji usługi Azure AD 

Aby dodać aplikacje z już istniejącymi w firmie usługi Azure Active Directory: 

1.  Z **użytkowników** strony (w obszarze **ustawienia konta**), wybierz opcję **Dodaj aplikacje usługi Azure AD**.
2.  Wybierz co najmniej jednej aplikacji usługi Azure AD z wyświetlonej listy. Pole wyszukiwania można użyć do wyszukiwania określonych aplikacji usługi Azure AD. Jeśli wybierzesz więcej niż jedną aplikację usługi Azure AD, aby dodać do konta w Centrum partnerskim, należy im przypisać tego samego rolę lub zestaw uprawnień niestandardowych. Aby dodać wiele aplikacji usługi Azure AD przy użyciu różnych ról lub wymagane uprawnienia, powtórz te kroki dla poszczególnych ról lub ustaw uprawnienia niestandardowe.
3.  Po wybraniu aplikacji usługi Azure AD, kliknij przycisk **Dodaj wybrane**.
5.  W **role** sekcji, określ ról lub uprawnień niestandardowych dla wybranej aplikacji usługi Azure AD.
6.  Wybierz pozycję **Zapisz**.

#### <a name="add-new-azure-ad-applications"></a>Dodawanie nowej usługi Azure AD aplikacji 

Jeśli chcesz udzielić dostępu do Centrum partnerskiego na zupełnie nowym platformy Azure AD konto aplikacji, możesz je utworzyć w **użytkowników** sekcji. Należy pamiętać, że spowoduje to utworzenie nowego konta w swojego konta służbowego firmy (dzierżawy usługi Azure AD), nie tylko na Twoim koncie Centrum partnerskiego. Jeśli jest używany przede wszystkim tę aplikację usługi Azure AD do uwierzytelniania w Centrum partnerskim, a nie ma potrzeby użytkownikom dostępu do niego bezpośrednio, możesz wprowadzić dowolny prawidłowy adres dla **adres URL odpowiedzi** i **identyfikator URI Identyfikatora aplikacji**, tak długo, te wartości nie są używane przez inną aplikację usługi Azure AD w katalogu.

1.  Z **użytkowników** strony (w obszarze **ustawienia konta**), wybierz opcję **Dodaj aplikacje usługi Azure AD**.
2.  Na następnej stronie wybierz **nowej usługi Azure AD aplikacji**.
3.  Wprowadź **adres URL odpowiedzi** dla nowej aplikacji usługi Azure AD. To jest adres URL, w którym użytkownicy mogą zarejestrować się w i korzystania z aplikacji usługi Azure AD (czasami nazywany także adres URL aplikacji lub adres URL logowania). **Adres URL odpowiedzi** nie może być dłuższa niż 256 znaków i musi być unikatowa w katalogu.
4.  Wprowadź **identyfikator URI Identyfikatora aplikacji** dla nowej aplikacji usługi Azure AD. Jest to identyfikator logiczny dla aplikacji Azure AD, które są prezentowane w przypadku pojedynczego żądania logowania są wysyłane do usługi Azure AD. Należy pamiętać, że **identyfikator URI Identyfikatora aplikacji** musi być unikatowa dla każdej aplikacji usługi Azure AD w katalogu. Ten identyfikator nie może być dłuższa niż 256 znaków. Aby uzyskać więcej informacji na temat identyfikator URI Identyfikatora aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  W **role** sekcji, określ ról lub uprawnień niestandardowych dla aplikacji Azure AD.
6.  Wybierz pozycję **Zapisz**.

Po dodaniu lub utworzyć aplikację usługi Azure AD możesz wrócić do **użytkowników** sekcji, a następnie wybierz nazwę aplikacji, aby przejrzeć ustawienia aplikacji, w tym identyfikator dzierżawy, identyfikator klienta, adres URL odpowiedzi i identyfikator URI Identyfikatora aplikacji.

#### <a name="remove-an-application"></a>Usuwanie aplikacji

Aby usunąć aplikację z Twojego konta służbowego (dzierżawy usługi Azure AD), przejdź do **użytkowników** (w obszarze **ustawienia konta**), wybierz aplikację, którą chcesz usunąć za pomocą pola wyboru w prawej kolumnie następnie wybierz **Usuń** z dostępnych akcji. Potwierdzenie, że chcesz usunąć wybrane aplikacje pojawi się okno podręczne.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Zarządzanie kluczami dla aplikacji usługi Azure AD

Jeśli aplikacja usługi Azure AD odczytuje i zapisuje dane w usłudze Microsoft Azure AD, będzie musiała klucza. Klucze dla aplikacji usługi Azure AD można utworzyć, edytując informacje o nim w Centrum partnerskim. Można również usunąć klucze, które nie są już potrzebne.

1.  Z **użytkowników** strony (w obszarze **ustawienia konta**), wybierz nazwę aplikacji usługi Azure AD. Zostaną wyświetlone wszystkie aktywne klucze dla aplikacji usługi Azure AD, w tym daty, w którym został utworzony klucz i kiedy wygaśnie. 
2. Aby usunąć klucz, który nie jest już potrzebny, zaznacz **Usuń**.
3.  Aby dodać nowy klucz, zaznacz **Dodaj nowy klucz**.
4.  Zostanie wyświetlony ekran pokazujący **identyfikator klienta** i **wartości klucza**. Pamiętaj drukowania lub kopiowania tych informacji, ponieważ nie będzie można uzyskać do niego dostęp ponownie po opuszczeniu tej strony.
4.  Aby utworzyć więcej klucze, należy zaznaczyć **Dodaj inny klucz**.


### <a name="define-user-roles-and-permissions"></a>Definiowanie ról użytkowników i uprawnień

Następujące role i uprawnienia w programie komercyjnych portalu Marketplace w Centrum partnerskim można przypisać użytkowników w firmie. 

Należy zauważyć, że role dzierżawy usługi Azure Active Directory (AAD) obejmują administratora globalnego, administratora użytkowników i role dostawcy usług Kryptograficznych. Role usługi AAD nie są wszystkich ról, które nie zarządzania dzierżawą i obejmują one MPN administratora, firmy profilu administratora, administrator odwołania, motywacji administratora i motywacji użytkownika.


|**Rola**|**Uprawnienia**|
|----------------------------------|:---------------------------------|
|Administrator globalny|• Dostęp wszystkich usług firmy Microsoft konta/z pełnymi uprawnieniami
|      |• Tworzyć bilety pomocy technicznej dla Centrum partnerskiego
||• Wyświetlanie umów, cenniki i oferty
||• Wyświetlanie, tworzenie i zarządzanie nimi użytkowników z firm partnerskich|
|maszyny wirtualnej|• Mogą korzystać ze wszystkich funkcji konta Microsoft, z wyjątkiem ustawienia opodatkowanie, a informacje dotyczące wypłat
|      |• Można zarządzać użytkownikami, ról i pracować kont (dzierżawcy)|
|Developer|• Przekaż pakiety, przesłać aplikacje i dodatki i wyświetlić raport użycia, aby uzyskać szczegółowe informacje z danych telemetrycznych
|      |• Nie można uzyskać dostępu finansowych ustawienia konta lub informacji|
|Współautor biznesowych|• Mogą uzyskiwać dostęp do informacji finansowych i Ustaw szczegóły cennika
|      |• Nie można utworzyć lub Prześlij nowe aplikacje i dodatki|
|Współautor finansowych|• Mogą wyświetlać informacje dotyczące wypłat raporty
|      |• Nie może wprowadzać zmian do aplikacji lub ustawienia|
|Marketer|• Mogą odpowiadać na przeglądy wykonywane przez klientów i raportach finansowych
|      |• Nie może wprowadzać zmian do aplikacji lub ustawienia|

Aby uzyskać więcej informacji na temat zarządzania rolami i uprawnieniami w innych obszarach Centrum partnerskie, takich jak Azure Active Directory (AD), Cloud Solution Provider (CSP), dostawcy Panelu sterowania (CPV), gości lub Microsoft Partner Network (MPN), zobacz [przypisania Role użytkowników i uprawnienia w Centrum partnerskim](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Zarządzanie dzierżawami

Dzierżawy usługi Azure Active Directory (AD), określane również jako "konto służbowe" w tej dokumentacji jest reprezentacja organizacji w witrynie Azure portal i pomaga w zarządzaniu konkretnego wystąpienia usług chmurowych firmy Microsoft do wewnętrznych i użytkowników zewnętrznych. Jeśli do usługi w chmurze firmy Microsoft, takich jak Azure, Microsoft Intune i Office 365, zasubskrybowane przez organizację dzierżawę usługi Azure AD zostało ustanowione dla Ciebie. 

Można skonfigurować wiele dzierżaw za pomocą Centrum partnerskiego. Każdy użytkownik mający **Menedżera** rola w ramach konta Centrum partnerskiego będzie miała możliwość dodawania i usuwania dzierżaw usługi Azure AD z konta.  

### <a name="add-an-existing-tenant"></a>Dodaj istniejącą dzierżawę

Aby skojarzyć innej dzierżawy usługi Azure AD przy użyciu danych konta Centrum partnerów:

1.  Z **dzierżaw** strony (w obszarze **ustawienia konta**), wybierz opcję **skojarzyć innej dzierżawy usługi Azure AD**.
2. Wprowadź swoje poświadczenia usługi Azure AD dla dzierżawy, który chcesz skojarzyć.
3. Zweryfikuj nazwę organizacji i domeny dla dzierżawy usługi Azure AD. Aby ukończyć skojarzenia, wybrać **Potwierdź**.

Jeśli skojarzenie zakończy się pomyślnie, następnie będzie gotowy do dodawania użytkowników i zarządzać nimi konta w **użytkowników** sekcji Centrum partnerskiego.

### <a name="create-a-new-tenant"></a>Utworzenie nowej dzierżawy

Aby utworzyć dzierżawę marki nowej usługi Azure AD przy użyciu danych konta Centrum partnerów:

1.  Z **dzierżaw** strony (w obszarze **ustawienia konta**), wybierz opcję **dzierżawa usługi Azure AD Utwórz nowy**.
2. Wprowadź informacje o katalogu dla nowej usługi Azure AD:
    - **Nazwa domeny**: Unikatową nazwę, która użyjemy dla Twojej domeny usługi Azure AD wraz z ". onmicrosoft.com". Na przykład jeśli wprowadzono "przykład" domenę usługi Azure AD będzie "example.onmicrosoft.com".
    - **Kontaktowy adres e-mail**: Adres e-mail, w którym firma Microsoft mogą się skontaktować o Twoim koncie w razie potrzeby.
    - **Informacje o koncie użytkownika administratora globalnego**: Imię, ostatni nazwę, nazwę użytkownika i hasło, których chcesz użyć dla nowego konta administratora globalnego.
3. Wybierz **Utwórz** aby upewnić się, nowe informacje dotyczące domeny i konta.
4. Zaloguj się przy użyciu nowej platformy Azure AD administratora globalnego w nazwę użytkownika i hasło, aby rozpocząć [dodawania użytkowników i zarządzanie nimi](#manage-users).

Aby uzyskać więcej informacji na temat tworzenia nowych dzierżaw w ramach witryny Azure portal, a nie za pośrednictwem portalu Centrum partnerskiego, zobacz artykuł [utworzyć nową dzierżawę usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Usunięcie dzierżawy

Aby usunąć dzierżawę z konta w Centrum partnerskim, Znajdź jego nazwę na **dzierżaw** strony (w **ustawienia konta**), a następnie wybierz **Usuń**. Zostanie wyświetlony monit o potwierdzenie, czy chcesz usunąć dzierżawy. Po wykonaniu tej czynności żaden użytkownik w tej dzierżawie będą mogli logować się do konta w Centrum partnerskim, a wszystkie uprawnienia, które zostały skonfigurowane dla tych użytkowników zostaną usunięte.

Po usunięciu dzierżawy wszyscy użytkownicy, którzy zostali dodani do konta w Centrum partnerskim z tej dzierżawy już nie będzie można zalogować się do konta.

> [!TIP]
> Nie można usunąć dzierżawę, jeśli zalogowania się do Centrum partnerskiego przy użyciu konta w tej samej dzierżawy. Aby usunąć dzierżawę, musisz zarejestrować się do Centrum partnerskiego jako **Menedżera** do innej dzierżawy, który jest skojarzony z kontem. W przypadku tylko jedną dzierżawą skojarzone z kontem tej dzierżawy można usunąć tylko po zarejestrowaniu się przy użyciu konta Microsoft, które otworzyć konto.


## <a name="agreements"></a>Umowy

**Umów** sekcji Centrum partnerskiego (w obszarze **ustawienia konta**) umożliwia wyświetlanie listy publikowania umowy, które zostały autoryzowane. Umowy te są wyświetlane zgodnie z nazwę i numer wersji, w tym datę jego zostało zaakceptowane i nazwę użytkownika, który zaakceptowania Umowy. 

**Wymagane przeprowadzenie kolejnych czynności** może pojawić się w górnej części tej strony, jeśli są dostępne aktualizacje umowy, które wymagają Twojej uwagi. Aby zaakceptować zaktualizowane umowy, najpierw odczytać połączonej wersji umowę, a następnie wybierz **Zaakceptuj umowę**. 

Aby uzyskać informacji na temat umów Cloud Solution Provider (CSP) w Centrum partnerskim, odwiedź stronę [umowy chmury firmy Microsoft, region i język](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie nowej oferty SaaS](./create-new-saas-offer.md)

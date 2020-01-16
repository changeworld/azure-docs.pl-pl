---
title: Wprowadzenie do witryny Azure EA Portal
description: W tym artykule wyjaśniono, jak klienci platformy Azure z umową EA używają witryny Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ace3c251d979a67666d2aaf01dca01e257bed66b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992233"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Wprowadzenie do witryny Azure EA Portal

Ten artykuł ułatwia bezpośrednim i pośrednim klientom platformy Azure z umową EA rozpoczęcie korzystania z [witryny Azure EA Portal](https://ea.azure.com) dzięki podstawowym informacjom na następujące tematy:

- Struktura witryny Azure EA Portal.
- Role używane w witrynie Azure EA Portal.
- Jak zacząć tworzyć subskrypcje.
- Analizowanie kosztów w witrynach Azure EA Portal i Azure Portal.

Oto wideo przedstawiające pełną sesję dołączania do witryny Azure EA Portal:

[Wideo dotyczące dołączania w witrynie Azure EA Portal](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Hierarchia witryny Azure EA Portal

Hierarchia witryny Azure EA Portal obejmuje następujące elementy:

**Witrynę Microsoft Azure EA Portal** — witryna Azure EA Portal to portal zarządzania online, który ułatwia zarządzanie kosztami usług w ramach umowy EA na platformie Azure. Służy do tworzenia hierarchii umowy EA platformy Azure, w tym działów, kont i subskrypcji. Służy również do uzgadniania kosztów używanych usług, pobierania raportów użycia i wyświetlania cenników. Ponadto umożliwia tworzenie kluczy interfejsu API dla rejestracji.

**Działy** — tworzenie działów, aby ułatwić podział kosztów na logiczne grupy, a następnie ustawianie budżetu lub limitu przydziału na poziomie działu.

**Konta** — konta są jednostkami organizacyjnymi w witrynie Azure EA Portal i służą do zarządzania subskrypcjami. Konta są również używane na potrzeby raportowania.

**Subskrypcje** — subskrypcje to najmniejsze jednostki w witrynie Azure EA Portal. Są to kontenery dla usług platformy Azure zarządzanych przez administratora usługi.

Na poniższym diagramie przedstawiono proste hierarchie witryny Azure EA Portal.

![Diagram prostych hierarchii witryny Azure EA Portal](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role użytkownika przedsiębiorstwa

W celu administrowania usługami platformy Azure w ramach rejestracji istnieje pięć odrębnych ról użytkowników administracyjnych przedsiębiorstwa:

- Administrator przedsiębiorstwa
- Administrator działu
- Właściciel konta
- Administrator usługi
- Kontakt z powiadomieniem

Role są używane do wykonywania zadań w dwóch różnych witrynach Microsoft Azure Portal. Witryna Azure EA Portal (https://ea.azure.com) jest używana do zarządzania rozliczeniami i kosztami. Witryna Azure Portal (https://portal.azure.com) służy do zarządzania usługami platformy Azure.

Role użytkowników są skojarzone z kontem użytkownika. Aby zweryfikować autentyczność użytkowników, każdy użytkownik musi mieć prawidłowe konto służbowe lub Microsoft. Upewnij się, że każde konto jest skojarzone z używanym adresem e-mail. Powiadomienia konta są wysyłane na adres e-mail.

Podczas konfigurowania użytkowników do roli administratora przedsiębiorstwa można przypisać wiele kont służbowych lub Microsoft. Można jednak przypisać tylko jedno konto służbowe lub konto Microsoft do roli właściciela konta. Ponadto jedno konto służbowe lub konto Microsoft może mieć przypisane role zarówno administratora przedsiębiorstwa, jak i właściciela konta.

### <a name="enterprise-administrator"></a>Administrator przedsiębiorstwa

Rola administratora przedsiębiorstwa ma najwyższy poziom dostępu. Użytkownicy z tą rolą mogą wykonywać następujące czynności:

- Zarządzanie kontami i właścicielami kont
- Zarządzanie innymi administratorami przedsiębiorstwa
- Zarządzanie administratorami działów
- Zarządzanie kontaktami dla powiadomień
- Wyświetlanie użycia na wszystkich kontach
- Wyświetlanie nienaliczonych opłat na wszystkich kontach

W ramach rejestracji przedsiębiorstwa może być wielu administratorów przedsiębiorstwa. Administratorom przedsiębiorstwa można przyznać dostęp tylko do odczytu. Wszyscy oni dziedziczą rolę administratora działu.

### <a name="department-administrator"></a>Administrator działu

Użytkownicy z tą rolą mogą wykonywać następujące czynności:

- Tworzenie działów i zarządzanie nimi
- Tworzenie nowych właścicieli kont
- Wyświetlanie szczegółów użycia dla działów, którymi zarządzają
- Wyświetlanie kosztów w przypadku posiadania niezbędnych uprawnień

Dla każdej rejestracji przedsiębiorstwa można mieć wielu administratorów działów.

Administratorom działów można przyznać dostęp tylko do odczytu. Aby przyznać dostęp tylko do odczytu, edytuj lub utwórz nowego administratora działu i ustaw wartość **Tak** dla opcji tylko do odczytu.

### <a name="account-owner"></a>Właściciel konta

Użytkownicy z tą rolą mogą wykonywać następujące czynności:

- Tworzenie subskrypcji i zarządzanie nimi
- Zarządzanie administratorami usług
- Wyświetlanie użycia dla subskrypcji

Każde konto wymaga unikatowego konta służbowego lub konta Microsoft. Aby uzyskać więcej informacji na temat ról administracyjnych w witrynie Azure EA Portal, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrator usługi

Administrator usługi ma uprawnienia do zarządzania usługami w witrynie Azure Portal i przypisywania użytkowników do roli współadministratora.

### <a name="notification-contact"></a>Kontakt z powiadomieniem

Kontakt z powiadomieniem odbiera powiadomienia dotyczące użycia związane z rejestracją.

## <a name="activate-your-enrollment"></a>Aktywowanie rejestracji

W celu aktywowania usługi początkowy administrator przedsiębiorstwa otwiera witrynę Azure EA Portal pod adresem [https://ea.azure.com](https://ea.azure.com) i loguje się przy użyciu adresu e-mail z wiadomości e-mail z zaproszeniem.

Jeśli użytkownik został skonfigurowany jako administrator EA, nie musisz otrzymywać wiadomości e-mail z aktywacją, aby zalogować się do portalu Azure EA. Możesz kontynuować [https://ea.azure.com](https://ea.azure.com) i zalogować się przy użyciu swojego adresu e-mail (służbowego, szkoły lub identyfikatora na żywo) i hasła.

Jeśli masz więcej niż jedną rejestrację, wybierz tę, którą chcesz aktywować. Domyślnie są wyświetlane tylko aktywne rejestracje. Aby wyświetlić historię rejestracji, wyczyść opcję **Aktywne** w prawym górnym rogu witryny Azure EA Portal.

W obszarze Rejestracja wyświetlany jest stan **Aktywne**.

![Przykład pokazujący aktywną rejestrację](./media/ea-portal-get-started/ea-enrollment-status.png)

Tylko istniejący administratorzy przedsiębiorstwa platformy Azure mogą tworzyć innych administratorów przedsiębiorstwa.

### <a name="create-another-enterprise-admin"></a>Tworzenie innego administratora przedsiębiorstwa

- Zaloguj się do witryny [Azure EA Portal](https://ea.azure.com) i przejdź do pozycji **Zarządzanie** > **Szczegóły rejestracji**, a następnie kliknij pozycję **+ Dodaj administratora** w prawym górnym rogu strony.

Upewnij się, że masz adresy e-mail użytkownika i preferowaną metodę uwierzytelniania, na przykład uwierzytelnianie służbowe lub konto Microsoft. Potrzebujesz tych informacji, aby dodać użytkownika.

Jeśli nie jesteś administratorem umowy EA, skontaktuj się z administratorem umowy EA, aby zażądać dodania Ciebie do rejestracji. Po dodaniu do rejestracji otrzymasz wiadomość e-mail dotyczącą aktywacji.

Jeśli administrator umowy EA nie może Ci pomóc, utwórz [wniosek o pomoc techniczną witryny Azure EA Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Podaj następujące informacje:

- Numer rejestracji
- Adres e-mail do dodania i typ uwierzytelnienia (konto służbowe lub konto Microsoft)
- Wiadomość e-mail dotycząca zatwierdzenia od istniejącego administratora umowy EA
  - Jeśli istniejący administrator umowy EA jest niedostępny, skontaktuj się z partnerem lub doradcą ds. oprogramowania, aby zażądać zmiany danych kontaktowych za pomocą narzędzia VLSC.

Aby uzyskać więcej informacji na temat ról administracyjnych w przedsiębiorstwie, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Tworzenie działu w witrynie Azure EA Portal

Administratorzy przedsiębiorstwa i administratorzy działów wykorzystują działy do organizowania oraz tworzenia raportów dla przedsiębiorstwa dotyczących usług platformy Azure i użycia wg działów oraz centrów kosztów. Administrator przedsiębiorstwa może wykonywać następujące czynności:

- Dodawanie lub usuwanie działów
- Kojarzenie konta z działem
- Tworzenie administratorów działów
- Zezwalanie administratorom działów na wyświetlanie cen i kosztów

Administrator działu może dodawać nowe konta do swoich działów. Może on usuwać konta ze swoich działów, ale nie z rejestracji.

Aby dodać dział:

1. W obszarze nawigacji po lewej stronie kliknij pozycję **Zarządzanie**.
2. Kliknij kartę **Dział**, a następnie kliknij pozycję **+ Dodaj dział** i wpisz wymagane informacje.
3. Nazwa działu jest jedynym polem wymaganym. Musi zawierać co najmniej trzy znaki.
4. Po zakończeniu kliknij pozycję **Dodaj**.

## <a name="add-a-department-admin"></a>Dodawanie administratora działu

Po utworzeniu działu administrator przedsiębiorstwa platformy Azure może dodawać administratorów działów i kojarzyć poszczególnych z nich z działami. Administrator działu może wykonywać następujące czynności:

- Tworzenie innych administratorów działów
- Wyświetlanie i edytowanie właściwości działu, takich jak nazwa lub centrum kosztów
- Dodawanie konta do swoich działów
- Usuwanie kont ze swoich działów
- Pobieranie szczegółów użycia dla swoich działów
- Wyświetlanie miesięcznego użycia i opłat dla swojego działu, jeśli administrator przedsiębiorstwa udzielił mu uprawnień<sup>1</sup>

### <a name="to-add-a-department-admin"></a>Aby dodać administratora działu

Jako administrator przedsiębiorstwa:

1. W obszarze nawigacji po lewej stronie kliknij pozycję **Zarządzanie**.
2. Kliknij kartę **Dział**, a następnie kliknij dział.
3. Kliknij pozycję **+ Dodaj administratora** i dodaj wymagane informacje.
4. W przypadku dostępu tylko do odczytu dla opcji **Tylko do odczytu** ustaw wartość **Tak**, a następnie kliknij przycisk **Dodaj**.

![Przykład przedstawiający okno dialogowe Dodawanie administratora działu](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Aby ustawić dostęp tylko do odczytu

Administratorom działów można przyznać dostęp tylko do odczytu. Podczas tworzenia nowego administratora działu:

- Dla opcji tylko do odczytu ustaw wartość **Tak**.

Aby edytować istniejącego administratora działu:

1. Wybierz dział, a następnie kliknij symbol ołówka obok **administratora działu**, którego chcesz edytować.
2. Dla opcji tylko do odczytu ustaw wartość **Tak**. Następnie kliknij przycisk **Zapisz**.

Użytkownicy z rolą administratora przedsiębiorstwa automatycznie uzyskują uprawnienia administratora działu.

<sup>1</sup> Jeśli masz uprawnienia do wyświetlania miesięcznego użycia i opłat dla działu, ale nie widzisz ich, skontaktuj się z partnerem.

## <a name="add-an-account"></a>Dodaj konto

Struktura subskrypcji i konto mają wpływ na sposób administrowania nimi oraz sposób ich wyświetlania na fakturach i w raportach. Przykłady typowej organizacji obejmują struktury składające się z pionów biznesowych, zespołów funkcjonalnych i lokalizacji geograficznych.

Aby dodać konto:

1. W witrynie Azure EA Portal kliknij pozycję **Zarządzanie** w obszarze nawigacji po lewej stronie.
2. Kliknij kartę **Konto**, a następnie na stronie **Konto** kliknij pozycję **+Dodaj konto**.
3. Wybierz dział lub pozostaw konto jako nieprzypisane, a następnie wybierz żądany typ uwierzytelniania.
4. Wpisz przyjazną nazwę, która będzie używana do identyfikowania konta w raportach.
5. Wpisz **Adres e-mail właściciela konta**, który ma zostać skojarzony z nowym kontem.
6. Potwierdź adres e-mail, a następnie kliknij pozycję **Dodaj**.

![Przykład przedstawiający listę kont i opcję +Dodaj konto](./media/ea-portal-get-started/create-ea-add-an-account.png)

Możesz dodać kolejne konto, klikając pozycję **Dodaj kolejne konto** lub klikając pozycję **Dodaj** w prawym dolnym rogu lewego paska narzędzi.

Aby potwierdzić własność konta:

1. Zaloguj się do witryny Azure EA Portal.
1. Potwierdź własność konta, wyświetlając stan. Stan powinien ulec zmianie z **Oczekujące** na **Data początkowa/końcowa**. Data początkowa/końcowa to data pierwszego logowania użytkownika i data zakończenia obowiązywania umowy.
1. Gdy zostanie wyświetlony komunikat „Ostrzeżenie”, właściciel konta musi kliknąć przycisk **Kontynuuj**, aby aktywować konto podczas pierwszego logowania się do witryny Azure EA Portal.


## <a name="change-account-owner"></a>Zmienianie właściciela konta

Administratorzy przedsiębiorstwa mogą korzystać z witryny Azure EA Portal do przenoszenia własności konta subskrypcji podczas rejestracji. Akcja ta przenosi wszystkie subskrypcje z konta użytkownika źródłowego na konto użytkownika docelowego.

Ważne kwestie dotyczące przenoszenia informacji o koncie użytkownika:

- Obsługiwane jest przenoszenie z konta służbowego na inne konto służbowe.
- Obsługiwane jest przenoszenie z konta Microsoft na konto służbowe.
- Przenoszenie z konta służbowego na konto Microsoft nie jest obsługiwane.
- Obsługiwane jest przenoszenie z konta Microsoft na inne konto Microsoft. Konto docelowe musi być prawidłowym kontem komercyjnym platformy Azure, aby być prawidłowym miejscem docelowym umożliwiającym przenoszenie. W przypadku nowych kont przed zalogowaniem do witryny Azure EA Portal zostanie wyświetlony monit o utworzenie konta komercyjnego platformy Azure. W przypadku istniejących kont, aby konto było uprawnione, należy najpierw utworzyć nową subskrypcję platformy Azure.
- Po zakończeniu przenoszenia subskrypcji firma Microsoft aktualizuje właściciela konta.

Zasady kontroli dostępu opartej na rolach:

- Tylko przenoszenie subskrypcji platformy Azure między dwoma identyfikatorami organizacyjnymi w tej samej dzierżawie zachowuje istniejące zasady kontroli dostępu opartej na rolach (RBAC) na platformie Azure, przypisania roli administratora usługi i przypisania roli współadministratora. Inne przenoszenie subskrypcji powoduje utratę zasad kontroli dostępu opartej na rolach oraz przypisań roli współadministratora i roli administratora usługi. Zasady i role administratorów nie są przenoszone między różnymi katalogami. Administratorzy usługi są aktualizowani do właściciela konta docelowego.
- W przypadku przenoszenia subskrypcji między dwoma identyfikatorami organizacyjnymi w tej samej dzierżawie zasady kontroli dostępu opartej na rolach i istniejące role administratora usługi oraz współadministratora są zachowywane.

Przed zmianą właściciela konta:

1. Wyświetl kartę **Konto** i zidentyfikuj konto źródłowe. Konto źródłowe musi być aktywne.
2. Zidentyfikuj konto docelowe. Musi być aktywne.

Aby przenieść własność konta dla wszystkich subskrypcji:

1. W obszarze nawigacji po lewej stronie kliknij pozycję **Zarządzanie**.
2. Kliknij kartę **Konto** i zatrzymaj wskaźnik myszy na koncie.
3. Kliknij symbol zmiany właściciela konta po prawej stronie. Symbol przedstawia osobę.
4. Wybierz kwalifikujące się konto, a następnie kliknij pozycję **Dalej**.
5. Potwierdź przeniesienie i kliknij pozycję **Prześlij**.

![Obraz przedstawiający symbol Zmiana właściciela konta](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Aby przenieść własność konta dla jednej subskrypcji:

1. W obszarze nawigacji po lewej stronie kliknij pozycję **Zarządzanie**.
2. Kliknij kartę **Konto** i zatrzymaj wskaźnik myszy na koncie.
3. Kliknij symbol przenoszenia subskrypcji po prawej stronie. Symbol przedstawia stronę.
4. Wybierz kwalifikującą się subskrypcję, a następnie kliknij pozycję **Dalej**.
5. Potwierdź przeniesienie, a następnie kliknij pozycję **Prześlij**.

![Obraz przedstawiający symbol Przenoszenie subskrypcji](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Oto wideo przedstawiające zarządzanie użytkownikami w witrynie Azure EA Portal:

[Wideo dotyczące zarządzania użytkownikami w witrynie Azure EA Portal](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

Właściciele kont mogą wyświetlać subskrypcje i zarządzać nimi. Za pomocą subskrypcji można udzielać zespołom w organizacji dostępu do środowisk deweloperskich i projektów. Mogą to być środowiska testowe, produkcyjne, deweloperskie i przejściowe. Utworzenie różnych subskrypcji dla każdego środowiska aplikacji pomaga zabezpieczyć każde środowisko. Do każdej subskrypcji można także przypisać inne konto administratora usługi. Subskrypcje można kojarzyć z dowolną liczbą usług. Właściciel konta tworzy subskrypcje i przypisuje konto administratora usługi do każdej subskrypcji na swoim koncie.

### <a name="add-a-subscription"></a>Dodawanie subskrypcji

Aby dodać subskrypcję, skorzystaj z poniższych informacji.

Przy pierwszym dodawaniu subskrypcji do konta zostanie wyświetlona prośba o zaakceptowanie umowy MOSA oraz planu taryfowego. Chociaż nie mają one zastosowania do klientów z umową Enterprise Agreement, są potrzebne do utworzenia subskrypcji. Poprawka do rejestracji umowy Microsoft Azure Enterprise zastępuje powyższe elementy, a Twoja relacja umowna nie ulega zmianie. Po wyświetleniu monitu zaznacz pole informujące o zaakceptowaniu warunków.

Wszystkie nowe subskrypcje są tworzone z domyślną nazwą subskrypcji _Microsoft Azure Enterprise_. Możesz zaktualizować nazwę subskrypcji, aby odróżnić ją od innych subskrypcji w swojej rejestracji. Pozwoli to też zagwarantować, że będzie rozpoznawalna w raportach na poziomie przedsiębiorstwa.

Aby dodać subskrypcję:

1. Zaloguj się do konta w witrynie Azure EA Portal.
2. Kliknij kartę **Administrator**, a następnie kliknij pozycję **Subskrypcja** w górnej części strony.
2. Sprawdź fakt zalogowania jako właściciel konta.
3. Kliknij pozycję **+Dodaj subskrypcję**, a następnie kliknij pozycję **Kup**.
  Przy pierwszym dodawaniu subskrypcji do konta musisz podać swoje informacje kontaktowe. Podczas dodawania kolejnych subskrypcji Twoje informacje kontaktowe będą dodawane automatycznie.
4. Kliknij pozycję **Subskrypcje**, wybierz utworzoną subskrypcję, a następnie kliknij pozycję **Edytuj szczegóły subskrypcji**.
5. Zaktualizuj pozycje **Nazwa subskrypcji** i **Administrator usługi**, a następnie wybierz znacznik wyboru.
  Nazwa subskrypcji jest wyświetlana w raportach i jest nazwą projektu skojarzonego z subskrypcją w portalu deweloperskim.

Nowe subskrypcje mogą pojawić się na liście subskrypcji po upływie do 24 godzin. Po utworzeniu subskrypcji można wykonywać następujące czynności:

- [Edytowanie szczegółów subskrypcji ](https://account.azure.com/Subscriptions)
- [Zarządzanie usługami subskrypcji](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Przenoszenie subskrypcji umowy EA do subskrypcji płatnej zgodnie z rzeczywistym użyciem

Aby przenieść subskrypcję umowy EA do pojedynczej subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem, należy utworzyć nowy wniosek o pomoc techniczną w witrynie Azure EA Portal. Aby utworzyć żądanie pomocy technicznej, kliknij pozycję **+ Nowe żądanie pomocy technicznej** w obszarze Pomoc i obsługa techniczna.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Kojarzenie istniejącego konta z subskrypcją płatności zgodnie z rzeczywistym użyciem

Jeśli masz już istniejące konto Microsoft Azure w witrynie Microsoft Azure Portal, wprowadź skojarzone konto Microsoft lub konto służbowe, aby skojarzyć je z rejestracją umowy Enterprise Agreement.

### <a name="associate-an-existing-account"></a>Kojarzenie istniejącego konta

1. W witrynie Enterprise Portal kliknij pozycję **Zarządzanie**.
1. Kliknij kartę **Konto**.
1. Kliknij pozycję **+ Dodaj konto**.
1. Wprowadź konto Microsoft lub konto służbowe skojarzone z istniejącym kontem.
1. Potwierdź konto Microsoft lub konto służbowe skojarzone z istniejącym kontem.
1. Podaj nazwę, której chcesz używać do identyfikowania tego konta na potrzeby raportowania.
1. Kliknij pozycję **Dodaj**.
1. Możesz dodać kolejne konto, wybierając ponownie opcję **+ Dodaj konto**, lub wrócić do strony głównej, wybierając przycisk **Administrator**.
1. Jeśli klikniesz, aby wyświetlić stronę **Konto**, nowo dodane konto zostanie wyświetlone ze stanem **Oczekiwanie**.

### <a name="confirm-account-ownership"></a>Potwierdzanie własności konta

1. Zaloguj się na konto poczty e-mail skojarzone z podanym kontem Microsoft lub kontem służbowym.
1. Otwórz powiadomienie e-mail zatytułowane _„Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing”_ (Zaproszenie do aktywacji konta w usłudze Microsoft Azure w ramach licencjonowania zbiorowego firmy Microsoft).
1. Kliknij link **Log into the Microsoft Azure Enterprise Portal** (Zaloguj się do witryny Microsoft Azure Enterprise Portal) w zaproszeniu.
1. Kliknij pozycję **Zaloguj się**.
1. Wprowadź konto Microsoft lub konto służbowe i hasło, aby zalogować się i potwierdzić własność konta.

### <a name="azure-marketplace"></a>Azure Marketplace

Chociaż większość subskrypcji jest konwertowana ze środowiska płatności zgodnie z rzeczywistym użyciem do środowiska Enterprise Azure, nie dotyczy to usług witryny Azure Marketplace. Aby móc korzystać z pojedynczego widoku wszystkich subskrypcji i opłat, zalecamy dodanie usług witryny Azure Marketplace do witryny Enterprise Portal:

1. Kliknij pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Kliknij kartę **Rejestracja**.
1. Wyświetl sekcję Szczegóły rejestracji.
1. Na prawo od pola Azure Marketplace kliknij ikonę ołówka, aby włączyć, i naciśnij przycisk **Zapisz**.

Właściciel konta może teraz kupić subskrypcje witryny Azure Marketplace, które posiadał poprzednio w ramach płatności zgodnie z rzeczywistym użyciem.

Po aktywowaniu nowych subskrypcji witryny Azure Marketplace w ramach rejestracji anuluj subskrypcje witryny Marketplace utworzone w środowisku płatności zgodnie z rzeczywistym użyciem. Ten krok ma krytyczne znaczenie, aby subskrypcje witryny Marketplace nie przeszły w nieprawidłowy stan, gdy wygaśnie instrument płatniczy środowiska płatności zgodnie z rzeczywistym użyciem.

### <a name="msdn"></a>MSDN

Subskrypcje MSDN są automatycznie konwertowane na ofertę MSDN Tworzenie/testowanie, a oferta EA utraci wszystkie istniejące środki pieniężne.

### <a name="azure-in-open"></a>Licencja Azure w ramach programu licencjonowania Open

Skojarzenie subskrypcji licencji Azure w ramach programu licencjonowania Open z umową EA spowoduje utratę wszystkich niewykorzystanych środków licencji Azure w ramach programu licencjonowania Open. W celu uniknięcia możliwej utraty środków zaleca się, aby klienci wykorzystali wszystkie środki w subskrypcji licencji Azure w ramach programu licencjonowania Open przed dodaniem konta do umowy EA.  

### <a name="accounts-with-support-subscriptions"></a>Konta z subskrypcjami pomocy technicznej

Podczas dodawania do witryny Enterprise Portal istniejących kont, które mają subskrypcję pomocy technicznej (i nie mają jeszcze subskrypcji pomocy technicznej umowy EA), należy pamiętać, że subskrypcja pomocy technicznej MOSA nie zostanie przeniesiona automatycznie i trzeba będzie ponownie kupić pomoc techniczną w ramach umowy EA. Okres prolongaty obowiązywania pomocy technicznej zostanie przedłużony do końca kolejnego miesiąca w celu zapewnienia czasu na ponowne zamówienie pomocy technicznej.

## <a name="view-usage-summary-and-download-reports"></a>Wyświetlanie podsumowania użycia i pobieranie raportów

Administratorzy przedsiębiorstwa mogą wyświetlać podsumowanie danych użycia, wykorzystanych zobowiązań pieniężnych i opłat związanych z dodatkowym użyciem w witrynie Azure EA Portal. Opłaty są wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji.

Aby wyświetlić szczegółowe informacje o użyciu dla określonych kont:

Pobierz szczegółowy raport użycia. Kliknij pozycję **raporty** , a następnie kliknij kartę **pobieranie użycia** . Na liście raportów kliknij pozycję **Pobierz** , aby wyświetlić miesięczny raport, który chcesz uzyskać.

Raport nie zawiera żadnych należnych podatków. Może wystąpić opóźnienie do ośmiu godzin od momentu, w którym wystąpiło użycie, do chwili, gdy zostanie ono odzwierciedlone w raporcie.

Aby wyświetlić raporty i wykresy podsumowania użycia:

1. W witrynie Azure EA Portal w lewym obszarze nawigacji kliknij pozycję **Raporty** i wyświetl kartę **Podsumowanie użycia**.  
  ![Tworzenie i wyświetlanie podsumowania użycia i pobieranie raportów](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Wybierz termin zobowiązania.
3. Przełączaj między ustawieniem **M** (miesięczne) i **C** (niestandardowe) w prawym górnym rogu strony, aby wyświetlić **Podsumowanie użycia** z niestandardowymi datami rozpoczęcia i zakończenia.  
  ![Tworzenie i wyświetlanie podsumowania użycia i pobieranie raportów w widoku niestandardowym](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Wybierz okres lub miesiąc na wykresie, aby wyświetlić dodatkowe szczegóły.
5. Wykres przedstawia użycie miesiąc do miesiąca z podziałem na wykorzystane użycie, nadpłatę za usługę, opłaty naliczane osobno i opłaty za korzystanie z witryny Marketplace.
6. W wybranym miesiącu filtruj według działów, kont i subskrypcji poniżej wykresu.
7. Przełączaj się między pozycjami **Opłata według usług** i **Opłata według hierarchii**.
8. Rozwijaj i zwijaj pozycje **Usługa platformy Azure**, **Opłaty rozliczane oddzielnie** i **Azure Marketplace**, aby wyświetlać szczegóły.

Oto wideo przedstawiające sposób wyświetlania użycia:

[Wideo dotyczące użycia w witrynie Azure EA Portal](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Pobieranie raportów CSV

Administratorzy przedsiębiorstwa używają strony Pobieranie raportu miesięcznego do pobierania kilku raportów jako plików CSV. Obejmują one następujące raporty:

- Saldo i opłata
- Szczegółowe zestawienie użycia
- Opłaty za korzystanie z witryny Marketplace
- Arkusz cen

Aby pobrać raporty:


1. W witrynie Azure EA Portal kliknij pozycję **Raporty**.
2. Kliknij pozycję **Pobieranie danych użycia** w górnej części strony.
3. Wybierz pozycję **Pobierz** obok raportu za dany miesiąc.

Może wystąpić opóźnienie do pięciu dni od daty, kiedy wystąpiło użycie, do daty, gdy zostanie ono wykazane w raporcie.

Użytkownicy pobierający pliki CSV przy użyciu przeglądarki Safari do programu Excel mogą napotkać błędy formatowania. Aby uniknąć błędów, otwórz plik za pomocą edytora tekstu.

![Przykład pokazujący stronę Pobieranie danych użycia](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Oto wideo pokazujące, jak pobrać informacje o użyciu:

[Wideo dotyczące użycia w witrynie Azure EA Portal](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Zaawansowane pobieranie raportu

W przypadku raportowania dla określonych zakresów dat lub kont można użyć funkcji zaawansowanego pobierania raportu. Od 30 sierpnia 2016 r. format pliku wyjściowego został zmieniony z xlsx na csv w celu przystosowania do większych zestawów rekordów.

1. Wybierz pozycję **Zaawansowane pobieranie raportu**.
1. Wybierz pozycję **Odpowiedni zakres dat**.
1. Wybierz pozycję **Odpowiednie konta**.
1. Wybierz pozycję **Żądaj danych użycia**.
1. Wybieraj przycisk **Odśwież** do momentu aktualizacji stanu raportu na **Pobierz**.
1. Pobierz raport.

## <a name="ea-term-glossary"></a>Słownik terminów umowy EA

- **Konto**: jednostka organizacyjna w portalu Azure EA używana do administrowania subskrypcjami i wykorzystywana do raportowania.
- **Właściciel konta**: osoba zidentyfikowana do zarządzania subskrypcjami i administratorami usługi w Microsoft Azure. Może wyświetlać dane użycia na tym koncie i w skojarzonych z nim subskrypcjach.
- **Subskrypcja zmian**: jeden rok lub subskrypcja coterminous w ramach zmiany rejestracji.
- **Zobowiązanie**: zobowiązanie rocznej kwoty pieniężnej dla usług Microsoft Azure z obniżoną stawką zobowiązania na korzystanie z tej przedpłaty.
- **Administrator działu**: osoby identyfikowane w celu zarządzania działach, tworzenia nowych kont i właścicieli kont, wyświetlania szczegółów użycia dla działów, którymi zarządzają, i wyświetlania kosztów po udzieleniu uprawnień.
- **Numer rejestracji**: unikatowy identyfikator dostarczony przez firmę Microsoft w celu zidentyfikowania konkretnej rejestracji skojarzonej z umową Enterprise Agreement.
- **Administrator przedsiębiorstwa**: te osoby zidentyfikowano w celu zarządzania wydziałami i właścicielami i kontami oraz właścicielami kont na Microsoft Azure. Ma możliwość zarządzania administratorami przedsiębiorstwa, a także wyświetlania danych użycia, rozliczonych ilości i nierozliczonych opłat na wszystkich kontach i we wszystkich subskrypcjach skojarzonych z rejestracją przedsiębiorstwa.
- **Umowa Enterprise Agreement**: Umowa licencyjna firmy Microsoft dla klientów z scentralizowanym zakupem, którzy chcą standaryzacji całej organizacji w zakresie technologii firmy Microsoft i utrzymania infrastruktury technologii informatycznych na standardowym oprogramowaniu firmy Microsoft.
- **Rejestracja w ramach umowy Enterprise Agreement**: Rejestracja w programie w ramach umowy Enterprise Agreement dostarczająca produkty firmy Microsoft w ramach obniżonych stawek.
- **Konto Microsoft**: usługa oparta na sieci Web, która umożliwia witrynom uczestniczącym uwierzytelnianie użytkownika przy użyciu jednego zestawu poświadczeń.
- **Microsoft Azure poprawka rejestracji w przedsiębiorstwie (poprawka rejestracji)** : Poprawka podpisana przez przedsiębiorstwo, która zapewnia im dostęp do Microsoft Azure w ramach rejestracji w przedsiębiorstwie.
- **Portal Azure EA**: Portal używany przez naszych klientów korporacyjnych do zarządzania kontami Microsoft Azure i powiązane z nimi subskrypcje.
- Użyta **ilość zasobów**: liczba poszczególnych usług Microsoft Azure, które były używane w danym miesiącu.
- **Administrator usługi**: osoba zidentyfikowana w celu uzyskiwania dostępu do subskrypcji i projektów programistycznych oraz zarządzania nimi w portalu Azure EA.
- **Subskrypcja**: reprezentuje subskrypcję portalu Azure EA i jest kontenerem usług Microsoft Azure zarządzanych przez tego samego administratora usługi.
- **Konto służbowe**: w przypadku organizacji, które skonfigurują usługi Active Directory z Federacją w chmurze, a wszystkie konta znajdują się w jednej dzierżawie.

### <a name="enrollment-statuses"></a>Stany rejestracji:

- **Oczekujące**: administrator rejestracji musi zalogować się do portalu Azure EA. Po zalogowaniu rejestracja zostanie przełączona do stanu aktywnego.
- **Aktywne**: Rejestracja jest aktywna, a konta i subskrypcje można utworzyć w portalu Azure EA. Rejestracja pozostanie aktywna do daty zakończenia umowy Enterprise Agreement.
- **Nieokreślony termin rozszerzony**: nieokreślony rozszerzony termin jest realizowany po upływie daty zakończenia umowy Enterprise Agreement. Umożliwia to klientom z umowami EA, którzy wybrali opcję przedłużenia, dalsze korzystanie z platformy Azure przez czas nieokreślony po zakończeniu umowy Enterprise Agreement. Zanim rejestracja umowy EA osiągnie datę zakończenia umowy Enterprise Agreement, administrator rejestracji powinien zdecydować, czy organizacja odnowi rejestrację przez dodanie kolejnego zobowiązania pieniężnego, przeniesie się do nowej rejestracji, przeprowadzi migrację do programu subskrypcji online firmy Microsoft (MOSP, Microsoft Online Subscription Program) lub potwierdzi wyłączenie wszystkich usług skojarzonych z daną rejestracją.
- **Wygasłe**: klient z umową EA został wyłączony z okresu rozszerzonego, a rejestracja EA osiągnęła datę końcową umowy Enterprise, rejestracja wygaśnie, a wszystkie skojarzone usługi zostaną wyłączone.
- **Przetransferowane**: rejestracje, w przypadku których wszystkie powiązane konta i usługi zostały przeniesione do nowej rejestracji, będą wyświetlane ze stanem przesłany. Należy pamiętać, że rejestracje nie są przenoszone automatycznie w przypadku wygenerowania nowego numeru rejestracji podczas odnawiania. Aby nastąpiło automatyczne przeniesienie, należy uwzględnić poprzedni numer rejestracji w dokumentacji odnowienia klienta.

## <a name="get-started-on-azure-ea-faq"></a>Wprowadzenie do umowy EA platformy Azure — często zadawane pytania

Ten dokument zawiera szczegółowe informacje dotyczące typowych pytań, które zadawali klienci podczas procesu dołączania.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Czy można skojarzyć istniejące konto platformy Azure z rejestracją Enterprise?

Tak, możesz. Należy pamiętać, że wszystkie subskrypcje platformy Azure, dla których jesteś właścicielem konta, zostaną przekonwertowane na umowę Enterprise Agreement. Obejmuje to subskrypcje korzystające ze środków miesięcznych (np. Visual Studio, AzurePass, MPN, BizSpark itp.), co oznacza, że miesięczne środki zostaną utracone.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Przypadkowo skojarzono moje istniejące konto platformy Azure z rejestracją Enterprise. W wyniku środki miesięczne zostały utracone. Czy jest możliwe odzyskanie tych środków miesięcznych?

Aby odzyskać korzyść platformy Azure dla indywidualnej subskrypcji programu Visual Studio po uwierzytelnieniu się jako właściciel konta EA, gdy użyjesz tych samych danych logowania dla umowy EA, co dla subskrypcji programu Visual Studio, musisz wykonać następujące czynności:
1. Usuń tego właściciela konta z witryny EA Portal po usunięciu lub przeniesieniu wszelkich posiadanych przez niego subskrypcji platformy Azure i poproś go o ponowne zarejestrowanie się w celu uzyskania indywidualnych korzyści platformy Azure dla programu Visual Studio.
 LUB
1. Usuń subskrybenta programu Visual Studio z witryny administracyjnej w portalu VLSC i ponownie przypisz subskrypcję, korzystając tym razem z innych danych logowania — następnie użytkownik może od nowa zarejestrować się w celu uzyskania indywidualnych korzyści platformy Azure dla programu Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Jakiego typu subskrypcję należy utworzyć?

Witryna EA Portal oferuje dwa typy subskrypcji dla klientów korporacyjnych:

- Microsoft Azure Enterprise — idealne pod kątem:
  - wszelkich zastosowań produkcyjnych
  - najlepszych cen na podstawie wydatków na infrastrukturę
  - Więcej szczegółów można znaleźć pod adresem https://azure.microsoft.com/pricing/enterprise-agreement/
- Enterprise — tworzenie i testowanie. Idealne pod kątem:
  - Wszystkie obciążenia deweloperskie/testowe zespołu
  - Średnie i duże indywidualne obciążenia deweloperskie/testowe
  - dostępu do specjalnych obrazów MSDN i preferencyjnych stawek za usługi
  - Więcej szczegółów można znaleźć pod adresem https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Czy można przenieść własność subskrypcji na inne konto?

Tak, można przenieść własność subskrypcji na inne konto. Jeśli na przykład konto A ma trzy subskrypcje, administrator przedsiębiorstwa może przenieść jedną subskrypcję na konto B, jedną na konto C i jedną na konto D lub wszystkie na konto E.

Po przejściu do witryny EA, kliknięciu pozycji Zarządzaj > Konto i umieszczeniu wskaźnika myszy na pozycji **Konto** (skrajnie na prawo) zostanie wyświetlona opcja Przenoszenie własności (ikona zdjęcia portretowego) i opcja Przenoszenie subskrypcji (ikona listy).

Ta opcja będzie widoczna tylko dla kont aktywnych.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Widzę, że nazwa subskrypcji to domyślnie nazwa oferty. Czy należy zmienić nazwę subskrypcji na coś znaczącego dla mojej organizacji?

Każda utworzona subskrypcja domyślnie będzie miała nazwę wybranego typu oferty. Zalecamy zmianę nazwy subskrypcji na coś, co ułatwi śledzenie subskrypcji.

**Aby zmienić nazwę:**
1. Zaloguj się do witryny [https://account.windowsazure.com](https://account.windowsazure.com).
1. Kliknij listę subskrypcji.
1. Wybierz subskrypcję.
1. Kliknij ikonę **Zarządzaj subskrypcją**.
1. Edytuj szczegóły subskrypcji.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Jak mogę śledzić koszty wygenerowane przez Centrum kosztów?

Aby śledzić koszty wg. Centrum kosztów, należy zdefiniować Centrum kosztów na dowolnym z następujących poziomów:
- Dział
- Konto
- Subskrypcja

W zależności od potrzeb można użyć tego samego Centrum kosztów, aby śledzić użycie i koszty związane z określonym Centrum kosztów.

Aby na przykład śledzić koszty dla określonego projektu, w którym uczestniczy wiele działów, można użyć Centrum kosztów na poziomie subskrypcji w celu śledzenia użycia i kosztów.

Nie można zdefiniować Centrum kosztów na poziomie usługi. Jeśli chcesz śledzić użycie na poziomie usługi, możesz użyć funkcji tagowania dostępnej na poziomie usługi.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Jak mogę śledzić użycie i wydatki dla różnych działów w mojej organizacji?

W ramach rejestracji umowy EA możesz utworzyć tyle działów, ile potrzebujesz. Aby prawidłowo śledzić użycie, należy się upewnić, że subskrypcje nie są współużytkowane przez działy.

Po zakończeniu tworzenia działów i subskrypcji można wyświetlić informacje przesyłane w raporcie użycia, który będzie pomocny w śledzeniu użycia i w zarządzaniu kosztami/wydatkami na poziomie działu.

Dostęp do użycia możesz również uzyskać za pośrednictwem szczegółowych informacji interfejsu API. Przykładowy kod jest dostępny na stronie [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Czy mogę ustawić limit przydziału wydatków i otrzymywać alerty w miarę zbliżania się do wykorzystania limitu?

Limit przydziału wydatków można ustawić na poziomie działu, a system automatycznie powiadomi Cię, gdy limit przydziału wydatków osiągnie 50%, 75%, 90% i 100% zdefiniowanej wartości.

Aby zdefiniować limit przydziału wydatków, kliknij dział, dla którego chcesz dodać limit przydziału wydatków, i kliknij ikonę Edytuj. Kliknij przycisk **Zapisz**, aby zapisać szczegóły.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>W celu zaimplementowania kontroli dostępu opartej na rolach i śledzenia użycia stosowano grupy zasobów. Jak można wyświetlić skojarzone szczegóły użycia?

Informacje, takie jak „grupy zasobów” i „tag”, jeśli są używane, są śledzone na poziomie usługi, i są one dostępne w pliku do pobrania użycia szczegółowego (CSV), który można pobrać z witryny Azure EA Portal [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

Dostęp do użycia możesz również uzyskać za pośrednictwem szczegółowych informacji interfejsu API. Przykładowy kod jest dostępny na stronie [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Należy pamiętać, że tagi można stosować tylko do zasobów, które obsługują operacje Menedżera zasobów. W przypadku utworzenia maszyny wirtualnej, sieci wirtualnej lub magazynu za pomocą klasycznego modelu wdrażania (na przykład za pośrednictwem portalu klasycznego) nie można zastosować znacznika do takiego zasobu. Aby umożliwić obsługę tagowania, należy ponownie wdrożyć te zasoby za pomocą Menedżera zasobów. Wszystkie inne zasoby obsługują tagowanie.

### <a name="can-i-perform-analyses-using-power-bi"></a>Czy mogę wykonywać analizy przy użyciu usługi Power BI?

Tak. Pakiet zawartości Microsoft Azure Enterprise dla usługi Power BI umożliwia szybkie importowanie i analizowanie danych dotyczących wykorzystania platformy Azure na potrzeby rejestracji w przedsiębiorstwie. Sprawdź, który dział, konto lub subskrypcja odnotowały największe użycie oraz z której usługi organizacja korzystała najczęściej. Śledź trendy dotyczące wydatków i użycia.

**Przejdź do witryny internetowej usługi Power BI:**

 1. Zaloguj się za pomocą ważnego konta służbowego.
    - Konto służbowe może być takie samo jak użyte w celu uzyskania dostępu do rejestracji za pomocą witryny Azure EA Portal lub inne.
 1. Na pulpicie nawigacyjnym usług wybierz:
    - kafelek Microsoft Azure Enterprise.
    - Kliknij przycisk **Połącz**.
 1. Na ekranie „Nawiązywanie połączenia z platformą Azure Enterprise” wybierz:
    - Adres URL środowiska platformy Azure: [https://ea.azure.com](https://ea.azure.com).
    - Liczba miesięcy: wybierz z przedziału od 1 do 36.
    - Numer rejestracji: podaj numer rejestracji.
    - Kliknij przycisk **Dalej**.
 1. W polu Klucz uwierzytelniania podaj klucz interfejsu API. Klucz interfejsu API możesz uzyskać w witrynie Azure EA Portal na karcie „Pobierz użycie”: kliknij pozycję **Klucz dostępu interfejsu API**.
    - Skopiuj i wklej klucz w polu „Klucz konta”.
    - Załadowanie danych do usługi Power BI trwa około 5-30 minut w zależności od rozmiaru zestawu danych.

Funkcja raportowania usługi Power BI jest dostępna dla bezpośrednich klientów umowy EA, partnerów i klientów pośrednich, którzy mogą wyświetlać informacje dotyczące rozliczeń.

## <a name="next-steps"></a>Następne kroki

- Administratorzy portalu EA platformy Azure powinni przeczytać artykuł [Administracja portalu Azure EA](ea-portal-administration.md), aby poznać typowe zadania administracyjne.
- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](ea-portal-troubleshoot.md).
- Przewodnik dołączania do umowy EA platformy Azure znajduje się w temacie [Przewodnik po dołączaniu do umowy EA platformy Azure](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).

---
title: Wprowadzenie do witryny Azure EA Portal
description: W tym artykule wyjaśniono, jak klienci platformy Azure z umową EA używają witryny Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900934"
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

![Diagram prostych hierarchii witryny Azure EA Portal](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role użytkownika przedsiębiorstwa

Aby administrować usługami platformy Azure w ramach rejestracji, istnieją cztery odrębne role użytkowników administracyjnych przedsiębiorstwa:

- Administrator przedsiębiorstwa
- Administrator działu
- Właściciel konta
- Administrator usługi

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

Każde konto wymaga unikatowego konta służbowego lub konta Microsoft. Aby uzyskać więcej informacji na temat ról administracyjnych w witrynie Azure EA Portal, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Administrator usługi

Administrator usługi ma uprawnienia do zarządzania usługami w witrynie Azure Portal i przypisywania użytkowników do roli współadministratora.

## <a name="activate-your-enrollment"></a>Aktywowanie rejestracji

W celu aktywowania usługi początkowy administrator przedsiębiorstwa otwiera witrynę Azure EA Portal pod adresem [https://ea.azure.com](https://ea.azure.com) i loguje się przy użyciu adresu e-mail z wiadomości e-mail z zaproszeniem.

Jeśli masz więcej niż jedną rejestrację, wybierz tę, którą chcesz aktywować. Domyślnie są wyświetlane tylko aktywne rejestracje. Aby wyświetlić historię rejestracji, wyczyść opcję **Aktywne** w prawym górnym rogu witryny Azure EA Portal.

W obszarze Rejestracja wyświetlany jest stan **Aktywne**.

![Przykład pokazujący aktywną rejestrację](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Tylko istniejący administratorzy przedsiębiorstwa platformy Azure mogą tworzyć innych administratorów przedsiębiorstwa.

### <a name="create-another-enterprise-admin"></a>Tworzenie innego administratora przedsiębiorstwa

- Zaloguj się do witryny [Azure EA Portal](https://ea.azure.com) i przejdź do pozycji **Zarządzanie** > **Szczegóły rejestracji**, a następnie kliknij pozycję **+ Dodaj administratora** w prawym górnym rogu strony.

Upewnij się, że masz adresy e-mail użytkownika i preferowaną metodę uwierzytelniania, na przykład uwierzytelnianie służbowe lub konto Microsoft. Potrzebujesz tych informacji, aby dodać użytkownika.

Jeśli nie jesteś administratorem umowy EA, skontaktuj się z administratorem umowy EA, aby zażądać dodania Ciebie do rejestracji. Po dodaniu do rejestracji otrzymasz wiadomość e-mail dotyczącą aktywacji.

Jeśli administrator umowy EA nie może Ci pomóc, utwórz wniosek o pomoc techniczną dla witryny Azure EA Portal. Podaj następujące informacje:

- Numer rejestracji
- Adres e-mail do dodania i typ uwierzytelnienia (konto służbowe lub konto Microsoft)
- Wiadomość e-mail dotycząca zatwierdzenia od istniejącego administratora umowy EA
  - Jeśli istniejący administrator umowy EA jest niedostępny, skontaktuj się z partnerem lub doradcą ds. oprogramowania, aby zażądać zmiany danych kontaktowych za pomocą narzędzia VLSC.

Aby uzyskać więcej informacji na temat ról administracyjnych w przedsiębiorstwie, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](billing-understand-ea-roles.md).

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

![Przykład przedstawiający okno dialogowe Dodawanie administratora działu](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

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

![Przykład przedstawiający listę kont i opcję +Dodaj konto](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Możesz dodać kolejne konto, klikając pozycję **Dodaj kolejne konto** lub klikając pozycję **Dodaj** w prawym dolnym rogu lewego paska narzędzi.

Aby potwierdzić własność konta:

1. Zaloguj się do witryny Azure EA Portal.
2. Potwierdź własność konta, wyświetlając stan. Stan powinien ulec zmianie z **Oczekujące** na **Data początkowa/końcowa**. Data początkowa/końcowa to data pierwszego logowania użytkownika i data zakończenia obowiązywania umowy.


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

![Obraz przedstawiający symbol Zmiana właściciela konta](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Aby przenieść własność konta dla jednej subskrypcji:

1. W obszarze nawigacji po lewej stronie kliknij pozycję **Zarządzanie**.
2. Kliknij kartę **Konto** i zatrzymaj wskaźnik myszy na koncie.
3. Kliknij symbol przenoszenia subskrypcji po prawej stronie. Symbol przedstawia stronę.
4. Wybierz kwalifikującą się subskrypcję, a następnie kliknij pozycję **Dalej**.
5. Potwierdź przeniesienie, a następnie kliknij pozycję **Prześlij**.

![Obraz przedstawiający symbol Przenoszenie subskrypcji](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

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

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Przenoszenie subskrypcji płatnej zgodnie z rzeczywistym użyciem do subskrypcji umowy EA

Aby przenieść pojedynczą subskrypcję ze stawkami płatności zgodnie z rzeczywistym użyciem do subskrypcji umowy EA, należy utworzyć nowe żądanie pomocy technicznej w witrynie Azure Portal. Aby utworzyć żądanie pomocy technicznej, kliknij pozycję **+ Nowe żądanie pomocy technicznej** w obszarze Pomoc i obsługa techniczna.


## <a name="view-usage-summary-and-download-reports"></a>Wyświetlanie podsumowania użycia i pobieranie raportów

Administratorzy przedsiębiorstwa mogą wyświetlać podsumowanie danych użycia, wykorzystanych zobowiązań pieniężnych i opłat związanych z dodatkowym użyciem w witrynie Azure EA Portal. Opłaty są wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji.

Wyświetlanie szczegółowych informacji o użyciu określonych kont

Pobierz szczegółowy raport użycia. Kliknij pozycję **Raporty**, a następnie kliknij kartę **Pobieranie danych użycia**. Na liście raportów kliknij pozycję **Pobierz** dla miesięcznego raportu, który chcesz uzyskać.

Raport nie zawiera żadnych należnych podatków. Może wystąpić opóźnienie do ośmiu godzin od momentu, w którym wystąpiło użycie, do chwili, gdy zostanie ono odzwierciedlone w raporcie.

Aby wyświetlić raporty i wykresy podsumowania użycia:

1. W witrynie Azure EA Portal w lewym obszarze nawigacji kliknij pozycję **Raporty** i wyświetl kartę **Podsumowanie użycia**.  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Wybierz termin zobowiązania.
3. Przełączaj między ustawieniem **M** (miesięczne) i **C** (niestandardowe) w prawym górnym rogu strony, aby wyświetlić **Podsumowanie użycia** z niestandardowymi datami rozpoczęcia i zakończenia.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
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

![Przykład pokazujący stronę Pobieranie danych użycia](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Oto wideo pokazujące, jak pobrać informacje o użyciu:

[Wideo dotyczące użycia w witrynie Azure EA Portal](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Planowanie połączenia dotyczącego dołączania

Jeśli chcesz zaplanować osobistą sesję dołączania klienta, utwórz wniosek o pomoc techniczną na stronie [Azure EA Portal — pomoc techniczna](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Wybierz pozycję **Dołączanie** w sekcji **Kategoria problemu**.

## <a name="next-steps"></a>Następne kroki
- Administratorzy witryny Azure EA Portal powinni przeczytać artykuł [Administracja w witrynie Azure EA Portal](billing-ea-portal-administration.md), aby poznać typowe zadania administracyjne.
- Jeśli potrzebujesz pomocy dotyczącej rozwiązywania problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](billing-ea-portal-troubleshoot.md).

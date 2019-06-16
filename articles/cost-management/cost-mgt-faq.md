---
title: Często zadawane pytania dotyczące platformy Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące platformy Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: troubleshooting
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 02a03adb128c140343032075ec334cbd6d88729b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002022"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Często zadawane pytania dotyczące platformy Cloudyn

W tym artykule opisano często zadawane pytania dotyczące platformy Cloudyn. Jeśli masz pytania dotyczące platformy Cloudyn, można zadawać w [często zadawane pytania dotyczące platformy Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Jak można rozwiązać typowe problemy z instalacją pośrednich enterprise?

Przy pierwszym użyciu portalu Cloudyn możesz zobaczyć poniższe komunikaty, jeśli jesteś użytkownikiem z umową Enterprise Agreement lub dostawcą rozwiązań w chmurze (CSP, Cloud Solution Provider):

- "Określony klucz interfejsu API nie jest kluczem najwyższego poziomu rejestrowania" wyświetlana w **Ustaw Cloudyn** kreatora.
- "Rejestracja bezpośrednia — nie" wyświetlana w portalu Enterprise Agreement.
- "Brak danych użycia został znaleziony w ciągu ostatnich 30 dni. Skontaktuj się z dystrybutora, aby upewnić się, że znaczników został włączony dla Twojego konta platformy Azure"wyświetlana w portalu Cloudyn.

Poprzednie komunikaty oznaczają, że umowa Azure Enterprise Agreement została zakupiona od odsprzedawcy lub dostawcy rozwiązań w chmurze. Odsprzedawca lub dostawca rozwiązań w chmurze musi włączyć _znaczniki_ dla konta platformy Azure, aby można było wyświetlać dane na platformie Cloudyn.

Oto sposoby rozwiązywania tych problemów:

1. Odsprzedawca musi włączyć _znaczniki_ dla Twojego konta. Aby uzyskać instrukcje, zapoznaj się z dokumentem [Indirect Customer Onboarding Guide (Przewodnik dołączania klienta pośredniego)](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Wygenerujesz klucz umowy Azure Enterprise Agreement do użycia na platformie Cloudyn. Aby uzyskać instrukcje, zobacz [Dodawanie Your Azure EA](quick-register-ea.md#register-with-cloudyn) lub [jak znaleźć swój identyfikator rejestracji EA i klucz interfejsu API](https://youtu.be/u_phLs_udig).

Tylko administrator usług platformy Azure może włączyć platformę Cloudyn. Uprawnienia współadministratora są niewystarczające.

Przed wygenerowaniem klucza interfejsu API umowy Azure Enterprise Agreement w celu skonfigurowania platformy Cloudyn należy włączyć interfejs API rozliczeń platformy Azure, wykonując następujące instrukcje:

- [Overview of Reporting APIs for Enterprise customers (Omówienie interfejsów API raportowania dla klientów korporacyjnych)](../billing/billing-enterprise-api.md)
- [Interfejs API raportowania w witrynie Microsoft Azure dla przedsiębiorstw](https://ea.azure.com/helpdocs/reportingAPI) w obszarze **Włączanie dostępu danych do interfejsu API**


Być może trzeba będzie również nadać administratorom działów, właścicielom kont i administratorom przedsiębiorstwa uprawnienia do _wyświetlania opłat_ przy użyciu interfejsu API rozliczeń.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Dlaczego nie widzę Optymalizator zalecenia

Zalecenie dotyczące informacji jest dostępna tylko dla kont, które są aktywowane. Nie zobaczysz żadnych informacji zalecenie w **Optymalizator** raportu kategorie dla konta, które są *nieaktywowani*, w tym:

- Menedżer optymalizacji
- Optymalizacja rozmiaru
- Czynniki

Jeśli nie można wyświetlić wszystkie dane zalecenie Optymalizator, najprawdopodobniej musisz kont, które są nieaktywowani. Aby aktywować konto, należy zarejestrować go za pomocą swoich poświadczeń platformy Azure.

Aby aktywować konto:

1.  W portalu Cloudyn kliknij pozycję **Ustawienia** w górnym prawym rogu i wybierz pozycję **Konta w chmurze**.
2.  Na karcie kont usługi Microsoft Azure, wyszukaj kont, które mają **nieaktywowani** subskrypcji.
3.  Po prawej stronie nieaktywowani konta **Edytuj** symbol, który przypomina ołówka.
4.  Dzierżawy, identyfikator i Identyfikatora stawki jest wykrywany automatycznie. Kliknij przycisk **Dalej**.
5.  Użytkownik jest przekierowywany do witryny Azure portal. Zaloguj się do portalu i autoryzować rozwiązanie Cloudyn moduł zbierający, aby uzyskiwać dostęp do danych platformy Azure.
6.  Następnie jest przekierowanie do strony zarządzania kontami Cloudyn i subskrypcja zostanie zaktualizowana przy użyciu **active** stan konta. Przedstawia on zielony znacznik wyboru.
7.  Jeśli nie widzisz symbolu zielony znacznik wyboru dla przynajmniej jednej subskrypcji, oznacza to, że nie masz uprawnień do tworzenia aplikacji reader (CloudynCollector) dla subskrypcji. Użytkownik mający wyższych uprawnień dla subskrypcji musi Powtórz kroki 3 i 4.  

Po wykonaniu powyższych kroków, możesz wyświetlić zalecenia optymalizator w jeden lub dwa dni. Jednak może upłynąć do pięciu dni przed Pełna optymalizacja dane są dostępne.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Jak włączyć wstrzymania lub zablokowanego użytkowników?

Najpierw Przyjrzyjmy się najbardziej typowy scenariusz, który powoduje, że konta użytkowników uzyskać *initiallySuspended*.

> Admin1 może być użytkownikiem dostawcy rozwiązań w chmurze firmy Microsoft lub umowy Enterprise Agreement. Organizacji jest gotowy rozpocząć korzystanie z platformy Cloudyn.  Użytkownik rejestruje się w witrynie Azure portal i zaloguje się do portalu Cloudyn. Jako osoba, która rejestruje usługi Cloudyn i loguje się do portalu Cloudyn, staje się Admin1 *głównych administratorów*. Admin1 nie utworzyć kont użytkowników. Jednak przy użyciu portalu Cloudyn, ich tworzenie kont platformy Azure i Konfigurowanie hierarchii jednostek. Admin1 informuje Admin2, administrator dzierżawy, należy zarejestrować w usłudze Cloudyn i zaloguj się do portalu Cloudyn.
>
> Admin2 rejestruje się w witrynie Azure portal. Jednak gdy użytkownik próbuje zalogować się do portalu Cloudyn one wyświetlony komunikat o błędzie informujący o tym, jego konto jest **zawieszone**. Administrator podstawowy Admin1, jest powiadamiany o zawieszenie konta. Admin1 musi aktywować firmy Admin2 konto i przyznać *dostęp do jednostki administracyjnej* dla odpowiednich jednostek i umożliwia użytkownika zarządzania dostępem i aktywne konta użytkownika.


Jeśli zostanie wyświetlony alert o żądanie, aby zezwolić użytkownikowi na dostęp, musisz aktywować konto użytkownika.

Aby aktywować konto użytkownika:

1. Zaloguj się do rozwiązania Cloudyn przy użyciu konta użytkownika administracyjnego platformy Azure, którego użyto do skonfigurowania Cloudyn. Lub zaloguj się przy użyciu konta użytkownika, który otrzymał prawa dostępu administratora.
2. Wybierz symbol koła zębatego w prawym górnym rogu, a następnie wybierz pozycję **Zarządzanie użytkownikami**.
3. Znajdź użytkownika, wybierz symbol ołówka, a następnie edytować użytkownika.
4. W obszarze **stan użytkownika**, Zmień stan z **zawieszone** do **Active**.

Łączenie konta użytkowników platformy Cloudyn przy użyciu logowania jednokrotnego z platformy Azure. Jeśli użytkownik mistypes swojego hasła, ich może zabezpieczenie przed zablokowaniem dostępu Cloudyn, mimo że nadal mogą uzyskiwać dostęp do platformy Azure.

Jeśli zmienisz swój adres e-mail w rozwiązaniu Cloudyn z domyślny adres na platformie Azure, Pobierz zablokowane konta. Może wyświetlać "initiallySuspended stan". Jeśli Twoje konto użytkownika jest zablokowane, skontaktuj się z alternatywnych administratorem, aby zresetował Twoje konto.

Firma Microsoft zaleca utworzenie co najmniej dwóch kont administratorów platformy Cloudyn, w przypadku, gdy jedno z kont zostanie zablokowane.

Jeśli nie można zalogować się do portalu Cloudyn, upewnij się, że używasz prawidłowego adresu URL do logowania się na platformie Cloudyn. Użyj [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Należy unikać bezpośredniego adresu URL Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Jak uaktywnić nieaktywowani kont przy użyciu poświadczeń platformy Azure?

Jak najszybciej kont systemu Azure są wykrywane przez firmę Cloudyn, danych rozwiązania cost natychmiast znajduje się w raportów na podstawie kosztów. Jednak na platformie Cloudyn dostarczający dane użycia i wydajności, należy zarejestrować swoje poświadczenia platformy Azure dla kont. Aby uzyskać instrukcje, zobacz [Dodaj konto lub zaktualizować subskrypcji](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Aby dodać poświadczenia platformy Azure dla konta w portalu Cloudyn, wybierz symbol edycji z prawej strony nazwy konta, a nie w ramach subskrypcji.

Dopóki poświadczenia platformy Azure zostaną dodane do rozwiązania Cloudyn, konto jest wyświetlane jako _nie aktywowano_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Jak dodać wiele kont i jednostek do istniejącej subskrypcji?

Dodatkowe jednostki są używane do dodawania dodatkowych umowy Enterprise z subskrypcją platformy Cloudyn. Aby uzyskać więcej informacji, zobacz [tworzenie i zarządzanie jednostkami](tutorial-user-access.md#create-and-manage-entities).

Dla dostawców usług kryptograficznych:

Aby dodać dodatkowe konta programu CSP do jednostki, wybierz **dostępu do pliku MSP** zamiast **Enterprise** podczas tworzenia nowej jednostki. Jeśli Twoje konto jest zarejestrowany jako umowy Enterprise Agreement, które chcesz dodać poświadczenia dostawcy usług Kryptograficznych platformy Cloudyn personel pomocy technicznej może się okazać zmodyfikowanie ustawień konta. Jeśli masz płatne subskrybenta platformy Azure, możesz utworzyć nowe żądanie pomocy technicznej w witrynie Azure portal. Wybierz **Pomoc i obsługa techniczna**, a następnie wybierz pozycję **nowe żądanie obsługi**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Symbole waluty w raportach usługi Cloudyn

Może mieć wielu kont platformy Azure przy użyciu różnych walutach. Jednak raportach kosztów w rozwiązaniu Cloudyn nie pokazuj więcej niż jeden typ waluty na raport.

Jeśli masz wiele subskrypcji przy użyciu różnych walutach, jednostka nadrzędna i jego waluty jednostki podrzędne są wyświetlane w USD **$** . Nasze sugerowane najlepszym rozwiązaniem jest należy unikać używania różnych walutach są w tej samej hierarchii jednostki. Innymi słowy wszystkie swoje subskrypcje strukturę jednostki należy używać tej samej walucie.

Cloudyn automatycznie wykrywa walucie subskrypcji Enterprise Agreement i prezentuje je poprawnie w raportach.  Jednak Cloudyn wyświetlane są tylko USD **$** dla dostawcy usług Kryptograficznych i kont platformy Azure bezpośrednio w sieci web.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Co to są dane Cloudyn odświeżyć osi czasu?

Cloudyn ma następujące osie czasu odświeżania danych:

- **Początkowa**: Po skonfigurowaniu może potrwać do 24 godzin, aby wyświetlić dane kosztów w rozwiązaniu Cloudyn. Może to również przybrać rozwiązanie Cloudyn do zbierania wystarczającej ilości danych, aby wyświetlić zalecenia dotyczące ustalania rozmiaru w ciągu 10 dni.
- **Codzienne**: Z dziesiątego na koniec każdego miesiąca Cloudyn powinny pokazywać dane jako aktualne informacje z poprzedniego dnia po o UTC + 3 następnego dnia.
- **Miesięczne**: Począwszy od pierwszego dnia dziesiątym dniu każdego miesiąca Cloudyn może wyświetlać dane tylko do końca poprzedniego miesiąca.

Cloudyn przetwarza dane z poprzedniego dnia, gdy jest dostępna pełna danych z poprzedniego dnia. Poprzedniego dnia, dane są zwykle dostępne w rozwiązaniu Cloudyn przez o UTC + 3 każdego dnia. Niektórych danych, takich jak tagi oraz może być dodatkowe 24 godziny do przetwarzania.

Dane dla bieżącego miesiąca są niedostępne dla kolekcji na początku każdego miesiąca. W okresie usługodawców finalize ich rozliczanie w poprzednim miesiącu. Poprzedni miesiąc dane zostaną wyświetlone w rozwiązaniu Cloudyn 5 do 10 dni, po rozpoczęciu dnia każdego miesiąca. W tym czasie mogą być wyświetlane tylko zamortyzowany koszt z poprzedniego miesiąca. Mogą nie być wyświetlane dane dotyczące dziennego rozliczeń lub użycia. Po udostępnieniu danych Cloudyn przetwarza je wstecznie. Po zakończeniu przetwarzania miesięczne dane jest wyświetlany między piątym dzień i dziesiątym dniu każdego miesiąca.

Jeśli występuje opóźnienie wysyłania danych z platformy Azure do rozwiązania Cloudyn, dane są nadal zapisywane na platformie Azure. Dane są przesyłane do rozwiązania Cloudyn po przywróceniu połączenia.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Koszt wahania w raportach kosztów Cloudyn

Raportach kosztów może być wahania kosztów, zawsze wtedy, gdy dostawców usług w chmurze wysyłania zaktualizowanych plików rozliczeń. Koszty obsługi wystąpić, gdy nowe pliki są odbierane z dostawcy usług w chmurze poza zwykłe codziennie lub co miesiąc, raportowanie harmonogramu. Koszt zmiany nie są wynikiem ponowne obliczenie Cloudyn.

W ciągu miesiąca wszystkie pliki rozliczeń wysyłane przez dostawcę usługi w chmurze znajdują się szacowanie codziennego koszty. Czasami dane są często aktualizowane — od czasu do czasu kilka razy dziennie. Aktualizacje są za pomocą usług AWS częściej niż platformy Azure. Całkowite koszty powinny być trwałe, po zakończeniu obliczeń rozliczenia dla poprzedniego miesiąca i ostateczną rozliczeń została odebrana. Zazwyczaj przez 10 dnia miesiąca.

Zmiany zostaną wprowadzone po otrzymaniu korekty kosztów od dostawcy usług w chmurze. Odbieranie środków jest jednym z przykładów. Zmiany mogą być miesięcy po danym miesiącu została zamknięta. Zmiany są wyświetlane zawsze, gdy ponowne obliczenie jest tworzone przez dostawcę usługi w chmurze. Cloudyn aktualizuje jego danych historycznych, aby upewnić się, że wszystkie zmiany są ponownie obliczane. Sprawdza także, że koszty są wyświetlane w dokładnie raportuje.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Jak bezpośredniego programu CSP skonfigurować Cloudyn dostęp dla klientów bezpośredniego programu CSP lub partnerów?

Aby uzyskać instrukcje, zobacz [skonfigurować bezpośredni dostęp do programu CSP w rozwiązaniu Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Co powoduje, że element menu Optymalizator pojawiają się?

Po dodaniu dostęp do usługi Azure Resource Manager i dane są zbierane, powinien zostać wyświetlony **Optymalizator** opcji. Aby aktywować dostęp do usługi Azure Resource Manager, zobacz [jak aktywować nieaktywowani kont przy użyciu poświadczeń platformy Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Oparte na agentach Cloudyn

Nie. Agenci nie są używane. Dane metryk maszyny wirtualnej platformy Azure dla maszyn wirtualnych są zbierane z interfejsu API usługi Microsoft Insights. Jeśli chcesz zbierać dane metryk z maszyn wirtualnych platformy Azure muszą mieć włączonych ustawień diagnostycznych.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Raporty usługi Cloudyn, są wyświetlane więcej niż jednej dzierżawy usługi AD na raport?

Tak. Możesz [tworzenie odpowiedniej jednostki konta chmury](tutorial-user-access.md#create-and-manage-entities) dla każdej dzierżawy usługi AD, czy masz. Następnie możesz wyświetlić wszystkie dane dzierżawy usługi Azure AD i innych dostawców platformy chmury, w tym usług Amazon Web Services i Google Cloud Platform.

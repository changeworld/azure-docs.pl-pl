---
title: Często zadawane pytania dotyczące usługi Azure Management koszt | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Management kosztów.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: af6e15a86107aeb74a20c3e78a8f91f07d6d377a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Często zadawane pytania dotyczące usługi Azure Management koszt

W tym artykule opisano często zadawane pytania dotyczące usługi Azure Management koszt (znanej także jako Cloudyn). Jeśli masz pytania dotyczące zarządzania kosztów, możesz je pod adresem [często zadawane pytania dotyczące usługi Azure Management koszt](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Jak można rozwiązać typowe problemy z instalacją pośrednie przedsiębiorstwa?

Przy pierwszym użyciu portalu Cloudyn, jeśli jesteś użytkownikiem umowy Enterprise Agreement lub Cloud Solution Provider (CSP) można napotkać następujące komunikaty:

- "Określony klucz interfejsu API nie jest kluczem najwyższego poziomu rejestrowania" wyświetlana w **ustawić zapasowej Azure kosztów zarządzania** kreatora.
- "Rejestracja bezpośrednia — nie" wyświetlana w portalu umowy Enterprise Agreement.
- "Nie użycia znaleziono danych w ciągu ostatnich 30 dni. Skontaktuj się z dystrybutora, aby upewnić się, że znaczników zostały włączone dla konta platformy Azure"wyświetlana w portalu Cloudyn.

Poprzednie komunikaty wskazują zakupiono Azure Enterprise Agreement u odsprzedawcy lub dostawcy usług Kryptograficznych. Ze sprzedawcą lub dostawcy usług Kryptograficznych, trzeba włączyć _znacznika_ dla konta platformy Azure, dzięki czemu można wyświetlać dane w Cloudyn.

Oto jak rozwiązać problemy:

1. Należy włączyć odsprzedawcą _znacznika_ dla Twojego konta. Aby uzyskać instrukcje, zobacz [pośrednie przewodnik dołączania klienta](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Możesz wygenerować klucz Azure Enterprise Agreement do użycia z Cloudyn. Aby uzyskać instrukcje, zobacz [Dodawanie Your Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) lub [jak znaleźć swój EA rejestracji identyfikator i klucz API](https://youtu.be/u_phLs_udig).

Tylko administrator usługi Azure można włączyć zarządzanie kosztów. Uprawnienia administratora współpracującego są niewystarczające.

Przed wygenerowaniem klucza interfejsu API umowy Enterprise Azure do skonfigurowania Cloudyn, należy włączyć interfejsu API rozliczenia Azure zgodnie z instrukcjami na:

- [Omówienie API raportowania dla przedsiębiorstw](../billing/billing-enterprise-api.md)
- [Microsoft Azure enterprise portal interfejsu API raportowania](https://ea.azure.com/helpdocs/reportingAPI) w obszarze **Włączanie dostępu do interfejsu API danych**


Może należy również podać Administratorzy działu, właściciele konta i uprawnienia administratorów przedsiębiorstwa _wyświetlić opłat_ przy użyciu interfejsu API rozliczeń.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Dlaczego nie widzę zalecenia Optymalizator?

Informacje o zalecenie jest dostępna tylko dla kont, które są aktywowane. Nawet nie widać żadnych informacji zalecenia w **Optymalizator** raport kategorii dla konta, które są *nieaktywowani*, takie jak:

- Menedżer optymalizacji
- Ustawianie rozmiaru optymalizacji
- Wydajność

Jeśli nie można wyświetlić wszystkie dane zalecenie Optymalizator, najprawdopodobniej masz konta, które są nieaktywowani. Aby aktywować konto, musisz zarejestrować go przy użyciu poświadczeń usługi Azure.

Aby aktywować konto:

1.  W portalu Cloudyn kliknij pozycję **Ustawienia** w górnym prawym rogu i wybierz pozycję **Konta w chmurze**.
2.  Na karcie konta usługi Microsoft Azure, wyszukaj konta, które mają **nieaktywowani** subskrypcji.
3.  Po prawej stronie konto nieaktywowani **Edytuj** symbol podobny ołówka.
4.  Identyfikator i szybkość identyfikator dzierżawy jest wykrywany automatycznie. Kliknij przycisk **Dalej**.
5.  Użytkownik jest przekierowywany do portalu Azure. Zaloguj się do portalu i autoryzować modułu zbierającego Cloudyn dostępu do danych Azure.
6.  Następnie jest przekierowanie do strony zarządzania Cloudyn konta i subskrypcji został zaktualizowany o **active** stan konta. Widoczny jest zielony znacznik wyboru.
7.  Jeśli nie widzisz symbolu zielonym znacznikiem wyboru dla co najmniej jednego z subskrypcji, oznacza to, nie masz uprawnień do tworzenia aplikacji czytnika (CloudynCollector) dla subskrypcji. Użytkownik z uprawnieniami wyższej subskrypcji musi Powtórz kroki 3 i 4.  

Po wykonaniu powyższych kroków można wyświetlić Optymalizator zalecenia w ciągu jednej do dwóch dni. Jednak może potrwać maksymalnie pięć dni przed Pełna optymalizacja dane są dostępne.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Jak włączyć wstrzymane lub zablokowanego użytkowników?

Jeśli zostanie wyświetlony alert o żądanie, aby zezwolić na dostęp dla użytkownika, musisz aktywować konto użytkownika.

Aby aktywować konto użytkownika:

1. Zaloguj się do Cloudyn za pomocą konta Azure użytkownika administracyjnego, który zostanie użyty do skonfigurowania Cloudyn. Lub zaloguj się przy użyciu konta użytkownika, który został udzielony dostęp administratora.

2. Wybierz symbol koło zębate w prawym górnym rogu i wybierz **Zarządzanie użytkownikami**.

3. Znajdź użytkownika, wybierz symbol ołówka, a następnie Edytuj użytkownika.

4. W obszarze **stan użytkownika**, zmianę stanu z **zawieszone** do **Active**.

Konta użytkowników Cloudyn połączenia przy użyciu rejestracji jednokrotnej z platformy Azure. Jeśli użytkownik mistypes swoje hasło, ich mogą być blokowane z Cloudyn, nawet jeśli ich mogą nadal uzyskiwać dostęp do usługi Azure.

Zmiana adresu e-mail w Cloudyn z domyślnym adresem na platformie Azure, Twoje konto można pobrać zablokowane. Mogą być wyświetlane "stan initiallySuspended." Jeśli konto użytkownika jest zablokowane, skontaktuj się z alternatywnych administratorem, aby zresetować Twoje konto.

Firma Microsoft zaleca utworzenie co najmniej dwóch kont administratora Cloudyn na wypadek, gdyby jedno z kont zostanie zablokowane.

Nie można zalogować się do portalu Cloudyn, upewnij się, logować się do Cloudyn używasz poprawny adres URL zarządzania koszt Azure. Użyj [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Unikaj używania bezpośredni adres URL Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Jak aktywować nieaktywowani kont z poświadczeniami Azure?

Jak konta platformy Azure są odnajdywane przez Cloudyn, kosztów natychmiast podano danych w raportach na podstawie kosztów. Jednak dla Cloudyn dostarczający dane użycia i wydajności, należy zarejestrować poświadczenia dla konta platformy Azure. Aby uzyskać instrukcje, zobacz [Dodaj usługi Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Aby dodać Azure poświadczenia dla konta w portalu Cloudyn wybierz symbol edycji z prawej strony nazwy konta, a nie w ramach subskrypcji.

Dopóki poświadczenia platformy Azure są dodawane do Cloudyn, konto zostanie wyświetlone jako _nie aktywowali_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Jak dodać wiele kont i jednostek do istniejącej subskrypcji?

Dodatkowe jednostki są używane do dodać dodatkowe umowy Enterprise do subskrypcji Cloudyn. Poniższe linki zawierają opis sposobu dodawania dodatkowych jednostek:

- [Dodawanie jednostki](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) artykułu
- [Definiowanie hierarchii z jednostkami koszt](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) wideo

Dla dostawców usług kryptograficznych:

Aby dodać dodatkowe konta dostawcy usług Kryptograficznych do jednostki, wybierz **dostępu MSP** zamiast **Enterprise** podczas tworzenia nowego obiektu. Jeśli Twoje konto jest zarejestrowany jako umowy Enterprise Agreement, i chcesz dodać poświadczenia dostawcy usług Kryptograficznych, Cloudyn pomocy technicznej może być konieczne zmodyfikuj ustawienia konta. Jeśli masz płatne subskrybent platformy Azure, można utworzyć nowe żądanie pomocy technicznej w portalu Azure. Wybierz **Pomoc i obsługa techniczna**, a następnie wybierz **nowy obsługuje żądania**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Symbole waluty w raportach Cloudyn

Może mieć wiele kont platformy Azure przy użyciu różnych waluty. Jednak koszt raportów w Cloudyn nie pokazuj więcej niż jeden typ waluty na raport.

Jeśli masz wiele subskrypcji przy użyciu różnych walut jednostki nadrzędnej i jej podrzędne jednostki waluty są wyświetlane w USD **$**. Nasze sugerowane najlepszym rozwiązaniem jest aby uniknąć używania różnych waluty w tej samej hierarchii jednostek. Innymi słowy wszystkie subskrypcje strukturę jednostek należy używać tej samej waluty.

Cloudyn automatycznie wykrywa walucie subskrypcji Enterprise Agreement i przedstawia on prawidłowo w raportach.  Jednak Cloudyn wyświetlane są tylko USD **$** dla konta platformy Azure bezpośrednio w sieci web i dostawcy usług Kryptograficznych.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Co to są dane Cloudyn Odśwież osi czasu?

Cloudyn ma następujące osiach czasu odświeżania danych:

- **Początkowa**: po skonfigurowaniu, może potrwać do 24 godzin, aby wyświetlić dane kosztów w Cloudyn. Można również wykonać Cloudyn zbierać wystarczającej ilości danych, aby wyświetlić zalecenia dotyczące zmiany rozmiaru w ciągu 10 dni.
- **Codzienne**: Z dziesiątego na koniec każdego miesiąca, Cloudyn powinny być widoczne dane na bieżąco z poprzedniego dnia po o UTC + 3 następnego dnia.
- **Miesięczne**: od pierwszego dnia do dziesiątego dnia każdego miesiąca, Cloudyn mogą być wyświetlane dane tylko do końca poprzedniego miesiąca.

Cloudyn przetwarza dane z poprzedniego dnia, gdy są dostępne pełne dane z poprzedniego dnia. Dane poprzedniego dnia są zwykle dostępne w Cloudyn przez o UTC + 3 każdego dnia. Niektóre dane, takie jak tagi, możliwe jest dodatkowe 24 godziny do przetworzenia.

Dane dla bieżącego miesiąca nie są dostępne dla kolekcji na początku każdego miesiąca. W okresie usługodawców finalize ich rozliczeń dla poprzedniego miesiąca. Poprzedniego miesiąca dane są wyświetlane w Cloudyn 5 do 10 dni, po rozpoczęciu dnia każdego miesiąca. W tym czasie może zobaczyć tylko amortyzowanego koszty z poprzedniego miesiąca. Codzienne dane rozliczeń lub obciążenie może być niewidoczny. Po udostępnieniu danych Cloudyn procesy Wstecz. Po zakończeniu przetwarzania wyświetlone miesięczne dane między piątego dnia i dziesiątego dnia każdego miesiąca.

Jeśli występuje opóźnienie wysyłania danych z platformy Azure do Cloudyn, dane są nadal rejestrowane w usłudze Azure. Dane są przesyłane do Cloudyn po przywróceniu połączenia.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Jak bezpośredniego dostawcy usług Kryptograficznych skonfigurowany dostęp Cloudyn dla pośredniego dostawcy usług Kryptograficznych klientów lub partnerów

Aby uzyskać instrukcje, zobacz [skonfigurować bezpośredni dostęp do dostawcy usług Kryptograficznych w Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Co powoduje, że element menu Optymalizator pojawią się?

Po dodaniu dostępu do usługi Azure Resource Manager i dane są zbierane, powinny pojawić się **Optymalizator** opcji. Aby aktywować dostęp do usługi Azure Resource Manager, zobacz [jak aktywować nieaktywowani kont z poświadczeniami Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>Na podstawie kosztów zarządzania/Cloudyn agenta

Nie. Agenci nie są używane. Dane maszyny wirtualnej platformy Azure dla maszyn wirtualnych są zbierane z interfejsu API informacje na temat technologii firmy Microsoft. Chcesz zbierać dane metryki z maszyn wirtualnych platformy Azure, muszą mieć włączone ustawienia diagnostyki.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Cloudyn raporty są wyświetlane więcej niż jednej dzierżawy AD na raport?

Tak. Możesz [tworzenia jednostek konta chmury](tutorial-user-access.md#create-entities) dla każdego dzierżawcy AD, czy masz. Następnie można wyświetlić wszystkich danych dzierżawy usługi Azure AD i innych dostawców platformy chmury, w tym usług Amazon Web Services i usługi Google Cloud Platform.

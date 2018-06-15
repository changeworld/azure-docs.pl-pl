---
title: Tworzenie i rejestrowanie konta wydawcy | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące tworzenia konta Microsoft Developer, więc po zatwierdzeniu można sprzedać różnych oferują typy w witrynie Azure Marketplace.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: mbaldwin
ms.openlocfilehash: 69da37cbf5691fe94b9550200e259711e9e201d4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179337"
---
# <a name="create-a-microsoft-developer-account"></a>Utwórz konto Microsoft Developer
Ten artykuł przeprowadzi Cię przez proces rejestracji zostać zatwierdzone Microsoft Developer do portalu Azure Marketplace i tworzenie konta niezbędne.

## <a name="1-create-a-microsoft-account"></a>1. Tworzenie konta Microsoft
Aby rozpocząć proces publikowania, musisz utworzyć konto Microsoft. To konto będzie używane do rejestrowania w celu zarówno **Microsoft Developer Center** i **publikowania Portal Azure**. Dla Twojej oferty Azure Marketplace, powinien mieć tylko jedno konto Microsoft. Nie powinny być specyficzne dla usługi lub oferty.

Adres, który wchodzi w skład nazwy użytkownika powinny być w domenie i kontrolowane przez Twój zespół IT. Publikowanie pokrewne działania ma się odbywać za pomocą tego konta.

> [!WARNING]
> Słów, takich jak **"Azure"** i **"Microsoft"** nie są obsługiwane dla rejestracji konta Microsoft. Unikaj używania te słowa, aby zakończyć proces rejestracji i tworzenia konta.
>
>

### <a name="guidelines-for-company-accounts"></a>Wskazówki dla firm
Podczas tworzenia konta firmy, zgodna z tymi wytycznymi Jeśli więcej niż jedna osoba potrzebować dostępu do konta, logując się przy użyciu konta Microsoft, które otworzyć konto.

> [!Important]
> Ważne Aby Zezwalaj wielu użytkownikom na dostęp do tego konta Centrum deweloperów, zaleca się używanie usługi Azure Active Directory do przypisywania ról dla poszczególnych użytkowników, którzy mogą uzyskać dostępu do konta poprzez zalogowanie za pomocą ich indywidualne poświadczenia usługi Azure AD. Aby uzyskać więcej informacji, zobacz [użytkownikami konta](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users).

* Utwórz konto Microsoft przy użyciu adresu e-mail, który należy do domeny firmy, ale nie do jedna osoba — na przykład windowsapps@fabrikam.com.
* Ograniczanie dostępu do tego konta Microsoft na najmniejszą możliwą liczbę deweloperów.
* Konfigurowanie listy dystrybucyjnej firmowej poczty e-mail, która zawiera wszystkich użytkowników potrzebuje uzyskiwać dostęp do konta dewelopera, a następnie dodać ten adres e-mail do informacji zabezpieczających. Dzięki temu wszyscy pracownicy na liście, aby otrzymać kod zabezpieczeń w razie potrzeby i zarządzać informacji zabezpieczających konta Microsoft. Konfigurowanie listy dystrybucyjnej nie jest realne, właściciela konta e-mail poszczególnych będzie muszą być dostępne do uzyskania dostępu i udostępniania kodu zabezpieczeń po wyświetleniu monitu (np. po dodaniu nowych informacji zabezpieczających do konta lub gdy muszą być dostępne z nowych urządzeń).
* Dodaj numer telefonu firmy, który nie wymaga rozszerzenia i jest dostępna dla członków zespołu klucza.
* Ogólnie rzecz biorąc mieć deweloperzy Użyj zaufanych urządzeń, aby zalogować się do konta dewelopera w firmie. Wszyscy członkowie zespołu kluczy powinien mieć dostęp do tych zaufanych urządzeń. Zmniejsza to potrzebę kody zabezpieczeń do wysłania podczas uzyskiwania dostępu do konta.
* Jeśli chcesz zezwolić na dostęp do konta z komputera z systemem innym niż zaufane, ograniczyć tej dostęp do maksymalnie pięć deweloperów. Najlepiej, jeśli te deweloperzy powinien uzyskiwać dostęp do konta z maszyny, które identyczny geograficzne i lokalizacja w sieci.
* Często przejrzeć informacje zabezpieczające firmy na stronie [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) się upewnić, że jest ona wszystkie bieżące.

Konto dewelopera powinni mieć dostęp przede wszystkim z zaufanych komputerów. Jest to krytyczny, ponieważ istnieje limit liczby kody wygenerowane dla danego konta, na tydzień. Umożliwia także bezproblemową obsługę logowania.

Aby uzyskać więcej informacji o wytycznych konta dewelopera dodatkowe i zabezpieczeniach, kliknij przycisk [tutaj](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instrukcje
1. Otwórz nowy Incognito przeglądarki Chrome lub sesja przeglądania Internet Explorer InPrivate Aby upewnić się, że nie zalogowano Cię do istniejącego konta.
2. Zarejestruj wiadomości e-mail (na wcześniej wskazówki dotyczące np. windowsapp@fabrikam.com) jako konto Microsoft za pośrednictwem łącza [ https://signup.live.com/signup.aspx ](https://signup.live.com/signup.aspx). Postępuj zgodnie z poniższymi instrukcjami.

   1. Podczas rejestrowania konta jako konta Microsoft, musisz podać prawidłowy numer telefonu dla systemu wysłać kod weryfikacyjny konta jako wiadomości SMS lub telefonicznie.
   2. Podczas rejestrowania konta jako konta Microsoft, należy podać identyfikator prawidłowy adres e-mail do odbierania automatyczne wiadomości e-mail do weryfikacji konta.
3. Sprawdź poprawność adresu e-mail wysyłane do listy Dystrybucyjnej.
4. Teraz możesz używać nowego konta Microsoft w programie Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Zarejestrować konta w programie Microsoft Developer Center
Microsoft Developer Center służy do rejestrowania danych firmy raz. Rejestratorem musi być prawidłową przedstawicielem firmy i podaj swoje informacje osobiste, aby zweryfikować swoją tożsamość. Osoby, rejestrowanie, należy użyć konta Microsoft, które są udostępniane dla firmy, **i mogą być używane to samo konto w portalu Azure publikowania.** Należy sprawdzić, upewnij się, że firma nie ma jeszcze konta Microsoft Developer Center przed podjęciem próby go utworzyć. W trakcie będą zbierane informacje adres firmy, informacje o koncie bank i informacjami. Zwykle można je uzyskać od osób kontaktowych związanych z finansami lub biznesem.

> [!IMPORTANT]
> Następujące składniki profilu Developer należy wykonać, aby przejść przez różne etapy oferta tworzenia i wdrażania.
>
>

| Profil Developer | Aby uruchomić projekt | Przemieszczanie | Publikowanie bezpłatne i szablon rozwiązania | Publikowanie komercyjnych |
| --- | --- | --- | --- | --- |
| Rejestracja firmy |Musi mieć |Musi mieć |Musi mieć |Musi mieć |
| NIP profilu |Optional (Opcjonalność) |Optional (Opcjonalność) |Optional (Opcjonalność) |Musi mieć |
| Konta bankowego |Optional (Opcjonalność) |Optional (Opcjonalność) |Optional (Opcjonalność) |Musi mieć |

> [!NOTE]
> Bring Your Own License (BYOL) jest obsługiwana tylko dla maszyn wirtualnych i jest uznawany za **wolnego** oferty.
>
>

### <a name="register-your-company-account"></a>Zarejestruj konto firmowe
1. Otwórz nowy Internet Explorer InPrivate lub Incognito przeglądarki Chrome Przeglądanie sesji aby upewnić się, że nie zalogowano Cię na koncie osobistym.
2. Przejdź do [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) Aby zarejestrować się jako sprzedawcy w Centrum deweloperów. Przed kontynuowaniem przeczytaj następujące ważne uwagi.

   > [!IMPORTANT]
   > Sprawdź, czy wiadomości e-mail identyfikator lub listę dystrybucyjną (listę dystrybucyjną zaleca się usunięcie zależności od osób) będzie używany do rejestrowania w Centrum deweloperów jest na najpierw zarejestrowany jako konta Microsoft. Jeśli nie, następnie zarejestruj za pomocą tej [łącze](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Ponadto **identyfikatorze znajdujące się w domenie firmy Microsoft żadnego e-mail tj. @microsoft.com nie można użyć** o rejestrację w Centrum deweloperów.
   >
   >

    ![Rysowanie][img-signin]
3. Ukończ pracę kreatora "Pomóż nam chronić Twoje konto", który będzie zweryfikować Twoją tożsamość za pomocą numeru telefonu lub poczty e-mail adresu.

    ![Rysowanie][img-verify]
4. W sekcji "Informacje o rejestracji konta" Wybierz Twoje **konta kraj/region** z menu rozwijanego.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"Sprzedaje — od" krajach:** aby sprzedaży z usług w witrynie Azure Marketplace, zarejestrowanych jednostki musi pochodzić z jednej z zatwierdzonych "sprzedaje — od" krajów powyżej. To ograniczenie jest ze względu na wypłaty i podatków. Aktywnie czekamy w najbliższej przyszłości Rozwiń tę listę krajów, więc wkrótce. Aby uzyskać więcej informacji, zobacz [zasady udziału Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Wybierz opcję "Typ konta" jako **firmy** , a następnie kliknij przycisk **dalej** przycisku.

   > [!IMPORTANT]
   > Aby lepiej zrozumieć typy kont i najlepiej można wybrać, Wyświetl stronę [konta typów, lokalizacji i opłat](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Wprowadź **wydawcy, nazwa wyświetlana**, zwykle nazwę swojej firmy.

   > [!TIP]
   > Wprowadzona w Centrum deweloperów wyświetlana nazwa wydawcy nie jest wyświetlana w portalu Azure Marketplace, gdy ofertę przejdzie do listy. Jednak to należy wprowadzić, aby ukończyć proces rejestracji.
   >
   >
7. Wprowadź **informacje kontaktowe** na weryfikację konta.

   > [!IMPORTANT]
   > Należy podać dokładnych informacji kontaktowych, ponieważ zostanie ono używane w naszych proces weryfikacji przez firmę może zostać zatwierdzona w Centrum deweloperów.
   >
   >
8. Wprowadź informacje kontaktowe **osoba zatwierdzająca w firmie**. Osoba zatwierdzająca w firmie będzie użytkownik, który można zweryfikować, że masz uprawnienia do tworzenia konta w Centrum deweloperów w imieniu swojej organizacji. Polecenie **dalej** można przenieść do **płatności, zobacz sekcję"** po zakończeniu.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Wprowadź informacje o Twojej płatności zapłacenia dla Twojego konta. Jeśli masz kod promocyjny, który obejmuje koszt rejestracji, które można wprowadzić w tym miejscu. W przeciwnym razie Podaj informacje o karcie kredytowej (lub PayPal rynkach obsługiwane). Gdy skończysz, kliknij przycisk **dalej** można przenieść do **"Ekran Przegląd"**.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Przejrzyj informacje o swoim koncie i upewnij się, że wszystkie ustawienia są poprawne. Następnie przeczytać i zaakceptować warunki i postanowienia o [Microsoft Azure Marketplace wydawcy umowy](http://go.microsoft.com/fwlink/?LinkID=699560). Pole wyboru, aby wskazać ma przeczytanie i zaakceptowanie tych postanowień.
11. Kliknij przycisk **Zakończ** aby potwierdzić swoją rejestrację. Komunikat potwierdzający wyślemy Ci na adres e-mail.
12. Jeśli planujesz opublikować tylko wolnego oferty, kliknij przycisk **przejdź do portalu publikowania Azure Marketplace** i można przejść do sekcji 3 tego dokumentu, [zarejestrować konta w portalu publikowania](#3-register-your-account-in-the-publishing-portal).

Jeśli planujesz opublikować komercyjnej oferuje (np. oferuje maszyny wirtualnej o modelu rozliczenia godzinowe), kliknij przycisk **zaktualizować informacje o Twoim koncie** gdzie należy wypełnić kwota podatku i informacji bankowych w danych konta Centrum deweloperów.

Jeśli wolisz zaktualizować podatku bank informacji i później, a następnie przejściem do następnej sekcji, tj. sekcji 3 tego dokumentu, [zarejestrować konta w portalu publikowania](#3-register-your-account-in-the-publishing-portal), a następnie tu później za pomocą łączy w Azure Publishing Portal.

> [!IMPORTANT]
> W przypadku ofert handlowych nie będzie mógł wypychania oferty do środowiska produkcyjnego bez uzupełnianie informacji kwota podatku i wartość konta bankowego.
>
>

Jeśli chcesz zaktualizować podatku bank informacji i później, można przejść do sekcji 3, [zarejestrować konta w portalu publikowania](#3-register-your-account-in-the-publishing-portal), a następnie ją później za pomocą łączy w portalu Azure Publishing.

### <a name="add-tax-and-banking-information"></a>Dodaj podatku i informacje dotyczące bankowości
 Jeśli chcesz opublikować komercyjnych oferty zakupu, należy dodać wypłaty i informacjami i przesłać je do weryfikacji w Centrum deweloperów. Jeśli zostanie opublikowany tylko wolnego oferty (lub oferuje BYOL), nie konieczne dodanie tych informacji. Można dodać go później, ale dopiero po pewnym czasie można sprawdzić poprawności informacji podatku. Jeśli znasz zaoferuje komercyjnych oferty zakupu, firma Microsoft zaleca, dodaj go jak najszybciej.

**Bank informacji**

1. Zaloguj się do [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) z kontem Microsoft.
2. Kliknij przycisk **konta wypłaty** w menu po lewej stronie w obszarze **Wybierz formę płatności** kliknij **konta bankowego** lub **PayPal**.

   > [!IMPORTANT]
   > Jeśli masz ofert handlowych, których klienci kupić w witrynie Marketplace, to konto będzie otrzymywania wypłaty za te zakupy.
   >
   >
3. Wprowadź informacje o płatności, a następnie kliknij przycisk **zapisać** po zakończeniu.

   > [!IMPORTANT]
   > Jeśli trzeba zaktualizować lub zmienić ustawienia konta wypłaty, wykonaj te same czynności, powyżej, zastępując bieżących informacji o nowe informacje. Zmiana konta wypłaty można opóźnić płatności przez maksymalnie jeden cykl płatności. To opóźnienie występuje, ponieważ musimy zweryfikować zmiany konta, jak robiliśmy po skonfigurowaniu konta wypłaty. Będzie nadal uzyskać płatności za łączną ilość po Twoje konto zostało zweryfikowane; płatności powodu dla bieżącej płatności cykl zostanie dodany do następnej.
   >
   >
4. Kliknij przycisk **Dalej**.

**Informacje o podatku**

1. Zaloguj się do [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) przy użyciu konta Microsoft (w razie potrzeby).
2. Kliknij przycisk **podatku profilu** w lewym menu.
3. Na **skonfigurować formularz podatku** strony, wybierz kraj lub region, w której są trwałe siedziby, a następnie wybierz kraj lub region, gdzie są przechowywane podstawowe możliwości dostępnych. Kliknij przycisk **Dalej**.
4. Wprowadź szczegóły podatku, a następnie kliknij przycisk **dalej**.

> [!WARNING]
> Nie można wypchnąć do produkcji programu komercyjnej oferuje przerywając kwota podatku i wartość konta bankowego informacji w ramach Twojego konta Microsoft Developer Center.
>
>

Jeśli masz problemy z rejestracji w Centrum deweloperów, zgodnie z poniższymi instrukcjami Zaloguj biletu pomocy technicznej

1. Przejdź do łącza pomocy technicznej [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. W obszarze **skontaktuj się z nami** sekcji, kliknij przycisk **przesłać zdarzenia** (jak pokazano na poniższym zrzucie ekranu)

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Wybierz opcję "Pomoc z Centrum deweloperów" jako **typ problemu** i "publikowania aplikacji i zarządzanie nimi" jako **kategorii**. Następnie kliknij przycisk "E-mail Start".

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Zostaną wyświetlone za pomocą strony logowania. Użyj logowania konta Microsoft w. Jeśli nie masz konta Microsoft, następnie utwórz ją przy użyciu to [łącze](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Wypełnij szczegóły problemu i subit bilet, klikając **przesyłania** przycisku.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Zarejestrować konta w portalu publikowania
[Publikowania portalu](http://publish.windowsazure.com) umożliwia publikowanie i zarządzanie nimi Twojej oferty.

1. Otwórz nowy Incognito przeglądarki Chrome lub sesja przeglądania Internet Explorer InPrivate Aby upewnić się, że nie zalogowano Cię na koncie osobistym.
2. Przejdź do obszaru [http://publish.windowsazure.com](http://publish.windowsazure.com) (Ustawienia — Integracje i usługi).
3. W przypadku nowego użytkownika i zalogowanie się do publikowania portalu po raz pierwszy, następnie użytkownik musi zalogować się przy użyciu ten sam identyfikator poczty e-mail przy użyciu której zarejestrowano danych konta Centrum deweloperów. W ten sposób danych konta Centrum deweloperów i publikowania portalu konto zostanie połączone ze sobą. Później można dodać innych członków firmy, którzy pracują w aplikacji, jako współadministrator w publikacji portalu, wykonując poniższe kroki.

Jeśli są dodawane jako współadministrator w publikacji portalu, następnie można zalogujesz się przy użyciu konta administratora co.

> [!TIP]
> Zasady udziału są opisane na [witryny sieci Web Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Kroki, aby dodać współadministrator w publikacji portalu
**Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby dodać co administratora.

> [!NOTE]
> **Dla nowych użytkowników** przed dodaniem współadministrator w publikacji portalu, upewnij się, że utworzono co najmniej jedną aplikację w publikacji portalu. Jest to wymagane jako **WYDAWCÓW** karcie są wyświetlane tylko w po utworzeniu co najmniej jedną aplikację w publikacji portalu.
>
>

1. Upewnij się, że identyfikator wiadomości e-mail współadministrator jest account(MSA) firmy Microsoft. Jeśli nie, zarejestruj go jako MSA, za pomocą tej [łącze](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Upewnij się, że istnieje co najmniej jedną aplikację przy użyciu konta administratora, przed podjęciem próby dodania co administratora.
3. Po wykonaniu powyższych czynności są czynności logowania do publikowania portal współadministrator identyfikator wiadomości e-mail, a następnie zaloguj się.
4. Zaloguj się teraz do publikowania portalu o identyfikatorze wiadomości e-mail administratora.
5. Przejdź do wydawcy -> konta -> Wybierz Administratorzy -> Dodaj współadministrator (zrzut ekranu poniżej)

   ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Kroki, aby usunąć współadministrator w publikacji portalu
**Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby usunąć co administratora.

1. Zaloguj się do publikowania portalu o identyfikatorze wiadomości e-mail administratora.
2. Przejdź do **wydawców** -> konta -> Wybierz **Administratorzy** -> **Współadministratorzy**.
3. Polecenie **X** przycisk współadministrator ma tot usuwania (zrzut ekranu poniżej).

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Kolejne kroki
Twoje konto jest utworzona i zarejestrowana, upewnij się, spełnienia lub spełnisz nietechnicznych wymagań wstępnych, aby opublikować ofertę, przeglądając [nietechnicznej wstępne](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md

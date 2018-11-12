---
title: Tworzenie i rejestrowanie konta wydawcy | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące tworzenia konta Microsoft Developer, więc po zatwierdzeniu, możesz sprzedawać różnych oferują typy w portalu Azure Marketplace.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ms.openlocfilehash: 3b1d0a7476cff2be00aa44bb8b859261099298d8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236694"
---
# <a name="create-a-microsoft-developer-account"></a>Utwórz konto Microsoft Developer
W tym artykule przedstawiono tworzenie konta niezbędne i procesu rejestracji, aby stać się zatwierdzonych Microsoft Developer Portal Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Tworzenie konta Microsoft
Aby rozpocząć proces publikowania, musisz utworzyć konto Microsoft. To konto będzie używane do zarejestrowania się w obu **Microsoft Developer Center** i **portalu wydawców Azure**. Powinna mieć tylko jedno konto Microsoft, dla ofert portalu Azure Marketplace. Nie powinien być określony dla usługi lub oferty.

Adres stanowiący nazwę użytkownika powinien należeć do domeny i kontrolowany przez Twój zespół IT. Publikowanie powiązanych działań powinno być wykonywane przy użyciu tego konta.

> [!WARNING]
> Wyrazy, takie jak **"Azure"** i **"Microsoft"** nie są obsługiwane w przypadku rejestracji konta Microsoft. Unikaj używania tych słów aby ukończyć proces rejestracji i tworzenia konta.
>
>

### <a name="guidelines-for-company-accounts"></a>Wytyczne dla firm
Podczas tworzenia konta firmy, należy przestrzegać następujących wytycznych, jeśli więcej niż jedna osoba będzie potrzebował dostępu do konta zalogować się przy użyciu konta Microsoft, które otworzyć konto.

> [!Important]
> Ważne Aby umożliwić wielu użytkownikom dostęp do danych konta Centrum deweloperów, firma Microsoft zaleca używanie usługi Azure Active Directory do przypisywania ról dla poszczególnych użytkowników, którzy mogą uzyskać dostępu do konta poprzez zalogowanie za pomocą ich poszczególne poświadczenia usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami konta](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users).

* Tworzenie konta Microsoft, przy użyciu adresu e-mail, który należy do domeny swojej firmy, ale nie do pojedynczej osoby — na przykład windowsapps@fabrikam.com.
* Ograniczanie dostępu do tego konta Microsoft do najmniejszej możliwej liczby deweloperów.
* Ustawianie firmowej poczty e-mail listy dystrybucyjnej, która zawiera wszystkich użytkowników, którzy potrzebują dostępu do konta dewelopera, a następnie dodać ten adres e-mail do informacji zabezpieczających. Dzięki temu wszyscy pracownicy na liście, aby otrzymać kody zabezpieczeń w razie oraz do zarządzania informacji zabezpieczających konta Microsoft. Jeśli skonfigurowanie listy dystrybucyjnej nie jest możliwe, właściciel konta e-mail poszczególnych będzie muszą być dostępne dostęp do i udostępniania kodu zabezpieczeń po wyświetleniu monitu (na przykład po dodaniu nowe informacje zabezpieczające do konta lub kiedy musi być dostępny z nowego urządzenia).
* Dodaj numer telefonu firmy, który nie wymaga rozszerzenia i jest dostępny dla innych członków zespołu klucza.
* Ogólnie rzecz biorąc należy mieć deweloperów, zaloguj się do konta dewelopera firmy przy użyciu zaufanych urządzeń. Wszyscy członkowie zespołu klucza powinien mieć dostęp do tych zaufanych urządzeń. Zmniejsza to potrzebę kody zabezpieczeń do wysłania, gdy dostęp do tego konta.
* Jeśli musisz zezwolić na dostęp do konta z komputera z niezaufanej, ograniczyć że dostęp do maksymalnie pięciu deweloperów. W idealnym przypadku tych deweloperów powinna dostęp do konta z maszyny, które współużytkować ten sam geograficzne i lokalizacja w sieci.
* Przejrzyj często stosowane w firmie zabezpieczające na stronie [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) się upewnić, że jest ona wszystkie bieżące.

Konto dewelopera powinni mieć dostęp przede wszystkim z zaufanych komputerów z systemem. Jest to istotne, ponieważ ma limitu liczby kody wygenerowane na jednym koncie na tydzień. Umożliwia ona także najbardziej bezproblemowe środowisko logowania.

Aby uzyskać więcej informacji na temat wytycznych konta dewelopera dodatkowe i zabezpieczeń, kliknij [tutaj](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instrukcje
1. Otwórz nowy Incognito przeglądarki Chrome lub Internet Explorer przeglądanie InPrivate sesji przeglądania w celu zapewnienia, że nie zalogowano do istniejącego konta.
2. Zarejestruj wiadomość e-mail (na wcześniej wskazówki dotyczące np. windowsapp@fabrikam.com) jako konto Microsoft za pośrednictwem łącza [ https://signup.live.com/signup.aspx ](https://signup.live.com/signup.aspx). Postępuj zgodnie z poniższymi instrukcjami.

   1. Podczas rejestrowania konta jako konta Microsoft, musisz podać prawidłowy numer telefonu dla systemu, aby wysłać kod weryfikacyjny konta jako wiadomość SMS lub telefonicznie.
   2. Podczas rejestrowania konta jako konta Microsoft, musisz podać poprawny identyfikator poczty e-mail do odbierania automatyczne wiadomości e-mail do weryfikacji konta.
3. Sprawdź adres e-mail wysyłane do listy Dystrybucyjnej.
4. Teraz możesz używać tego nowego konta Microsoft w Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Zarejestruj swoje konto w programie Microsoft Developer Center
Microsoft Developer Center jest używane do rejestrowania danych firmy, jeden raz. Bazy danych WHOIS musi być prawidłową przedstawiciela firmy, a następnie podaj swoje informacje osobiste, aby zweryfikować swoją tożsamość. Osoby, rejestrowanie, należy użyć konta Microsoft, który jest udostępniany dla firmy, **i mogą być używane to samo konto w portalu wydawców Azure.** Należy sprawdzić, upewnij się, że Twoja firma nie ma jeszcze konta Microsoft Developer Center przed podjęciem próby utwórz je. W trakcie będą zbierane informacje adres firmy, informacje o koncie bankowym i opodatkowania. Zwykle można je uzyskać od osób kontaktowych związanych z finansami lub biznesem.

> [!IMPORTANT]
> Aby można było przejść przez różne etapy oferty tworzenia i wdrażania, należy wykonać następujące składniki dla deweloperów w profilu.
>
>

| Profil dewelopera | Aby uruchomić projekt | Przemieszczanie | Bezpłatnie Publikuj i szablon rozwiązania | Publikowanie komercyjnych |
| --- | --- | --- | --- | --- |
| Rejestracja firmy |Musi mieć |Musi mieć |Musi mieć |Musi mieć |
| Profil NIP |Optional (Opcjonalność) |Optional (Opcjonalność) |Optional (Opcjonalność) |Musi mieć |
| Konta bankowego |Optional (Opcjonalność) |Optional (Opcjonalność) |Optional (Opcjonalność) |Musi mieć |

> [!NOTE]
> Bring Your Own License (BYOL) jest obsługiwana tylko dla maszyn wirtualnych i jest uznawany za **bezpłatne** oferty.
>
>

### <a name="register-your-company-account"></a>Zarejestruj konto firmowe
1. Otwórz nowy Internet Explorer InPrivate lub Incognito przeglądarki Chrome sesji przeglądania w celu zapewnienia, że użytkownik nie zalogowany do konta osobistego.
2. Przejdź do [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) Aby zarejestrować się jako sprzedawcy w Centrum deweloperów. Przed kontynuowaniem przeczytaj następujące ważne uwagi.

   > [!IMPORTANT]
   > Upewnij się, że wiadomości e-mail identyfikator lub listy dystrybucyjnej (listy dystrybucyjnej zaleca się wyeliminować zależność od osób), który będzie używany do rejestrowania w Centrum deweloperów przy pierwszej rejestracji jako konta Microsoft. Jeśli nie, następnie zarejestruj za pomocą tego [łącze](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Ponadto **e-mail dowolny identyfikator w domenie firmy Microsoft tj @microsoft.com nie można użyć** o rejestrację w Centrum deweloperów.
   >
   >

    ![Rysowanie][img-signin]
3. Ukończ Kreatora "Pomagają nam chronić Twoje konto", które spowodują zweryfikowanie Twojej tożsamości za pomocą numeru telefonu, adresu e-mail.

    ![Rysowanie][img-verify]
4. W sekcji "Konto rejestracji Info" Wybierz swoje **konta kraj/region** z menu rozwijanego.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"Sprzedaż — od" kraje:** sprzedawać swoje usługi w portalu Azure Marketplace, zarejestrowanych jednostki musi pochodzić z jednej z zatwierdzonych "sprzedaż — od" krajów powyżej. To ograniczenie jest ze względu na informacje dotyczące wypłat i opodatkowania. Firma Microsoft aktywnie chcą rozwiń ta lista krajów w najbliższej przyszłości, więc wkrótce. Aby uzyskać więcej informacji, zobacz [Marketplace — zasady udziału](https://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Wybierz opcję "Typ konta" jako **firmy** a następnie kliknij przycisk **dalej** przycisku.

   > [!IMPORTANT]
   > Aby lepiej zrozumieć typy kont i co jest najlepsze w przypadku wybrania, Wyświetl stronę [konta typów, lokalizacji i opłaty](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Wprowadź **Nazwa wyświetlana wydawcy**, zazwyczaj nazwę swojej firmy.

   > [!TIP]
   > Nazwa wyświetlana wydawcy, które są wprowadzone w Centrum deweloperów nie jest wyświetlana w portalu Azure Marketplace, po oferty przechodzi uwzględnione na liście. Ale to jest wymagane do ukończenia procesu rejestracji.
   >
   >
7. Wprowadź **informacje kontaktowe** weryfikacji konta.

   > [!IMPORTANT]
   > Należy podać dokładne informacje kontaktowe, ponieważ będzie ona używana w nasz proces weryfikacji dla Twojej firmy w celu uzyskania zatwierdzenia w Centrum deweloperów.
   >
   >
8. Wprowadź informacje kontaktowe **osoba zatwierdzająca w firmie**. Osoba zatwierdzająca w firmie to osoba, która można sprawdzić, czy masz uprawnienia do utworzenia konta w Centrum deweloperów w imieniu swojej organizacji. Kliknij pozycję **dalej** można przenieść do **płatności, zobacz sekcję"** po zakończeniu.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Wprowadź swoje informacje o płatności do zapłaty dla swojego konta. Jeśli masz kod promocyjny, który obejmuje koszt rejestracji, które można wprowadzić tutaj. W przeciwnym razie Podaj informacje o karcie kredytowej (lub PayPal w obsługiwane rynki). Gdy skończysz, kliknij przycisk **dalej** można przenieść do **"Ekranu Przegląd"**.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Przejrzyj informacje o swoim koncie i upewnij się, że wszystko jest poprawna. Następnie przeczytaj i zaakceptuj warunki i postanowienia programu [umowie wydawcy portalu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Pole wyboru, aby wskazać mają przeczytanie i zaakceptowanie tych postanowień.
11. Kliknij przycisk **Zakończ** aby potwierdzić swoją rejestrację. Komunikat z potwierdzeniem będziemy wysyłać na adres e-mail.
12. Jeśli zamierzasz opublikować tylko bezpłatnych ofert, kliknij przycisk **przejdź do portalu wydawców Azure Marketplace** i możesz przejść do sekcji 3 w tym dokumencie [Zarejestruj swoje konto w portalu wydawców](#3-register-your-account-in-the-publishing-portal).

Jeśli zamierzasz publikować komercyjne oferuje (np. oferty maszyny wirtualnej z modelem rozliczeń godzinowych), kliknij przycisk **zaktualizować informacje o Twoim koncie** gdzie należy wypełnić podatkowe i bankowe informacji w danych konta Centrum deweloperów.

Jeśli chcesz zaktualizować później informacje podatkowe i bankowe, a następnie można przenieść do następnej sekcji, czyli części 3 tego dokumentu, [Zarejestruj swoje konto w portalu wydawców](#3-register-your-account-in-the-publishing-portal), a następnie ją później za pomocą łączy w publikowania na platformie Azure Portal.

> [!IMPORTANT]
> W przypadku ofert handlowych nie będzie możliwe wypychanie Twoich ofert w środowisku produkcyjnym nie kończą działania informacje podatkowe i bankowe konta.
>
>

Jeśli chcesz zaktualizować później informacje podatkowe i bankowe, możesz przejść do sekcji 3, [Zarejestruj swoje konto w portalu wydawców](#3-register-your-account-in-the-publishing-portal), a następnie ją później za pomocą łączy w portalu wydawców Azure.

### <a name="add-tax-and-banking-information"></a>Dodaj podatkowe i bankowe informacji
 Jeśli chcesz opublikować komercyjnych oferty zakupu, należy również dodać informacje dotyczące wypłat oraz opodatkowania i przesłać go w celu weryfikacji w Centrum deweloperów. Jeśli zostanie opublikowany tylko bezpłatnych ofert (lub oferuje BYOL), nie trzeba dodać te informacje. Możesz je dodać później, ale dopiero po pewnym czasie w celu zweryfikowania informacji podatkowych. Jeśli znasz zaoferuje komercyjnych oferty zakupu, firma Microsoft zaleca, dodaj go jak najszybciej.

**Informacje o nazwie Bank**

1. Zaloguj się do [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) z Twoim kontem Microsoft.
2. Kliknij przycisk **konta informacje dotyczące wypłat** w menu po lewej stronie w obszarze **wybierania metody płatności** kliknij **konta bankowego** lub **PayPal**.

   > [!IMPORTANT]
   > Jeśli masz komercyjnych oferty, które klienci zakupu w portalu Marketplace, to konto gdzie otrzymasz informacje dotyczące wypłat dla tych zakupów.
   >
   >
3. Wprowadź informacje o płatności, a następnie kliknij przycisk **Zapisz** gdy są spełnione.

   > [!IMPORTANT]
   > Jeśli musisz zaktualizować lub zmienić ustawienia konta informacje dotyczące wypłat te same czynności powyżej, zastępując bieżące informacje o nowe informacje. Zmiana typu konta informacje dotyczące wypłat można opóźnić płatności za do jednego cyklu płatności. To opóźnienie występuje, ponieważ będziemy musieli zweryfikować zmiany konta, podobnie jak zrobiliśmy po skonfigurowaniu konta informacje dotyczące wypłat. Będziesz nadal opłacania cała kwota po zweryfikowaniu konta; płatności powodu dla bieżącej płatności cykl zostanie dodany do następnej.
   >
   >
4. Kliknij przycisk **Dalej**.

**Informacje podatkowe**

1. Zaloguj się do [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) z Twoim kontem Microsoft (jeśli jest to konieczne).
2. Kliknij przycisk **podatkowych profilu** w menu po lewej stronie.
3. Na **skonfigurować formularz podatku** stronie, wybierz kraj lub region, w którym masz stały rezydencji, a następnie wybierz kraj lub region, gdzie przechowywać obywatelstwa podstawowego. Kliknij przycisk **Dalej**.
4. Wprowadź szczegóły podatku, a następnie kliknij przycisk **dalej**.

> [!WARNING]
> Nie można wypchnąć do środowiska produkcyjnego, Twoje komercyjne oferuje przerywając informacje podatkowe i bankowe konta w ramach Twojego konta Microsoft Developer Center.
>
>

Jeśli masz problemy z rejestracją Centrum deweloperów, Prześlij zgłoszenie do pomocy technicznej zgodnie z poniższymi instrukcjami

1. Przejdź do łącza pomocy technicznej [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. W obszarze **skontaktuj się z nami** sekcji, kliknij przycisk **przesłać** (jak pokazano na poniższym zrzucie ekranu)

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Wybierz pozycję "Pomoc z Centrum deweloperów" jako **typ problemu** oraz "Opublikuj aplikacje i zarządzać nimi" jako **kategorii**. Następnie kliknij przycisk "Start email".

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Otrzymasz ze strony logowania. Użyj logowania konta Microsoft w. Jeśli nie masz konta Microsoft, następnie utwórz ją przy użyciu tego [łącze](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Wypełnij szczegóły tego problemu i jego subit--ticket, klikając **przesyłania** przycisku.

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Zarejestruj swoje konto w portalu wydawców
[Portal wydawców](http://publish.windowsazure.com) służy do publikowania i zarządzanie nimi swojej oferty.

1. Otwórz nowy Incognito przeglądarki Chrome lub Internet Explorer przeglądanie InPrivate sesji przeglądania w celu zapewnienia, że użytkownik nie zalogowany do konta osobistego.
2. Przejdź do obszaru [http://publish.windowsazure.com](http://publish.windowsazure.com) (Ustawienia — Integracje i usługi).
3. Jeśli nowy użytkownik i zalogowanie się do publikowania portal po raz pierwszy, następnie musisz zarejestrować się w taki sam identyfikator poczty e-mail, z którym jest zarejestrowana danych konta Centrum deweloperów. W ten sposób danych konta Centrum deweloperów i publikowania w portalu konta zostaną połączone ze sobą. Inni członkowie firmie, którzy pracują nad aplikacją, później można dodać jako współadministratora w publikowania portalu, wykonując poniższe kroki.

Jeśli dodano jako współadministrator podczas publikowania w portalu, następnie możesz zarejestrować się za pomocą konta współadministratora.

> [!TIP]
> Opisano w zasadach uczestnictwa [witryny sieci Web Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Kroki, aby dodać współadministrator podczas publikowania w portalu
**Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby dodać współadministratora.

> [!NOTE]
> **Nowi użytkownicy** przed dodaniem współadministrator podczas publikowania w portalu, upewnij się, że utworzono co najmniej jedną aplikację w publikowania portalu. Jest to wymagane jako **WYDAWCÓW** karty są wyświetlane tylko po to, po utworzeniu co najmniej jedną aplikację w publikowania portalu.
>
>

1. Upewnij się, że identyfikator poczty e-mail współadministrator account(MSA) firmy Microsoft. Jeśli nie, zarejestruj go jako zarządzanych kont usług, za pomocą tego [łącze](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Upewnij się, że istnieje co najmniej jedną aplikację przy użyciu konta administratora, zanim spróbujesz dodać współadministratora.
3. Po wykonaniu czynności powyższe kroki, zaloguj się do publikowania portalu z współadministrator identyfikator poczty e-mail, a następnie, w przypadku wylogowania.
4. Teraz Zaloguj się do publikowania w portalu przy użyciu identyfikatora poczty e-mail administratora.
5. Przejdź do wydawcy -> Wybierz -> konta administratorów -> Dodaj współadministrator (zrzut ekranu, podane poniżej)

   ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Kroki, aby usunąć współadministrator podczas publikowania w portalu
**Przy założeniu, że jesteś administratorem,** podane poniżej przedstawiono kroki, aby usunąć współadministratora.

1. Zaloguj się do publikowania w portalu przy użyciu identyfikatora poczty e-mail administratora.
2. Przejdź do **wydawców** -> Wybierz -> Twoje konto **Administratorzy** -> **Współadministratorzy**.
3. Kliknij pozycję **X** przycisk znajdujący się obok współadministrator ma tot usuwania (zrzut ekranu, podane poniżej).

    ![Rysowanie](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Kolejne kroki
Teraz, że Twoje konto zostanie utworzone i zarejestrowany, upewnij się, spełniają lub spełnisz Nietechniczne wymagań wstępnych, aby opublikować ofertę, przeglądając [Nietechniczne wstępne](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

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

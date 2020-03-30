---
title: Testowa aplikacja logiki | Azure Marketplace
description: W tym artykule wyjaśniono, jak utworzyć ich testowej, która łączy się z dynamics AX/CRM wystąpienie lub innego zasobu poza tylko Platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278386"
---
<a name="logic-app-test-drive"></a>Dysk testowy aplikacji logiki
====================

Ten artykuł jest przeznaczony dla wydawców, którzy mają swoją ofertę na AppSource i chcą utworzyć ich testowej, która łączy się z Dynamics AX/CRM wystąpienie lub innego zasobu poza tylko Platformy Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Jak zbudować testową jazdę logiczną
-----------------------------------

Dokumentacja dysku testowego dla aplikacji logiki testowych jest obecnie nadal na GitHub dla [operacji](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [customer engagement,](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)przejdź tam, aby dowiedzieć się więcej.

<a name="how-to-publish-a-test-drive"></a>Jak opublikować jazdę próbną
---------------------------

Teraz, gdy masz wbudowany testowy, w tej sekcji przechodzi przez każde z pól wymaganych do pomyślnego opublikowania dysku testowego.

![Włączanie funkcji jazdy testowej](./media/azure-resource-manager-test-drive/howtopub1.png)

Pierwszym i najważniejszym polem jest przełączanie, czy chcesz, aby test formularza ze wszystkimi wymaganymi polami był prezentowany do wypełnienia. Po wybraniu opcji Nie formularz zostanie **wyłączony,** a ponowne opublikowanie dysku testowego zostanie wyłączone, dysk testowy zostanie usunięty z produkcji.

*Uwaga:* Jeśli użytkownicy będą aktywnie używane przez dyski testowe, będą je uruchamiać do czasu wygaśnięcia sesji.

### <a name="details"></a>Szczegóły

Następna sekcja do wypełnienia to szczegóły dotyczące oferty testowej.

![Szczegóły dotyczące sterownika testowego](./media/azure-resource-manager-test-drive/howtopub2.png)

**Opis -** *[Wymagane pole]* W tym miejscu piszesz główny opis tego, co znajduje się na dysku testowym. Klient przyjdzie tutaj, aby przeczytać, jakie scenariusze test drive będzie pokrycie o produkcie. 

**Instrukcja obsługi —** *[Wymagane pole]* Jest to szczegółowy przewodnik po doświadczeniu dysku testowego. Klient otworzy to i może przejść przez dokładnie to, co chcesz, aby zrobić w całej ich jazdy testowej. Ważne jest, aby ta treść była łatwa do zrozumienia i naśladowania! (Musi to być plik .pdf)

**Test Drive Demo Video -** \[Zalecane\] Podobne do instrukcji obsługi, najlepiej jest dołączyć film instruktażowy doświadczenia z jazdy testowej. Klient będzie oglądać to przed lub podczas jazdy testowej i może przejść przez dokładnie to, co chcesz, aby zrobić w całej ich jazdy testowej. Ważne jest, aby ta treść była łatwa do zrozumienia i naśladowania!

- **Nazwa** — tytuł filmu
- **Link** - Musi być osadzony adres URL z YouTube lub Vimeo. Przykład, jak uzyskać osadzony adres URL, znajduje się poniżej:
- **Miniatura** — musi być obrazem wysokiej jakości (533x324) pikseli. Zaleca się wykonanie zrzutu ekranu przedstawiającego część środowiska z jazdy próbnej tutaj.

Poniżej opisano, jak te pola są wyświetlane dla klienta podczas korzystania z jazdy testowej.

![Pola dysku testowego wyglądają i czują](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Konfiguracja techniczna

Następna sekcja do wypełnienia jest, gdzie można skonfigurować aplikację logiki jazdy testowej i określić, jak konkretnie działają wystąpienia dysku testowego.

![Konfiguracja techniczna dysku testowego](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region** - *[Wymagane pole]* Wybrany region to miejsce, w którym można wybrać miejsce wdrażania zasobów aplikacji logiki dysku testowego.

    *Uwaga:* Jeśli aplikacja logiki ma żadnych zasobów niestandardowych, które są przechowywane w regionie, upewnij się, że region jest zaznaczony w tym miejscu. Najlepszym sposobem, aby to zrobić, jest **pełne wdrożenie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa** przed zapisaniem jej w tym miejscu.

- **Maksymalna liczba równoczesnych dysków testowych** - *[Wymagane pole]* Liczba wystąpień dysku testowego, które są już wdrożone i oczekują na dostęp dla wybranych regionów. Klienci mogą natychmiast uzyskać dostęp do tych dysków testowych, zamiast czekać na wdrożenie.

    *Uwaga:* Jeśli prowadzisz seminarium internetowe/klasę, w której chcesz, aby cała liczba uczniów N skorzystała z dysku testowego, zaleca się publikowanie z n liczbą wystąpień hot, a następnie po zakończeniu zajęć, aby ponownie opublikować normalną liczbę gorących wystąpień.

- **Czas trwania dysku testowego (godziny) -** *[Wymagane pole]* Czas trwania, przez jaki czas jazdy testowej pozostanie aktywny, w \# godzinach. Dysk testowy kończy się automatycznie po zakończeniu tego okresu.

- **Nazwa grupy zasobów platformy Azure —** *[Wymagane pole]* Zapisuj w nazwie grupy zasobów, w której są zapisywane dyski testowe aplikacji logiki.

- **Przypisz nazwę aplikacji logiki —** *[Wymagane pole]* Zapisuj w aplikacji logiki, która jest używana do przypisywania użytkownika na dysku testowym, zanim klient go otrzyma, napisz w tym miejscu w nazwie tej aplikacji logiki. Upewnij się, że ten plik jest zapisany w grupie zasobów powyżej.

- **Nazwa aplikacji logiki deprovision —** *[Wymagane pole]* Zapisz w nazwie aplikacji logiki dla usuwania obsługi administracyjnej wszystkich zasobów utworzonych na dysku testowym. Upewnij się, że ten plik jest zapisany w grupie zasobów powyżej.

- **Informacje o dostępie —** *[Wymagane pole]* Po tym, jak klient otrzyma jazdę testową, informacje o dostępie są im prezentowane. Te instrukcje są przeznaczone do udostępniania przydatnych parametrów wyjściowych z szablonu Menedżera zasobów dysku testowego. Aby uwzględnić parametry wyjściowe, należy użyć podwójnych nawiasów klamrowych (na przykład **{{outputname}}**) i zostaną one poprawnie wstawione w lokalizacji. (Formatowanie ciągów HTML jest zalecane w tym miejscu do renderowania w frontonie).

### <a name="test-drive-deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia dysku testowego

Ostatnią sekcją do wypełnienia jest możliwość automatycznego wdrażania dysków testowych przez połączenie subskrypcji platformy Azure i usługi Azure Active Directory (AD).

![Szczegóły subskrypcji wdrożenia dysku testowego](./media/azure-resource-manager-test-drive/subdetails1.png)

**Identyfikator subskrypcji platformy Azure** *[Wymagane pole]* Zapewnia dostęp do usług platformy Azure i witryny Azure portal. Subskrypcja jest, gdzie użycie zasobów jest zgłaszane i usługi są rozliczane. Jeśli nie masz jeszcze **osobnej** subskrypcji platformy Azure tylko dla dysków testowych, przejdź dalej i ją ześliń. Identyfikatory subskrypcji platformy Azure można znaleźć, logując się do witryny Azure portal i przechodząc do subskrypcji w menu po lewej stronie.
(Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subskrypcje platformy Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**Identyfikator dzierżawy usługi Azure AD** *[Wymagane pole]* Jeśli masz już dostępny identyfikator dzierżawy, możesz go znaleźć poniżej w identyfikatorze właściwości —\> katalog.

![Usługa Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

W przeciwnym razie utwórz nową dzierżawę w usłudze Azure Active Directory.

![Ekran właściwości usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Dzierżawcy usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**Identyfikator aplikacji usługi Azure AD** *[Wymagane pole]* Następnym krokiem jest utworzenie i zarejestrowanie nowej aplikacji. Użyjemy tej aplikacji do wykonywania operacji w wystąpieniu dysku testowego.

1. Przejdź do nowo utworzonego katalogu lub już istniejącego katalogu i wybierz pozycję Usługa Azure Active directory w okienku filtrów.
2. Wyszukaj "Rejestracje aplikacji" i kliknij "Dodaj"
3. Podaj nazwę aplikacji.
4. Wybierz typ jako "Aplikacja internetowa / API"
5. Podaj dowolną wartość w adresie URL logowania, nie będziemy\'używać tego pola.
6. Kliknij pozycję Utwórz.
7. Po utworzeniu aplikacji przejdź do właściwości\> — ustaw aplikację jako wielodostępną i naciśnij zapisz.

Kliknij pozycję Zapisz. Ostatnim krokiem jest, aby pobrać identyfikator aplikacji dla tej zarejestrowanej aplikacji i wkleić go w polu Test Drive tutaj.

![Identyfikator aplikacji usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Biorąc pod uwagę, że używamy aplikacji do wdrożenia w ramach subskrypcji, musimy dodać aplikację jako współautora subskrypcji. Instrukcje dla tych są jak poniżej:

1. Przejdź do bloku Subskrypcje i wybierz odpowiednią subskrypcję, która jest używany tylko dla dysku testowego.
1. Kliknij pozycję **Kontrola dostępu (IAM)**.
1. Kliknij kartę **Przypisania ról.**  ![Usługa Azure Active Directory, dodawanie nowego podmiotu kontroli dostępu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kliknij **pozycję Dodaj przypisanie roli**.
1. Ustaw rolę **współautora**.
1. Wpisz nazwę aplikacji usługi Azure AD i wybierz aplikację, aby przypisać rolę.
    ![Uprawnienia usługi Azure Active Directory](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kliknij przycisk **Zapisz**.

**Klucz aplikacji usługi Azure AD —** *[Wymagane pole]* Ostatecznym polem jest wygenerowanie klucza uwierzytelniania. W obszarze klawisze dodaj opis klucza, ustaw czas trwania, aby nigdy nie wygaśnie, a następnie wybierz pozycję Zapisz. Ważne **jest,** aby uniknąć posiadania wygasłego klucza, który spowoduje przerwanie jazdy testowej w produkcji. Skopiuj tę wartość i wklej ją do wymaganego pola Dysku testowego.

![Sekcja Klucze usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Nie można użyć usługi Azure App Registration Preview, ponieważ obecnie nie generuje klucza zakodowanego base64.


<a name="next-steps"></a>Następne kroki
----------

Teraz, gdy masz wszystkie pola jazdy testowej wypełnione, przejdź i **ponownie opublikować** swoją ofertę. Po przejściu procesu certyfikacji na dysku testowym należy przetestować doświadczenie klienta w **wersji zapoznawczej** oferty. Uruchom jazdę testową w interfejsie użytkownika i sprawdź, czy dyski testowe są w pełni wdrażane poprawnie.

Należy pamiętać, że nie należy usuwać żadnej części dysku testowego, ponieważ są one aprowidyzwane dla klientów, więc usługa dysku testowego automatycznie wyczyści te grupy zasobów po zakończeniu z nim przez klienta.

Gdy poczujesz się komfortowo z ofertą podglądu, teraz nadszedł czas, aby **przejść na żywo!** Istnieje proces ostatecznego przeglądu od firmy Microsoft po opublikowaniu oferty, aby dokładnie sprawdzić całe doświadczenie od końca do końca. Jeśli z jakiegoś powodu oferta zostanie odrzucona, wyślemy powiadomienie do kontaktu inżyniera dla Twojej oferty, wyjaśniając, co będzie trzeba naprawić.

Jeśli masz więcej pytań, szukasz porad dotyczących rozwiązywania problemów lub chcesz zwiększyć sukces na dysku testowym, przejdź do [faq, rozwiązywania problemów, & najlepszych praktyk.](./marketing-and-best-practices.md)

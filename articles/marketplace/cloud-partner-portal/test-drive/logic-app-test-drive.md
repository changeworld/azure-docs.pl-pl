---
title: Wersja testowa aplikacji logiki | Portal Azure Marketplace
description: Wyjaśnia sposób tworzenia ich wersji testowej, łączącego z wystąpienia programu Dynamics AX/CRM lub innych zasobów poza po prostu Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 3dc5642541f411acc93301f6bb9828e901aba449
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64940004"
---
<a name="logic-app-test-drive"></a>Wersja testowa aplikacji logiki
====================

Ten artykuł jest dla wydawców, którzy z opisem oferty w usłudze AppSource i chcesz utworzyć ich wersji testowej, łączącego z wystąpienia programu Dynamics AX/CRM lub innych zasobów poza po prostu platformy Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Jak utworzyć wersję testową aplikacji logiki
-----------------------------------

Testowanie dokumentacji dysku dla wersji testowych aplikację logiki obecnie jest nadal w witrynie GitHub [operacji](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [zaangażowania użytkowników](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)Przejdź miejsca, aby dowiedzieć się więcej.

<a name="how-to-publish-a-test-drive"></a>Jak opublikować wersję testową
---------------------------

Teraz, gdy masz utworzone wersji testowej, w tej sekcji przedstawiono wszystkie pola wymagane opublikowanie wersji testowej.

![Włącz tę funkcję w wersji testowej](./media/azure-resource-manager-test-drive/howtopub1.png)

Pole pierwszy i najważniejszy jest do wyświetlania i ukrywania ma testu formularza, wszystkie wymagane pola są prezentowane służących do wypełniania. Po wybraniu **nie** formularza zostanie wyłączona, a Jeśli publikujesz za pomocą wersji testowej wyłączone wersji testowej zostanie usunięta ze środowiska produkcyjnego.

*Uwaga*: Jeśli ma żadnych testów dyski są aktywnie używane przez użytkowników, tych wersji testowych będzie nadal działać do momentu wygaśnięcia ich sesji.

### <a name="details"></a>Szczegóły

Następnej sekcji, aby wypełnić to oferują szczegółowe informacje o wersji testowej.

![Szczegóły sterownika testu](./media/azure-resource-manager-test-drive/howtopub2.png)

**Opis —** *[wymagane pole]* jest to, gdzie wpisać opis główny o nowościach w wersji testowej. Klient będzie przejdź tutaj, aby odczytać scenariusze, jakie wersji testowej będzie obejmował dotyczących produktu. 

**Podręcznik użytkownika -** *[wymagane pole]* to szczegółowe wskazówki dotyczące środowiska wersji testowej. Spowoduje to otwarcie siedziba klienta i zapoznać się z dokładnie co chcesz zrobić w całym ich wersji testowej. Jest ważne, że ta zawartość jest łatwy do zrozumienia i postępuj zgodnie z! (Musi być plikiem PDF)

**Testowanie dysku demonstracyjny materiał wideo —** \[zalecane\] podobny do podręcznika użytkownika, najlepiej jest uwzględnienie Samouczek wideo dla środowiska wersji testowej. Klient będzie oczekiwał w tym wcześniejsze lub w trakcie ich wersji testowej i zapoznać się z dokładnie co chcesz zrobić w całym ich wersji testowej. Jest ważne, że ta zawartość jest łatwy do zrozumienia i postępuj zgodnie z!

- **Nazwa** — tytuł filmu wideo
- **Link** -musi być adresem URL osadzonego serwisu YouTube lub Vimeo. Przykład wprowadzenie adresu url osadzonego znajduje się poniżej:
- **Miniatura** -musi być obrazem o wysokiej jakości pikseli (533 x 324). Zalecane jest wykonanie zrzutu część środowiska wersji testowej w tym miejscu.

Poniżej przedstawiono, jak te pola wyświetlane dla klientów podczas ich wersji testowej.

![Test stacji pola wygląd i działanie](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Pomoc konfiguracji

Jest następnej sekcji, aby wypełnić, którym można skonfigurować aplikację logiki dysku testu i zdefiniuj sposób specjalnie wersji testowej wystąpienia pracy.

![Konfiguracja techniczne dysku testu](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region** -  *[wymagane pole]* regionu, możesz wybrać to, gdzie wybierz wdrożonym zasobów aplikacji logiki dysku testu w.

    *Uwaga:* Jeśli Twoja aplikacja logiki ma zasoby niestandardowe, które są przechowywane w regionie, upewnij się, że ten region wybrany w tym miejscu. Najlepszym sposobem, w tym celu jest **pełni wdrażanie aplikacji logiki lokalnie w Twojej subskrypcji platformy Azure w portalu i sprawdź, czy działa** przed napisaniem go tutaj.

- **Maksymalna współbieżnych wersji testowych** -  *[wymagane pole]* wystąpień z numer dysku testów, które są już wdrożone i Oczekiwanie na dostęp do wybranego regionu. Klienci mogą uzyskać dostęp natychmiast wersji testowych, a nie musisz czekać, aż do wdrożenia.

    *Uwaga:* Jeśli używasz seminarium w sieci Web/class, którego wszystkich uczniów, aby wypróbować wersję testową liczbę N, zaleca się publikowanie N liczby wystąpień w warstwie gorąca, a następnie drugi raz klasy jest ponad ponownie opublikować do Twojej normalną liczbę wystąpień w warstwie gorąca.

- **Dysk czas trwania testu (godziny) —** *[wymagane pole]* czas trwania ile wersji testowej pozostaną aktywne w \# godzin. Wersji testowej kończy się automatycznie po zakończeniu tego okresu.

- **Nazwa grupy zasobów platformy Azure —** *[wymagane pole]* zapisu w Nazwa grupy zasobów, w którym usługi Logic App testu dyski są zapisywane.

- **Przypisz nazwę aplikacji logiki —** *[wymagane pole]* zapisu w logice aplikacji, która umożliwia przypisywanie użytkownika w wersji testowej, zanim klient pobiera ją, zapisać nazwę tej aplikacji logiki. Upewnij się, że plik został zapisany w grupie zasobów powyżej.

- **Anulowanie aprowizacji Nazwa aplikacji logiki —** *[wymagane pole]* zapisu w nazwie aplikacji logiki anulować wszystkie zasoby utworzone w wersji testowej. Upewnij się, że plik został zapisany w grupie zasobów powyżej.

- **Dostęp do informacji -** *[wymagane pole]* po klient pobiera ich wersji testowej, uzyskiwanie dostępu do informacji są prezentowane do nich. Te instrukcje są przeznaczone do udostępniania parametry wyjściowe przydatne szablonu Menedżera zasobów testowych z dysku. Aby uwzględnić parametry wyjściowe, należy użyć podwójnego nawiasów klamrowych (na przykład **{{outputname}}** ), a ich zostanie wstawiony poprawnie w lokalizacji. (Formatowanie ciągu HTML zaleca się tutaj do renderowania frontonu).

### <a name="test-drive-deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia dysku testu

Sekcji końcowej, aby wypełnić ma mieć możliwość wdrażania wersji testowych automatycznie, łącząc subskrypcji platformy Azure i usługi Azure Active Directory (AD).

![Wdrażanie wersji subskrypcji Szczegóły testu](./media/azure-resource-manager-test-drive/subdetails1.png)

**Identyfikator subskrypcji platformy Azure** *[wymagane pole]* spowoduje to przydzielenie dostępu do usług platformy Azure i witryny Azure portal. Subskrypcja jest, gdzie raportowania użycia zasobów i usługi są rozliczane. Jeśli nie masz jeszcze **oddzielnych** subskrypcji platformy Azure dla wersji testowych tylko, przejdź dalej i utworzyć. Identyfikatory subskrypcji platformy Azure można znaleźć, logując się do witryny Azure portal i przechodząc do subskrypcji, w menu po lewej stronie.
(Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subskrypcje platformy Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**Identyfikator dzierżawy usługi Azure AD** *[wymagane pole]* Jeśli masz już dostępną identyfikator dzierżawy go poniżej można znaleźć we właściwościach -\> identyfikator katalogu.

![Usługa Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

W przeciwnym razie należy utworzyć nową dzierżawę usługi Azure Active Directory.

![Ekran właściwości w usłudze Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Usługa Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Dzierżawy usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**Identyfikator aplikacji w usłudze Azure AD** *[wymagane pole]* następnym krokiem jest tworzenie i rejestrowanie nowej aplikacji. Ta aplikacja będzie służy do wykonywania operacji w ramach wystąpienia wersji testowej.

1. Przejdź do katalogu nowo utworzonego lub już istniejących w katalogu, a następnie wybierz pozycję usługi Azure Active directory w okienku filtrów.
2. Wyszukaj "Rejestracje aplikacji" i kliknij przycisk "Dodaj"
3. Podaj nazwę aplikacji.
4. Wybierz typ jako "aplikacja sieci Web / interfejs API"
5. Wprowadzić dowolną wartość w polu adres URL logowania, zdobyliśmy\'t można za pomocą tego pola.
6. Kliknij przycisk Utwórz.
7. Po utworzeniu aplikacji przejdź do właściwości -\> ustaw ją jako wielodostępne, a następnie kliknij przycisk Zapisz.

Kliknij pozycję Zapisz. Ostatnim krokiem jest identyfikator aplikacji dla tej aplikacji zarejestrowanych i wklej go w tym polu wersji testowej.

![Identyfikator aplikacji usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Biorąc pod uwagę używamy aplikacji do wdrożenia do subskrypcji, musimy dodać aplikację jako współautora w subskrypcji. Instrukcje te są jak poniżej:

1. Przejdź do bloku subskrypcje i wybierz odpowiednią subskrypcję, którego używasz do testowej.
1. Kliknij przycisk **kontrola dostępu (IAM)** .
1. Kliknij przycisk **przypisań ról** kartę.  ![Usługa Azure Active Directory, dodając nową jednostkę kontroli dostępu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kliknij przycisk **Dodaj przypisanie roli**.
1. Ustaw rolę **Współautor**.
1. Wpisz nazwę aplikacji usługi Azure AD, a następnie wybierz aplikację, aby przypisać rolę.
    ![Uprawnienia usługi Active Directory platformy Azure](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kliknij pozycję **Zapisz**.

**Klucz aplikacji w usłudze Azure AD —** *[wymagane pole]* końcowego pole jest do generowania klucza uwierzytelniania. W obszarze klucze Dodaj opis klucza, ustaw czas nigdy nie wygasa, następnie wybierz pozycję Zapisz. Jest **ważne** pozwala uniknąć wygasłe klucza, co spowoduje przerwanie wersji testowej w środowisku produkcyjnym. Skopiuj tę wartość i wklej go do wymagane pole wersji testowej.

![Usługa Azure Active Directory klucze sekcji](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Nie można użyć wersji zapoznawczej rejestracji w aplikacji Azure, ponieważ nie generuje obecnie klucz zakodowane w formacie base64.


<a name="next-steps"></a>Kolejne kroki
----------

Teraz, gdy wszystkich pól wersji testowej wypełnione przejść i **ponownie opublikować** oferty. Po wersji testowej został przekazany proces certyfikacji, należy go dokładnie przetestować środowiska klienta w **Podgląd** oferty. Uruchom wersję testową w interfejsie użytkownika, a następnie sprawdź, czy wersji testowych są w pełni wdrażany prawidłowo.

Jest to należy zwrócić uwagę, czy nie powoduje usunięcia dowolnej części wersji testowej jako są udostępnione dla klientów, więc wersji testowej usługi będzie automatycznie wyczyścić te grupy zasobów po zakończeniu z nim pracy klienta.

Gdy masz doświadczenie z ofertą użytkownika (wersja zapoznawcza), nadszedł czas na **emisji na żywo**! Gdy oferta została publikowanych w podwójnego wyboru całe środowisko typu end to end jest proces ostateczny Przegląd, od firmy Microsoft. Jeśli z jakiegoś powodu odrzucenia pobiera oferty firma Microsoft będzie wysyłał powiadomienie do inżynierów skontaktuj się z ofertą wyjaśniający, co będzie potrzebne do rozwiązania.

Jeśli masz więcej pytań, czego szukasz poradę dotyczącą rozwiązywania problemów lub mają być łatwiejsza, wersji testowej, przejdź do [— często zadawane pytania, rozwiązywanie problemów i najlepsze rozwiązania](./marketing-and-best-practices.md).

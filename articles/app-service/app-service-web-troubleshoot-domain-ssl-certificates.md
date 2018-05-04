---
title: Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w aplikacjach sieci web platformy Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w aplikacjach sieci web platformy Azure
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w aplikacjach sieci web platformy Azure

W tym artykule wymieniono typowe problemy, które mogą wystąpić podczas konfigurowania domeny lub certyfikat SSL dla aplikacji sieci web platformy Azure. Opisuje również możliwe przyczyny i rozwiązania tych problemów.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [MSDN Azure i fora przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz polecenie **Get Support**.

## <a name="certificate-problems"></a>Problemy z certyfikatem

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>Nie można dodać powiązania certyfikatu SSL do aplikacji sieci Web 

### <a name="symptom"></a>Objaw

Dodaj powiązanie SSL, pojawi się następujący komunikat o błędzie:

**Nie można dodać powiązania SSL. Nie można ustawić certyfikat dla istniejącego wirtualnego adresu IP, ponieważ inny VIP używa już tego certyfikatów.**

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele powiązania SSL opartego na protokole IP dla tego samego adresu IP w wielu aplikacjach sieci web. Na przykład aplikacja sieci web A ma opartych na protokole SSL z stary certyfikat. Aplikacja sieci Web B z opartych na protokole SSL z nowego certyfikatu dla tego samego adresu IP. Po zaktualizowaniu powiązanie SSL aplikacji sieci web przy użyciu nowego certyfikatu go zakończy się niepowodzeniem z powodu następującego błędu od tego samego adresu IP jest używana dla innej aplikacji. 

### <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, użyj jednej z następujących metod:

- Usuń powiązania SSL opartego na protokole IP w aplikacji sieci web, który będzie używał starego certyfikatu. 
- Tworzenie nowego powiązania SSL opartego na protokole IP, który używa nowego certyfikatu.

### <a name="unable-to-delete-a-certificate"></a>Nie można usunąć certyfikatu 

### <a name="symptom"></a>Objaw

Podczas usuwania certyfikatu, pojawi się następujący komunikat o błędzie:

**Nie można usunąć certyfikatu, ponieważ jest on aktualnie używany w powiązaniu protokołu SSL. Powiązanie SSL muszą zostać usunięte przed usunięciem certyfikatu.**

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli certyfikat jest używany przez inną aplikację sieci web.

### <a name="solution"></a>Rozwiązanie

Usuń powiązanie SSL dla tego certyfikatu z aplikacji sieci web. Spróbuj usunąć certyfikat. Jeśli nadal nie można usunąć certyfikatu, wyczyść pamięć podręczną przeglądarki internetowe, otwórz ponownie portalu Azure w nowym oknie przeglądarki. A następnie spróbuj usunąć certyfikat.

### <a name="unable-to-purchase-an-app-service-certificate"></a>Nie można kupić certyfikat usługi aplikacji 

### <a name="symptom"></a>Objaw
Nie można kupić [certyfikatu usługi aplikacji](./web-sites-purchase-ssl-web-site.md) z portalu Azure.

### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie
Ten problem może wystąpić z następujących powodów:

- Plan usługi aplikacji jest "Wolne" lub "Shared". Protokół SSL nie jest obsługiwana dla tych warstwach cenowych. 

    **Rozwiązanie**: uaktualnić plan usługi aplikacji dla aplikacji sieci web do "Standard".

- Subskrypcja nie ma ważnej karty kredytowej.

    **Rozwiązanie**: Dodaj ważnej karty kredytowej z subskrypcją. 

- Oferta subskrypcji nie obsługuje zakupu certyfikatów usługi aplikacji, takich jak Microsoft Student.  

    **Rozwiązanie**: uaktualnienia subskrypcji. 

- Subskrypcja osiągnęła limit maksymalny wynoszący zakupów, które są dozwolone w przypadku subskrypcji.

    **Rozwiązanie**: certyfikaty usługi aplikacji istnieje limit 10 zakupów certyfikatu dla typów subskrypcji przenośna jako płatności i atrybutów Rozszerzonych. Dla innych typów subskrypcji limit wynosi 3. Aby zwiększyć limit, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certyfikat usługi aplikacji zostało oznaczone jako oszustwa. Pojawi się następujący komunikat o błędzie: "certyfikat ma została oznaczona flagą możliwym oszustwie. Żądanie jest obecnie w ramach przeglądu. Jeśli certyfikat nie zostanie można używać w ciągu 24 godzin".

    **Rozwiązanie**: Jeśli certyfikat jest oznaczone jako oszustwa i nie zostało rozpoznane po 24 godzinach, wykonaj następujące kroki:

    1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
    2. Przejdź do **certyfikaty usługi aplikacji**, wybrać certyfikat.
    3. Wybierz **konfiguracji certyfikatu** > **krok 2: Sprawdź** > **weryfikację domeny**. To wysyła powiadomienie e-mail do dostawcy certyfikatu Azure, aby rozwiązać ten problem.

## <a name="domain-problems"></a>Problemy z domeny

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>Zakupiony certyfikat SSL do niewłaściwej domenie

### <a name="symptom"></a>Objaw

Zakupiono certyfikat usługi aplikacji — dla niewłaściwej domenie i nie można zaktualizować certyfikat do użycia w prawidłowej domenie.

### <a name="solution"></a>Rozwiązanie

- Usunięcie tego certyfikatu, a następnie Kup nowy certyfikat.
- Jeśli bieżący certyfikat, który używa niewłaściwej domenie jest w stanie "Wydany", następnie również naliczania dla tego certyfikatu. Certyfikaty usługi aplikacji nie podlega zwrotowi, ale może nawiązać kontakt z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby zobaczyć, czy istnieją inne opcje. 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>Usługi aplikacji — certyfikat został odnowiony, ale wciąż pokazuje starego certyfikatu 

### <a name="symptom"></a>Objaw

Certyfikat usługi aplikacji został odnowiony, ale aplikacji sieci web, który używa certyfikatu z usługi aplikacji jest nadal przy użyciu starego certyfikatu. Ponadto otrzymany ostrzeżenie, że wymagany jest protokół HTTPS.

### <a name="cause"></a>Przyczyna 
Usługi aplikacji sieci Web uruchamia zadanie w tle co 8 godzin i synchronizuje zasobów certyfikatu, jeśli nie wprowadzono żadnych zmian. W związku z tym gdy Obróć lub zaktualizuj certyfikat, czasami aplikacji nadal pobiera starego certyfikatu, a nie zaktualizowanego certyfikatu. Jest to spowodowane zadania synchronizacji zasobów certyfikat nie został jeszcze uruchomiony. 
 
### <a name="solution"></a>Rozwiązanie

Można wymusić synchronizacji certyfikatu:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). Wybierz **certyfikaty usługi aplikacji**, a następnie wybierz certyfikat.
2. Kliknij przycisk **ponowne tworzenie klucza i zsynchronizuj**, a następnie kliknij przycisk **synchronizacji**. Trwa to trochę czasu. 
3. Po ukończeniu synchronizacji, zobacz następujące powiadomienie: "Pomyślnie zaktualizowano wszystkich zasobów przy użyciu najnowszych certyfikatu".

### <a name="domain-verification-is-not-working"></a>Weryfikacja domeny nie działa 

### <a name="symptom"></a>Objaw 
Certyfikat usługi aplikacji wymaga weryfikacji domeny certyfikat jest gotowa do użycia. Po kliknięciu **Sprawdź**, proces kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie
Ręcznie Sprawdź domenę, dodając rekord TXT:
 
1.  Przejdź do dostawcy usługi nazw domen (DNS, Domain Name System), który zawiera nazwę domeny.
2.  Dodaj rekord TXT domenę, która używa wartość tokenu domeny, która jest wyświetlana w portalu Azure. 

Poczekaj kilka minut dla propagacji DNS do uruchomienia, a następnie kliknij przycisk **Odśwież** przycisk, aby wyzwolić weryfikacji. 

Alternatywna metoda można ręcznie zweryfikować jest "metodę HTML strony sieci Web", która może służyć do urzędu certyfikacji potwierdzić własność domeny domeny, której certyfikat został wystawiony dla Zezwalaj.

1.  Utwórz plik HTML o nazwie {tokenu weryfikacji domeny} HTML. 
2.  Zawartość tego pliku powinna być wartość tokenu weryfikacji domeny.
3.  Przekazywanie tego pliku w katalogu głównym serwera sieci web, który jest hostem domeny
4.  Kliknij przycisk **Odśwież** do sprawdzania stanu certyfikatu. Może upłynąć kilka minut, aż weryfikacji zakończyć.

Na przykład, jeśli kupujesz to standardowy certyfikat do witryny azure.com z domeny weryfikacji tokenu "1234abcd" żądania sieci web przeprowadzane http://azure.com/1234abcd.html 1234abcd powinna zostać zwrócona. 

> [!IMPORTANT]
> Kolejność certyfikat ma tylko 15 dni, aby ukończyć operację weryfikacji domeny. Po 15 dniach odmowa przez urząd certyfikacji certyfikatu, a nie są naliczane opłaty dla certyfikatu. W takim przypadku należy usunąć ten certyfikat i spróbuj ponownie.
>
> 

### <a name="unable-to-purchase-a-domain"></a>Nie można kupić domeny

### <a name="symptom"></a>Objaw
Nie można kupić domenę z aplikacji sieci Web lub domena usługi aplikacji w portalu Azure.

### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

Ten problem występuje w jednej z następujących przyczyn:

- Bez karty kredytowej na subskrypcji platformy Azure lub nieprawidłowy karty kredytowej.

    **Rozwiązanie**: Dodaj ważnej karty kredytowej z subskrypcją, jeśli nie masz.

- Jeśli nie jesteś właścicielem subskrypcji, nie masz uprawnień, aby kupić domenę.

    **Rozwiązanie**: [Dodaj rolę właściciela](../billing/billing-add-change-azure-subscription-administrator.md) do konta lub kontaktować się z administratorem subskrypcji, aby uzyskać uprawnienia, aby kupić domenę.
- Osiągnięto limit do zakupu domenach w Twojej subskrypcji. Bieżący limit wynosi 20.

    **Rozwiązanie**: do żądania Zwiększ limit, skontaktuj się z pomocą [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Typ Twojej subskrypcji platformy Azure nie obsługuje kupowania domeny usługi aplikacji.

    **Rozwiązanie**: uaktualnienia subskrypcji platformy Azure na inne typy subskrypcji, takie jak subskrypcja płatność za rzeczywiste użycie.

### <a name="unable-to-add-a-hostname-to-web-app"></a>Nie można dodać nazwę hosta aplikacji sieci Web 

### <a name="symptom"></a>Objaw

Po dodaniu nazwy hosta procesu nie powiodło się sprawdzanie poprawności i zweryfikować domenę.

### <a name="cause"></a>Przyczyna 

Ten problem występuje w jednej z następujących przyczyn:

- Nie masz uprawnień do dodawania nazwy hosta.

    **Rozwiązanie**: Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia, aby dodać nazwę hosta.
- Nie można zweryfikować prawa własności domeny.

    **Rozwiązanie**: Sprawdź, czy Twoje CNAME lub rekord są skonfigurowane poprawnie. Aby zamapować niestandardową domenę do aplikacji sieci web, należy utworzyć CNAME lub rekordu. Jeśli chcesz użyć domeny głównej, należy użyć rekordy A i TXT:

    |Typ rekordu|Host|Wskaż pozycję|
    |------|------|-----|
    |A|@|Adres IP dla aplikacji sieci web|
    |TXT|@|< nazwa aplikacji >. azurewebsites.net|
    |CNAME|www|< nazwa aplikacji >. azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>Nie można rozpoznać nazwy DNS

### <a name="symptom"></a>Objaw

Pojawi się komunikat o błędzie "rekord DNS nie można zlokalizować".

### <a name="cause"></a>Przyczyna
Ten problem występuje w jednej z następujących przyczyn:

- Czas wygaśnięcia (TTL) okres nie wygasł. Sprawdź konfigurację systemu DNS dla domeny ustalić wartość TTL, a następnie poczekaj, aż okresu wygaśnięcia.
- Konfiguracja DNS jest nieprawidłowa.

### <a name="solution"></a>Rozwiązanie
- Poczekaj, aż ten problem zostać rozwiązany automatycznie w ciągu 48 godzin.
- Jeśli ustawienie TTL można zmienić w konfiguracji DNS, zmień wartość na 5 minut, aby zobaczyć, czy to rozwiąże problem.
- Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) można sprawdzić, czy domenę na adres IP aplikacji sieci web. Jeśli nie, należy skonfigurować rekord A poprawny adres IP w aplikacji sieci web.

### <a name="restore-a-deleted-domain"></a>Przywracanie usuniętych domeny 

### <a name="symptom"></a>Objaw
Domeny nie jest już widoczna w portalu Azure.

### <a name="cause"></a>Przyczyna 
Domeny może przypadkowo usunięto przez właściciela subskrypcji.

### <a name="solution"></a>Rozwiązanie
Jeśli domena została usunięta mniej niż 7 dni temu, domena nie rozpoczął proces usuwania. W takim przypadku można kupić tej samej domenie ponownie w portalu Azure w ramach tej samej subskrypcji (Upewnij się, że w wyszukiwania wpisz nazwę domeny dokładne). Użytkownik nie zostanie obciążona ponownie dla tej domeny. Jeśli domena została usunięta ponad siedem dni, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby uzyskać pomoc przywrócić domeny.

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>Domena niestandardowa zwraca 404 lub lokacji niedostępny 

### <a name="symptom"></a>Objaw

Przejdź do witryny przy użyciu nazwy domeny niestandardowej, pojawi się następujący komunikat o błędzie:

**Nie odnaleziono aplikacji sieci Web 404 błędu.**


### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

**Przyczyny 1** 

Brak rekordu CNAME i A domeny niestandardowej, która została skonfigurowana. 

**Rozwiązanie dla przyczyny 1**

- Jeśli dodano rekord A, upewnij się, że dodano także rekord TXT. Aby uzyskać więcej informacji, zobacz [Utwórz a rekordami](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Jeśli nie masz do korzystania z domeny katalogu głównego aplikacji sieci web, zaleca się przy użyciu rekordu CNAME zamiast rekordu.
- Nie należy używać zarówno CNAME i A rekord dla tej domeny. Może to spowodować konflikt i uniemożliwić rozpoznawanie domeny. 

**Przyczyny 2** 

Stary adres IP dla domeny nadal może buforowanie przeglądarki internetowej. 

**Rozwiązanie dla przyczyny 2**

Wyczyść przeglądarki. Dla urządzeń z systemem Windows, należy uruchomić polecenie `ipconfig /flushdns`. Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) można sprawdzić, czy domenę na adres IP aplikacji sieci web. 

### <a name="unable-to-add-subdomain"></a>Nie można dodać poddomeny 

### <a name="symptom"></a>Objaw

Nie można dodać nowej nazwy hosta w aplikacji sieci web, aby przypisać domenę podrzędną.

### <a name="solution"></a>Rozwiązanie

- Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia do dodawania nazwę hosta aplikacji sieci web.
- Jeśli potrzebujesz więcej poddomen, zaleca się zmienić hosting domeny do usługi Azure DNS. Za pomocą usługi Azure DNS, nazwy hostów 500 można dodać do aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [dodać poddomeną](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 



















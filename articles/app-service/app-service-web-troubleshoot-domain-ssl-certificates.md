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
ms.openlocfilehash: 59a9011edef49494288716ab16f30e28e440293b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w aplikacjach sieci web platformy Azure

W tym artykule wymieniono typowe problemy, które mogą wystąpić podczas konfigurowania domeny lub certyfikat SSL dla aplikacji sieci web platformy Azure. Opisuje również możliwe przyczyny i potencjalne rozwiązania tych problemów.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **Get Support**.

## <a name="certificate-problems"></a>Problemy z certyfikatem

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Nie można dodać powiązania certyfikatu SSL do aplikacji sieci web 

#### <a name="symptom"></a>Objaw

Dodaj powiązanie SSL, pojawi się następujący komunikat o błędzie:

"Nie można dodać powiązania SSL. Nie można ustawić certyfikat dla istniejącego wirtualnego adresu IP, ponieważ inny VIP już używa tego certyfikatu."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele powiązania SSL opartego na protokole IP dla tego samego adresu IP w wielu aplikacjach sieci web. Na przykład aplikacja sieci web A ma opartych na protokole SSL przy użyciu starego certyfikatu. Aplikacja sieci Web B ma opartych na protokole SSL przy użyciu nowego certyfikatu dla tego samego adresu IP. Po zaktualizowaniu powiązanie SSL aplikacji sieci web przy użyciu nowego certyfikatu go kończy się niepowodzeniem z powodu następującego błędu ponieważ ten sam adres IP jest używana dla innej aplikacji. 

#### <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, użyj jednej z następujących metod:

- Usuń powiązania SSL opartego na protokole IP w aplikacji sieci web, który będzie używał starego certyfikatu. 
- Tworzenie nowego powiązania SSL opartego na protokole IP, który używa nowego certyfikatu.

### <a name="you-cant-delete-a-certificate"></a>Nie można usunąć certyfikatu 

#### <a name="symptom"></a>Objaw

Podczas usuwania certyfikatu, pojawi się następujący komunikat o błędzie:

"Nie można usunąć certyfikatu, ponieważ jest on aktualnie używany w powiązaniu protokołu SSL. Powiązanie SSL muszą zostać usunięte przed usunięciem certyfikatu."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli innej aplikacji sieci web używa certyfikatu.

#### <a name="solution"></a>Rozwiązanie

Usuń powiązanie SSL dla tego certyfikatu z aplikacji sieci web. Spróbuj usunąć certyfikat. Jeśli nadal nie można usunąć certyfikatu, wyczyść pamięć podręczną przeglądarki internetowe i ponownie otwórz portalu Azure w nowym oknie przeglądarki. Spróbuj usunąć certyfikat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nie można kupić certyfikat usługi aplikacji 

#### <a name="symptom"></a>Objaw
Nie można kupić [certyfikatu w usłudze Azure App Service](./web-sites-purchase-ssl-web-site.md) z portalu Azure.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie
Ten problem może wystąpić z następujących powodów:

- Plan usługi aplikacji jest bezpłatna lub udostępnione. Te warstw cenowych nie obsługują protokołu SSL. 

    **Rozwiązanie**: Uaktualnij plan usług aplikacji dla aplikacji sieci web do standardowego.

- Subskrypcja nie ma ważnej karty kredytowej.

    **Rozwiązanie**: Dodaj ważnej karty kredytowej z subskrypcją. 

- Oferta subskrypcji nie obsługuje zakupu certyfikatów usługi aplikacji, takich jak Microsoft Student.  

    **Rozwiązanie**: uaktualnienia subskrypcji. 

- Subskrypcja osiągnęła limit zakupów, które są dozwolone w przypadku subskrypcji.

    **Rozwiązanie**: certyfikaty usługi aplikacji istnieje limit 10 zakupów certyfikatu dla typów subskrypcji płatność za rzeczywiste użycie i atrybutów Rozszerzonych. Dla innych typów subskrypcji limit wynosi 3. Aby zwiększyć limit, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certyfikat usługi aplikacji zostało oznaczone jako oszustwa. Odebrano komunikat o błędzie: "certyfikat ma została oznaczona flagą możliwym oszustwie. Żądanie jest obecnie w ramach przeglądu. Jeśli certyfikat nie zostanie można używać w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure."

    **Rozwiązanie**: Jeśli certyfikat jest oznaczone jako oszustwa i nie zostanie rozwiązany po 24 godzinach, wykonaj następujące kroki:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Przejdź do **certyfikaty usługi aplikacji**i wybrać certyfikat.
    3. Wybierz **konfiguracji certyfikatu** > **krok 2: Sprawdź** > **weryfikację domeny**. Ten krok wysyła powiadomienie e-mail do dostawcy certyfikatu Azure, aby rozwiązać ten problem.

## <a name="domain-problems"></a>Problemy z domeny

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Zakupiono certyfikatu SSL dla niewłaściwej domenie

#### <a name="symptom"></a>Objaw

Zakupiono certyfikat usługi aplikacji — dla niewłaściwej domenie. Nie można zaktualizować certyfikat do użycia w prawidłowej domenie.

#### <a name="solution"></a>Rozwiązanie

Usunięcie tego certyfikatu, a następnie Kup nowy certyfikat.

Jeśli bieżący certyfikat, który używa niewłaściwej domenie jest w stanie "Wydany", również zostaną naliczone opłaty dla tego certyfikatu. Certyfikaty usługi aplikacji nie podlega zwrotowi, ale może nawiązać kontakt z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby zobaczyć, czy istnieją inne opcje. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Usługi aplikacji — certyfikat został odnowiony, ale aplikacja sieci web zawiera stary certyfikat 

#### <a name="symptom"></a>Objaw

Certyfikat usługi aplikacji został odnowiony, ale aplikacji sieci web, który używa certyfikatu z usługi aplikacji jest nadal przy użyciu starego certyfikatu. Ponadto otrzymany ostrzeżenie, że wymagany jest protokół HTTPS.

#### <a name="cause"></a>Przyczyna 
Funkcja Web Apps w usłudze Azure App Service uruchamia zadanie w tle co 8 godzin i synchronizuje zasobów certyfikatu, jeśli nie wprowadzono żadnych zmian. Obróć lub zaktualizuj certyfikat, czasami aplikacji nadal pobiera stary certyfikat i nie zaktualizowanego certyfikatu. Dzieje się tak nie jeszcze uruchomione zadanie, aby zsynchronizować zasób certyfikatu. 
 
#### <a name="solution"></a>Rozwiązanie

Można wymusić synchronizacji certyfikatu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **certyfikaty usługi aplikacji**, a następnie wybierz certyfikat.
2. Wybierz **ponowne tworzenie klucza i zsynchronizuj**, a następnie wybierz **synchronizacji**. Synchronizacji trwa trochę czasu. 
3. Po ukończeniu synchronizacji, zobacz następujące powiadomienie: "Pomyślnie zaktualizowano wszystkich zasobów przy użyciu najnowszych certyfikatu."

### <a name="domain-verification-is-not-working"></a>Weryfikacja domeny nie działa 

#### <a name="symptom"></a>Objaw 
Certyfikat usługi aplikacji wymaga weryfikacji domeny certyfikat jest gotowa do użycia. Po wybraniu **Sprawdź**, proces kończy się niepowodzeniem.

#### <a name="solution"></a>Rozwiązanie
Ręcznie Sprawdź domenę, dodając rekord TXT:
 
1.  Przejdź do dostawcy usługi nazw domen (DNS, Domain Name System), który zawiera nazwę domeny.
2.  Dodaj rekord TXT domenę, która używa wartość tokenu domeny, która jest wyświetlana w portalu Azure. 

Poczekaj kilka minut dla propagacji DNS do uruchomienia, a następnie wybierz **Odśwież** przycisk, aby wyzwolić weryfikacji. 

Alternatywnie służy metoda HTML strony sieci Web można ręcznie zweryfikować domenę. Ta metoda umożliwia urzędowi certyfikacji potwierdzić własność domenę, w której certyfikat został wystawiony dla domeny.

1.  Utwórz plik HTML o nazwie .html {tokenu weryfikacji domeny}. Zawartość tego pliku powinna być wartość tokenu weryfikacji domeny.
3.  Przekazywanie tego pliku w katalogu głównym serwera sieci web, który jest hostem domeny.
4.  Wybierz **Odśwież** do sprawdzania stanu certyfikatu. Może upłynąć kilka minut, aż weryfikacji zakończyć.

Na przykład, jeśli kupujesz to standardowy certyfikat do witryny azure.com z 1234abcd tokenu weryfikacji domeny, żądania sieci web wprowadzone http://azure.com/1234abcd.html 1234abcd powinna zostać zwrócona. 

> [!IMPORTANT]
> Kolejność certyfikat ma tylko 15 dni, aby ukończyć operację weryfikacji domeny. Po 15 dniach urząd certyfikacji nie zezwala na certyfikat, a nie są naliczane opłaty dla certyfikatu. W takim przypadku należy usunąć ten certyfikat i spróbuj ponownie.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nie można kupić domeny

#### <a name="symptom"></a>Objaw
Nie można kupić domenę z domeny aplikacji sieci Web lub usługi aplikacji w portalu Azure.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

Ten problem występuje w jednej z następujących przyczyn:

- Karta kredytowa nie jest dostępne dla subskrypcji platformy Azure lub karty kredytowej jest nieprawidłowy.

    **Rozwiązanie**: Dodaj ważnej karty kredytowej z subskrypcją.

- Nie jesteś właścicielem subskrypcji, dlatego nie masz uprawnień, aby kupić domenę.

    **Rozwiązanie**: [Dodaj rolę właściciela](../billing/billing-add-change-azure-subscription-administrator.md) do swojego konta. Lub skontaktuj się z administratorem subskrypcji, aby uzyskać uprawnienia do zakupu domeny.
- Osiągnięto limit do zakupu domenach w Twojej subskrypcji. Bieżący limit wynosi 20.

    **Rozwiązanie**: Aby zażądać zwiększenia limitu, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Typ Twojej subskrypcji platformy Azure nie obsługuje kupowania domeny usługi App Service.

    **Rozwiązanie**: uaktualnienia subskrypcji platformy Azure na inny typ subskrypcji, takie jak subskrypcja płatność za rzeczywiste użycie.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Nie można dodać nazwę hosta aplikacji sieci web 

#### <a name="symptom"></a>Objaw

Po dodaniu nazwy hosta, proces kończy się niepowodzeniem do sprawdzania poprawności i zweryfikować domenę.

#### <a name="cause"></a>Przyczyna 

Ten problem występuje w jednej z następujących przyczyn:

- Nie masz uprawnień do dodawania nazwy hosta.

    **Rozwiązanie**: Poproś administratora subskrypcji, aby zezwolić na dodanie nazwy hosta.
- Nie można zweryfikować prawa własności domeny.

    **Rozwiązanie**: Sprawdź, czy Twoje CNAME lub rekord jest poprawnie skonfigurowane. Aby zamapować niestandardową domenę do aplikacji sieci web, należy utworzyć rekord CNAME lub rekord A. Jeśli chcesz użyć domeny głównej, należy użyć rekordy A i TXT:

    |Typ rekordu|Host|Wskaż pozycję|
    |------|------|-----|
    |A|@|Adres IP dla aplikacji sieci web|
    |TXT|@|< nazwa aplikacji >. azurewebsites.net|
    |CNAME|www|< nazwa aplikacji >. azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>DNS nie można rozpoznać

#### <a name="symptom"></a>Objaw

Odebrany komunikat o błędzie:

"Rekord DNS nie można zlokalizować."

#### <a name="cause"></a>Przyczyna
Ten problem występuje w jednej z następujących przyczyn:

- Czas do okresu (czasu wygaśnięcia TTL) na żywo nie wygasł. Sprawdź konfigurację systemu DNS dla domeny ustalić wartość TTL, a następnie poczekaj, aż okresu wygaśnięcia.
- Konfiguracja DNS jest nieprawidłowa.

#### <a name="solution"></a>Rozwiązanie
- Poczekaj, aż ten problem zostać rozwiązany automatycznie w ciągu 48 godzin.
- Jeśli ustawienie TTL można zmienić w konfiguracji DNS, zmień wartość na 5 minut, aby zobaczyć, czy to rozwiąże problem.
- Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) można sprawdzić, czy domena aplikacji sieci web, adres IP. W przeciwnym razie należy skonfigurować rekord A poprawny adres IP w aplikacji sieci web.

### <a name="you-need-to-restore-a-deleted-domain"></a>Należy przywrócić usuniętego domeny 

#### <a name="symptom"></a>Objaw
Domeny nie jest już widoczna w portalu Azure.

#### <a name="cause"></a>Przyczyna 
Właściciel subskrypcji może przypadkowo usunął domeny.

#### <a name="solution"></a>Rozwiązanie
Jeśli domena została usunięta mniej niż siedmiu dni temu, domena nie rozpoczął proces usuwania. W takim przypadku można kupić tej samej domenie ponownie w portalu Azure w ramach tej samej subskrypcji. (Należy wpisać nazwę domeny dokładnie w polu wyszukiwania.) Nie zostanie obciążona ponownie dla tej domeny. Jeśli domena została usunięta ponad siedem dni, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby uzyskać pomoc przywrócić domeny.

### <a name="a-custom-domain-returns-a-404-error"></a>Zwraca błąd 404, domenę niestandardową 

#### <a name="symptom"></a>Objaw

Przejdź do witryny przy użyciu nazwy domeny niestandardowej, pojawi się następujący komunikat o błędzie:

"Nie można odnaleźć aplikacji sieci Web 404 błędu."


#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

**Przyczyny 1** 

Brak rekordu CNAME i A domeny niestandardowej, która została skonfigurowana. 

**Rozwiązanie dla przyczyny 1**

- Jeśli dodano rekord A, upewnij się, rekord TXT jest także dodawane. Aby uzyskać więcej informacji, zobacz [Utwórz rekord a](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Jeśli nie masz do korzystania z domeny katalogu głównego aplikacji sieci web, zaleca się używania rekordu CNAME zamiast rekordu A.
- Nie używaj zarówno rekord CNAME, jak i rekordu A dla tej samej domenie. Może to spowodować konflikt i uniemożliwić rozwiązywany domeny. 

**Przyczyny 2** 

Stary adres IP dla domeny nadal może buforowanie przeglądarki internetowej. 

**Rozwiązanie dla przyczyny 2**

Wyczyść przeglądarki. Dla urządzeń z systemem Windows, należy uruchomić polecenie `ipconfig /flushdns`. Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) można sprawdzić, czy domena aplikacji sieci web, adres IP. 

### <a name="you-cant-add-a-subdomain"></a>Nie można dodać poddomeny 

#### <a name="symptom"></a>Objaw

Nie można dodać nową nazwę hosta aplikacji sieci web, aby przypisać poddomeny.

#### <a name="solution"></a>Rozwiązanie

- Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia do dodawania nazwę hosta aplikacji sieci web.
- Jeśli potrzebujesz więcej poddomen, zaleca się zmienić hosting domeny do usługi Azure DNS. Za pomocą usługi Azure DNS, nazwy hosta 500 można dodać do aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [dodania poddomeny](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 



















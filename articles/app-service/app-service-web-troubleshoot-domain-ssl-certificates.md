---
title: Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w usłudze Azure web apps | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w usłudze Azure web apps
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e10c6580c29784f8d56a31e267c47ec08894113b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418368"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w usłudze Azure web apps

W tym artykule wymieniono typowe problemy, które można napotkać podczas konfigurowania domeny lub certyfikatu protokołu SSL dla aplikacji sieci web platformy Azure. Opisano również możliwe przyczyny i potencjalne rozwiązania tych problemów.

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [na forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz **Uzyskaj pomoc techniczną**.

## <a name="certificate-problems"></a>Problemy z certyfikatem

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Nie można dodać powiązanie certyfikatu SSL do aplikacji sieci web 

#### <a name="symptom"></a>Objaw

Po dodaniu powiązanie SSL, pojawi się następujący komunikat o błędzie:

"Nie można dodać wiązania SSL. Nie można ustawić certyfikat dla istniejącego adresu VIP, ponieważ inny adres VIP używa już tego certyfikatu."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele powiązań SSL opartych na protokole IP dla tego samego adresu IP przez wiele aplikacji sieci web. Na przykład aplikacja sieci web A ma opartego na protokole IP protokołu SSL przy użyciu starego certyfikatu. Aplikacja sieci Web B ma opartego na protokole IP protokołu SSL przy użyciu nowego certyfikatu dla tego samego adresu IP. Po zaktualizowaniu powiązania SSL aplikacji sieci web przy użyciu nowego certyfikatu go kończy się niepowodzeniem z powodu następującego błędu ten sam adres IP jest używany dla innej aplikacji. 

#### <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, należy użyć jednej z następujących metod:

- Usuwanie powiązania SSL oparte na adresie IP w aplikacji sieci web, która używa starego certyfikatu. 
- Utwórz nowe powiązanie SSL oparte na protokole IP, który używa nowego certyfikatu.

### <a name="you-cant-delete-a-certificate"></a>Nie można usunąć certyfikatu 

#### <a name="symptom"></a>Objaw

Podczas usuwania certyfikatu, pojawi się następujący komunikat o błędzie:

"Nie można usunąć certyfikatu, ponieważ jest on aktualnie używany w powiązaniu SSL. Powiązanie SSL należy usunąć przed usunięciem certyfikatu."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli innej aplikacji sieci web używa certyfikatu.

#### <a name="solution"></a>Rozwiązanie

Usuń powiązanie SSL dla tego certyfikatu z aplikacji sieci web. Spróbuj usunąć certyfikat. Jeśli nadal nie można usunąć certyfikatu, wyczyść pamięć podręczną przeglądarki internetowej i ponownie otworzyć portalu Azure w nowym oknie przeglądarki. Spróbuj usunąć certyfikat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nie można kupić certyfikatu usługi App Service 

#### <a name="symptom"></a>Objaw
Nie można kupić [certyfikatu usługi Azure App Service](./web-sites-purchase-ssl-web-site.md) w witrynie Azure portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie
Ten problem może wystąpić z następujących powodów:

- Plan usługi App Service jest bezpłatna i współdzielona. Te warstwy cenowe nie obsługują protokołu SSL. 

    **Rozwiązanie**: Uaktualnij plan usługi App Service dla aplikacji internetowej do warstwy standardowa.

- Subskrypcja nie ma ważnej karty kredytowej.

    **Rozwiązanie**: Dodaj potwierdzenia ważności karty kredytowej do subskrypcji. 

- Oferta subskrypcji nie obsługuje zakup certyfikatu usługi App Service, takich jak Microsoft Student.  

    **Rozwiązanie**: Uaktualnij swoją subskrypcję. 

- Subskrypcja osiągnęła limit zakupów, które są dozwolone w ramach subskrypcji.

    **Rozwiązanie**: certyfikaty usługi App Service istnieje limit 10 zakupów certyfikatu dla typów subskrypcji rozliczana według bieżącego użycia i umowy EA. Dla innych typów subskrypcji limit wynosi 3. Aby zwiększyć limit, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certyfikat usługi App Service zostało oznaczone jako oszustwa. Odebrano następujący komunikat o błędzie: "certyfikat został oflagowany jako ewentualnym oszustwem. Żądanie jest obecnie w trakcie przeglądu. Jeśli certyfikat nie zostanie można używać w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure."

    **Rozwiązanie**: Jeśli certyfikat jest oznaczone jako oszustwa i nie zostanie rozwiązany po 24 godzinach, wykonaj następujące kroki:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Przejdź do **certyfikaty usługi App Service**, a następnie wybierz certyfikat.
    3. Wybierz **ukończenie konfiguracji certyfikatu** > **krok 2: Sprawdź** > **weryfikacji domeny**. W tym kroku wysyła powiadomienie e-mail do dostawcy programu certyfikatu platformy Azure, aby rozwiązać ten problem.

## <a name="domain-problems"></a>Problemy z domeny

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Możesz kupić certyfikat SSL do niewłaściwej domeny

#### <a name="symptom"></a>Objaw

Zakupiono certyfikatu usługi App Service do niewłaściwej domeny. Nie można zaktualizować certyfikat do użycia w prawidłowej domenie.

#### <a name="solution"></a>Rozwiązanie

Usuń ten certyfikat, a następnie Kup nowy certyfikat.

Jeśli bieżący certyfikat, który korzysta z niewłaściwej domeny jest w stanie "Wydany dla", możesz również będzie naliczana dla tego certyfikatu. Certyfikaty usługi App Service nie podlega zwrotowi, ale możesz skontaktować się ze [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby zobaczyć, czy istnieją inne opcje. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Odnowiono certyfikat usługi App Service, ale stary certyfikat wyświetlany przez aplikację sieci web 

#### <a name="symptom"></a>Objaw

Odnowiono certyfikat usługi App Service, ale aplikacji sieci web, która używa certyfikatu usługi App Service jest nadal przy użyciu starego certyfikatu. Ponadto otrzymasz ostrzeżenie, że protokół HTTPS jest wymagany.

#### <a name="cause"></a>Przyczyna 
Funkcja Web Apps w usłudze Azure App Service uruchamia zadania w tle co osiem godzin i synchronizuje zasobu certyfikatu, jeśli nie wprowadzono żadnych zmian. Gdy obrócić lub zaktualizuj certyfikat, czasami aplikacja nadal pobiera starego certyfikatu i certyfikat właśnie został zaktualizowany. Przyczyną jest to nie zostało jeszcze uruchomione zadanie, które można zsynchronizować zasobu certyfikatu. 
 
#### <a name="solution"></a>Rozwiązanie

Można wymusić synchronizację certyfikatu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **certyfikaty usługi App Service**, a następnie wybierz certyfikat.
2. Wybierz **Wymiana klucza i synchronizacja**, a następnie wybierz pozycję **synchronizacji**. Synchronizacja zajmuje trochę czasu, aby zakończyć. 
3. Po ukończeniu synchronizacji, zobacz następujące powiadomienie: "Pomyślnie zaktualizowano wszystkie zasoby za pomocą najnowszego certyfikatu."

### <a name="domain-verification-is-not-working"></a>Weryfikacja domeny nie działa. 

#### <a name="symptom"></a>Objaw 
Certyfikat usługi App Service wymaga weryfikacji domeny, zanim certyfikat jest gotowy do użycia. Po wybraniu **Sprawdź**, proces kończy się niepowodzeniem.

#### <a name="solution"></a>Rozwiązanie
Ręcznie Sprawdź domenę, przez dodanie rekordu TXT:
 
1.  Przejdź do dostawcy usługi nazw domen (DNS, Domain Name System), który jest hostem nazwy domeny.
2.  Dodaj rekord TXT dla swojej domeny, która używa wartości tokenu domeny, która jest wyświetlana w witrynie Azure portal. 

Poczekaj kilka minut na propagację DNS uruchomić, a następnie wybierz **Odśwież** przycisk, aby wyzwolić weryfikację. 

Jako alternatywę Metoda sieci Web HTML służy również do ręcznie zweryfikować domenę. Ta metoda umożliwia urzędowi certyfikacji potwierdzenie własności domeny wystawiono certyfikat dla domeny.

1.  Utwórz plik HTML, który nosi nazwę {tokenu weryfikacji domeny} .html. Zawartość tego pliku powinna być wartość tokenu weryfikacji domeny.
3.  Przekaż ten plik w folderze głównym serwera sieci web, który jest hostem domeny.
4.  Wybierz **Odśwież** sprawdzić stan certyfikatu. Może upłynąć kilka minut w celu weryfikacji zakończyć.

Na przykład, jeśli kupujesz Standardowy certyfikat w witrynie azure.com za pomocą 1234abcd tokenu weryfikacji domeny, żądania sieci web wprowadzone http://azure.com/1234abcd.html powinno zwrócić wartość 1234abcd. 

> [!IMPORTANT]
> Zamówienia certyfikatu jest tylko 15 dni na wykonanie operacji weryfikacji domeny. Po 15 dniach urząd certyfikacji nie zezwala na certyfikat, a nie są naliczane dla certyfikatu. W takiej sytuacji należy usunąć ten certyfikat i spróbuj ponownie.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nie można kupić domenę

#### <a name="symptom"></a>Objaw
Nie można kupować domeny z domeny aplikacji sieci Web lub usługi App Service w witrynie Azure portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

Ten problem występuje jeden z następujących powodów:

- Karta kredytowa nie jest istnieje w subskrypcji platformy Azure lub karty kredytowej jest nieprawidłowy.

    **Rozwiązanie**: Dodaj potwierdzenia ważności karty kredytowej do subskrypcji.

- Nie jesteś właścicielem subskrypcji, dlatego nie masz uprawnień, aby kupić domenę.

    **Rozwiązanie**: [przypisanie roli właściciela](../role-based-access-control/role-assignments-portal.md) do Twojego konta. Lub skontaktuj się z administratorem subskrypcji, aby uzyskać uprawnienia w celu zakupienia domeny.
- Osiągnięto limit zakupu domen w ramach Twojej subskrypcji. Aktualne ograniczenie wynosi 20.

    **Rozwiązanie**: Aby zażądać zwiększenia limitu, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Do typu Twojej subskrypcji platformy Azure nie obsługuje zakupu domen usługi App Service.

    **Rozwiązanie**: Uaktualnij swoją subskrypcję platformy Azure na inny typ subskrypcji, takie jak subskrypcja płatność za rzeczywiste użycie.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Nie można dodać nazwę hosta aplikacji sieci web 

#### <a name="symptom"></a>Objaw

Po dodaniu nazwy hosta, proces kończy się niepowodzeniem do sprawdzania poprawności i zweryfikować domenę.

#### <a name="cause"></a>Przyczyna 

Ten problem występuje jeden z następujących powodów:

- Nie masz uprawnień, aby dodać nazwę hosta.

    **Rozwiązanie**: Poproś administratora subskrypcji, aby zezwolić na dodawanie nazwy hosta.
- Nie można zweryfikować własność domeny.

    **Rozwiązanie**: Sprawdź, czy Twoje CNAME lub rekord jest poprawnie skonfigurowana. Aby zamapować domenę niestandardową do aplikacji sieci web, należy utworzyć rekord CNAME lub rekord. Jeśli chcesz użyć domeny katalogu głównego, należy użyć rekordy A i TXT:

    |Typ rekordu|Host|Wskaż|
    |------|------|-----|
    |A|@|Adres IP dla aplikacji sieci web|
    |TXT|@|< nazwa aplikacji >. azurewebsites.net|
    |CNAME|www|< nazwa aplikacji >. azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>DNS nie można go rozpoznać

#### <a name="symptom"></a>Objaw

Otrzymasz następujący komunikat o błędzie:

"Rekord DNS nie można zlokalizować."

#### <a name="cause"></a>Przyczyna
Ten problem występuje jeden z następujących powodów:

- Nie upłynął czas na żywo okres (czasu wygaśnięcia TTL). Sprawdź konfigurację usług DNS dla swojej domeny ustalić wartość TTL, a następnie poczekaj na okres wygaśnięcia.
- Konfiguracja DNS jest nieprawidłowa.

#### <a name="solution"></a>Rozwiązanie
- Poczekaj, aż ten problem rozwiąże się samoistnie w ciągu 48 godzin.
- Jeśli ustawienie czasu wygaśnięcia można zmienić w konfiguracji DNS, zmień wartość na 5 minut, aby zobaczyć, czy ten problem zostanie rozwiązany.
- Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) Aby zweryfikować, że domeny wskazuje adres IP aplikacji sieci web. Jeśli nie, należy skonfigurować rekord A poprawny adres IP w aplikacji sieci web.

### <a name="you-need-to-restore-a-deleted-domain"></a>Należy przywrócić usunięto domenę 

#### <a name="symptom"></a>Objaw
Domeny nie jest już widoczna w witrynie Azure portal.

#### <a name="cause"></a>Przyczyna 
Właściciel subskrypcji może przypadkowo usunięta domeny.

#### <a name="solution"></a>Rozwiązanie
Jeśli domena została usunięta mniej niż 7 dni temu, domena nie zostało jeszcze uruchomione proces usuwania. W takim przypadku można kupić tej samej domenie, ponownie w witrynie Azure portal, w ramach tej samej subskrypcji. (Należy wpisać nazwę domeny dokładnie w polu wyszukiwania.) Opłata zostanie naliczona ponownie dla tej domeny. Jeśli domena została usunięta ponad siedem dni temu, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby uzyskać pomoc przywrócić domeny.

### <a name="a-custom-domain-returns-a-404-error"></a>Domenę niestandardową zwróci błąd 404 

#### <a name="symptom"></a>Objaw

Przejdź do witryny przy użyciu nazwy domeny niestandardowej, pojawi się następujący komunikat o błędzie:

"Nie znaleziono aplikacji sieci Web 404 błędu."


#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

**Przyczyny 1** 

Brak rekordu CNAME i A domeny niestandardowej, który został skonfigurowany. 

**Rozwiązanie przyczyny 1**

- Jeśli został dodany rekord A, upewnij się, również zostanie dodany rekord TXT. Aby uzyskać więcej informacji, zobacz [Utwórz rekord a](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Jeśli nie masz do korzystania z domeny katalogu głównego dla aplikacji sieci web, zaleca się używania rekordu CNAME zamiast rekordu.
- Nie używaj zarówno rekord CNAME, jak i rekord A dla tej samej domenie. Może to powodować konflikt i uniemożliwić rozwiązywany w domenie. 

**Przyczyny 2** 

Stary adres IP dla swojej domeny nadal może być buforowanie przeglądarki internetowej. 

**Rozwiązanie przyczyny 2**

Wyczyść przeglądarki. W przypadku urządzeń Windows, można uruchomić polecenie `ipconfig /flushdns`. Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) Aby zweryfikować, że domeny wskazuje adres IP aplikacji sieci web. 

### <a name="you-cant-add-a-subdomain"></a>Nie można dodać poddomeny 

#### <a name="symptom"></a>Objaw

Nie można dodać nową nazwę hosta aplikacji sieci web, aby przypisać poddomeny.

#### <a name="solution"></a>Rozwiązanie

- Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia do dodawania nazwy hosta do aplikacji sieci web.
- Jeśli potrzebujesz więcej poddomeny, zaleca się zmiany hosting domeny do usługi Azure DNS. Za pomocą usługi Azure DNS, można dodawać do aplikacji sieci web 500 nazwy hosta. Aby uzyskać więcej informacji, zobacz [Dodawanie poddomeny](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 



















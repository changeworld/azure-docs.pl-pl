---
title: Rozwiązywanie problemów z domeny i certyfikaty SSL — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w usłudze Azure App Service
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
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: c0584a69349c2785b5b6bce1d17c023c95b36151
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136177"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Rozwiązywanie problemów z domeny i problemy z certyfikatem SSL w usłudze Azure App Service

W tym artykule wymieniono typowe problemy, które można napotkać podczas konfigurowania domeny lub certyfikatu protokołu SSL dla aplikacji sieci web w usłudze Azure App Service. Opisano również możliwe przyczyny i potencjalne rozwiązania tych problemów.

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [na forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz **Uzyskaj pomoc techniczną**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemy z certyfikatem

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Nie można dodać powiązanie certyfikatu SSL do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu powiązanie SSL, pojawi się następujący komunikat o błędzie:

"Nie można dodać wiązania SSL. Nie można ustawić certyfikat dla istniejącego adresu VIP, ponieważ inny adres VIP używa już tego certyfikatu."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele powiązań SSL opartych na protokole IP dla tego samego adresu IP przez wiele aplikacji. Na przykład aplikacja A ma opartego na protokole IP protokołu SSL przy użyciu starego certyfikatu. Aplikacja B ma opartego na protokole IP protokołu SSL przy użyciu nowego certyfikatu dla tego samego adresu IP. Po zaktualizowaniu powiązania SSL aplikacji przy użyciu nowego certyfikatu go kończy się niepowodzeniem z powodu następującego błędu ten sam adres IP jest używany dla innej aplikacji. 

#### <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, należy użyć jednej z następujących metod:

- Usuwanie powiązania SSL opartego na protokole IP, na aplikację, która używa starego certyfikatu. 
- Utwórz nowe powiązanie SSL oparte na protokole IP, który używa nowego certyfikatu.

### <a name="you-cant-delete-a-certificate"></a>Nie można usunąć certyfikatu 

#### <a name="symptom"></a>Objaw

Podczas usuwania certyfikatu, pojawi się następujący komunikat o błędzie:

"Nie można usunąć certyfikatu, ponieważ jest on aktualnie używany w powiązaniu SSL. Powiązanie SSL należy usunąć przed usunięciem certyfikatu."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli certyfikat korzysta z innej aplikacji.

#### <a name="solution"></a>Rozwiązanie

Usuń powiązanie SSL dla tego certyfikatu z aplikacji. Spróbuj usunąć certyfikat. Jeśli nadal nie można usunąć certyfikatu, wyczyść pamięć podręczną przeglądarki internetowej i ponownie otworzyć portalu Azure w nowym oknie przeglądarki. Spróbuj usunąć certyfikat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nie można kupić certyfikatu usługi App Service 

#### <a name="symptom"></a>Objaw
Nie można kupić [certyfikatu usługi Azure App Service](./web-sites-purchase-ssl-web-site.md) w witrynie Azure portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie
Ten problem może wystąpić z następujących powodów:

- Plan usługi App Service jest bezpłatna i współdzielona. Te warstwy cenowe nie obsługują protokołu SSL. 

    **Rozwiązanie**: Uaktualnij plan usługi App Service dla aplikacji do warstwy standardowa.

- Subskrypcja nie ma ważnej karty kredytowej.

    **Rozwiązanie**: Dodaj potwierdzenia ważności karty kredytowej do subskrypcji. 

- Oferta subskrypcji nie obsługuje zakup certyfikatu usługi App Service, takich jak Microsoft Student.  

    **Rozwiązanie**: Uaktualnij swoją subskrypcję. 

- Subskrypcja osiągnęła limit zakupów, które są dozwolone w ramach subskrypcji.

    **Rozwiązanie**: Certyfikaty usługi App Service mają limit 10 zakupów certyfikatu dla typów subskrypcji rozliczana według bieżącego użycia i umowy EA. Dla innych typów subskrypcji limit wynosi 3. Aby zwiększyć limit, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certyfikat usługi App Service zostało oznaczone jako oszustwa. Otrzymasz następujący komunikat o błędzie: "Twój certyfikat oznaczono jako ewentualnym oszustwem. Żądanie jest obecnie w trakcie przeglądu. Jeśli certyfikat nie zostanie można używać w ciągu 24 godzin, skontaktuj się z działem pomocy technicznej systemu Azure."

    **Rozwiązanie**: Jeśli certyfikat jest oznaczone jako oszustwa i nie zostanie rozwiązany po 24 godzinach, wykonaj następujące kroki:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Przejdź do **certyfikaty usługi App Service**, a następnie wybierz certyfikat.
    3. Wybierz **ukończenie konfiguracji certyfikatu** > **krok 2: Sprawdź** > **weryfikacji domeny**. W tym kroku wysyła powiadomienie e-mail do dostawcy programu certyfikatu platformy Azure, aby rozwiązać ten problem.

## <a name="custom-domain-problems"></a>Problemy z domeny niestandardowej

### <a name="a-custom-domain-returns-a-404-error"></a>Domenę niestandardową zwróci błąd 404 

#### <a name="symptom"></a>Objaw

Przejdź do witryny przy użyciu nazwy domeny niestandardowej, pojawi się następujący komunikat o błędzie:

"Nie znaleziono aplikacji sieci Web 404 błędu."

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

**Przyczyny 1** 

Brak rekordu CNAME i A domeny niestandardowej, który został skonfigurowany. 

**Rozwiązanie przyczyny 1**

- Jeśli został dodany rekord A, upewnij się, również zostanie dodany rekord TXT. Aby uzyskać więcej informacji, zobacz [Utwórz rekord a](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Jeśli nie masz do korzystania z domeny katalogu głównego aplikacji, zaleca się używania rekordu CNAME zamiast rekordu.
- Nie używaj zarówno rekord CNAME, jak i rekord A dla tej samej domenie. Ten problem może spowodować konflikt i uniemożliwić rozwiązywany w domenie. 

**Przyczyny 2** 

Stary adres IP dla swojej domeny nadal może być buforowanie przeglądarki internetowej. 

**Rozwiązanie przyczyny 2**

Wyczyść przeglądarki. W przypadku urządzeń Windows, można uruchomić polecenie `ipconfig /flushdns`. Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) Aby zweryfikować, że domeny wskazuje adresu IP aplikacji. 

### <a name="you-cant-add-a-subdomain"></a>Nie można dodać poddomeny 

#### <a name="symptom"></a>Objaw

Nie można dodać nową nazwę hosta, na aplikację, aby przypisać poddomeny.

#### <a name="solution"></a>Rozwiązanie

- Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia do dodawania nazwy hosta do aplikacji.
- Jeśli potrzebujesz więcej poddomeny, zaleca się zmiany do usługi nazw domen (DNS) platformy Azure hostowanie domen. Za pomocą usługi Azure DNS, można dodać 500 nazw hostów do aplikacji. Aby uzyskać więcej informacji, zobacz [Dodawanie poddomeny](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

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
- Użyj [WhatsmyDNS.net](https://www.whatsmydns.net/) Aby zweryfikować, że domeny wskazuje adresu IP aplikacji. Jeśli nie, należy skonfigurować rekord A poprawny adres IP aplikacji.

### <a name="you-need-to-restore-a-deleted-domain"></a>Należy przywrócić usunięto domenę 

#### <a name="symptom"></a>Objaw
Domeny nie jest już widoczna w witrynie Azure portal.

#### <a name="cause"></a>Przyczyna 
Właściciel subskrypcji może przypadkowo usunięta domeny.

#### <a name="solution"></a>Rozwiązanie
Jeśli domena została usunięta mniej niż 7 dni temu, domena nie zostało jeszcze uruchomione proces usuwania. W takim przypadku można kupić tej samej domenie, ponownie w witrynie Azure portal, w ramach tej samej subskrypcji. (Należy wpisać nazwę domeny dokładnie w polu wyszukiwania.) Opłata zostanie naliczona ponownie dla tej domeny. Jeśli domena została usunięta ponad siedem dni temu, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby uzyskać pomoc dotyczącą przywracania domeny.

## <a name="domain-problems"></a>Problemy z domeny

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Możesz kupić certyfikat SSL do niewłaściwej domeny

#### <a name="symptom"></a>Objaw

Zakupiono certyfikatu usługi App Service do niewłaściwej domeny. Nie można zaktualizować certyfikat do użycia w prawidłowej domenie.

#### <a name="solution"></a>Rozwiązanie

Usuń ten certyfikat, a następnie Kup nowy certyfikat.

Jeśli bieżący certyfikat, który korzysta z niewłaściwej domeny jest w stanie "Wydany dla", możesz również będzie naliczana dla tego certyfikatu. Certyfikaty usługi App Service nie podlega zwrotowi, ale możesz skontaktować się ze [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby zobaczyć, czy istnieją inne opcje. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Odnowiono certyfikat usługi App Service, ale aplikacja będzie wyświetlana starego certyfikatu 

#### <a name="symptom"></a>Objaw

Odnowiono certyfikat usługi App Service, ale aplikację, która używa certyfikatu usługi App Service jest nadal przy użyciu starego certyfikatu. Ponadto otrzymasz ostrzeżenie, że protokół HTTPS jest wymagany.

#### <a name="cause"></a>Przyczyna 
Usługa Azure App Service uruchamia zadania w tle co osiem godzin i synchronizuje zasobu certyfikatu, jeśli nie wprowadzono żadnych zmian. Gdy obrócić lub zaktualizuj certyfikat, czasami aplikacja nadal pobiera starego certyfikatu i certyfikat właśnie został zaktualizowany. Przyczyną jest to nie zostało jeszcze uruchomione zadanie, które można zsynchronizować zasobu certyfikatu. 
 
#### <a name="solution"></a>Rozwiązanie

Można wymusić synchronizację certyfikatu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **certyfikaty usługi App Service**, a następnie wybierz certyfikat.
2. Wybierz **Wymiana klucza i synchronizacja**, a następnie wybierz pozycję **synchronizacji**. Synchronizacja zajmuje trochę czasu, aby zakończyć. 
3. Po ukończeniu synchronizacji, zostanie wyświetlone następujące powiadomienie: "Pomyślnie zaktualizowano wszystkie zasoby za pomocą najnowszego certyfikatu."

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

Na przykład, jeśli kupujesz Standardowy certyfikat w witrynie azure.com za pomocą 1234abcd tokenu weryfikacji domeny, żądania sieci web wprowadzone https://azure.com/1234abcd.html powinno zwrócić wartość 1234abcd. 

> [!IMPORTANT]
> Zamówienia certyfikatu jest tylko 15 dni na wykonanie operacji weryfikacji domeny. Po 15 dniach urząd certyfikacji nie zezwala na certyfikat, a nie są naliczane dla certyfikatu. W takiej sytuacji należy usunąć ten certyfikat i spróbuj ponownie.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nie można kupić domenę

#### <a name="symptom"></a>Objaw
Nie można kupić domenę usługi App Service w witrynie Azure portal.

#### <a name="cause-and-solution"></a>Przyczyna i rozwiązanie

Ten problem występuje jeden z następujących powodów:

- Karta kredytowa nie jest istnieje w subskrypcji platformy Azure lub karty kredytowej jest nieprawidłowy.

    **Rozwiązanie**: Dodaj potwierdzenia ważności karty kredytowej do subskrypcji.

- Nie jesteś właścicielem subskrypcji, dlatego nie masz uprawnień, aby kupić domenę.

    **Rozwiązanie**: [Przypisanie roli właściciela](../role-based-access-control/role-assignments-portal.md) do Twojego konta. Lub skontaktuj się z administratorem subskrypcji, aby uzyskać uprawnienia w celu zakupienia domeny.
- Osiągnięto limit zakupu domen w ramach Twojej subskrypcji. Aktualne ograniczenie wynosi 20.

    **Rozwiązanie**: Aby poprosić o zwiększenie limitu, skontaktuj się z pomocą [pomocy technicznej platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Do typu Twojej subskrypcji platformy Azure nie obsługuje zakupu domen usługi App Service.

    **Rozwiązanie**: Uaktualnij swoją subskrypcję platformy Azure do innego typu subskrypcji, takie jak subskrypcja płatność za rzeczywiste użycie.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Nazwa hosta nie można dodać do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu nazwy hosta, proces kończy się niepowodzeniem do sprawdzania poprawności i zweryfikować domenę.

#### <a name="cause"></a>Przyczyna 

Ten problem występuje jeden z następujących powodów:

- Nie masz uprawnień, aby dodać nazwę hosta.

    **Rozwiązanie**: Poproś administratora subskrypcji udzieliła Ci zgody, aby dodać nazwę hosta.
- Nie można zweryfikować własność domeny.

    **Rozwiązanie**: Sprawdź, czy Twoje CNAME lub rekord jest poprawnie skonfigurowana. Aby mapować domenę niestandardową na aplikację, należy utworzyć rekord CNAME lub rekord. Jeśli chcesz użyć domeny katalogu głównego, należy użyć rekordy A i TXT:

    |Typ rekordu|Host|Wskaż|
    |------|------|-----|
    |A|@|Adres IP dla aplikacji|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Często zadawane pytania

**Czy muszę Moje niestandardowe skonfiguruj domenę pod kątem Moja witryna sieci Web po mogę kupić?**

Przy zakupie domeny w witrynie Azure portal, aplikacji usługi App Service automatycznie jest skonfigurowany do korzystania z tej domeny niestandardowej. Nie trzeba wykonać dodatkowe czynności. Aby uzyskać więcej informacji, obejrzyj [usługi Azure App Service samoobsługowy może mi pomóc: Dodawanie niestandardowej nazwy domeny](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) w witrynie Channel9.

**Aby wskazywały na Maszynie wirtualnej platformy Azure zamiast tego można używać domenę, kupić w witrynie Azure portal?**

Tak, może wskazywać domeny maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

**Czy moja domena jest obsługiwana przez GoDaddy lub system DNS Azure?**

Domeny usługi App Service umożliwia GoDaddy dla domeny rejestracji i system DNS Azure hostowanie domen. 

**Mam automatyczne odnawianie włączone, ale nadal otrzymał powiadomienie odnawiania dla mojej domeny za pośrednictwem poczty e-mail. Co zrobić?**

Jeśli masz automatyczne odnawianie włączone, nie trzeba podejmować żadnych działań. Powiadomienie e-mail jest dostarczany, aby poinformować, że domena jest bliski wygaśnięcia i odnowić ręcznie, jeśli automatycznego odnawiania nie jest włączona.

**Zostanie naliczona dla usługi Azure DNS hostowania mojej domeny?**

Początkowy koszt zakupu domen dotyczy tylko rejestracja domeny. Oprócz kosztów rejestracji istnieją ponoszenia opłat za usługę Azure DNS, na podstawie użycia. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/) Aby uzyskać więcej informacji.

**Czy mogę nabyć w mojej domeny wcześniej w witrynie Azure portal i chcesz przenieść od firmy GoDaddy hostowania hostingu usługi Azure DNS. Jak można to zrobić?**

Nie jest to konieczne, aby przeprowadzić migrację do usługi Azure DNS obsługujący. Jeśli chcesz przeprowadzić migrację do usługi Azure DNS, możliwości zarządzania domeny w witrynie Azure portal, temat zawiera informacje dotyczące kroków, które są niezbędne do przejścia do usługi Azure DNS. Jeśli domena zostało zakupione w ramach usługi App Service, migracja z GoDaddy hostingu do usługi Azure DNS jest stosunkowo bezproblemowe procedury.

**Chcę kupić mojej domeny z domeny usługi App Service, ale może obsługiwać mojej domeny na GoDaddy, a nie usługi Azure DNS?**

Począwszy od 24 lipca 2017 r. domeny usługi App Service zakupionych w portalu znajdują się w usłudze Azure DNS. Jeśli chcesz użyć innego dostawcy hostingu, należy przejść do swojej witryny sieci Web można uzyskać domeny hosting rozwiązań.

**Czy muszę płacić za ochronę prywatności dla mojej domeny?**

Przy zakupie domeny za pośrednictwem witryny Azure portal, można dodać zachowania bez ponoszenia dodatkowych kosztów. Jest to jedna z korzyści związanych z zakupem swojej domeny dzięki użyciu usługi Azure App Service.

**Jeśli zdecyduję się, że mojej domeny nie są już potrzebne, można uzyskać moich pieniędzy ponownie?**

Przy zakupie domeny, nie są naliczane przez okres pięciu dni, w tym czasie możesz zrezygnować domeny. Jeśli zdecydujesz, że nie chcesz, aby w tym okresie pięciu dni domeny, nie są naliczane. (.pl domeny są wyjątkiem od tej. Jeśli kupisz domeny .pl, opłaty są naliczane od razu i nie zostanie zwrócone.)

**Czy można używać domeny w innej aplikacji w usłudze Azure App Service, w mojej subskrypcji?**

Tak. Gdy uzyskujesz dostęp do bloku domeny niestandardowe i protokół SSL w witrynie Azure portal, zostanie wyświetlony domen, które zostały nabyte. Można skonfigurować aplikację tak, aby użyć dowolnego z tych domen.

**Czy mogę przenieść domenę z jednej subskrypcji do innej subskrypcji?**

Domeny można przenieść do innej grupy zasobów/subskrypcji przy użyciu [AzResource przenoszenia](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) polecenia cmdlet programu PowerShell.

**Jak można zarządzać Mój domeny niestandardowej, jeśli obecnie nie mam aplikację usługi Azure App Service?**

Domenę można zarządzać, nawet jeśli nie masz aplikacji internetowej usługi App Service. Domeny może służyć do usług platformy Azure, takich jak maszyny wirtualne, Magazyn itp. Jeśli zamierzasz używać domeny dla aplikacji sieci Web usługi App Service, musisz Uwzględnij aplikację sieci Web, który nie znajduje się na bezpłatny plan usługi App Service do powiązania domeny na aplikację internetową.

**Czy mogę przenieść aplikację sieci web przy użyciu domeny niestandardowej do innej subskrypcji lub ze środowiska App Service Environment v1 na V2?**

Tak, można przenieść aplikację sieci web w subskrypcjach. Postępuj zgodnie ze wskazówkami w [sposób przenoszenia zasobów na platformie Azure](../azure-resource-manager/resource-group-move-resources.md). Istnieją pewne ograniczenia podczas przenoszenia aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [ograniczenia dotyczące przenoszenia zasobów usługi App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations
).

Po przeniesieniu aplikacji sieci web, hosta powiązania nazwy domen w obrębie domen niestandardowych, ustawienia należy pozostają takie same. Żadne dodatkowe kroki są wymagane do skonfigurowania powiązania nazwy hosta.

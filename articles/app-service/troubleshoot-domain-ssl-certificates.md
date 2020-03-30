---
title: Rozwiązywanie problemów z certyfikatami domeny i SSL
description: Znajdź rozwiązania typowych problemów, które mogą wystąpić podczas konfigurowania domeny lub certyfikatu SSL w usłudze Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: e299821b54692327cbb7d497af0295e3b93658cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966973"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Rozwiązywanie problemów z domeną i certyfikatami SSL w usłudze Azure App Service

W tym artykule wymieniono typowe problemy, które mogą wystąpić podczas konfigurowania domeny lub certyfikatu SSL dla aplikacji sieci web w usłudze Azure App Service. Opisano również możliwe przyczyny i rozwiązania tych problemów.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemy z certyfikatem

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Nie można dodać powiązania certyfikatu SSL do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu powiązania SSL zostanie wyświetlony następujący komunikat o błędzie:

"Nie można dodać powiązania SSL. Nie można ustawić certyfikatu dla istniejącego adresu VIP, ponieważ inny adres VIP już używa tego certyfikatu."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele powiązań SSL opartych na protokosze dla tego samego adresu IP w wielu aplikacjach. Na przykład aplikacja A ma protokół SSL oparty na protokoi z certyfikatem IP ze starym certyfikatem. Aplikacja B ma protokół SSL oparty na protokoi z nowym certyfikatem dla tego samego adresu IP. Po zaktualizowaniu powiązania SSL aplikacji z nowym certyfikatem, nie powiedzie się z tym błędem, ponieważ ten sam adres IP jest używany dla innej aplikacji. 

#### <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, należy użyć jednej z następujących metod:

- Usuń powiązanie SSL oparte na protoke ip dla aplikacji, która używa starego certyfikatu. 
- Utwórz nowe powiązanie SSL oparte na protokocze IP, które używa nowego certyfikatu.

### <a name="you-cant-delete-a-certificate"></a>Nie można usunąć certyfikatu 

#### <a name="symptom"></a>Objaw

Podczas próby usunięcia certyfikatu pojawia się następujący komunikat o błędzie:

"Nie można usunąć certyfikatu, ponieważ jest on obecnie używany w powiązaniu SSL. Powiązanie SSL musi zostać usunięte, zanim będzie można usunąć certyfikat."

#### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli inna aplikacja używa certyfikatu.

#### <a name="solution"></a>Rozwiązanie

Usuń powiązanie SSL dla tego certyfikatu z aplikacji. Następnie spróbuj usunąć certyfikat. Jeśli nadal nie można usunąć certyfikatu, wyczyść pamięć podręczną przeglądarki internetowej i otwórz ponownie witrynę Azure portal w nowym oknie przeglądarki. Następnie spróbuj usunąć certyfikat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nie można kupić certyfikatu usługi app service 

#### <a name="symptom"></a>Objaw
Nie można kupić [certyfikatu usługi Azure App Service](./configure-ssl-certificate.md#import-an-app-service-certificate) z witryny Azure portal.

#### <a name="cause-and-solution"></a>Przyczyna i roztwór
Ten problem może wystąpić z następujących powodów:

- Plan usługi aplikacji jest bezpłatny lub udostępniony. Te warstwy cenowe nie obsługują ssl. 

    **Rozwiązanie:** Uaktualnij plan usługi app dla aplikacji do standardu.

- Subskrypcja nie ma ważnej karty kredytowej.

    **Rozwiązanie:** Dodaj prawidłową kartę kredytową do subskrypcji. 

- Oferta subskrypcji nie obsługuje zakupu certyfikatu usługi App Service, takiego jak Microsoft Student.  

    **Rozwiązanie:** Uaktualnij subskrypcję. 

- Subskrypcja osiągnęła limit zakupów, które są dozwolone w ramach subskrypcji.

    **Rozwiązanie:** Certyfikaty usługi App Service mają limit 10 zakupów certyfikatów dla typów subskrypcji płatności zgodnie z rzeczywistym i ea. W przypadku innych typów subskrypcji limit wynosi 3. Aby zwiększyć limit, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certyfikat usługi app service został oznaczony jako oszustwo. Pojawił się następujący komunikat o błędzie: "Twój certyfikat został oflagowany w celu zgłoszenia oszustwa. Wniosek jest obecnie w trakcie rozpatrywana. Jeśli certyfikat nie stanie się użyteczny w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure."

    **Rozwiązanie:** Jeśli certyfikat jest oznaczony jako oszustwo i nie został rozwiązany po 24 godzinach, wykonaj następujące kroki:

    1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
    2. Przejdź do strony **Certyfikaty usługi app service**i wybierz certyfikat.
    3. Wybierz krok konfiguracji **certyfikatu** > **2: Weryfikuj** > **weryfikację domeny**. Ten krok wysyła powiadomienie e-mail do dostawcy certyfikatów platformy Azure, aby rozwiązać problem.

## <a name="custom-domain-problems"></a>Problemy z domeną niestandardową

### <a name="a-custom-domain-returns-a-404-error"></a>Domena niestandardowa zwraca błąd 404 

#### <a name="symptom"></a>Objaw

Podczas przeglądania witryny przy użyciu niestandardowej nazwy domeny jest wyświetlany następujący komunikat o błędzie:

"Nie znaleziono aplikacji o błędzie 404-Web."

#### <a name="cause-and-solution"></a>Przyczyna i roztwór

**Przyczyna 1** 

W skonfigurowanej domenie niestandardowej brakuje rekordu CNAME lub A. 

**Rozwiązanie dla przyczyny 1**

- Jeśli dodano rekord A, upewnij się, że dodano również rekord TXT. Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Jeśli nie musisz używać domeny głównej dla aplikacji, zalecamy użycie rekordu CNAME zamiast rekordu A.
- Nie używaj zarówno rekordu CNAME, jak i rekordu A dla tej samej domeny. Ten problem może spowodować konflikt i uniemożliwić rozwiązanie domeny. 

**Przyczyna 2** 

Przeglądarka internetowa może nadal buforować stary adres IP twojej domeny. 

**Rozwiązanie dla przyczyny 2**

Wyczyść przeglądarkę. W przypadku urządzeń z systemem `ipconfig /flushdns`Windows można uruchomić polecenie . Użyj [WhatsmyDNS.net,](https://www.whatsmydns.net/) aby sprawdzić, czy twoja domena wskazuje adres IP aplikacji. 

### <a name="you-cant-add-a-subdomain"></a>Nie można dodać poddomeny 

#### <a name="symptom"></a>Objaw

Nie można dodać nowej nazwy hosta do aplikacji, aby przypisać poddomenę.

#### <a name="solution"></a>Rozwiązanie

- Skontaktuj się z administratorem subskrypcji, aby upewnić się, że masz uprawnienia do dodawania nazwy hosta do aplikacji.
- Jeśli potrzebujesz więcej poddomen, zalecamy zmianę hostingu domeny na usługę Dns (Azure Domain Name Service). Za pomocą usługi Azure DNS można dodać 500 nazw hostów do aplikacji. Aby uzyskać więcej informacji, zobacz [Dodawanie poddomeny](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>Nie można rozpoznać systemu DNS

#### <a name="symptom"></a>Objaw

Pojawi się następujący komunikat o błędzie:

"Nie można zlokalizować rekordu DNS."

#### <a name="cause"></a>Przyczyna
Ten problem występuje z jednego z następujących powodów:

- Okres wygaśnięcia czasu życia (TTL) nie upłynął. Sprawdź konfigurację DNS domeny, aby określić wartość TTL, a następnie poczekaj, aż okres wygaśnie.
- Konfiguracja DNS jest niepoprawna.

#### <a name="solution"></a>Rozwiązanie
- Poczekaj 48 godzin, aż ten problem sam się rozwiąże.
- Jeśli można zmienić ustawienie czasu wygaśnięcia w konfiguracji DNS, zmień wartość na 5 minut, aby zobaczyć, czy to rozwiązuje problem.
- Użyj [WhatsmyDNS.net,](https://www.whatsmydns.net/) aby sprawdzić, czy twoja domena wskazuje adres IP aplikacji. Jeśli tak nie jest, skonfiguruj rekord A na poprawny adres IP aplikacji.

### <a name="you-need-to-restore-a-deleted-domain"></a>Musisz przywrócić usuniętą domenę 

#### <a name="symptom"></a>Objaw
Twoja domena nie jest już widoczna w witrynie Azure portal.

#### <a name="cause"></a>Przyczyna 
Właściciel subskrypcji mógł przypadkowo usunąć domenę.

#### <a name="solution"></a>Rozwiązanie
Jeśli domena została usunięta mniej niż siedem dni temu, domena nie rozpoczęła jeszcze procesu usuwania. W takim przypadku można ponownie kupić tę samą domenę w witrynie Azure portal w ramach tej samej subskrypcji. (Pamiętaj, aby wpisać dokładną nazwę domeny w polu wyszukiwania). Opłata za tę domenę nie zostanie naliczona ponownie. Jeśli domena została usunięta więcej niż siedem dni temu, skontaktuj się z [pomocą techniczną platformy Azure,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby uzyskać pomoc dotyczącą przywracania domeny.

## <a name="domain-problems"></a>Problemy z domeną

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Zakupiono certyfikat SSL dla niewłaściwej domeny

#### <a name="symptom"></a>Objaw

Zakupiono certyfikat usługi App Service dla niewłaściwej domeny. Nie można zaktualizować certyfikatu, aby użyć poprawnej domeny.

#### <a name="solution"></a>Rozwiązanie

Usuń ten certyfikat, a następnie kup nowy certyfikat.

Jeśli bieżący certyfikat, który używa niewłaściwej domeny, jest w stanie "Wystawione", zostanie również naliczona naliczona naliczona naliczona kwota tego certyfikatu. Certyfikaty usługi App Service nie podlegają zwrotowi, ale możesz skontaktować się z [pomocą techniczną platformy Azure,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby sprawdzić, czy istnieją inne opcje. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Certyfikat usługi App Service został odnowiony, ale aplikacja pokazuje stary certyfikat 

#### <a name="symptom"></a>Objaw

Certyfikat usługi App Service został odnowiony, ale aplikacja korzystająca z certyfikatu usługi App Service nadal używa starego certyfikatu. Ponadto odebrano ostrzeżenie, że wymagany jest protokół HTTPS.

#### <a name="cause"></a>Przyczyna 
Usługa App Service automatycznie synchronizuje certyfikat w ciągu 48 godzin. Podczas obracania lub aktualizowania certyfikatu czasami aplikacja nadal pobiera stary certyfikat, a nie nowo zaktualizowany certyfikat. Powodem jest to, że zadanie synchronizacji zasobu certyfikatu nie zostało jeszcze uruchomione. Kliknij pozycję Synchronizuj. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta certyfikatu w usłudze App Service bez powodowania przestojów w aplikacjach.
 
#### <a name="solution"></a>Rozwiązanie

Można wymusić synchronizację certyfikatu:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Certyfikaty usługi app service**, a następnie wybierz certyfikat.
2. Wybierz **pozycję Ponownieklucz i Synchronizuj**, a następnie wybierz pozycję **Synchronizuj**. Synchronizacja zajmuje trochę czasu, aby zakończyć. 
3. Po zakończeniu synchronizacji zostanie wyświetlone następujące powiadomienie: "Pomyślnie zaktualizowano wszystkie zasoby za pomocą najnowszego certyfikatu".

### <a name="domain-verification-is-not-working"></a>Weryfikacja domeny nie działa 

#### <a name="symptom"></a>Objaw 
Certyfikat usługi App Service wymaga weryfikacji domeny, zanim certyfikat będzie gotowy do użycia. Po wybraniu opcji **Sprawdź**proces zakończy się niepowodzeniem.

#### <a name="solution"></a>Rozwiązanie
Ręcznie zweryfikuj domenę, dodając rekord TXT:
 
1.  Przejdź do dostawcy usługi dns (Domain Name Service), który obsługuje twoją nazwę domeny.
2.  Dodaj rekord TXT dla domeny, który używa wartości tokenu domeny, który jest wyświetlany w witrynie Azure portal. 

Poczekaj kilka minut na uruchomienie propagacji DNS, a następnie wybierz przycisk **Odśwież,** aby wyzwolić weryfikację. 

Alternatywnie można użyć metody strony HTML, aby ręcznie zweryfikować domenę. Ta metoda umożliwia urząd certyfikacji potwierdzenie własności domeny, dla których wystawiono certyfikat.

1.  Utwórz plik HTML o nazwie {token weryfikacji domeny}.html. Zawartość tego pliku powinna być wartością tokenu weryfikacji domeny.
3.  Przekaż ten plik w katalogu głównym serwera sieci web, na który znajduje się Twoja domena.
4.  Wybierz **przycisk Odśwież,** aby sprawdzić stan certyfikatu. Weryfikacja może potrwać kilka minut.

Na przykład jeśli kupujesz standardowy certyfikat dla azure.com z tokenem weryfikacji domeny 1234abcd, żądanie sieci web, do https://azure.com/1234abcd.html którego został złożony, powinno zostać odesłane 1234abcd. 

> [!IMPORTANT]
> Zamówienie certyfikatu ma tylko 15 dni na ukończenie operacji weryfikacji domeny. Po 15 dniach urząd certyfikacji odmawia certyfikatu i nie są obciążane za certyfikat. W tej sytuacji usuń ten certyfikat i spróbuj ponownie.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nie można kupić domeny

#### <a name="symptom"></a>Objaw
Nie można kupić domeny usługi app service w witrynie Azure portal.

#### <a name="cause-and-solution"></a>Przyczyna i roztwór

Ten problem występuje z jednego z następujących powodów:

- W subskrypcji platformy Azure nie ma karty kredytowej lub karta kredytowa jest nieprawidłowa.

    **Rozwiązanie:** Dodaj prawidłową kartę kredytową do subskrypcji.

- Nie jesteś właścicielem subskrypcji, dlatego nie masz uprawnień do zakupu domeny.

    **Rozwiązanie:** [Przypisz rolę Właściciel](../role-based-access-control/role-assignments-portal.md) do swojego konta. Możesz też skontaktować się z administratorem subskrypcji, aby uzyskać pozwolenie na zakup domeny.
- Osiągnięto limit zakupów domen w ramach subskrypcji. Bieżący limit wynosi 20.

    **Rozwiązanie:** Aby zażądać zwiększenia limitu, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Typ subskrypcji platformy Azure nie obsługuje zakupu domeny usługi App Service.

    **Rozwiązanie:** Uaktualnij subskrypcję platformy Azure do innego typu subskrypcji, takiego jak subskrypcja płatności zgodnie z rzeczywistym rozwiązaniem.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Nie można dodać nazwy hosta do aplikacji 

#### <a name="symptom"></a>Objaw

Po dodaniu nazwy hosta proces nie może zweryfikować i zweryfikować domeny.

#### <a name="cause"></a>Przyczyna 

Ten problem występuje z jednego z następujących powodów:

- Nie masz uprawnień do dodawania nazwy hosta.

    **Rozwiązanie:** Poproś administratora subskrypcji o pozwolenie na dodanie nazwy hosta.
- Nie można zweryfikować własności domeny.

    **Rozwiązanie:** Sprawdź, czy rekord CNAME lub A jest poprawnie skonfigurowany. Aby zamapować domenę niestandardową na aplikację, utwórz rekord CNAME lub rekord A. Jeśli chcesz użyć domeny głównej, musisz użyć rekordów A i TXT:

    |Typ rekordu|Host|Wskaż do|
    |------|------|-----|
    |A|@|Adres IP aplikacji|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Najczęściej zadawane pytania

**Czy muszę skonfigurować domenę niestandardową dla mojej witryny sieci Web po jej zakupie?**

Podczas zakupu domeny z witryny Azure portal aplikacja usługi App Service jest automatycznie skonfigurowana do używania tej domeny niestandardowej. Nie musisz podejmować żadnych dodatkowych kroków. Aby uzyskać więcej informacji, obejrzyj [samootuac usługi Azure App Service: Dodaj niestandardową nazwę domeny](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) w kanale Channel9.

**Czy mogę użyć domeny zakupionej w witrynie Azure portal, aby wskazać maszynę wirtualną platformy Azure zamiast tego?**

Tak, możesz skierować domenę na maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

**Czy moja domena jest hostowana przez goDaddy lub usługę Azure DNS?**

Domeny usługi app service używają goDaddy do rejestracji domeny i usługi Azure DNS do hostowania domen. 

**Mam włączone automatyczne odnawianie, ale nadal otrzymałem powiadomienie o odnowieniu mojej domeny za pośrednictwem poczty e-mail. Co należy zrobić?**

Jeśli masz włączoną funkcję automatycznego odnawiania, nie musisz podejmować żadnych działań. Wiadomość e-mail z powiadomieniem jest dostarczana w celu poinformowania, że domena jest bliska wygaśnięcia i ręcznego odnawiania, jeśli automatyczne odnawianie nie jest włączone.

**Czy naliczona zostanie opłata za hosting mojej domeny przez usługę Azure DNS?**

Początkowy koszt zakupu domeny dotyczy tylko rejestracji domeny. Oprócz kosztów rejestracji, istnieją opłaty za usługi Azure DNS na podstawie użycia. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure DNS, aby](https://azure.microsoft.com/pricing/details/dns/) uzyskać więcej informacji.

**Kupiłem domenę wcześniej z witryny Azure portal i chcę przejść z hostingu GoDaddy do hostingu DNS platformy Azure. Jak to zrobić?**

Migracja do usługi Azure DNS hosting nie jest obowiązkowa. Jeśli chcesz przeprowadzić migrację do usługi Azure DNS, środowisko zarządzania domeną w witrynie Azure portal o zawiera informacje na temat kroków niezbędnych do przejścia do usługi Azure DNS. Jeśli domena została zakupiona za pośrednictwem usługi App Service, migracja z hostingu GoDaddy do usługi Azure DNS jest stosunkowo bezproblemowa procedura.

**Chcę kupić domenę w domenie usługi App Service, ale czy mogę hostować moją domenę w witrynie GoDaddy zamiast usługi Azure DNS?**

Od 24 lipca 2017 r. domeny usługi App Service zakupione w portalu są hostowane w usłudze Azure DNS. Jeśli wolisz używać innego dostawcy hostingu, musisz przejść do jego witryny, aby uzyskać rozwiązanie hostingowe domeny.

**Czy muszę płacić za ochronę prywatności mojej domeny?**

Podczas zakupu domeny za pośrednictwem witryny Azure portal, można dodać prywatność bez dodatkowych kosztów. Jest to jedna z zalet zakupu domeny za pośrednictwem usługi Azure App Service.

**Jeśli uznam, że nie chcę już mojej domeny, czy mogę odzyskać pieniądze?**

W przypadku zakupu domeny nie są naliczane opłaty za okres pięciu dni, w tym czasie możesz zdecydować, że nie chcesz domeny. Jeśli zdecydujesz, że nie chcesz domeny w tym okresie pięciu dni, nie są naliczane opłaty. (Domeny.uk są wyjątkiem od tego. Jeśli kupisz domenę .uk, zostaniesz obciążony natychmiast i nie możesz uzyskać zwrotu pieniędzy).

**Czy mogę używać domeny w innej aplikacji usługi Azure App Service w mojej subskrypcji?**

Tak. Po dodaniu dostępu do domen niestandardowych i bloku SSL w witrynie Azure portal, zobaczysz domeny, które zostały zakupione. Możesz skonfigurować aplikację tak, aby używała dowolnej z tych domen.

**Czy mogę przenieść domenę z jednej subskrypcji do innej subskrypcji?**

Domenę można przenieść do innej grupy subskrypcji/zasobów przy użyciu polecenia cmdlet [programu Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) Programu PowerShell.

**Jak zarządzać domeną niestandardową, jeśli nie mam obecnie aplikacji usługi Azure App Service?**

Możesz zarządzać domeną, nawet jeśli nie masz aplikacji sieci Web usługi aplikacji. Domena może być używana dla usług platformy Azure, takich jak maszyna wirtualna, magazyn itp. Jeśli zamierzasz używać domeny dla aplikacji sieci Web Usługi aplikacji, należy dołączyć aplikację sieci Web, która nie znajduje się w planie bezpłatnej usługi aplikacji, aby powiązać domenę z aplikacją sieci web.

**Czy mogę przenieść aplikację sieci web z domeną niestandardową do innej subskrypcji lub ze środowiska usługi App Service w wersji 1 do wersji 2?**

Tak, możesz przenieść aplikację internetową między subskrypcjami. Postępuj zgodnie ze wskazówkami dotyczącymi [przenoszenia zasobów na platformie Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Istnieje kilka ograniczeń podczas przenoszenia aplikacji internetowej. Aby uzyskać więcej informacji, zobacz [Ograniczenia dotyczące przenoszenia zasobów usługi App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

Po przeniesieniu aplikacji sieci web powiązania nazw hostów domen w ramach ustawienia domen niestandardowych powinny pozostać takie same. Aby skonfigurować powiązania nazw hostów, nie są wymagane żadne dodatkowe kroki.

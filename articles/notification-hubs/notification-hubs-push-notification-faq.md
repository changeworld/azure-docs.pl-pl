---
title: 'Centra powiadomień Azure: Często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft'
description: Często zadawane pytania dotyczące projektowania/implementacji rozwiązań dotyczących usługi Notification Hubs
services: notification-hubs
documentationcenter: mobile
author: dimazaid
manager: kpiteira
editor: spelluru
keywords: powiadomienia wypychane, powiadomień wypychanych, powiadomień wypychanych systemu iOS, powiadomień wypychanych systemu android, ios push, wypychanych systemu android
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: fbdb52b1b2dee682021fd822d584551f41f723ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Wypychanie powiadomień z usług Azure Notification Hubs: często zadawane pytania
## <a name="general"></a>Ogólne
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Co to jest struktura zasobów centra powiadomień?

Centra powiadomień Azure ma dwa poziomy zasobu: koncentratorów i przestrzenie nazw. Koncentrator jest wypychania pojedynczego zasobu, który może przechowywać informacje wypychania i platform w jednej aplikacji. Przestrzeń nazw jest kolekcją koncentratory w jednym regionie.

Mapowanie zalecane odpowiada jednej przestrzeni nazw z jedną aplikacją. W przypadku przestrzeni nazw może mieć produkcji koncentratora, który współpracuje z aplikacjami produkcji, testowania koncentratora, który współpracuje z testowania aplikacji i tak dalej.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Co to jest model cen dla centra powiadomień?
Najnowszy cenowa szczegółowe informacje można znaleźć w [Notification Hubs — cennik] strony. Centra powiadomień jest on rozliczany na poziomie przestrzeni nazw. (Aby uzyskać definicję przestrzeni nazw, zobacz "Co to jest struktura zasobów usługi Notification Hubs?") Centra powiadomień oferuje trzy warstwy:

* **Bezpłatne**: Ta warstwa jest dobry punkt wyjścia do eksploracji możliwości wypychania. Nie zaleca się w przypadku aplikacji produkcyjnej. Pobierz 500 urządzeń i 1 milion wypchnięcia uwzględnione na przestrzeni nazw na miesiąc, z żadnej gwarancji, Umowa dotycząca poziomu (SLA) usługi.
* **Podstawowe**: Ta warstwa (lub planu Standard) jest zalecane w przypadku mniejszych aplikacji produkcyjnej. Pobierz 200 000 urządzeń i 10 milionów wypchnięcia uwzględnione na przestrzeni nazw na miesiąc, jako linii bazowej. Opcje przydziału wzrostu są uwzględniane.
* **Standardowe**: Ta warstwa jest zalecane dla nośnika do produkcji dużych aplikacji. Możesz uzyskać 10 milionów urządzeń i 10 milionów wypchnięcia uwzględnione na przestrzeni nazw na miesiąc, jako linii bazowej. Przydział wzrost opcje i rozbudowane dane telemetryczne możliwości są uwzględniane.

Funkcje warstwy standardowa:
* **Rozbudowane informacje telemetryczne**: centra powiadomień na komunikat Telemetrii można użyć do śledzenia żądań wypychania, a opinii systemu powiadomień platformy do debugowania.
* **Obsługa wielu podmiotów**: możesz pracować z poświadczeń systemu powiadomień platformy na poziomie przestrzeni nazw. Ta opcja umożliwia łatwe podzielone dzierżaw koncentratory, w ramach tego samego obszaru nazw.
* **Zaplanowane wypychania**: można zaplanować powiadomienia, które będą wysyłane w dowolnym momencie.

### <a name="what-is-the-notification-hubs-sla"></a>Co to jest SLA centra powiadomień?
W warstwach Basic i Standard Notification Hubs poprawnego skonfigurowania aplikacji można wysyłać powiadomienia wypychane lub wykonywać operacje zarządzania rejestracji co najmniej 99,9% czasu. Aby dowiedzieć się więcej na temat umowy SLA, przejdź do [SLA centra powiadomień](https://azure.microsoft.com/support/legal/sla/notification-hubs/) strony.

> [!NOTE]
> Ponieważ powiadomienia wypychane są zależne od innych firm systemy powiadomień platformy (na przykład Apple APNS i Google FCM), nie ma żadnej gwarancji SLA, w celu dostarczania tych wiadomości. Po centra powiadomień w partie są wysyłane do systemów powiadomień platformy (gwarancji SLA), jest odpowiedzialny za systemów powiadomień platformy w celu dostarczenia wypchnięć (nie gwarancji SLA).

### <a name="which-customers-are-using-notification-hubs"></a>Którzy użytkownicy korzystają z usługi Notification Hubs?
Wielu klientów używać usługi Notification Hubs. Poniżej przedstawiono niektóre z nich ważne:

* Sochi 2014: Setki grup zainteresowań, 3 milionów urządzeń i 150 + milionów powiadomienia wysyłane w dwóch tygodni. [Analiza przypadku: Sochi]
* Skanska: [— analiza przypadków: Skanska]
* Czas Seattle: [— analiza przypadków: razy Seattle]
* Mural.LY: [— analiza przypadków: Mural.ly]
* 7Digital: [— analiza przypadków: 7Digital]
* Aplikacje usługi Bing: Dziesiątki milionów urządzeń wysyłać powiadomienia 3 milionów na dzień.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Jak uaktualnić lub starszą wersję mojej koncentratora lub przestrzeni nazw z inną warstwą?
Przejdź do  **[portalu Azure]** > **centra powiadomień w przestrzeni nazw** lub **usługi Notification Hubs**. Wybierz zasób, aby zaktualizować, a następnie przejdź do **warstwy cenowej**. Należy uwzględnić następujące wymagania:

* Stosuje zaktualizowano warstwę cenową do *wszystkie* koncentratory w pracy z przestrzeni nazw.
* Liczba urządzeń przekracza limit wynoszący warstwy, które jest powrót do subskrypcji, należy usunąć urządzenia, zanim można obniżyć.


## <a name="design-and-development"></a>Projektowania i opracowywania
### <a name="which-server-side-platforms-do-you-support"></a>Platformy po stronie serwera, w których są obsługiwane?
Zestawy SDK serwera są dostępne dla platformy .NET, Java, Node.js, PHP i Python. Interfejsy API centra powiadomień są oparte na interfejsy REST, więc może współpracować bezpośrednio z interfejsów API REST, jeśli używasz różnych platform lub nie chcesz, dodatkowe zależności. Aby uzyskać więcej informacji, przejdź do [interfejsów API REST centra powiadomień] strony.

### <a name="which-client-platforms-do-you-support"></a>Które platformy klienta są obsługiwane?
Powiadomienia wypychane są obsługiwane w przypadku [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [uniwersalnych systemu Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android Chin (za pośrednictwem usługi Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) i [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md), i [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Aby uzyskać więcej informacji, przejdź do [samouczków usługi Notification Hubs wprowadzenie] strony.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Są obsługiwane wiadomości SMS, wiadomości e-mail lub powiadomienia w sieci web?
Centra powiadomień jest przeznaczony głównie do wysyłania powiadomień do aplikacji mobilnych. Nie dostarcza adres e-mail lub tekst wiadomości możliwości. Jednak platformy innych firm, które zapewniają następujące możliwości można zintegrować z usługi Notification Hubs wysyłać powiadomienia natywnych powiadomień wypychanych przy użyciu [Mobile Apps].

Centra powiadomień nie udostępniają usługi dostarczania powiadomień wypychanych w przeglądarce, bez. Klientów można zaimplementować tę funkcję za pomocą SignalR na obsługiwanych platformach po stronie serwera. Jeśli chcesz wysyłać powiadomienia do aplikacji przeglądarki w piaskownicy Chrome, zobacz [samouczek aplikacji dla programu Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>W jaki sposób aplikacje mobilne i powiązane usługi Azure Notification Hubs i kiedy ich używać?
Jeśli masz istniejącą aplikację mobilną wstecz zakończenia i chcesz dodać tylko możliwość wysyłania powiadomień wypychanych, można użyć usługi Azure Notification Hubs. Jeśli chcesz skonfigurować aplikację mobilną wstecz zakończenia od początku, należy wziąć pod uwagę przy użyciu funkcji Mobile Apps w usłudze Azure App Service. Aplikacji mobilnej automatycznie inicjuje Centrum powiadomień, dzięki czemu umożliwia łatwe wysyłanie powiadomień wypychanych z zaplecza aplikacji mobilnej. Cennik Mobile Apps zawiera podstawowe opłat za Centrum powiadomień. Płaci się tylko gdy przekracza wypchnięć uwzględnione. Aby uzyskać więcej szczegółów kosztów, przejdź do [App Service — ceny] strony.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Ile urządzeń strony I obsługuje I wysyłania powiadomień wypychanych przy użyciu usługi Notification Hubs?
Zapoznaj się [Notification Hubs — cennik] strony szczegółowe informacje na temat liczby obsługiwanych urządzeń.

Jeśli potrzebujesz pomocy technicznej dla zarejestrowanych urządzeń więcej niż 10 milionów [skontaktuj się z nami](https://azure.microsoft.com/overview/contact-us/) bezpośrednio i możemy pomóc Ci skalować rozwiązania.

### <a name="how-many-push-notifications-can-i-send-out"></a>Jak wiele powiadomień wypychanych można wysłać?
W zależności od wybranej warstwy usługi Azure Notification Hubs jest automatycznie skalowany na podstawie liczby powiadomienia przepływających przez system.

> [!NOTE]
> Koszty ogólne użycie może zwiększyć zależności od liczby obsługiwanej powiadomień wypychanych. Upewnij się, że masz świadomość limity warstwy opisane na [Notification Hubs — cennik] strony.
> 
> 

Naszym klientom wysyłanie codziennie miliony powiadomień wypychanych przy użyciu usługi Notification Hubs. Nie trzeba wykonywać żadnych czynności specjalne skalowania zasięg powiadomień wypychanych tak długo, jak używasz usługi Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak długo trwa dla wysyłane wypychanie powiadomień do urządzenia?
W przypadku użycia normalny, gdzie przychodzące obciążenie jest zgodne, a nawet, usługi Azure Notification Hubs może przetworzyć co najmniej *minutę wysyła powiadomienia wypychane 1 milion*. Ta stawka mogą się różnić w zależności od liczby tagów, wysyła przychodzące i inne czynniki zewnętrzne.

Podczas szacowany czas dostawy usługa oblicza elementów docelowych dla każdej platformy i tras wiadomości do usługi wypychania powiadomień (PNS) na podstawie znaczników lub wyrażeń tagów. Jest odpowiedzialny za systemu powiadomień platformy, aby wysyłać powiadomienia do urządzenia.

System powiadomień platformy nie gwarantuje wszystkie umowy SLA dostarczania powiadomień. Jednak większość powiadomienia wypychane są dostarczane na urządzenia docelowe w ciągu kilku minut (zazwyczaj w ciągu 10 minut) od momentu, gdy są one wysyłane do usługi Notification Hubs. Kilka powiadomień może zająć więcej czasu.

> [!NOTE]
> Centra powiadomień Azure ma zasady w celu porzucić wszystkie powiadomienia wypychane, które nie są dostarczane do systemu powiadomień platformy w ciągu 30 minut. To opóźnienie może się tak dziać istnieje wiele możliwych przyczyn, ale większość często, ponieważ system powiadomień platformy jest ograniczanie aplikacji.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>Istnieje już żadnych gwarancji opóźnienia?
Z powodu charakter powiadomień wypychanych (są dostarczane przez zewnętrznych, specyficzne dla platformy systemu powiadomień platformy) należy nie ma żadnej gwarancji opóźnienia. Zazwyczaj większość powiadomienia wypychane są dostarczane w ciągu kilku minut.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co należy wziąć pod uwagę podczas projektowania rozwiązania z koncentratorami obszary nazw i powiadomień?
#### <a name="mobile-appenvironment"></a>Środowisko/aplikacji mobilnych
* Użyj jednego centrum powiadomień dla aplikacji mobilnej na środowisko.
* W scenariuszu wielodostępnym każdy dzierżawca powinien mieć osobnego koncentratora.
* Nigdy nie udostępniaj tego samego Centrum powiadomień w środowisku produkcyjnym i przetestuj środowisk. Takie rozwiązanie może spowodować problemy podczas wysyłania powiadomień. (Apple oferuje piaskownicy i produkcji Push punktów końcowych, każda z osobnych poświadczeń).
* Domyślnie możesz wysłać powiadomienia testowe do zarejestrowanych urządzeń za pośrednictwem portalu Azure lub składnik Azure zintegrowane w programie Visual Studio. Próg wynosi 10 urządzeń, które są losowo dobierane spośród puli rejestracji.

> [!NOTE]
> Jeśli Centrum był oryginalnie skonfigurowany za pomocą certyfikatu piaskownicy firmy Apple, a następnie tak skonfigurować, aby korzystać z certyfikatu produkcyjnego firmy Apple, oryginalny tokenów urządzeń są nieprawidłowe. Nieprawidłowy tokeny spowodować wypchnięć się niepowodzeniem. Oddzielne środowiska produkcyjnego i testowego, a przy użyciu różnych hubs dla różnych środowisk.
> 
> 

#### <a name="pns-credentials"></a>Poświadczenia systemu powiadomień platformy
Po zarejestrowaniu aplikacji mobilnej z portalu dla deweloperów platformy (na przykład firmy Apple lub Google) są wysyłane tokenów identyfikator i zabezpieczeń aplikacji. Zaplecza aplikacji zapewnia te tokeny platformy systemu powiadomień platformy, aby powiadomienia wypychane mogą być wysyłane do urządzenia. Tokeny zabezpieczające może być w formie certyfikaty (na przykład system Apple iOS lub Windows Phone) lub kluczy zabezpieczeń (na przykład systemu Google Android lub Windows). Muszą zostać skonfigurowane w usłudze notification hubs. Konfiguracja jest zazwyczaj wykonywane na poziomie Centrum powiadomień, ale można także przeprowadzić na poziomie przestrzeni nazw w scenariuszu wielodostępnym.

#### <a name="namespaces"></a>Przestrzenie nazw
Przestrzenie nazw może służyć do grupowania wdrożenia. Można one również używane do reprezentowania wszystkie centra powiadomień dla wszystkich dzierżawców w tej samej aplikacji w wielodostępnym scenariusza.

#### <a name="geo-distribution"></a>Dystrybucja geograficzna
Geograficznie dystrybucji nie zawsze jest krytyczne w scenariuszach powiadomień wypychanych. Nie są równomiernie różnych PNSes (na przykład usługi APNS lub GCM), które dostarczają powiadomień wypychanych do urządzeń.

Jeśli masz aplikację, która jest używana globalnie, można utworzyć koncentratory w różnych przestrzeniach nazw przy użyciu usługi Notification Hubs w różnych regionach platformy Azure na całym świecie.

> [!NOTE]
> Nie zaleca się to rozmieszczenie ponieważ zwiększa koszt zarządzania, szczególnie w przypadku rejestracji. Należy to zrobić tylko wtedy, gdy istnieje potrzeba jawnego.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>Zrobić rejestracje z zaplecza aplikacji lub bezpośrednio za pomocą klienta urządzenia?
Rejestracje z zaplecza aplikacji są przydatne, gdy konieczne jest uwierzytelnianie klientów przed utworzeniem rejestracji. Są one również przydatne w przypadku tagi, które zostały utworzone lub zmodyfikowane przez zaplecza aplikacji opartych na logice aplikacji. Aby uzyskać więcej informacji, przejdź do [wskazówki wewnętrznej bazy danych rejestracji] i [wskazówki wewnętrznej bazy danych rejestracji 2] stron.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Co to jest model zabezpieczeń dostarczania powiadomień wypychanych?
Centra powiadomień Azure używa [sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md)— na podstawie modelu zabezpieczeń. Możesz użyć tokeny sygnatury dostępu współdzielonego na poziomie głównym przestrzeni nazw lub na poziomie koncentratora szczegółowe powiadomienia. Udostępnione sygnatury dostępu, tokeny można ustawić wykonaj reguł autoryzacji inny, na przykład, Wyślij komunikat uprawnienia lub nasłuchiwania uprawnienia powiadomień. Aby uzyskać więcej informacji, zobacz [model zabezpieczeń usługi Notification Hubs] dokumentu.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak obsługiwać poufnych ładunku w powiadomieniach wypychanych?
Wszystkie powiadomienia są dostarczane na urządzenia docelowe za platformy systemu powiadomień platformy. Jeśli powiadomienie jest wysyłane do usługi Azure Notification Hubs, jest przetwarzane i przekazane do odpowiednich systemu powiadomień platformy.

Wszystkie połączenia od nadawcy do usługi Azure Notification Hubs do systemu powiadomień platformy, używać protokołu HTTPS.

> [!NOTE]
> Centra powiadomień Azure nie rejestruje ładunku wiadomości w dowolny sposób.
> 
> 

Aby wysłać ładunki poufne, firma Microsoft zaleca używanie wzorca Secure Push. Nadawca zapewnia powiadomienie ping przy użyciu identyfikatora wiadomości na urządzeniu bez poufnych ładunku. Aplikacji na urządzeniu odebrania ładunku aplikacji wymaga bezpiecznego interfejsu API bezpośrednio do pobrania treść wiadomości. Przewodnik na temat sposobu implementacji tego wzorca, przejdź do [powiadomienia Push Secure koncentratory samouczek] strony.

## <a name="operations"></a>Operacje
### <a name="what-support-is-provided-for-disaster-recovery"></a>Jakie jest obsługiwane dla odzyskiwania po awarii?
Udostępniamy pokrycia odzyskiwania po awarii metadanych po naszej stronie (nazwa usługi Notification Hubs, ciąg połączenia i innych ważnych informacji). Po wyzwoleniu scenariusza odzyskiwania po awarii, dane rejestracji jest *tylko segment* infrastruktury usługi Notification Hubs, który zostało utracone. Należy wdrożyć rozwiązanie do wypełnienia tych danych do nowego po odzyskiwaniu Centrum:

1. Tworzenie Centrum powiadomień dodatkowej w różnych centrach danych. Firma Microsoft zaleca utworzenie od samego początku, aby użytkownik z zdarzenie odzyskiwania po awarii, które mogą mieć wpływ na możliwości zarządzania. Można również utworzyć w czasie zdarzenia odzyskiwania po awarii.

2. Wypełnij Centrum powiadomień dodatkowej rejestracji z Centrum powiadomień podstawowego. Nie zaleca się zachowanie rejestracji na obu koncentratorów i ich synchronizowania jako rejestracji są dostępne w. Takie rozwiązanie nie działa poprawnie z powodu dostępu do właściwych tendencji rejestracji wygaśnie po stronie systemu powiadomień platformy. Centra powiadomień czyści je jako odbierze systemu powiadomień platformy swoją opinię na temat rejestracji wygasły lub nieprawidłowy.  

Firma Microsoft ma dwa zalecenia dla zaplecza aplikacji:

* Użyj aplikacji zaplecza przechowującą danego zestawu rejestracje jego końcem. Następnie można przeprowadzić wstawiania zbiorczego w Centrum powiadomień dodatkowej.

* Za pomocą wewnętrznej bazy aplikacji, która pobiera regularne zrzutu rejestracji z Centrum powiadomień głównej jako kopia zapasowa. Następnie można przeprowadzić wstawiania zbiorczego w Centrum powiadomień dodatkowej.

> [!NOTE]
> Funkcja eksportu/importu rejestracji dostępne w warstwie standardowa jest opisany w [rejestracje eksportu/importu] dokumentu.
> 
> 

Jeśli nie masz zaplecze, po uruchomieniu aplikacji na urządzeniach docelowych, wykonują nowej rejestracji w Centrum powiadomień dodatkowej. Centrum powiadomień dodatkowej będzie mieć aktywnych urządzeń zarejestrowanych.

Będą w czasie, gdy urządzenia z aplikacjami nieotwarte nie będą otrzymywać powiadomienia.

### <a name="is-there-audit-log-capability"></a>Istnieje możliwość dziennika inspekcji?
Wszystkie operacje zarządzania usługi Notification Hubs, przejdź do dzienników operacji, które są widoczne w [portalu Azure].

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów
### <a name="what-troubleshooting-capabilities-are-available"></a>Jakie funkcje do rozwiązywania problemów są dostępne?
Centra powiadomień Azure zapewnia kilka funkcji rozwiązywania problemów, szczególnie w przypadku najbardziej typowym scenariuszem porzuconych powiadomienia. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługą Notification Hubs] oficjalny dokument.

### <a name="what-telemetry-features-are-available"></a>Jakie funkcje dane telemetryczne są dostępne?
Azure umożliwia usługi Notification Hubs wyświetlania danych telemetrycznych w [portalu Azure]. Szczegóły miar są dostępne na [metryki centra powiadomień] strony.

> [!NOTE]
> Powiadomienia o pomyślnym po prostu oznacza, że powiadomienia wypychane zostały dostarczone do zewnętrznego systemu powiadomień platformy (na przykład APN do firmy Apple) lub usługi GCM dla usług Google. Jest odpowiedzialny za systemu powiadomień platformy do dostarczenia powiadomienia do urządzeń docelowych. Zazwyczaj systemu powiadomień platformy nie ujawnia metryki dostarczania osobom trzecim.  
> 
> 

Firma Microsoft udostępnia również możliwość eksportowania danych telemetrycznych programowo (w warstwie standardowej). Aby uzyskać więcej informacji, zobacz [Przykładowe metryki centra powiadomień].

[portalu Azure]: https://portal.azure.com
[Notification Hubs — cennik]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Analiza przypadku: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Analiza przypadku: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[— Analiza przypadków: Czasy Seattle]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Analiza przypadku: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Analiza przypadku: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[interfejsów API REST centra powiadomień]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[samouczków usługi Notification Hubs wprowadzenie]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[samouczek aplikacji dla programu Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[wskazówki wewnętrznej bazy danych rejestracji]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[wskazówki wewnętrznej bazy danych rejestracji 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[model zabezpieczeń usługi Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[powiadomienia Push Secure koncentratory samouczek]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Rozwiązywanie problemów z usługą Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[metryki centra powiadomień]: https://msdn.microsoft.com/library/dn458822.aspx
[Przykładowe metryki centra powiadomień]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[rejestracje eksportu/importu]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service — ceny]: https://azure.microsoft.com/pricing/details/app-service/

---
title: 'Azure Notification Hubs: Często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft'
description: Często zadawane pytania dotyczące projektowania/implementowania rozwiązań w usłudze Notification Hubs
services: notification-hubs
documentationcenter: mobile
author: jwargo
manager: patniko
editor: spelluru
keywords: powiadomienie wypychane, powiadomienia wypychane, powiadomień wypychanych systemu iOS, powiadomienia wypychane w systemie android, ios push, wypychane w systemie android
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 03/11/2019
ms.author: jowargo
ms.openlocfilehash: 642e586371a0ce6abe362f8b10b54bfd8baff678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872041"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Powiadomienia wypychane przy użyciu usługi Azure Notification Hubs: Często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Co to jest struktury zasobów usługi Notification hubs?

Usługa Azure Notification Hubs ma dwa poziomy zasobów: koncentratorów i przestrzeni nazw. Centrum to zasobu pojedynczej wypychania, jaką może zawierać informacje międzyplatformowych wypychanych jednej aplikacji. Przestrzeń nazw jest kolekcją koncentratory w jednym regionie.

Zalecane mapowanie pasuje do jednej przestrzeni nazw z jedną aplikacją. W przestrzeni nazw możesz mieć koncentratora produkcji, który działa z Twoją aplikacją w środowisku produkcyjnym, Centrum testowania, która współdziała z testowania aplikacji i tak dalej.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Co to jest model cen usługi Notification Hubs?

Najnowszy cennik można znaleźć na [Cennik usługi Notification Hubs] strony. Usługa Notification Hubs jest rozliczana na poziomie przestrzeni nazw. (Aby uzyskać definicję przestrzeni nazw, zobacz "Co to jest struktury zasobów usługi Notification hubs?") Usługa Notification Hubs oferuje trzy warstwy:

* **Bezpłatne**: Ta warstwa jest dobry punkt wyjścia do eksplorowania możliwości push. Nie jest zalecane dla aplikacji produkcyjnych. Uzyskaj 500 urządzeń i 1 milion wypchnięć uwzględnione na przestrzeń nazw na miesiąc, za pomocą żadnej gwarancji umowy dotyczącej poziomu (SLA), usługi.
* **Podstawowe**: Ta warstwa (lub warstwy standardowa) jest zalecane dla mniejszych aplikacji produkcyjnych. Uzyskaj 200 000 urządzeń i 10 milionów wypchnięć uwzględnione na przestrzeń nazw na miesiąc jako punkt odniesienia.
* **Standardowa**: Ta warstwa jest zalecane dla średnich i dużych produkcyjnych aplikacji interfejsów aplikacji. Pobierz 10 milionów urządzeń i 10 milionów wypchnięć uwzględnione na przestrzeń nazw na miesiąc jako punkt odniesienia. Zawiera zaawansowane danych telemetrycznych (dodatkowe dane dotyczące stanu wypychanej podane).

Funkcje w warstwie standardowa:

* **Rozbudowane informacje telemetryczne**: Na komunikat Telemetria usługi Notification Hubs umożliwia śledzenie dowolnego żądania wypychania i informacje zwrotne z systemu powiadomień platformy do debugowania.
* **Wielodostępność**: Poświadczenia systemu powiadomień platformy można pracować na poziomie przestrzeni nazw. Ta opcja umożliwia łatwe podzielić dzierżaw koncentratory, w ramach tej samej przestrzeni nazw.
* **Zaplanowane wypychania**: Można zaplanować powiadomienia, które zostaną wysłane w dowolnym momencie.
* **Zbiorcze operacje**: Funkcja eksportu/importu rejestracji umożliwia, zgodnie z opisem w [Rejestracje eksportu/importu] dokumentu.

### <a name="what-is-the-notification-hubs-sla"></a>Co to jest umowa SLA centrów powiadomień?

W przypadku warstw podstawowa i standardowa usługi Notification Hubs prawidłowo skonfigurowane aplikacje mogą wysyłać powiadomienia wypychane lub wykonywać operacje zarządzania rejestracją w co najmniej 99,9% czasu. Aby dowiedzieć się więcej o umowach SLA, przejdź do [Notification Hubs SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) strony.

> [!NOTE]
> Powiadomienia wypychane, zależą od systemów powiadomień platformy innych firm (np. usługi APNS firmy Apple i Google FCM), dlatego nie ma żadnej gwarancji umowy SLA, mechanizmem realizacji tych komunikatów. Usługa Notification Hubs wysyła partii do systemów powiadomień platformy (gwarantowana umowa SLA), po odpowiedzialność systemy powiadomień platformy, aby dostarczać wypchnięcia (umowa SLA nie gwarantowane).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Jak uaktualnić lub obniżyć wersję mojego Centrum lub przestrzeni nazw do innej warstwy

Przejdź do  **[Azure Portal]** > **przestrzenie nazw usługi Notification Hubs** lub **usługi Notification Hubs**. Wybierz zasób, aby zaktualizować, a następnie przejdź do **warstwy cenowej**. Należy uwzględnić następujące wymagania:

* Zaktualizowano warstwę cenową dotyczy *wszystkich* centrów w przestrzeni nazw, pracujemy z użyciem.
* Liczba Twoich urządzeń przekracza limit wynoszący warstwy której jesteś powrót do subskrypcji, należy usunąć urządzenia, zanim obniżanie poziomu.

## <a name="design-and-development"></a>Projektowanie i opracowywanie

### <a name="which-server-side-platforms-do-you-support"></a>Platform po stronie serwera, w których są obsługiwane?

Zestawy SDK serwera są dostępne dla platformy .NET, Java, Node.js, PHP i Python. Interfejsy API centrów powiadomień są oparte na interfejsy REST, aby można było pracować bezpośrednio za pomocą interfejsów API REST, jeśli używasz różnych platform lub nie mają dodatkowe zależności. Aby uzyskać więcej informacji, przejdź do [Usługa Notification Hubs interfejsów API REST] strony.

### <a name="which-client-platforms-do-you-support"></a>Platformach klienckich, które są obsługiwane?

Powiadomienia wypychane są obsługiwane w przypadku [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android — Chiny (przy użyciu usługi Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) i Android, [aplikacji dla programu Chrome](notification-hubs-chrome-push-notifications-get-started.md), i [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Aby uzyskać więcej informacji, przejdź do [Samouczki wprowadzenie centra powiadomień] strony.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Są obsługiwane wiadomość SMS, wiadomości e-mail lub powiadomienia w sieci web?

Usługa Notification Hubs jest przeznaczony głównie do wysyłania powiadomień do aplikacji mobilnych. Zapewnia ona e-mail lub SMS możliwości komunikatów. Jednak platformy innych firm, które dostarczają te możliwości można zintegrować z usługą Notification Hubs wysyłać natywne powiadomienia push przy użyciu [Mobile Apps].

Usługa Notification Hubs nie udostępniają usługi dostarczania powiadomień wypychanych w przeglądarce, gotowe. Klienci mogą zaimplementować tę funkcję za pomocą biblioteki SignalR na obsługiwanych platformach po stronie serwera. Jeśli chcesz wysyłać powiadomienia do aplikacji przeglądarki w piaskownicy usługi dla programu Chrome, zobacz [samouczek aplikacji dla programu Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>W jaki sposób aplikacje mobilne i związane z usługi Azure Notification Hubs i kiedy ich używać?

Jeśli masz istniejącego zaplecza aplikacji mobilnej, i chcesz dodać możliwości do wysyłania powiadomień wypychanych, można użyć usługi Azure Notification Hubs. Jeśli chcesz skonfigurować zaplecza aplikacji mobilnej od podstaw, rozważ użycie funkcji Mobile Apps w usłudze Azure App Service. Aplikacja mobilna automatycznie aprowizuje Centrum powiadomień, aby umożliwia łatwe wysyłanie powiadomień wypychanych z zaplecza aplikacji mobilnej. Cennik usługi Mobile Apps zawiera opłat podstawowych dla Centrum powiadomień. Opłaty są naliczane tylko wtedy gdy przekracza wliczone wypchnięcia. Aby uzyskać szczegółowe informacje na temat kosztów, przejdź do [Cennik usługi App Service] strony.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Ile urządzeń strony można obsługiwać I wysyłania powiadomień wypychanych za pomocą usługi Notification Hubs?

Zapoznaj się [Cennik usługi Notification Hubs] strony szczegółowe informacje na temat liczby obsługiwanych urządzeń.

Jeśli potrzebujesz pomocy w przypadku urządzeń zarejestrowanych ponad 10 milionów [skontaktuj się z nami](https://azure.microsoft.com/overview/contact-us/) bezpośrednio i możemy Ci pomóc skalować swoje rozwiązanie.

### <a name="how-many-push-notifications-can-i-send-out"></a>Jak wiele powiadomień wypychanych można wysłać?

W zależności od wybranej warstwy usługi Azure Notification Hubs automatycznie skaluje na podstawie liczby powiadomień przepływają przez system.

> [!NOTE]
> Na podstawie liczby powiadomień wypychanych obsługiwanych zwiększyć koszty ogólne użycie. Upewnij się, że masz świadomość limity warstwy opisane na [Cennik usługi Notification Hubs] strony.

Naszym klientom codziennie wysłać miliony powiadomień wypychanych przy użyciu usługi Notification Hubs. Nie trzeba wykonywać żadnych specjalnych do skalowania zasięgu powiadomień wypychanych, tak długo, jak długo używasz usługi Azure Notification Hubs czynności.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak długo trwa wysyłany powiadomienia wypychane do dotarcie do mojego urządzenia?

W scenariuszu normalne użycie, gdzie przychodzące obciążenia ma cechy stałego, a nawet, usługi Azure Notification Hubs może przetworzyć co najmniej *1 miliona powiadomień wypychanych wysyła chwilę*. Stawki mogą się różnić w zależności od tego, czy liczba tagów, właściwości, które wysyła przychodzące oraz innych czynników zewnętrznych.

W czasie szacowany czas dostawy usługa oblicza elementów docelowych dla danej platformy i tras komunikatów do usługi Push Notification (PNS) na podstawie znaczników lub wyrażenia tagu. Jest odpowiedzialny za system powiadomień platformy, aby wysyłać powiadomienia do urządzenia.

System powiadomień platformy nie gwarantuje żadnej umowy SLA dla dostarczanie powiadomień. Jednak większość powiadomienia wypychane są dostarczane na urządzenia docelowe w ciągu kilku minut (zwykle w ciągu 10 minut) od chwili, gdy są one wysyłane do usługi Notification Hubs. Kilka powiadomienia może zająć więcej czasu.

> [!NOTE]
> Usługa Azure Notification Hubs obowiązują zasady w miejscu, można usunąć wszelkie powiadomienia wypychane, które nie są dostarczane do systemu powiadomień platformy w ciągu 30 minut. To opóźnienie może wystąpić z kilku powodów, ale większość często, ponieważ system powiadomień platformy jest ograniczanie aplikacji.

### <a name="is-there-any-latency-guarantee"></a>Czy istnieje żadnych gwarancji opóźnień?

Ze względu na charakter powiadomień wypychanych (dostarczane przez zewnętrznych, specyficzne dla platformy systemu powiadomień platformy) nie ma żadnej gwarancji opóźnień. Zazwyczaj większość powiadomienia wypychane są dostarczane w ciągu kilku minut.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co należy wziąć pod uwagę podczas projektowania rozwiązania przy użyciu przestrzeni nazw i notification hubs?

#### <a name="mobile-appenvironment"></a>Aplikacja/środowiska mobilnego

* Użyj jednego centrum powiadomień dla aplikacji mobilnej na środowisko.
* W scenariuszu z wieloma dzierżawami Każda dzierżawa powinna mieć osobnego koncentratora.
* Nigdy nie udostępniaj tego samego Centrum powiadomień w środowisku produkcyjnym i testowym. To rozwiązanie może spowodować problemy podczas wysyłania powiadomień. (Firma Apple oferuje piaskownica i Wypchnij produkcji punkty końcowe, każdy z oddzielnych poświadczeń).
* Domyślnie można wysłać powiadomień dotyczących testów do urządzeń zarejestrowanych za pośrednictwem witryny Azure portal lub składnika Zintegrowane platformy Azure w programie Visual Studio. Próg wynosi 10 urządzeń, które są wybrane w losowo wybranym momencie z puli rejestracji.

> [!NOTE]
> Jeśli Centrum pierwotnie został skonfigurowany za pomocą certyfikatu firmy Apple piaskownicy, a następnie została ponownie skonfigurowana do używania certyfikatu produkcyjnego firmy Apple, oryginalnym tokenów urządzeń są nieprawidłowe. Nieprawidłowe tokeny spowodować wypchnięć nie powiedzie się. Rozdzielenie środowisk produkcyjnych i testowych i używać różnych centrach w różnych środowiskach.

#### <a name="pns-credentials"></a>Poświadczenia systemu powiadomień platformy

Po zarejestrowaniu aplikacji mobilnych za pomocą portalu dla deweloperów platformy (na przykład firmy Apple lub Google) wysyłane są tokeny identyfikatora i zabezpieczeń aplikacji. Zaplecze aplikacji udostępnia tokeny te służą do platformy systemu powiadomień platformy, dzięki czemu można wysłać powiadomień wypychanych do urządzeń. Tokeny zabezpieczeń może być w postaci certyfikatów (na przykład, Apple iOS lub Windows Phone) lub kluczy zabezpieczeń (na przykład system Google Android lub Windows). Muszą one skonfigurowane w usłudze notification hubs. Konfiguracja jest zazwyczaj wykonywane na poziomie Centrum powiadomień, ale można także przeprowadzić na poziomie przestrzeni nazw w scenariuszu z wieloma dzierżawami.

#### <a name="namespaces"></a>Przestrzenie nazw

Przestrzenie nazw może służyć do grupowania wdrożenia. Mogą one również służyć do reprezentowania wszystkie centra powiadomień dla wszystkich dzierżaw w tej samej aplikacji w scenariuszu z wieloma dzierżawami.

#### <a name="geo-distribution"></a>Dystrybucja geograficzna

Rozkład geograficzny nie zawsze jest szczególnie ważne w scenariusze dotyczące powiadomień wypychanych. Różne PNSes (na przykład usługi APNS lub FCM), dostarczanie powiadomień wypychanych do urządzeń, które nie są dystrybuowane równomiernie.

W przypadku aplikacji, która jest używana jako globalnie centrów w różnych obszarach nazw można utworzyć przy użyciu usługi Notification Hubs w różnych regionach platformy Azure na całym świecie.

> [!NOTE]
> Nie zalecamy to rozwiązanie, ponieważ zwiększa koszt zarządzania, szczególnie w przypadku rejestracji Należy to zrobić tylko wtedy, gdy istnieje potrzeba jawnego.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Należy zrobić rejestracje z zaplecza aplikacji lub bezpośrednio za pomocą klienta urządzenia?

Rejestracje z zaplecza aplikacji są przydatne, gdy do uwierzytelniania klientów przed utworzeniem rejestracji. Są one również przydatne w przypadku znaczników, które zostały utworzone lub zmodyfikowane przez zaplecze aplikacji, w oparciu o logikę w aplikacji. Aby uzyskać więcej informacji, przejdź do [Wskazówki dotyczące rejestracji wewnętrznej bazy danych] i [Wskazówki dotyczące rejestracji wewnętrznej bazy danych 2] stron.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Co to jest model zabezpieczeń dostarczanie powiadomień wypychanych?

Usługa Azure Notification Hubs używa [sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md)— na podstawie modelu zabezpieczeń. Na głównym poziomie przestrzeni nazw lub na poziomie Centrum powiadomień szczegółowych, można użyć tokenów sygnatur dostępu współdzielonego. Udostępnione podpis dostępu, który tokenów można ustawić z reguły autoryzacji inny, na przykład, aby wysłać komunikat uprawnienia lub do nasłuchiwania pod kątem powiadomień uprawnień. Aby uzyskać więcej informacji, zobacz [Model zabezpieczeń centra powiadomień] dokumentu.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak obsługiwać poufnych ładunku w przypadku powiadomień push?

Wszystkie powiadomienia są dostarczane na urządzenia docelowe przez platformy systemu powiadomień platformy. Jeśli powiadomienie jest wysyłane do usługi Azure Notification Hubs, jest przetwarzane i przekazywane do odpowiednich systemu powiadomień platformy.

Wszystkie połączenia od nadawcy do usługi Azure Notification Hubs do systemu powiadomień platformy, używania protokołu HTTPS.

> [!NOTE]
> Usługa Azure Notification Hubs nie rejestruje ładunku komunikatów w dowolny sposób.

Aby wysłać ładunki poufne, firma Microsoft zaleca, przy użyciu wzorca Secure wypychania. Nadawca dostarcza powiadomienie ping z identyfikator wiadomości do urządzenia bez poufnych ładunku. Gdy aplikacja na urządzeniu odbiera ładunek, aplikacja wywołuje bezpiecznego interfejsu API bezpośrednio w celu pobrania szczegółów komunikatu. Aby uzyskać wskazówki dotyczące implementowania tego wzorca, przejdź do [Samouczek dotyczący Secure wypychania centrów powiadomień] strony.

## <a name="operations"></a>Operacje

### <a name="what-support-is-provided-for-disaster-recovery"></a>Jakie pomoc techniczna jest świadczona w przypadku odzyskiwania po awarii?

Firma Microsoft oferuje pokrycia odzyskiwania po awarii metadane po naszej stronie (nazwa usługi Notification Hubs, ciąg połączenia i inne krytyczne informacje). Po wyzwoleniu scenariusza odzyskiwania po awarii jest dane rejestracyjne *tylko segmentu* z infrastruktury usługi Notification Hubs, która zostanie utracony. Należy zaimplementować rozwiązanie ponownie wypełnić te dane do nowej po odzyskaniu Centrum:

1. Utwórz koncentrator dodatkowej powiadomienia w centrum danych innego. Zalecamy utworzenie go od samego początku, aby włączyć osłony dla od zdarzenia odzyskiwania po awarii, które mogą mieć wpływ na możliwości zarządzania. Można również utworzyć jeden w czasie zdarzenia odzyskiwania po awarii.

2. Należy wypełnić w Centrum powiadomień dodatkowej za pomocą rejestracji z Centrum powiadomień podstawowego. Nie zaleca się próby Obsługa rejestracji na obu koncentratorów i zachować synchronizację zgodnie z przychodzą rejestracji. Taka praktyka nie działa dobrze w związku z używaniem tendencja rejestracji wygasa po stronie systemu powiadomień platformy. Usługa Notification Hubs czyści je jako odbierze PNS swoją opinię na temat rejestracji wygasła lub jest nieprawidłowy.  

Mamy dwie zalecenia dotyczące zaplecza aplikacji:

* Użyj z zapleczem aplikacji, która utrzymuje danego zestawu rejestracje na jej końcu. Następnie można wykonywać zbiorcze wstawianie do Centrum powiadomień dodatkowej.
* Użyj z zapleczem aplikacji, która regularnie zrzutu rejestracji z Centrum powiadomień podstawowego do przechowywania kopii zapasowych. Następnie można wykonywać zbiorcze wstawianie do Centrum powiadomień dodatkowej.

> [!NOTE]
> Funkcja eksportu/importu rejestracji jest dostępne w warstwie standardowa jest opisana w [Rejestracje eksportu/importu] dokumentu.

Jeśli nie masz wewnętrznej bazy danych, po uruchomieniu aplikacji na urządzeniach docelowych, wykonują nową rejestrację w Centrum powiadomień dodatkowej. Po pewnym czasie Centrum powiadomień dodatkowej mają aktywnych urządzeń zarejestrowanych.

Będzie okres czasu, gdy urządzeń za pomocą aplikacji nieotwarte nie będą otrzymywać powiadomienia.

### <a name="is-there-audit-log-capability"></a>Czy istnieje możliwość dziennika inspekcji?

Tak. Aktualizacja operacji zarządzania usługi Notification Hubs w wszystkich dziennika aktywności platformy Azure, do której jest widoczna w [Azure Portal]. Dziennik aktywności platformy Azure oferuje szczegółowe dane operacji wykonywanych na zasobach w Twoich subskrypcjach. Przy użyciu dziennika aktywności, możesz określić zakres działań, kto i kiedy dla każdego zapisu (PUT, POST, DELETE) dla zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stan działania i inne odpowiednie właściwości. Jednak. Dziennik aktywności nie obejmują operacji odczytu (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

### <a name="what-troubleshooting-capabilities-are-available"></a>Jakie funkcje do rozwiązywania problemów są dostępne?

Usługa Azure Notification Hubs udostępnia kilka funkcji do rozwiązywania problemów, szczególnie w przypadku najbardziej typowym scenariuszem porzuconymi powiadomieniami. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów w usłudze Notification Hubs] oficjalny dokument.

### <a name="what-telemetry-features-are-available"></a>Jakie funkcje telemetrii są dostępne?

Usługa Azure Notification Hubs umożliwia, wyświetlanie danych telemetrycznych w [Azure Portal]. Szczegóły metryki są dostępne na [Notification Hubs metryki] strony.

Można także programowo uzyskać dostęp do metryk. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Pobieranie metryk usługi Azure Monitor przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Ta próbka używa nazwy użytkownika i hasła. Do korzystania z certyfikatu, przeciążenia metody FromServicePrincipal, aby zapewnić certyfikat, jak pokazano w [w tym przykładzie](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Pobieranie zasobu metryki i Dzienniki aktywności](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Monitorowanie interfejsu API REST Azure — przewodnik](../azure-monitor/platform/rest-api-walkthrough.md)


> [!NOTE]
> Powiadomienia zakończone powodzeniem po prostu oznacza, że powiadomienia wypychane zostaną dostarczone do zewnętrznego systemu powiadomień platformy (na przykład APNS dla firmy Apple) lub usługi FCM dla usług Google. Jest odpowiedzialny za system powiadomień platformy w celu dostarczania powiadomień na urządzenia docelowe. Zazwyczaj system powiadomień platformy nie ujawnia dostarczanie metryk do podmiotów trzecich.  

[Azure Portal]: https://portal.azure.com
[Cennik usługi Notification Hubs]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Usługa Notification Hubs interfejsów API REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Samouczki wprowadzenie centra powiadomień]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Samouczek aplikacji dla programu Chrome]: https://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Wskazówki dotyczące rejestracji wewnętrznej bazy danych]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Wskazówki dotyczące rejestracji wewnętrznej bazy danych 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Model zabezpieczeń centra powiadomień]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Samouczek dotyczący Secure wypychania centrów powiadomień]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Rozwiązywanie problemów w usłudze Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs metryki]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Rejestracje eksportu/importu]: https://docs.microsoft.com/en-us/azure/notification-hubs/export-modify-registrations-bulk
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[Cennik usługi App Service]: https://azure.microsoft.com/pricing/details/app-service/

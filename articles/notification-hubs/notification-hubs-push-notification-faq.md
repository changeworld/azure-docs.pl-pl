---
title: Usługi Azure Notification Hubs Często zadawane pytania (często zadawane pytania) | Dokumenty firmy Microsoft
description: Często zadawane pytania dotyczące projektowania i wdrażania rozwiązań w usłudze Azure Notification Hubs.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: powiadomienie push, powiadomienia push, powiadomienia push z systemem iOS, powiadomienia push android, ios push, android push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 3212520f37d33a2d8fb1b071506f688b9f75f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263833"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Powiadomienia wypychane za pomocą usługi Azure Notification Hubs: często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Jaka jest struktura zasobów centrów powiadomień?

Usługi Azure Notification Hubs ma dwa poziomy zasobów: centra i obszary nazw. Koncentrator to pojedynczy zasób wypychania, który może przechowywać informacje wypychania między platformami jednej aplikacji. Obszar nazw jest zbiorem koncentratorów w jednym regionie. Zalecane mapowanie pasuje do jednej przestrzeni nazw z jedną aplikacją. W obszarze nazw możesz mieć centrum produkcyjne, które współpracuje z aplikacją produkcyjną, centrum testowania, które współpracuje z aplikacją testową i tak dalej.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Jaki jest model cenowy dla centrów powiadomień?

Najnowsze szczegóły cen można znaleźć na stronie [Cennik centrum powiadomień.] Centra powiadomień są rozliczane na poziomie obszaru nazw. (Aby uzyskać definicję obszaru nazw, zobacz "Jaka jest struktura zasobów Centrów powiadomień?") Centra powiadomień oferują trzy warstwy:

* **Bezpłatna:** Ta warstwa jest dobrym punktem wyjścia do odkrywania możliwości wypychania. Nie jest zalecane w przypadku aplikacji produkcyjnych. Otrzymujesz 500 urządzeń i 1 milion wypycheń uwzględnionych na obszar nazw miesięcznie, bez gwarancji umowy dotyczącej poziomu usług (SLA).
* **Podstawowe:** Ta warstwa (lub warstwa standardowa) jest zalecana dla mniejszych aplikacji produkcyjnych. Otrzymujesz 200 000 urządzeń i 10 milionów wypycheń uwzględnionych na obszar nazw miesięcznie jako punkt odniesienia.
* **Standardowa:** Ta warstwa jest zalecana dla średnich i dużych aplikacji produkcyjnych. Otrzymujesz 10 milionów urządzeń i 10 milionów wypycheń uwzględnionych na obszar nazw miesięcznie jako punkt odniesienia. Zawiera zaawansowane dane telemetryczne (dodatkowe dane dotyczące stanu wypychania).

Funkcje warstwy standardowej:

* **Rozszerzone dane telemetryczne:** Za pomocą centrów powiadomień na telemetrii wiadomości można śledzić wszelkie żądania wypychania i opinie systemu powiadomień platformy do debugowania.
* **Multi-dzierżawy:** Można pracować z poświadczeń systemu powiadomień platformy na poziomie obszaru nazw. Ta opcja umożliwia łatwe dzielenie dzierżawców na koncentratory w tym samym obszarze nazw.
* **Zaplanowane wypychanie:** Możesz zaplanować wysłanie powiadomień w dowolnym momencie.
* **Operacje zbiorcze**: Włącza rejestracje Eksportuj/Importuj funkcje opisane w dokumencie [Eksport/Import rejestracji.]

### <a name="what-is-the-notification-hubs-sla"></a>Co to jest usługa SLA centrów powiadomień?

W warstwach Podstawowe i Standardowe centra powiadomień prawidłowo skonfigurowane aplikacje mogą wysyłać powiadomienia wypychane lub wykonywać operacje zarządzania rejestracją przez co najmniej 99,9 procent czasu. Aby dowiedzieć się więcej o umowy SLA, przejdź do strony [Umowy SLA centrów powiadomień.](https://azure.microsoft.com/support/legal/sla/notification-hubs/)

> [!NOTE]
> Ponieważ powiadomienia push zależą od systemów powiadomień platformy innych firm, takich jak usługa powiadomień push firmy Apple (APN) i komunikat FCM Firebase Cloud Messaging (FCM) firmy Google, nie ma gwarancji umowy SLA na dostarczanie tych wiadomości. Po notification Hubs wysyła partii do systemów powiadomień platformy (SLA gwarantowane), jest odpowiedzialny za systemy powiadomień platformy do dostarczania wypycheń (bez gwarancji SLA).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Jak uaktualnić lub obniżyć węzeł lub obszar nazw do innej warstwy?

Przejdź do**obszarów nazw centrów powiadomień** **[portalu Azure]** > lub **Centrów powiadomień**. Wybierz zasób, który chcesz zaktualizować, i przejdź do **warstwy cenowej**. Należy zwrócić uwagę na następujące wymagania:

* Zaktualizowana warstwa cenowa ma zastosowanie do *wszystkich* koncentratorów w obszarze nazw, z którymi pracujesz.
* Jeśli liczba urządzeń przekracza limit warstwy, do której się obniżasz, musisz usunąć urządzenia przed przejściem na starszą wersję.

## <a name="design-and-development"></a>Projektowanie i rozwój

### <a name="which-server-side-platforms-do-you-support"></a>Które platformy po stronie serwera obsługujecie?

SDK serwera są dostępne dla .NET, Java, Node.js, PHP i Python. Interfejsy API centrów powiadomień są oparte na interfejsach REST, dzięki czemu można pracować bezpośrednio z interfejsami API REST, jeśli używasz różnych platform lub nie chcesz dodatkowej zależności. Aby uzyskać więcej informacji, przejdź do [centrum powiadomień REST interfejsów API] strony.

### <a name="which-client-platforms-do-you-support"></a>Które platformy klienckie obsługujesz?

Powiadomienia push są obsługiwane dla [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), Android Chiny [(przez Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) i Android, i [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Aby uzyskać więcej informacji, przejdź do [centrum powiadomień wprowadzenie samouczki] strony.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Czy obsługujesz powiadomienia SMS, e-mail lub internetowe?

Centra powiadomień wysyłają powiadomienia do urządzeń z aplikacjami mobilnymi. Nie zapewnia możliwości wiadomości e-mail ani wiadomości tekstowych. Centra powiadomień również nie udostępnia usługi dostarczania powiadomień wypychanych w przeglądarce po wyjęciu z pudełka. Klienci mogą zaimplementować tę funkcję przy użyciu SignalR na platformach po stronie serwera. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Ile urządzeń mogę obsługiwać, jeśli wysyłam powiadomienia wypychane za pośrednictwem Centrów powiadomień?

Szczegółowe informacje na temat liczby obsługiwanych urządzeń można znaleźć na stronie [Cennik centrów powiadomień.]

Jeśli potrzebujesz pomocy technicznej dla ponad 10 milionów zarejestrowanych urządzeń, musisz podzielić urządzenia na wiele koncentratorów.

### <a name="how-many-push-notifications-can-i-send-out"></a>Ile powiadomień wypychanych mogę wysłać?

W zależności od wybranej warstwy usługi Azure Notification Hubs automatycznie skaluje się w górę na podstawie liczby powiadomień przesyłanych przez system.

> [!NOTE]
> Całkowity koszt użycia może wzrosnąć w zależności od liczby wysłanych powiadomień wypychanych. Upewnij się, że znasz limity warstwy opisane na stronie [Cennik Centrów powiadomień.]

Nasi klienci korzystają z Centrów powiadomień, aby codziennie wysyłać miliony powiadomień wypychanych. Nie musisz robić nic specjalnego, aby skalować zasięg powiadomień wypychanych, o ile używasz usługi Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak długo trwa dotarcie wysłanych powiadomień push do mojego urządzenia?

W scenariuszu normalnego użycia, w którym obciążenie przychodzące jest spójne, a nawet usługa Azure Notification Hubs może przetwarzać co najmniej *1 milion powiadomień wypychanych wysyła minutę.* Szybkość ta może się różnić w zależności od liczby tagów, charakteru przychodzących wysyłań i innych czynników zewnętrznych.

W szacowanym czasie dostawy usługa oblicza obiekty docelowe na platformę i kieruje wiadomości do usługi powiadomień wypychanych (PNS) na podstawie zarejestrowanych tagów lub wyrażeń tagów. Obowiązkiem sieci PNS jest wysyłanie powiadomień do urządzenia.

Usługa PNS nie gwarantuje żadnych umowy SLA w celu dostarczania powiadomień. Jednak większość powiadomień wypychanych są dostarczane do urządzeń docelowych w ciągu kilku minut (zazwyczaj w ciągu 10 minut) od momentu ich wysłania do Centrum powiadomień. Kilka powiadomień może zająć więcej czasu.

> [!NOTE]
> Usługa Azure Notification Hubs ma zasady w celu upuszczenia wszystkich powiadomień wypychanych, które nie są dostarczane do usługi PNS w ciągu 30 minut. To opóźnienie może się zdarzyć z wielu powodów, ale najczęściej, ponieważ usługa PNS jest ograniczanie aplikacji.

### <a name="is-there-any-latency-guarantee"></a>Czy istnieje gwarancja opóźnienia?

Ze względu na charakter powiadomień wypychanych (są one dostarczane przez zewnętrzny, specyficzne dla platformy usługi PNS), nie ma gwarancji opóźnienia. Zazwyczaj większość powiadomień wypychanych jest dostarczana w ciągu kilku minut.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co należy wziąć pod uwagę podczas projektowania rozwiązania z obszarami nazw i centrami powiadomień?

#### <a name="mobile-appenvironment"></a>Aplikacja mobilna/środowisko

* Użyj jednego centrum powiadomień na aplikację mobilną, na środowisko.
* W scenariuszu wielodostępnym każda dzierżawa powinna mieć oddzielne centrum.
* Nigdy nie udostępniaj tego samego centrum powiadomień dla środowisk produkcyjnych i testowych. Ta praktyka może powodować problemy podczas wysyłania powiadomień. (Firma Apple oferuje punkty końcowe sandbox i production push, z których każdy ma oddzielne poświadczenia).
* Domyślnie można wysyłać powiadomienia testowe do zarejestrowanych urządzeń za pośrednictwem witryny Azure portal lub składnika zintegrowanej platformy Azure w programie Visual Studio. Próg jest ustawiony na 10 urządzeń, które są wybierane losowo z puli rejestracji.

> [!NOTE]
> Jeśli koncentrator został pierwotnie skonfigurowany z certyfikatem piaskownicy firmy Apple, a następnie został ponownie skonfigurowany do używania certyfikatu produkcyjnego firmy Apple, oryginalne tokeny urządzenia są nieprawidłowe. Nieprawidłowe tokeny powodują niepowodzenie wypychania. Oddziel środowiska produkcyjne i testowe i użyj różnych koncentratorów dla różnych środowisk.

#### <a name="pns-credentials"></a>Poświadczenia PNS

Gdy aplikacja mobilna jest zarejestrowana w portalu deweloperskim platformy (na przykład Apple lub Google), wysyłany jest identyfikator aplikacji i tokeny zabezpieczające. Wewnętrznej bazy danych aplikacji udostępnia te tokeny do usługi PNS platformy, dzięki czemu powiadomienia wypychania mogą być wysyłane do urządzeń. Tokeny zabezpieczające mogą mieć postać certyfikatów (na przykład Apple iOS lub Windows Phone) lub kluczy bezpieczeństwa (na przykład Google Android lub Windows). Muszą być skonfigurowane w centrach powiadomień. Konfiguracja jest zazwyczaj wykonywane na poziomie centrum powiadomień, ale można również zrobić na poziomie obszaru nazw w scenariuszu wielu dzierżawców.

#### <a name="namespaces"></a>Namespaces

Przestrzenie nazw mogą być używane do grupowania wdrożeń. Mogą one również służyć do reprezentowania wszystkich centrów powiadomień dla wszystkich dzierżaw tej samej aplikacji w scenariuszu wielu dzierżaw.

#### <a name="geo-distribution"></a>Dystrybucja geograficzna

Dystrybucja geograficzna nie zawsze jest krytyczna w scenariuszach powiadomień wypychanych. Różne PNSes (na przykład APN lub FCM), które dostarczają powiadomienia wypychane do urządzeń nie są równomiernie rozproszone.

Jeśli masz aplikację, która jest używana globalnie, można utworzyć centra w różnych obszarach nazw przy użyciu usługi Centra powiadomień w różnych regionach platformy Azure na całym świecie.

> [!NOTE]
> Nie zalecamy tego rozwiązania, ponieważ zwiększa to koszty zarządzania, szczególnie w przypadku rejestracji. Należy to zrobić tylko wtedy, gdy istnieje wyraźna potrzeba.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Czy należy wykonywać rejestracje z zaplecza aplikacji lub bezpośrednio za pośrednictwem urządzeń klienckich?

Rejestracje z wewnętrznej bazy danych aplikacji są przydatne, gdy trzeba uwierzytelnić klientów przed utworzeniem rejestracji. Są one również przydatne, gdy masz tagi, które muszą zostać utworzone lub zmodyfikowane przez zaplecze aplikacji na podstawie logiki aplikacji. Aby uzyskać więcej informacji, przejdź do [wskazówki dotyczące rejestracji wewnętrznej bazy danych] i [wskazówki dotyczące rejestracji zaplecza 2] strony.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Co to jest model zabezpieczeń dostarczania powiadomień wypychaczy?

Usługa Azure Notification Hubs używa modelu zabezpieczeń opartego na [sygnaturach dostępu współdzielonego.](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Tokeny podpisu dostępu udostępnionego można używać na poziomie głównego obszaru nazw lub na poziomie centrum powiadomień szczegółowego. Tokeny podpisu dostępu współdzielonego można ustawić tak, aby były zgodne z różnymi regułami autoryzacji, na przykład w celu wysyłania uprawnień do wiadomości lub nasłuchiwania uprawnień powiadomień. Aby uzyskać więcej informacji, zobacz dokument [modelu zabezpieczeń Centra powiadomień.]

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak obsługiwać poufny ładunek w powiadomieniach wypychanych?

Wszystkie powiadomienia są dostarczane do urządzeń docelowych przez pns platformy. Gdy powiadomienie jest wysyłane do usługi Azure Notification Hubs, jest przetwarzane i przekazywane do odpowiedniego usługi PNS.

Wszystkie połączenia, od nadawcy do usługi Azure Notification Hubs do usługi PNS, użyj protokołu HTTPS.

> [!NOTE]
> Usługa Azure Notification Hubs nie rejestruje ładunku wiadomości.

Aby wysłać poufne ładunki, zalecamy użycie wzorca bezpiecznego wypychania. Nadawca dostarcza powiadomienie ping z identyfikatorem wiadomości do urządzenia bez poufnego ładunku. Gdy aplikacja na urządzeniu odbiera ładunek, aplikacja wywołuje bezpieczny interfejs API bezpośrednio, aby pobrać szczegóły wiadomości. Aby uzyskać przewodnik na temat implementacji tego wzorca, przejdź do [centrum powiadomień bezpieczne wypychanie strony samouczka.]

## <a name="operations"></a>Operacje

### <a name="what-support-is-provided-for-disaster-recovery"></a>Jakie wsparcie jest przewidziane dla odzyskiwania po awarii?

Zapewniamy pokrycie odzyskiwania po awarii metadanych na naszym końcu (nazwa centrum powiadomień, parametry połączenia i inne krytyczne informacje). Po wyzwoleniu scenariusza odzyskiwania po awarii dane rejestracyjne są *jedynym segmentem* infrastruktury Usługi powiadomień, który zostanie utracony. Należy zaimplementować rozwiązanie, aby ponownie wypełnić te dane w nowym centrum po odzyskaniu:

1. Utwórz dodatkowe centrum powiadomień w innym centrum danych. Firma Microsoft zaleca tworzenie jednego od początku, aby chronić cię przed zdarzeniem odzyskiwania po awarii, które mogą mieć wpływ na możliwości zarządzania. Można również utworzyć jeden w momencie zdarzenia odzyskiwania po awarii.

2. Wypełnij centrum powiadomień pomocniczych rejestracjami z centrum powiadomień podstawowych. Nie zalecamy zachowywania rejestracji w obu centrach i utrzymywania ich w synchronizacji w miarę rejestracji. Praktyka ta nie działa dobrze ze względu na wrodzoną tendencję rejestracji do wygaśnięcia po stronie PNS. Centra powiadomień czyszczą je, gdy otrzymują opinie usługi PNS dotyczące wygasłych lub nieprawidłowych rejestracji.  

Mamy dwa zalecenia dotyczące zaplecza aplikacji:

* Użyj wewnętrznej bazy danych aplikacji, która utrzymuje dany zestaw rejestracji na jego końcu. Następnie można wykonać wstawianie zbiorcze do pomocniczego centrum powiadomień.
* Użyj wewnętrznej bazy danych aplikacji, która pobiera regularny zrzut rejestracji z centrum powiadomień podstawowych jako kopię zapasową. Następnie można wykonać wstawianie zbiorcze do pomocniczego centrum powiadomień.

> [!NOTE]
> Rejestracje Eksportuj/Importuj funkcje dostępne w warstwie Standardowa jest opisana w dokumencie [Eksport/import rejestracji.]

Jeśli nie masz wewnętrznej bazy danych, gdy aplikacja uruchamia się na urządzeniach docelowych, wykonują nową rejestrację w centrum powiadomień pomocniczych. Po pewnym czasie centrum powiadomień pomocniczych będzie zarejestrowanych wszystkie aktywne urządzenia.

Będzie okres czasu, kiedy urządzenia z nieotwartymi aplikacjami nie będą otrzymywać powiadomień.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Czy wszystkie moje dane są przechowywane w postaci zaszyfrowanej?

Usługa Azure Notification Hubs szyfruje wszystkie dane klientów w spoczynku, z wyjątkiem tagów rejestracji. Z tego powodu nie należy przechowywać danych osobowych ani poufnych przy użyciu tagów.

### <a name="is-there-audit-log-capability"></a>Czy istnieje możliwość dziennika inspekcji?

Tak. Wszystkie operacje zarządzania centrum powiadomień zaktualizować dziennik aktywności platformy Azure, do którego jest widoczna w [witrynie Azure portal]. Dziennik aktywności platformy Azure oferuje wgląd w operacje wykonywane na zasobach w subskrypcjach. Za pomocą dziennika aktywności, można określić, co, kto i kiedy dla wszelkich operacji zapisu (PUT, POST, DELETE) wykonane dla zasobów w ramach subskrypcji. Można również zrozumieć stan operacji i innych odpowiednich właściwości. Jednak. Dziennik aktywności nie zawiera operacji odczytu (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

### <a name="what-troubleshooting-capabilities-are-available"></a>Jakie możliwości rozwiązywania problemów są dostępne?

Usługa Azure Notification Hubs udostępnia kilka funkcji rozwiązywania problemów, szczególnie w najbardziej typowym scenariuszu porzuconych powiadomień. Aby uzyskać szczegółowe informacje, zobacz oficjalny dokument [dotyczący rozwiązywania problemów z centrami powiadomień.]

### <a name="what-telemetry-features-are-available"></a>Jakie funkcje telemetryczne są dostępne?

Usługa Azure Notification Hubs umożliwia wyświetlanie danych telemetrycznych w [witrynie Azure portal.] Szczegóły dotyczące metryk są dostępne na stronie [Metryki centrów powiadomień.]

Można również programowo uzyskać dostęp do metryk. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Pobieranie metryk usługi Azure Monitor za pomocą platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). W tym przykładzie użyto nazwy użytkownika i hasła. Aby użyć certyfikatu, należy przeciążyć FromServicePrincipal metody, aby zapewnić certyfikat, jak pokazano w [tym przykładzie](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Uzyskiwanie metryk i dzienników działań dla zasobu](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Przewodnik interfejsu API interfejsu REST monitorowania platformy Azure](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Pomyślne powiadomienia oznaczają po prostu, że powiadomienia wypychane zostały dostarczone do zewnętrznego systemu PNS (na przykład APN dla systemów iOS i macOS lub FCM dla urządzeń z systemem Android). Obowiązkiem sieci PNS jest dostarczanie powiadomień do urządzeń docelowych. Zazwyczaj usługi PNS nie udostępnia metryki dostawy do stron trzecich.  

[Portal Azure]: https://portal.azure.com
[Ceny centrów powiadomień]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Centra powiadomień REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Samouczki Dotyczące za pracę w centrach powiadomień]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Wskazówki dotyczące rejestracji wewnętrznej bazy danych]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Wskazówki dotyczące rejestracji zaplecza 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Model zabezpieczeń Centra powiadomień]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Poradnik Bezpiecznego wypychania w centrach powiadomień]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Rozwiązywanie problemów z centrami powiadomień]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Metryki centrów powiadomień]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Rejestracje Eksport/Import]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Portal Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/

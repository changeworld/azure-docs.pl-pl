---
title: Informacje o Mobile Apps
description: Dowiedz się, jakie korzyści z usługi App Service można osiągać podczas pracy z aplikacjami mobilnymi w przedsiębiorstwie.
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.tgt_pltfrm: mobile-multiple
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d85ac89a4226b545419cea3413a04f4013a073be
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668704"
---
# <a name="getting-started"> </a>Informacje o funkcji Mobile Apps w usłudze Azure App Service

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

Usługa Azure App Service to oferta w pełni zarządzanej [platformy jako usługi](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) dla profesjonalnych deweloperów. Usługa ta oferuje bogaty zestaw funkcji na potrzeby scenariuszy internetowych, mobilnych i dotyczących integracji. 

Funkcja Mobile Apps usługi Azure App Service oferuje deweloperom w przedsiębiorstwach oraz integratorom systemów platformę służącą do tworzenia aplikacji mobilnych, która jest wysoko skalowalna i dostępna globalnie.

![Wizualne omówienie funkcji Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Dlaczego warto korzystać z usługi Mobile Apps?
Przy użyciu funkcji Mobile Apps można:

* **Kompilować aplikacje natywne i międzyplatformowe** — bez względu na to, czy kompilujesz natywne aplikacje dla systemów iOS, Android i Windows, czy też międzyplatformowe aplikacje platform Xamarin lub Cordova (PhoneGap), możesz korzystać z usługi App Service za pośrednictwem natywnych zestawów SDK.
* **Nawiązywać połączenia z systemami przedsiębiorstwa** — dzięki funkcji Mobile Apps można w ciągu kilku minut dodać opcję logowania firmowego oraz nawiązywać połączenia z zasobami przedsiębiorstwa lokalnie lub w chmurze.
* **Kompilować aplikacje gotowe do działania w trybie offline z opcją synchronizacji danych** — aby personel mobilny mógł pracować wydajniej, należy skompilować aplikacje działające w trybie offline, a następnie po nawiązaniu łączności za pomocą usługi Mobile Apps synchronizować w tle dane z dowolnych źródeł danych przedsiębiorstwa lub interfejsów API oprogramowania jako usługi (SaaS).
* **Wypychać powiadomienia do milionów użytkowników w ciągu sekund** — można kontaktować się z klientami dzięki błyskawicznym powiadomieniom wypychanym z dowolnego urządzenia, spersonalizowanym zgodnie z ich potrzebami i wysyłanym w odpowiednim czasie.

## <a name="mobile-apps-features"></a>Funkcje Mobile Apps
Następujące funkcje są ważnymi elementami tworzenia aplikacji mobilnych z obsługą chmury:

* **Uwierzytelnianie i autoryzacja** — obsługa dostawców tożsamości, obejmująca usługę Azure Active Directory na potrzeby uwierzytelniania w przedsiębiorstwie, a także dostawców sieci społecznościowych, takich jak Facebook, Google, Twitter i konta Microsoft. Funkcja Mobile Apps udostępnia usługę OAuth 2.0 dla każdego dostawcy. Można również zintegrować zestaw SDK dla dostawcy tożsamości, aby korzystać z funkcji specyficznych dla tego dostawcy.

    Dowiedz się więcej na temat [funkcji uwierzytelniania].

* **Dostęp do danych**: funkcja Mobile Apps oferuje współpracujące z urządzeniami przenośnymi źródło danych OData v3 połączone z usługą Azure SQL Database lub lokalnym serwerem SQL. Ponieważ ta usługa może opierać się na platformie Entity Framework, możesz łatwo zintegrować ją z innymi dostawcami danych NoSQL i SQL, w tym [Azure Table Storage], MongoDB i [Azure Cosmos DB], a także dostawcami interfejsów API SaaS, takimi jak Office 365 i Salesforce.com.

* **Synchronizacja w trybie offline**: zestawy SDK klientów ułatwiają tworzenie niezawodnych i dynamicznych aplikacji mobilnych, które korzystają z zestawu danych w trybie offline. Ten zestaw danych można automatycznie zsynchronizować z danymi zaplecza, uwzględniając obsługę rozwiązywania konfliktów.

  Dowiedz się więcej na temat [funkcji związanych z danymi].

* **Powiadomienia wypychane**: zestawy SDK klientów są bezproblemowo integrowane z możliwościami rejestracji w usłudze Azure Notification Hubs, co umożliwia wysyłanie powiadomień wypychanych do milionów użytkowników jednocześnie.

  Dowiedz się więcej na temat [funkcji powiadomień wypychanych].

* **Zestawy SDK klientów**: dostępny jest kompletny pakiet zestawów SDK klientów, które obsługują tworzenie aplikacji natywnych ([iOS], [Android] i [Windows]), międzyplatformowych ([Xamarin.iOS i Xamarin.Android], [Xamarin.Forms]) i hybrydowych ([Apache Cordova]). Każdy zestaw SDK klienta jest dostępny z licencją MIT i jest rozwiązaniem typu open source.

## <a name="azure-app-service-features"></a>Funkcje usługi Azure App Service
Poniższe funkcje platformy są przydatne w przypadku witryn produkcyjnych aplikacji mobilnych:

* **Skalowanie automatyczne**: usługa App Service pozwala szybko skalować w górę lub w poziomie, a co za tym idzie — obsługiwać dowolne przychodzące obciążenia klientów. Ręcznie wybierz liczbę i rozmiar maszyn wirtualnych lub skonfiguruj skalowanie automatyczne, aby skalować zaplecze aplikacji mobilnej w oparciu o obciążenie lub harmonogram.

  Dowiedz się więcej na temat [skalowania automatycznego].

* **Środowiska przejściowe**: usługa App Service może uruchamiać wiele wersji witryny, co umożliwia testowanie A/B, testowanie w środowisku produkcyjnym jako część większego planu metodyki DevOps oraz miejscowe wdrażanie przejściowe nowego zaplecza.

  Dowiedz się więcej na temat [środowiska przejściowe].

* **Ciągłe wdrażanie**: usługę App Service można zintegrować z typowymi _systemami zarządzania kontrolą kodu źródłowego_ (SCM), umożliwiając w ten sposób łatwe wdrażanie nowej wersji zaplecza.

  Dowiedz się więcej na temat [opcje wdrożenia](../app-service/deploy-local-git.md).

* **Praca w sieci wirtualnej**: usługa App Service może nawiązywać połączenia z zasobami lokalnymi przy użyciu sieci wirtualnej, usługi Azure ExpressRoute lub połączeń hybrydowych.

  Dowiedz się więcej na temat [połączenia hybrydowe], [sieci wirtualnych] i [ExpressRoute].

* **Środowiska izolowane i dedykowane**: usługę App Service można uruchamiać w dedykowanym, w pełni izolowanym środowisku, aby bezpiecznie pracować z aplikacjami usługi Azure App Service. To środowisko jest idealne w przypadku obciążeń aplikacji wymagających dużej skali, izolacji lub bezpiecznego dostępu do sieci.

  Dowiedz się więcej na temat [środowiska usługi App Service].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z funkcją Mobile Apps w usłudze Azure App Service, wykonaj kroki samouczka z [wprowadzeniem]. Samouczek zawiera podstawowe informacje na temat tworzenia wybranego klienta i zaplecza mobilnego. Obejmuje on również zagadnienia, takie jak integrowanie uwierzytelniania, synchronizacji w trybie offline i powiadomień wypychanych. Kroki samouczka można wykonać wielokrotnie, jeden raz dla każdej aplikacji klienta.

Aby uzyskać więcej informacji o funkcji Mobile Apps, zapoznaj się z naszą [mapą nauki].
Aby uzyskać więcej informacji o platformie Azure App Service, zobacz temat [Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[wprowadzeniem]: app-service-mobile-ios-get-started.md
[Azure Table storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[funkcji uwierzytelniania]: ./app-service-mobile-auth.md
[funkcji związanych z danymi]: ./app-service-mobile-offline-data-sync.md
[funkcji powiadomień wypychanych]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS i Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[skalowania automatycznego]: ../app-service/manage-scale-up.md
[środowiska przejściowe]: ../app-service/deploy-staging-slots.md
[połączenia hybrydowe]: ../biztalk-services/integration-hybrid-connection-overview.md
[sieci wirtualnych]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[środowiska usługi App Service]: ../app-service/environment/intro.md
[mapą nauki]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Azure App Service]: ../app-service/overview.md

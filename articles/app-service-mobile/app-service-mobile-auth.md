---
title: Uwierzytelnianie i autoryzację w usłudze Azure App Service dla aplikacji mobilnych | Dokumentacja firmy Microsoft
description: Odwołanie koncepcyjne i omówienie uwierzytelniania / autoryzacji funkcji Azure App Service specjalnie dla aplikacji mobilnych
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 237310c607eb8488e53631b6e69d01703d1ebf99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Uwierzytelnianie i autoryzację w usłudze Azure App Service dla aplikacji mobilnych

W tym artykule opisano, jak uwierzytelnianie i autoryzacja działa podczas tworzenia natywnych aplikacji mobilnych z usługi aplikacji zaplecza. Usługi aplikacji udostępnia zintegrowane uwierzytelnianie i autoryzacja, aplikacje mobilne można zalogować się użytkowników bez konieczności zmieniania kodu w usłudze App Service. Zapewnia prosty sposób ochrona aplikacji i pracować z danymi użytkownika. 

Ten artykuł skupia się na projektowanie aplikacji mobilnej. Aby szybko rozpocząć pracę z usługi aplikacji uwierzytelniania i autoryzacji dla aplikacji mobilnej, zobacz jedną z następujących samouczków [Dodawanie uwierzytelniania do aplikacji systemu iOS] [ iOS] (lub [systemuAndroid], [Windows], [Xamarin.iOS], [Xamarin.Android], [platformy Xamarin.Forms], lub [Cordova ]). 

Aby uzyskać informacje na temat uwierzytelniania i autoryzacji w usłudze App Service, zobacz [uwierzytelnianie i autoryzację w usłudze Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="authentication-with-provider-sdk"></a>Uwierzytelnianie za pomocą dostawcy zestawu SDK

Po skonfigurowaniu wszystko, co w usłudze App Service można zmodyfikować klientów mobilnych, aby zalogować się przy użyciu usługi aplikacji. Istnieją dwie metody w tym miejscu:

* Korzystanie z zestawu SDK, publikującej dostawcy tożsamości danego do ustalenia tożsamości i uzyskać dostęp do usługi App Service.
* Użyj pojedynczy wiersz kodu tak, aby zalogować się użytkowników klienta Mobile Apps SDK.

> [!TIP]
> Większość aplikacji należy używać dostawcy zestawu SDK, aby uzyskać bardziej spójne środowisko podczas logowania, z obsługą tokenów odświeżania, a także aby uzyskać inne korzyści, które określa dostawcę.
> 
> 

Gdy używasz dostawcy SDK, można logowania użytkownika do bardziej ściśle zintegrowana z systemem operacyjnym, że aplikacja jest uruchomiona na środowisko. Ta metoda umożliwia także token dostawcy i niektóre informacje użytkownika na komputerze klienckim, co pozwala na znacznie łatwiejsze do wykorzystania wykres interfejsów API i dostosowanie środowiska użytkownika. Od czasu do czasu na blogi i fora go nazywa się "przebieg klienta" lub "przepływ klient skierowany" ponieważ kod na klienta loguje użytkowników i kod klienta ma dostęp do dostawcy tokenu.

Po uzyskaniu tokenu dostawcy musi wysłane do aplikacji usługi do sprawdzania poprawności. Po usługi aplikacji sprawdza poprawność tokenu, usługa aplikacji tworzy nowy token usługi aplikacji, która jest zwracana do klienta. Klient Mobile Apps SDK ma metody pomocnicze do zarządzania tym exchange i automatycznie dołączyć token do wszystkich żądań do aplikacji wewnętrznych. Deweloperzy mogą również zachować odwołania do tokenu dostawcy.

Aby uzyskać więcej informacji na przebieg uwierzytelniania, zobacz [przepływ uwierzytelniania usługi aplikacji](../app-service/app-service-authentication-overview.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Uwierzytelnianie bez dostawcy zestawu SDK

Jeśli nie chcesz skonfigurować dostawcy SDK, można zezwolić funkcji Mobile Apps usługi aplikacji Azure do logowania dla Ciebie. Klient Mobile Apps SDK Otwórz widok sieci web dostawcy wybrane i zalogować użytkownika. Od czasu do czasu na blogi i fora, jest on nazywany "serwera przepływ" lub "przepływ skierowane do serwera", ponieważ serwer zarządza procesem, który loguje się użytkowników i zestawu SDK klienta nigdy nie otrzyma tokenu dostawcy.

Kod, aby uruchomić ten przepływ znajduje się w samouczku uwierzytelniania dla każdej platformy. Na końcu przepływu klienta SDK ma token usługi aplikacji, a token są automatycznie dołączane do wszystkich żądań do wewnętrznej bazy danych aplikacji.

Aby uzyskać więcej informacji na przebieg uwierzytelniania, zobacz [przepływ uwierzytelniania usługi aplikacji](../app-service/app-service-authentication-overview.md#authentication-flow). 
## <a name="more-resources"></a>Więcej zasobów

Następujące samouczki przedstawiają sposób dodawania uwierzytelniania dla klientów mobilnych za pomocą [skierowane do serwera przepływ](../app-service/app-service-authentication-overview.md#authentication-flow):

* [Dodawanie uwierzytelniania do aplikacji systemu iOS][iOS]
* [Dodawanie uwierzytelniania do aplikacji systemu Android][systemuAndroid]
* [Dodawanie uwierzytelniania do aplikacji systemu Windows][Windows]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin.iOS][Xamarin.iOS]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin.Android][Xamarin.Android]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin.Forms][platformy Xamarin.Forms]
* [Dodawanie uwierzytelniania do aplikacji platformy Cordova][Cordova ]

Użyj następujących zasobów, jeśli chcesz użyć [klient skierowany przepływu](../app-service/app-service-authentication-overview.md#authentication-flow) dla usługi Azure Active Directory:

* [Użyj biblioteki uwierzytelniania usługi Active Directory dla systemu iOS][ADAL-iOS]
* [Korzystanie z biblioteki uwierzytelniania usługi Active Directory dla systemu Android][ADAL-Android]
* [Użyj biblioteki uwierzytelniania usługi Active Directory systemu Windows i Xamarin][ADAL-dotnet]

Użyj następujących zasobów, jeśli chcesz użyć [klient skierowany przepływu](../app-service/app-service-authentication-overview.md#authentication-flow) dla usługi Facebook:

* [Używanie zestawu SDK usługi Facebook dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Użyj następujących zasobów, jeśli chcesz użyć [klient skierowany przepływu](../app-service/app-service-authentication-overview.md#authentication-flow) na Twitterze:

* [Użyj usługi Twitter sieci szkieletowej dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Użyj następujących zasobów, jeśli chcesz użyć [klient skierowany przepływu](../app-service/app-service-authentication-overview.md#authentication-flow) dla usług Google:

* [Korzystanie z usługi Google logowania zestawu SDK dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[systemuAndroid]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[platformy Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova ]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

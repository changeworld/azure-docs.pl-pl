---
title: Uwierzytelnianie i autoryzacja w Azure App Service dla aplikacji mobilnych | Microsoft Docs
description: Informacje koncepcyjne i Omówienie funkcji uwierzytelniania/autoryzacji dla Azure App Service, w odniesieniu do aplikacji mobilnych
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 6017d9bc8b9a791e46fd1562660c9049ca0f72e5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388906"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Uwierzytelnianie i autoryzacja w Azure App Service dla aplikacji mobilnych

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

W tym artykule opisano sposób działania uwierzytelniania i autoryzacji podczas tworzenia natywnych aplikacji mobilnych z zaplecem App Service. App Service zapewnia zintegrowane uwierzytelnianie i autoryzację, dzięki czemu aplikacje mobilne mogą logować użytkowników w programie bez zmiany kodu w App Service. Zapewnia łatwy sposób ochrony aplikacji i pracy z danymi poszczególnych użytkowników. 

Ten artykuł koncentruje się na tworzeniu aplikacji mobilnych. Aby szybko rozpocząć pracę z App Service uwierzytelniania i autoryzacji aplikacji mobilnej, zobacz jeden z następujących samouczków [Dodawanie uwierzytelniania do aplikacji dla systemu iOS][iOS] (lub [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]lub [Cordova]). 

Aby uzyskać informacje na temat sposobu działania uwierzytelniania i autoryzacji w App Service, zobacz [uwierzytelnianie i autoryzacja w programie Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Uwierzytelnianie za pomocą zestawu SDK dostawcy

Po skonfigurowaniu wszystkich elementów w App Service można zmodyfikować klientów mobilnych, aby zalogować się przy użyciu App Service. Poniżej przedstawiono dwie podejścia:

* Użyj zestawu SDK, który jest publikowany przez danego dostawcę tożsamości w celu ustanowienia tożsamości, a następnie uzyskania dostępu do App Service.
* Użyj jednego wiersza kodu, tak aby zestaw SDK klienta Mobile Apps może zalogować użytkowników.

> [!TIP]
> Większość aplikacji powinna używać zestawu SDK dostawcy w celu zapewnienia bardziej spójnego środowiska, gdy użytkownicy logują się, aby użyć obsługi odświeżania tokenu i uzyskać inne korzyści, które określi dostawca.
> 
> 

W przypadku korzystania z zestawu SDK dostawcy użytkownicy mogą zalogować się do środowiska, które jest ściśle zintegrowane z systemem operacyjnym, na którym działa aplikacja. Ta metoda zapewnia również token dostawcy i niektóre informacje o użytkowniku na kliencie, co znacznie ułatwia korzystanie z interfejsów API programu Graph i dostosowywanie środowiska użytkownika. Czasami na blogach i forach jest on nazywany "przepływem klienta" lub "przepływem skierowanym do klienta", ponieważ kod na kliencie jest zalogowany, a kod klienta ma dostęp do tokenu dostawcy.

Po uzyskaniu tokenu dostawcy należy go wysłać do App Service w celu sprawdzenia poprawności. Po App Service zweryfikuje token, App Service tworzy nowy token App Service, który jest zwracany do klienta. Zestaw SDK klienta Mobile Apps ma metody pomocnika do zarządzania tą wymianą i automatycznego dołączania tokenu do wszystkich żądań do zaplecza aplikacji. Deweloperzy mogą również zachować odwołanie do tokenu dostawcy.

Aby uzyskać więcej informacji na temat przepływu uwierzytelniania, zobacz [App Service Flow Authentication](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Uwierzytelnianie bez zestawu SDK dostawcy

Jeśli nie chcesz konfigurować zestawu SDK dostawcy, możesz zezwolić Mobile Apps funkcji Azure App Service na zalogowanie się. Zestaw SDK klienta Mobile Apps otworzy widok sieci Web dla dostawcy wybierania i logowania użytkownika. Czasami na blogach i forach nazywa się "przepływ serwera" lub "przepływ ukierunkowany na serwer", ponieważ serwer zarządza procesem, który loguje się do użytkowników, a zestaw SDK klienta nigdy nie odbiera tokenu dostawcy.

Kod do uruchomienia tego przepływu znajduje się w samouczku dotyczącym uwierzytelniania dla każdej platformy. Na końcu przepływu zestaw SDK klienta ma token App Service, a token jest automatycznie dołączany do wszystkich żądań do zaplecza aplikacji.

Aby uzyskać więcej informacji na temat przepływu uwierzytelniania, zobacz [App Service Flow Authentication](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Więcej zasobów

W poniższych samouczkach pokazano, jak dodać uwierzytelnianie do klientów mobilnych przy użyciu [przepływu ukierunkowanego na serwer](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Dodawanie uwierzytelniania do aplikacji systemu iOS][iOS]
* [Dodawanie uwierzytelniania do aplikacji systemu Android][Android]
* [Dodawanie uwierzytelniania do aplikacji systemu Windows][Windows]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin. iOS][Xamarin.iOS]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin. Android][Xamarin.Android]
* [Dodawanie uwierzytelniania do aplikacji platformy Xamarin. Forms][Xamarin.Forms]
* [Dodawanie uwierzytelniania do aplikacji oprogramowania Cordova][Cordova]

Użyj następujących zasobów, jeśli chcesz użyć [przepływu ukierunkowanego na klienta](../app-service/overview-authentication-authorization.md#authentication-flow) dla Azure Active Directory:

* [Użyj Active Directory Authentication Library dla systemu iOS][ADAL-iOS]
* [Używanie Active Directory Authentication Library dla systemu Android][ADAL-Android]
* [Używanie Active Directory Authentication Library dla systemu Windows i platformy Xamarin][ADAL-dotnet]

Użyj następujących zasobów, jeśli chcesz użyć [przepływu ukierunkowanego na klienta](../app-service/overview-authentication-authorization.md#authentication-flow) w usłudze Facebook:

* [Korzystanie z zestawu SDK usługi Facebook dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Użyj następujących zasobów, jeśli chcesz użyć [przepływu ukierunkowanego na klienta](../app-service/overview-authentication-authorization.md#authentication-flow) dla usługi Twitter:

* [Korzystanie z sieci szkieletowej Twitter dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Użyj następujących zasobów, jeśli chcesz użyć [przepływu kierowanego przez klienta](../app-service/overview-authentication-authorization.md#authentication-flow) dla usługi Google:

* [Korzystanie z zestawu SDK logowania Google dla systemu iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

---
title: Testowanie aplikacji Microsoft Authentication Library (MSAL) | Azure
description: Dowiedz się więcej o testowaniu aplikacji Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/28/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3495ad5f691ac57b69ab5d3efcd5436cc66e9a6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307528"
---
# <a name="testing-msal-applications"></a>Testowanie aplikacji biblioteki MSAL
W tym artykule omówiono sugestie dotyczące testowania aplikacji Microsoft Authentication Library (MSAL).

## <a name="unit-testing"></a>Testy jednostkowe
Interfejsy API biblioteki MSAL używają [wzorzec konstruktora](https://wikipedia.org/wiki/Builder_pattern) dużym stopniu. Producenci są trudnym i żmudnym na testowanie. Zamiast tego zaleca się zawijać logiki uwierzytelniania poza interfejsem i testowanie, w swojej aplikacji.

## <a name="end-to-end-testing"></a>Testowanie end-to-end
Do testowania end-to-end, można skonfigurować konta testowego, testowania aplikacji lub nawet wtedy, rozdzielić katalogów. Nazwy użytkownika i hasła można wdrażać za pośrednictwem potoku ciągłej integracji (na przykład, zmienne kompilacji wpisu tajnego w DevOps platformy Azure). Kolejną strategią jest zapewnienie Testuj poświadczenia [usługi Key Vault](/azure/key-vault/) i skonfigurować komputer, na którym jest uruchamiane testy, aby dostęp do usługi Key Vault), na przykład przy instalowaniu certyfikatu. Możesz ją do użycia biblioteki MSAL firmy [strategii do uzyskiwania dostępu do usługi Key Vault](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.LabInfrastructure/KeyVaultSecretsProvider.cs#L112).

Gdy wystąpi uzyskanie tokenu, token dostępu i token odświeżania są buforowane. Pierwszy ma okres istnienia jedna godzina, ostatnie kilka miesięcy. Po wygaśnięciu token dostępu MSAL automatycznie używać tokenu odświeżania można uzyskać nową, bez interakcji z użytkownikiem. Możesz polegać na to zachowanie, aby aprowizować testów.

Jeśli masz skonfigurowany dostęp warunkowy, automatyzacja wokół niej będzie trudne. Będzie można ją łatwiej mają to krok wykonywany ręcznie, który łączy się z dostępu warunkowego (na przykład uwierzytelnianie wieloskładnikowe), które będą Dodaj znaczniki do pamięci podręcznej biblioteki MSAL i następnie zależą od dyskretnej pozyskanie tokenu, oznacza to, zależą od wcześniej zalogowanego użytkownika.

## <a name="web-apps"></a>Aplikacje internetowe
Użyj Selenium lub równoważne technologii, aby zautomatyzować aplikacji sieci web. Pobierz nazwy użytkownika i hasło z bezpiecznej lokalizacji.

## <a name="daemon-apps"></a>Aplikacje demona
Demon aplikacje używają wcześniej wdrożonej wpisy tajne (haseł lub certyfikatów), aby porozmawiać do usługi Azure Active Directory (AAD). Można wdrożyć wpisu tajnego do danego środowiska testowego lub aprowizować testów przy użyciu tokenu techniki buforowania. [Przyznania poświadczeń klienta](msal-authentication-flows.md#client-credentials) pobrać tokeny odświeżania, nie tylko dostęp do tokenów, które wygaśnie w ciągu jednej godziny.

## <a name="native-client-apps"></a>Aplikacje klienta natywnego
W przypadku klientów natywnych są różne podejścia do testowania:

* Użyj [grant nazwy użytkownika/hasła](msal-authentication-flows.md#usernamepassword) można pobrać tokenu w sposób nieinterakcyjny. Ten przepływ nie jest zalecane w środowisku produkcyjnym, ale można go używać do testowania.

* Użyj struktury, takich jak Appium lub Xamarin.Test, która udostępnia interfejs automatyzacji dla aplikacji i Biblioteka MSAL utworzony przeglądarki.

* Biblioteka MSAL uwidacznia punkt rozszerzeń, który umożliwia deweloperom wstawić własne środowisko przeglądarki. Biblioteka MSAL zespół wykorzystuje to wewnętrznie przetestować scenariusze interakcyjne uwierzytelniania. Zapoznaj się z [.NET ten projekt testowy](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.Integration.net45/SeleniumTests/InteractiveFlowTests.cs) aby zobaczyć, jak można wstrzyknąć [przeglądarki obsługiwane Selenium](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/tree/master/tests/Microsoft.Identity.Test.Integration.net45/Infrastructure) , może obsługiwać uwierzytelnianie.

## <a name="xamarin-apps"></a>Aplikacje platformy Xamarin
Zespół MSAL obecnie uruchamiane są testy na aplikację Xamarin, która używa platformy MSAL.NET; Używamy [platformy App Center](https://appcenter.ms/apps) do zarządzania urządzeniami, etc przebiegów testowych. Framework testów jest [Xamarin.UITest](/appcenter/test-cloud/uitest/). Ograniczenie, które znaleźliśmy to firma Microsoft nie można przetestować przeglądarki systemu tylko osadzone przeglądarki.

Podczas obliczania struktury testowej, warto również o przyjrzeć się Appium i innych środowisk testowych, jak również innych dostawców ciągłej integracji/ciągłego wdrażania w przenośnych miejsca.

## <a name="testing-the-token-cache"></a>Testowanie pamięci podręcznej tokenu
Niezależnie od tego, jakie platformy, możesz użyć biblioteki MSAL przechowuje tokeny w pamięci podręcznej tokenu. Na niektórych platformach Poinformuj MSAL jak do serializacji tej pamięci podręcznej. Na platformach mobilnych Biblioteka MSAL serializuje pamięci podręcznej dla Ciebie. Z perspektywy aplikacji pamięci podręcznej tokenu jest odpowiedzialny za trzy elementy:

* Przechowywanie tokenów w pamięci podręcznej, po ich pobrano (na przykład za pomocą `AcquireTokenInteractive` metoda)
* Pobieranie tokenów z pamięci podręcznej podczas wywoływania `AcquireTokenSilent` — metoda
* Pobieranie metadanych z konta z pamięci podręcznej podczas wywoływania `GetAccount` — metoda

Dlatego jeśli chcesz przetestować scenariusze pamięci podręcznej, należy rozważyć pisania scenariusz, w którym będzie:

* Uzyskiwanie jeden lub kilka tokenów (na przykład za pomocą [grant nazwy użytkownika/hasła](msal-authentication-flows.md#usernamepassword) przepływ, który jest najprostszym do testowania)
* Upewnij się, że `GetAccounts` działa
* Upewnij się, że `AcquireTokenSilent` działa

Warto również sprawdzić, czy:

* ponowne uruchomienie aplikacji nie powoduje usunięcia pamięci podręcznej
* `AcquireTokenSilent` nie powoduje odświeżenia tokenu odświeżania (czyli sieciowy wywołań do usługi AAD), ale służy tokenu dostępu, jeśli nie wygasł. Kontrolę klienta HTTP, można osiągnąć to i inne scenariusze związane z protokołu HTTP.  Na przykład .NET, zobacz [podając własne klasy HttpClient](msal-net-provide-httpclient.md)

## <a name="feedback"></a>Opinia
Możesz [dziennika problemów](developer-support-help-options.md#create-a-github-issue) lub zadać pytania dotyczące testowania. Dobry test umożliwiający obsługi jest jednym z celów zespołu.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o implementacji [rejestrowania](msal-logging.md) w aplikacji biblioteki MSAL.
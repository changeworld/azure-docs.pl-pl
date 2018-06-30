---
title: Ochrona zawartości przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym artykule nadaj Omówienie ochrony zawartości z usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: juliako
ms.openlocfilehash: 28c10d7c478ea7a9b1d1bfa91da79452eba3a4b6
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132992"
---
# <a name="content-protection-overview"></a>Omówienie ochrony zawartości

Usługi Azure Media Services służy do zabezpieczania od momentu, gdy opuszczą komputera za pośrednictwem przechowywania, przetwarzania i dostarczania multimediów. Z usługi Media Services można dostarczania zawartości na żywo i na żądanie dynamicznie szyfrowany za pomocą Advanced Encryption Standard (AES-128) lub jeden z trzech głównych prawami cyfrowymi systemów zarządzania (prawami cyfrowymi DRM): Microsoft PlayReady, Google Widevine i FairPlay firmy Apple. Usługa Media Services udostępnia usługę dostarczania kluczy AES i DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Na poniższym obrazie przedstawiono przepływu pracy ochrony zawartości Media Services: 

![Ochrona zawartości](./media/content-protection/content-protection.png)

&#42;*obsługuje dynamicznego szyfrowania AES-128 "klucz niezaszyfrowany", CBCS i CENC. Szczegółowe informacje można znaleźć macierzy obsługi [tutaj](#streaming-protocols-and-encryption-types).*

W tym artykule opisano pojęcia i terminologię istotne dla zrozumienia ochrony zawartości z usługi Media Services. Artykuł również zawiera łącza do artykułów, które zawierają opis do ochrony zawartości. 

## <a name="main-components-of-the-content-protection-system"></a>Głównymi składnikami systemu ochrony zawartości

Do pomyślnego ukończenia projektu aplikacji/systemowej "ochrony zawartości", należy całkowicie opis zakresu działania. Poniższa lista zawiera przegląd trzech części, który będzie potrzebny do wdrożenia. 

1. Azure Media Services kodu
  
  * Szablony licencji PlayReady, Widevine i FairPlay. Szablony umożliwiają skonfigurowanie prawa i uprawnienia dla każdego używanego DRMs
  * Autoryzacja dostarczania licencji, określanie logiki sprawdzania autoryzacji na podstawie oświadczeń w token JWT
  * Klucze zawartości, protokołów przesyłania strumieniowego i DRMs odpowiedniego stosowania definiującego szyfrowania DRM

  > [!NOTE]
  > Można zaszyfrować poszczególnych zasobów z wieloma typami szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [przesyłania strumieniowego protokoły i typy szyfrowania](#streaming-protocols-and-encryption-types), aby zobaczyć, co ma sens połączyć.
  
  Następujący artykuł Pokaż kroki szyfrowania zawartości z użyciem standardu AES: [Chroń za pomocą szyfrowania AES](protect-with-aes128.md)
 
2. Odtwarzacz klienta AES lub DRM. Aplikacji odtwarzacza wideo, oparte na player SDK (natywny lub przeglądarki) musi spełniać następujące wymagania:
  * Zestaw SDK Windows Media player obsługuje wymagane klientów DRM
  * Odtwarzacz zestaw SDK obsługuje wymagane protokołów przesyłania strumieniowego: Smooth, DASH lub HLS
  * Zestaw SDK Windows Media player musi być może obsłużyć, przekazując tokenu JWT żądanie nabycie licencji
  
    Odtwarzacz można tworzyć przy użyciu [interfejsu API usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API usługi Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) do określenia technologii DRM na różnych platformach DRM.

    Testowanie AES lub CENC (Widevine + PlayReady) szyfrowane zawartości, można użyć [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Upewnij się, kliknij przycisk "Zaawansowane opcje" i sprawdź AES i podaj token.

    Jeśli chcesz przetestować FairPlay zaszyfrowana zawartość, użyj [player tego testu](http://aka.ms/amtest). Odtwarzacz obsługuje Widevine, PlayReady, a FairPlay DRMs, a także AES-128 wyczyść klucza szyfrowania. Musisz wybrać prawo przeglądarki do testowania różnych DRMs: Chrome/Opera/Firefox Widevine, MS krawędzi/IE11 dla PlayReady, Safari na maOS dla FairPlay.

3. Secure Token Service (STS), która wystawia Token sieci Web JSON (JWT) jako tokenu dostępu, aby uzyskać dostęp do zasobów w wewnętrznej bazie danych. Można użyć usług dostarczania licencji AMS jako zasób wewnętrznej bazy danych. Usługa tokenu Zabezpieczającego musi definiują następujące elementy:

  * Wystawca i odbiorców (lub zakres)
  * Oświadczenia, które są zależne od wymagań biznesowych w ochrony zawartości
  * Weryfikacja konfiguracji symetrycznej lub asymetrycznej do sprawdzenia podpisu
  * Obsługa przerzucania kluczy (Jeśli to konieczne)

    Można użyć [to narzędzie STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) do testu STS, która obsługuje wszystkie typy 3 klucza weryfikacji: symetrycznego, asymetrycznego, lub usługi AAD z przerzucania klucza. 

> [!NOTE]
> Zdecydowanie zalecane jest skupić się i pełni przetestować każda część (opisanej powyżej) przed przejściem do następnego fragmentu. Aby przetestować systemu "ochrony zawartości", użyj narzędzi określonych na liście powyżej.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoły i typy szyfrowania przesyłania strumieniowego

Usługi Media Services umożliwia dostarczanie dynamicznie zaszyfrowany wyczyść klucza AES lub szyfrowania DRM, za pomocą PlayReady i Widevine, FairPlay zawartości. Obecnie można zaszyfrować formatów HTTP Live Streaming (HLS), MPEG DASH i Smooth Streaming. Każdy protokół obsługuje następujące metody szyfrowania:

|Protokół|Format kontenera|Schemat szyfrowania|
|---|---|---|---|
|MPEG-DASH|Wszyscy|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Wszyscy|AES|
||MPG2 TS |CBCS (Fairplay) |
||MPG2 TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

W v3 Media Services klucz zawartości jest skojarzony z StreamingLocator (zobacz [w tym przykładzie](protect-with-aes128.md)). Jeśli przy użyciu usługi Media Services klucza dostawy, powinien automatycznie wygenerować klucz zawartości. Należy generować klucz zawartości samodzielnie Jeśli używasz własnej klucza dostawy usług, czy należy do obsługi scenariusza wysokiej dostępności, gdy musisz mieć ten sam klucz zawartości w dwoma centrami danych.

Strumień zleconą przez odtwarzacz usługi Media Services używa określonego klucza do dynamicznego szyfrowania zawartości za pomocą klucza niezaszyfrowanego AES lub szyfrowania DRM. Aby odszyfrować strumienia, odtwarzacza żądań klucz z usługi Media Services klucza dostawy lub usługa klucza dostawy określona. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

## <a name="aes-128-clear-key-vs-drm"></a>Vs wyczyść klucza AES-128. DRM

Klienci często zastanawiasz się, czy należy używać szyfrowania AES lub DRM system. Główną różnicą między tymi dwoma systemami jest, że z szyfrowania AES klucz zawartości są przesyłane do klientów w niezaszyfrowanej postaci ("w Wyczyść"). W związku z tym klucz używany do szyfrowania zawartości można wyświetlić w śledzenia sieci na komputerze klienckim w postaci zwykłego tekstu. Wyczyść klucza szyfrowania AES-128 jest odpowiednia dla przypadków użycia, gdy przeglądarka jest zaufany (na przykład szyfrowania firmowej filmy wideo rozproszonych w obrębie firmy będą wyświetlane przez pracowników).

PlayReady, Widevine i FairPlay wszystkie zapewnia wyższy poziom szyfrowania w porównaniu do AES-128 wyczyść klucza szyfrowania. Klucz zawartości jest przekazywane w postaci zaszyfrowanej. Ponadto odszyfrowywania jest obsługiwany w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniej złośliwemu użytkownikowi na ataki. DRM zaleca się użycie zgłoszenia Podgląd może nie być zaufany wymagają najwyższy poziom zabezpieczeń.

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić zasobów magazynowane, zasoby powinny być szyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak szyfrowanie po stronie magazynu działa w wersji 3 usługi Media Services:

|Opcja szyfrowania|Opis|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi multimediów| AES 256 szyfrowanie klucza zarządzane przez usługę Media Services|Nieobsługiwane<sup>(1)</sup>|
|[Szyfrowanie usługi Magazyn danych w stanie spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera oferowanych przez usługi Azure Storage, klucz zarządzany przez usługę Azure lub przez klienta|Obsługiwane|
|[Szyfrowanie magazynu po stronie klienta](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Oferowane przez usługi Azure storage, klucz zarządzany przez klienta w magazynie kluczy szyfrowania po stronie klienta|Nieobsługiwane|

<sup>1</sup> Media Services w wersji 3 szyfrowanie magazynu (szyfrowanie AES 256) jest tylko wykorzystywać dla zapewnienia zgodności z zasoby zostały utworzone za pomocą usługi Media Services w wersji 2. Co oznacza działa w wersji 3 z dotychczasowej pamięci masowej zaszyfrowany zasoby, ale nie zezwala na tworzenie nowych.

## <a name="licenses-and-keys-delivery-service"></a>Usługi dostarczania licencji i kluczy

Usługi Media Services udostępnia usługę dostarczania kluczy do dostarczania licencji DRM (PlayReady, Widevine, FairPlay) i kluczy AES do autoryzowanych klientów. Interfejs API REST lub biblioteka klienta usługi Media Services umożliwia konfigurowanie zasad autoryzacji i uwierzytelnianie licencji i kluczy.

## <a name="control-content-access"></a>Kontrola dostępu do zawartości

Można kontrolować, kto ma dostęp do zawartości przez skonfigurowanie zasad klucza zawartości. Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Należy skonfigurować zasady klucza zawartości. Klienta (odtwarzacz) musi spełniać zasady, aby klucz mogą być dostarczane do klienta. Zasady klucza zawartości mogą mieć **Otwórz** lub **tokenu** ograniczeń. 

Za pomocą tokenu ograniczonej zawartości klucza zasad klucz zawartości jest wysyłane tylko do klienta, który stanowi prawidłowe JSON Web Token (JWT) lub simple web token (SWT) w żądaniu klucz/licencji. Token ten musi być wystawiony przez usługę tokenu zabezpieczającego (STS). Można użyć usługi Azure Active Directory jako tokenu Zabezpieczającego lub wdrożyć niestandardowe STS. Usługa tokenu Zabezpieczającego musi być skonfigurowana do utworzenia tokenu podpisany z określonego klucza i problem oświadczenia określony w konfiguracji ograniczenia tokenu. Usługa Media Services klucza dostawy zwraca klucz żądanej/licencji do klienta, jeśli token jest prawidłowy, a oświadczenia w tokenie pasują do klucza/licencji.

Po skonfigurowaniu zasadzie ograniczenia tokenu, należy określić klucz podstawowy weryfikacji, wystawcy i parametry odbiorców. Klucz podstawowy weryfikacji zawiera klucz, który został podpisany token, z. Wystawca jest bezpieczne usługi tokenu, który wystawia token. Odbiorców, nazywane również zakres, w tym artykule opisano celem tokenu lub zasobu tokenu zezwala na dostęp do. Usługa Media Services klucza dostawy weryfikuje, czy te wartości w tokenie pasują do wartości w szablonie.

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego

Jeśli zawartości została zaszyfrowana z więcej niż jeden DRM, użyj tagu szyfrowania w adresie URL przesyłania strumieniowego: (format = "m3u8-aapl, szyfrowanie = 'xxx').

Następujące kwestie:

* Typ szyfrowania nie ma określonego w adresie URL, jeśli tylko jeden szyfrowania została zastosowana do zasobu.
* Typ szyfrowania jest uwzględniana wielkość liter.
* Można określić następujące typy szyfrowania:
  * **cenc**: dla PlayReady lub Widevine (szyfrowania common encryption)
  * **cbcs-aapl**: dla FairPlay (szyfrowanie AES CBC)
  * **CBC**: dla AES envelope szyfrowania

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Skorzystaj z poniższych informacji dotyczące rozwiązywania problemów, aby uzyskać pomoc dotyczącą problemów z implementacją.

* Wystawca adres URL musi kończyć się "/". Odbiorców musi być identyfikatorem player aplikacji klienta. Ponadto Dodaj "/" na końcu adresu URL wystawcy.

  ```
  <add key="ida:audience" value="[Application Client ID GUID]" />
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
  ```

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na **Konfiguruj** aplikacji. Uprawnienia są wymagane dla każdej aplikacji lokalnych i wdrożonej wersji.
* Użyj poprawne wystawcy, podczas konfigurowania ochrony CENC dynamicznych.

  ```
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
  ```

  Nie działa następujące czynności:

  ```
  <add key="ida:issuer" value="https://username.onmicrosoft.com/" />
  ```

  Identyfikator GUID jest identyfikatorem dzierżawy usługi Azure AD Identyfikator GUID można znaleźć w **punkty końcowe** menu podręcznego w portalu Azure.

* Członkostwo w grupie GRANT oświadczeń uprawnień. Upewnij się, że poniżej znajduje się w pliku manifestu aplikacji usługi Azure AD: 

    "groupMembershipClaims": "All" (wartość domyślna to null)

## <a name="next-steps"></a>Kolejne kroki

[Ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)

---
title: Konfigurowanie aplikacji pod kątem dostępu do internetowych interfejsów API (wersja zapoznawcza) | Azure
description: Dowiedz się, jak skonfigurować aplikację zarejestrowaną za pomocą platformy tożsamości firmy Microsoft tak, aby uwzględnić identyfikatory URI przekierowania, poświadczenia lub uprawnienia na potrzeby dostępu do internetowych interfejsów API.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93e88cedfd098f450e8faeea894f7fdfc796cf17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443610"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>Szybki start: Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API (wersja zapoznawcza)

Aby aplikacje klienckie przeznaczone dla Internetu lub aplikacje poufne mogły uczestniczyć w przepływie przydziałów autoryzacji, który wymaga uwierzytelniania (i uzyskania tokenu dostępu), muszą ustanowić bezpieczne poświadczenia. Domyślną metodą uwierzytelniania obsługiwaną w witrynie Azure Portal jest użycie identyfikatora klienta i klucza tajnego.

Dodatkowo, zanim klient będzie mógł uzyskać dostęp do internetowego interfejsu API uwidocznionego przez aplikację zasobów (np. interfejsu API Microsoft Graph), platforma udzielania zgody zapewnia, że klient uzyska wymagane udzielenie uprawnień na podstawie żądanych uprawnień. Domyślnie wszystkie aplikacje mogą wybierać uprawnienia z interfejsu API Microsoft Graph. [Uprawnienie „Loguj się i odczytuj profil użytkownika” interfejsu API Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) jest domyślnie wybrane. Dla każdego żądanego internetowego interfejsu API możesz wybrać spośród [dwóch rodzajów uprawnień](developer-glossary.md#permissions):

* **Uprawnienia aplikacji** — Twoja aplikacja kliencka musi mieć bezpośredni dostęp do internetowego interfejsu API w swoim imieniu (bez kontekstu użytkownika). Tego rodzaju uprawnienia wymagają zgody administratora i nie są dostępne dla publicznych (mobilnych i klasycznych) aplikacji klienckich.
* **Uprawnienia delegowane** — Twoja aplikacja kliencka musi mieć dostęp do internetowego interfejsu API w imieniu zalogowanego użytkownika, ale z dostępem ograniczonym przez wybrane uprawnienie. Tego rodzaju uprawnienie może być udzielane przez użytkownika, chyba że wymaga ono zgody administratora.

  > [!NOTE]
  > Dodanie uprawnienia delegowanego do aplikacji nie powoduje automatycznego udzielenia zgody dla użytkowników w dzierżawie. Użytkownicy muszą nadal ręcznie wyrażać zgodę na dodane uprawnienia delegowane w czasie wykonywania, chyba że administrator wyrazi zgodę w imieniu wszystkich użytkowników.

W tym przewodniku Szybki start przedstawimy, jak skonfigurować aplikację pod kątem wykonywania następujących działań:

* [Dodawanie identyfikatorów URI przekierowania do aplikacji](#add-redirect-uris-to-your-application)
* [Dodawanie poświadczeń do aplikacji sieci web](#add-credentials-to-your-web-application)
* [Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy upewnij się, że są spełnione następujące wymagania wstępne:

* Znasz obsługiwane [uprawnienia i zgody](v2-permissions-and-consent.md), ponieważ ich znajomość jest istotna podczas tworzenia aplikacji, które mają być używane przez innych użytkowników lub aplikacje.
* Masz dzierżawę z zarejestrowanymi w niej aplikacjami.
  * Jeśli nie masz zarejestrowanych aplikacji, [dowiedz się, jak zarejestrować aplikacje za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md).
* Wyrażono zgodę na użycie środowiska w wersji zapoznawczej dla rejestracji aplikacji w witrynie Azure Portal. Kroki opisane w tym przewodniku Szybki start odpowiadają nowemu interfejsowi użytkownika i zadziałają tylko wtedy, gdy wyrażono zgodę na środowisko w wersji zapoznawczej.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logowanie do witryny Azure Portal i wybranie aplikacji

Przed skonfigurowaniem aplikacji wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, a następnie pozycję **Rejestracje aplikacji (wersja zapoznawcza)**.
1. Znajdź i wybierz aplikację do skonfigurowania. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji lub główną stronę rejestracji.
1. Wykonaj kroki konfigurowania aplikacji pod kątem dostępu do internetowych interfejsów API: 
    * [Dodawanie identyfikatorów URI przekierowania do aplikacji](#add-redirect-uris-to-your-application)
    * [Dodawanie poświadczeń do aplikacji internetowej](#add-credentials-to-your-web-application)
    * [Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Dodawanie identyfikatorów URI przekierowania do aplikacji

[![Dodawanie niestandardowych identyfikatorów URI przekierowania dla internetowej i publicznej aplikacji klienckiej](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Aby dodać identyfikator URI przekierowania do aplikacji:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uwierzytelnianie**.

1. Aby dodać niestandardowy identyfikator URI dla internetowej i publicznej aplikacji klienckiej, wykonaj następujące kroki:

   1. Znajdź sekcję **Identyfikator URI przekierowania**.
   1. Wybierz typ tworzonej aplikacji: **Internetowa** lub **Klient publiczny (mobilny i klasyczny)**.
   1. Podaj identyfikator URI przekierowania dla aplikacji.
      * Dla aplikacji internetowych podaj podstawowy adres URL aplikacji. Na przykład ciąg `http://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą używać tego adresu URL, aby zalogować się do internetowej aplikacji klienckiej.
      * W przypadku aplikacji publicznych podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Określ wartość specyficzną dla Twojej aplikacji, na przykład https://MyFirstApp.

1. Aby wybrać z sugerowanych identyfikatorów URI przekierowania dla klientów publicznych (mobilnych, klasycznych), wykonaj następujące kroki:

    1. Znajdź sekcję **Identyfikatory URI przekierowania dla klientów publicznych (mobilnych, klasycznych)** z sugerowanymi identyfikatorami.
    1. Wybierz odpowiednie identyfikatory URI przekierowania dla aplikacji za pomocą pól wyboru.

## <a name="add-credentials-to-your-web-application"></a>Dodawanie poświadczeń do aplikacji internetowej

[![Dodawanie certyfikatów i wpisów tajnych klienta](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Aby dodać poświadczenia do aplikacji internetowej:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Certyfikaty i wpisy tajne**.

1. Aby dodać certyfikat, wykonaj następujące kroki:

    1. Wybierz pozycję **Przekaż certyfikat**.
    1. Wybierz plik, który chcesz przekazać. Plik musi być plikiem typu cer, pem lub crt.
    1. Wybierz pozycję **Dodaj**.

1. Aby dodać wpis tajny klienta, wykonaj następujące kroki:

    1. Wybierz pozycję **Nowy wpis tajny klienta**.
    1. Dodaj opis wpisu tajnego klienta.
    1. Wybierz czas trwania.
    1. Wybierz pozycję **Dodaj**.

> [!NOTE]
> Po zapisaniu zmian w konfiguracji kolumna najdalej z prawej strony będzie zawierać wartość wpisu tajnego klienta. **Pamiętaj o skopiowaniu wartości** do użycia w kodzie aplikacji klienckiej, ponieważ nie będą one dostępne po opuszczeniu tej strony.

## <a name="add-permissions-to-access-web-apis"></a>Dodawanie uprawnień na potrzeby dostępu do internetowych interfejsów API

[![Dodawanie uprawnień do interfejsu API](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Aby dodać uprawnienia na potrzeby uzyskiwania dostępu do interfejsów API zasobu z poziomu klienta:

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Uprawnienia interfejsu API**.
1. Wybierz przycisk **Dodaj uprawnienia**.
1. Domyślnie widok umożliwia wybranie **interfejsów API firmy Microsoft**. Wybierz sekcję interfejsów API, która Cię interesuje:
    * **Interfejsy API firmy Microsoft** — umożliwia wybranie uprawnień dla interfejsów API firmy Microsoft, takich jak Microsoft Graph.
    * **Interfejsy API używane przez moją organizację** — umożliwia wybranie uprawnień dla interfejsów API, które zostały uwidocznione przez organizację, lub interfejsów API, z którymi jest zintegrowana Twoja organizacja.
    * **Moje interfejsy API** — umożliwia wybranie uprawnień dla interfejsów API, które zostały uwidocznione przez Ciebie.
1. Po wybraniu interfejsów API zobaczysz stronę **Żądanie uprawnień interfejsu API**. Jeśli interfejs API udostępnia uprawnienia delegowane i aplikacji, wybierz typ uprawnień, którego potrzebuje Twoja aplikacja.
1. Po zakończeniu wybierz pozycję **Dodaj uprawnienia**. Wrócisz do strony **Uprawnienia interfejsu API**, gdzie uprawnienia zostały zapisane i dodane do tabeli.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat innych powiązanych przewodników Szybki start dotyczących zarządzania aplikacjami:

* [Rejestrowanie aplikacji za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md)
* [Konfigurowanie aplikacji w celu uwidocznienia internetowych interfejsów API](quickstart-configure-app-expose-web-apis.md)
* [Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)
* [Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft](quickstart-remove-app.md)

Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

Aby dowiedzieć się więcej o wytycznych dotyczących oznaczania marką, które należy stosować podczas opracowywania aplikacji przy użyciu usługi Azure Active Directory, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).

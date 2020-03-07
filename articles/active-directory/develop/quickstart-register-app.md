---
title: Zarejestruj aplikację na platformie tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak dodawać i rejestrować aplikacje za pomocą platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: bb741fe36e00c3d528470866669e64e9b0b221ae
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377239"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft

Deweloperzy w przedsiębiorstwach i dostawcy oprogramowania jako usługi (SaaS) mogą tworzyć komercyjne usługi w chmurze lub aplikacje biznesowe i integrować je z platformą tożsamości firmy Microsoft, aby zapewnić bezpieczne logowanie i autoryzację dla swoich usług.

Ten przewodnik Szybki Start przedstawia sposób dodawania i rejestrowania aplikacji przy użyciu **rejestracje aplikacji** środowiska w Azure Portal, dzięki czemu aplikacja będzie mogła zostać zintegrowana z platformą tożsamości firmy Microsoft. Aby dowiedzieć się więcej na temat nowych funkcji i ulepszeń w nowym środowisku rejestrowania aplikacji, zobacz [ten wpis w blogu](https://developer.microsoft.com/graph/blogs/new-app-registration/).

## <a name="register-a-new-application-using-the-azure-portal"></a>Rejestrowanie nowej aplikacji w witrynie Azure Portal

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na wybraną dzierżawę usługi Azure AD.
1. Wyszukaj i wybierz **Azure Active Directory**. Na stronie **Active Directory** wybierz pozycję **rejestracje aplikacji** , a następnie wybierz pozycję **Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:

   - **Nazwa** — podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji.
   - **Obsługiwane typy konta** — wybierz konta, które aplikacja ma obsługiwać.

       | Obsługiwane typy konta | Opis |
       |-------------------------|-------------|
       | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli kompilujesz aplikację biznesową. Ta opcja nie jest dostępna, jeśli aplikacja nie jest rejestrowana w katalogu.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację jednodostępną.<br><br>Jest to opcja domyślna, chyba że rejestrujesz aplikację poza katalogiem. W przypadkach, gdy aplikacja jest rejestrowana poza katalogiem, wartość domyślna to aplikacja wielodostępna usługi Azure AD i konta osobiste Microsoft. |
       | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli aplikacja jest przeznaczona dla wszystkich klientów biznesowych i edukacyjnych.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację wielodostępną.<br><br>Jeśli aplikacja jest zarejestrowana w usłudze Azure AD jako jednodostępna, możesz ją zaktualizować do aplikacji wielodostępnej usługi Azure AD i z powrotem za pomocą bloku **Uwierzytelnianie**. |
       | **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft** | Wybierz tę opcję, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.<br><br>Ta opcja mapuje do usługi Azure AD aplikację wielodostępną i konta osobiste Microsoft.<br><br>Jeśli aplikacja jest zarejestrowana jako aplikacja wielodostępna usługi Azure AD z kontami osobistymi Microsoft, nie możesz zmienić tego w interfejsie użytkownika. Zamiast tego musisz użyć edytora manifestu aplikacji, aby zmienić obsługiwane typy konta. |

   - **Identyfikator URI przekierowania (opcjonalny)** — wybierz typ tworzonej aplikacji, **Internet** lub **Klient publiczny (mobilny i klasyczny)** , a następnie podaj identyfikator URI przekierowania (lub adres URL odpowiedzi) dla aplikacji.
       - W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg `https://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego.
       - W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Podaj wartość specyficzną dla Twojej aplikacji, np. `myapp://auth`.

     Aby zobaczyć konkretne przykłady dotyczące aplikacji internetowych lub natywnych, zapoznaj się z [przewodnikami Szybki start](https://docs.microsoft.com/azure/active-directory/develop).

1. Po zakończeniu wybierz pozycję **Rejestruj**.

    [![wyświetla ekran, aby zarejestrować nową aplikację w Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Usługa Azure AD przypisze do aplikacji unikatowy identyfikator (klienta), po czym nastąpi przeniesienie do strony **Przegląd** aplikacji. Aby dodać kolejne możliwości do aplikacji, możesz wybrać inne opcje konfiguracji, w tym znakowanie, certyfikaty i wpisy tajne, uprawnienia interfejsu API i więcej.

[Przykład ![na stronie przeglądu nowo zarejestrowanej aplikacji](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [uprawnieniach i zgodach](v2-permissions-and-consent.md).
- Aby włączyć dodatkowe funkcje konfiguracji w rejestracji aplikacji — takie jak poświadczenia i uprawnienia — oraz włączyć logowanie dla użytkowników z innych dzierżaw, zobacz następujące przewodniki Szybki start:
    - [Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md)
    - [Konfigurowanie aplikacji w celu uwidocznienia internetowych interfejsów API](quickstart-configure-app-expose-web-apis.md)
    - [Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)
- Wybierz [przewodnik Szybki start](https://docs.microsoft.com/azure/active-directory/develop), aby szybko utworzyć aplikację i dodać do niej funkcje, takie jak uzyskiwanie tokenów, odświeżanie tokenów, logowanie użytkownika i wyświetlanie informacji o użytkowniku.
- Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD reprezentujących zarejestrowaną aplikację i związku między nimi, zobacz [Application objects and service principal objects (Obiekty aplikacji i obiekty główne usługi)](app-objects-and-service-principals.md).
- Aby dowiedzieć się więcej na temat wytycznych dotyczących znakowania, do których należy stosować się podczas opracowywania aplikacji, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).

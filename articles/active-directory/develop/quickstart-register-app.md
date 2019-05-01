---
title: Rejestrowanie aplikacji z platformą tożsamości Microsoft — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak dodawać i rejestrować aplikacje za pomocą platformy tożsamości firmy Microsoft.
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
ms.date: 04/30/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2e78a837588ef177182f0c13db49e480845f772
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918753"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Szybki start: Rejestrowanie aplikacji w usłudze Microsoft platformy tożsamości

Deweloperzy w przedsiębiorstwach i dostawcy oprogramowania jako usługi (SaaS) mogą tworzyć komercyjne usługi w chmurze lub aplikacje biznesowe i integrować je z platformą tożsamości firmy Microsoft, aby zapewnić bezpieczne logowanie i autoryzację dla swoich usług.

Ten przewodnik Szybki Start dowiesz się, jak dodać i rejestrowanie aplikacji za pomocą **rejestracje aplikacji** środowisko w witrynie Azure portal, dzięki czemu aplikacja może zostać zintegrowany z platformą Microsoft identity. Aby dowiedzieć się więcej na temat nowych funkcji i ulepszeń w nowym środowisku rejestrowania aplikacji, zobacz [ten wpis w blogu](https://developer.microsoft.com/graph/blogs/new-app-registration/).

## <a name="prerequisite"></a>Wymagania wstępne

Aby rozpocząć pracę, musisz wyrazić zgodę na użycie środowiska w wersji zapoznawczej dla rejestracji aplikacji w witrynie Azure Portal. Kroki opisane w tym przewodniku Szybki start odpowiadają nowemu interfejsowi użytkownika i zadziałają tylko wtedy, gdy wyrażono zgodę na środowisko w wersji zapoznawczej.

## <a name="register-a-new-application-using-the-azure-portal"></a>Rejestrowanie nowej aplikacji w witrynie Azure Portal

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na wybraną dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, a następnie pozycję **Rejestracje aplikacji (wersja zapoznawcza) > Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:

   - **Nazwa** — podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji.
   - **Obsługiwane typy konta** — wybierz konta, które aplikacja ma obsługiwać.

       | Obsługiwane typy konta | Opis |
       |-------------------------|-------------|
       | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli kompilujesz aplikację biznesową. Ta opcja nie jest dostępna, jeśli aplikacja nie jest rejestrowana w katalogu.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację jednodostępną.<br><br>Jest to opcja domyślna, chyba że rejestrujesz aplikację poza katalogiem. W przypadkach, gdy aplikacja jest rejestrowana poza katalogiem, wartość domyślna to aplikacja wielodostępna usługi Azure AD i konta osobiste Microsoft. |
       | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli aplikacja jest przeznaczona dla wszystkich klientów biznesowych i edukacyjnych.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację wielodostępną.<br><br>Jeśli aplikacja jest zarejestrowana w usłudze Azure AD jako jednodostępna, możesz ją zaktualizować do aplikacji wielodostępnej usługi Azure AD i z powrotem za pomocą bloku **Uwierzytelnianie**. |
       | **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft** | Wybierz tę opcję, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.<br><br>Ta opcja mapuje do usługi Azure AD aplikację wielodostępną i konta osobiste Microsoft.<br><br>Jeśli aplikacja jest zarejestrowana jako aplikacja wielodostępna usługi Azure AD z kontami osobistymi Microsoft, nie możesz zmienić tego w interfejsie użytkownika. Zamiast tego musisz użyć edytora manifestu aplikacji, aby zmienić obsługiwane typy konta. |

   - **Identyfikator URI przekierowania (opcjonalny)** — wybierz typ tworzonej aplikacji, **Internet** lub **Klient publiczny (mobilny i klasyczny)**, a następnie podaj identyfikator URI przekierowania (lub adres URL odpowiedzi) dla aplikacji.
       - W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg `http://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego.
       - W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Podaj wartość specyficzną dla Twojej aplikacji, np. `myapp://auth`.

     Aby zobaczyć konkretne przykłady dotyczące aplikacji internetowych lub natywnych, zapoznaj się z [przewodnikami Szybki start](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).

1. Po zakończeniu wybierz pozycję **Rejestruj**.

    [![Rejestrowanie nowej aplikacji w witrynie Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Usługa Azure AD przypisze do aplikacji unikatowy identyfikator (klienta), po czym nastąpi przeniesienie do strony **Przegląd** aplikacji. Aby dodać kolejne możliwości do aplikacji, możesz wybrać inne opcje konfiguracji, w tym znakowanie, certyfikaty i wpisy tajne, uprawnienia interfejsu API i więcej.

[![Strona Przegląd nowo zarejestrowanej aplikacji](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [uprawnieniach i zgodach](v2-permissions-and-consent.md).
- Aby włączyć dodatkowe funkcje konfiguracji w rejestracji aplikacji — takie jak poświadczenia i uprawnienia — oraz włączyć logowanie dla użytkowników z innych dzierżaw, zobacz następujące przewodniki Szybki start:
    - [Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md)
    - [Konfigurowanie aplikacji w celu uwidocznienia internetowych interfejsów API](quickstart-configure-app-expose-web-apis.md)
    - [Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)
- Wybierz [przewodnik Szybki start](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts), aby szybko utworzyć aplikację i dodać do niej funkcje, takie jak uzyskiwanie tokenów, odświeżanie tokenów, logowanie użytkownika i wyświetlanie informacji o użytkowniku.
- Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD reprezentujących zarejestrowaną aplikację i związku między nimi, zobacz [Application objects and service principal objects (Obiekty aplikacji i obiekty główne usługi)](app-objects-and-service-principals.md).
- Aby dowiedzieć się więcej na temat wytycznych dotyczących znakowania, do których należy stosować się podczas opracowywania aplikacji, zobacz [Wytyczne dotyczące oznaczania aplikacji marką](howto-add-branding-in-azure-ad-apps.md).

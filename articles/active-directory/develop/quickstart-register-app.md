---
title: 'Szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft | Azure'
description: W tym przewodniku Szybki start dowiesz się, jak dodać i zarejestrować aplikację na platformie tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 79983678d13b810a521a00ba2c1978de92a5029f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309506"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft

W tym przewodniku Szybki start można zarejestrować aplikację przy użyciu **środowiska rejestracji aplikacji** w witrynie Azure portal. 

Aplikacja jest zintegrowana z platformą tożsamości firmy Microsoft, rejestrując ją w dzierżawie usługi Azure Active Directory. Deweloperzy korporacyjni i dostawcy oprogramowania jako usługi (SaaS) mogą tworzyć komercyjne usługi w chmurze lub aplikacje biznesowe, które można zintegrować z platformą tożsamości firmy Microsoft. Integracja zapewnia bezpieczne logowanie i autoryzację dla takich usług.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Dzierżawa usługi Azure AD](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Rejestrowanie nowej aplikacji w witrynie Azure Portal

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli twoje konto daje ci dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu. Ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**. W obszarze **Zarządzanie**wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz **pozycję Nowa rejestracja**.
1. W **rejestrze aplikacji**wprowadź opisową nazwę aplikacji, która ma być wyświetlana użytkownikom.
1. Określ, kto może korzystać z aplikacji w następujący sposób:

    | Obsługiwane typy konta | Opis |
    |-------------------------|-------------|
    | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli kompilujesz aplikację biznesową. Ta opcja nie jest dostępna, jeśli nie rejestrujesz aplikacji w katalogu.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację jednodostępną.<br><br>Ta opcja jest domyślna, chyba że rejestrujesz aplikację poza katalogiem. W przypadkach, gdy aplikacja jest rejestrowana poza katalogiem, wartość domyślna to aplikacja wielodostępna usługi Azure AD i konta osobiste Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli aplikacja jest przeznaczona dla wszystkich klientów biznesowych i edukacyjnych.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację wielodostępną.<br><br>Jeśli aplikacja została zarejestrowana jako usługa Azure AD tylko dla jednej dzierżawy, można ją zaktualizować, aby była usługą Azure AD wielodostępną i z powrotem do pojedynczej dzierżawy za pośrednictwem strony **uwierzytelniania.** |
    | **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft** | Wybierz tę opcję, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.<br><br>Ta opcja mapuje do usługi Azure AD aplikację wielodostępną i konta osobiste Microsoft.<br><br>Jeśli aplikacja została zarejestrowana jako usługi Azure AD wielodostępne i osobiste konta Microsoft, nie można zmienić tego ustawienia w interfejsie użytkownika. Zamiast tego musisz użyć edytora manifestu aplikacji, aby zmienić obsługiwane typy konta. |

1. W obszarze **Przekierowanie identyfikatora URI (opcjonalnie)** wybierz typ aplikacji, którą budujesz: **Klient internetowy** lub **publiczny (mobilny & pulpit)**. Następnie wprowadź adres URL przekierowania URI lub adres URL odpowiedzi dla aplikacji.

    * W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg `https://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego.
    * W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Podaj wartość specyficzną dla Twojej aplikacji, np. `myapp://auth`.

    Aby zapoznać się z przykładami aplikacji sieci Web lub aplikacji natywnych, zobacz przewodniki Szybki start na [platformie tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

1. Po zakończeniu wybierz pozycję **Rejestruj**.

    ![Pokazuje ekran, aby zarejestrować nową aplikację w witrynie Azure portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Usługa Azure AD przypisuje unikatową aplikację lub identyfikator klienta do aplikacji. Portal otwiera stronę **Przegląd** aplikacji. Aby dodać możliwości aplikacji, można wybrać inne opcje konfiguracji, w tym znakowanie, certyfikaty i wpisy tajne, uprawnienia interfejsu API i inne.

![Przykład strony przeglądu nowo zarejestrowanej aplikacji](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać dostęp do internetowych interfejsów API, zobacz [Szybki start: Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md)

* Aby dowiedzieć się więcej o uprawnieniach, zobacz [Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](v2-permissions-and-consent.md).

* Aby udostępnić internetowe interfejsy API, zobacz [Szybki start: Konfigurowanie aplikacji w celu udostępnienia interfejsów API sieci](quickstart-configure-app-expose-web-apis.md)Web .

* Aby zarządzać obsługiwanymi kontami, zobacz [Szybki start: Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md).

* Aby utworzyć aplikację i dodać funkcje, zobacz przewodniki Szybki start na [platformie tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

* Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

* Aby dowiedzieć się więcej o wytycznych dotyczących znakowania, których należy używać podczas tworzenia aplikacji, zobacz [Wskazówki dotyczące znakowania aplikacji.](howto-add-branding-in-azure-ad-apps.md)

---
title: Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak usunąć aplikację zarejestrowaną za pomocą platformy tożsamości firmy Microsoft.
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
ms.date: 05/08/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 271fe45082749afc310e8f217e8119d3931c3cac
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413732"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Szybki start: Usunąć aplikację, który został zarejestrowany z platformą Microsoft identity

Deweloperzy w przedsiębiorstwach oraz dostawcy oprogramowania jako usługi (SaaS), którzy zarejestrowali aplikacje za pomocą platformy tożsamości firmy Microsoft, mogą potrzebować usunąć rejestrację aplikacji.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację
* Usuwanie aplikacji utworzonej przez inną organizację

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne jest posiadanie dzierżawy, który został zarejestrowany do niej aplikacje. Aby dowiedzieć się, jak dodawać i rejestrować aplikacje, zobacz [Rejestrowanie aplikacji za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md).

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Usuwanie aplikacji utworzonej przez Ciebie lub Twoją organizację

Aplikacje zarejestrowane przez Ciebie lub Twoją aplikację są reprezentowane przez obiekt aplikacji i obiekt jednostki usługi w dzierżawie. Aby uzyskać więcej informacji, zobacz [Obiekty aplikacji i obiekty jednostki usługi](active-directory-application-objects.md).

### <a name="to-remove-an-application"></a>Usuwanie aplikacji

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
2. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
3. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi, a następnie wybierz **rejestracje aplikacji**. Znajdź i wybierz aplikację, którą chcesz skonfigurować. Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji.
4. Na stronie **Przegląd** wybierz pozycję **Usuń**.
5. Wybierz pozycję **Tak**, aby potwierdzić, że chcesz usunąć aplikację.

   > [!NOTE]
   > Aby usunąć aplikację, musisz być właścicielem aplikacji lub mieć uprawnienia administratora.

## <a name="remove-an-application-authored-by-another-organization"></a>Usuwanie aplikacji utworzonej przez inną organizację

Jeśli wyświetlasz stronę **Rejestracje aplikacji** w kontekście dzierżawy, podzbiór aplikacji wyświetlanych na karcie **Wszystkie aplikacje** pochodzi z innej dzierżawy, a aplikacje z tego podzbioru zostały zarejestrowane w Twojej dzierżawie w ramach procesu udzielania zgody. Dokładniej mówiąc, są reprezentowane tylko przez obiekt główny usługi w dzierżawie, bez odpowiadającego mu obiektu aplikacji. Aby uzyskać więcej informacji na temat różnic między obiektami aplikacji a jednostkami usługi, zobacz [Application and service principal objects in Azure AD (Obiekty aplikacji i obiekty jednostki usługi w usłudze Azure AD)](active-directory-application-objects.md).

Aby usunąć dostęp aplikacji do Twojego katalogu (po wcześniejszym udzieleniu zgody), administrator firmy musi usunąć jej jednostkę usługi. Administrator z uprawnieniami administratora globalnego może usunąć aplikację w witrynie Azure Portal lub za pomocą [poleceń cmdlet programu PowerShell usługi Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat innych powiązanych przewodników Szybki start dotyczących zarządzania aplikacjami:

* [Rejestrowanie aplikacji za pomocą platformy tożsamości firmy Microsoft](quickstart-register-app.md)
* [Konfigurowanie aplikacji klienckiej na potrzeby uzyskiwania dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md)
* [Konfigurowanie aplikacji w celu uwidocznienia internetowych interfejsów API](quickstart-configure-app-expose-web-apis.md)
* [Modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md)

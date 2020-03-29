---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta na Facebooku
titleSuffix: Azure AD B2C
description: Zapewnij rejestrację i zaloguj się do klientów za pomocą kont Na Facebooku w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188277"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta na Facebooku przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Tworzenie aplikacji na Facebooku

Aby użyć konta facebook jako [dostawcy tożsamości](authorization-code-flow.md) w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta na Facebooku, możesz [https://www.facebook.com/](https://www.facebook.com/)zarejestrować się w .

1. Zaloguj się na [Facebooku dla programistów za](https://developers.facebook.com/) pomocą poświadczeń konta na Facebooku.
1. Jeśli jeszcze tego nie zrobiłeś, musisz zarejestrować się jako programista Facebooka. Aby to zrobić, wybierz **pozycję Wprowadzenie** w prawym górnym rogu strony, zaakceptuj zasady Facebooka i wykonaj kroki rejestracji.
1. Wybierz **pozycję Moje aplikacje,** a następnie **utwórz aplikację**.
1. Wprowadź **nazwę wyświetlaną** i prawidłowy **adres e-mail kontaktu**.
1. Wybierz **pozycję Utwórz identyfikator aplikacji**. Może to wymagać zaakceptowania zasad platformy Facebooka i zakończenia kontroli bezpieczeństwa online.
1. Wybierz **pozycję Ustawienia** > **podstawowe**.
1. Wybierz **kategorię**, `Business and Pages`na przykład . Ta wartość jest wymagana przez facebooka, ale nie jest używana dla usługi Azure AD B2C.
1. U dołu strony wybierz pozycję **Dodaj platformę**, a następnie wybierz pozycję **Witryna sieci Web**.
1. W **adresie URL witryny**wprowadź `https://your-tenant-name.b2clogin.com/` zastąpienie `your-tenant-name` nazwą dzierżawy. Wprowadź adres URL adresu URL polityki `http://www.contoso.com` **prywatności**, na przykład . Adres URL zasad jest stroną, którą przechowujesz, aby podać informacje o prywatności dla aplikacji.
1. Wybierz pozycję **Zapisz zmiany**.
1. W górnej części strony skopiuj wartość **identyfikatora aplikacji**.
1. Wybierz **pozycję Pokaż** i skopiuj wartość **klucza tajnego aplikacji**. Oba te elementy umożliwiają skonfigurowanie Facebooka jako dostawcy tożsamości w dzierżawie. **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń.
1. Wybierz znak plus obok **pozycji PRODUKTY**, a następnie wybierz pozycję **Skonfiguruj** w obszarze **Logowanie na Facebooku**.
1. W obszarze **Logowanie na Facebooku**wybierz pozycję **Ustawienia**.
1. W **polu Prawidłowe identyfikatory URI przekierowania OAuth**wprowadź . `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` Zamień `your-tenant-name` na nazwę dzierżawy. Wybierz **pozycję Zapisz zmiany** u dołu strony.
1. Aby udostępnić aplikację Facebook na platformie Azure AD B2C, wybierz selektor stanu w prawym górnym rogu strony i włącz **ją,** aby udostępnić aplikację, a następnie wybierz pozycję **Przełącz tryb**.  W tym momencie stan powinien zmienić się z **Rozwoju** **na Żywo**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurowanie konta na Facebooku jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję **Facebook**.
1. Wprowadź **nazwę**. Na przykład *Facebook*.
1. W przypadku identyfikatora **klienta**wprowadź identyfikator aplikacji Facebooka, który został utworzony wcześniej.
1. W przypadku **klucza tajnego klienta**wprowadź zarejestrowany klucz tajny aplikacji.
1. Wybierz **pozycję Zapisz**.

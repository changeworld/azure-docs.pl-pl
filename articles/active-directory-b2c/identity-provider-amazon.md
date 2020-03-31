---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta Amazon
titleSuffix: Azure AD B2C
description: Zapewnij rejestrację i zaloguj się do klientów za pomocą kont Amazon w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188464"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta Amazon przy użyciu usługi Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Tworzenie aplikacji Amazon

Aby użyć konta Amazon jako [dostawcy tożsamości](authorization-code-flow.md) w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta Amazon, możesz [https://www.amazon.com/](https://www.amazon.com/)zarejestrować się w .

1. Zaloguj się do [Centrum deweloperów Amazon](https://login.amazon.com/) za pomocą poświadczeń konta Amazon.
1. Jeśli jeszcze tego nie zrobiono, kliknij pozycję **Zarejestruj się,** postępuj zgodnie z instrukcjami rejestracji dewelopera i zaakceptuj zasady.
1. Wybierz **zarejestruj nową aplikację**.
1. Wprowadź **adres**URL name , **description**i **privacy notice**, a następnie kliknij przycisk **Zapisz**. Polityka prywatności to strona, którą zarządzasz, która udostępnia użytkownikom informacje o prywatności.
1. W sekcji **Ustawienia sieci Web** skopiuj wartości **identyfikatora klienta**. Wybierz **pozycję Pokaż klucz tajny,** aby uzyskać klucz tajny klienta, a następnie skopiować go. Oba jednych i drugich muszą skonfigurować konto Amazon jako dostawcę tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. W sekcji **Ustawienia sieci Web** wybierz pozycję **Edytuj**, a następnie `https://your-tenant-name.b2clogin.com` wprowadź pozycję Dozwolone stany JavaScript i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **adresy URL dozwolonego zwrotu**. **Allowed JavaScript Origins** Zamień `your-tenant-name` na nazwę dzierżawy. Należy użyć wszystkich małych liter podczas wprowadzania nazwy dzierżawy, nawet jeśli dzierżawca jest zdefiniowany za pomocą wielkich liter w usłudze Azure AD B2C.
1. Kliknij przycisk **Zapisz**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurowanie konta Amazon jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **dostawców tożsamości**, a następnie wybierz **Amazon**.
1. Wprowadź **nazwę**. Na przykład *Amazon*.
1. W przypadku identyfikatora **klienta**wprowadź identyfikator klienta aplikacji Amazon, który został utworzony wcześniej.
1. W przypadku **klucza tajnego Klienta**wprowadź zarejestrowany klucz tajny klienta.
1. Wybierz **pozycję Zapisz**.

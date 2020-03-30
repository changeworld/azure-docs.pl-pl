---
title: Konfigurowanie dostępu usługi Azure Active Directory — usługa Azure Blockchain Service
description: Jak skonfigurować usługę Azure Blockchain Service z dostępem usługi Azure Active Directory
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455842"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Jak skonfigurować dostęp usługi Azure Active Directory dla usługi Azure Blockchain

W tym artykule dowiesz się, jak udzielić dostępu i połączyć się z węzłami usługi Azure Blockchain przy użyciu identyfikatorów użytkowników, grup lub aplikacji usługi Azure Active Directory (Azure AD).

Usługa Azure AD zapewnia oparte na chmurze zarządzanie tożsamościami i umożliwia używanie jednej tożsamości w całym przedsiębiorstwie i uzyskiwanie dostępu do aplikacji na platformie Azure. Usługa Azure Blockchain service jest zintegrowana z usługą Azure AD i oferuje korzyści, takie jak federacja identyfikatorów, logowanie jednokrotne i uwierzytelnianie wieloskładnikowe.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie członka łańcucha bloków przy użyciu witryny Azure portal](create-member.md)

## <a name="grant-access"></a>Udzielanie dostępu

Można udzielić dostępu zarówno na poziomie elementu członkowskiego, jak i na poziomie węzła. Przyznanie praw dostępu na poziomie członkowskim z kolei zapewni dostęp do wszystkich węzłów w ramach elementu członkowskiego.

### <a name="grant-member-level-access"></a>Przyznanie dostępu na poziomie członka

Aby udzielić uprawnień dostępu na poziomie elementu członkowskiego.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do pozycji **Kontrola dostępu (IAM) > Dodaj > Dodaj przypisanie roli**.
1. Wybierz rolę **Dostęp do węzła członkowskiego łańcucha bloków (wersja zapoznawcza)** i dodaj obiekt identyfikatora usługi Azure AD ID, do którego chcesz udzielić dostępu. Obiekt identyfikatora usługi Azure AD ID może być:

    | Obiekt usługi Azure AD | Przykład |
    |-----------------|---------|
    | Użytkownik usługi Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Grupa usługi Azure AD  | `sales@contoso.onmicrosoft.com` |
    | Identyfikator aplikacji  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Dodaj przypisanie roli](./media/configure-aad/add-role-assignment.png)

1. Wybierz **pozycję Zapisz**.

### <a name="grant-node-level-access"></a>Udziel dostępu na poziomie węzła

Dostęp można udzielić na poziomie węzła, przechodząc do zabezpieczeń węzła i klikając nazwę węzła, do którego chcesz udzielić dostępu.

Wybierz rolę Dostęp do węzła członkowskiego łańcucha bloków (wersja zapoznawcza) i dodaj obiekt identyfikatora usługi Azure AD ID, do którego chcesz udzielić dostępu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie węzłów transakcji usługi Azure Blockchain Service](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Łączenie się przy użyciu usługi Azure Blockchain Connector

Pobierz lub sklonuj usługę [Azure Blockchain Connector z usługi GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Postępuj zgodnie z sekcją Szybki start w **readme,** aby utworzyć łącznik z kodu źródłowego.

### <a name="connect-using-an-azure-ad-user-account"></a>Łączenie się przy użyciu konta użytkownika usługi Azure AD

1. Uruchom następujące polecenie, aby uwierzytelnić się przy użyciu konta użytkownika usługi Azure AD. Zastąp \<program\> myAADDirectory domeną usługi Azure AD. Na przykład `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Usługa Azure AD monituje o poświadczenia.
1. Zaloguj się przy tym przy tym przy tym przy tym przyuślesz nazwę użytkownika i hasło.
1. Po pomyślnym uwierzytelnieniu lokalny serwer proxy łączy się z węzłem łańcucha bloków. Teraz można dołączyć klienta Geth z lokalnym punktem końcowym.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Łączenie się przy użyciu identyfikatora aplikacji

Wiele aplikacji uwierzytelnia się przy użyciu usługi Azure AD przy użyciu identyfikatora aplikacji zamiast konta użytkownika usługi Azure AD.

Aby połączyć się z węzłem przy użyciu identyfikatora aplikacji, **zastąp kod aadauthcode** **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametr | Opis |
|-----------|-------------|
| identyfikator dzierżawy | Domena usługi Azure AD, na przykład`yourdomain.onmicrosoft.com`
| identyfikator klienta | Identyfikator klienta zarejestrowanej aplikacji w usłudze Azure AD
| klucz tajny klienta | Klucz tajny klienta zarejestrowanej aplikacji w usłudze Azure AD

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [Jak: Użyj portalu do utworzenia aplikacji i jednostki usługi Azure AD, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Podłączanie urządzenia przenośnego lub przeglądarki tekstowej

W przypadku urządzenia przenośnego lub przeglądarki tekstowej, w której wyświetlanie wyskakujące okienka uwierzytelniania usługi Azure AD nie jest możliwe, usługa Azure AD generuje jednorazowy kod dostępu. Kod można skopiować i kontynuować uwierzytelnianie usługi Azure AD w innym środowisku.

Aby wygenerować kod dostępu, **zastąp kod aadauthcode** **aaddevice**. Zastąp \<program\> myAADDirectory domeną usługi Azure AD. Na przykład `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zabezpieczeń danych w usłudze Azure Blockchain, zobacz [Zabezpieczenia usługi Azure Blockchain Service](data-security.md).
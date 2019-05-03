---
title: Jak skonfigurować dostęp do usługi Azure Active Directory
description: Jak skonfigurować usługę łańcucha bloków platformy Azure za pomocą usługi Azure Active Directory dostępu
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028218"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Jak skonfigurować dostęp do usługi Azure Active Directory

W tym artykule dowiesz się, jak udzielić dostępu i łączyć się z węzłami usługi Azure Service łańcucha bloków przy użyciu identyfikatorów aplikacji, grupy lub użytkownika usługi Azure Active Directory (Azure AD).

Usługi Azure AD zapewnia zarządzanie tożsamościami oparta na chmurze i umożliwia skorzystanie z jednej tożsamości na całego przedsiębiorstwa i dostęp do aplikacji na platformie Azure. Usługa Azure Blockchain jest zintegrowana z usługą Azure AD i zapewnia korzyści, takie jak identyfikator Federacji, pojedynczego logowania jednokrotnego oraz uwierzytelnianie wieloskładnikowe.

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz element członkowski łańcucha bloków w witrynie Azure portal](create-member.md)

## <a name="grant-access"></a>Udzielanie dostępu

Można przyznać dostęp na poziomie węzła i poziom elementu członkowskiego. Udzielanie prawa dostępu na poziomie elementu członkowskiego z kolei spowoduje przyznanie dostępu do wszystkich węzłów w ramach elementu członkowskiego.

### <a name="grant-member-level-access"></a>Udziel dostępu na poziomie elementu członkowskiego

Aby udzielić uprawnień dostępu na poziomie elementu członkowskiego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **kontrola dostępu (IAM) > Dodaj > Dodaj przypisanie roli**.
1. Wybierz **dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza)** roli i Dodaj chcesz udzielić dostępu do obiektu identyfikator usługi Azure AD. Może być usługa Azure AD identyfikator obiektu:

    | Obiekt usługi Azure AD | Przykład |
    |-----------------|---------|
    | Użytkownika usługi Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Grupa usługi Azure AD  | `sales@contoso.onmicrosoft.com` |
    | Identyfikator aplikacji  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Dodaj przypisanie roli](./media/configure-aad/add-role-assignment.png)

1. Wybierz pozycję **Zapisz**.

### <a name="grant-node-level-access"></a>Udzielanie dostępu na poziomie węzła

1. Można przyznać dostęp na poziomie węzła, przechodząc do węzła zabezpieczeń i kliknij nazwę węzła, którego chcesz udzielić dostępu.
1. Wybierz rolę, dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza), a następnie dodaj obiekt identyfikator usługi Azure AD, którą chcesz udzielić dostępu do. 

## <a name="connect-using-azure-blockchain-connector"></a>Nawiązywanie połączenia przy użyciu łącznika usługi Azure Blockchain

Pobierz lub sklonuj [łącznika usługi Azure Blockchain z serwisu GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Postępuj zgodnie z sekcji tego przewodnika Szybki Start **readme** do tworzenia łącznika z kodu źródłowego.

### <a name="connect-using-an-azure-ad-user-account"></a>Nawiązywanie połączenia przy użyciu konta użytkownika usługi Azure AD

1. Uruchom następujące polecenie, aby uwierzytelniać przy użyciu konta użytkownika usługi Azure AD. Zastąp \<myAADDirectory\> z domeną usługi Azure AD. Na przykład `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Usługa Azure AD monituje o podanie poświadczeń.
1. Zaloguj się przy użyciu nazwy użytkownika i hasło.
1. Po pomyślnym uwierzytelnieniu na lokalnym serwerze proxy łączy się z węzła z łańcucha bloków. Można teraz dołączać Geth klienta przy użyciu lokalnego punktu końcowego.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Nawiązywanie połączenia przy użyciu Identyfikatora aplikacji

Wiele aplikacji uwierzytelnianie za pomocą usługi Azure AD za pomocą Identyfikatora aplikacji zamiast konta użytkownika usługi Azure AD.

Aby połączyć się z węzła przy użyciu Identyfikatora aplikacji, należy zastąpić **aadauthcode** z **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametr | Opis |
|-----------|-------------|
| Identyfikator dzierżawy | Domena usługi Azure AD, na przykład `yourdomain.onmicrosoft.com`
| Identyfikator klienta | Identyfikator klienta aplikacji zarejestrowanej w usłudze Azure AD
| client-secret | Klucz tajny klienta aplikacji zarejestrowanej w usłudze Azure AD

Aby uzyskać więcej informacji o tym, jak zarejestrować aplikację w usłudze Azure AD, zobacz [jak: Aby utworzyć aplikację usługi Azure AD i jednostkę usługi, które mogą uzyskiwać dostęp do zasobów, użyj portalu](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Łączenie urządzenia lub tekst przeglądarce dla urządzeń przenośnych

Urządzenie przenośne lub przeglądarki oparte na tekście, gdy nie jest możliwe wyświetlanie wyskakujących uwierzytelniania usługi Azure AD usługa Azure AD generuje jednorazowy kod dostępu. Można skopiować kod dostępu i kontynuować uwierzytelnianie usługi Azure AD w innym środowisku.

Aby wygenerować kod dostępu, należy zastąpić **aadauthcode** z **aaddevice**. Zastąp \<myAADDirectory\> z domeną usługi Azure AD. Na przykład `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat bezpieczeństwa danych w usłudze Azure Service łańcucha bloków zobacz:

> [!div class="nextstepaction"]
> [Zabezpieczenia usługi Azure Service łańcucha bloków](data-security.md)
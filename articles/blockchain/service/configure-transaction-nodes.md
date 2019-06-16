---
title: Konfigurowanie węzłów transakcji usługi Azure Service łańcucha bloków
description: Jak skonfigurować węzły transakcji usługi Azure Service łańcucha bloków
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027963"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurowanie węzłów transakcji usługi Azure Service łańcucha bloków

Do interakcji z usługą Azure Service łańcucha bloków, możesz to zrobić za pośrednictwem nawiązywania połączenia z co najmniej jeden węzeł transakcji w elemencie członkowskim z łańcucha bloków.  W celu interakcji z węzłami transakcji, należy skonfigurować węzły dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie elementu członkowskiego temat aplikacji Azure Blockchain](create-member.md)

## <a name="transaction-node-overview"></a>Przegląd węzła transakcji

Węzły transakcji są używane do wysyłania transakcji łańcucha bloków do usługi Azure Service łańcucha bloków za pośrednictwem publicznego punktu końcowego. Domyślny węzeł transakcji zawiera klucz prywatny konta Ethereum zarejestrowany z łańcucha bloków i jako takie nie można usunąć.

Aby wyświetlić szczegóły węzła transakcji domyślne:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure Service łańcucha bloków. Wybierz **węzłów transakcji**.

    ![Wybierz węzeł transakcji domyślne](./media/configure-transaction-nodes/nodes.png)

    Szczegóły przeglądu obejmują adresy publicznego punktu końcowego i klucz publiczny.

## <a name="create-transaction-node"></a>Utwórz węzeł transakcji

Możesz dodać maksymalnie dziewięć dodatkowych transakcji węzły do elementów członkowskich łańcucha bloków, łącznie z dziesięciu węzłów transakcji. Dodając węzły transakcji, można zwiększyć skalowalność lub rozłożenie obciążenia. Na przykład można mieć punktem końcowym węzła transakcji dla różnych aplikacji klienckich.

Aby dodać węzeł transakcji:

1. W witrynie Azure portal przejdź do elementu członkowskiego usługi Azure Service łańcucha bloków i wybierz **węzłów transakcji > Dodaj**.
1. Określ ustawienia dla nowego węzła transakcji.

    ![Dodaj węzeł transakcji](./media/configure-transaction-nodes/add-node.png)

    | Ustawienie | Opis |
    |---------|-------------|
    | Name (Nazwa) | Nazwa węzła transakcji. Nazwa jest używana do utworzenia adresu DNS dla punktu końcowego węzła transakcji. Na przykład `newnode-myblockchainmember.blockchain.azure.com`. Nie można zmienić nazwy węzła, po jego utworzeniu. |
    | Hasło | Ustaw silne hasło. Użyj hasła do dostępu do punktu końcowego węzła transakcji z uwierzytelnianiem podstawowym.

1. Wybierz pozycję **Utwórz**.

    Aprowizacja nowego węzła transakcji trwa około 10 minut. Transakcja dodatkowe węzły spowodować naliczenie opłat. Aby uzyskać więcej informacji na temat kosztów, zobacz [cennik usługi Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Punkty końcowe

Węzły transakcji ma unikatową nazwę DNS i publicznych punktów końcowych.

Aby wyświetlić szczegóły punktu końcowego węzła transakcji:

1. W witrynie Azure portal przejdź do jednego z węzłów usługi Azure Service łańcucha bloków elementu członkowskiego transakcji, a następnie wybierz pozycję **Przegląd**.

    ![Punkty końcowe](./media/configure-transaction-nodes/endpoints.png)

Punkty końcowe węzła transakcji są bezpieczne i wymagać uwierzytelniania. Możesz połączyć się z punktem końcowym transakcji przy użyciu uwierzytelniania usługi Azure AD, uwierzytelnianie podstawowe dla protokołu HTTPS, a przy użyciu klucza dostępu za pośrednictwem protokołu HTTPS lub Websocket za pośrednictwem protokołu SSL.

### <a name="azure-active-directory-access-control"></a>Kontrola dostępu za pomocą usługi Azure Active Directory

Azure Blockchain transakcji węzła punkty końcowe usługi obsługują uwierzytelnianie usługi Azure Active Directory (Azure AD). Można przyznać jednostce usługi dostęp, grupy i użytkownika usługi Azure AD do punktu końcowego usługi.

Aby udzielić usłudze Azure AD dostęp kontroli do punktu końcowego usługi:

1. W witrynie Azure portal przejdź do elementu członkowskiego usługi Azure Service łańcucha bloków i wybierz **węzłów transakcji > Kontrola dostępu (IAM) > Dodaj > Dodaj przypisanie roli**.
1. Utwórz nowe przypisanie roli dla użytkownika, grupy lub nazwa główna usługi (role aplikacji).

    ![Dodaj rolę zarządzania tożsamościami i Dostępem](./media/configure-transaction-nodes/add-role.png)

    | Ustawienie | Akcja |
    |---------|-------------|
    | Rola | Wybierz **właściciela**, **Współautor**, lub **czytnika**.
    | Przypisz dostęp do | Wybierz **użytkownika, grupy lub jednostki usługi Azure AD**.
    | Wybierz | Wyszukaj użytkownika, grupy lub jednostki usługi, które chcesz dodać.

1. Wybierz **Zapisz** można dodać przypisania roli.

Aby uzyskać więcej informacji na temat kontroli dostępu w usłudze Azure AD, zobacz [zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md)

Aby uzyskać więcej informacji o tym, jak nawiązać połączenie przy użyciu uwierzytelniania usługi Azure AD, zobacz [nawiązania połączenia z węzłem przy użyciu uwierzytelniania usługi AAD](configure-aad.md).

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Uwierzytelnianie podstawowe dla protokołu HTTPS poświadczenia nazwy i hasła użytkownika są przekazywane w nagłówku żądania HTTPS do punktu końcowego.

Szczegóły punktu końcowego uwierzytelniania podstawowego węzła transakcji można wyświetlić w witrynie Azure portal. Przejdź do jednego z węzłów usługi Azure Service łańcucha bloków elementu członkowskiego transakcji, a następnie wybierz pozycję **uwierzytelnianie podstawowe** w ustawieniach.

![Uwierzytelnianie podstawowe](./media/configure-transaction-nodes/basic.png)

Nazwa użytkownika jest nazwa węzła i nie można jej zmienić.

Aby użyć adresu URL, Zastąp \<hasło\> hasłem ustawiana, jeśli węzeł został aprowizowany. Zaktualizuj hasło, wybierając **Resetuj hasło**.

### <a name="access-keys"></a>Klucze dostępu

Aby uzyskać dostęp do uwierzytelniania za pomocą klucza klucz dostępu znajduje się w adresu URL punktu końcowego. Podczas aprowizacji węzła transakcji są generowane dwa klucze dostępu. Klucz dostępu, albo może służyć do uwierzytelniania. Włącz dwa klucze, Zmień i wymiany kluczy.

Można wyświetlić szczegółów klucza dostępu do węzła transakcji i skopiuj adresy punktów końcowych, zawierających klucze dostępu. Przejdź do jednego z węzłów usługi Azure Service łańcucha bloków elementu członkowskiego transakcji, a następnie wybierz pozycję **klucze dostępu** w ustawieniach.

### <a name="firewall-rules"></a>Reguły zapory

Reguły zapory umożliwiają ograniczenie adresów IP, które może podjąć próbę uwierzytelniania w węźle transakcji.  Jeśli nie ma reguł zapory są skonfigurowane dla węzła transakcji, nie jest dostępna każda ze stron.  

Aby wyświetlić reguły zapory na węźle transakcji, przejdź do jednego z węzłów usługi Azure Service łańcucha bloków elementu członkowskiego transakcji, a następnie wybierz pozycję **reguły zapory** w ustawieniach.

Można dodać reguły zapory, wprowadzając nazwę reguły, uruchamianie IP adres i końcowy adresów IP w **reguły zapory** siatki.

![Reguły zapory](./media/configure-transaction-nodes/firewall-rules.png)

Aby włączyć:

* **Pojedynczy adres IP:** Skonfiguruj ten sam adres IP, początkowy i końcowy adres IP.
* **Zakres adresów IP:** Skonfiguruj początkowy i końcowy zakresu adresów IP. Na przykład zakres, zaczynając od 10.221.34.0, a kończąc na 10.221.34.255 umożliwiłby 10.221.34.xxx całej podsieci.
* **Zezwalaj na wszystkie adresy IP:** Skonfiguruj początkowy adres IP na 0.0.0.0 i końcowy adres IP, który 255.255.255.255.

## <a name="connection-strings"></a>Parametry połączeń

Składnia ciągu połączenia dla węzła transakcji towarzyszy podstawowe uwierzytelnianie lub za pomocą kluczy dostępu. Parametry połączenia, w tym klawiszy dostępu za pośrednictwem protokołu HTTPS i WebSockets są dostarczane.

Można wyświetlić parametry połączenia z węzłem transakcji i skopiuj adresy punktów końcowych. Przejdź do jednego z węzłów usługi Azure Service łańcucha bloków elementu członkowskiego transakcji, a następnie wybierz pozycję **parametry połączenia** w ustawieniach.

![Parametry połączeń](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod jest udostępniane na szybkie włączenie nawiązywania połączenia za pośrednictwem sieci Web 3, Nethereum, Web3js i Truffle węzeł transakcji.

Można wyświetlać węzeł transakcji przykładowego połączenia kodu i skopiuj go do korzystania z popularnymi narzędziami deweloperskimi. Przejdź do jednego z węzłów usługi Azure Service łańcucha bloków elementu członkowskiego transakcji, a następnie wybierz pozycję **przykładowy kod** w ustawieniach.

Wybierz kartę sieci Web 3 lub Nethereum, aby wyświetlić przykładowy kod, którego chcesz użyć.

![Przykładowy kod](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie węzłów transakcji przy użyciu wiersza polecenia platformy Azure](manage-cli.md)

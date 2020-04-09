---
title: Konfigurowanie węzłów transakcji usługi Azure Blockchain Service
description: Jak skonfigurować węzły transakcji usługi Azure Blockchain Service
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 8fa18496d0c1aa59beb55569e731967d5ebea427
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876933"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurowanie węzłów transakcji usługi Azure Blockchain Service

Węzły transakcji są używane do wysyłania transakcji łańcucha bloków do usługi Azure Blockchain za pośrednictwem publicznego punktu końcowego. Domyślny węzeł transakcji zawiera klucz prywatny konta Ethereum zarejestrowanego w łańcuchu bloków i jako taki nie może zostać usunięty.

Aby wyświetlić szczegóły węzła transakcji domyślnej:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do członka usługi Azure Blockchain Service. Wybierz **węzeł transakcji**.

    ![Wybierz domyślny węzeł transakcji](./media/configure-transaction-nodes/nodes.png)

    Szczegóły przeglądu obejmują adresy publicznych punktów końcowych i klucz publiczny.

## <a name="create-transaction-node"></a>Tworzenie węzła transakcji

Możesz dodać maksymalnie dziewięć dodatkowych węzłów transakcji do członka łańcucha bloków, w sumie 10 węzłów transakcji. Dodając węzły transakcji, można zwiększyć skalowalność lub rozłożyć obciążenie. Na przykład może mieć punkt końcowy węzła transakcji dla różnych aplikacji klienckich.

Aby dodać węzeł transakcji:

1. W witrynie Azure portal przejdź do członka usługi Azure Blockchain Service i wybierz **węzeł transakcji > Dodaj**.
1. Uzupełnij ustawienia nowego węzła transakcji.

    ![Dodaj węzeł transakcji](./media/configure-transaction-nodes/add-node.png)

    | Ustawienie | Opis |
    |---------|-------------|
    | Nazwa | Nazwa węzła transakcji. Nazwa jest używana do tworzenia adresu DNS dla punktu końcowego węzła transakcji. Na przykład `newnode-myblockchainmember.blockchain.azure.com`. Nie można zmienić nazwy węzła po jej utworzeniu. |
    | Hasło | Ustaw silne hasło. Użyj hasła, aby uzyskać dostęp do punktu końcowego węzła transakcji z uwierzytelnianiem podstawowym.

1. Wybierz **pozycję Utwórz**.

    Inicjowanie obsługi administracyjnej nowego węzła transakcji trwa około 10 minut. Dodatkowe węzły transakcji ponoszą koszty. Aby uzyskać więcej informacji na temat kosztów, zobacz [Ceny platformy Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Punkty końcowe

Węzły transakcji mają unikatową nazwę DNS i publiczne punkty końcowe.

Aby wyświetlić szczegóły punktu końcowego węzła transakcji:

1. W witrynie Azure portal przejdź do jednego z węzłów transakcji członkowskich usługi Azure Blockchain Service i wybierz pozycję **Przegląd**.

    ![Punkty końcowe](./media/configure-transaction-nodes/endpoints.png)

Punkty końcowe węzła transakcji są bezpieczne i wymagają uwierzytelniania. Można połączyć się z punktem końcowym transakcji przy użyciu uwierzytelniania usługi Azure AD, uwierzytelniania podstawowego HTTPS i przy użyciu klucza dostępu za pośrednictwem protokołu HTTPS lub Websocket za pośrednictwem protokołu TLS.

### <a name="azure-active-directory-access-control"></a>Kontrola dostępu usługi Azure Active Directory

Punkty końcowe węzła transakcji usługi Azure Blockchain Service obsługują uwierzytelnianie usługi Azure Active Directory (Azure AD). Można udzielić użytkownikowi, grupie i podmiotowi usługi Azure AD dostępu do punktu końcowego.

Aby udzielić kontroli dostępu usługi Azure AD do punktu końcowego:

1. W witrynie Azure portal przejdź do elementu członkowskiego usługi Azure Blockchain Service i wybierz pozycję **Węzły transakcji > kontroli dostępu (IAM) > Dodaj > Dodaj przypisanie roli.**
1. Utwórz nowe przypisanie roli dla użytkownika, grupy lub jednostki usługi (role aplikacji).

    ![Dodawanie roli IAM](./media/configure-transaction-nodes/add-role.png)

    | Ustawienie | Akcja |
    |---------|-------------|
    | Rola | Wybierz **pozycję Właściciel,** **Współautor**lub **Czytelnik**.
    | Przypisz dostęp do | Wybierz **użytkownika, grupę lub jednostkę usługi Azure AD**.
    | Wybierz pozycję | Wyszukaj użytkownika, grupę lub jednostkę usługi, którą chcesz dodać.

1. Wybierz **pozycję Zapisz,** aby dodać przypisanie roli.

Aby uzyskać więcej informacji na temat kontroli dostępu usługi Azure AD, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md)

Aby uzyskać szczegółowe informacje na temat łączenia się przy użyciu uwierzytelniania usługi Azure AD, zobacz [łączenie się z węzłem przy użyciu uwierzytelniania usługi AAD](configure-aad.md).

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

W przypadku uwierzytelniania podstawowego HTTPS poświadczenia nazwy użytkownika i hasła są przekazywane w nagłówku HTTPS żądania do punktu końcowego.

Szczegóły punktu końcowego uwierzytelniania podstawowego węzła transakcji można wyświetlić w witrynie Azure Portal. Przejdź do jednego z węzłów transakcji członkowskich usługi Azure Blockchain Service i wybierz **pozycję Uwierzytelnianie podstawowe** w ustawieniach.

![Uwierzytelnianie podstawowe](./media/configure-transaction-nodes/basic.png)

Nazwa użytkownika jest nazwą węzła i nie można jej zmienić.

Aby użyć adresu \<URL, zastąp hasło\> hasłem ustawionym podczas inicjowania obsługi administracyjnej węzła. Hasło można zaktualizować, wybierając pozycję **Resetuj hasło**.

### <a name="access-keys"></a>Klawisze dostępu

W przypadku uwierzytelniania klucza dostępu klucz dostępu znajduje się w adresie URL punktu końcowego. Gdy węzeł transakcji jest aprowizowana, dwa klucze dostępu są generowane. Każdy klucz dostępu może służyć do uwierzytelniania. Dwa klawisze umożliwiają zmianę i obrót klawiszy.

Można wyświetlić szczegóły klucza dostępu węzła transakcji i skopiować adresy punktów końcowych, które zawierają klucze dostępu. Przejdź do jednego z węzłów transakcji członkowskich usługi Azure Blockchain Service i wybierz **klawisze dostępu** w ustawieniach.

### <a name="firewall-rules"></a>Reguły zapory

Reguły zapory umożliwiają ograniczenie adresów IP, które mogą próbować uwierzytelnić się w węźle transakcji.  Jeśli dla węzła transakcji nie skonfigurowano żadnych reguł zapory, żadna ze stron nie może uzyskać do niej dostępu.  

Aby wyświetlić reguły zapory węzła transakcji, przejdź do jednego z węzłów transakcji członkowskich usługi Azure Blockchain Service i wybierz **reguły zapory** w ustawieniach.

Reguły zapory można dodawać, wprowadzając nazwę reguły, początkowy adres IP i końcowy adres IP w siatce **reguł zapory.**

![Reguły zapory](./media/configure-transaction-nodes/firewall-rules.png)

Aby włączyć:

* **Pojedynczy adres IP:** Skonfiguruj ten sam adres IP dla początkowych i kończących się adresów IP.
* **Zakres adresów IP:** Skonfiguruj początkowy i końcowy zakres adresów IP. Na przykład zakres rozpoczynający się od 10.221.34.0 i kończący się na 10.221.34.255 umożliwiłoby całą podsieć 10.221.34.xxx.
* **Zezwalaj na wszystkie adresy IP:** Skonfiguruj początkowy adres IP na 0.0.0.0 i końcowy adres IP do 255.255.255.255.

## <a name="connection-strings"></a>Parametry połączeń

Składnia ciągu połączenia dla węzła transakcji jest dostępna dla uwierzytelniania podstawowego lub przy użyciu kluczy dostępu. Dostępne są parametry połączenia, w tym klucze dostępu za pośrednictwem protokołu HTTPS i WebSockets.

Można wyświetlić parametry połączenia węzła transakcji i skopiować adresy punktów końcowych. Przejdź do jednego z węzłów transakcji członkowskich usługi Azure Blockchain Service i wybierz parametry połączenia w **ustawieniach.**

![Parametry połączeń](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod jest dostarczany, aby szybko włączyć łączenie się z węzłem transakcji za pośrednictwem sieci Web3, Nethereum, Web3js i Truffle.

Można wyświetlić przykładowy kod połączenia węzła transakcji i skopiować go do użycia z popularnych narzędzi programistycznych. Przejdź do jednego z węzłów transakcji członkowskich usługi Azure Blockchain Service i wybierz **przykładowy kod** w ustawieniach.

Wybierz kartę Web3, Nethereum, Trufla lub Web3j, aby wyświetlić przykładowy kod, którego chcesz użyć.

![Przykładowy kod](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie węzłów transakcji przy użyciu interfejsu wiersza polecenia platformy Azure](manage-cli.md)

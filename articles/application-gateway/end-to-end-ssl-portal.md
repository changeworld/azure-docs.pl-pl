---
title: Konfigurowanie kompleksowego szyfrowania SSL przy użyciu portalu
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak używać Azure Portal do tworzenia bramy aplikacji z kompleksowym szyfrowaniem SSL.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a878b966266bdd326db35d266bc14b2f81161e92
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075141"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurowanie kompleksowego protokołu SSL przy użyciu Application Gateway z portalem

W tym artykule opisano, jak używać Azure Portal do konfigurowania kompleksowego szyfrowania SSL (SSL) za pośrednictwem jednostki SKU systemu Azure Application Gateway v1.

> [!NOTE]
> Jednostka SKU Application Gateway v2 wymaga zaufanych certyfikatów głównych do włączenia konfiguracji kompleksowej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby skonfigurować kompleksowe połączenie SSL z bramą aplikacji, wymagany jest certyfikat dla bramy. Certyfikaty są również wymagane dla serwerów zaplecza. Certyfikat bramy służy do uzyskiwania klucza symetrycznego w zgodności ze specyfikacją protokołu SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. 

Aby kompleksowe szyfrowanie SSL było możliwe, w bramie aplikacji musi być dozwolony prawidłowy serwer zaplecza. Aby umożliwić ten dostęp, Przekaż certyfikat publiczny serwerów zaplecza, nazywany również certyfikatami uwierzytelniania (v1) lub zaufanymi certyfikatami głównymi (v2), do bramy aplikacji. Dodanie certyfikatu zapewnia, że Brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. Ta konfiguracja zabezpiecza kompleksową komunikację.

Aby dowiedzieć się więcej, zobacz Kończenie [protokołu SSL i kompleksowy protokół SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Tworzenie nowej bramy aplikacji z kompleksowym protokołem SSL

Aby utworzyć nową bramę aplikacji z kompleksowym szyfrowaniem SSL, należy najpierw włączyć Kończenie protokołu SSL podczas tworzenia nowej bramy aplikacji. Ta akcja umożliwia szyfrowanie SSL na potrzeby komunikacji między klientem a bramą aplikacji. Następnie należy umieścić na liście bezpiecznych adresatów certyfikaty dla serwerów zaplecza w ustawieniach protokołu HTTP. Ta konfiguracja umożliwia szyfrowanie SSL komunikacji między bramą aplikacji a serwerami zaplecza. Ma to na celu kompleksowe szyfrowanie SSL.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Włącz Kończenie protokołu SSL podczas tworzenia nowej bramy aplikacji

Aby dowiedzieć się więcej, zobacz [Włączanie zakończenia protokołu SSL podczas tworzenia nowej bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Dodawanie uwierzytelniania/certyfikatów głównych serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Ustawienia http** z menu po lewej stronie. Platforma Azure automatycznie utworzyła domyślne ustawienie HTTP **appGatewayBackendHttpSettings**podczas tworzenia bramy aplikacji. 

3. Wybierz pozycję **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko **certyfikatów uwierzytelniania zaplecza lub zaufanych certyfikatów głównych** .

5. Wybierz pozycję**Utwórz nowy**.

6. W polu **Nazwa** wprowadź odpowiednią nazwę.

7. Wybierz plik certyfikatu w polu **Przekaż certyfikat CER** .

   W przypadku bram aplikacji Standard i WAF (v1) należy przekazać klucz publiczny certyfikatu serwera zaplecza w formacie CER.

   ![Dodawanie certyfikatu](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku Standard_v2 i WAF_v2 bram aplikacji należy przekazać certyfikat główny certyfikatu serwera zaplecza w formacie CER. Jeśli certyfikat zaplecza jest wystawiany przez dobrze znany urząd certyfikacji, można zaznaczyć pole wyboru **Użyj dobrego certyfikatu urzędu certyfikacji** , a następnie nie trzeba przekazywać certyfikatu.

   ![Dodaj zaufany certyfikat główny](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certyfikat główny](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Wybierz pozycję **Zapisz**.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>Włączanie kompleksowego protokołu SSL dla istniejącej bramy aplikacji

Aby skonfigurować istniejącą bramę aplikacji z kompleksowym szyfrowaniem SSL, należy najpierw włączyć zakończenie protokołu SSL w odbiorniku. Ta akcja umożliwia szyfrowanie SSL na potrzeby komunikacji między klientem i bramą aplikacji. Następnie należy umieścić te certyfikaty dla serwerów zaplecza w ustawieniach protokołu HTTP na liście bezpiecznych adresatów. Ta konfiguracja umożliwia szyfrowanie SSL komunikacji między bramą aplikacji a serwerami zaplecza. Ma to na celu kompleksowe szyfrowanie SSL.

Musisz użyć odbiornika z protokołem HTTPS i certyfikatem do włączenia kończenia protokołu SSL. Możesz użyć istniejącego odbiornika, który spełnia te warunki, lub utworzyć nowy odbiornik. Jeśli wybierzesz poprzednią opcję, możesz zignorować następujące polecenie "Włącz zakończenie protokołu SSL w istniejącej bramie aplikacji" i przejść bezpośrednio do sekcji "Dodawanie uwierzytelniania/zaufanych certyfikatów głównych dla serwerów zaplecza".

W przypadku wybrania tej opcji należy zastosować kroki opisane w poniższej procedurze.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>Włączanie zakończenia protokołu SSL w istniejącej bramie aplikacji

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **detektory** z menu po lewej stronie.

3. W zależności od potrzeb wybierz opcję odbiornik **podstawowy** lub **wiele witryn** .

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko dla **certyfikatu** .

5. Przekaż certyfikat PFX, którego zamierzasz użyć do zakończenia protokołu SSL między klientem a bramą aplikacji.

   > [!NOTE]
   > Do celów testowych można użyć certyfikatu z podpisem własnym. Nie jest to jednak zalecane w przypadku obciążeń produkcyjnych, ponieważ są one trudniejsze do zarządzania i nie są całkowicie bezpieczne. Aby uzyskać więcej informacji, zobacz [Tworzenie certyfikatu z](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)podpisem własnym.

6. Dodaj inne wymagane ustawienia dla **odbiornika**, w zależności od wymagań.

7. Wybierz opcję **OK**, aby zapisać.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Dodawanie uwierzytelniania/zaufanych certyfikatów głównych serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Ustawienia http** z menu po lewej stronie. Certyfikaty można umieścić w istniejącym ustawieniu HTTP zaplecza na liście bezpiecznych adresatów lub utworzyć nowe ustawienie protokołu HTTP. (W następnym kroku certyfikat dla domyślnego ustawienia HTTP, **appGatewayBackendHttpSettings**, zostanie dodany do listy bezpiecznych adresatów).

3. Wybierz pozycję **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko **certyfikatów uwierzytelniania zaplecza lub zaufanych certyfikatów głównych** . 

5. Wybierz pozycję**Utwórz nowy**.

6. W polu **Nazwa** wprowadź odpowiednią nazwę.

7. Wybierz plik certyfikatu w polu **Przekaż certyfikat CER** .

   W przypadku bram aplikacji Standard i WAF (v1) należy przekazać klucz publiczny certyfikatu serwera zaplecza w formacie CER.

   ![Dodawanie certyfikatu](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku Standard_v2 i WAF_v2 bram aplikacji należy przekazać certyfikat główny certyfikatu serwera zaplecza w formacie CER. Jeśli certyfikat zaplecza jest wystawiony przez dobrze znany urząd certyfikacji, można zaznaczyć pole wyboru **Użyj certyfikatu dobrze znanego urzędu certyfikacji** , a następnie nie trzeba przekazywać certyfikatu.

   ![Dodaj zaufany certyfikat główny](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

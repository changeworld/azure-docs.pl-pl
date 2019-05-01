---
title: Przewodnik Szybki Start — Konfigurowanie end-to-end szyfrowania SSL przy użyciu usługi Azure Application Gateway — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać witryny Azure portal do tworzenia usługi Azure Application Gateway z szyfrowaniem SSL end-to-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: bd165f81b45e3ae0c121fb8876ed88e68d493195
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64946795"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurowanie certyfikatu SSL end-to-end przy użyciu bramy aplikacji przy użyciu portalu

W tym artykule pokazano, jak korzystać z portalu Azure do skonfigurowania end-to-end szyfrowania SSL z usługą application gateway jednostkę SKU v1.  

> [!NOTE]
> Jednostka SKU v2 bramy aplikacji wymaga zaufanych certyfikatów głównych, włączanie konfiguracji end-to-end. Portal pomocy technicznej dotyczące dodawania zaufanych certyfikatów głównych nie jest jeszcze dostępna. W związku z tym, w przypadku v2 jednostki SKU zobacz [skonfigurować end-to-end protokołu SSL przy użyciu programu PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do skonfigurowania end-to-end SSL z usługą application gateway, certyfikat jest wymagany dla bramy i certyfikaty są wymagane do serwerów zaplecza. Certyfikat bramy jest używany do uzyskania klucza symetrycznego zgodnie z specyfikacją protokołu SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruch wysyłany do bramy. End-to-end szyfrowania SSL wewnętrznej musi być na liście dozwolonych usługi application gateway. Aby to zrobić, należy przekazać certyfikat publiczny serwerów zaplecza, nazywane także certyfikaty uwierzytelniania do usługi application gateway. Dodawanie certyfikatu gwarantuje, że bramy application gateway komunikuje się tylko ze znanych wystąpień zaplecza. W ten sposób dalszej komunikacji end-to-end.

Aby dowiedzieć się więcej, zobacz [SSL zakończenia i end-to-end SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Utwórz nową bramę aplikacji przy użyciu protokołu SSL end-to-end

Aby utworzyć nową bramę aplikacji przy użyciu szyfrowania SSL end-to-end, należy najpierw włączyć kończenia żądań SSL, tworząc nową bramę aplikacji. Spowoduje to włączenie szyfrowania SSL dla komunikacji między bramą klienta i aplikacji. Następnie należy umieścić na liście dozwolonych certyfikatów dla serwerów wewnętrznej bazy danych w ustawieniach protokołu HTTP umożliwia włączenie szyfrowania SSL dla komunikacji między bramą i wewnętrznej bazy danych serwerów aplikacji, wykonywanie end-to-end szyfrowania SSL.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Kończenie żądań protokołu SSL podczas tworzenia nowej bramy aplikacji

Zapoznaj się z tym artykułem, aby zrozumieć, jak [kończenie żądań protokołu SSL podczas tworzenia nowej bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Lista dozwolonych certyfikatów dla serwerów wewnętrznej bazy danych

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz **ustawienia HTTP** menu po lewej stronie. Azure automatycznie tworzone domyślne ustawienie protokołu HTTP **appGatewayBackendHttpSettings**, podczas tworzenia bramy aplikacji. 

3. Wybierz **appGatewayBackendHttpSettings**.

4. W obszarze **protokołu**, wybierz opcję **HTTPS**. Okienko do **certyfikaty uwierzytelniania zaplecza** będą wyświetlane. 

5. W obszarze **certyfikaty uwierzytelniania zaplecza**, wybierz **Utwórz nową**.

6. Wprowadź odpowiednie **nazwa**.

7. Przekaż certyfikat przy użyciu **certyfikatu CER do przekazania** okno.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Certyfikat, opisane w tym kroku należy klucz publiczny certyfikatu pfx na zapleczu. Wyeksportuj certyfikat (nie certyfikat główny) zainstalowane na serwerze zaplecza w formacie oświadczeń, dowód i wnioskowania (CER) i używać go w tym kroku. Ten krok umieszczenie zaplecza za pomocą bramy application gateway.

8. Wybierz pozycję **Zapisz**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Włącz SSL end-to-end dla istniejącej bramie aplikacji

Aby skonfigurować istniejącą bramę aplikacji przy użyciu szyfrowania SSL end-to-end, będziesz musiał pierwszy kończenia żądań SSL Włącz odbiornika. Spowoduje to włączenie szyfrowania SSL dla komunikacji między bramą klienta i aplikacji. Następnie należy umieścić na liście dozwolonych certyfikatów dla serwerów wewnętrznej bazy danych w ustawieniach protokołu HTTP umożliwia włączenie szyfrowania SSL dla komunikacji między bramą i wewnętrznej bazy danych serwerów aplikacji, wykonywanie end-to-end szyfrowania SSL.

Należy użyć odbiornik przy użyciu protokołu HTTPS i certyfikatów umożliwiających kończenia żądań SSL. Nie można zmienić protokół istniejącego odbiornika. Tak albo można Użyj istniejącego odbiornika przy użyciu protokołu HTTPS i certyfikat lub Utwórz nowy odbiornik. Jeśli zdecydujesz się pierwsza, możesz zignorować wymienione poniżej kroki, aby **Włącz kończenie żądań protokołu SSL w istniejącej bramie aplikacji** i bezpośrednie przejście do **dozwolonych certyfikatów dla serwerów wewnętrznej bazy danych** sekcja. Jeśli zdecydujesz się one, wykonaj następujące kroki.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Kończenie żądań protokołu SSL w istniejącej bramie aplikacji

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz **odbiorników** menu po lewej stronie.

3. Wybieranie między **podstawowe** i **obejmujące wiele lokacji** odbiornika zgodnie z wymaganiami.

4. W obszarze **protokołu**, wybierz opcję **HTTPS**. Okienko do **certyfikatu** będą wyświetlane.

5. Przekaż certyfikat PFX, które mają być używane do kończenia żądań SSL od bramą klienta i aplikację.

   > [!NOTE]
   > Do celów testowych można użyć certyfikatu z podpisem własnym. Nie należy używać certyfikatu z podpisem własnym dla obciążeń produkcyjnych. Dowiedz się, jak [Utwórz certyfikat z podpisem własnym](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Dodaj inne wymagane ustawienia **odbiornika** zgodnie z wymaganiami.

7. Wybierz opcję **OK**, aby zapisać.

### <a name="whitelist-certificates-for-backend-servers"></a>Lista dozwolonych certyfikatów dla serwerów wewnętrznej bazy danych

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz **ustawienia HTTP** menu po lewej stronie. Można albo umieścić na liście dozwolonych certyfikaty istniejącego zaplecza HTTP ustawienie lub utworzyć nowe ustawienie protokołu HTTP. W poniższych kroku zostanie dozwolonych certyfikat dla domyślnego ustawienia HTTP **appGatewayBackendHttpSettings**.

3. Wybierz **appGatewayBackendHttpSettings**.

4. W obszarze **protokołu**, wybierz opcję **HTTPS**. Okienko do **certyfikaty uwierzytelniania zaplecza** będą wyświetlane. 

5. W obszarze **certyfikaty uwierzytelniania zaplecza**, wybierz **Utwórz nową**.

6. Wprowadź odpowiednie **nazwa**.

7. Przekaż certyfikat przy użyciu **certyfikatu CER do przekazania** okno.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Certyfikat, opisane w tym kroku należy klucz publiczny certyfikatu pfx na zapleczu. Wyeksportuj certyfikat (nie certyfikat główny) zainstalowane na serwerze zaplecza w formacie oświadczeń, dowód i wnioskowania (CER) i używać go w tym kroku. Ten krok umieszczenie zaplecza za pomocą bramy application gateway.

8. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

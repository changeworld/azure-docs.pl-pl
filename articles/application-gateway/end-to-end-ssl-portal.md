---
title: Konfigurowanie szyfrowania TLS end-to-end przy użyciu portalu
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć bramę aplikacji z kompleksowym szyfrowaniem TLS za pomocą portalu Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133008"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Konfigurowanie kompleksowego protokołu TLS przy użyciu bramy aplikacji z portalem

W tym artykule opisano sposób używania witryny Azure Portal do konfigurowania kompleksowego szyfrowania TLS (Transport Layer Security), znanego wcześniej jako szyfrowanie SSL (Secure Sockets Layer), za pośrednictwem usługi Azure Application Gateway w wersji 1 SKU.

> [!NOTE]
> Jednostka SKU bramy aplikacji w wersji 2 wymaga zaufanych certyfikatów głównych do włączania konfiguracji end-to-end.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby skonfigurować end-to-end TLS z bramą aplikacji, potrzebny jest certyfikat dla bramy. Certyfikaty są również wymagane dla serwerów zaplecza. Certyfikat bramy jest używany do wyprowadzania klucza symetrycznego zgodnie ze specyfikacją protokołu TLS. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. 

W przypadku szyfrowania TLS end-to-end w bramie aplikacji muszą być dozwolone prawe serwery zaplecza. Aby zezwolić na ten dostęp, przekaż do bramy aplikacji publiczny certyfikat serwerów zaplecza, znany również jako certyfikaty uwierzytelniania (wersja 1) lub zaufane certyfikaty główne (wersja 2). Dodanie certyfikatu gwarantuje, że brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. Ta konfiguracja dodatkowo zabezpiecza komunikację end-to-end.

Aby dowiedzieć się więcej, zobacz [Omówienie zakończenia protokołu TLS i zakończenia tls z bramą aplikacji](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Tworzenie nowej bramy aplikacji z kompleksowym tlsem

Aby utworzyć nową bramę aplikacji z kompleksowym szyfrowaniem TLS, musisz najpierw włączyć zakończenie protokołu TLS podczas tworzenia nowej bramy aplikacji. Ta akcja umożliwia szyfrowanie TLS do komunikacji między klientem a bramą aplikacji. Następnie musisz umieścić na liście Bezpiecznych adresatów certyfikaty dla serwerów zaplecza w ustawieniach HTTP. Ta konfiguracja umożliwia szyfrowanie TLS do komunikacji między bramą aplikacji a serwerami zaplecza. To realizuje szyfrowanie TLS end-to-end.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Włączanie zakończenia protokołu TLS podczas tworzenia nowej bramy aplikacji

Aby dowiedzieć się więcej, zobacz [Włączanie zakończenia protokołu TLS podczas tworzenia nowej bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Dodawanie certyfikatów uwierzytelniania/głównych serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz **ustawienia HTTP** z menu po lewej stronie. Podczas tworzenia bramy aplikacji platforma Azure automatycznie utworzyła domyślne ustawienie HTTP, **appGatewayBackendHttpSettings.** 

3. Wybierz **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz **https**. Pojawi się okienko **dla certyfikatów uwierzytelniania wewnętrznej bazy danych lub zaufanych certyfikatów głównych.**

5. Wybierz pozycję**Utwórz nowy**.

6. W polu **Nazwa** wprowadź odpowiednią nazwę.

7. Wybierz plik certyfikatu w polu **Przekaż certyfikat CER.**

   W przypadku bram aplikacji Standard i WAF (w wersji 1) należy przekazać klucz publiczny certyfikatu serwera zaplecza w formacie cer.

   ![Dodawanie certyfikatu](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku Standard_v2 i WAF_v2 bram aplikacji należy przekazać certyfikat główny certyfikatu serwera zaplecza w formacie cer. Jeśli certyfikat zaplecza jest wystawiany przez dobrze znany urząd certyfikacji, można zaznaczyć pole wyboru **Użyj dobrze znanego certyfikatu urzędu certyfikacji,** a następnie nie trzeba przekazywać certyfikatu.

   ![Dodawanie zaufanego certyfikatu głównego](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certyfikat główny](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Wybierz **pozycję Zapisz**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Włączanie kompleksowego protokołu TLS dla istniejącej bramy aplikacji

Aby skonfigurować istniejącą bramę aplikacji z szyfrowaniem TLS end-to-end, należy najpierw włączyć zakończenie protokołu TLS w odbiorniku. Ta akcja umożliwia szyfrowanie TLS do komunikacji między klientem a bramą aplikacji. Następnie umieść te certyfikaty dla serwerów zaplecza w ustawieniach HTTP na liście Bezpieczni adresaci. Ta konfiguracja umożliwia szyfrowanie TLS do komunikacji między bramą aplikacji a serwerami zaplecza. To realizuje szyfrowanie TLS end-to-end.

Musisz użyć odbiornika z protokołem HTTPS i certyfikatem umożliwiającym zakończenie protokołu TLS. Można użyć istniejącego odbiornika, który spełnia te warunki lub utworzyć nowy odbiornik. Jeśli wybierzesz poprzednią opcję, możesz zignorować następującą sekcję "Włącz zakończenie protokołu TLS w istniejącej bramie aplikacji" i przejść bezpośrednio do sekcji "Dodaj uwierzytelnianie/zaufane certyfikaty główne dla serwerów wewnętrznej".

Jeśli wybierzesz tę drugą opcję, zastosuj kroki opisane w poniższej procedurze.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Włączanie zakończenia protokołu TLS w istniejącej bramie aplikacji

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz **pozycję Detektory** z menu po lewej stronie.

3. Wybierz odbiornik **podstawowy** lub **wielomiejscowy** w zależności od wymagań.

4. W obszarze **Protokół**wybierz **https**. Pojawi się okienko **certyfikatu.**

5. Przekaż certyfikat PFX, którego zamierzasz użyć do zakończenia protokołu TLS między klientem a bramą aplikacji.

   > [!NOTE]
   > Do celów testowych można użyć certyfikatu z podpisem własnym. Jednak nie jest to zalecane dla obciążeń produkcyjnych, ponieważ są one trudniejsze do zarządzania i nie są całkowicie bezpieczne. Aby uzyskać więcej informacji, zobacz [tworzenie certyfikatu z podpisem własnym](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Dodaj inne wymagane ustawienia dla **odbiornika**, w zależności od wymagań.

7. Wybierz opcję **OK**, aby zapisać.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Dodawanie certyfikatów uwierzytelniania/zaufanych serwerów głównych

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz **ustawienia HTTP** z menu po lewej stronie. Certyfikaty można umieścić w istniejącym zapleczu HTTP na liście Bezpieczni adresaci lub utworzyć nowe ustawienie HTTP. (W następnym kroku certyfikat domyślnego ustawienia HTTP, **appGatewayBackendHttpSettings,** zostanie dodany do listy Bezpiecznych adresatów).

3. Wybierz **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz **https**. Pojawi się okienko **dla certyfikatów uwierzytelniania wewnętrznej bazy danych lub zaufanych certyfikatów głównych.** 

5. Wybierz pozycję**Utwórz nowy**.

6. W polu **Nazwa** wprowadź odpowiednią nazwę.

7. Wybierz plik certyfikatu w polu **Przekaż certyfikat CER.**

   W przypadku bram aplikacji Standard i WAF (w wersji 1) należy przekazać klucz publiczny certyfikatu serwera zaplecza w formacie cer.

   ![Dodawanie certyfikatu](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku Standard_v2 i WAF_v2 bram aplikacji należy przekazać certyfikat główny certyfikatu serwera zaplecza w formacie cer. Jeśli certyfikat zaplecza jest wystawiany przez dobrze znany urząd certyfikacji, można zaznaczyć pole wyboru **Użyj dobrze znanego certyfikatu urzędu certyfikacji,** a następnie nie trzeba przekazywać certyfikatu.

   ![Dodawanie zaufanego certyfikatu głównego](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Wybierz **pozycję Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

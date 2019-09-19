---
title: Szybki Start — Konfigurowanie kompleksowego szyfrowania SSL przy użyciu usługi Azure Application Gateway — Azure Portal | Microsoft Docs
description: Dowiedz się, jak za pomocą Azure Portal utworzyć Application Gateway platformy Azure z kompleksowym szyfrowaniem SSL.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097180"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Konfigurowanie kompleksowego protokołu SSL przy użyciu Application Gateway z portalem

W tym artykule pokazano, jak za pomocą Azure Portal skonfigurować kompleksowe szyfrowanie SSL przy użyciu jednostki SKU bramy aplikacji w wersji 1.  

> [!NOTE]
> Jednostka SKU Application Gateway v2 wymaga zaufanych certyfikatów głównych do włączenia konfiguracji kompleksowej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby skonfigurować kompleksowe połączenie SSL z Application Gateway, wymagany jest certyfikat dla bramy, a certyfikaty są wymagane dla serwerów zaplecza. Certyfikat bramy służy do wyprowadzania klucza symetrycznego zgodnie ze specyfikacją protokołu SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Aby kompleksowe szyfrowanie SSL było możliwe, w bramie aplikacji musi być dozwolony prawidłowy serwer zaplecza. W tym celu Przekaż certyfikat publiczny serwerów zaplecza, nazywany również certyfikatami uwierzytelniania (v1) lub zaufanymi certyfikatami głównymi (v2), do Application Gateway. Dodanie certyfikatu zapewnia, że Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza. Pozwala to na zapewnienie kompleksowej komunikacji.

Aby dowiedzieć się więcej, zobacz Kończenie [protokołu SSL i kompleksowy protokół SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Tworzenie nowej bramy aplikacji z kompleksowym protokołem SSL

Aby utworzyć nową bramę aplikacji z kompleksowym szyfrowaniem SSL, należy najpierw włączyć Kończenie protokołu SSL podczas tworzenia nowej bramy aplikacji. Spowoduje to włączenie szyfrowania SSL na potrzeby komunikacji między klientem a bramą aplikacji. Następnie należy dozwolonych certyfikaty serwerów zaplecza w ustawieniach protokołu HTTP, aby umożliwić szyfrowanie SSL komunikacji między bramą aplikacji a serwerami zaplecza, co zapewnia kompleksowe szyfrowanie SSL.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Włącz Kończenie protokołu SSL podczas tworzenia nowej bramy aplikacji

Zapoznaj się z tym artykułem, aby dowiedzieć się, jak [włączyć Kończenie protokołu SSL podczas tworzenia nowej bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>Dodaj uwierzytelnianie/certyfikat główny serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Ustawienia http** z menu po lewej stronie. Platforma Azure automatycznie utworzyła domyślne ustawienie HTTP **appGatewayBackendHttpSettings**podczas tworzenia bramy aplikacji. 

3. Wybierz pozycję **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko **certyfikatów uwierzytelniania zaplecza lub zaufanych certyfikatów głównych** . 

5. Wybierz pozycję **Utwórz nowy**.

6. Wprowadź odpowiednią **nazwę**.

7. Wybierz plik certyfikatu przy użyciu pola **Przekaż certyfikat CER** .

   W przypadku bram aplikacji Standard i WAF (v1) należy przekazać klucz publiczny certyfikatu serwera wewnętrznej bazy danych w formacie CER.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku bram aplikacji Standard_v2 i WAF_v2 należy przekazać **certyfikat główny** certyfikatu serwera wewnętrznej bazy danych w formacie CER. Jeśli certyfikat zaplecza został wystawiony przez dobrze znany urząd certyfikacji, możesz zaznaczyć pole wyboru Użyj certyfikatu dobrze znanego urzędu certyfikacji i nie ma potrzeby przekazywania certyfikatu.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Wybierz pozycję **Zapisz**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Włączanie kompleksowego protokołu SSL dla istniejącej bramy aplikacji

Aby skonfigurować istniejącą bramę aplikacji z kompleksowym szyfrowaniem SSL, należy najpierw włączyć zakończenie protokołu SSL w odbiorniku. Spowoduje to włączenie szyfrowania SSL na potrzeby komunikacji między klientem a bramą aplikacji. Następnie należy dozwolonych certyfikaty serwerów zaplecza w ustawieniach protokołu HTTP, aby umożliwić szyfrowanie SSL komunikacji między bramą aplikacji a serwerami zaplecza, co zapewnia kompleksowe szyfrowanie SSL.

Musisz użyć odbiornika z protokołem HTTPS i certyfikatem, aby włączyć Kończenie protokołu SSL. Z tego względu można wybrać opcję użycia istniejącego odbiornika z protokołem HTTPS i certyfikatem lub utworzyć nowy odbiornik. W przypadku wybrania pierwszego z nich można zignorować poniższe kroki, aby **włączyć Kończenie protokołu SSL w istniejącej bramie aplikacji** i bezpośrednio przenieść je do sekcji **Dodawanie uwierzytelniania/zaufanych certyfikatów głównych dla serwerów zaplecza** . W przypadku wybrania tej opcji należy wykonać następujące czynności.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Włącz zakończenie protokołu SSL w istniejącej bramie aplikacji

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję detektory z menu po lewej stronie.

3. Wybierz odbiornik **podstawowy** i **wiele witryn** zgodnie z wymaganiami.

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko dla **certyfikatu** .

5. Przekaż certyfikat PFX, który ma zostać użyty do zakończenia protokołu SSL między bramą klienta i aplikacji.

   > [!NOTE]
   > Do celów testowych można użyć certyfikatu z podpisem własnym. ale nie jest to zalecane w przypadku obciążeń produkcyjnych, ponieważ są one trudniejsze do zarządzania i nie są całkowicie bezpieczne. Dowiedz się, jak [utworzyć certyfikat z](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)podpisem własnym.

6. Dodaj inne wymagane ustawienia dla **odbiornika** zgodnie z wymaganiami.

7. Wybierz opcję **OK**, aby zapisać.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Dodawanie uwierzytelniania/zaufanych certyfikatów głównych serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Ustawienia http** z menu po lewej stronie. Można dozwolonych certyfikaty w istniejącym ustawieniu HTTP zaplecza lub utworzyć nowe ustawienie HTTP. W poniższym kroku dozwolonychmy certyfikat dla domyślnego ustawienia HTTP, **appGatewayBackendHttpSettings**.

3. Wybierz pozycję **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko **certyfikatów uwierzytelniania zaplecza lub zaufanych certyfikatów głównych** . 

5. Wybierz pozycję **Utwórz nowy**.

6. Wprowadź odpowiednią **nazwę**.

7. Wybierz plik certyfikatu przy użyciu pola **Przekaż certyfikat CER** .

   W przypadku bram aplikacji Standard i WAF (v1) należy przekazać klucz publiczny certyfikatu serwera wewnętrznej bazy danych w formacie CER.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku bram aplikacji Standard_v2 i WAF_v2 należy przekazać **certyfikat główny** certyfikatu serwera wewnętrznej bazy danych w formacie CER. Jeśli certyfikat zaplecza został wystawiony przez dobrze znany urząd certyfikacji, możesz zaznaczyć pole wyboru Użyj certyfikatu dobrze znanego urzędu certyfikacji i nie ma potrzeby przekazywania certyfikatu.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

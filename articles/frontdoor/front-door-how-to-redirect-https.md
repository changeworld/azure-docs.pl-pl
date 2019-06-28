---
title: Utwórz drzwiami frontowymi za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS przy użyciu witryny Azure portal
description: Dowiedz się, jak utworzyć drzwiami frontowymi przekierowanego ruchu z protokołu HTTP do HTTPS przy użyciu witryny Azure portal.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: a07b19c49630cc925e719aaa1d46476a1edc58f5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333157"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Utwórz drzwiami frontowymi za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS przy użyciu witryny Azure portal

Można użyć witryny Azure portal, aby utworzyć [drzwiami frontowymi](front-door-overview.md) przy użyciu certyfikatu dla kończenia żądań SSL. Reguła routingu jest używana do przekierowywania ruchu HTTP do HTTPS.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz drzwiami frontowymi z istniejącym zasobem aplikacji sieci Web
> * Dodaj domenę niestandardową przy użyciu certyfikatu SSL 
> * Konfigurowanie przekierowania protokołu HTTPS w domenie niestandardowej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Utwórz drzwiami frontowymi z istniejącym zasobem aplikacji sieci Web

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
3. Wyszukaj **drzwiami frontowymi** używając przycisku Wyszukaj paska, a po znalezieniu typ zasobu, kliknij przycisk **Utwórz**.
4. Wybierz subskrypcję, a następnie użyj istniejącej grupy zasobów lub Utwórz nową. Pamiętaj, że lokalizacja zadawane w interfejsie użytkownika znajduje się tylko grupy zasobów. Konfigurację drzwiami frontowymi zostanie wdrożony we wszystkich [lokalizacje POP usługi Azure drzwiami frontowymi](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Konfigurowanie podstawy dla nowego drzwi](./media/front-door-url-redirect/front-door-create-basics.png)

5. Kliknij przycisk **dalej** wprowadzić na karcie Konfiguracja. Konfiguracja drzwiami frontowymi odbywa się w trzech krokach — Dodawanie domyślnego hosta serwera sieci Web, dodawanie zaplecza w puli zaplecza i następnie utworzenie reguły routingu, aby zamapować zachowania routingu dla hosta serwera sieci Web. 

     ![Projektanta konfiguracji drzwi](./media/front-door-url-redirect/front-door-designer.png)

6. Kliknij przycisk " **+** " ikonę na _hosty frontonu_ w celu utworzenia hosta serwera sieci Web wprowadź globalnie unikatową nazwę domyślnego hosta serwera sieci Web dla usługi drzwiami frontowymi (`\<**name**\>.azurefd.net`). Kliknij przycisk **Dodaj** można przejść do następnego kroku.

     ![Dodawanie hosta serwera sieci Web](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Kliknij przycisk " **+** " ikonę na _pule zaplecza_ do utworzenia puli zaplecza. Podaj nazwę dla puli zaplecza, a następnie kliknij przycisk "**Dodawanie zaplecza**".
8. Wybierz typ hosta wewnętrznej bazy danych jako _usługi App service_. Wybierz subskrypcję, w którym jest hostowana aplikacja sieci web, a następnie wybierz aplikację sieci web z listy rozwijanej dla **wewnętrznej bazy danych, nazwy hosta**.
9. Kliknij przycisk **Dodaj** zapisać wewnętrznej bazy danych, a następnie kliknij przycisk **Dodaj** ponownie, aby zapisać konfiguracji puli zaplecza.   ![Dodawanie zaplecza w puli zaplecza](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Kliknij przycisk " **+** " ikonę na _reguły routingu_ tworzona jest trasa. Podaj nazwę dla danej trasy powiedzieć "HttpToHttpsRedirect", a następnie ustaw _zaakceptowane protokołów_ pole **"Tylko HTTP"** . Upewnij się, że odpowiednie _hosta frontonu_ jest zaznaczone.  
11. Na _szczegóły trasy_ sekcji, ustaw _typ trasy_ do **przekierowania**, upewnij się, że _przekierowania typu_ jest ustawiona na  **Odnalezione (302)** i _przekierowania protokołu_ ustawiono **tylko HTTPS**. 
12. Kliknij przycisk Dodaj, aby zapisać regułę routingu dla protokołu HTTP do przekierowania protokołu HTTPS.
     ![Dodaj HTTP do tras przekierowania protokołu HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Dodaj inną regułę routingu do obsługi ruchu HTTPS. Kliknij przycisk " **+** " Zaloguj się _reguły routingu_ i podaj nazwę dla danej trasy powiedzieć "DefaultForwardingRoute", a następnie ustaw _zaakceptowane protokołów_ pola Aby **"Tylko HTTPS"** . Upewnij się, że odpowiednie _hosta frontonu_ jest zaznaczone.
14. W sekcji szczegółów trasy ustawić _typ trasy_ do **do przodu**, upewnij się, że wybrano puli zaplecza w prawo i _przekazywania protokołu_ jest ustawiona na  **Tylko HTTPS**. 
15. Kliknij przycisk Dodaj, aby zapisać regułę routingu do przekazywania żądań.
     ![Dodaj trasę do przodu dla ruchu HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Kliknij przycisk **przeglądu + Utwórz** i następnie **Utwórz**, aby utworzyć własny profil wejściu. Przejdź do zasobu, po utworzeniu.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Dodawanie domeny niestandardowej do sieci drzwiami frontowymi i Włącz protokół HTTPS
Poniższe kroki pokazują, jak dodawanie domeny niestandardowej w istniejącym zasobie drzwiami frontowymi i włączysz HTTP do przekierowania protokołu HTTPS na nim. 

### <a name="add-a-custom-domain"></a>Dodawanie domeny niestandardowej

W tym przykładzie Dodaj rekord CNAME dla `www` domeny podrzędnej (na przykład `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Dodaj rekord CNAME, aby zmapować poddomenę na Twoje drzwiami frontowymi domyślnego serwera sieci Web hosta (`<name>.azurefd.net`, gdzie `<name>` jest nazwą profilu drzwiami frontowymi).

Aby uzyskać `www.contoso.com` domeny, na przykład Dodaj rekord CNAME, który zmapuje nazwę `www` do `<name>.azurefd.net`.

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Rekordu CNAME domeny niestandardowej do drzwi](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Dodawanie domeny niestandardowej na Twoje drzwi

1. Na karcie projektanta drzwiami frontowymi kliknij pozycję "a +" ikonę na hostach serwera sieci Web sekcji Dodaj domenę niestandardową. 
2. Wprowadź w pełni kwalifikowaną niestandardową nazwę DNS w polu Nazwa niestandardowego hosta przykład `www.contosonews.com`. 
3. Po zweryfikowaniu mapowanie CNAME z domeny do usługi drzwiami frontowymi kliknij **Dodaj** do dodawania domeny niestandardowej.
4. Kliknij przycisk **Zapisz** przesłanie zmian.

![Menu domen niestandardowych](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Włączanie obsługi protokołu HTTPS w domenie niestandardowej

1. Kliknij w domenie niestandardowej, która została dodana, a także w sekcji **protokół HTTPS domen niestandardowych**, Zmień stan na **włączone**.
2. Możesz pozostawić **zarządzania typ certyfikatu** równa _drzwiami frontowymi zarządzane_ bezpłatne certyfikatu utrzymywane, zarządzane i autorotated przy wejściu. Można także użyć własnego niestandardowego certyfikatu SSL, które są przechowywane w usłudze Azure Key Vault. Ten samouczek zakłada użycie drzwiami frontowymi certyfikat zarządzany.
![Włączanie protokołu HTTPS dla domeny niestandardowej](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Kliknij pozycję **aktualizacji** Zapisz wybór, a następnie kliknij przycisk **Zapisz**.
4. Kliknij przycisk **Odśwież** za kilka minut i kliknij przycisk w domenie niestandardowej, ponownie, aby wyświetlić postęp aprowizacja obsługi certyfikatów. 

> [!WARNING]
> Włączanie protokołu HTTPS dla domeny niestandardowej może potrwać kilka minut i również jest zależny od weryfikacji własności domeny, jeśli rekord CNAME nie jest bezpośrednio zamapowany na hoście drzwiami frontowymi `<name>.azurefd.net`. Dowiedz się więcej o [jak włączyć protokół HTTPS dla domeny niestandardowej](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Skonfiguruj reguły routingu dla domeny niestandardowej

1. Kliknij utworzoną wcześniej regułę routingu przekierowania.
2. Kliknij listę rozwijaną dla hostów serwera sieci Web i wybierz domenę niestandardową, aby zastosować tę trasę dla swojej domeny, jak również.
3. Kliknij przycisk **Aktualizuj**.
4. Oznacza to, czy tej samej operacji dla innych regułę routingu oraz dla Twojego trasa przekazywania dalej dodać domenę niestandardową.
5. Kliknij przycisk **Zapisz** Aby przesłać wprowadzone zmiany.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
- Dowiedz się więcej o [przekierowywania adresu URL na wejściu](front-door-url-redirect.md).

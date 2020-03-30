---
title: Tworzenie drzwi ekscesji z http do HTTPS za pomocą portalu Azure
description: Dowiedz się, jak utworzyć drzwi frontowe z przekierowanym ruchem z HTTP do HTTPS za pomocą witryny Azure portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: b7385ef27cd17705f2c86b6f57d4780511b6935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246861"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Tworzenie drzwi ekscesji z http do HTTPS za pomocą portalu Azure

Za pomocą witryny Azure Portal można utworzyć [drzwi frontowe](front-door-overview.md) z certyfikatem zakończenia SSL. Reguła routingu służy do przekierowywania ruchu HTTP do protokołu HTTPS.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie drzwi ekwigowych z istniejącym zasobem aplikacji sieci Web
> * Dodawanie domeny niestandardowej z certyfikatem SSL 
> * Konfigurowanie przekierowania HTTPS w domenie niestandardowej

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Tworzenie drzwi ekwigowych z istniejącym zasobem aplikacji sieci Web

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
3. Wyszukaj **drzwi przednie** za pomocą paska wyszukiwania i po znalezieniu typu zasobu kliknij przycisk **Utwórz**.
4. Wybierz subskrypcję, a następnie użyj istniejącej grupy zasobów lub utwórz nową. Uwaga: lokalizacja żądana w interfejsie użytkownika jest tylko dla grupy zasobów. Konfiguracja drzwi ami frontowymi zostanie wdrożona we wszystkich [lokalizacjach pop usługi Azure Front Door.](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)

    ![Konfigurowanie podstaw dla nowych drzwi przednich](./media/front-door-url-redirect/front-door-create-basics.png)

5. Kliknij **przycisk Dalej,** aby wprowadzić kartę konfiguracji. Konfiguracja dla drzwi ami frontowymi odbywa się w trzech krokach — dodawanie domyślnego hosta frontendu, dodawanie zaplecza w puli wewnętrznej bazy danych, a następnie tworzenie reguł routingu w celu mapowania zachowania routingu dla hosta frontoneku. 

     ![Projektant konfiguracji drzwi wejściowych](./media/front-door-url-redirect/front-door-designer.png)

6. Kliknij ikonę '**+**' na _hostach frontendu,_ aby utworzyć hosta frontendu, wprowadź`\<**name**\>.azurefd.net`globalnie unikatową nazwę domyślnego hosta frontendu dla drzwi frontowych ( ). Kliknij **przycisk Dodaj,** aby przejść do następnego kroku.

     ![Dodawanie hosta frontendu](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Kliknij ikonę '**+**' w _pulach wewnętrznej bazy danych,_ aby utworzyć pulę zaplecza. Podaj nazwę puli wewnętrznej bazy danych, a następnie kliknij przycisk '**Dodaj wewnętrznej bazy danych**".
8. Wybierz typ hosta wewnętrznej bazy jako _usługę aplikacji_. Wybierz subskrypcję, w której jest hostowana aplikacja internetowa, a następnie wybierz konkretną aplikację internetową z listy rozwijanej **dla nazwy hosta wewnętrznej bazy danych**.
9. Kliknij **przycisk Dodaj,** aby zapisać wewnętrznej bazy danych i kliknij przycisk **Dodaj** ponownie, aby zapisać config puli wewnętrznej bazy danych.   ![Dodawanie wewnętrznej bazy danych w puli wewnętrznej bazy danych](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Kliknij ikonę '**+** w _regułach routingu,_ aby utworzyć trasę. Podaj nazwę trasy, powiedz "HttpToHttpsRedirect", a następnie ustaw pole _Akceptowane protokoły_ na **"Tylko HTTP".** Upewnij się, że wybrany jest odpowiedni _host frontendu._  
11. W sekcji _Szczegóły trasy_ ustaw _typ trasy_ na **Przekierowanie**, upewnij się, że _typ przekierowania_ jest ustawiony na **Znaleziono (302),** a _protokół Przekierowanie_ jest ustawiony tylko na **HTTPS**. 
12. Kliknij przycisk Dodaj, aby zapisać regułę routingu dla przekierowania HTTP do HTTPS.
     ![Dodawanie trasy przekierowania HTTP do HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Dodaj inną regułę routingu do obsługi ruchu HTTPS. Kliknij znak**+**' ' na _regułach routingu_ i podaj nazwę trasy, powiedz "DefaultForwardingRoute", a następnie ustaw pole _Akceptowane protokoły_ na **"Tylko HTTPS".** Upewnij się, że wybrany jest odpowiedni _host frontendu._
14. W sekcji Szczegóły trasy ustaw _typ trasy_ na **Prześlij**dalej, upewnij się, że wybrana jest prawa pula wewnętrznej bazy danych, a _protokół przesyłania dalej_ jest ustawiony tylko na **HTTPS**. 
15. Kliknij przycisk Dodaj, aby zapisać regułę routingu do przekazywania żądań.
     ![Dodawanie trasy do przodu dla ruchu HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Kliknij **pozycję Recenzja + utwórz,** a następnie **utwórz**, aby utworzyć profil drzwi frontowych. Przejdź do zasobu po utworzeniu.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Dodawanie domeny niestandardowej do drzwi nych i włączanie na niej protokołu HTTPS
Poniższe kroki pokazują, jak można dodać domenę niestandardową do istniejącego zasobu drzwi frontowych, a następnie włączyć przekierowanie HTTP do HTTPS na nim. 

### <a name="add-a-custom-domain"></a>Dodawanie domeny niestandardowej

W tym przykładzie należy dodać rekord `www` CNAME dla poddomeny (na przykład `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Dodaj rekord CNAME, aby zamapować poddomenę do domyślnego hosta frontendu drzwi frontowych (`<name>.azurefd.net`, gdzie `<name>` jest nazwa profilu drzwi frontowych).

Na `www.contoso.com` przykład w przypadku domeny dodaj rekord CNAME, `www` `<name>.azurefd.net`który mapuje nazwę na .

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Domena niestandardowa CNAME do drzwi frontowych](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Wbudowana domena niestandardowa w drzwiach frontowych

1. Na karcie Projektant drzwi frontowych kliknij ikonę "+" w sekcji Hosty frontu, aby dodać nową domenę niestandardową. 
2. Wprowadź w pełni kwalifikowaną niestandardową nazwę DNS `www.contosonews.com`w polu nazwa hosta niestandardowego, na przykład . 
3. Po sprawdzeniu poprawności mapowania CNAME z domeny do drzwi frontowych kliknij przycisk **Dodaj,** aby dodać domenę niestandardową.
4. Kliknij **przycisk Zapisz,** aby przesłać zmiany.

![Menu domen niestandardowych](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Włączanie protokołu HTTPS w domenie niestandardowej

1. Kliknij domenę niestandardową, która została dodana i w sekcji **Domena niestandardowa HTTPS**zmień stan na **Włączone**.
2. **Typ zarządzania certyfikatami** można pozostawić ustawiony na _Drzwi są zarządzane_ przez bezpłatne certyfikaty obsługiwane, zarządzane i autoryzowane przez drzwi frontowe. Można również użyć własnego niestandardowego certyfikatu SSL przechowywanego w usłudze Azure Key Vault. W tym samouczku przyjęto założenie, że użycie certyfikatu zarządzanego przez drzwiami frontowymi.
![Włączanie protokołu HTTPS dla domeny niestandardowej](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Kliknij **przycisk Aktualizuj,** aby zapisać zaznaczenie, a następnie kliknij przycisk **Zapisz**.
4. Kliknij **przycisk Odśwież** po kilku minutach, a następnie kliknij ponownie domenę niestandardową, aby zobaczyć postęp inicjowania obsługi administracyjnej certyfikatów. 

> [!WARNING]
> Włączenie protokołu HTTPS dla domeny niestandardowej może potrwać kilka minut, a także zależy od weryfikacji własności `<name>.azurefd.net`domeny, jeśli CNAME nie jest bezpośrednio mapowane na hosta drzwi frontowych. Dowiedz się więcej o [włączaniu protokołu HTTPS dla domeny niestandardowej](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurowanie reguł routingu dla domeny niestandardowej

1. Kliknij regułę routingu przekierowania utworzoną wcześniej.
2. Kliknij na rozwijaną dla hostów frontu i wybierz domenę niestandardową, aby zastosować tę trasę również dla domeny.
3. Kliknij przycisk **Update** (Aktualizuj).
4. Wykonaj tę samą operację dla innej reguły routingu, jak również, to znaczy, dla trasy przesyłania dalej, aby dodać domenę niestandardową.
5. Kliknij **przycisk Zapisz,** aby przesłać zmiany.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
- Dowiedz się więcej o [przekierowaniu adresu URL na drzwiach frontowych](front-door-url-redirect.md).

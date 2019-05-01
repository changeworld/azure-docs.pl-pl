---
title: Azure SaaS oferty techniczne konfiguracji aplikacji | Portal Azure Marketplace
description: Skonfiguruj informacje techniczne dla oferty aplikacji SaaS w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 46dcf4aeb7ddb67028eb23dde9236f2b7709f86d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941725"
---
# <a name="saas-application-technical-info-tab"></a>Karta informacje techniczne aplikacji SaaS

Karta informacje techniczne zawiera formularz konfiguracji Technical Preview. Ten formularz Wybierz typ aplikacji SaaS (aplikacja), tworzenia i konfigurowania, jak aplikacja są przekazywane klientom.

![Formularz konfiguracji Technical Preview](./media/saas-techinfo-techconfig.png)


## <a name="technical-configuration-form"></a>Formularz konfiguracji Technical Preview

Ten formularz zawiera pola 2: Produkt i wezwanie do działania.


### <a name="product-field"></a>Pole produkt

Dla obu następujących sklepów, można podać aplikacji SaaS:
- Dla użytkownika biznesowego, wybierając **ofercie** opcji.
- Dla administratora IT, wybierając **sprzedaży firmy Microsoft**.
Aby określić, jakiego typu aplikacji SaaS w przypadku tworzenia, odczytu [zrozumieć storefront wybór](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection).


#### <a name="sell-through-microsoft"></a>Sprzedaży firmy Microsoft
Do tworzenia tego środowiska, należy skonfigurować następujące elementy:

- Połącz swoje witryny sieci Web usługi SaaS z interfejsami API usług SaaS firmy Microsoft. [Sprzedaży SaaS za pośrednictwem platformy Azure — interfejsy API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) artykule wyjaśniono, jak utworzyć to połączenie.
- Włącz sprzedaży za pośrednictwem platformy Azure w portalu Cloud Partner w formularzu konfiguracji technicznej i podaj wymagane informacje. Aby uzyskać więcej informacji o ten model rozliczeń i sposób implementacji, zobacz [SaaS — sprzedawać za pośrednictwem platformy Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions).

  ![Sprzedaż dzięki formularza firmy Microsoft](./media/saas-techinfo-sellthrough-ms.png)

W poniższej tabeli opisano wymagane pola dla **sprzedaży firmy Microsoft**.  Wymagane pola są wskazanych przez znak gwiazdki (*).

|  **Nazwa pola**   |  **Opis**  |
|  ---------------  |  ---------------  |
|  **Identyfikatory subskrypcji (wersja zapoznawcza)\***   |  Wszystkich subskrypcji platformy Azure identyfikatory używane do testowania oferty w wersji zapoznawczej, zanim będzie dostępny publicznie.  |
|  **Konta usługi AAD/MSA w wersji zapoznawczej\***   |  Konta usługi Azure AD/MSA rozdzielonych przecinkami, które uzyskują dostęp do wersji zapoznawczej. |
|  **Instrukcje z wprowadzeniem** |  Wskazówki, aby udostępnić ze swoimi klientami, aby pomóc im nawiązywanie aplikację SaaS. Podstawowe tagów HTML są dozwolone, na przykład: &lt;p&gt;, &lt;h1&gt;, &lt;li&gt;itp.    |
|  **Adres URL strony\***           |  Adres URL witryny, która będzie kierowanie klientów jako proponowany po uzyskaniu z witryny Azure portal, aby. Ten adres URL będzie również punkt końcowy, który będzie otrzymywać połączenia interfejsy API w celu ułatwienia handlowych firmy Microsoft.   |
| **Połączenie elementu Webhook\***            |  Dla wszystkich zdarzeń asynchronicznych, które firma Microsoft musi wysłać użytkownikom w imieniu klienta (przykład: Subskrypcja platformy Azure stała się nieprawidłowa), wymagamy Opisz element webhook połączenia. Jeśli nie masz jeszcze system elementu webhook w miejscu, najprostsza konfiguracja jest aplikacja logiki punktu końcowego HTTP, który będzie nasłuchiwać zdarzeń ogłaszany do niego i odpowiednio je obsłużyć. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">wywołania wyzwalacza lub zagnieżdżanie przepływy pracy za pomocą punktów końcowych HTTP w usłudze logic apps</a>    |
|  **Identyfikator dzierżawy usługi Azure AD\***  i **Identyfikatora aplikacji\***      |   W witrynie Azure portal, wymagamy, aby utworzyć aplikacji usługi Active Directory, dzięki czemu możemy zweryfikować połączenie między dwoma naszych usług znajduje się za uwierzytelnianiu komunikacji. Dla tych pól, Utwórz aplikację usługi AD i wkleić w odpowiednich identyfikatora dzierżawy oraz identyfikatora aplikacji jest wymagane. Należy pamiętać, że identyfikator aplikacji jest skojarzona z Twojej publisherID. W związku z tym upewnij się, że ten sam identyfikator aplikacji, tak jak wszystkie oferty.   |
|   |   |

Na koniec po wybraniu **sprzedaży firmy Microsoft**, istnieje inna karta nowa oferta o nazwie **plany**. 

[Kartę plany](./cpp-plans-tab.md) wyświetla określonych planów i ich odpowiednie ceny, obsługiwanych przez aplikację SaaS. Obecnie firma Microsoft umożliwia ceny miesięczne z możliwością umożliwia dla 1 - lub 3-miesięcznym bezpłatny dostęp. Te plany i ceny powinna być zgodna dokładnie plany i ceny, do których masz na swojej stronie aplikacji SaaS.

>[!NOTE] 
>Plany są wymagane tylko, jeśli wybierzesz sprzedaży firmy Microsoft.

### <a name="call-to-action-field"></a>Wywołanie pole akcji

Wywołanie metody pól akcji umożliwia wybranie komunikat, który jest wyświetlany na przycisku nabycia swojej oferty. Dostępne są następujące opcje:

- Bezpłatne — w przypadku wybrania tej opcji, zostanie wyświetlony monit wprowadź adres URL wersji próbnej, gdzie klienci mogą uzyskać dostęp do aplikacji SaaS. Na przykład: https://contoso.com/trial
- Bezpłatna wersja próbna — Jeśli wybrania tej opcji, zostanie wyświetlony monit o podanie adresu URL wersji próbnej, gdzie klienci mogą uzyskać dostęp do aplikacji SaaS. Na przykład: https://contoso.com/trial
- Kontakt ze mną

Aby uzyskać więcej informacji na temat wywołania Opcje akcji Zobacz wybierz jedną z opcji publikowania.

>[!Note]
>Dostawcy rozwiązań (CSP) partnera kanału zoptymalizowany pod kątem w chmurze już dostępna.  Zobacz [dostawców rozwiązań w chmurze](../../cloud-solution-providers.md) więcej informacji na temat marketingowych oferty za pośrednictwem programu Microsoft CSP partner kanałów.

## <a name="next-steps"></a>Kolejne kroki

- [Plany karty (opcjonalnie)](./cpp-plans-tab.md)
- [Karta informacji z kanału](./cpp-channel-info-tab.md)

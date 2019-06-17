---
title: Migrowanie aktywnej nazwy DNS — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację niestandardowej nazwy domeny DNS, który jest już przypisana do witryny na żywo w usłudze Azure App Service bez żadnych przestojów.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6215230a52bcb5c44f54747b447dc5f64e6af650
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130385"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrowanie aktywnej nazwy DNS do usługi Azure App Service

W tym artykule dowiesz się, jak przeprowadzić migrację aktywnej nazwy DNS na [usługi Azure App Service](../app-service/overview.md) bez żadnych przestojów.

Przeprowadzając migrację aktywnej witryny oraz jej nazwy domeny DNS w usłudze App Service, tej nazwy już obsługuje ruch na żywo. Aby uniknąć przestoju w rozpoznawania nazw DNS podczas migracji, prewencyjnego powiązanie aktywnej nazwy DNS na aplikację usługi App Service.

Jeśli nie masz Boisz się o przestoje w rozpoznawania nazw DNS, zobacz [mapowanie istniejącej niestandardowej nazwy DNS w usłudze Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia całego instruktażu:

- [Upewnij się, że aplikacji usługi app Service nie jest w warstwie bezpłatna](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Prewencyjnego powiązania nazwy domeny

Gdy powiążesz prewencyjnego domenę niestandardową, można wykonać obie z następujących czynności przed wprowadzeniem jakichkolwiek zmian w rekordach DNS:

- Weryfikowanie własności domeny
- Włącz nazwy domeny dla swojej aplikacji

Gdy na końcu migracji swojej niestandardowej nazwy DNS ze starej witryny do aplikacji usługi App Service, nastąpi bez przerwy w działaniu w rozpoznawania nazw DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Utwórz rekord weryfikacji domeny

Aby zweryfikować własność domeny, należy dodać rekord TXT. Rekord TXT mapy z _awverify.&lt; Poddomena >_ do  _&lt;nazwa_aplikacji >. azurewebsites.net_. 

Rekord TXT, czego potrzebujesz, zależy od rekordu DNS, które mają zostać zmigrowane. Aby uzyskać przykłady, zobacz poniższą tabelę (`@` zazwyczaj reprezentuje domenę katalogu głównego):

| Przykładowy rekord DNS | TXT Host | Wartość TXT |
| - | - | - |
| \@ (root) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| WWW (pod) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \* (symbol wieloznaczny) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Na stronie rekordów DNS należy pamiętać, typ rekordu nazwy DNS, które mają zostać zmigrowane. Usługa App Service obsługuje mapowania CNAME i.

> [!NOTE]
> W przypadku niektórych dostawców, takich jak CloudFlare, `awverify.*` nie jest prawidłowym rekordem. Użyj `*` tylko zamiast tego.

> [!NOTE]
> Symbol wieloznaczny `*` rekordów będzie nieprawidłowa poddomeny przy użyciu rekordu CNAME istniejących. Konieczne może być jawnie utworzyć rekord TXT w każdej domenie podrzędnej.


### <a name="enable-the-domain-for-your-app"></a>Włącz domeny aplikacji

W [witryny Azure portal](https://portal.azure.com), w lewym obszarze nawigacji na stronie aplikacji wybierz **domen niestandardowych**. 

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

W **domen niestandardowych** wybierz opcję **+** ikona obok pozycji **Dodaj nazwę hosta**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowana nazwa domeny dodany rekord TXT, takich jak `www.contoso.com`. Dla domeny z symbolami wieloznacznymi (takich jak \*. contoso.com), można użyć dowolnej nazwy DNS, który pasuje do domeny z symbolami wieloznacznymi. 

Wybierz przycisk **Weryfikuj**.

Zostanie aktywowany przycisk **Dodaj nazwę hosta**. 

Upewnij się, że **typ rekordu nazwy hosta** jest ustawiona na typ rekordu DNS, które są przeznaczone do migracji.

Wybierz przycisk **Dodaj nazwę hosta**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Może upłynąć trochę czasu, zanim nowa nazwa hosta zostanie odzwierciedlona na stronie **Domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Swojej niestandardowej nazwy DNS jest teraz włączony w aplikacji platformy Azure. 

## <a name="remap-the-active-dns-name"></a>Ponowne mapowanie aktywnej nazwy DNS

Tylko po lewej stronie co zrobić to ponowne mapowanie active rekord DNS w taki sposób, by wskazywał usługi App Service. Po prawej stronie teraz nadal wskazuje starej witryny.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Skopiuj adres IP aplikacji (tylko rekordu)

Jeśli to ponowne mapowanie rekordu CNAME, Pomiń tę sekcję. 

Aby ponownie zamapować rekord A, należy zewnętrzny adres IP aplikacji usługi App Service, co zostało pokazane **domen niestandardowych** strony.

Zamknij **Dodaj nazwę hosta** strony, wybierając **X** w prawym górnym rogu. 

Na stronie **Domeny niestandardowe** skopiuj adres IP aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Zaktualizuj rekord DNS

Na strona rekordów DNS dostawcy domeny zaznacz rekord DNS, aby ponownie zamapować.

Aby uzyskać `contoso.com` główny przykładowej domeny, ponowne mapowanie rekordu A lub CNAME, takich jak przykłady przedstawione w poniższej tabeli: 

| Przykład nazwy FQDN | Typ rekordu | Host | Wartość |
| - | - | - | - |
| contoso.com (root) | A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
| www\.contoso.com (pod) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*. contoso.com (symbol wieloznaczny) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Zapisz ustawienia.

Na początek zapytania DNS rozpoznawania aplikacji usługi app Service, natychmiast po zakończeniu Propagacja DNS się dzieje.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak powiązać niestandardowy certyfikat protokołu SSL usługi App Service.

> [!div class="nextstepaction"]
> [Wiązanie istniejącego niestandardowego certyfikatu SSL z usługą Azure App Service](app-service-web-tutorial-custom-ssl.md)

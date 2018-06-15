---
title: Migracji active nazwy DNS w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację niestandardowej nazwy domeny DNS, który jest już przypisany do lokacji na żywo w usłudze Azure App Service bez żadnego przestoju.
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
ms.openlocfilehash: cd04be2046a23901471cb7bd0da9e0ed2d514d0d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
ms.locfileid: "27566495"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migracji active nazwy DNS w usłudze Azure App Service

W tym artykule przedstawiono sposób migracji active nazwy DNS [usłudze Azure App Service](../app-service/app-service-web-overview.md) bez żadnego przestoju.

Podczas migracji na żywo lokacji i nazwy domeny DNS do usługi App Service, tej nazwy DNS jest już obsługujące ruch na żywo. Aby uniknąć przestój w przypadku rozpoznawania nazw DNS podczas migracji, można preemptively powiązanie nazwy DNS active aplikację usługi aplikacji.

Jeśli nie możesz Obawiamy o przestój w przypadku rozpoznawania nazw DNS, zobacz [mapowania istniejącej nazwy DNS niestandardowej aplikacji sieci Web Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten Porada:

- [Upewnij się, że aplikację usługi aplikacji nie znajduje się w warstwie bezpłatna](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Powiąż preemptively nazwy domeny

Gdy powiąże preemptively niestandardową domenę, wykonywać następujące przed wprowadzeniem jakichkolwiek zmian w rekordach DNS:

- Weryfikowanie własności domeny
- Włącz nazwy domeny dla aplikacji

Na koniec migracji nazwę DNS niestandardowych ze starej witryny do aplikacji usługi app Service, nie będzie bez przestojów w rozpoznawania nazw DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Utwórz rekord weryfikacji domeny

Aby zweryfikować prawo własności do domeny, Dodaj rekord TXT. Rekord TXT mapy z _awverify.&lt; Poddomena >_ do  _&lt;nazwa_aplikacji >. azurewebsites.net_. 

Rekord TXT, które są potrzebne, zależy od rekord DNS, który chcesz przeprowadzić migrację. Aby uzyskać przykłady, zobacz poniższą tabelę (`@` zazwyczaj reprezentuje domeny katalogu głównego):

| Przykładowy rekord DNS | TXT Host | Wartość TXT |
| - | - | - |
| @ (root) | _awverify_ | _&lt;Nazwa aplikacji >. azurewebsites.net_ |
| WWW (sub) | _awverify.www_ | _&lt;Nazwa aplikacji >. azurewebsites.net_ |
| \*(symbol wieloznaczny) | _awverify.\*_ | _&lt;Nazwa aplikacji >. azurewebsites.net_ |

Na stronie rekordy DNS należy pamiętać, typ rekordu nazwy DNS, które chcesz migrować. Usługa aplikacji obsługuje mapowania z CNAME i.

### <a name="enable-the-domain-for-your-app"></a>Włącz domeny aplikacji

W [portalu Azure](https://portal.azure.com), w lewym obszarze nawigacji strony aplikacji, wybierz **domen niestandardowych**. 

![Menu domeny niestandardowej](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

W **domen niestandardowych** wybierz pozycję  **+**  obok opcji **dodać nazwę hosta**.

![Dodaj nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowaną nazwę domeny dodaje rekord TXT, takich jak `www.contoso.com`. Dla domeny symbolu wieloznacznego (takich jak \*. contoso.com), można użyć dowolnej nazwy DNS, która pasuje do domeny symboli wieloznacznych. 

Wybierz **zweryfikować**.

**Dodać nazwę hosta** przycisk jest aktywny. 

Upewnij się, że **typu rekordu Hostname** ustawiono typ rekordu DNS chcesz migrować.

Wybierz **dodać nazwę hosta**.

![Dodaj nazwę DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Może upłynąć trochę czasu, zanim nowej nazwy hosta zostaną odzwierciedlone w aplikacji **domen niestandardowych** strony. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodaje rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Niestandardowe nazwę DNS jest teraz włączone w aplikacji Azure. 

## <a name="remap-the-active-dns-name"></a>Ponowne mapowanie active nazwy DNS

Tylko po lewej co zrobić jest ponowne mapowanie sieci active rekord DNS, aby wskazywał usługi aplikacji. Prawo teraz, nadal wskazuje stare witryny.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Skopiuj adres IP aplikacji (tylko rekord)

Jeśli są ponowne mapowanie rekord CNAME, Pomiń tę sekcję. 

Aby ponownie zamapować rekord A, należy zewnętrzny adres IP aplikację usługi aplikacji, który jest wyświetlany w **domen niestandardowych** strony.

Zamknij **dodać nazwę hosta** strony, wybierając **X** w prawym górnym rogu. 

W **domen niestandardowych** strony, skopiuj adres IP aplikacji.

![Nawigacji w portalu do aplikacji Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Zaktualizuj rekord DNS

Ponownie na stronie rekordy DNS dostawcy domeny wybierz rekord DNS, aby ponownie zamapować.

Aby uzyskać `contoso.com` przykładowa domena główna, ponownie zamapować rekord A lub CNAME, podobnie jak w przykładach w poniższej tabeli: 

| Przykład nazwy FQDN | Typ rekordu | Host | Wartość |
| - | - | - | - |
| contoso.com (root) | A | `@` | Adres IP z [skopiuj adres IP aplikacji](#info) |
| www.contoso.com (sub) | CNAME | `www` | _&lt;Nazwa aplikacji >. azurewebsites.net_ |
| \*. contoso.com (symbol wieloznaczny) | CNAME | _\*_ | _&lt;Nazwa aplikacji >. azurewebsites.net_ |

Zapisz ustawienia.

Zapytania DNS powinien rozpocząć rozpoznawania aplikację usługi aplikacji natychmiast po sytuacji propagacji DNS.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak powiązać niestandardowego certyfikatu SSL z usługi aplikacji.

> [!div class="nextstepaction"]
> [Powiąż istniejący certyfikat SSL niestandardowych do aplikacji sieci Web Azure](app-service-web-tutorial-custom-ssl.md)

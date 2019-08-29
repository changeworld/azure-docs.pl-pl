---
title: Migrowanie aktywnej nazwy DNS — Azure App Service | Microsoft Docs
description: Dowiedz się, jak migrować niestandardową nazwę domeny DNS, która jest już przypisana do działającej witryny, aby Azure App Service bez przestojów.
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
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 703a151f801f65b968ecf93eaa97640c22a71bd2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073088"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrowanie aktywnej nazwy DNS do Azure App Service

W tym artykule przedstawiono sposób migrowania aktywnej nazwy DNS w celu [Azure App Service](../app-service/overview.md) bez przestojów.

W przypadku migrowania aktywnej witryny i jej nazwy domeny DNS do App Service, nazwa DNS ma już obsługiwać ruch na żywo. Można uniknąć przestojów rozpoznawania nazw DNS podczas migracji przez powiązanie aktywnej nazwy DNS z aplikacją App Service App zapobiegawczo.

Jeśli nie martwisz się o przestoju w rozpoznawaniu nazw DNS, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

- Upewnij się [, że aplikacja App Service nie znajduje się w warstwie Bezpłatna](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Powiąż nazwę domeny zapobiegawczo

W przypadku powiązania niestandardowej domeny zapobiegawczo należy wykonać obie następujące czynności przed wprowadzeniem jakichkolwiek zmian w rekordach DNS:

- Weryfikowanie własności domeny
- Włączanie nazwy domeny dla aplikacji

Po zakończeniu migracji niestandardowej nazwy DNS ze starej lokacji do aplikacji App Service nie będzie żadnych przestojów w rozpoznawaniu nazw DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Utwórz rekord weryfikacji domeny

Aby sprawdzić własność domeny, Dodaj rekord TXT. Rekord TXT jest mapowany z _awverify.&lt; poddomena >_ do programu  _&lt;nazwa_aplikacji >. azurewebsites. NET_. 

Rekord TXT, którego potrzebujesz, zależy od rekordu DNS, który ma zostać zmigrowany. Aby zapoznać się z przykładami, zobacz`@` poniższą tabelę (zazwyczaj reprezentuje domenę główną):

| Przykład rekordu DNS | Host TXT | Wartość TXT |
| - | - | - |
| \@pierwiastek | _awverify_ | _&lt;nazwa_aplikacji >. azurewebsites. NET_ |
| www (Sub) | _awverify. www_ | _&lt;nazwa_aplikacji >. azurewebsites. NET_ |
| \*znaku | _awverify.\*_ | _&lt;nazwa_aplikacji >. azurewebsites. NET_ |

Na stronie rekordów DNS Zwróć uwagę na typ rekordu nazwy DNS, którą chcesz migrować. App Service obsługuje mapowania z rekordów CNAME i A.

> [!NOTE]
> W przypadku niektórych dostawców, takich jak CloudFlare `awverify.*` , nie jest prawidłowym rekordem. Użyj `*` tylko zamiast.

> [!NOTE]
> Rekordy `*` z symbolami wieloznacznymi nie sprawdzają domen poddomen przy użyciu istniejącego rekordu CNAME. Może być konieczne jawne utworzenie rekordu TXT dla każdej poddomeny.


### <a name="enable-the-domain-for-your-app"></a>Włącz domenę dla aplikacji

W [Azure Portal](https://portal.azure.com)w lewym okienku nawigacji strony aplikacji wybierz pozycję **domeny niestandardowe**. 

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stronie **domeny niestandardowe** wybierz **+** ikonę obok pozycji **Dodaj nazwę hosta**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowaną nazwę domeny, do której dodano rekord TXT, `www.contoso.com`na przykład. W przypadku domeny wieloznacznej \*(np. contoso.com) można użyć dowolnej nazwy DNS, która pasuje do domeny z symbolem wieloznacznym. 

Wybierz przycisk **Weryfikuj**.

Zostanie aktywowany przycisk **Dodaj nazwę hosta**. 

Upewnij się, że **Typ rekordu nazwy hosta** jest ustawiony na typ rekordu DNS, który ma zostać zmigrowany.

Wybierz przycisk **Dodaj nazwę hosta**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Może upłynąć trochę czasu, zanim nowa nazwa hosta zostanie odzwierciedlona na stronie **Domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Niestandardowa nazwa DNS jest teraz włączona w aplikacji platformy Azure. 

## <a name="remap-the-active-dns-name"></a>Ponowne mapowanie aktywnej nazwy DNS

Jedyną czynnością, którą pozostało do wykonania, jest ponowne mapowanie aktywnego rekordu DNS w celu wskazywania App Service. Teraz nadal wskazuje Twoją starą lokację.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Skopiuj adres IP aplikacji (tylko rekord)

Jeśli ponownie mapujesz rekord CNAME, Pomiń tę sekcję. 

Aby ponownie zamapować rekord A, potrzebny jest zewnętrzny adres IP aplikacji App Service, który jest wyświetlany na stronie **domeny niestandardowe** .

Zamknij stronę **Dodawanie nazwy hosta** , wybierając pozycję **X** w prawym górnym rogu. 

Na stronie **Domeny niestandardowe** skopiuj adres IP aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizowanie rekordu DNS

Na stronie rekordy DNS dostawcy domeny wybierz rekord DNS do ponownego mapowania.

W przykładzie domeny katalogu głównego ponownie zamapuj rekord A lub CNAME, jak przykłady w poniższej tabeli: `contoso.com` 

| Przykład nazwy FQDN | Typ rekordu | Host | Value |
| - | - | - | - |
| contoso.com (główny) | A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
| contoso.com\.www (Sub) | CNAME | `www` | _&lt;nazwa_aplikacji >. azurewebsites. NET_ |
| \*. contoso.com (symbol wieloznaczny) | CNAME | _\*_ | _&lt;nazwa_aplikacji >. azurewebsites. NET_ |

Zapisz ustawienia.

Zapytania DNS powinny rozpoczynać rozpoznawanie do aplikacji App Service natychmiast po wykonaniu propagacji DNS.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak powiązać niestandardowy certyfikat SSL z App Service.

> [!div class="nextstepaction"]
> [Wiązanie istniejącego niestandardowego certyfikatu SSL z usługą Azure App Service](app-service-web-tutorial-custom-ssl.md)

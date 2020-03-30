---
title: Dołączanie domeny głównej lub wierzchołkowej do istniejących drzwi frontowych — witryna Azure portal
description: Dowiedz się, jak przypomnieć domenę główną lub pierwszogłową do istniejących drzwi frontowych przy użyciu witryny Azure portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184612"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Wbudowana domena główna lub wierzchołowa w drzwiach frontowych
Usługa Azure Front Door używa rekordów CNAME do sprawdzania poprawności własności domeny w celu dołączania domen niestandardowych. Ponadto drzwiami frontowymi nie uwidaczniają adresu IP frontonu skojarzonego z profilem drzwiami frontowymi, więc nie można mapować domeny wierzchołka na adres IP, jeśli celem jest dołączenie go do drzwi frontowych platformy Azure.

Protokół DNS uniemożliwia przypisywanie rekordów CNAME w wierzchołku strefy. Na przykład, jeśli `contoso.com`domena jest ; można tworzyć rekordy CNAME dla `somelabel.contoso.com`; ale nie można utworzyć CNAME dla `contoso.com` siebie. To ograniczenie stanowi problem dla właścicieli aplikacji, którzy mają aplikacje z równoważeniem obciążenia za drzwiami frontowymi platformy Azure. Ponieważ użycie profilu drzwi y przedniej wymaga utworzenia rekordu CNAME, nie można wskakiwać profilu drzwi frontowych z wierzchołka strefy.

Ten problem został rozwiązany przy użyciu rekordów aliasu w usłudze Azure DNS. W przeciwieństwie do rekordów CNAME rekordy aliasów są tworzone w wierzchołku strefy, a właściciele aplikacji mogą go używać do wskazywali swój rekord wierzchołka strefy na profil drzwiach frontowych z publicznymi punktami końcowymi. Właściciele aplikacji wskazują ten sam profil drzwi frontowych, który jest używany dla każdej innej domeny w strefie DNS. Na przykład `contoso.com` `www.contoso.com` i może wskazywać ten sam profil drzwi przednich. 

Mapowanie wierzchołka lub domeny głównej do profilu drzwi frontowych zasadniczo wymaga spłaszczenia CNAME lub gonienia DNS, który jest mechanizmem, w którym w dostawcy DNS rekursywnie rozpoznaje wpis CNAME, dopóki nie osiągnie adresu IP. Ta funkcja jest obsługiwana przez usługę Azure DNS dla punktów końcowych drzwi frontowych. 

> [!NOTE]
> Istnieją również inni dostawcy dns, którzy obsługują spłaszczanie CNAME lub gonienie DNS, jednak usługa Azure Front Door zaleca używanie usługi Azure DNS dla swoich klientów do obsługi ich domen.

Za pomocą witryny Azure Portal można użyć do wbudowanej domeny wierzchołka w drzwiach frontowych i włączyć https na nim, kojarząc go z certyfikatem zakończenia protokołu SSL. Domeny Apex są również określane jako domeny główne lub nagie.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie rekordu aliasu, który wskazuje profil drzwi yjektej
> * Dodawanie domeny głównej do drzwi podstawowych
> * Konfigurowanie protokołu HTTPS w domenie głównej

> [!NOTE]
> Ten samouczek wymaga, aby masz już utworzony profil drzwi frontowych. Zapoznaj się z innymi samouczkami, takimi jak [Szybki start: Utwórz drzwi przednie](./quickstart-create-front-door.md) lub [Utwórz drzwi frontowe z przekierowaniem HTTP do HTTPS,](./front-door-how-to-redirect-https.md) aby rozpocząć.

## <a name="create-an-alias-record-for-zone-apex"></a>Tworzenie rekordu aliasu dla wierzchołka strefy

1. Otwórz konfigurację **usługi Azure DNS,** aby domena była dołączana.
2. Tworzenie lub edytowanie rekordu dla wierzchołka strefy.
3. Wybierz **typ** rekordu jako _rekord,_ a następnie wybierz pozycję _Tak_ dla **zestawu rekordów aliasu**. **Typ aliasu** powinien być ustawiony na _zasób platformy Azure_.
4. Wybierz subskrypcję platformy Azure, w której znajduje się Twój profil drzwiami frontowymi, a następnie wybierz zasób drzwiach frontowych z listy rozwijanej **zasobów platformy Azure.**
5. Kliknij **przycisk OK,** aby przesłać zmiany.

    ![Rekord aliasu dla wierzchołka strefy](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Powyższy krok utworzy rekord wierzchołka strefy wskazujący zasób drzwi frontowych, a `afdverify.contosonews.com`także `afdverify.<name>.azurefd.net` mapowanie rekordu CNAME "afdverify" (przykład - ), do którego będzie używany do dołączania domeny w profilu drzwiami frontowymi.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Wbudowana domena niestandardowa w drzwiach frontowych

1. Na karcie Projektant drzwi frontowych kliknij ikonę "+" w sekcji Hosty frontu, aby dodać nową domenę niestandardową.
2. Wprowadź nazwę domeny głównej lub wierzchołka `contosonews.com`w polu nazwa hosta niestandardowego, na przykład .
3. Po sprawdzeniu poprawności mapowania CNAME z domeny do drzwi frontowych kliknij przycisk **Dodaj,** aby dodać domenę niestandardową.
4. Kliknij **przycisk Zapisz,** aby przesłać zmiany.

![Menu domen niestandardowych](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Włączanie protokołu HTTPS w domenie niestandardowej

1. Kliknij domenę niestandardową, która została dodana i w sekcji **Domena niestandardowa HTTPS**zmień stan na **Włączone**.
2. Wybierz **typ zarządzania certyfikatami,** aby _wybrać opcję "Użyj własnego certyfikatu"._

> [!WARNING]
> Typ zarządzania certyfikatami zarządzanymi przez drzwiami frontowymi nie jest obecnie obsługiwany dla domen wierzchołkowych lub głównych. Jedyną opcją dostępną do włączania protokołu HTTPS w domenie wierzchołkowej lub głównej dla drzwi ekwiwalom frontowym jest użycie własnego niestandardowego certyfikatu SSL hostowanego w usłudze Azure Key Vault.

3. Upewnij się, że masz skonfigurowane odpowiednie uprawnienia dla drzwiach frontowych, aby uzyskać dostęp do magazynu kluczy, jak wspomniano w interfejsie użytkownika, przed przejściem do następnego kroku.
4. Wybierz **konto Usługi Key Vault** z bieżącej subskrypcji, a następnie wybierz odpowiednią wersję **tajną** i **tajną,** aby zamapować na odpowiedni certyfikat.
5. Kliknij **przycisk Aktualizuj,** aby zapisać zaznaczenie, a następnie kliknij przycisk **Zapisz**.
6. Kliknij **przycisk Odśwież** po kilku minutach, a następnie kliknij ponownie domenę niestandardową, aby zobaczyć postęp inicjowania obsługi administracyjnej certyfikatów. 

> [!WARNING]
> Upewnij się, że utworzono odpowiednie reguły routingu dla domeny apex lub dodano domenę do istniejących reguł routingu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
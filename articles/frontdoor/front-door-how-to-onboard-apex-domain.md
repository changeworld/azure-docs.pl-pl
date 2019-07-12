---
title: Dołączanie domeny katalogu głównego lub wierzchołku do istniejących drzwiami frontowymi przy użyciu witryny Azure portal
description: Dowiedz się, jak dołączyć domeny katalogu głównego lub wierzchołku do istniejących drzwiami frontowymi przy użyciu witryny Azure portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 8fe8da95a61d2f2bb35095236131670cb6ef0e70
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605791"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Dołączanie do domeny katalogu głównego lub wierzchołku w swojej drzwiami frontowymi
Azure drzwiami frontowymi używa rekordów CNAME, aby zweryfikować własność domeny do dołączenia do domen niestandardowych. Ponadto drzwiami frontowymi nie ujawnia adresu IP frontonu, które są skojarzone z profilem drzwiami frontowymi, a więc nie można mapować domenę wierzchołku jako adres IP, jeśli celem jest dodać ją do platformy Azure drzwiami frontowymi.

Protokół DNS uniemożliwia przypisania rekordów CNAME w wierzchołku strefy. Na przykład, jeśli Twoja domena to `contoso.com`; można utworzyć rekordy CNAME dla `somelabel.contoso.com`; ale nie można utworzyć rekord CNAME dla domeny `contoso.com` sam. Ograniczenie to stanowi problem dla właścicieli aplikacji, mających równoważenia obciążenia aplikacji za zaporą Azure drzwi wejściowe. Ponieważ przy użyciu profilu drzwiami frontowymi wymaga utworzenia rekordu CNAME, nie jest możliwe wskazywała na profil drzwiami frontowymi w wierzchołku strefy.

Ten problem został rozwiązany, przy użyciu rekordów aliasów w usłudze Azure DNS. W przeciwieństwie do rekordów CNAME rekordów aliasów są tworzone w wierzchołku strefy i właścicieli aplikacji może użyć go do punktu ich rekordów w wierzchołku strefy w profilu drzwiami frontowymi, który ma publicznych punktów końcowych. Właściciele aplikacji wskaż taki sam jak profil drzwiami frontowymi jest używana do innej domeny w swojej strefie DNS. Na przykład `contoso.com` i `www.contoso.com` może wskazywać tego samego profilu wejściu. 

Mapowanie domeny wierzchołku lub katalogu głównego do Twojego profilu drzwiami frontowymi zasadniczo wymaga spłaszczanie CNAME lub DNS wyszukiwać te elementy, które to mechanizm, gdzie w systemie DNS rekursywnie dostawcy jest rozpoznawana jako wpis CNAME aż trafienia adresu IP. Ta funkcja jest obsługiwana przez usługę Azure DNS dla punktów końcowych drzwiami frontowymi. 

> [!NOTE]
> Nie ma innych dostawców usługi DNS oraz obsługujące CNAME spłaszczanie lub łączone DNS, jednak Azure drzwiami frontowymi zaleca się przy użyciu usługi Azure DNS dla swoich klientów do hostowania swojej domeny.

Można używać witryny Azure portal, aby dołączyć domeny wierzchołku na Twoje drzwiami frontowymi i włączyć protokół HTTPS na nim przez skojarzenie jej z certyfikatu dla kończenia żądań SSL. Domen wierzchołku również są określane jako głównego lub domeny "naked".

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie rekordu aliasu wskazujący na wejściu profilu
> * Dodawanie domeny katalogu głównego do drzwiami frontowymi
> * Konfigurowanie protokołu HTTPS w domenie katalogu głównego

> [!NOTE]
> Ten samouczek wymaga jeszcze profilu drzwiami frontowymi, który został utworzony. Zapoznaj się z innych samouczków, takich jak [Szybki Start: Utwórz drzwiami frontowymi](./quickstart-create-front-door.md) lub [tworzenie drzwiami frontowymi za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS](./front-door-how-to-redirect-https.md) na rozpoczęcie pracy.

## <a name="create-an-alias-record-for-zone-apex"></a>Tworzenie rekordu aliasu dla wierzchołku strefy

1. Otwórz **system DNS Azure** konfiguracji dla domeny, które mają zostać dołączone.
2. Tworzenie i edytowanie rekordów na wierzchołku strefy.
3. Wybierz rekord **typu** jako _A_ rejestrowania, a następnie wybierz pozycję _tak_ dla **zestawu rekordów aliasów**. **Wpisz alias** powinna być równa _zasobów platformy Azure_.
4. Wybierz subskrypcję platformy Azure, w którym jest hostowany Twój profil drzwiami frontowymi, a następnie wybierz zasób drzwiami frontowymi z **zasobów platformy Azure** listy rozwijanej.
5. Kliknij przycisk **OK** Aby przesłać wprowadzone zmiany.

    ![Alias rekordów na wierzchołku strefy](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. Powyższy krok spowoduje utworzenie rekordu wierzchołku strefy wskazującego drzwiami frontowymi zasobu, a także mapowania rekordów CNAME "afdverify" (przykład — `afdverify.contosonews.com`) do `afdverify.<name>.azurefd.net` który będzie używany do dołączania do domeny w Twoim profilu drzwiami frontowymi.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Dodawanie domeny niestandardowej na Twoje drzwi

1. Na karcie projektanta drzwiami frontowymi kliknij pozycję "a +" ikonę na hostach serwera sieci Web sekcji Dodaj domenę niestandardową.
2. Wprowadź nazwę domeny katalogu głównego lub języka apex w polu Nazwa niestandardowego hosta przykład `contosonews.com`.
3. Po zweryfikowaniu mapowanie CNAME z domeny do usługi drzwiami frontowymi kliknij **Dodaj** do dodawania domeny niestandardowej.
4. Kliknij przycisk **Zapisz** przesłanie zmian.

![Menu domen niestandardowych](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Włączanie obsługi protokołu HTTPS w domenie niestandardowej

1. Kliknij w domenie niestandardowej, która została dodana, a także w sekcji **protokół HTTPS domen niestandardowych**, Zmień stan na **włączone**.
2. Wybierz **zarządzania typ certyfikatu** do _"Użyj własnego certyfikatu"_ .

> [!WARNING]
> Typ zarządzania certyfikatami w usłudze frontonu drzwi zarządzane nie jest obecnie obsługiwane w przypadku domen wierzchołku lub katalogu głównego. Jedyna opcja dostępna dotyczące włączania protokołu HTTPS w domenie wierzchołku lub katalogu głównego drzwiami frontowymi jest przy użyciu własnego niestandardowego certyfikatu SSL, które są hostowane w usłudze Azure Key Vault.

3. Upewnij się, że Instalator odpowiednie uprawnienia umożliwiające drzwiami frontowymi dostępu do Twojego klucza magazynu, jak wspomniano w interfejsie użytkownika przed przejściem do następnego kroku.
4. Wybierz **koncie usługi Key Vault** z bieżącej subskrypcji, a następnie wybierz odpowiednią **klucz tajny** i **wersja wpisu tajnego** do mapowania na właściwy certyfikat.
5. Kliknij pozycję **aktualizacji** Zapisz wybór, a następnie kliknij przycisk **Zapisz**.
6. Kliknij przycisk **Odśwież** za kilka minut i kliknij przycisk w domenie niestandardowej, ponownie, aby wyświetlić postęp aprowizacja obsługi certyfikatów. 

> [!WARNING]
> Upewnij się, że tworzone odpowiednie reguły routingu dla swojej domeny wierzchołku lub dodać domeny do istniejącej reguły routingu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
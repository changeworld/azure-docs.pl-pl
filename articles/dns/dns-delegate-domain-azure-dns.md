---
title: Samouczek — hostowanie własnej domeny i poddomeny w usłudze Azure DNS
description: W tym samouczku przedstawiono sposób konfigurowania usługi Azure DNS na potrzeby hostowania stref DNS.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: ea0dc257d691326bc073b4cbff37e847a6990f02
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452304"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Samouczek: hostowanie własnej domeny w usłudze Azure DNS

Za pomocą usługi Azure DNS można hostować swoją domenę DNS i zarządzać rekordami DNS. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. 

Załóżmy, że masz zakupioną domenę „contoso.net” od rejestratora nazw domen i tworzysz strefę o nazwie „contoso.net” w usłudze Azure DNS. Ponieważ jesteś właścicielem domeny, rejestrator oferuje Ci opcję skonfigurowania rekordów serwerów nazw (NS) dla domeny. Rejestrator przechowuje rekordy NS w strefie nadrzędnej „.net”. Użytkownicy Internetu na całym świecie są kierowani do Twojej domeny w strefie usługi Azure DNS podczas próby rozpoznania rekordów DNS w strefie „contoso.net”.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie strefy DNS
> * Pobieranie listy serwerów nazw
> * Delegowanie domeny
> * Sprawdzanie poprawności działania delegowania


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Zaloguj się do Portalu Azure.
1. W lewym górnym rogu wybierz pozycję **Utwórz zasób** > **Sieć** > **Strefa DNS**, aby otworzyć stronę **Tworzenie strefy DNS**.

   ![Strefa DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Na stronie **Tworzenie strefy DNS** wprowadź następujące wartości, a następnie wybierz pozycję **Utwórz**:

   | **Ustawienie** | **Wartość** | **Szczegóły** |
   |---|---|---|
   |**Nazwa**|[nazwa Twojej domeny] |Nazwa zakupionej domeny. W tym samouczku używana jest przykładowa nazwa „contoso.net”.|
   |**Subskrypcja**|[Twoja subskrypcja]|Wybierz subskrypcję, w której chcesz utworzyć strefę.|
   |**Grupa zasobów**|**Utwórz nową:** contosoRG|Utwórz grupę zasobów. Nazwa grupy zasobów musi być unikatowa w obrębie wybranej subskrypcji. |
   |**Lokalizacja**|Wschodnie stany USA||

> [!NOTE]
> Lokalizacja grupy zasobów nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

## <a name="retrieve-name-servers"></a>Pobieranie serwerów nazw

Aby móc delegować swoją strefę DNS do usługi Azure DNS, musisz znać serwery nazw dla swojej strefy. Usługa Azure DNS przydziela serwery nazw z puli za każdym razem, gdy tworzona jest strefa.

1. Gdy utworzysz strefę DNS, w okienku **Ulubione** witryny Azure Portal wybierz pozycję **Wszystkie zasoby**. Na stronie **Wszystkie zasoby** wybierz swoją strefę DNS. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wpisać nazwę Twojej domeny w polu **Filtruj według nazwy**, aby łatwo uzyskać dostęp do bramy aplikacji. 

1. Na stronie Strefa DNS pobierz serwery nazw. W tym przykładzie strefie „contoso.net” przypisano serwery nazw *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* i *ns4-01.azure-dns.info*:

   ![Lista serwerów nazw](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Usługa Azure DNS automatycznie tworzy autorytatywne rekordy NS w strefie dla przypisanych serwerów nazw.


## <a name="delegate-the-domain"></a>Delegowanie domeny

Po utworzeniu strefy DNS i pobraniu serwerów nazw należy zaktualizować domenę nadrzędną przy użyciu informacji o serwerach nazw usługi Azure DNS. Każdy rejestrator ma swoje własne narzędzia do zarządzania systemem DNS służące do zmiany rekordów serwerów nazw dla domeny. Na stronie zarządzania systemem DNS rejestratora edytuj rekordy NS i zastąp je rekordami serwerów nazw usługi Azure DNS.

Podczas delegowania domeny do usługi Azure DNS należy użyć serwerów nazw udostępnionych przez usługę Azure DNS. Zaleca się używanie wszystkich czterech serwerów nazw, niezależnie od nazwy domeny. Delegowanie domeny nie wymaga, aby serwer nazw korzystał z tej samej domeny najwyższego poziomu, co domena użytkownika.

> [!NOTE]
> Podczas kopiowania każdego z adresów serwera nazw upewnij się, że kopiujesz kropkę na końcu adresu. Końcowa kropka wskazuje koniec w pełni kwalifikowanej nazwy domeny. Niektórzy rejestratorzy mogą dołączać kropkę, jeśli nazwa NS nie ma jej na końcu. Jednak aby zachować zgodność ze specyfikacją RFC dla usługi DNS, dołącz końcową kropkę, ponieważ nie można zakładać, że każdy rejestrator doda ją w razie potrzeby.

Delegowanie z wykorzystaniem serwerów nazw we własnej strefie, niekiedy nazywanych *serwerami nazw znaczących*, nie jest obecnie obsługiwane w usłudze Azure DNS.

## <a name="verify-that-the-delegation-is-working"></a>Sprawdzanie poprawności działania delegowania

Po zakończeniu delegowania możesz sprawdzić poprawność jego działania, uruchamiając zapytanie o rekord SOA (Start of Authority) dla swojej strefy za pomocą narzędzia takiego jak *nslookup*. Rekord SOA jest automatycznie tworzony po utworzeniu strefy. Po zakończeniu delegowania może być konieczne odczekanie co najmniej 10 minut, zanim będzie można pomyślnie zweryfikować jego poprawne działanie. Jest to czas potrzebny na propagowanie zmian w systemie DNS.

Określenie serwerów nazw usługi Azure DNS nie jest konieczne. Jeśli delegowanie zostało skonfigurowane prawidłowo, normalny proces rozpoznawania nazw DNS znajdzie serwery nazw automatycznie.

W wierszu polecenia wpisz polecenie nslookup podobne do następującego:

```
nslookup -type=SOA contoso.net
```

Poniżej zamieszczono przykładową odpowiedź na poprzednie polecenie:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wykonanie następnego samouczka, możesz zachować grupę zasobów **contosoRG**. W przeciwnym razie usuń grupę zasobów **contosoRG**, co spowoduje usunięcie zasobów utworzonych w ramach tego samouczka. W tym celu kliknij grupę zasobów **contosoRG**, a następnie kliknij polecenie **Usuń grupę zasobów**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono strefę DNS dla domeny i delegowano ją do usługi Azure DNS. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)

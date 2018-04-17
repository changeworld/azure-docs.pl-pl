---
title: Samouczek — dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN | Microsoft Docs
description: W tym samouczku będziesz mapować zawartość punktu końcowego usługi Azure CDN na domenę niestandardową.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/09/2018
ms.author: mazha
ms.custom: mvc
ms.openlocfilehash: de04253a51d30885e936cb65a1925df4e5e96eaf
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN
Ten samouczek pokazuje, jak dodać domenę niestandardową do punktu końcowego usługi Azure CDN. W przypadku dostarczania zawartości przy użyciu punktu końcowego usługi CDN domena niestandardowa jest niezbędna, jeśli chcesz, aby nazwa Twojej domeny była widoczna w adresie URL usługi CDN. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem. 

Po utworzeniu punktu końcowego usługi CDN w profilu nazwa punktu końcowego, czyli domena podrzędna domeny azureedge.net, jest uwzględniana w adresie URL w celu domyślnego dostarczania zawartości usługi CDN (np. https:\//contoso.azureedge.net/photo.png). Dla Twojej wygody usługa Azure CDN udostępnia opcję kojarzenia domeny niestandardowej z punktem końcowym usługi CDN. Ta opcja umożliwia dostarczanie zawartości przy użyciu domeny niestandardowej w adresie URL zamiast korzystania z nazwy punktu końcowego (np. https:\//www.contoso.com/photo.png). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME
> - Tworzenie skojarzenia domeny niestandardowej z punktem końcowym usługi CDN
> - Weryfikowanie domeny niestandardowej

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć profil usługi CDN i co najmniej jeden punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](cdn-create-new-endpoint.md).

Jeśli nie masz jeszcze domeny niestandardowej, musisz ją najpierw kupić od dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app).

Jeśli używasz platformy Azure do hostowania [domen DNS](https://docs.microsoft.com/azure/dns/dns-overview), musisz delegować system nazw domen (DNS) dostawcy domen do usługi Azure DNS. Więcej informacji można znaleźć w temacie [Delegowanie domeny do usługi DNS platformy Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, przejdź do sekcji [Tworzenie rekordu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Tworzenie rekordu DNS CNAME

Przed rozpoczęciem używania domeny niestandardowej z punktem końcowym usługi Azure CDN należy najpierw utworzyć rekord nazwy kanonicznej (CNAME) przy użyciu dostawcy domeny, aby wskazać na punkt końcowy usługi CDN. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej. W przypadku usługi Azure CDN nazwa domeny źródłowej to nazwa domeny niestandardowej, a nazwa domeny docelowej to nazwa hosta punktu końcowego usługi CDN. Gdy usługa Azure CDN zweryfikuje tworzony rekord CNAME, ruch przeznaczony dla domeny niestandardowej źródła (np. www.contoso.com) jest kierowany do wybranej nazwy hosta docelowego punktu końcowego usługi CDN (np. contoso.azureedge.net). 

Domenę niestandardową i jej domenę podrzędną można skojarzyć tylko z jednym punktem końcowym równocześnie. Można jednak używać różnych domen podrzędnych z tej samej domeny niestandardowej dla różnych punktów końcowych platformy Azure, korzystając z wielu rekordów CNAME. Można również mapować domenę niestandardową z różnymi domenami podrzędnymi do tego samego punktu końcowego usługi CDN.

## <a name="map-temporary-cdnverify-subdomain"></a>Mapowanie tymczasowej domeny podrzędnej cdnverify

W przypadku mapowania istniejącej domeny, która znajduje się w środowisku produkcyjnym, należy wziąć pod uwagę zagadnienia specjalne. W przypadku rejestrowania domeny niestandardowej w witrynie Azure Portal może wystąpić krótki przestój domeny. Aby uniknąć zakłóceń ruchu internetowego, należy najpierw przeprowadzić mapowanie domeny niestandardowej do nazwy hosta punktu końcowego usługi CDN przy użyciu domeny podrzędnej cdnverify platformy Azure w celu utworzenia tymczasowego mapowania CNAME. Przy użyciu tej metody użytkownicy mogą uzyskiwać dostęp do domeny bez zakłóceń w trakcie mapowania DNS. 

W przeciwnym razie, jeśli używasz domeny niestandardowej po raz pierwszy i nie ma w niej żadnego ruchu związanego ze środowiskiem produkcyjnym, domenę niestandardową można mapować bezpośrednio do punktu końcowego usługi CDN. Przejdź do sekcji [Mapowanie trwałej domeny niestandardowej](#map-permanent-custom-domain).

Aby utworzyć rekord CNAME z poziomu poddomeny cdnverify:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny internetowej z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy                    | Typ  | Element docelowy                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Źródło: wprowadź nazwę domeny niestandardowej, w tym domeny podrzędnej cdnverify w następującym formacie: cdnverify._&lt;nazwa domeny niestandardowej&gt;. Na przykład: cdnverify.www.contoso.com.

    - Typ: wprowadź wartość *CNAME*.

    - Miejsce docelowe: wprowadź nazwę hosta punktu końcowego usługi CDN, w tym domeny podrzędnej cdnverify, w następującym formacie: cdnverify._&lt;nazwa punktu końcowego&gt;_.azureedge.net. Na przykład: cdnverify.contoso.azureedge.net.

4. Zapisz zmiany.

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij następujące pola wpisu CNAME:

    ![Wpis CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia, z uwzględnieniem nazwy domeny podrzędnej cdnverify. Na przykład: cdnverify.www.

    - Wskazuje na: wprowadź nazwę hosta punktu końcowego usługi CDN, z uwzględnieniem nazwy domeny podrzędnej cdnverify. Na przykład: cdnverify.contoso.azureedge.net. 

    - Czas wygaśnięcia: pozostaw wybraną wartość *1 godzina*.

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

    ![Tabela rekordów DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Tworzenie skojarzenia domeny niestandardowej z punktem końcowym usługi CDN

Po zarejestrowaniu domeny niestandardowej można dodać ją do punktu końcowego usługi CDN. 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i przejdź do profilu usługi CDN zawierającego punkt końcowy, który ma być mapowany na domenę niestandardową.
    
2. Na stronie **Profil CDN** wybierz punkt końcowy usługi CDN do skojarzenia z domeną niestandardową.

    Zostanie otwarta strona **Punkt końcowy**.
    
3. Wybierz pozycję **Domena niestandardowa**. 

   ![Przycisk domeny niestandardowej usługi CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. W obszarze **Niestandardowa nazwa hosta** wprowadź domenę niestandardową, w tym domenę podrzędną. Na przykład: www.contoso.com lub cdn.contoso.com. Nie używaj nazwy domeny podrzędnej cdnverify.

   ![Okno dialogowe domeny niestandardowej usługi CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

5. Wybierz pozycję **Dodaj**.

   Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa jest weryfikowana. Może upłynąć pewien czas, zanim rekord CNAME zostanie rozpropagowany do serwerów nazw. Jeśli domena nie zostanie zweryfikowana natychmiast, sprawdź, czy rekord CNAME jest poprawny, a następnie zaczekaj kilka minut i spróbuj ponownie. W przypadku punktów końcowych typu **Azure CDN from Verizon** wypełnienie wszystkich węzłów brzegowych usługi CDN ustawieniami domeny niestandardowej może potrwać do 90 minut.  


## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po zakończeniu rejestracji domeny niestandardowej sprawdź, czy odwołuje się ona do punktu końcowego usługi CDN.
 
1. Upewnij się, że masz publiczną zawartość buforowaną w punkcie końcowym. Jeśli na przykład punkt końcowy usługi CDN został skojarzony z kontem magazynu, usługa Azure CDN będzie buforować zawartość w kontenerze publicznym. Aby przetestować domenę niestandardową, sprawdź, czy kontener został ustawiony tak, aby zezwalać na publiczny dostęp, i czy zawiera co najmniej jeden plik.

2. W przeglądarce przejdź do adresu pliku przy użyciu domeny niestandardowej. Jeśli na przykład domena niestandardowa to cdn.contoso.com, adres URL buforowanego pliku powinien być podobny do następującego: http:\//cdn.contoso.com/my-public-container/my-file.jpg.

## <a name="map-permanent-custom-domain"></a>Mapowanie trwałej domeny niestandardowej

Jeśli sprawdzono, czy domena podrzędna cdnverify została pomyślnie mapowana do punktu końcowego (lub jeśli używasz nowej domeny niestandardowej, która nie znajduje się w środowisku produkcyjnym), można mapować domenę niestandardową bezpośrednio do nazwy hosta punktu końcowego usługi CDN.

Aby utworzyć rekord CNAME dla domeny niestandardowej:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny internetowej z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy          | Typ  | Element docelowy           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Źródło: wprowadź nazwę domeny niestandardowej (np. www.contoso.com).

    - Typ: wprowadź wartość *CNAME*.

    - Miejsce docelowe: wprowadź nazwę hosta punktu końcowego usługi CDN. Jej wymagany format to: _&lt;nazwa punktu końcowego&gt;_. azureedge.net. Na przykład: contoso.azureedge.net.

4. Zapisz zmiany.

5. Jeśli wcześniej utworzono rekord CNAME tymczasowej domeny podrzędnej cdnverify, usuń go. 

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij pola wpisu CNAME:

    ![Wpis CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia. Na przykład: www lub cdn.

    - Wskazuje na: wprowadź nazwę hosta punktu końcowego usługi CDN. Na przykład: contoso.azureedge.net. 

    - Czas wygaśnięcia: pozostaw wybraną wartość *1 godzina*.

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

    ![Tabela rekordów DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Jeśli masz rekord CNAME cdnverify, wybierz ikonę ołówka obok niej, a następnie wybierz ikonę kosza.

8. Wybierz pozycję **Usuń**, aby usunąć rekord CNAME.

Jeśli używasz tej domeny niestandardowej w środowisku produkcyjnym po raz pierwszy, wykonaj kroki z sekcji [Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN](#associate-the-custom-domain-with-your-cdn-endpoint) i [Weryfikowanie domeny niestandardowej](#verify-the-custom-domain).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach dodano domenę niestandardową do punktu końcowego usługi CDN. Jeśli nie chcesz skojarzyć punktu końcowego z domeną niestandardową, możesz usunąć domenę niestandardową, wykonując następujące kroki:
 
1. W profilu usługi CDN wybierz punkt końcowy z domeną niestandardową, która ma zostać usunięta.

2. Na stronie **Punkt końcowy** w obszarze domen niestandardowych kliknij prawym przyciskiem myszy domenę niestandardową, którą chcesz usunąć, a następnie wybierz polecenie **Usuń** z menu kontekstowego.  

   Skojarzenie domeny niestandardowej z punktem końcowym zostanie usunięte.


## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME
> - Tworzenie skojarzenia domeny niestandardowej z punktem końcowym usługi CDN
> - Weryfikowanie domeny niestandardowej

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować protokół HTTPS w domenie niestandardowej usługi Azure CDN.

> [!div class="nextstepaction"]
> [Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md)



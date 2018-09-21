---
title: Wdrażanie geograficznie rozproszonych usług AD FS o wysokiej dostępności na platformie Azure przy użyciu usługi Azure Traffic Manager | Microsoft Docs
description: W tym dokumencie omówiono procedurę wdrożenia usług AD FS na platformie Azure w celu zapewnienia wysokiej dostępności.
keywords: Usługi AD FS z menedżerem ruchu Azure, usługi AD FS z usługą Azure Traffic Manager, geograficznie, wiele centrów danych, geograficzne centra danych, geograficznie rozproszone centra danych, wdrażanie usług AD FS na platformie Azure, azure AD FS, usługi azure ad fs, wdrażania usług AD FS, wdrażanie adfs azure, azure adfs, wdrażanie adfs w azure, wdrażanie AD FS w azure, adfs azure, wprowadzenie do usługi AD FS, Azure, AD FS na platformie Azure, iaas, ADFS, przenoszenie adfs do azure
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
ms.openlocfilehash: 2ed0b551faba68c0956be89277348eeee60d759c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298221"
---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Wdrażanie geograficznie rozproszonych usług AD FS o wysokiej dostępności na platformie Azure przy użyciu usługi Azure Traffic Manager
Artykuł [Wdrożenie usług AD FS na platformie Azure](hybrid/how-to-connect-fed-azure-adfs.md) zawiera instrukcje krok po kroku dotyczące wdrażania prostej infrastruktury usług AD FS dla organizacji na platformie Azure. W tym artykule opisano następne kroki tworzenia geograficznie rozproszonego wdrożenia usług AD FS na platformie Azure przy użyciu usługi [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Usługa Azure Traffic Manager pomaga przy tworzeniu geograficznie rozproszonej infrastruktury usług AD FS o wysokiej dostępności i wydajności dla organizacji poprzez korzystanie z wielu dostępnych metod routingu dopasowanych do zróżnicowanych potrzeb infrastruktury.

Geograficznie rozproszona infrastruktura usług AD FS o wysokiej dostępności umożliwia wykonywanie następujących czynności:

* **Eliminacja pojedynczego punktu awarii:** Dzięki możliwości usługi Azure Traffic Manager do pracy w trybie failover można uzyskać infrastrukturę usług AD FS o wysokiej dostępności nawet w przypadku awarii jednego z centrów danych w danej części świata
* **Zwiększona wydajność:** Do utworzenia infrastruktury usług AD FS o wysokiej wydajności, która może ułatwić użytkownikom szybsze uwierzytelnianie, możesz użyć wdrożenia sugerowanego w tym artykule. 

## <a name="design-principles"></a>Zasady projektowania
![Ogólny projekt](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Podstawowe zasady projektowania będą takie same jak w zasadach projektowania w artykule Wdrożenie usług AD FS na platformie Azure. Na powyższym diagramie przedstawiono proste rozszerzenie podstawowego wdrożenia do innego regionu geograficznego. Poniżej wymieniono kilka kwestii, które należy wziąć pod uwagę podczas rozszerzania wdrożenia do innego regionu geograficznego

* **Sieć wirtualna:** Należy utworzyć nową sieć wirtualną w regionie geograficznym, w którym chcesz wdrożyć dodatkową infrastrukturę usług AD FS. Na powyższym diagramie sieci Geo1 VNET i Geo2 VNET są widoczne jako dwie sieci wirtualne w każdym z regionów geograficznych.
* **Kontrolery domeny i serwery usługi AD FS w nowej geograficznej sieci wirtualnej:** Zaleca się wdrożenie kontrolerów domeny w nowym regionie geograficznym, aby serwery usługi AD FS w nowym regionie nie musiały kontaktować się z kontrolerem domeny w innej odległej sieci w celu ukończenia uwierzytelniania — spowoduje to wzrost wydajności.
* **Konta magazynu:** Konta magazynu są skojarzone z regionem. Ponieważ maszyny będą wdrażane w nowym regionie geograficznym, będzie konieczne utworzenie nowych kont magazynu do użytku w tym regionie.  
* **Grupy zabezpieczeń sieci:** Ponieważ są one kontami magazynu, grup zabezpieczeń sieci utworzonych w danym regionie nie można używać w innym regionie geograficznym. W związku z tym należy utworzyć w nowym regionie geograficznym nowe grupy zabezpieczeń sieci dla podsieci INT i DMZ podobne do tych w pierwszym regionie geograficznym.
* **Etykiety DNS dla publicznych adresów IP:** Usługa Azure Traffic Manager może odwoływać się do punktów końcowych TYLKO za pośrednictwem etykiet DNS. W związku z tym należy utworzyć etykiety DNS dla publicznych adresów IP zewnętrznych modułów równoważenia obciążenia.
* **Usługa Azure Traffic Manager:** Usługa Microsoft Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu użytkowników do punktów końcowych usługi uruchomionych w różnych centrach danych na całym świecie. Usługa Azure Traffic Manager działa na poziomie usługi DNS. Przy użyciu odpowiedzi usługi DNS kieruje ona ruch użytkowników końcowych do punktów końcowych rozproszonych po całym świecie. Klienci następnie łączą się z tymi punktami końcowymi bezpośrednio. Dzięki różnym opcjom routingu, takim jak Wydajność, Ważona i Priorytet, możesz łatwo wybrać opcję odpowiednią do potrzeb Twojej organizacji. 
* **Łączność typu sieć wirtualna-sieć wirtualna między dwoma regionami:** Łączność między samymi sieciami wirtualnymi nie jest potrzebna. Ponieważ każda sieć wirtualna ma dostęp do kontrolerów domeny oraz zawiera w sobie serwer usług AD FS i serwer proxy aplikacji sieci Web, mogą one pracować bez łączności z sieciami wirtualnymi w innych regionach. 

## <a name="steps-to-integrate-azure-traffic-manager"></a>Kroki dotyczące integrowania usługi Azure Traffic Manager
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>Wdrażanie usług AD FS w nowym regionie geograficznym
Postępuj zgodnie z krokami i wskazówkami opisanymi w artykule [Wdrożenie usług AD FS na platformie Azure](hybrid/how-to-connect-fed-azure-adfs.md), aby wdrożyć tę samą topologię w nowym regionie geograficznym.

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Etykiety DNS dla publicznych adresów IP modułów równoważenia obciążenia dostępnych z Internetu (publicznych)
Jak wspomniano powyżej, usługa Azure Traffic Manager może odwoływać się tylko do etykiet DNS jako punktów końcowych, dlatego należy pamiętać o utworzeniu etykiet DNS dla publicznego adresu IP zewnętrznych modułów równoważenia obciążenia. Poniższy zrzut ekranu przedstawia sposób konfiguracji etykiety DNS dla publicznego adresu IP. 

![Etykieta DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>Wdrażanie usługi Azure Traffic Manager
Wykonaj poniższe kroki, aby utworzyć profil usługi Traffic Manager. Aby uzyskać więcej informacji, możesz również zapoznać się z artykułem [Zarządzanie profilem usługi Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Tworzenie profilu usługi Traffic Manager:** Nadaj profilowi usługi Traffic Manager unikatową nazwę. Ta nazwa profilu jest częścią nazwy DNS i działa jako prefiks dla etykiety nazwy domeny usługi Traffic Manager. Nazwa lub prefiks są dodawane do domeny .trafficmanager.net w celu utworzenia etykiety DNS dla usługi Traffic Manager. Na poniższym zrzucie ekranu prefiks DNS usługi Traffic Manager to mysts, w związku z czym etykieta DNS będzie brzmieć mysts.trafficmanager.net. 
   
    ![Tworzenie profilu usługi Traffic Manager](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **Metoda routingu ruchu:** W usłudze Traffic Manager są dostępne trzy opcje routingu:
   
   * Priorytet 
   * Wydajność
   * Ważona
     
     **Wydajność** to zalecana opcja umożliwiająca uzyskanie infrastruktury usług AD FS o wysokiej dynamice. Można jednak wybrać dowolną metodę routingu najlepiej dopasowaną do potrzeb wdrożenia. Wybrana opcja routingu nie ma wpływu na funkcje usług AD FS. Zobacz [Metody routingu ruchu w usłudze Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md), aby uzyskać więcej informacji. Na przykładowym zrzucie ekranu powyżej wybrano metodę **Wydajność**.
3. **Konfigurowanie punktów końcowych:** Na stronie usługi Traffic Manager kliknij punkty końcowe i wybierz pozycję Dodaj. Spowoduje to otwarcie strony Dodawanie punktu końcowego podobnej do tej widocznej na poniższym zrzucie ekranu
   
   ![Konfigurowanie punktów końcowych](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   Aby wprowadzić inne dane wejściowe, zapoznaj się z poniższymi wytycznymi:
   
   **Typ:** Wybierz pozycję Punkt końcowy platformy Azure, ponieważ zostanie wskazany publiczny adres IP platformy Azure.
   
   **Nazwa:** Utwórz nazwę, którą chcesz skojarzyć z punktem końcowym. To nie jest nazwa DNS i nie ma żadnego wpływu na rekordy DNS.
   
   **Typ zasobu docelowego:** Wybierz pozycję Publiczny adres IP jako wartość tej właściwości. 
   
   **Zasób docelowy:** Możesz wybrać spośród różnych etykiet DNS dostępnych w subskrypcji. Wybierz etykietę DNS odpowiadającą konfigurowanemu punktowi końcowemu.
   
   Dodaj punkt końcowy dla każdego regionu geograficznego, do którego usługa Azure Traffic Manager ma kierować ruch.
   Aby uzyskać więcej informacji oraz szczegółowy opis kroków dotyczących dodawania/konfigurowania punktów końcowych w usłudze Traffic Manager, zobacz [Dodawanie, usuwanie, włączanie i wyłączanie punktów końcowych](../traffic-manager/traffic-manager-endpoints.md)
4. **Konfigurowanie sondy:** Na stronie usługi Traffic Manager kliknij pozycję Konfiguracja. Na stronie konfiguracji należy zmienić ustawienia monitora, aby sondował protokół HTTP, port 80 i ścieżkę względną /adfs/probe
   
    ![Konfigurowanie sondy](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **Upewnij się, że po zakończeniu konfiguracji punkty końcowe są w stanie ONLINE**. Jeśli wszystkie punkty końcowe są w stanie obniżonej wydajności, usługa Azure Traffic Manager podejmie próbę kierowania ruchem, zakładając, że diagnostyka jest nieprawidłowa, a wszystkie punkty końcowe są dostępne.
   > 
   > 
5. **Modyfikowanie rekordów DNS dla usługi Azure Traffic Manager:** Usługą federacyjną powinien być rekord CNAME nazwy DNS usługi Azure Traffic Manager. Utwórz rekord CNAME w publicznych rekordach DNS, aby osoba próbująca połączyć się z usługą federacyjną połączyła się z usługą Azure Traffic Manager.
   
    Aby na przykład wskazać usługę federacyjną fs.fabidentity.com usłudze Traffic Manager, należy zaktualizować rekord zasobu DNS w następujący sposób:
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>Testowanie routingu i logowania za pomocą usług AD FS
### <a name="routing-test"></a>Test routingu
Aby wykonać bardzo prosty test routingu, spróbuj wysłać polecenie ping do nazwy DNS usługi federacyjnej z maszyny w każdym z regionów geograficznych. W zależności od wybranej metody routingu punkt końcowy, do którego wysłano polecenie ping, zostanie odzwierciedlony w wyświetlaczu ping. Jeśli na przykład wybrano metodę Wydajność, zostanie nawiązane połączenie z punktem końcowym znajdującym się najbliżej regionu klienta. Poniżej znajduje się migawka dwóch poleceń ping z maszyn w dwóch różnych regionach, jedna w regionie Azja Wschodnia i jedna w regionie Zachodnie stany USA. 

![Test routingu](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>Test logowania za pomocą usług AD FS
Najprostszym sposobem przetestowania działania usług AD FS jest użycie strony IdpInitiatedSignon.aspx. Przede wszystkim trzeba ją włączyć we właściwościach usług AD FS. Wykonaj poniższe kroki, aby zweryfikować konfigurację usług AD FS.

1. Aby włączyć tę stronę, uruchom poniższe polecenie cmdlet na serwerze usług AD FS przy użyciu programu PowerShell. 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. Przy użyciu dowolnej maszyny zewnętrznej otwórz stronę https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Powinna pojawić się poniższa strona usług AD FS:
   
    ![Test usług AD FS — żądanie uwierzytelnienia](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    Po pomyślnym zalogowaniu zostanie wyświetlony poniższy komunikat:
   
    ![Test usług AD FS — uwierzytelnianie powiodło się](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>Powiązane linki
* [Podstawowe wdrożenie usług AD FS na platformie Azure](hybrid/how-to-connect-fed-azure-adfs.md)
* [Microsoft Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [Metody routingu ruchu w usłudze Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>Następne kroki
* [Zarządzanie profilem usługi Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md)
* [Dodawanie, usuwanie, włączanie i wyłączanie punktów końcowych](../traffic-manager/traffic-manager-endpoints.md) 


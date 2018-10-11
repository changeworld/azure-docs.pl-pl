---
title: Samouczek — tworzenie rekordu aliasu usługi Azure DNS do obsługi nazw wierzchołków domeny przy użyciu usługi Traffic Manager
description: W tym samouczku przedstawiono sposób konfigurowania rekordu aliasu usługi Azure DNS do obsługi nazwy wierzchołka domeny przy użyciu usługi Traffic Manager.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967442"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Samouczek: konfigurowanie rekordu aliasu w celu obsługi nazw domen wierzchołkowych przy użyciu usługi Traffic Manager 

Można utworzyć rekord aliasu wierzchołka nazwy domeny (na przykład contoso.com), aby odwoływać się do profilu usługi Traffic Manager. Dlatego zamiast używać w tym celu usługi przekierowania, można skonfigurować usługę Azure DNS, aby odwoływać się do profilu usługi Traffic Manager bezpośrednio z poziomu strefy. 


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej hosta i infrastruktury sieciowej
> * Tworzenie profilu usługi Traffic Manager
> * Tworzenie rekordu aliasu
> * Testowanie rekordu aliasu


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną, w tym możliwość ustawiania dla domeny rekordów serwera nazw.

Aby uzyskać instrukcje dotyczące hostowania własnej domeny w usłudze Azure DNS, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

Przykładowa domena używana w tym samouczku to contoso.com, ale należy korzystać z własnej nazwy domeny.

## <a name="create-the-network-infrastructure"></a>Tworzenie infrastruktury sieci
Najpierw utwórz sieć wirtualną i podsieć, aby umieścić w nich serwery internetowe.
1. Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.
2. W lewym górnym rogu portalu kliknij pozycję **Utwórz zasób**, wpisz *grupa zasobów* w polu wyszukiwania, a następnie utwórz grupę zasobów o nazwie **RG-DNS-Alias TM**.
3. Kliknij pozycję **Utwórz zasób**, kliknij pozycję **Sieć**, a następnie kliknij pozycję **Sieć wirtualna**.
4. Utwórz sieć wirtualną o nazwie **VNet-Servers**, umieść ją w grupie zasobów **RG-DNS-Alias-TM** i nadaj podsieci nazwę **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych serwera wirtualnego
1. Kliknij pozycję **Utwórz zasób**, a następnie kliknij pozycję **Maszyna wirtualna z systemem Windows Server 2016**.
2. Wpisz nazwę **Web-01** i umieść maszynę wirtualną w grupie zasobów **RG-DNS-Alias-TM**. Wpisz nazwę użytkownika i hasło, a następnie kliknij przycisk **OK**.
3. W obszarze **Rozmiar** wybierz jednostkę SKU z 8 GB pamięci RAM.
4. W obszarze **Ustawienia** wybierz sieć wirtualną **VNet-Servers** i podsieć **SN-Web**.
5. Kliknij pozycję **publiczny adres IP**, a następnie w obszarze **Przypisanie** kliknij kolejno pozycje **Statyczne** i **OK**.
6. W przypadku publicznych portów wejściowych wybierz pozycje **HTTP**, **HTTPS** i **RDP (3389)**, a następnie kliknij przycisk **OK**.
7. Na stronie podsumowania kliknij pozycję **Utwórz**.

   Wykonanie tej operacji może zająć kilka minut.
6. Powtórz tę procedurę, aby utworzyć inną maszynę wirtualną o nazwie **Web-02**.

### <a name="add-a-dns-label"></a>Dodawanie etykiety DNS
Publiczne adresy IP potrzebują etykiet DNS do pracy z usługą Traffic Manager.
1. W grupie zasobów **RG-DNS-Alias TM** kliknij publiczny adres IP **Web-01-ip**.
2. W obszarze **Ustawienia** kliknij pozycję **Konfiguracja**.
3. W polu tekstowym etykiety nazwy DNS wpisz **web01pip**.
4. Kliknij pozycję **Zapisz**.

Powtórz tę procedurę dla publicznego adresu IP **Web-02-ip**, używając ciągu **web02pip** jako etykiety nazwy DNS.

### <a name="install-iis"></a>Instalowanie usług IIS

Zainstaluj usługi IIS na maszynach **Web-01** i **Web-02**.

1. Połącz się z maszyną wirtualną **Web-01** i zaloguj się.
2. Na pulpicie nawigacyjnym Menedżera serwera kliknij pozycję **Dodaj role i funkcje**.
3. Kliknij przycisk **Dalej** trzy razy i na stronie **Role serwera** wybierz pozycję **Serwer internetowy (IIS)**.
4. Kliknij pozycję **Dodaj funkcje** i kliknij przycisk **Dalej**.
5. Kliknij przycisk **Dalej** cztery razy, a następnie kliknij pozycję **Zainstaluj**.

   Ukończenie tej operacji potrwa kilka minut.
6. Po zakończeniu instalacji kliknij przycisk **Zamknij**.
7. Otwórz przeglądarkę internetową i przejdź do lokalizacji **localhost**, aby sprawdzić, czy zostanie wyświetlona domyślna strona internetowa usług IIS.

Powtórz ten proces, aby zainstalować usługi IIS na maszynie wirtualnej **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Wymagane 

1. Otwórz grupę zasobów **RG-DNS-Alias-TM** i kliknij publiczny adres IP **Web-01-ip**. Zanotuj adres IP do późniejszego użycia. Powtórz tę procedurę dla publicznego adresu IP **Web-02-ip**.
1. Kliknij pozycję **Utwórz zasób**, kliknij pozycję **Sieć**, a następnie kliknij pozycję **Profil usługi Traffic Manager**.
2. Wpisz nazwę **TM-alias-test** i umieść maszynę wirtualną w grupie zasobów **RG-DNS-Alias-TM**.
3. Kliknij pozycję **Utwórz**.
4. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu**.
5. Na stronie profilu usługi Traffic Manager w obszarze **Ustawienia** kliknij pozycję **Punkty końcowe**.
6. Kliknij pozycję **Add** (Dodaj).
7. W obszarze **Typ** wybierz pozycję **Zewnętrzny punkt końcowy** i w polu **Nazwa** wpisz **EP-Web01**.
8. W polu tekstowym **W pełni kwalifikowana nazwa domeny (FQDN) lub adres IP** wpisz zanotowany wcześniej adres IP **Web-01-ip**.
9. Wybierz tę samą **lokalizację**, co w przypadku innych zasobów, a następnie kliknij przycisk **OK**.

Powtórz tę procedurę, aby dodać punkt końcowy **Web-02**, używając zanotowanego wcześniej adresu IP **Web-02-ip**.

## <a name="create-an-alias-record"></a>Tworzenie rekordu aliasu

Utwórz rekord aliasu wskazujący na profil usługi Traffic Manager.

1. Kliknij strefę usługi Azure DNS, aby ją otworzyć.
2. Kliknij pozycję **Zestaw rekordów**.
3. Pozostaw puste pole tekstowe **Nazwa**, aby reprezentować wierzchołek nazwy domeny (np. contoso.com).
4. Pozostaw pole **Typ** ustawione na rekord **A**.
5. Kliknij pole wyboru **Zestaw rekordów aliasów**.
6. Kliknij pozycję **Wybierz usługę platformy Azure** i wybierz profil usługi Traffic Manager **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testowanie rekordu aliasu

1. Z poziomu przeglądarki internetowej przejdź do wierzchołka nazwy domeny (np. contoso.com). Powinna zostać wyświetlona domyślna strona internetowa usługi IIS. Zamknij przeglądarkę internetową.
2. Zamknij maszynę wirtualną **Web-01** i zaczekaj kilka minut na jej całkowite zamknięcie.
3. Otwórz nową przeglądarkę internetową i przejdź ponownie do wierzchołka nazwy domeny.
4. Powinna ponownie pojawić się domyślna strona usług IIS, ponieważ usługa Traffic Manager obsługiwała tę sytuację i kierowała ruch do maszyny wirtualnej **Web-02**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli grupa zasobów **RG-DNS-Alias-TM** nie jest już potrzebna, możesz ją usunąć, aby usunąć wszystkie zasoby utworzone w tym samouczku.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono rekord aliasu umożliwiający użycie nazwy domeny wierzchołkowej do odwoływania się do profilu usługi Traffic Manager. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)

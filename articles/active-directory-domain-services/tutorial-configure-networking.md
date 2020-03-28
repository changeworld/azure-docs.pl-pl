---
title: Samouczek — konfigurowanie sieci wirtualnej dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć i skonfigurować podsieć sieci wirtualnej platformy Azure lub komunikacji równorzędnej sieci dla wystąpienia usług domenowych usługi active directory platformy Azure przy użyciu witryny Azure portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 26122278ad74fb1d383ca7a900810b6060ee78f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73172675"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Samouczek: Konfigurowanie sieci wirtualnej dla wystąpienia usług domenowych usługi domenowej Active Directory platformy Azure

Aby zapewnić łączność użytkownikom i aplikacjom, domena zarządzana usługami domenowymi usługi Azure AD (Azure AD DS) jest wdrażana w podsieci sieci wirtualnej platformy Azure. Ta podsieć sieci wirtualnej powinna być używana tylko dla zasobów domeny zarządzanej dostarczanych przez platformę Azure. Podczas tworzenia własnych maszyn wirtualnych i aplikacji nie powinny być wdrażane w tej samej podsieci sieci wirtualnej. Zamiast tego należy utworzyć i wdrożyć aplikacje w oddzielnej podsieci sieci wirtualnej lub w oddzielnej sieci wirtualnej, która jest równorzędna z siecią wirtualną usługi Azure AD DS.

W tym samouczku pokazano, jak utworzyć i skonfigurować dedykowaną podsieć sieci wirtualnej lub jak utworzyć inną sieć z siecią wirtualną domeny zarządzanej usługą Azure AD DS.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Poznaj opcje łączności sieci wirtualnej dla zasobów przyłączonych do domeny w usługach Ad DS
> * Tworzenie zakresu adresów IP i dodatkowej podsieci w sieci wirtualnej usług Azure AD DS
> * Konfigurowanie komunikacji równorzędnej sieci wirtualnej w sieci oddzielonej od usług Azure AD DS

Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Aby włączyć usługi Azure AD DS, potrzebujesz uprawnień *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usług Azure AD DS, potrzebujesz uprawnień *współautora* w ramach subskrypcji platformy Azure.
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby pierwszy samouczek [tworzy i konfiguruje wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku należy utworzyć i skonfigurować wystąpienie usług Azure AD DS przy użyciu witryny Azure portal. Aby rozpocząć, najpierw zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Opcje łączności obciążenia aplikacji

W poprzednim samouczku utworzono domenę zarządzaną usługą Azure AD DS, która używała niektórych domyślnych opcji konfiguracji dla sieci wirtualnej. Te opcje domyślne utworzyły sieć wirtualną platformy Azure i podsieć sieci wirtualnej. Kontrolery domeny usług Ad DS platformy Azure, które zapewniają usługi domeny zarządzanej są połączone z tej podsieci sieci wirtualnej.

Podczas tworzenia i uruchamiania maszyn wirtualnych, które muszą korzystać z domeny zarządzanej usług Azure AD DS, łączność sieciowa musi być podana. Ta łączność sieciowa może być zapewniona w jeden z następujących sposobów:

* Utwórz dodatkową podsieć sieci wirtualnej w domyślnej sieci wirtualnej domeny zarządzanej usługą Azure AD DS. Ta dodatkowa podsieć jest, gdzie można utworzyć i połączyć maszyn wirtualnych.
    * Ponieważ maszyny wirtualne są częścią tej samej sieci wirtualnej, mogą automatycznie wykonywać rozpoznawanie nazw i komunikować się z kontrolerami domeny usług Azure AD DS.
* Skonfiguruj komunikację równorzędnych sieci wirtualnej platformy Azure z sieci wirtualnej domeny zarządzanej usługą Azure AD DS na jedną lub więcej oddzielnych sieci wirtualnych. Te oddzielne sieci wirtualne są miejscem tworzenia i łączenia maszyn wirtualnych.
    * Podczas konfigurowania komunikacji równorzędnej sieci wirtualnej należy również skonfigurować ustawienia DNS, aby używać rozpoznawania nazw z powrotem do kontrolerów domeny usług Azure AD DS.

Zwykle używasz tylko jednej z tych opcji łączności sieciowej. Wybór często zależy od tego, jak chcesz zarządzać oddzielnymi zasobami platformy Azure. Jeśli chcesz zarządzać usługami Azure AD DS i połączonymi maszynami wirtualnymi jako jedną grupą zasobów, możesz utworzyć dodatkową podsieć sieci wirtualnej dla maszyn wirtualnych. Jeśli chcesz oddzielić zarządzanie usługą Azure AD DS, a następnie wszystkie podłączone maszyny wirtualne, możesz użyć komunikacji równorzędnej w sieci wirtualnej. Można również użyć komunikacji równorzędnej sieci wirtualnej, aby zapewnić łączność z istniejącymi maszynami wirtualnymi w środowisku platformy Azure, które są połączone z istniejącą siecią wirtualną.

W tym samouczku wystarczy skonfigurować jedną z tych opcji łączności sieci wirtualnej.

Aby uzyskać więcej informacji na temat planowania i konfigurowania sieci wirtualnej, zobacz [zagadnienia dotyczące sieci dla usług domenowych Active Directory platformy Azure][zagadnienia dotyczące sieci].

## <a name="create-a-virtual-network-subnet"></a>Tworzenie podsieci sieci wirtualnej

Domyślnie sieć wirtualna platformy Azure utworzona za pomocą domeny zarządzanej usługi Azure AD DS zawiera pojedynczą podsieć sieci wirtualnej. Ta podsieć sieci wirtualnej powinna być używana tylko przez platformę Azure w celu świadczenia usług domeny zarządzanej. Aby utworzyć i używać własnych maszyn wirtualnych w tej sieci wirtualnej platformy Azure, należy utworzyć dodatkową podsieć.

Aby utworzyć podsieć sieci wirtualnej dla maszyn wirtualnych i obciążeń aplikacji, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz grupę zasobów domeny zarządzanej usług Azure AD DS, takiej jak *myResourceGroup*. Z listy zasobów wybierz domyślną sieć wirtualną, taką jak *aadds-vnet*.
1. W menu po lewej stronie okna sieci wirtualnej wybierz pozycję **Przestrzeń adresowa**. Sieć wirtualna jest tworzona z pojedynczą przestrzenią adresową *10.0.1.0/24*, która jest używana przez domyślną podsieć.

    Dodaj dodatkowy zakres adresów IP do sieci wirtualnej. Rozmiar tego zakresu adresów i rzeczywistego zakresu adresów IP, którego należy użyć, zależy od innych już wdrożonych zasobów sieciowych. Zakres adresów IP nie powinien pokrywać się z istniejącymi zakresami adresów w środowisku platformy Azure lub lokalnym. Upewnij się, że rozmiar zakresu adresów IP wystarczająco duży dla liczby maszyn wirtualnych, które mają być wdrażane w podsieci.

    W poniższym przykładzie dodano dodatkowy zakres adresów IP *10.0.2.0/24.* Gdy będzie gotowy, wybierz pozycję **Zapisz**.

    ![Dodawanie dodatkowego zakresu adresów IP sieci wirtualnej w witrynie Azure portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Następnie w menu po lewej stronie okna sieci wirtualnej wybierz pozycję **Podsieci**, a następnie wybierz + **Podsieć,** aby dodać podsieć.
1. Wprowadź nazwę podsieci, taką jak *obciążenia*. W razie potrzeby **zaktualizuj zakres adresów,** jeśli chcesz użyć podzbioru zakresu adresów IP skonfigurowanego dla sieci wirtualnej w poprzednich krokach. Na razie pozostaw domyślne opcje, takie jak grupa zabezpieczeń sieci, tabela tras, punkty końcowe usługi.

    W poniższym przykładzie tworzony jest *podsieć* o nazwie obciążeń, który używa zakresu adresów IP *10.0.2.0/24:*

    ![Dodawanie dodatkowej podsieci sieci wirtualnej w witrynie Azure portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Gdy będzie gotowy, wybierz przycisk **OK**. Utworzenie podsieci sieci wirtualnej zajmuje kilka chwil.

Podczas tworzenia maszyny Wirtualnej, która musi używać domeny zarządzanej usług Azure AD DS, upewnij się, że wybierz tę podsieć sieci wirtualnej. Nie należy tworzyć maszyn wirtualnych w *domyślnej podsieci aadds*. Jeśli wybierzesz inną sieć wirtualną, nie ma łączności sieciowej i rozpoznawania DNS, aby dotrzeć do domeny zarządzanej usług Azure AD DS, chyba że skonfigurujesz komunikację równorzędną sieci wirtualnej.

## <a name="configure-virtual-network-peering"></a>Konfigurowanie komunikacji równorzędnej w sieci wirtualnej

Być może masz istniejącą sieć wirtualną platformy Azure dla maszyn wirtualnych lub chcesz zachować sieć wirtualną domeny zarządzanej usługą Azure AD DS oddzielnie. Aby korzystać z domeny zarządzanej, maszyny wirtualne w innych sieciach wirtualnych potrzebują sposobu komunikowania się z kontrolerami domeny usług Azure AD DS. Ta łączność może być podana przy użyciu komunikacji równorzędnej sieci wirtualnej platformy Azure.

Dzięki komunikacji równorzędnej sieci wirtualnej platformy Azure dwie sieci wirtualne są ze sobą połączone, bez konieczności korzystania z urządzenia wirtualnej sieci prywatnej (VPN). Komunikacja równorzędna sieci umożliwia szybkie łączenie sieci wirtualnych i definiowanie przepływów ruchu w środowisku platformy Azure. Aby uzyskać więcej informacji na temat komunikacji równorzędnej, zobacz [omówienie komunikacji równorzędnej sieci wirtualnej platformy Azure][peering-overview].

Aby równorzędować sieć wirtualną z siecią wirtualną domeny zarządzanej usługą Azure AD DS, wykonaj następujące czynności:

1. Wybierz domyślną sieć wirtualną utworzoną dla wystąpienia usług Azure AD DS o nazwie *aadds-vnet*.
1. W menu po lewej stronie okna sieci wirtualnej wybierz pozycję **Peerings**.
1. Aby utworzyć komunikację równorzędną, wybierz pozycję **+ Dodaj**. W poniższym przykładzie *domyślna sieć aadds-vnet* jest równorzędna z siecią wirtualną o nazwie *myVnet*. Skonfiguruj następujące ustawienia z własnymi wartościami:

    * **Nazwa komunikacji równorzędnej z sieci aadds-vnet do zdalnej sieci wirtualnej:** opisowy identyfikator dwóch sieci, takich jak *aadds-vnet-to-myvnet*
    * **Typ wdrożenia sieci wirtualnej**: *Menedżer zasobów*
    * **Subskrypcja:** subskrypcja sieci wirtualnej, z którą chcesz się równorzędnie, na przykład *platformy Azure*
    * **Sieć wirtualna**: sieć wirtualna, z którą chcesz się równorzędnie, na przykład *myVnet*
    * **Nazwa komunikacji równorzędnej z myVnet do aadds-vnet:** opisowy identyfikator dwóch sieci, takich jak *myvnet-to-aadds-vnet*

    ![Konfigurowanie komunikacji równorzędnej sieci wirtualnej w witrynie Azure portal](./media/tutorial-configure-networking/create-peering.png)

    Pozostaw wszystkie inne ustawienia domyślne dostępu do sieci wirtualnej lub ruchu przesyłanym dalej, chyba że masz określone wymagania dla danego środowiska, a następnie wybierz **przycisk OK**.

1. Utworzenie komunikacji równorzędnej zarówno w sieci wirtualnej usługi Azure AD DS, jak i w wybranej sieci wirtualnej zajmuje kilka chwil. Gdy jest to gotowe, **raporty stanu komunikacji równorzędnej** *Połączone*, jak pokazano w poniższym przykładzie:

    ![Pomyślnie połączone sieci równorzędne w witrynie Azure portal](./media/tutorial-configure-networking/connected-peering.png)

Zanim maszyny wirtualne w sieci wirtualnej równorzędnej będą mogły korzystać z domeny zarządzanej usługi Azure AD DS, skonfiguruj serwery DNS tak, aby zezwalały na prawidłowe rozpoznawanie nazw.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Konfigurowanie serwerów DNS w sieci wirtualnej równorzędnej

Aby maszyny wirtualne i aplikacje w sieci wirtualnej w elementach równorzędnych pomyślnie rozmawiały z domeną zarządzana usługą Azure AD DS, ustawienia DNS muszą zostać zaktualizowane. Adresy IP kontrolerów domeny usług Azure AD DS muszą być skonfigurowane jako serwery DNS w sieci wirtualnej równorzędnej. Kontrolery domeny można skonfigurować jako serwery DNS dla sieci wirtualnej równorzędnej:

* Skonfiguruj serwery DNS sieci wirtualnej platformy Azure do używania kontrolerów domeny usług Azure AD DS.
* Skonfiguruj istniejący serwer DNS używany w sieci wirtualnej równorzędnej, aby używał warunkowego przekazywania dns do kierowania zapytań do domeny zarządzanej usług Azure AD DS. Te kroki różnią się w zależności od istniejącego używanego serwera DNS.

W tym samouczku skonfigurujmy serwery DNS sieci wirtualnej platformy Azure, aby kierować wszystkie zapytania do kontrolerów domeny usług Azure AD DS.

1. W witrynie Azure portal wybierz grupę zasobów sieci wirtualnej równorzędnej, taką jak *myResourceGroup*. Z listy zasobów wybierz sieć wirtualną równorzędnej, taką jak *myVnet*.
1. W menu po lewej stronie okna sieci wirtualnej wybierz pozycję **Serwery DNS**.
1. Domyślnie sieć wirtualna używa wbudowanych serwerów DNS dostarczonych przez platformę Azure. Wybierz opcję **używania niestandardowych** serwerów DNS. Wprowadź adresy IP kontrolerów domeny usług Azure AD DS, które zwykle są *10.0.1.4* i *10.0.1.5*. Potwierdź te adresy IP w oknie **Przegląd** domeny zarządzanej usług Azure AD DS w portalu.

    ![Konfigurowanie serwerów DNS w sieci wirtualnej do używania kontrolerów domeny usług Azure AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Gdy będzie gotowy, wybierz pozycję **Zapisz**. Aktualizacja serwerów DNS dla sieci wirtualnej zajmuje kilka chwil.
1. Aby zastosować zaktualizowane ustawienia DNS do maszyn wirtualnych, uruchom ponownie maszyny wirtualne połączone z siecią wirtualną równorzędnej.

Podczas tworzenia maszyny Wirtualnej, która musi używać domeny zarządzanej usług Azure AD DS, upewnij się, że wybierz tę sieć wirtualną równorzędną. Jeśli wybierzesz inną sieć wirtualną, nie ma łączności sieciowej i rozpoznawania DNS, aby dotrzeć do domeny zarządzanej usług Azure AD DS.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Poznaj opcje łączności sieci wirtualnej dla zasobów przyłączonych do domeny w usługach Ad DS
> * Tworzenie zakresu adresów IP i dodatkowej podsieci w sieci wirtualnej usług Azure AD DS
> * Konfigurowanie komunikacji równorzędnej sieci wirtualnej w sieci oddzielonej od usług Azure AD DS

Aby wyświetlić tę domenę zarządzaną w akcji, utwórz i dołącz maszynę wirtualną do domeny.

> [!div class="nextstepaction"]
> [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md

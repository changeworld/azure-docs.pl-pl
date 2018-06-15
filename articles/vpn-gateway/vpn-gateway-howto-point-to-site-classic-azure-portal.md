---
title: 'Łączenie komputera z siecią wirtualną przy użyciu połączenia typu punkt-lokacja i uwierzytelniania certyfikatu: klasyczna witryna Azure Portal | Microsoft Docs'
description: Utwórz klasyczne połączenie bramy sieci VPN typu punkt-lokacja przy użyciu witryny Azure Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: fe460113441933d655b183e87cceefee4dd24d24
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29464331"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-classic-azure-portal"></a>Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu uwierzytelniania certyfikatu (klasyczna): Azure Portal

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

W tym artykule pokazano sposób tworzenia sieci wirtualnej z połączeniem punkt-lokacja w klasycznym modelu wdrażania przy użyciu witryny Azure Portal. Ta konfiguracja korzysta z certyfikatów z podpisem własnym lub wystawionych przez urząd certyfikacji do uwierzytelniania klienta nawiązującego połączenie. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Brama sieci VPN typu punkt-lokacja (P2S, Point-to-Site) pozwala utworzyć bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenia sieci VPN typu punkt-lokacja przydają się w przypadku, gdy celem użytkownika jest połączenie się z siecią wirtualną z lokalizacji zdalnej, podczas pracy zdalnej z domu lub konferencji. Połączenie sieci VPN typu punkt-lokacja jest również przydatne zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Połączenie sieci VPN typu punkt-lokacja jest nawiązywane przez zainicjowanie go z komputera klienckiego.

> [!IMPORTANT]
> Klasyczny model wdrożenia obsługuje tylko klientów sieci VPN systemu Windows i używa protokołu Secure Socket Tunneling Protocol (SSTP) będącego protokołem sieci VPN opartym na protokole SSL. Aby zapewnić obsługę innych klientów niż sieć VPN systemu Windows, Twoja sieć wirtualna musi zostać utworzona przy użyciu modelu wdrażania usługi Resource Manager. Model wdrażania usługi Resource Manager obsługuje, oprócz protokołu SSTP, protokół IKEv2 sieci VPN. Aby uzyskać więcej informacji, zobacz [About P2S connections (Informacje o połączeniach punkt-lokacja)](point-to-site-about.md).
>
>

![Diagram: połączenie typu punkt-lokacja](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)


Połączenia typu punkt-lokacja z uwierzytelnianiem certyfikatu mają następujące wymagania:

* Brama dynamicznej sieci VPN.
* Klucz publiczny (plik cer) dla certyfikatu głównego, przekazany na platformę Azure. Jest uznawany za certyfikat zaufany i używany do uwierzytelniania.
* Certyfikat klienta wygenerowany na podstawie certyfikatu głównego i zainstalowany na każdym komputerze klienckim, który będzie nawiązywać połączenie. Ten certyfikat jest używany do uwierzytelniania klientów.
* Pakiet konfiguracji klienta sieci VPN musi zostać wygenerowany i zainstalowany na każdym komputerze klienckim, który nawiązuje połączenie. Pakiet konfiguracji klienta konfiguruje natywnego klienta sieci VPN, który znajduje się już w systemie operacyjnym klienta, ustawiając w nim informacje niezbędne do łączenia się z siecią wirtualną.

Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani lokalnego publicznego adresu IP. Połączenie sieci VPN jest nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol). Po stronie serwera obsługiwany jest protokół SSTP w wersji 1.0, 1.1 i 1.2. Klient decyduje o wyborze wersji do użycia. W przypadku systemu Windows 8.1 i nowszych protokół SSTP domyślnie używa wersji 1.2. 

Więcej informacji na temat połączeń typu punkt-lokacja znajduje się w sekcji [Często zadawane pytania dotyczące połączeń typu punkt-lokacja](#faq) na końcu tego artykułu.

### <a name="example-settings"></a>Przykładowe ustawienia

Następujących wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule:

* **Nazwa: VNet1**
* **Przestrzeń adresowa: 192.168.0.0/16**<br>W tym przykładzie zostanie wykorzystana tylko jedna przestrzeń adresowa. Istnieje możliwość użycia więcej niż jednej przestrzeni adresowej dla sieci wirtualnej, jak pokazano na diagramie.
* **Nazwa podsieci: FrontEnd**
* **Zakres adresów podsieci: 192.168.1.0/24**
* **Subskrypcja:** jeśli masz więcej niż jedną subskrypcję, sprawdź, czy korzystasz z właściwej.
* **Grupa zasobów: TestRG**
* **Lokalizacja: Wschodnie stany USA**
* **Typ połączenia: Punkt-lokacja**
* **Przestrzeń adresowa klienta: 172.16.201.0/24**. Klienci sieci VPN połączeni z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z określonej puli.
* **GatewaySubnet: 192.168.200.0/24**. Podsieć bramy musi używać nazwy „GatewaySubnet”.
* **Rozmiar:** Wybierz jednostkę SKU bramy, która ma być używana.
* **Typ routingu: Dynamiczny**

## <a name="vnetvpn"></a>1. Tworzenie sieci wirtualnej i bramy VPN Gateway

Przed rozpoczęciem sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="createvnet"></a>Część 1. Tworzenie sieci wirtualnej

Jeśli nie masz jeszcze sieci wirtualnej, utwórz ją. Zamieszczone zrzuty ekranu są przykładowe. Przedstawione wartości należy zastąpić własnymi. Aby utworzyć sieć wirtualną przy użyciu witryny Azure Portal, wykonaj poniższe kroki:

1. W przeglądarce przejdź do witryny [Azure Portal](http://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**. 
3. Z listy **Wybierz model wdrożenia** w dolnej części strony Sieć wirtualna wybierz pozycję **Klasyczny**, a następnie kliknij przycisk **Utwórz**.

  ![Wybieranie modelu wdrożenia](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Na tej stronie dodaj pierwszą przestrzeń adresową i zakres adresów pojedynczej podsieci. Po zakończeniu tworzenia sieci wirtualnej możesz wrócić i dodać dodatkowe podsieci oraz przestrzenie adresowe.

  ![Strona Tworzenie sieci wirtualnej](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Sprawdź, czy pole **Subskrypcja** zawiera prawidłową wartość. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
6. Kliknij przycisk **Grupy zasobów** i wybierz istniejącą grupę zasobów lub utwórz nową, wpisując nazwę nowej grupy zasobów. Jeśli tworzysz nową grupę zasobów, nadaj jej nazwę odpowiadającą wartościom planowanej konfiguracji. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Następnie w polu **Lokalizacja** wybierz ustawienia sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.
8. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, jeśli chcesz, aby Twoją sieć wirtualną można było łatwo znaleźć na pulpicie nawigacyjnym, a następnie kliknij przycisk **Utwórz**.

  ![Przypnij do pulpitu nawigacyjnego](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Po kliknięciu przycisku Utwórz na pulpicie nawigacyjnym zostanie umieszczony kafelek, który będzie odzwierciedlać postęp Twojej sieci wirtualnej. Wygląd kafelka zmienia się w trakcie tworzenia sieci wirtualnej.

  ![Kafelek tworzenia sieci wirtualnej](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Po utworzeniu sieci wirtualnej zobaczysz komunikat **Utworzono**.
11. Dodaj serwer DNS (opcjonalnie). Po utworzeniu sieci wirtualnej możesz dodać adres IP serwera DNS na potrzeby rozpoznawania nazw. Określony adres IP serwera DNS powinien rozpoznawać nazwy zasobów w sieci wirtualnej.<br>Aby dodać serwer DNS, otwórz ustawienia dla swojej sieci wirtualnej, kliknij serwery DNS i dodaj adres IP serwera DNS, którego chcesz użyć.

### <a name="gateway"></a>Część 2. Tworzenie podsieci bramy i bramy o dynamicznym routingu

W tym kroku tworzona jest podsieć bramy i brama o dynamicznym routingu. W witrynie Azure Portal w przypadku klasycznego modelu wdrażania tworzenie podsieci bramy i bramy można wykonać za pomocą tych samych stron konfiguracji. Podsieć bramy jest używana tylko w odniesieniu do usług bramy. Nigdy nie należy wdrażać żadnych elementów, takich jak maszyny wirtualne lub inne usługi, bezpośrednio w podsieci bramy.

1. W witrynie portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę.
2. Na stronie **Przegląd** dla odpowiedniej sieci wirtualnej, w sekcji Połączenia VPN kliknij pozycję **Brama**.

  ![Kliknij w celu utworzenia bramy](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na stronie **Nowe połączenie VPN**wybierz pozycję **Punkt-lokacja**.

  ![Połączenie typu punkt-lokacja](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. W obszarze **Przestrzeń adresowa klienta** dodaj zakres adresów IP. Jest to zakres, z którego klienci VPN otrzymują adres IP podczas nawiązywania połączenia. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której będziesz się łączyć, ani na sieć wirtualną, z którą chcesz się łączyć. Automatycznie wypełniony zakres możesz usunąć, a następnie dodać zakres prywatnych adresów IP, z którego chcesz korzystać. Ten przykład przedstawia automatycznie wypełniony zakres. Usuń go, aby dodać inną wartość.

  ![Przestrzeń adresowa klienta](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Zaznacz pole wyboru **Utwórz bramę natychmiast**. Kliknij pozycję **Opcjonalna konfiguracja bramy**, aby otworzyć stronę **Konfiguracja bramy**.

  ![Kliknij pozycję Opcjonalna konfiguracja bramy](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
6. Kliknij pozycje **Podsieć > Skonfiguruj wymagane ustawienia**, aby dodać **podsieć bramy**. Chociaż możliwe jest utworzenie małej podsieci bramy /29, zaleca się wybranie podsieci przynajmniej /28 lub /27, aby zawierała więcej adresów. Zapewni to wystarczająco dużo adresów, aby możliwe były dodatkowe konfiguracje, które mogą być potrzebne w przyszłości. Podczas pracy z podsieciami bramy należy unikać kojarzenia sieciowej grupy zabezpieczeń (NSG, Network Security Group) z podsiecią bramy. Skojarzenie sieciowej grupy zabezpieczeń z tą podsiecią spowoduje, że brama sieci VPN przestanie działać zgodnie z oczekiwaniami.

  ![Dodawanie podsieci GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Wybierz **rozmiar** bramy. Rozmiar to jednostka SKU bramy sieci wirtualnej. W portalu domyślną jednostką SKU jest **Podstawowa**. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Informacje o ustawieniach bramy VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Rozmiar bramy](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Wybierz **Typ routingu** dla bramy. Konfiguracje P2S wymagają typu routingu **Dynamiczny**. Kliknij przycisk **OK** po zakończeniu konfigurowania tej strony.

  ![Konfigurowanie typu routingu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
9. Na stronie **Nowe połączenie VPN** kliknij przycisk **OK** u dołu strony, aby rozpocząć tworzenie bramy sieci wirtualnej. Tworzenie bramy sieci VPN może zająć do 45 minut, zależnie od wybranej jednostki sku bramy.

## <a name="generatecerts"></a>2. Tworzenie certyfikatów

Certyfikaty są używane przez platformę Azure do uwierzytelniania klientów sieci VPN w obrębie sieci VPN typu punkt-lokacja. Informacje o kluczu publicznym certyfikatu głównego należy przekazać na platformę Azure. Klucz publiczny jest wtedy uważany za „zaufany”. Certyfikaty klienta muszą być generowane na podstawie zaufanego certyfikatu głównego, a następnie instalowane na każdym komputerze klienckim w magazynie certyfikatów Certificates-Current User/Personal. Certyfikat jest używany do uwierzytelniania klienta, gdy inicjuje on połączenie z siecią wirtualną. 

Jeśli używasz certyfikatów z podpisem własnym, należy je utworzyć przy użyciu określonych parametrów. Certyfikat z podpisem własnym można utworzyć przy użyciu instrukcji dotyczących [środowiska PowerShell i systemu Windows 10](vpn-gateway-certificates-point-to-site.md) lub polecenia [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Ważne jest, aby wykonać kroki opisane w tych instrukcjach w przypadku pracy z certyfikatami głównymi z podpisem własnym i generowania certyfikatów klienta z certyfikatu głównego z podpisem własnym. W przeciwnym razie utworzone przez Ciebie certyfikaty nie będą zgodne z połączeniami typu punkt-lokacja i zostanie wyświetlony błąd połączenia.

### <a name="cer"></a>Część 1. Uzyskiwanie klucza publicznego (pliku cer) dla certyfikatu głównego

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>Część 2. Generowanie certyfikatu klienta

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3. Przekazywanie pliku cer certyfikatu głównego

Po utworzeniu bramy możesz przekazać plik cer (który zawiera informacje o kluczu publicznym) dla zaufanego certyfikatu głównego na platformę Azure. Nie przekazuj klucza prywatnego dla certyfikatu głównego do platformy Azure. Po przekazaniu pliku cer platforma Azure może używać go do uwierzytelniania klientów, którzy mają zainstalowany certyfikat klienta wygenerowany na podstawie zaufanego certyfikatu głównego. Jeśli okaże się to konieczne, dodatkowe pliki zaufanego certyfikatu głównego możesz przekazać później — maksymalnie może ich być 20.  

1. W sekcji **Połączenia sieci VPN** na stronie danej sieci wirtualnej kliknij grafikę **klienci**, aby otworzyć stronę **Połączenie sieci VPN typu punkt-lokacja**.

  ![Klienci](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na stronie **Połączenie punkt-lokacja** kliknij przycisk **Zarządzanie certyfikatami**, aby otworzyć stronę **Certyfikaty**.<br>

  ![Strona Certyfikaty](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na stronie **Certyfikaty** kliknij pozycję **Przekaż**, aby otworzyć stronę **Przekaż certyfikat**.<br>

    ![Strona przekazywania certyfikatów](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Kliknij grafikę folderu, aby przejść do pliku cer. Wybierz plik, a następnie kliknij przycisk **OK**. Odśwież stronę, aby wyświetlić przekazany certyfikat na stronie **Certyfikaty**.

  ![Przekazywanie certyfikatu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. Konfigurowanie klienta

Aby nawiązać połączenie z siecią wirtualną przy użyciu połączenia sieci VPN typu punkt-lokacja, na każdym kliencie trzeba zainstalować pakiet do konfiguracji natywnego klienta sieci VPN systemu Windows. Pakiet konfiguracji powoduje skonfigurowanie natywnego klienta sieci VPN systemu Windows za pomocą ustawień koniecznych do łączenia się z siecią wirtualną.

Tego samego pakietu konfiguracji klienta VPN można użyć na każdym komputerze klienckim, o ile wersja jest zgodna z architekturą dla klienta. Lista obsługiwanych systemów operacyjnych klienta znajduje się w sekcji [Często zadawane pytania dotyczące połączeń typu punkt-lokacja](#faq) na końcu tego artykułu.

### <a name="generateconfigpackage"></a>Część 1. Generowanie i instalowanie pakietu konfiguracji klienta sieci VPN

1. W witrynie Azure Portal na stronie **Omówienie** danej sieci wirtualnej kliknij grafikę klienta w obszarze **Połączenia VPN**, aby otworzyć stronę **Połączenie sieci VPN typu punkt-lokacja**.
2. W górnej części strony **Połączenie sieci VPN typu punkt-lokacja** kliknij pakiet pobierania, który odpowiada systemowi operacyjnemu klienta, na którym zostanie zainstalowany:

  * Dla klientów 64-bitowych wybierz pakiet **Klient sieci VPN (64-bitowy)**.
  * Dla klientów 32-bitowych wybierz pakiet **Klient sieci VPN (32-bitowy)**.

  ![Pobieranie pakietu konfiguracji klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Po wygenerowaniu pakietu pobierz go i zainstaluj na komputerze klienckim. Jeśli zostanie wyświetlone okno podręczne SmartScreen, kliknij pozycję **Więcej informacji**, a następnie pozycję **Uruchom mimo to**. Możesz także zapisać pakiet w celu zainstalowania go na innych komputerach klienckich.

### <a name="installclientcert"></a>Część 2. Instalowanie certyfikatu klienta

Jeśli chcesz utworzyć połączenie punkt-lokacja z komputerem klienckim innym niż użyty do wygenerowania certyfikatów klienta, należy zainstalować certyfikat klienta. Podczas instalowania certyfikatu klienta potrzebne jest hasło, które zostało utworzone w trakcie eksportowania certyfikatu klienta. Zazwyczaj jest to kwestia dwukrotnego kliknięcia certyfikatu i zainstalowania go. Aby uzyskać więcej informacji, zobacz [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install) (Instalowanie wyeksportowanego certyfikatu klienta).

## <a name="connect"></a>5. Nawiązywanie połączenia z usługą Azure

### <a name="connect-to-your-vnet"></a>Nawiązywanie połączenia z siecią wirtualną

>[!NOTE]
>Musisz mieć uprawnienia administratora na komputerze klienckim, z którym nawiązujesz połączenie.
>
>

1. Aby nawiązać połączenie z siecią wirtualną na komputerze klienckim, przejdź do połączeń sieci VPN i wyszukaj wcześniej utworzone połączenie sieci VPN. Połączenie będzie miało taką samą nazwę jak sieć wirtualna. Kliknij przycisk **Połącz**. Może pojawić się komunikat podręczny, który odwołuje się do użycia certyfikatu. Jeśli tak się stanie, kliknij przycisk **Kontynuuj**, aby skorzystać z podniesionych uprawnień.
2. Na stronie stanu **Połączenie** kliknij przycisk **Połącz**, aby rozpocząć połączenie. Jeśli widzisz ekran **Wybierz certyfikat**, sprawdź, czy wyświetlany certyfikat klienta to ten, który ma zostać użyty do nawiązania połączenia. Jeśli nie, kliknij strzałkę na liście rozwijanej, aby wybrać właściwy certyfikat, a następnie kliknij przycisk **OK**.

  ![Połączenie klienta sieci VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Połączenie zostało ustanowione.

  ![Ustanowiono połączenie](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>Rozwiązywanie problemów dotyczących połączeń typu punkt-lokacja

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="verifyvpnconnect"></a>Sprawdzanie połączenia sieci VPN

1. Aby sprawdzić, czy połączenie sieci VPN jest aktywne, na komputerze klienckim otwórz wiersz polecenia z podwyższonym poziomem uprawnień, a następnie uruchom polecenie *ipconfig/all*.
2. Przejrzyj wyniki. Zwróć uwagę na fakt, że otrzymany adres IP jest jednym z adresów z zakresu dla połączenia typu punkt-lokacja określonego podczas tworzenia sieci wirtualnej. Wyniki powinny być podobne do poniższego przykładu:

  ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Dodawanie lub usuwanie zaufanych certyfikatów głównych

Zaufane certyfikaty główne można dodawać do platformy Azure lub z niej usuwać. Po usunięciu certyfikatu głównego klienci, którzy mają certyfikat wygenerowany na podstawie tego certyfikatu głównego, nie będą w stanie się uwierzytelnić i w związku z tym nie będą mogli nawiązywać połączeń. Jeśli chcesz, aby klienci mogli uwierzytelniać się i nawiązywać połączenia, musisz zainstalować nowy certyfikat klienta wygenerowany na podstawie certyfikatu głównego, który jest traktowany jako zaufany przez platformę Azure (przekazany na tę platformę).

### <a name="addtrustedroot"></a>Aby dodać zaufany certyfikat główny

Na platformie Azure można dodać maksymalnie 20 plików cer zaufanego certyfikatu głównego. Aby uzyskać instrukcje, zobacz [Sekcja 3 — Przekazywanie pliku cer certyfikatu głównego](#upload).

### <a name="removetrustedroot"></a>Aby usunąć zaufany certyfikat główny

1. W sekcji **Połączenia sieci VPN** na stronie danej sieci wirtualnej kliknij grafikę **klienci**, aby otworzyć stronę **Połączenie sieci VPN typu punkt-lokacja**.

  ![Klienci](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na stronie **Połączenie punkt-lokacja** kliknij przycisk **Zarządzanie certyfikatami**, aby otworzyć stronę **Certyfikaty**.<br>

  ![Strona Certyfikaty](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na stronie **Certyfikaty** kliknij wielokropek obok certyfikatu, który chcesz usunąć, a następnie kliknij pozycję **Usuń**.

  ![Usuwanie certyfikatu głównego](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Odwoływanie certyfikatu klienta

Certyfikaty klienta można odwołać. Lista odwołania certyfikatów umożliwia dokonanie selektywnej odmowy połączenia punkt-lokacja w oparciu o indywidualne certyfikaty klienta. Różni się to od usuwania zaufanego certyfikatu głównego. Jeśli usuniesz plik cer zaufanego certyfikatu głównego z platformy Azure, spowoduje to odwołanie dostępu dla wszystkich certyfikatów klienta wygenerowanych lub podpisanych przez odwołany certyfikat główny. Odwołanie certyfikatu klienta zamiast certyfikatu głównego pozwala dalej używać innych certyfikatów wygenerowanych na podstawie certyfikatu głównego do uwierzytelniania połączeń punkt-lokacja.

Częstą praktyką jest użycie certyfikatu głównego do zarządzania dostępem na poziomach zespołu lub organizacji przy jednoczesnym korzystaniu z odwołanych certyfikatów klienta dla bardziej precyzyjnej kontroli dostępu poszczególnych użytkowników.

### <a name="revokeclientcert"></a>Aby odwołać certyfikat klienta

Certyfikat klienta można odwołać przez dodanie odcisku palca do listy odwołania.

1. Pobierz odcisk palca certyfikatu klienta. Aby uzyskać więcej informacji, zobacz [Instrukcje: Pobieranie odcisku palca certyfikatu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Skopiuj informacje do edytora tekstu i usuń wszelkie spacje, tak aby powstał ciąg bez odstępów.
3. Przejdź do strony **„nazwa klasycznej sieci wirtualnej” > Połączenie VPN punkt-lokacja > Certyfikaty**, a następnie kliknij pozycję **Lista odwołania**, aby otworzyć stronę Lista odwołania. 
4. Na stronie **Lista odwołania** kliknij pozycję **+ Dodaj certyfikat**, aby otworzyć stronę **Dodawanie certyfikatu do listy odwołania**.
5. Na stronie **Dodawanie certyfikatu do listy odwołania** wklej odcisk palca certyfikatu jako jeden ciągły wiersz tekstu bez odstępów. Kliknij przycisk **OK** w dolnej części strony.
6. Po zakończeniu aktualizowania nie będzie można już używać certyfikatu do nawiązywania połączeń. Klienci, którzy spróbują połączyć się za pomocą tego certyfikatu, zobaczą komunikat informujący o tym, że certyfikat nie jest już ważny.

## <a name="faq"></a>Często zadawane pytania dotyczące połączeń typu punkt-lokacja

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshoot Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).